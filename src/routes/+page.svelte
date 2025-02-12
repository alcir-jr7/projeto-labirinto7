<script lang="ts">
    import { onMount } from 'svelte';
  
    // Estado dos botões
    let musicaLigada: boolean = true;
    let efeitosLigados: boolean = true;
  
    // Referência ao elemento de música
    let musica: HTMLAudioElement;
  
    // Alternar música
    function alternarMusica(): void {
      musicaLigada = !musicaLigada;
      if (musicaLigada) {
        musica.muted = false;
        musica.play();
      } else {
        musica.pause();
        musica.muted = true;
      }
    }
  
    // Alternar efeitos sonoros (lógica pode ser adicionada futuramente)
    function alternarEfeitos(): void {
      efeitosLigados = !efeitosLigados;
    }
  
    // Quando o componente monta
    onMount(() => {
      musica = document.getElementById('musica') as HTMLAudioElement;
      musica.muted = true; // Inicia mudo para evitar bloqueio do navegador
    });
  </script>
  
  <!-- Conteúdo da página -->
  <div class="quadro-conteudo">
    <img class="logo" src="/images/logogame.png" alt="logo IPFE" />
    <a class="menu" href="/jogar">JOGAR</a>
    <br />
    <a class="menu" href="/sobre">SOBRE</a>
  </div>
  
  <!-- Botões de som -->
  <div class="configuracoes-som">
    <button class="botao-som efeitos" on:click={alternarEfeitos}>
      <img src="/images/efeitos.jpeg" alt="efeitos" class="{efeitosLigados ? 'ativo' : 'desligado'}" />
    </button>
    <button class="botao-som musica" on:click={alternarMusica}>
      <img src="/images/nota.jpeg" alt="musica" class="{musicaLigada ? 'ativo' : 'desligado'}" />
    </button>
  </div>
  
  <!-- Elemento de áudio -->
  <audio id="musica" autoplay loop muted>
    <source src="/audio/esporte1.mp3" type="audio/mp3">
    Seu navegador não suporta o elemento de áudio.
  </audio>