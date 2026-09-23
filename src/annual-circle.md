---
title: 25 years of KEXP Annual Best-Of lists
toc: false
head: '<link rel="icon" href="observable.png" type="image/png" sizes="32x32"><script src="https://cdn.jsdelivr.net/npm/iframe-resizer@5.5.9/js/iframeResizer.contentWindow.min.js"></script>'
---

# 25 years of KEXP Annual Best-Of lists

Each spoke is one year of KEXP's Annual "Best Albums of the Year" list, 2001
(top, running clockwise) through 2025. Rank 1 sits nearest the center; longer
spokes are years with more entries (most years have 91, but 2012 has 120 and
2024/2025 have 100). Dot color and size both encode the same thing: how many
distinct albums that artist has had across *all 25 years combined* — darker
and bigger means a more consistently list-worthy artist over the full
quarter-century, not just this one year. Use the checkboxes below to
highlight specific years, or search for an artist to see every year they
made the list.

```js
const rows = FileAttachment("data/annual_circle_2001_2025.csv").csv({typed: true});
```

```js
const years = d3.sort(new Set(rows.map((d) => d.list_year)));
```

```js
function yearCheckboxes(allYears) {
  const container = document.createElement("div");

  const controls = document.createElement("div");
  controls.style.cssText = "display:flex;gap:8px;margin-bottom:0.4rem;";
  const selectAllBtn = document.createElement("button");
  selectAllBtn.type = "button";
  selectAllBtn.textContent = "Select all";
  selectAllBtn.style.cssText = "font-size:12px;padding:4px 10px;border-radius:6px;border:1px solid var(--theme-foreground-faint);background:transparent;color:var(--theme-foreground);cursor:pointer;";
  const clearAllBtn = document.createElement("button");
  clearAllBtn.type = "button";
  clearAllBtn.textContent = "Clear all";
  clearAllBtn.style.cssText = selectAllBtn.style.cssText;
  controls.append(selectAllBtn, clearAllBtn);

  const div = document.createElement("div");
  div.style.cssText = "display:flex;flex-wrap:wrap;gap:6px 16px;font-size:13px;margin:0 0 0.75rem;";
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

  container.append(controls, div);

  Object.defineProperty(container, "value", {
    get() {
      return boxes.filter((b) => b.checked).map((b) => +b.value);
    },
  });

  function setAll(checked) {
    for (const b of boxes) b.checked = checked;
    container.dispatchEvent(new Event("input"));
  }
  selectAllBtn.addEventListener("click", () => setAll(true));
  clearAllBtn.addEventListener("click", () => setAll(false));

  return container;
}

const selectedYears = view(yearCheckboxes(years));
```

```js
const artistList = (() => {
  const seen = new Map();
  for (const d of rows) if (!seen.has(d.artist_id)) seen.set(d.artist_id, d.artist_name);
  return Array.from(seen, ([id, name]) => ({id, name})).sort((a, b) => a.name.localeCompare(b.name));
})();
```

```js
function artistSearch(list) {
  const container = document.createElement("div");
  container.style.cssText = "position:relative;max-width:320px;margin:0.25rem 0 0.75rem;";

  const row = document.createElement("div");
  row.style.cssText = "display:flex;align-items:center;gap:8px;";

  const input = document.createElement("input");
  input.type = "text";
  input.placeholder = "Search for an artist…";
  input.autocomplete = "off";
  input.style.cssText = "flex:1;box-sizing:border-box;padding:8px 10px;font-size:14px;border:1px solid var(--theme-foreground-faint);border-radius:6px;background:var(--theme-background);color:var(--theme-foreground);";

  const clearBtn = document.createElement("button");
  clearBtn.type = "button";
  clearBtn.textContent = "Clear";
  clearBtn.style.cssText = "font-size:13px;padding:7px 10px;border-radius:6px;border:1px solid var(--theme-foreground-faint);background:transparent;color:var(--theme-foreground);cursor:pointer;display:none;";

  row.append(input, clearBtn);

  const suggestions = document.createElement("div");
  suggestions.style.cssText = "position:absolute;top:100%;left:0;right:0;background:#1a1a19;border:1px solid #383835;border-radius:8px;margin-top:4px;max-height:240px;overflow-y:auto;z-index:20;display:none;";

  container.append(row, suggestions);

  Object.defineProperty(container, "value", {
    get() {
      return container._selectedId ?? null;
    },
  });

  function renderSuggestions(query) {
    suggestions.innerHTML = "";
    const q = query.trim().toLowerCase();
    if (!q) {
      suggestions.style.display = "none";
      return;
    }
    const matches = list.filter((a) => a.name.toLowerCase().includes(q)).slice(0, 8);
    if (!matches.length) {
      suggestions.style.display = "none";
      return;
    }
    for (const a of matches) {
      const item = document.createElement("div");
      item.textContent = a.name;
      item.style.cssText = "padding:8px 10px;cursor:pointer;color:#f0efec;font-size:14px;";
      item.addEventListener("pointerenter", () => (item.style.background = "#2a2a27"));
      item.addEventListener("pointerleave", () => (item.style.background = "transparent"));
      item.addEventListener("mousedown", (event) => {
        event.preventDefault();
        select(a);
      });
      suggestions.append(item);
    }
    suggestions.style.display = "block";
  }

  function select(a) {
    container._selectedId = a.id;
    input.value = a.name;
    suggestions.style.display = "none";
    clearBtn.style.display = "inline-block";
    container.dispatchEvent(new Event("input"));
  }

  function clear() {
    container._selectedId = null;
    input.value = "";
    suggestions.style.display = "none";
    clearBtn.style.display = "none";
    input.focus();
    container.dispatchEvent(new Event("input"));
  }

  input.addEventListener("input", () => {
    container._selectedId = null;
    clearBtn.style.display = "none";
    renderSuggestions(input.value);
  });
  clearBtn.addEventListener("click", clear);

  return container;
}

const selectedArtist = view(artistSearch(artistList));
```

