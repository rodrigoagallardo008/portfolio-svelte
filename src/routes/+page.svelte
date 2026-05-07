<script>
	import { base } from '$app/paths';
	import projects from '$lib/projects.json';
	import Project from '$lib/Project.svelte';
	import reading from '$lib/reading.json';
	import ReadingItem from '$lib/ReadingItem.svelte';
</script>

<svelte:head>
	<title>Rodrigo Gallardo: Personal Site and Portfolio</title>
</svelte:head>

<h1>Rodrigo Gallardo</h1>

<div class="home-header">
	<p>
		I am a graduate student and researcher at MIT working at the intersection of
		human-computer interaction, design, and data. I focus on building interactive
		systems that enhance usability, ergonomics, and real-world performance.
	</p>
	<img src="{base}/Rodrigo_Headshot.png"
		 alt="Photo of Rodrigo Gallardo"
		 style="width:150px;">
</div>

<h2>Latest Projects</h2>
<div class="projects highlights">
  {#each projects.slice(0, 3) as p}
    <Project data={p} />
  {/each}
</div>

<h2>What I'm Reading</h2>
<div class="reading">
  {#each reading as item}
    <ReadingItem data={item} />
  {/each}
</div>
