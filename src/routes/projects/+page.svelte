<script>
	import { base } from '$app/paths';
	import projects from '$lib/projects.json';
	import Project from '$lib/Project.svelte';
	import reading from '$lib/reading.json';
	import ReadingItem from '$lib/ReadingItem.svelte';
	import ProjectNarrative from '$lib/ProjectNarrative.svelte';
	import * as d3 from 'd3';
	import Bar from '$lib/Bar.svelte';

	let years = projects.map(proj => proj.year);
	let range = Math.max(...years) - Math.min(...years);
	$: barData = d3.rollups(projects, v => v.length, d => d.year).map(([year, count]) => ({ label: String(year), value: count }));
</script>

<svelte:head>
	<title>Projects – Rodrigo Gallardo</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

<Bar data={barData} />

<p>Scroll down to see a timeline of my projects and how they've shaped my growth as a designer and researcher.</p>
<ProjectNarrative />
<p class="outro">Thanks for scrolling! Explore all projects below.</p>

<div class="projects">

	<article>
		<h2>PSET 2: Corporate Landlords & Evictions</h2>
		<img src="{base}/pset2figures/Neighborhood Corp Ownership.png"
			 alt="Chart showing neighborhood corporate ownership trends in Boston 2004-2024" />
		<p>Exploratory data analysis of corporate ownership and owner-occupancy rates across Boston neighborhoods from 2004–2024, examining racial and demographic patterns.</p>
		<a href="{base}/projects/pset2.html">View Project →</a>
	</article>

	<article>
		<h2>PSET 3: Visualization Critique & Redesign</h2>
		<img src="{base}/pset3figures/Final2.png"
			 alt="Redesigned visualization from PSET 3" />
		<p>Critical analysis and redesign of existing data visualizations, applying principles of clarity, accuracy, and visual communication.</p>
		<a href="{base}/projects/pset3.html">View Project →</a>
	</article>

	<article>
		<h2>PSET 4: Abortion Data Analysis</h2>
		<img src="{base}/pset4figures/Visualization 1.png"
			 alt="Visualization of abortion data by state" />
		<p>Data analysis and visualization of abortion statistics by state using Guttmacher Institute data, exploring geographic and policy patterns.</p>
		<a href="{base}/projects/pset4.html">View Project →</a>
	</article>

	<article>
		<h2>BikeWatch</h2>
		<img src="{base}/timeline/bikewatching.png"
			 alt="BikeWatch map visualization of BlueBike stations in Boston" />
		<p>An interactive map visualization of BlueBike station traffic in the Boston/Cambridge area. Explore bike lane networks, station demand by time of day, and cycling isochrones from any station.</p>
		<a href="https://rodrigoagallardo008.github.io/bikewatching/" target="_blank">View Project →</a>
	</article>

</div>

<style>
  .outro { margin-bottom: 3rem; }
</style>
