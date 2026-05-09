<script>
  import { base } from '$app/paths';
  import projects from '$lib/projects.json';
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

<h1 style="font-size: 1.5rem">{projects.length} Projects over {range} Years</h1>

<div style="display: flex; justify-content: center;">
  <Bar data={barData} />
</div>

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

<h2>Project Timeline</h2>
<p>Scroll down to see a timeline of my projects and how they've shaped my growth as a designer and researcher.</p>
<ProjectNarrative />

<style>
  .projects {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1.5rem;
    margin-bottom: 3rem;
  }
  article {
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }
  article img {
    width: 100%;
    height: 160px;
    object-fit: cover;
    border-radius: 4px;
  }
  article h2 { font-size: 1rem; margin: 0; }
  article p { font-size: 0.85rem; color: #555; flex: 1; }
  article a { font-size: 0.85rem; font-weight: bold; }
</style>