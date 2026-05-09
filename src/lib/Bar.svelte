<script>
  import * as d3 from 'd3';

  export let data = [];
  export let title = "Projects per Year";

  let width = 600, height = 300;
  let margin = { top: 40, right: 120, bottom: 50, left: 60 };
  let innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  let selectedIndex = -1;
  let liveText = "";
  let showChart = true;

  $: xScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerWidth])
    .padding(0.2);

  $: yScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value) || 0])
    .range([innerHeight, 0])
    .nice();

  $: colorScale = d3.scaleOrdinal()
    .domain(data.map(d => d.label))
    .range(d3.quantize(d3.interpolateBlues, Math.max(data.length, 2)));

  $: description = `A bar chart showing project counts by year. ${data.map(d => `${d.label}: ${d.value} projects`).join(', ')}.`;

  function toggleBar(index, event) {
    if (!event.key || event.key === "Enter") {
      selectedIndex = selectedIndex === index ? -1 : index;
      const d = data[index];
      liveText = selectedIndex === -1
        ? "Selection cleared."
        : `${d.label}: ${d.value} projects selected.`;
    }
  }

  function toggleView() {
    showChart = !showChart;
    liveText = showChart ? "Bar chart view shown." : "Table view shown.";
  }

  let xAxis, yAxis;
  $: if (xAxis && yAxis) {
    d3.select(xAxis).call(d3.axisBottom(xScale));
    d3.select(yAxis).call(
      d3.axisLeft(yScale).tickFormat(d => Number.isInteger(d) ? d : "")
    );
  }

  // annotation: find max label
  $: annotationLabel = data.length > 0
    ? data.reduce((a, b) => a.value > b.value ? a : b).label
    : null;
</script>

<button
  on:click={toggleView}
  aria-pressed={!showChart}
  aria-label="Toggle between bar chart and table view"
  class="toggle-button">
  {showChart ? 'Show Table' : 'Show Chart'}
</button>

{#if showChart}
<div class="container">
  <svg
    viewBox="0 0 {width} {height}"
    role="img"
    aria-labelledby="bar-title bar-desc"
  >
    <title id="bar-title">{title}</title>
    <desc id="bar-desc">{description}</desc>

    <g transform="translate({margin.left},{margin.top})">
      <g bind:this={xAxis} transform="translate(0,{innerHeight})" />
      <g bind:this={yAxis} />

      <!-- x-axis label -->
      <text
        x={innerWidth / 2}
        y={innerHeight + 40}
        text-anchor="middle"
        font-size="12"
        fill="currentColor">Year</text>

      <!-- y-axis label -->
      <text
        transform="rotate(-90)"
        x={-innerHeight / 2}
        y={-45}
        text-anchor="middle"
        font-size="12"
        fill="currentColor">Number of Projects</text>

      <!-- bars -->
      {#each data as d, index}
        <rect
          x={xScale(d.label)}
          y={yScale(d.value)}
          width={xScale.bandwidth()}
          height={innerHeight - yScale(d.value)}
          fill={colorScale(d.label)}
          stroke="black"
          stroke-width="1"
          opacity={selectedIndex === -1 || selectedIndex === index ? 1 : 0.45}
          tabindex="0"
          role="button"
          aria-label="{d.label}: {d.value} project{d.value !== 1 ? 's' : ''}"
          aria-pressed={selectedIndex === index}
          on:click={e => toggleBar(index, e)}
          on:keyup={e => toggleBar(index, e)}
        />
      {/each}

      <!-- annotation for most projects -->
      {#if annotationLabel}
        {@const ax = xScale(annotationLabel) + xScale.bandwidth()}
        {@const ay = yScale(data.find(d => d.label === annotationLabel)?.value ?? 0)}
        <line x1={ax} y1={ay} x2={ax + 20} y2={ay - 10} stroke="black" stroke-width="1" />
        <text x={ax + 22} y={ay - 12} font-size="11" fill="currentColor" font-style="italic">Most projects</text>
      {/if}
    </g>

    <!-- legend -->
    <g transform="translate({width - margin.right + 10}, {margin.top})">
      {#each data as d, i}
        <rect x="0" y={i * 22} width="14" height="14" fill={colorScale(d.label)} stroke="black" stroke-width="0.5" />
        <text x="18" y={i * 22 + 11} font-size="11" fill="currentColor">
          {d.label} <tspan font-style="italic" opacity="0.7">({d.value})</tspan>
        </text>
      {/each}
    </g>
  </svg>
</div>
{:else}
<table aria-label="Table showing project counts by year" class="data-table">
  <caption>Projects by Year</caption>
  <thead>
    <tr>
      <th id="year-header" scope="col">Year</th>
      <th id="projects-header" scope="col">Projects</th>
    </tr>
  </thead>
  <tbody>
    {#each data as d, i}
      <tr>
        <th id="row-{i}" scope="row">{d.label}</th>
        <td aria-labelledby="row-{i} projects-header">{d.value}</td>
      </tr>
    {/each}
  </tbody>
</table>
{/if}

<p aria-live="polite" class="sr-only">{liveText}</p>

<style>
  .container { position: relative; }

  svg { overflow: visible; }

  rect {
    transition: 300ms;
    outline: none;
    cursor: pointer;
    stroke: black;
    stroke-width: 1;
  }

  svg:hover rect:not(:hover),
  .container:focus-within rect:not(:focus-visible) {
    opacity: 50%;
  }

  rect:focus-visible {
    stroke: white;
    stroke-width: 2px;
    stroke-dasharray: 4;
  }

  .toggle-button {
    margin-bottom: 0.5em;
    padding: 0.4em 1em;
    cursor: pointer;
    border: 1px solid #ccc;
    border-radius: 4px;
    background: #f5f5f5;
    font-size: 0.9rem;
  }

  .toggle-button:hover { background: #e0e0e0; }

  .sr-only {
    position: absolute;
    left: -9999px;
    width: 1px;
    height: 1px;
    overflow: hidden;
  }

  .data-table {
    margin-top: 1rem;
    margin-bottom: 1rem;
    border-collapse: collapse;
    width: 100%;
    max-width: 30em;
  }

  .data-table caption {
    font-weight: bold;
    margin-bottom: 0.5em;
    text-align: left;
  }

  .data-table th,
  .data-table td {
    border: 1px solid #ccc;
    padding: 0.5em;
    text-align: left;
  }

  .data-table th { background-color: #f0f0f0; }
</style>
