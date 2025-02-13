<script lang="ts">
  import { goto } from '$app/navigation';
  import { onDestroy } from 'svelte';  // Importando para limpar o intervalo quando o componente for destruído


  class Coordenada {
      linha: number;
      coluna: number;

      constructor (linha:number=0, coluna:number=0){
          this.linha = linha;
          this.coluna = coluna;
      }
  }

  class EstadoJogo {
      posicaoPersonagem: Coordenada;
      posicaoObjetivo: Coordenada;
      mapa: number[][];
      fase: number;
      tempoRestante: number;
      tempoAcabou: boolean;

      constructor (posicaoPersonagem: Coordenada, posicaoObjetivo: Coordenada, mapa: number[][], fase: number, tempoRestante: number, tempoAcabou: boolean) {
          this.posicaoPersonagem = posicaoPersonagem;
          this.posicaoObjetivo = posicaoObjetivo;
          this.mapa = mapa;
          this.fase = fase;
          this.tempoRestante = tempoRestante;
          this.tempoAcabou = tempoAcabou;
      }
  }

  // Função para gerar um labirinto aleatório utilizando o algoritmo de backtracking
  function gerarLabirinto(linhas: number, colunas: number): number[][] {
      const labirinto: number[][] = Array.from({ length: linhas }, () => Array(colunas).fill(1));  // Preenche com paredes

      const pilha: Coordenada[] = [];
      const direcoes = [
          [-1, 0], // cima
          [1, 0],  // baixo
          [0, -1], // esquerda
          [0, 1]   // direita
      ];

      // Função para verificar se a célula está dentro dos limites
      function dentroDoLimite(linha: number, coluna: number): boolean {
          return linha >= 0 && coluna >= 0 && linha < linhas && coluna < colunas;
      }

      // Função de backtracking para gerar o labirinto
      function gerar(x: number, y: number) {
          labirinto[x][y] = 0;  // Marca o caminho como livre
          pilha.push(new Coordenada(x, y));

          while (pilha.length > 0) {
              const pos = pilha[pilha.length - 1];
              const direcoesDisponiveis = [];

              // Verifica as direções possíveis
              for (let [dx, dy] of direcoes) {
                  const nx = pos.linha + dx * 2;
                  const ny = pos.coluna + dy * 2;
                  if (dentroDoLimite(nx, ny) && labirinto[nx][ny] === 1) {
                      direcoesDisponiveis.push([dx, dy]);
                  }
              }

              if (direcoesDisponiveis.length === 0) {
                  pilha.pop();  // Se não houver direções válidas, volta
              } else {
                  const [dx, dy] = direcoesDisponiveis[Math.floor(Math.random() * direcoesDisponiveis.length)];
                  const nx = pos.linha + dx * 2;
                  const ny = pos.coluna + dy * 2;
                  labirinto[nx][ny] = 0;
                  labirinto[pos.linha + dx][pos.coluna + dy] = 0;
                  pilha.push(new Coordenada(nx, ny));
              }
          }
      }

      // Começa a geração do labirinto na posição (1, 1)
      gerar(1, 1);
      
      // Define a entrada e a saída
      labirinto[0][1] = 0; // Entrada
      labirinto[linhas - 1][colunas - 2] = 0; // Saída

      return labirinto;
  }

  // Função para inicializar o jogo
  function inicializarJogo(fase: number): EstadoJogo {
      let personagem: Coordenada = new Coordenada(0, 1);  // Começa na entrada
      let objetivo: Coordenada = new Coordenada();
      let mapa: number[][] = gerarLabirinto(10, 10);  // Gera um labirinto 10x10
      let tempoFase: number = 20;

      objetivo.linha = 9;
      objetivo.coluna = 8;  // Posição da saída

      let estado: EstadoJogo = new EstadoJogo(personagem, objetivo, mapa, fase, tempoFase, false);
      return estado;
  }

  function houveColisao(posicao: Coordenada, jogo: EstadoJogo): boolean {
      return (posicao.linha < 0 || posicao.coluna < 0)
          || (posicao.linha >= jogo.mapa.length || posicao.coluna >= jogo.mapa[0].length)
          || jogo.mapa[posicao.linha][posicao.coluna] == 1;
  }

  function onKeyDown(evento: { keyCode: any; }): void {
      if (jogo.tempoAcabou) return; // Impede movimentação se o tempo acabou

      let novaPosicao = new Coordenada(jogo.posicaoPersonagem.linha, jogo.posicaoPersonagem.coluna);

      switch (evento.keyCode) {
          case 38: // cima
              novaPosicao.linha--;
              break;
          case 40: // baixo
              novaPosicao.linha++;
              break;
          case 37: // esquerda
              novaPosicao.coluna--;
              break;
          case 39: // direita
              novaPosicao.coluna++;
              break;
      }

      if (novaPosicao.linha == jogo.posicaoObjetivo.linha && novaPosicao.coluna == jogo.posicaoObjetivo.coluna) {
          alert("Você completou o labirinto!");
          limparTempo();
          goto("/"); // Redireciona para a página inicial
      }

      if (!houveColisao(novaPosicao, jogo)) {
          jogo.posicaoPersonagem = novaPosicao;
      }
  }

  function diminuirTempo() {
      if (jogo.tempoRestante > 0) {
          jogo.tempoRestante--;
      } else if (!jogo.tempoAcabou) {
          jogo.tempoAcabou = true;
          alert("Tempo esgotado! Você perdeu a partida.");
          limparTempo();
          goto("/"); // Redireciona para a página inicial
      }
  }

  function limparTempo() {
      clearInterval(tempoInterval);
  }

  let jogo: EstadoJogo = inicializarJogo(1);

  // Inicia o contador de tempo a cada 1000ms (1 segundo)
  const tempoInterval = setInterval(diminuirTempo, 1000);

</script>

<h1>Escape the Maze</h1>

<p class="tempo-restante">Tempo restante: {jogo.tempoRestante} segundos</p>

<table>
  {#each jogo.mapa as linha, i}
      <!-- svelte-ignore node_invalid_placement_ssr -->
      <tr>
          {#each linha as celula, j}
              <td class="celula {i === jogo.posicaoPersonagem.linha && j === jogo.posicaoPersonagem.coluna ? 'personagem' : 
                                i === jogo.posicaoObjetivo.linha && j === jogo.posicaoObjetivo.coluna ? 'objetivo' : 
                                jogo.mapa[i][j] === 0 ? '' : 'bloco'}">
                  {#if i === jogo.posicaoPersonagem.linha && j === jogo.posicaoPersonagem.coluna}
                      <img src="/images/personagem.gif" alt="Personagem" class="personagem-gif" />
                  {/if}
              </td>
          {/each}
      </tr>
  {/each}  
</table>

<br />

<a class="menu" href="/" on:click|preventDefault={() => { limparTempo(); goto("/"); }}>VOLTAR AO MENU</a>

<svelte:window on:keydown|preventDefault={onKeyDown} />
