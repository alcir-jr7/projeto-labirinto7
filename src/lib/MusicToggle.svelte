<script lang="ts">
  import { musicaLigada } from '$lib/musicStore';
  import { onMount } from 'svelte';

  let audio: HTMLAudioElement;

  // Função para alternar o estado da música
  function alternarMusica() {
    musicaLigada.update(value => {
      const novoValor = !value;
      if (novoValor) {
        audio.play();
      } else {
        audio.pause();
      }
      return novoValor;
    });
  }

  onMount(() => {
    // Verifica o estado inicial da música
    $musicaLigada ? audio.play() : audio.pause();
  });
</script>

<div class="configuracoes-som">
  <button class="botao-som musica" on:click={alternarMusica}>
    <img src="/images/nota.jpeg" alt="musica" class="{$musicaLigada ? 'ativo' : 'desligado'}" />
  </button>

  <audio bind:this={audio} loop>
    <source src="/audio/musicgame.mp3" type="audio/mp3">
    Seu navegador não suporta o elemento de áudio.
  </audio>
</div>