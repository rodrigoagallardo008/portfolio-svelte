<script>
  import { base } from '$app/paths';
  import { page } from '$app/stores';

  let pages = [
    { url: "/", title: "Home" },
    { url: "/projects", title: "Projects" },
    { url: "/contact", title: "Contact" },
    { url: "/resume", title: "Resume" },
    { url: "https://github.com/rodrigoagallardo008", title: "GitHub" }
  ];

  let localStorage = globalThis.localStorage ?? {};
  let colorScheme = localStorage.colorScheme ?? "light dark";

  let root = globalThis.document?.documentElement;
  $: root?.style.setProperty("color-scheme", colorScheme);
  $: localStorage.colorScheme = colorScheme;
</script>

<label class="color-scheme-switch">
  Theme:
  <select bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
</label>

<nav>
  {#each pages as p}
    <a
      href={p.url.startsWith("http") ? p.url : base + p.url}
      target={p.url.startsWith("http") ? "_blank" : null}
      class:current={p.url === "/"
        ? $page.url.pathname === base + "/"
        : $page.url.pathname.startsWith(base + p.url)}
    >
      {p.title}
    </a>
  {/each}
</nav>

<slot />

<style>
  .color-scheme-switch {
    position: absolute;
    top: 1rem;
    right: 1rem;
    display: inline-flex;
    gap: 4px;
    font-size: 80%;
    font-family: inherit;
  }

  :global(:root) {
    --accent: oklch(45.457% 0.31135 265.276);
    --nav-border: oklch(50% 10% 200 / 40%);
    --current: oklch(70% 20% 50);
    --card: white;
    --bg: #f9f9f9;
    --text: #333;
  }

  :global(body) {
    background-color: var(--bg);
    color: var(--text);
  }

  :global([style*="color-scheme: dark"]) {
    --bg: #1a1a1a;
    --text: #f0f0f0;
    --card: #2a2a2a;
    --nav-border: rgba(255,255,255,0.25);
    --current: oklch(75% 20% 50);
  }

  :global(nav) {
    background-color: var(--card) !important;
  }

  :global(.projects article) {
    background-color: var(--card) !important;
  }

  :global(.contact-form) {
    background-color: var(--card) !important;
  }
</style>
