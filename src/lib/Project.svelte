<script>
  import { base } from '$app/paths';
  export let data = {};
</script>

<article>
  <h2>{data.title}</h2>
  <p class="year">{data.year}</p>
  <img src="{base}/{data.image}" alt={data.title} />
  <p>{data.description}</p>
</article>

<style>
  .year { font-size: 0.8rem; color: #888; margin: 0 0 0.5em; }
</style>