```js
const tierColor = {1: "#fac45a", 2: "#38c4bd", 3: "#9c57f3", 4: "#5127e9"};
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

function annualCircle(data, selectedYears, selectedArtist) {
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
    .attr("r", (d) => tierSize[d.tier] * (d.artist_id === selectedArtist && selected.has(d.list_year) ? 1.8 : 1))
    .attr("fill", (d) => tierColor[d.tier])
    .attr("stroke", (d) => (d.artist_id === selectedArtist && selected.has(d.list_year) ? "#f0efec" : "none"))
    .attr("stroke-width", 1.5)
    .attr("opacity", (d) => {
      if (selectedArtist) {
        return d.artist_id === selectedArtist && selected.has(d.list_year) ? 1 : dimOpacity;
      }
      return selected.has(d.list_year) ? 1 : dimOpacity;
    })
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

  // Reading-order key, grouped in the top-right corner as one "how to read
  // this" cluster: two stacked rows of dummy dots showing how rank snakes
  // outward within a spoke (1-5 left-to-right, then 6-10 right-to-left),
  // plus a short arrow reinforcing the same outward direction -- both in
  // the same bright color so they read as one unit. Placed top-right
  // rather than bottom-right specifically to avoid sitting next to a
  // bottom spoke (e.g. 2014) whose own reversed band already looks
  // superficially similar, which invited confusion.
  {
    const keyR = 11;
    const keyGap = 28;
    const keyMarginRight = 46;
    const keyMarginTop = 46;
    const rightX = size - keyMarginRight;
    const rowInner = [1, 2, 3, 4, 5]; // nearer the hub, drawn as the lower row
    const rowOuter = [10, 9, 8, 7, 6]; // farther out, drawn as the upper row
    const row2Y = keyMarginTop;
    const row1Y = keyMarginTop + keyGap;
    const keyG = svg.append("g").attr("opacity", 0.95);
    [
      {row: rowOuter, y: row2Y},
      {row: rowInner, y: row1Y},
    ].forEach(({row, y}) => {
      row.forEach((n, i) => {
        const cx2 = rightX - (row.length - 1 - i) * keyGap;
        keyG.append("circle")
          .attr("cx", cx2).attr("cy", y).attr("r", keyR)
          .attr("fill", "none").attr("stroke", "#c9c8c3").attr("stroke-width", 1.5);
        keyG.append("text")
          .attr("x", cx2).attr("y", y + 1)
          .attr("text-anchor", "middle").attr("dominant-baseline", "central")
          .attr("fill", "#c9c8c3").attr("font-size", 12)
          .text(n);
      });
    });

    const arrowX = rightX - (rowInner.length - 1) * keyGap - keyR - 22;
    keyG.append("line")
      .attr("x1", arrowX).attr("y1", row1Y)
      .attr("x2", arrowX).attr("y2", row2Y)
      .attr("stroke", "#c9c8c3").attr("stroke-width", 1.5).attr("stroke-linecap", "round");
    const headLen = 10, headWidth = 7;
    const tipY = row2Y - headLen * 0.6;
    const baseY = row2Y + headLen * 0.4;
    keyG.append("path")
      .attr("d", `M ${arrowX} ${tipY} L ${arrowX - headWidth / 2} ${baseY} L ${arrowX + headWidth / 2} ${baseY} Z`)
      .attr("fill", "#c9c8c3");
  }

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem;max-width:820px;margin:0 auto;">

```js
annualCircle(rows, selectedYears, selectedArtist)
```

<div style="display:flex;gap:20px;flex-wrap:wrap;margin-top:1rem;justify-content:center;">
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:11px;height:11px;border-radius:50%;background:#fac45a;display:inline-block"></span>1 album</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:14px;height:14px;border-radius:50%;background:#38c4bd;display:inline-block"></span>2 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:18px;height:18px;border-radius:50%;background:#9c57f3;display:inline-block"></span>3&ndash;9 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:24px;height:24px;border-radius:50%;background:#5127e9;display:inline-block"></span>10+ albums</span>
</div>

</div>

```js
function artistTable(artistId, data) {
  const container = document.createElement("div");
  container.style.cssText = "margin:1rem 0 1.5rem;";

  if (!artistId) {
    container.style.cssText += "color:var(--theme-foreground-muted);font-size:14px;";
    container.textContent = "Search for an artist above to see every year they made the list, highlighted on the wheel too.";
    return container;
  }

  const entries = data.filter((d) => d.artist_id === artistId).sort((a, b) => a.list_year - b.list_year);
  const tierLabel = {1: "1 album", 2: "2 albums", 3: "3–9 albums", 4: "10+ albums"};

  const heading = document.createElement("div");
  heading.style.cssText = "font-weight:600;font-size:15px;margin-bottom:0.5rem;";
  heading.textContent = `${entries[0].artist_name} — ${entries.length} appearance${entries.length === 1 ? "" : "s"} (${tierLabel[entries[0].tier]} across all 25 years)`;

  const tableWrap = document.createElement("div");
  tableWrap.style.cssText = "overflow-x:auto;";

  const table = document.createElement("table");
  table.style.cssText = "width:100%;max-width:520px;border-collapse:collapse;font-size:14px;";

  const thead = document.createElement("thead");
  const headRow = document.createElement("tr");
  for (const label of ["Year", "Rank", "Album"]) {
    const th = document.createElement("th");
    th.textContent = label;
    th.style.cssText = "text-align:left;padding:6px 12px 6px 0;border-bottom:1px solid var(--theme-foreground-faint);color:var(--theme-foreground-muted);font-weight:600;";
    headRow.append(th);
  }
  thead.append(headRow);

  const tbody = document.createElement("tbody");
  for (const d of entries) {
    const tr = document.createElement("tr");
    const tdYear = document.createElement("td");
    tdYear.textContent = d.list_year;
    tdYear.style.cssText = "padding:6px 12px 6px 0;border-bottom:1px solid var(--theme-foreground-faint);";
    const tdRank = document.createElement("td");
    tdRank.textContent = `#${d.rank}`;
    tdRank.style.cssText = "padding:6px 12px;border-bottom:1px solid var(--theme-foreground-faint);";
    const tdAlbum = document.createElement("td");
    tdAlbum.textContent = d.release_group_name;
    tdAlbum.style.cssText = "padding:6px 12px;border-bottom:1px solid var(--theme-foreground-faint);";
    tr.append(tdYear, tdRank, tdAlbum);
    tbody.append(tr);
  }
  table.append(thead, tbody);
  tableWrap.append(table);
  container.append(heading, tableWrap);
  return container;
}
```

```js
artistTable(selectedArtist, rows)
```

## Key findings

The wheel above shows every entry from every list. The two charts below pull
back to ask what those entries add up to.

### How rare is a repeat appearance?

```js
const artistTier = new Map();
for (const d of rows) if (!artistTier.has(d.artist_id)) artistTier.set(d.artist_id, d.tier);
const totalArtists = artistTier.size;
const tierArtistCounts = d3.rollup(Array.from(artistTier.values()), (v) => v.length, (t) => t);
```

```js
function rightRoundedRectPath(x, y, w, h, r) {
  const rr = Math.min(r, h / 2, Math.max(w, 0));
  if (w <= rr) return `M${x},${y} h${w} v${h} h${-w} Z`;
  return `M${x},${y} H${x + w - rr} A${rr},${rr} 0 0 1 ${x + w},${y + rr} V${y + h - rr} A${rr},${rr} 0 0 1 ${x + w - rr},${y + h} H${x} Z`;
}

