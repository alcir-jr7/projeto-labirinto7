<script lang="ts">
    import { goto } from '$app/navigation';
    import { onMount, onDestroy } from 'svelte';
    import MusicToggle from '$lib/MusicToggle.svelte'; // Importando o componente MusicToggle

    class Coordenada {
        linha: number;
        coluna: number;

        constructor(linha: number = 0, coluna: number = 0) {
            this.linha = linha;
            this.coluna = coluna;
        }
    }

    class Personagem {
        posicao: Coordenada;

        constructor(linha: number = 0, coluna: number = 0) {
            this.posicao = new Coordenada(linha, coluna);
        }

        mover(direcao: string): void {
            switch (direcao) {
                case 'cima':
                    this.posicao.linha--;
                    break;
                case 'baixo':
                    this.posicao.linha++;
                    break;
                case 'esquerda':
                    this.posicao.coluna--;
                    break;
                case 'direita':
                    this.posicao.coluna++;
                    break;
                default:
                    console.error('Direção inválida');
            }
        }
    }

    class EstadoJogo {
        personagem: Personagem;
        posicaoObjetivo: Coordenada;
        mapa: number[][];  
        fase: number;
        tempoRestante: number;
        tempoAcabou: boolean;

        constructor(personagem: Personagem, posicaoObjetivo: Coordenada, mapa: number[][], fase: number, tempoRestante: number, tempoAcabou: boolean) {
            this.personagem = personagem;
            this.posicaoObjetivo = posicaoObjetivo;
            this.mapa = mapa;
            this.fase = fase;
            this.tempoRestante = tempoRestante;
            this.tempoAcabou = tempoAcabou;
        }
    }

    function gerarLabirinto(linhas: number, colunas: number): number[][] {
        const labirinto: number[][] = Array.from({ length: linhas }, () => Array(colunas).fill(1));  
        const pilha: Coordenada[] = [];
        const direcoes = [
            [-1, 0], 
            [1, 0],  
            [0, -1], 
            [0, 1]   
        ];

        function dentroDoLimite(linha: number, coluna: number): boolean {
            return linha >= 0 && coluna >= 0 && linha < linhas && coluna < colunas;
        }

        function gerar(x: number, y: number) {
            labirinto[x][y] = 0;  
            pilha.push(new Coordenada(x, y));

            while (pilha.length > 0) {
                const pos = pilha[pilha.length - 1];
                const direcoesDisponiveis = [];

                for (let [dx, dy] of direcoes) {
                    const nx = pos.linha + dx * 2;
                    const ny = pos.coluna + dy * 2;
                    if (dentroDoLimite(nx, ny) && labirinto[nx][ny] === 1) {
                        direcoesDisponiveis.push([dx, dy]);
                    }
                }

                if (direcoesDisponiveis.length === 0) {
                    pilha.pop();
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

        gerar(1, 1);
        labirinto[0][1] = 0; 
        labirinto[linhas - 1][colunas - 2] = 0; 
        return labirinto;
    }

    function inicializarJogo(fase: number): EstadoJogo {
        let personagem: Personagem = new Personagem(0, 1);  
        let objetivo: Coordenada = new Coordenada();
        let mapa: number[][] = gerarLabirinto(10, 10);  
        let tempoFase: number = 20;

        objetivo.linha = 9;
        objetivo.coluna = 8;  

        if (fase === 2) {
            mapa = gerarLabirinto(12, 12);
            tempoFase = 25;
            objetivo.linha = 11;
            objetivo.coluna = 10;
        } else if (fase === 3) {
            mapa = gerarLabirinto(15, 15);
            tempoFase = 30;
            objetivo.linha = 14;
            objetivo.coluna = 13;
        } else if (fase === 4) {
            mapa = gerarLabirinto(18, 18);
            tempoFase = 35;
            objetivo.linha = 17;
            objetivo.coluna = 16;
        } else if (fase === 5) {
            mapa = gerarLabirinto(20, 20);
            tempoFase = 40;
            objetivo.linha = 19;
            objetivo.coluna = 18;
        }

        let estado: EstadoJogo = new EstadoJogo(personagem, objetivo, mapa, fase, tempoFase, false);
        return estado;
    }

    function houveColisao(posicao: Coordenada, jogo: EstadoJogo): boolean {
        return (posicao.linha < 0 || posicao.coluna < 0)
            || (posicao.linha >= jogo.mapa.length || posicao.coluna >= jogo.mapa[0].length)
            || jogo.mapa[posicao.linha][posicao.coluna] === 1;
    }

    function onKeyDown(evento: { keyCode: any; }): void {
        if (jogo.tempoAcabou) return; 

        let direcao: string | null = null;

        switch (evento.keyCode) {
            case 38: 
                direcao = 'cima';
                break;
            case 40: 
                direcao = 'baixo';
                break;
            case 37: 
                direcao = 'esquerda';
                break;
            case 39: 
                direcao = 'direita';
                break;
        }

        if (direcao) {
            const posicaoAnterior = { ...jogo.personagem.posicao }; 
            jogo.personagem.mover(direcao);
            const novaPosicao = jogo.personagem.posicao;

            if (!houveColisao(novaPosicao, jogo)) {
                jogo.personagem.posicao = novaPosicao;
            } else {
                jogo.personagem.posicao = posicaoAnterior;
            }

            if (novaPosicao.linha === jogo.posicaoObjetivo.linha && novaPosicao.coluna === jogo.posicaoObjetivo.coluna) {
                if (jogo.fase < 5) {
                    alert(`Parabéns! Você completou a fase ${jogo.fase}! Avançando para a próxima...`);
                    jogo = inicializarJogo(jogo.fase + 1); 
                } else {
                    alert("Parabéns! Você completou todas as fases do labirinto!");
                    limparTempo();
                    goto("/"); 
                }
            }
        }
    }

    function diminuirTempo() {
        if (jogo.tempoRestante > 0) {
            jogo.tempoRestante--;
        } else if (!jogo.tempoAcabou) {
            jogo.tempoAcabou = true;
            alert("Tempo esgotado! Você perdeu a partida.");
            limparTempo();
            goto("/"); 
        }
    }

    function limparTempo() {
        clearInterval(tempoInterval);
    }

    let jogo: EstadoJogo = inicializarJogo(1);
    const tempoInterval = setInterval(diminuirTempo, 1000);

    onDestroy(() => {
        clearInterval(tempoInterval);
    });
</script>

<h1>Escape the Maze - Fase {jogo.fase}</h1>

<p class="tempo-restante">Tempo restante: {jogo.tempoRestante} segundos</p>

<table>
    {#each jogo.mapa as linha, i}
        <!-- svelte-ignore node_invalid_placement_ssr -->
        <tr>
            {#each linha as celula, j}
                <td class="celula {i === jogo.personagem.posicao.linha && j === jogo.personagem.posicao.coluna ? 'personagem' : 
                                  i === jogo.posicaoObjetivo.linha && j === jogo.posicaoObjetivo.coluna ? 'objetivo' : 
                                  jogo.mapa[i][j] === 0 ? '' : 'bloco'}">
                    {#if i === jogo.personagem.posicao.linha && j === jogo.personagem.posicao.coluna}
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

<MusicToggle /> 
