<script lang="ts">
  import ArrowTopRightOnSquare from './icons/ArrowTopRightOnSquare.svelte';
  export let href: string;
  export let text: string;
  export let size: number = 16;
</script>

<a
  href={href}
  target="_blank"
  rel="noopener noreferrer"
  style="display: inline-flex; align-items: center; gap: 0.4rem; color: inherit; text-decoration: none;"
>
  {text}
  <ArrowTopRightOnSquare {size} />
</a>