function tierCohortChart(totalArtists, tierArtistCounts) {
  const tierLabel = {1: "1 album", 2: "2 albums", 3: "3–9 albums", 4: "10+ albums"};
  const tiers = [1, 2, 3, 4];
  const width = 640;
  const rowH = 54, barH = 22, padTop = 12, padBottom = 12, labelW = 96, tipW = 150;
  const height = padTop + tiers.length * rowH + padBottom;
  const plotW = width - labelW - tipW;
  const maxCount = d3.max(tiers, (t) => tierArtistCounts.get(t) || 0);
  const x = d3.scaleLinear().domain([0, maxCount]).range([0, plotW]);

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  const g = svg.append("g").attr("transform", `translate(${labelW},${padTop})`);
  const row = g.selectAll("g.row").data(tiers).join("g")
    .attr("transform", (t, i) => `translate(0,${i * rowH + (rowH - barH) / 2})`);

  row.append("text")
    .attr("x", -12)
    .attr("y", barH / 2)
    .attr("text-anchor", "end")
    .attr("dominant-baseline", "middle")
    .attr("fill", "#c9c8c3")
    .attr("font-size", 13)
    .text((t) => tierLabel[t]);

  row.append("path")
    .attr("d", (t) => rightRoundedRectPath(0, 0, Math.max(3, x(tierArtistCounts.get(t) || 0)), barH, 4))
    .attr("fill", (t) => tierColor[t])
    .style("transition", "opacity 0.15s")
    .on("pointerenter", function () { d3.select(this).style("opacity", 0.75); })
    .on("pointerleave", function () { d3.select(this).style("opacity", 1); });

  row.append("text")
    .attr("x", (t) => x(tierArtistCounts.get(t) || 0) + 10)
    .attr("y", barH / 2)
    .attr("dominant-baseline", "middle")
    .attr("fill", "#c9c8c3")
    .attr("font-size", 13)
    .text((t) => {
      const c = tierArtistCounts.get(t) || 0;
      const pct = (100 * c / totalArtists).toFixed(1);
      return `${c.toLocaleString()} artists · ${pct}%`;
    });

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem;max-width:820px;margin:0 auto;">

```js
tierCohortChart(totalArtists, tierArtistCounts)
```

</div>

```js
(() => {
  const p = document.createElement("p");
  p.innerHTML = `Of the ${totalArtists.toLocaleString()} distinct artists who have ever made an Annual list, ${((100 * tierArtistCounts.get(1)) / totalArtists).toFixed(1)}% have exactly <em>one</em> album on it, ever. &ldquo;One and done&rdquo; is the majority case, not the exception &mdash; and only ${tierArtistCounts.get(4)} artists in 25 years have cracked double digits.`;
  return p;
})()
```

### Does it matter *when* an artist first shows up?

You'd expect an artist's tier to just reflect how good and prolific they
are. But the tier is a 25-year *total*, and the 25-year window itself has
edges — so an artist's odds of reaching a high tier also depend on how much
of that window they had to work with.

```js
const yearStats = years.map((yr) => {
  const entries = rows.filter((d) => d.list_year === yr);
  const counts = {1: 0, 2: 0, 3: 0, 4: 0};
  for (const d of entries) counts[d.tier]++;
  return {year: yr, counts, total: entries.length};
});
```

```js
function tierTrendChart(yearStats) {
  const tiers = [1, 2, 3, 4];
  const width = 880, height = 380;
  const marginL = 40, marginR = 12, marginT = 16, marginB = 40;
  const plotW = width - marginL - marginR;
  const plotH = height - marginT - marginB;
  const gap = 2;

  const x = d3.scaleBand().domain(yearStats.map((d) => d.year)).range([0, plotW]).padding(0.22);
  const y = d3.scaleLinear().domain([0, 100]).range([plotH, 0]);

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  const g = svg.append("g").attr("transform", `translate(${marginL},${marginT})`);

  const yTicks = [0, 25, 50, 75, 100];
  g.append("g").selectAll("line").data(yTicks).join("line")
    .attr("x1", 0).attr("x2", plotW)
    .attr("y1", (d) => y(d)).attr("y2", (d) => y(d))
    .attr("stroke", "#33322f").attr("stroke-width", 1);
  g.append("g").selectAll("text").data(yTicks).join("text")
    .attr("x", -8).attr("y", (d) => y(d))
    .attr("text-anchor", "end").attr("dominant-baseline", "middle")
    .attr("fill", "#898781").attr("font-size", 10)
    .text((d) => d + "%");

  g.append("g").selectAll("text").data(yearStats).join("text")
    .attr("x", (d) => x(d.year) + x.bandwidth() / 2)
    .attr("y", plotH + 10)
    .attr("fill", "#898781")
    .attr("font-size", 9)
    .attr("text-anchor", "end")
    .attr("transform", (d) => `rotate(-55,${x(d.year) + x.bandwidth() / 2},${plotH + 10})`)
    .text((d) => d.year);

  const tooltip = d3.select(document.createElement("div"))
    .attr("style", "position:fixed;pointer-events:none;background:#1a1a19;color:#f0efec;border:1px solid #383835;border-radius:8px;padding:8px 10px;font-size:12px;font-family:var(--sans-serif);opacity:0;transition:opacity 0.1s;z-index:10;min-width:150px;");
  document.body.appendChild(tooltip.node());
  const tierLabel = {1: "1 album", 2: "2 albums", 3: "3–9 albums", 4: "10+ albums"};

  const bars = g.selectAll("g.bar").data(yearStats).join("g")
    .attr("transform", (d) => `translate(${x(d.year)},0)`);

  bars.each(function (d) {
    const gEl = d3.select(this);
    let cum = 0;
    tiers.forEach((t, i) => {
      const cnt = d.counts[t] || 0;
      const pct = (100 * cnt) / d.total;
      const y0 = cum, y1 = cum + pct;
      cum = y1;
      const yTop = y(y1), yBot = y(y0);
      const isTop = i === tiers.length - 1;
      const segH = Math.max(0, yBot - yTop - (isTop ? 0 : gap));
      const path = isTop
        ? (() => {
            const r = 3, w = x.bandwidth();
            const hh = Math.max(0, segH);
            if (hh <= r) return `M${0},${yTop} h${w} v${hh} h${-w} Z`;
            return `M${0},${yTop + r} A${r},${r} 0 0 1 ${r},${yTop} H${w - r} A${r},${r} 0 0 1 ${w},${yTop + r} V${yTop + hh} H${0} Z`;
          })()
        : `M0,${yTop} h${x.bandwidth()} v${segH} h${-x.bandwidth()} Z`;
      gEl.append("path").attr("d", path).attr("fill", tierColor[t]);
    });
  });

  bars.append("rect")
    .attr("x", 0).attr("y", 0)
    .attr("width", x.bandwidth()).attr("height", plotH)
    .attr("fill", "transparent")
    .on("pointerenter pointermove", function (event, d) {
      d3.select(this.parentNode).selectAll("path").style("opacity", 0.8);
      const lines = tiers.map((t) => {
        const c = d.counts[t] || 0;
        const pct = ((100 * c) / d.total).toFixed(1);
        return `<div style="display:flex;justify-content:space-between;gap:12px"><span>${tierLabel[t]}</span><b>${pct}%</b></div>`;
      });
      tooltip
        .style("opacity", 1)
        .html(`<b>${d.year}</b> · ${d.total} entries<br>${lines.join("")}`)
        .style("left", event.clientX + 14 + "px")
        .style("top", event.clientY + 14 + "px");
    })
    .on("pointerleave", function () {
      d3.select(this.parentNode).selectAll("path").style("opacity", 1);
      tooltip.style("opacity", 0);
    });

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem 1.5rem 0.5rem;max-width:900px;margin:0 auto;">

```js
tierTrendChart(yearStats)
```

<div style="display:flex;gap:20px;flex-wrap:wrap;margin:0.75rem 0 1rem;justify-content:center;">
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#fac45a;display:inline-block"></span>1 album</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#38c4bd;display:inline-block"></span>2 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#9c57f3;display:inline-block"></span>3&ndash;9 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#5127e9;display:inline-block"></span>10+ albums</span>
</div>

</div>

The pattern isn't a steady climb toward higher tiers as the wheel turns —
it's a **U-shape**. The "1 album" share is highest right at both edges of
the 25-year window (2001&ndash;2004 and 2024&ndash;2025, each above 30%) and
lowest in the middle stretch (down near 12&ndash;14% around 2008, 2017, and
2019&ndash;2020).

That matches a real, well-known effect from cohort analysis, just cutting
both ways rather than one: near **2001**, any artist whose prolific run was
already mostly behind them before the list existed gets none of that
earlier history counted — a **left-truncation** effect. Near **2025**, a
brand-new artist simply hasn't had the calendar time yet to earn a second
appearance — the mirror-image **right-censoring** effect. An artist who
happened to arrive mid-window, by contrast, had room on both sides to build
a track record. The instinct that the window's edges distort the count was
right — it just distorts *both* ends, not only the start.

### Is the top of the list reserved for familiar names?

The tier used above is a 25-year *total* — it already knows about albums an
artist hadn't released yet at the time of a given appearance. That's fine
for "how prolific did this artist turn out to be," but it's the wrong
measure for "was this artist already known *at the time* they got this
rank" — an artist's first-ever appearance would get credit for albums still
years in their future. So this chart uses a different count: how many times
this artist had appeared on an Annual list **up to and including this one**
— nothing about album chronology, just this list's own history, kept
separate from the artist-discography-order analysis planned for later.

```js
const asOfBucketed = (() => {
  const byArtist = new Map();
  for (const d of rows) {
    if (!byArtist.has(d.artist_id)) byArtist.set(d.artist_id, []);
    byArtist.get(d.artist_id).push(d);
  }
  const bucketFor = (n) => (n === 1 ? 1 : n === 2 ? 2 : n <= 9 ? 3 : 4);
  const out = new Map();
  for (const [, entries] of byArtist) {
    const years = d3.sort(new Set(entries.map((d) => d.list_year)));
    const numForYear = new Map(years.map((yr, i) => [yr, i + 1]));
    for (const d of entries) out.set(d, bucketFor(numForYear.get(d.list_year)));
  }
  return out;
})();
```

```js
const rankBands = [
  {label: "1–5", lo: 1, hi: 5},
  {label: "6–10", lo: 6, hi: 10},
  {label: "11–20", lo: 11, hi: 20},
  {label: "21–50", lo: 21, hi: 50},
  {label: "51+", lo: 51, hi: 120},
];
const rankBandStats = rankBands.map((band) => {
  const entries = rows.filter((d) => d.rank >= band.lo && d.rank <= band.hi);
  const counts = {1: 0, 2: 0, 3: 0, 4: 0};
  for (const d of entries) counts[asOfBucketed.get(d)]++;
  return {label: band.label, counts, total: entries.length};
});
```

```js
function rankTrendChart(bandStats) {
  const tiers = [1, 2, 3, 4];
  const bucketLabel = {1: "1st appearance", 2: "2nd appearance", 3: "3rd–9th appearance", 4: "10th+ appearance"};
  const width = 640, height = 340;
  const marginL = 40, marginR = 12, marginT = 16, marginB = 54;
  const plotW = width - marginL - marginR;
  const plotH = height - marginT - marginB;
  const gap = 2;

  const x = d3.scaleBand().domain(bandStats.map((d) => d.label)).range([0, plotW]).padding(0.3);
  const y = d3.scaleLinear().domain([0, 100]).range([plotH, 0]);

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  const g = svg.append("g").attr("transform", `translate(${marginL},${marginT})`);

  const yTicks = [0, 25, 50, 75, 100];
  g.append("g").selectAll("line").data(yTicks).join("line")
    .attr("x1", 0).attr("x2", plotW)
    .attr("y1", (d) => y(d)).attr("y2", (d) => y(d))
    .attr("stroke", "#33322f").attr("stroke-width", 1);
  g.append("g").selectAll("text").data(yTicks).join("text")
    .attr("x", -8).attr("y", (d) => y(d))
    .attr("text-anchor", "end").attr("dominant-baseline", "middle")
    .attr("fill", "#898781").attr("font-size", 10)
    .text((d) => d + "%");

  g.append("g").selectAll("text").data(bandStats).join("text")
    .attr("x", (d) => x(d.label) + x.bandwidth() / 2)
    .attr("y", plotH + 20)
    .attr("text-anchor", "middle")
    .attr("fill", "#898781")
    .attr("font-size", 11)
    .text((d) => d.label);

  g.append("text")
    .attr("x", plotW / 2)
    .attr("y", plotH + 42)
    .attr("text-anchor", "middle")
    .attr("fill", "#6b6a64")
    .attr("font-size", 10)
    .text("Rank on that year's list (1 = best)");

  const tooltip = d3.select(document.createElement("div"))
    .attr("style", "position:fixed;pointer-events:none;background:#1a1a19;color:#f0efec;border:1px solid #383835;border-radius:8px;padding:8px 10px;font-size:12px;font-family:var(--sans-serif);opacity:0;transition:opacity 0.1s;z-index:10;min-width:170px;");
  document.body.appendChild(tooltip.node());

  const bars = g.selectAll("g.bar").data(bandStats).join("g")
    .attr("transform", (d) => `translate(${x(d.label)},0)`);

  bars.each(function (d) {
    const gEl = d3.select(this);
    let cum = 0;
    tiers.forEach((t, i) => {
      const cnt = d.counts[t] || 0;
      const pct = (100 * cnt) / d.total;
      const y0 = cum, y1 = cum + pct;
      cum = y1;
      const yTop = y(y1), yBot = y(y0);
      const isTop = i === tiers.length - 1;
      const segH = Math.max(0, yBot - yTop - (isTop ? 0 : gap));
      const w = x.bandwidth();
      const path = isTop
        ? (() => {
            const r = 3;
            const hh = Math.max(0, segH);
            if (hh <= r) return `M${0},${yTop} h${w} v${hh} h${-w} Z`;
            return `M${0},${yTop + r} A${r},${r} 0 0 1 ${r},${yTop} H${w - r} A${r},${r} 0 0 1 ${w},${yTop + r} V${yTop + hh} H${0} Z`;
          })()
        : `M0,${yTop} h${w} v${segH} h${-w} Z`;
      gEl.append("path").attr("d", path).attr("fill", tierColor[t]);
    });
  });

  bars.append("rect")
    .attr("x", 0).attr("y", 0)
    .attr("width", x.bandwidth()).attr("height", plotH)
    .attr("fill", "transparent")
    .on("pointerenter pointermove", function (event, d) {
      d3.select(this.parentNode).selectAll("path").style("opacity", 0.8);
      const lines = tiers.map((t) => {
        const c = d.counts[t] || 0;
        const pct = ((100 * c) / d.total).toFixed(1);
        return `<div style="display:flex;justify-content:space-between;gap:12px"><span>${bucketLabel[t]}</span><b>${pct}%</b></div>`;
      });
      tooltip
        .style("opacity", 1)
        .html(`<b>Rank ${d.label}</b> · ${d.total} entries<br>${lines.join("")}`)
        .style("left", event.clientX + 14 + "px")
        .style("top", event.clientY + 14 + "px");
    })
    .on("pointerleave", function () {
      d3.select(this.parentNode).selectAll("path").style("opacity", 1);
      tooltip.style("opacity", 0);
    });

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem 1.5rem 0.5rem;max-width:700px;margin:0 auto;">

```js
rankTrendChart(rankBandStats)
```

<div style="display:flex;gap:20px;flex-wrap:wrap;margin:0.75rem 0 1rem;justify-content:center;">
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#fac45a;display:inline-block"></span>1st appearance</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#38c4bd;display:inline-block"></span>2nd appearance</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#9c57f3;display:inline-block"></span>3rd&ndash;9th appearance</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:#c9c8c3"><span style="width:12px;height:12px;border-radius:3px;background:#5127e9;display:inline-block"></span>10th+ appearance</span>
</div>

</div>

The gradient is real but softer than it first looked: first-timers make up
25.6% of rank 1&ndash;5 entries, climbing steadily to 54.1% by rank 51 and
below. The top of the list does skew toward familiar names — just not as
absolutely as counting every artist's eventual career total implied. One
specific number worth sitting with: of the 25 albums that have ever hit
**#1**, 7 of them (28%) were that artist's first-ever appearance on an
Annual list. Debuting at the top isn't rare — those artists just tend to
come back.

### How much of each year's list is brand new?

The U-shape chart above already hints at this from a different angle. This
one asks it directly: of everyone on a given year's list, what share had
never appeared on an Annual list before?

```js
const firstYearByArtist = new Map();
for (const d of rows) {
  const cur = firstYearByArtist.get(d.artist_id);
  if (cur === undefined || d.list_year < cur) firstYearByArtist.set(d.artist_id, d.list_year);
}
const turnoverStats = years.map((yr) => {
  const entries = rows.filter((d) => d.list_year === yr);
  const debuts = entries.filter((d) => firstYearByArtist.get(d.artist_id) === yr).length;
  return {year: yr, total: entries.length, debuts, pct: (100 * debuts) / entries.length};
});
```

```js
function turnoverLineChart(stats) {
  const width = 880, height = 320;
  const marginL = 40, marginR = 16, marginT = 16, marginB = 46;
  const plotW = width - marginL - marginR;
  const plotH = height - marginT - marginB;
  const color = "#fac45a";

  const x = d3.scalePoint(stats.map((d) => d.year), [0, plotW]);
  const y = d3.scaleLinear([0, 100], [plotH, 0]);

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  const g = svg.append("g").attr("transform", `translate(${marginL},${marginT})`);

  const yTicks = [0, 25, 50, 75, 100];
  g.append("g").selectAll("line").data(yTicks).join("line")
    .attr("x1", 0).attr("x2", plotW)
    .attr("y1", (d) => y(d)).attr("y2", (d) => y(d))
    .attr("stroke", "#33322f").attr("stroke-width", 1);
  g.append("g").selectAll("text").data(yTicks).join("text")
    .attr("x", -8).attr("y", (d) => y(d))
    .attr("text-anchor", "end").attr("dominant-baseline", "middle")
    .attr("fill", "#898781").attr("font-size", 10)
    .text((d) => d + "%");

  g.append("g").selectAll("text").data(stats).join("text")
    .attr("x", (d) => x(d.year))
    .attr("y", plotH + 10)
    .attr("text-anchor", "end")
    .attr("fill", "#898781")
    .attr("font-size", 9)
    .attr("transform", (d) => `rotate(-55,${x(d.year)},${plotH + 10})`)
    .text((d) => d.year);

  const line = d3.line().x((d) => x(d.year)).y((d) => y(d.pct));
  g.append("path")
    .attr("d", line(stats))
    .attr("fill", "none")
    .attr("stroke", color)
    .attr("stroke-width", 2)
    .attr("stroke-linejoin", "round")
    .attr("stroke-linecap", "round");

  const points = g.append("g").selectAll("g").data(stats).join("g")
    .attr("transform", (d) => `translate(${x(d.year)},${y(d.pct)})`);
  points.append("circle").attr("r", 6).attr("fill", "#1a1a19");
  points.append("circle").attr("r", 4).attr("fill", color);

  const crosshair = g.append("line")
    .attr("y1", 0).attr("y2", plotH)
    .attr("stroke", "#454440").attr("stroke-width", 1)
    .style("opacity", 0);

  const tooltip = d3.select(document.createElement("div"))
    .attr("style", "position:fixed;pointer-events:none;background:#1a1a19;color:#f0efec;border:1px solid #383835;border-radius:8px;padding:8px 10px;font-size:12px;font-family:var(--sans-serif);opacity:0;transition:opacity 0.1s;z-index:10;min-width:150px;");
  document.body.appendChild(tooltip.node());

  g.append("rect")
    .attr("x", 0).attr("y", 0)
    .attr("width", plotW).attr("height", plotH)
    .attr("fill", "transparent")
    .on("pointermove", function (event) {
      const [mx] = d3.pointer(event, this);
      const i = Math.round((mx / plotW) * (stats.length - 1));
      const d = stats[Math.max(0, Math.min(stats.length - 1, i))];
      crosshair.attr("x1", x(d.year)).attr("x2", x(d.year)).style("opacity", 1);
      points.select("circle:last-child").attr("r", (p) => (p === d ? 6 : 4));
      tooltip
        .style("opacity", 1)
        .html(`<b>${d.year}</b><br>${d.debuts} of ${d.total} entries were a first appearance<br><b>${d.pct.toFixed(1)}%</b> turnover`)
        .style("left", event.clientX + 14 + "px")
        .style("top", event.clientY + 14 + "px");
    })
    .on("pointerleave", function () {
      crosshair.style("opacity", 0);
      points.select("circle:last-child").attr("r", 4);
      tooltip.style("opacity", 0);
    });

  return svg.node();
}
```

<div class="card" style="background:#1a1a19;padding:1.5rem;max-width:920px;margin:0 auto;">

```js
turnoverLineChart(turnoverStats)
```

</div>

2001 opens at a trivial 100% — there's no prior list anyone *could* have
already been on, so that point isn't a real finding, just the starting
condition. From 2002 on it's a real, steady decline: 89% turnover in 2002
falling to a floor around 30&ndash;34% by the mid-2010s, where it's stayed
ever since (with some noise — 2021 and 2024 both tick back up over 40%).
Read together with the U-shape chart above, the story is consistent: the
list *matures* over its first decade as a growing pool of past artists
becomes available to return, then settles into a steady state where
roughly a third of any given year's list is a name that's never appeared
before — not shrinking further, just holding.

### When artists come back, how long is the wait?

The charts above already show *that* repeat appearances happen. These
three numbers are about the *timing*.

```js
const gapStats = (() => {
  const byArtist = new Map();
  for (const d of rows) {
    if (!byArtist.has(d.artist_id)) byArtist.set(d.artist_id, {name: d.artist_name, years: []});
    byArtist.get(d.artist_id).years.push(d.list_year);
  }
  const gaps = [];
  let maxGap = {gap: 0};
  let maxSpan = {span: 0};
  for (const [, info] of byArtist) {
    const ys = Array.from(new Set(info.years)).sort((a, b) => a - b);
    if (ys.length < 2) continue;
    for (let i = 1; i < ys.length; i++) {
      const g = ys[i] - ys[i - 1];
      gaps.push(g);
      if (g > maxGap.gap) maxGap = {gap: g, name: info.name, from: ys[i - 1], to: ys[i]};
    }
    const span = ys[ys.length - 1] - ys[0];
    if (span > maxSpan.span) {
      maxSpan = {span, name: info.name, first: ys[0], last: ys[ys.length - 1], appearances: ys.length};
    }
  }
  gaps.sort((a, b) => a - b);
  const median = gaps[Math.floor(gaps.length / 2)];
  const backToBackPct = (100 * gaps.filter((g) => g === 1).length) / gaps.length;
  return {median, backToBackPct, maxGap, maxSpan};
})();
```

```js
function gapStatTiles(stats) {
  const tileDefs = [
    {
      label: "Median gap between an artist's appearances",
      value: `${stats.median} years`,
      caption: `Only ${stats.backToBackPct.toFixed(1)}% of comebacks are back-to-back (a 1-year gap) — most repeat artists take a break first.`,
    },
    {
      label: "Longest gap between two appearances",
      value: `${stats.maxGap.gap} years`,
      caption: `${stats.maxGap.name}, ${stats.maxGap.from} → ${stats.maxGap.to}.`,
    },
    {
      label: "Longest span, first appearance to last",
      value: `${stats.maxSpan.span} years`,
      caption: `${stats.maxSpan.name}, ${stats.maxSpan.first}–${stats.maxSpan.last} — with ${stats.maxSpan.appearances} appearances across the whole span.`,
    },
  ];

  const wrap = document.createElement("div");
  wrap.style.cssText = "display:flex;flex-wrap:wrap;";

  tileDefs.forEach((t, i) => {
    const tile = document.createElement("div");
    tile.style.cssText = `flex:1 1 220px;padding:0 1.5rem;${i > 0 ? "border-left:1px solid #33322f;" : ""}`;

    const label = document.createElement("div");
    label.style.cssText = "color:#898781;font-size:12px;text-transform:uppercase;letter-spacing:0.04em;margin-bottom:0.5rem;";
    label.textContent = t.label;

    const value = document.createElement("div");
    value.style.cssText = "color:#f0efec;font-size:32px;font-weight:600;font-family:var(--sans-serif);line-height:1.1;";
    value.textContent = t.value;

    const caption = document.createElement("div");
    caption.style.cssText = "color:#c9c8c3;font-size:13px;margin-top:0.6rem;line-height:1.4;";
    caption.textContent = t.caption;

    tile.append(label, value, caption);
    wrap.append(tile);
  });

  return wrap;
}
```

<div class="card" style="background:#1a1a19;padding:1.75rem 1.5rem;max-width:920px;margin:0 auto;">

```js
gapStatTiles(gapStats)
```

</div>
