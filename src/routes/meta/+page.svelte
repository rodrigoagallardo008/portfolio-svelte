<script>
  import { base } from '$app/paths';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';
  import LineChart from '$lib/LineChart.svelte';
  import { computePosition, autoPlacement, offset } from '@floating-ui/dom';

  let locData = [];
  let barData = [];
  let commits = [];
  let hoveredIndex = -1;
  let hoveredCommit = {};
  let clickedCommits = [];
  let tooltipPosition = {x: 0, y: 0};
  let commitTooltip;
  let width = 1000, height = 600;
  let margin = { top: 20, right: 20, bottom: 30, left: 50 };
  let usableArea = {
    top: margin.top,
    right: width - margin.right,
    bottom: height - margin.bottom,
    left: margin.left
  };
  usableArea.width = usableArea.right - usableArea.left;
  usableArea.height = usableArea.bottom - usableArea.top;
  let xAxis, yAxis, yAxisGridlines;
  let svg;
  let linesByDate = [];

  $: hoveredCommit = commits[hoveredIndex] ?? {};
  $: [minDate, maxDate] = commits.length ? d3.extent(commits, d => d.date) : [new Date(), new Date()];
  $: maxDatePlusOne = (() => { let d = new Date(maxDate ?? new Date()); d.setDate(d.getDate() + 1); return d; })();
  $: xScale = d3.scaleTime().domain([minDate ?? new Date(), maxDatePlusOne]).range([usableArea.left, usableArea.right]).nice();
  $: yScale = d3.scaleLinear().domain([24, 0]).range([usableArea.bottom, usableArea.top]);
  $: rScale = d3.scaleSqrt().domain(d3.extent(commits, d => d.totalLines)).range([5, 30]);
  let brushSelection = null;

  function brushed(evt) {
    brushSelection = evt.selection;
  }

  function isCommitBrushed(commit) {
    if (!brushSelection) return false;
    let min = { x: brushSelection[0][0], y: brushSelection[0][1] };
    let max = { x: brushSelection[1][0], y: brushSelection[1][1] };
    let x = xScale(commit.datetime);
    let y = yScale(commit.hourFrac);
    return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
  }

  $: brushedCommits = brushSelection ? commits.filter(isCommitBrushed) : [];
  $: selectedCommits = Array.from(new Set([...clickedCommits, ...brushedCommits]));

  $: {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d % 24).padStart(2, "0") + ":00"));
    d3.select(yAxisGridlines).call(d3.axisLeft(yScale).tickFormat("").tickSize(-usableArea.width));
    d3.select(svg).call(
      d3.brush()
        .extent([[usableArea.left, usableArea.top], [usableArea.right, usableArea.bottom]])
        .on("start brush end", brushed)
    );
    d3.select(svg).selectAll(".dots, .overlay ~ *").raise();
  }
  $: selectedLines = (selectedCommits.length > 0 ? selectedCommits.flatMap(d => d.lines) : locData);
  $: selectedCounts = d3.rollup(selectedLines, v => v.length, d => d.type);
  $: allTypes = Array.from(new Set(locData.map(d => d.type)));
  $: barData = allTypes.map(type => ({ label: String(type), value: selectedCounts.get(type) || 0 }));
  $: barTitle = selectedCommits.length > 0
    ? `Language Breakdown for ${selectedCommits.length} Selected Commit${selectedCommits.length > 1 ? 's' : ''}`
    : "Language Breakdown for Entire Website";

  $: {
    const rolled = d3.rollups(
      locData,
      v => v.length,
      d => d3.timeDay.floor(d.datetime)
    ).map(([date, count]) => ({ date, count }));

    const [minDate, maxDate] = d3.extent(rolled, d => d.date);
    const allDays = minDate && maxDate ? d3.timeDays(minDate, d3.timeDay.offset(maxDate, 1)) : [];

    linesByDate = allDays.map(date => ({
      date,
      count: rolled.find(d => d.date.getTime() === date.getTime())?.count ?? 0
    }));
  }

  onMount(async () => {
    locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line),
      depth: Number(row.depth),
      length: Number(row.length),
      date: new Date(row.date + "T00:00" + row.timezone),
      datetime: new Date(row.datetime)
    }));

    commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
      let first = lines[0];
      let {author, date, time, timezone, datetime} = first;
      return {
        id: commit,
        url: "https://github.com/rodrigoagallardo008/portfolio-svelte/commit/" + commit,
        author, date, time, timezone, datetime,
        hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
        totalLines: lines.length,
        lines: lines
      };
    });

    commits = d3.sort(commits, d => -d.totalLines);

    barData = allTypes.map(type => ({ label: String(type), value: selectedCounts.get(type) || 0 }));
  });

  async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;
    if (evt.type === "mouseenter") {
      hoveredIndex = index;
      tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
        strategy: "fixed",
        middleware: [offset(5), autoPlacement()],
      });
    } else if (evt.type === "mouseleave") {
      hoveredIndex = -1;
    } else if (evt.type === "click") {
      let commit = commits[index];
      if (!clickedCommits.includes(commit)) {
        clickedCommits = [...clickedCommits, commit];
      } else {
        clickedCommits = clickedCommits.filter(c => c !== commit);
      }
    }
  }
