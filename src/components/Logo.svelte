<script>
import IconSvg from 'svelte-icon'
import drum from '../images/drum.svg'

export let size = '512px'
</script>
<span><IconSvg data={drum} viewBox="0 0 512 512" size={size}/></span>
<style>
* :global(path:nth-child(1)) {
  fill: var(--clap-color) !important;
}

* :global(path:nth-child(2)) {
  fill: var(--snare-color) !important;
}

* :global(path:nth-child(3)) {
  fill: var(--high-color) !important;
}

* :global(path:nth-child(4)) {
  fill: var(--kick-color) !important;
}

* :global(path:nth-child(5)) {
  fill: #fff !important;
}

* :global(path:nth-child(6)) {
  fill: #fff !important;
}

* :global(path:nth-child(7)) {
  fill: #fff !important;
}
</style>
