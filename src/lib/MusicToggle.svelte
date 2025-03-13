<script lang="ts">
    import { onMount } from 'svelte';
  
    // Estado da música (reativo)
    let musicaLigada = false; // Agora começa mutada
    let musica: HTMLAudioElement;
  
    // Alternar música reativamente
    function alternarMusica(): void {
      musicaLigada = !musicaLigada;
    }
  
    // Atualiza o áudio sempre que musicaLigada mudar
    $: if (musica) {
      if (musicaLigada) {
        musica.muted = false;
        musica.play();
      } else {
        musica.muted = true;
        musica.pause();
      }
    }
  
    // Quando o componente monta, garante que o áudio comece mutado
    onMount(() => {
      if (musica) {
        musica.muted = true;
        musica.pause();
      }
    });
  </script>
  
  <!-- Botão de música -->
  <div class="configuracoes-som">
    <button class="botao-som musica" on:click={alternarMusica}>
      <img src="/images/nota.jpeg" alt="musica" class="{musicaLigada ? 'ativo' : 'desligado'}" />
    </button>
  </div>
  
  <!-- Elemento de áudio vinculado -->
  <audio bind:this={musica} loop>
    <source src="/audio/musicgame.mp3" type="audio/mp3" />
    Seu navegador não suporta o elemento de áudio.
  </audio>
  