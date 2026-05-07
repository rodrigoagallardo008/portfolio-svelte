<script>
	import { base } from '$app/paths';
	import projects from '$lib/projects.json';
	import Project from '$lib/Project.svelte';
	import reading from '$lib/reading.json';
	import ReadingItem from '$lib/ReadingItem.svelte';
	import { onMount } from "svelte";

	let githubData = null;
	let loading = true;
	let error = null;

	onMount(async () => {
		try {
			let response = await fetch("https://api.github.com/users/rodrigoagallardo008");
			githubData = await response.json();
		} catch (err) {
			error = err;
		}
		loading = false;
	});
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

<section class="github-stats">
  <h2>GitHub Stats</h2>
  {#if loading}
    <p>Loading...</p>
  {:else if error}
    <p>Something went wrong: {error.message}</p>
  {:else}
    <dl>
      <dt>Public Repos</dt>
      <dd>{githubData.public_repos}</dd>
      <dt>Followers</dt>
      <dd>{githubData.followers}</dd>
      <dt>Following</dt>
      <dd>{githubData.following}</dd>
      <dt>Member Since</dt>
      <dd>{new Date(githubData.created_at).getFullYear()}</dd>
    </dl>
  {/if}
</section>

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

<style>
  .github-stats dl {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 0.5em;
    text-align: center;
  }
  .github-stats dt {
    grid-row: 1;
    font-size: 0.85rem;
    color: #888;
    text-transform: uppercase;
  }
  .github-stats dd {
    grid-row: 2;
    font-size: 2rem;
    font-weight: bold;
    margin: 0;
  }
</style>
