---
title: 25 years of KEXP Annual Best-Of lists
toc: false
---

# 25 years of KEXP Annual Best-Of lists

Each spoke is one year of KEXP's Annual "Best Albums of the Year" list, 2001
(top, running clockwise) through 2025. Rank 1 sits nearest the center; longer
spokes are years with more entries (most years have 91, but 2012 has 120 and
2024/2025 have 100). Dot color and size both encode the same thing: how many
distinct albums that artist has had across *all 25 years combined* — darker
and bigger means a more consistently list-worthy artist over the full
quarter-century, not just this one year. Use the checkboxes below to
highlight specific years.

```js
const rows = FileAttachment("data/annual_circle_2001_2025.csv").csv({typed: true});
```

```js
const years = d3.sort(new Set(rows.map((d) => d.list_year)));
```

```js
function yearCheckboxes(allYears) {
  const div = document.createElement("div");
  div.style.cssText = "display:flex;flex-wrap:wrap;gap:6px 16px;font-size:13px;margin:0.75rem 0;";
  const boxes = allYears.map((yr) => {
    const label = document.createElement("label");
    label.style.cssText = "display:flex;align-items:center;gap:4px;cursor:pointer;";
    const input = document.createElement("input");
    input.type = "checkbox";
    input.checked = true;
    input.value = yr;
    label.append(input, document.createTextNode(String(yr)));
    div.append(label);
    return input;
  });
  Object.defineProperty(div, "value", {
    get() {
      return boxes.filter((b) => b.checked).map((b) => +b.value);
    },
  });
  return div;
}

const selectedYears = view(yearCheckboxes(years));
```

