# The Backrooms: Nível 0 (O Motor e a Aura)

Um projeto de sobrevivência e terror psicológico no navegador utilizando **Three.js**. Este jogo gera infinitamente as famosas "Backrooms" com sistemas de perseguição, física, interação e ferramentas completas para desenvolvedores.

---

## 🎮 Funcionalidades Principais e Mecânicas

### 1. Geração de Mundo Procedural
* **Labirinto Infinito:** Geração baseada em grid utilizando DFS (Depth-First Search) para criar salas e corredores interconectados de forma aleatória.
* **Dois Andares:** O mapa possui o Andar 0 (térreo) e o Andar 1, conectados por estruturas de **Escadas** (com corrimão e degraus).
* **Texturas Procedurais:** O carpete úmido, o papel de parede amarelo e o teto do escritório são gerados nativamente por código (sem usar imagens externas), incluindo sujeiras e manchas dinâmicas.

### 2. Sistema de Portas Avançado
* **Molduras Realistas:** Toda passagem entre corredores ou salas possui batentes de madeira nas laterais e na parte superior (lintel).
* **Portas Interativas (70%):** Maioria das aberturas contam com portas de madeira marrom escura e maçanetas pretas (frente e verso).
    * **Interação:** Podem ser abertas ou fechadas clicando ou pressionando a tecla `E`.
    * **Colisão Dinâmica:** Se a porta estiver fechada, ela bloqueia o jogador. Ao abrir, o jogador pode atravessar.
* **Portas Trancadas (30%):** Algumas portas interativas estarão corrompidas/trancadas. Tentar abrir exibirá um aviso de "TRANCADA!".
* **Anomalias de Portas:**
    * 30% de chance de uma porta gerar anomalias, como ter 3 a 5 maçanetas extras espalhadas bizarramente pela madeira.
    * 30% de chance de gerar uma porta "invertida" no teto (nascendo de ponta cabeça).
* **Portas EXIT:** Portas vermelhas brilhantes com a palavra "EXIT" e o ícone 🏃. Ao invés de fugir, encostar nela teleporta o jogador de volta ao início do labirinto (Looping).

### 3. Sistema de Móveis e Objetos (Props)
Os objetos possuem sistema dinâmico de altura, permitindo que o jogador suba ou ande por cima das mesas.
* **Mesas de Escritório:** Tampo de madeira clara com 4 pernas metálicas escuras.
* **Cadeiras:** Cadeiras pretas giratórias geradas próximas às mesas.
* **Sofás:** Conjuntos modulares estofados (geralmente gerados em grupos de 3 em salas grandes).
* **Relógios de Parede:** Relógios redondos (borda preta, fundo branco, ponteiros) que surgem aleatoriamente grudados nas paredes.
* **Armários Altos:** Estruturas de metal que servem como bloqueio ou decoração encostadas nas paredes.
* **Gaveteiros:** Pequenas cômodas geradas nas salas.

### 4. Sobrevivência e Fisiologia
* **Sistema de Stress:** Correr, tomar dano ou ver anomalias aumenta o seu stress. Quanto maior o stress, mais a câmera treme (efeito de respiração pesada).
* **Nível de CO2 e Alarmes:** O jogador consome oxigênio.
    * **Amarelo (> 0.08):** Aviso de que o ar está pesado.
    * **Vermelho (> 0.10):** Alarme dispara, texto pisca e força o monstro principal a "spawnar" imediatamente e te caçar mais rápido.
* **Barra de Colapso Mental:** Sofrer ataques diretos das entidades preenche essa barra. Ao chegar a 100%, o jogador "morre" e a tela fica vermelha (Morte Certa), reiniciando a simulação.

### 5. Entidades e Inimigos
* **A Entidade (Wireframe Monster):** Um monstro humanóide feito de linhas distorcidas (wireframe).
    * Usa algoritmo avançado de Pathfinding (Busca em Largura - BFS) para caçar o jogador pelo labirinto, contornando paredes e abrindo caminho.
    * Spawna naturalmente após 1.5 ~ 3 minutos de jogo ou imediatamente se o Alarme Vermelho de CO2 for ativado.
* **O Observador:** Uma silhueta obscura estática.
    * Funciona na mecânica de "não pisque". Se você olhar para ele, ele paralisa, mas o seu nível de CO2 sobe 5x mais rápido de pavor.
    * Se você desviar o olhar por mais de 2.5 segundos, ele some e se teleporta para outro lugar, continuando a te perseguir.

### 6. Controles e Imersão
* **Controles PC:** `WASD` ou Setinhas para andar. Mouse para olhar. `Shift` para correr. `Espaço` para pular. `E` para interagir.
* **Controles Mobile (Celular):** 
    * Joystick virtual no lado esquerdo para locomoção.
    * Deslizar no lado direito da tela para olhar em volta.
    * Botões virtuais interativos para `Pulo` (⬆) e `Ação` (E).
    * Tela bloqueia e exige virar o celular deitada (Landscape) caso esteja em pé.
* **Imersão Visual:** Filtro de estática VHS por cima da tela, neblina escura que esconde os cantos, e lanternas de teto que piscam aleatoriamente.

### 7. Ferramentas de Desenvolvedor (Modo Dev)
Acessível através do botão "Testar" no Menu Principal.
* **Painel de Modificações ao vivo:** Altere a cor do teto, chão, paredes, cor da neblina e intensidade das luzes deslizando barras.
* **Painel de Monitores:** Uma dashboard que exibe em tempo real seus Frames (FPS), Stress, CO2, Entidades ativadas, contagem de móveis e de colisões da Engine.
* **Modo Voo (No-Clip):** Pressione `L` para voar através das paredes e visualizar o labirinto de fora.
* **Hotbar Mágica (Tecla TAB):** Inspirada em Minecraft, aperte TAB no PC para abrir uma barra de inventário flutuante. Clique nos botões para invocar (spawnar) diretamente na frente do jogador:
    * Cadeiras, Mesas, Sofás.
    * A Entidade (Ent) ou o Observador (Obs).
    * Poções de cura que resetam a barra de Stress ou CO2.

*Desenvolvido em Javascript Puro (Vanilla) + Three.js*
