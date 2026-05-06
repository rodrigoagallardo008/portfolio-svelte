<script>
	import { base } from '$app/paths';
</script>

<svelte:head>
	<title>Projects – Rodrigo Gallardo</title>
</svelte:head>

<nav>
	<a href=".">Home</a>
	<a class="current" href="projects">Projects</a>
	<a href="contact">Contact</a>
	<a href="resume">Resume</a>
	<a href="https://github.com/rodrigoagallardo008" target="_blank">GitHub</a>
</nav>

<h1>Projects</h1>

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

</div>
