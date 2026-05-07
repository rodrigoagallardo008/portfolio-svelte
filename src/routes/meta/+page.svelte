<script>
  import { base } from '$app/paths';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import BarHorizontal from '$lib/BarHorizontal.svelte';
  let barData = [];
  onMount(async () => {
    const locData = await d3.csv(`${base}/loc.csv`, row => ({
      ...row,
      line: Number(row.line),
      length: Number(row.length),
      depth: Number(row.depth)
    }));
    barData = d3.rollups(locData, v => v.length, d => d.type)
      .map(([lang, count]) => ({ label: lang, value: count }))
      .sort((a, b) => b.value - a.value);
  });
</script>
<svelte:head><title>Meta – Rodrigo Gallardo</title></svelte:head>
<h1>Meta</h1>
<p>Stats about this codebase, including lines of code per language.</p>
<BarHorizontal data={barData} />
