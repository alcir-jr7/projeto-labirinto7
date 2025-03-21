<script lang="ts">
    import { musicaLigada } from '$lib/musicStore';
    import MusicToggle from '$lib/MusicToggle.svelte';
  </script>
  
  <h1 class="">Como Jogar Escape the Maze</h1>
  <div id="menu-game" class="quadro-ia">
    <p>O jogo possui um total de 5 fases que são geradas aleatóriamente, e cada fase tem um tempo determinado para você escapar. Encontre a saída antes que o tempo acabe.</p>
    <p>Use ⬅️⬆️⬇️➡️ no teclado para mover o personagem pelo labirinto.</p>
    <p>Boa sorte!</p>
  </div>
  <br>
  <br>
  <a class="menu" href="/jogar">CLIQUE PARA CONTINUAR</a>
    <br>
  <a class="menu" href="/">VOLTAR AO MENU</a>

<!-- Componente de Música -->
<MusicToggle />
  