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
quarter-century, not just this one year.

```js
const rows = FileAttachment("data/annual_circle_2001_2025.csv").csv({typed: true});
```

```js
const tierColor = {1: "#74eeee", 2: "#39bcbc", 3: "#008c8c", 4: "#005e60"};
const tierSize = {1: 2.2, 2: 2.8, 3: 3.6, 4: 4.8};

function annualCircle(data, {width = 760} = {}) {
  const years = d3.sort(new Set(data.map((d) => d.list_year)));
  const height = width;
  const cx = width / 2, cy = height / 2;
  const maxRank = d3.max(data, (d) => d.rank);
  const innerR = 18;
  const outerR = Math.min(width, height) / 2 - 56;
  const rStep = (outerR - innerR) / maxRank;

  // 12:00 clockwise to ~11:00 -- a deliberate gap, not a closed circle, so
  // the layout never implies the timeline wraps back on itself.
  const gapDeg = 30;
  const sweepDeg = 360 - gapDeg;
  const angleForYear = d3.scaleLinear()
    .domain([0, years.length - 1])
    .range([0, sweepDeg]);

  function xy(year, rank) {
    const a = (angleForYear(years.indexOf(year)) - 90) * (Math.PI / 180);
    const r = innerR + rank * rStep;
    return [cx + r * Math.cos(a), cy + r * Math.sin(a)];
  }

  const svg = d3.create("svg")
    .attr("viewBox", [0, 0, width, height])
    .attr("width", width)
    .attr("height", height)
    .attr("style", "background:#1a1a19;border-radius:12px;max-width:100%;height:auto;font-family:var(--sans-serif);");

  // Year labels, placed just past each spoke's own last dot.
  svg.append("g")
    .selectAll("text")
    .data(years)
    .join("text")
    .attr("x", (yr) => {
      const n = d3.max(data.filter((d) => d.list_year === yr), (d) => d.rank);
      return xy(yr, n + 5)[0];
    })
    .attr("y", (yr) => {
      const n = d3.max(data.filter((d) => d.list_year === yr), (d) => d.rank);
      return xy(yr, n + 5)[1];
    })
    .attr("fill", "#898781")
    .attr("font-size", 11)
    .attr("text-anchor", "middle")
    .attr("dominant-baseline", "middle")
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
resize((width) => annualCircle(rows, {width: Math.min(width, 760)}))
```

</div>

<div style="display:flex;gap:20px;flex-wrap:wrap;margin-top:1rem;justify-content:center;">
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:var(--theme-foreground-muted)"><span style="width:11px;height:11px;border-radius:50%;background:#74eeee;display:inline-block"></span>1 album</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:var(--theme-foreground-muted)"><span style="width:14px;height:14px;border-radius:50%;background:#39bcbc;display:inline-block"></span>2 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:var(--theme-foreground-muted)"><span style="width:18px;height:18px;border-radius:50%;background:#008c8c;display:inline-block"></span>3&ndash;9 albums</span>
  <span style="display:flex;align-items:center;gap:6px;font-size:13px;color:var(--theme-foreground-muted)"><span style="width:24px;height:24px;border-radius:50%;background:#005e60;display:inline-block"></span>10+ albums</span>
</div>
