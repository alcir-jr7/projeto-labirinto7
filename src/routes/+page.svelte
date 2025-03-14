<script lang="ts">
  import { musicaLigada } from '$lib/musicStore';
  import MusicToggle from '$lib/MusicToggle.svelte';
</script>

<div class="quadro-conteudo">
  <img class="logo" src="/images/logogame.png" alt="logo IPFE" />
  <a class="menu" href="/jogar">JOGAR</a>
  <br />
  <a class="menu" href="/sobre">SOBRE</a>
</div>

<!-- Componente de Música -->
<MusicToggle />