```js
const tierColor = {1: "#f9d53f", 2: "#e67bf7", 3: "#9c57f3", 4: "#5127e9"};
const tierSize = {1: 2.2, 2: 2.8, 3: 3.6, 4: 4.8};

// Layout constants (fixed pixel geometry, not proportional to container width
// -- see DESIGN_DECISIONS.md "wedge/grid layout" for why: a dense chart like
// this needs guaranteed real spacing between dots, so it's designed at one
// canonical size and left to shrink as a whole image via CSS on narrow
// screens, rather than stretched/squeezed per-viewer.
const columns = 5;       // dots per radius band, fanned across each year's wedge
const colPitchPx = 8;    // fixed lateral spacing between columns, any radius
const bandHeight = 10;   // fixed radial spacing between successive bands
const innerR = 180;      // hub radius -- large enough that band-0's wedge
                          // width doesn't cross into neighboring years' spokes
const dimOpacity = 0.12; // opacity for years unchecked in the filter

function annualCircle(data, selectedYears) {
  const years = d3.sort(new Set(data.map((d) => d.list_year)));
  const selected = new Set(selectedYears);
  const maxRank = d3.max(data, (d) => d.rank);
  const maxBands = Math.ceil(maxRank / columns);
  const outerR = innerR + (maxBands - 1) * bandHeight;
  const size = (outerR + 70) * 2;
  const cx = size / 2, cy = size / 2;

  // 12:00 clockwise to ~11:00 -- a deliberate gap, not a closed circle, so
  // the layout never implies the timeline wraps back on itself.
  const gapDeg = 30;
  const sweepDeg = 360 - gapDeg;
  const angleForYear = d3.scaleLinear()
    .domain([0, years.length - 1])
    .range([0, sweepDeg]);

  function dirFor(year) {
    const a = (angleForYear(years.indexOf(year)) - 90) * (Math.PI / 180);
    return [Math.cos(a), Math.sin(a)];
  }

  // Each year's dots fan out in a small snaking grid (radius = band,
  // perpendicular offset = column) instead of stacking on a single line --
  // the mechanism that gives every dot real breathing room. See
  // DESIGN_DECISIONS.md for the geometry this is built on.
  function xy(year, rank) {
    const [dx, dy] = dirFor(year);
    const [px, py] = [-dy, dx];
    const idx = rank - 1;
    const band = Math.floor(idx / columns);
    const rawCol = idx % columns;
    const col = band % 2 === 0 ? rawCol : columns - 1 - rawCol;
    const colOffsetIndex = col - (columns - 1) / 2;
    const radius = innerR + band * bandHeight;
    const perp = colOffsetIndex * colPitchPx;
    return [cx + dx * radius + px * perp, cy + dy * radius + py * perp];
  }

  const yearCounts = d3.rollup(data, (v) => v.length, (d) => d.list_year);

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, size, size])
    .attr("width", size)
    .attr("height", size)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  // Hub direction arrow -- a plain curved arrow (no text) showing which way
  // the wheel reads, drawn well inside innerR so it never touches band-0 dots.
  {
    const arcR = 95;
    const startDeg = -90;
    const endDeg = startDeg + 300;
    const toRad = (deg) => (deg * Math.PI) / 180;
    const polar = (deg, r) => [cx + r * Math.cos(toRad(deg)), cy + r * Math.sin(toRad(deg))];
    const [sx, sy] = polar(startDeg, arcR);
    const [ex, ey] = polar(endDeg, arcR);

    svg.append("path")
      .attr("d", `M ${sx} ${sy} A ${arcR} ${arcR} 0 1 1 ${ex} ${ey}`)
      .attr("fill", "none")
      .attr("stroke", "#6b6a64")
      .attr("stroke-width", 2)
      .attr("stroke-linecap", "round")
      .attr("opacity", 0.7);

    const endRad = toRad(endDeg);
    const tangent = [-Math.sin(endRad), Math.cos(endRad)];
    const normal = [Math.cos(endRad), Math.sin(endRad)];
    const headLen = 12, headWidth = 8;
    const tip = [ex + tangent[0] * headLen * 0.6, ey + tangent[1] * headLen * 0.6];
    const base = [ex - tangent[0] * headLen * 0.4, ey - tangent[1] * headLen * 0.4];
    const p1 = [base[0] + normal[0] * headWidth / 2, base[1] + normal[1] * headWidth / 2];
    const p2 = [base[0] - normal[0] * headWidth / 2, base[1] - normal[1] * headWidth / 2];

    svg.append("path")
      .attr("d", `M ${tip[0]} ${tip[1]} L ${p1[0]} ${p1[1]} L ${p2[0]} ${p2[1]} Z`)
      .attr("fill", "#6b6a64")
      .attr("opacity", 0.7);
  }

  // Year labels, placed just past each spoke's own last band -- on the pure
  // centerline (no column offset), since only the dots need to fan out.
  svg.append("g")
    .selectAll("text")
    .data(years)
    .join("text")
    .attr("x", (yr) => {
      const bands = Math.ceil(yearCounts.get(yr) / columns);
      const r = innerR + (bands - 1) * bandHeight + 26;
      return cx + dirFor(yr)[0] * r;
    })
    .attr("y", (yr) => {
      const bands = Math.ceil(yearCounts.get(yr) / columns);
      const r = innerR + (bands - 1) * bandHeight + 26;
      return cy + dirFor(yr)[1] * r;
    })
    .attr("fill", "#898781")
    .attr("font-size", 11)
    .attr("text-anchor", "middle")
    .attr("dominant-baseline", "middle")
    .attr("opacity", (yr) => (selected.has(yr) ? 1 : dimOpacity))
    .text((yr) => yr);

  const tooltip = d3.select(document.createElement("div"))
    .attr("style", "position:fixed;pointer-events:none;background:#1a1a19;color:#f0efec;border:1px solid #383835;border-radius:8px;padding:6px 10px;font-size:12px;font-family:var(--sans-serif);opacity:0;transition:opacity 0.1s;z-index:10;max-width:220px;");
  document.body.appendChild(tooltip.node());

  svg.append("g")
    .selectAll("circle")
    .data(data)
    .join("circle")
    .attr("cx", (d) => xy(d.list_year, d.rank)[0])
    .attr("cy", (d) => xy(d.list_year, d.rank)[1])
    .attr("r", (d) => tierSize[d.tier])
    .attr("fill", (d) => tierColor[d.tier])
    .attr("opacity", (d) => (selected.has(d.list_year) ? 1 : dimOpacity))
    .on("pointerenter", (event, d) => {
      tooltip
        .style("opacity", 1)
        .html(`<b>${d.artist_name}</b><br>${d.release_group_name}<br>#${d.rank} on ${d.list_year} list`)
        .style("left", event.clientX + 14 + "px")
        .style("top", event.clientY + 14 + "px");
    })
    .on("pointermove", (event) => {
      tooltip.style("left", event.clientX + 14 + "px").style("top", event.clientY + 14 + "px");
    })
    .on("pointerleave", () => tooltip.style("opacity", 0));

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem;max-width:820px;margin:0 auto;">

```js
annualCircle(rows, selectedYears)
```

<div style="display:flex;gap:20px;flex-wrap:wrap;margin-top:1rem;justify-content:center;">
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:11px;height:11px;border-radius:50%;background:#f9d53f;display:inline-block"></span>1 album</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:14px;height:14px;border-radius:50%;background:#e67bf7;display:inline-block"></span>2 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:18px;height:18px;border-radius:50%;background:#9c57f3;display:inline-block"></span>3&ndash;9 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:24px;height:24px;border-radius:50%;background:#5127e9;display:inline-block"></span>10+ albums</span>
</div>

</div>
