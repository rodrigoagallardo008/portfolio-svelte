<script>
  import * as d3 from 'd3';
  export let data = [];
  export let title = "Lines of Code by Language";
  let width = 500, height = 200;
  let margin = { top: 30, right: 160, bottom: 50, left: 80 };
  let innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;
  let xAxis, yAxis;
  $: yScale = d3.scaleBand().domain(data.map(d => d.label)).range([0, innerHeight]).padding(0.2);
  $: xScale = d3.scaleLinear().domain([0, d3.max(data, d => d.value) || 1]).range([0, innerWidth]);
  $: colorScale = d3.scaleOrdinal(d3.schemeTableau10).domain(data.map(d => d.label));
  $: maxBar = d3.greatest(data, d => d.value);
  $: if (xAxis && yAxis) {
    d3.select(xAxis).call(
      d3.axisBottom(xScale).ticks(Math.min(d3.max(data, d => d.value) || 10, 10))
    );
    d3.select(yAxis).call(d3.axisLeft(yScale));
  }
</script>
<div class="container">
  <svg viewBox="0 0 {width} {height}">
    <text x={margin.left + innerWidth / 2} y={margin.top / 2} text-anchor="middle" class="chart-title">{title}</text>
    <g transform="translate({margin.left}, {margin.top + innerHeight})" bind:this={xAxis} />
    <g transform="translate({margin.left}, {margin.top})" bind:this={yAxis} />
    <g transform="translate({margin.left}, {margin.top})">
      {#each data as d}
        <rect x={0} y={yScale(d.label)} width={xScale(d.value)} height={yScale.bandwidth()} fill={colorScale(d.label)} />
      {/each}
      {#if maxBar}
        <rect x={0} y={yScale(maxBar.label)} width={xScale(maxBar.value)} height={yScale.bandwidth()} fill="none" stroke="currentColor" stroke-width="2" />
        <line x1={xScale(maxBar.value)} y1={yScale(maxBar.label) + yScale.bandwidth() / 2} x2={xScale(maxBar.value) + 30} y2={yScale(maxBar.label) + yScale.bandwidth() / 2} stroke="currentColor" stroke-width="1" />
        <text x={xScale(maxBar.value) + 35} y={yScale(maxBar.label) + yScale.bandwidth() / 2} dominant-baseline="middle" class="annotation">Most lines</text>
      {/if}
      <text x={innerWidth / 2} y={innerHeight + margin.bottom - 10} text-anchor="middle" class="axis-label">Lines of Code</text>
      <text x={-(innerHeight / 2)} y={-margin.left + 15} text-anchor="middle" transform="rotate(-90)" class="axis-label">Language</text>
    </g>
  </svg>
  <ul class="legend">
    {#each data as d}
      <li style="--color: {colorScale(d.label)}"><span class="swatch"></span>{d.label} <em>({d.value})</em></li>
    {/each}
  </ul>
</div>
<style>
  svg { max-width: 100%; height: auto; overflow: visible; }
  .container { display: flex; align-items: flex-start; gap: 1rem; }
  .legend { list-style: none; padding: 0; margin: 0; display: flex; flex-direction: column; gap: 0.5rem; }
  .legend li { display: flex; align-items: center; gap: 0.5rem; font-size: 0.85rem; }
  .swatch { display: inline-block; width: 12px; height: 12px; background-color: var(--color); border-radius: 2px; flex-shrink: 0; }
  .chart-title { font-size: 0.85em; font-weight: bold; fill: currentColor; }
  .axis-label { font-size: 0.7em; fill: currentColor; }
  .annotation { font-size: 0.65em; fill: currentColor; font-style: italic; }
</style>
