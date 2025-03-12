<script lang="ts">
  import { onMount } from 'svelte';

  // Estado do botão de música
  let musicaLigada: boolean = true;

  // Referência ao elemento de música usando bind:this
  let musica: HTMLAudioElement;

  // Alternar música
  function alternarMusica(): void {
    musicaLigada = !musicaLigada;
    if (musicaLigada) {
      musica.muted = false;
      musica.play(); // Tocar música
    } else {
      musica.muted = true;
      musica.pause(); // Pausar música
    }
  }

  // Quando o componente monta, o áudio é configurado
  onMount(() => {
    if (musicaLigada) {
      musica.muted = false;
      musica.play(); // Toca a música
    } else {
      musica.muted = true; // Começa mutado
    }
  });
</script>

<!-- Conteúdo da página -->
<div class="quadro-conteudo">
  <img class="logo" src="/images/logogame.png" alt="logo IPFE" />
  <a class="menu" href="/jogar">JOGAR</a>
  <br />
  <a class="menu" href="/sobre">SOBRE</a>
</div>

<!-- Botão de música -->
<div class="configuracoes-som">
  <button class="botao-som musica" on:click={alternarMusica}>
    <img src="/images/nota.jpeg" alt="musica" class="{musicaLigada ? 'ativo' : 'desligado'}" />
  </button>
</div>

<!-- Elemento de áudio com bind:this -->
<audio bind:this={musica} autoplay loop muted>
  <source src="/audio/musicgame.mp3" type="audio/mp3">
  Seu navegador não suporta o elemento de áudio.
</audio>
