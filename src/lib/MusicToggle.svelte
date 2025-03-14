<script lang="ts">
  import { onMount } from 'svelte';
  import { page } from '$app/state';  // Usando o store page para detectar a URL

  let musicaLigada = true; // Estado inicial da música (agora começa ligada/tocando)
  let musica: HTMLAudioElement;

  // Mapeamento de músicas por rota
  const musicasPorPagina = {
    '/': '/audio/musicgame.mp3',  // Música para a página principal
    '/jogar': '/audio/suspense.mp3',  // Música para a página do jogo
    '/sobre': '/audio/aventura.mp3',  // Música para a página "sobre"
    '/ia': '/audio/esporte1.mp3',   // Música para a página "IA"
  } as const; // Garante que as chaves são literais fixas

  // Tipo para garantir que a chave seja uma das rotas do objeto
  type CaminhoPagina = keyof typeof musicasPorPagina;

  // Reatividade para atualizar a música conforme a URL da página
  $: musicaAtual = musicasPorPagina[page.url.pathname as CaminhoPagina] || '/audio/musicdefault.mp3';

  // Alterna o estado da música
  function alternarMusica(): void {
    musicaLigada = !musicaLigada;
    // Salva o estado da música no localStorage
    localStorage.setItem('musicaLigada', JSON.stringify(musicaLigada));
  }

  // Controla a música com base no estado de musicaLigada
  $: if (musica) {
    if (musicaLigada) {
      musica.muted = false;
      musica.play();
    } else {
      musica.muted = true;
      musica.pause();
    }
  }

  // Garante que a música comece no estado correto quando o componente for montado
  onMount(() => {
    // Recupera o estado salvo da música do localStorage (se existir)
    const estadoSalvo = localStorage.getItem('musicaLigada');
    if (estadoSalvo) {
      musicaLigada = JSON.parse(estadoSalvo); // Restaura o estado da música
    }

    // Define a música para o estado inicial (mutado ou tocando)
    if (musica) {
      if (musicaLigada) {
        musica.muted = false;
        musica.play();
      } else {
        musica.muted = true;
        musica.pause();
      }
    }
  });
</script>

<!-- Botão para alternar a música -->
<div class="configuracoes-som">
  <button class="botao-som musica" on:click={alternarMusica}>
    <img src="/images/nota.jpeg" alt="musica" class="{musicaLigada ? 'ativo' : 'desligado'}" />
  </button>
</div>

<!-- Elemento de áudio -->
<audio bind:this={musica} loop>
  <source src={musicaAtual} type="audio/mp3" />
  Seu navegador não suporta o elemento de áudio.
</audio>