</script>
<svelte:head><title>Meta – Rodrigo Gallardo</title></svelte:head>

<h1>Meta</h1>
<p>Stats about this codebase, including lines of code per language.</p>

<dl class="stats">
  <dt>Total <abbr title="Lines of Code">LOC</abbr></dt>
  <dd>{locData.length}</dd>
  <dt>Total Commits</dt>
  <dd>{commits.length}</dd>
  <dt>Files</dt>
  <dd>{Array.from(new Set(locData.map(d => d.file))).length}</dd>
</dl>

<h2>Commits by Time of Day</h2>

<svg viewBox="0 0 {width} {height}" bind:this={svg}>
  <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
  <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
  <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
  <g class="dots">
    {#each commits as commit, index}
      <circle
        cx={xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r={rScale(commit.totalLines)}
        fill="steelblue"
        fill-opacity="0.6"
        role="button"
        tabindex="0"
        aria-label="Commit {commit.id} on {commit.datetime?.toLocaleDateString()}"
        class:selected={selectedCommits.includes(commit)}
        on:mouseenter={evt => dotInteraction(index, evt)}
        on:mouseleave={evt => dotInteraction(index, evt)}
        on:click={evt => dotInteraction(index, evt)}
        on:keydown={evt => evt.key === 'Enter' && dotInteraction(index, evt)}
      />
    {/each}
  </g>
</svg>

<dl class="info tooltip" hidden={hoveredIndex === -1}
    bind:this={commitTooltip}
    style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px">
  <dt>Commit</dt>
  <dd><a href={hoveredCommit.url} target="_blank">{hoveredCommit.id}</a></dd>
  <dt>Date</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"})}</dd>
  <dt>Time</dt>
  <dd>{hoveredCommit.datetime?.toLocaleString("en", {timeStyle: "short"})}</dd>
  <dt>Author</dt>
  <dd>{hoveredCommit.author}</dd>
  <dt>Lines Edited</dt>
  <dd>{hoveredCommit.totalLines}</dd>
</dl>

<h2>Language Breakdown</h2>
<BarHorizontal data={barData} title={barTitle} />

<LineChart data={linesByDate} />

<style>
  svg { overflow: visible; }
  .gridlines { stroke-opacity: 0.2; }
  circle {
    transition: 200ms;
    &:hover { fill: darkgreen; }
  }
  .selected { fill: var(--accent, steelblue); }
  dl.stats {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    text-align: center;
    gap: 0.5em;
  }
  dl.stats dt { font-size: 0.85rem; color: #888; text-transform: uppercase; grid-row: 1; }
  dl.stats dd { font-size: 2rem; font-weight: bold; margin: 0; grid-row: 2; }
  dl.info {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 0.25em 1em;
    background: white;
    padding: 1em;
    border-radius: 0.5em;
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    position: fixed;
    transition-duration: 500ms;
    transition-property: opacity, visibility;
  }
  dl.info[hidden]:not(:hover, :focus-within) {
    opacity: 0;
    visibility: hidden;
  }
  dl.info dt { color: #888; font-size: 0.85em; }
  @keyframes marching-ants {
    to { stroke-dashoffset: -8; }
  }
  svg :global(.selection) {
    fill-opacity: 10%;
    stroke: black;
    stroke-opacity: 70%;
    stroke-dasharray: 5 3;
    animation: marching-ants 2s linear infinite;
  }
</style>
