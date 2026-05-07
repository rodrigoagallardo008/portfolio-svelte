<script>
  import Scrolly from "svelte-scrolly";
  import { base } from "$app/paths";
  import projects from "$lib/projects.json";

  let scrollyProgress = 0;
  let sorted_projects = [...projects].sort((a, b) => a.year - b.year);
  let progressPerProject = 100 / sorted_projects.length;
  $: activeProjectIdx = Math.min(
    sorted_projects.length - 1,
    Math.floor(scrollyProgress / progressPerProject)
  );
</script>

<div class="scrolly-wrapper">
  <Scrolly bind:progress={scrollyProgress}>
    {#each sorted_projects as project}
      <section class="step">
        <div class="step-content">
          <h3>{project.title} ({project.year})</h3>
          <p>{project.story}</p>
        </div>
      </section>
    {/each}
    <svelte:fragment slot="viz">
      <div class="project-detail">
        <h3>{sorted_projects[activeProjectIdx].year}</h3>
        <img
          src="{base}/{sorted_projects[activeProjectIdx].image}"
          alt={sorted_projects[activeProjectIdx].title}
        />
        <p>{sorted_projects[activeProjectIdx].title}</p>
      </div>
    </svelte:fragment>
  </Scrolly>
</div>

<style>
  .scrolly-wrapper {
    width: min(90ch, 90vw);
    position: relative;
    left: 50%;
    transform: translateX(-50%);
  }
  .step {
    min-height: 40vh;
    padding: 1rem;
  }
  .step-content {
    border-left: 4px solid oklch(45.457% 0.31135 265.276);
    padding: 1.5rem 2rem;
    background: var(--card, white);
    border-radius: 0 0.5rem 0.5rem 0;
    box-shadow: 0 4px 12px rgba(0,0,0,0.08);
  }
  .step-content h3 { margin: 0 0 0.5rem; font-size: 1.1rem; }
  .step-content p { margin: 0; line-height: 1.6; }
  .project-detail {
    padding: 1rem;
    width: 100%;
    text-align: center;
  }
  .project-detail img {
    width: 100%;
    max-height: 250px;
    object-fit: cover;
    border-radius: 0.5rem;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  }
  .project-detail h3 {
    font-size: 2rem;
    font-weight: bold;
    margin-bottom: 0.5rem;
    color: oklch(45.457% 0.31135 265.276);
  }
</style>
