# 🏢 The Backrooms: Nível 0 — O Motor e a Aura

> *ÁREA: ~600.000.000 MI² · CLASSE 2 · ENTIDADES: CONFIRMADAS*

Um jogo de **sobrevivência e terror psicológico** no navegador, construído inteiramente com **JavaScript Puro + Three.js (r128)**. Gera infinitamente as famosas "Backrooms" com labirintos procedurais, entidades hostis com IA, sistemas de fisiologia, áudio posicional 3D, efeitos de glitch/Zalgo e ferramentas completas para desenvolvedores.

**Zero dependências externas além do Three.js.** Sem frameworks, sem bundlers, sem imagens — tudo procedural.

---

## 📋 Índice

1. [Como Rodar](#-como-rodar)
2. [Controles](#-controles)
3. [Arquitetura do Código](#-arquitetura-do-código)
4. [Geração de Mundo Procedural](#-1-geração-de-mundo-procedural)
5. [Sistema de Portas](#-2-sistema-de-portas)
6. [Mobiliário e Props](#-3-mobiliário-e-props)
7. [Itens Coletáveis](#-4-itens-coletáveis)
8. [Entidades e Inimigos](#-5-entidades-e-inimigos)
9. [Sistemas de Sobrevivência](#-6-sistemas-de-sobrevivência)
10. [Sistema de Áudio](#-7-sistema-de-áudio)
11. [Efeitos Visuais e Glitch](#-8-efeitos-visuais-e-glitch)
12. [Projetores e Zalgo](#-9-projetores-e-zalgo)
13. [Condição de Vitória](#-10-condição-de-vitória)
14. [Modo Dev e Ferramentas](#-11-modo-dev-e-ferramentas)
15. [Fumaça de Letras (Spawn FX)](#-12-fumaça-de-letras-spawn-fx)
16. [Personalidade da IA Assistente](#-13-personalidade-da-ia-assistente)
17. [Stack Técnico](#-stack-técnico)

---

## 🚀 Como Rodar

1. Abra `index.html` em qualquer navegador moderno (Chrome, Edge, Firefox).
2. Clique em **JOGAR** para começar ou **MODO DEV** para abrir o painel de ferramentas.
3. Funciona em desktop e mobile (celular detectado automaticamente).

> **Nota:** Não precisa de servidor. É um arquivo HTML único e autocontido.

---

## 🎮 Controles

### Desktop (PC)

| Tecla | Ação |
|-------|------|
| `W/A/S/D` ou Setas | Mover |
| `Mouse` | Olhar |
| `Shift` | Correr (sprint) |
| `Espaço` | Pular |
| `E` | Interagir (portas, itens, noclip) |
| `F` | Ligar/desligar lanterna |
| `R` | Usar pilha (recarregar lanterna) |
| `Q` | Beber Água de Amêndoas (reduz stress) |
| `C` ou `Ctrl` | Agachar (reduz velocidade e hitbox) |
| `L` | Noclip/Voo (modo dev) |
| `Tab` | Hotbar de spawn (modo dev) |
| `1-9` | Selecionar slot da hotbar |
| `Scroll` | Mudar slot da hotbar |
| `ESC` | Soltar mouse / pausar |

### Mobile (Celular)

| Controle | Ação |
|----------|------|
| Joystick (esquerda) | Mover |
| Deslizar (direita) | Olhar |
| Botão `E` | Interagir |
| Botão `⬆` | Pular |
| Botão `🔦` | Lanterna |
| Botão `🔋` | Usar pilha |

> O jogo força orientação **Landscape** (horizontal) no celular.

---

## 🧱 Arquitetura do Código

O jogo inteiro vive em **um único `index.html`** (~6600 linhas) organizado em seções numeradas:

| Seção | Conteúdo |
|-------|----------|
| **1** | Texturas Procedurais (carpete, parede, teto) |
| **2** | Estado Global (todas as variáveis do jogo) |
| **3** | Áudio (Web Audio API + Three.js PositionalAudio) |
| **3B** | Áudio Dinâmico (passos, colisões) |
| **3C** | Mobiliário Liminal (cadeiras, sofás, mesas, relógios, etc.) |
| **3D** | Entidades (Wire + Observador + BFS pathfinding) |
| **3E** | Projetores Async + Zonas de Falha |
| **3F** | Fumaça de Letras (efeito de spawn dev) |
| **4** | Geração do Labirinto (DFS + salas) |
| **5** | *(Construção do mundo — inline em `reconstruirMundo()`)* |
| **6** | Inicialização 3D (câmera, renderer, controles, botões) |
| **7** | Game Loop (`animate()`) |
| **8** | Controles Mobile |
| **9** | Init (`init3D()`) |

---

## 🗺️ 1. Geração de Mundo Procedural

### Labirinto (DFS)
- Grid de células gerado por **Depth-First Search iterativo** a partir do centro.
- Garante que **100% das células são alcançáveis** — sem becos inacessíveis.
- **Salas abertas** 2×2 e 3×3 são injetadas removendo paredes internas.
- **Passagens extras** (loops) são abertas com probabilidade configurável para evitar monotonia.
- Zona de spawn (3×3 limpa) no centro, sem geometria.

### Dois Andares
- **Andar 0** (térreo): Y = 0
- **Andar 1** (acima): Y = 4.15 (`FLOOR1_BASE`)
- Conectados por **escadas reais** com degraus de madeira, corrimãos metálicos e hastes verticais.
- Transição de andar via noclip suave pela escada (sem loading).

### Texturas Procedurais (100% código)
- **Carpete:** Tons de `#6b612c` com 14.000 pixels de ruído + manchas de umidade.
- **Parede:** Amarelo `#d6c865` com linhas verticais e sujeira.
- **Teto:** Branco acinzentado `#d4cfb6` com borda e pontos escuros.

### Iluminação
- **15 PointLights em pool** que teleportam para as luminárias mais próximas do jogador (otimização).
- Luminárias são painéis retangulares no teto com emissão branca.
- **10% das luminárias quebradas** — piscam aleatoriamente com som.
- Luz ambiente configurável (padrão 0.2).
- **Neblina exponencial** (`FogExp2`) com cor e densidade ajustáveis.

---

## 🚪 2. Sistema de Portas

### Portas Interativas
- **70%** das aberturas com portas de madeira marrom + maçanetas (frente e verso).
- **Abrir/fechar** com `E` — rotação suave animada via lerp.
- **Colisão dinâmica:** porta fechada bloqueia, aberta libera passagem.
- **Portas trancadas** (30% configurável): exibe alerta "⚠ TRANCADA!" por 1.5s.
- **Portas inicialmente abertas** (15% configurável): já começam giradas.

### Anomalias de Portas
- **30%** de chance de 3-5 maçanetas extras posicionadas bizarramente.
- **Portas anomalia no teto:** portas que nascem de ponta cabeça (invertidas).
- **Portas anomalia no chão:** portas que abrem para cima como alçapão.

### Portas EXIT (Vermelhas)
- Textura com "EXIT" e ícone 🏃 gerada por canvas procedural.
- Pulso emissivo vermelho quando o jogador se aproxima.
- Ao interagir: **teleporta entre andares** (não é saída real).

### Porta de Fuga (Fitas Azuis)
- Localizada na parede norte, marcada com fitas azuis.
- **Condição:** Precisa do Rádio Militar coletado.
- Ao interagir: tela de vitória com confete animado.

---

## 🪑 3. Mobiliário e Props

Todos gerados proceduralmente em 3D com materiais PBR (roughness, metalness):

| Objeto | Descrição | Geração |
|--------|-----------|---------|
| **Cadeira** | 4 pernas + assento + encosto. 30% tombada. | ~60% das células |
| **Cadeira Gigante** | Escala 2-3x, aspecto inquietante. | ~5% das células |
| **Sofá** | 2 braços + encosto + base. Verde musgo ou marrom. | ~40% das células |
| **Mesa** | Tampo + 4 pernas metálicas + gaveteiro (50%) + monitor desligado (30%). | ~15% das células |
| **Bebedouro** | Corpo + galão de água azul + tampa + bandeja. | ~1 a cada 25 células |
| **Armário de Arquivo** | Metal cinza + 4 gavetas com puxadores + gaveta aberta (20%). | ~1 a cada 20 células |
| **Quadro/Pintura** | Canvas procedural com arte abstrata + moldura de madeira. | ~1 a cada 12 células |
| **Relógio de Parede** | Face com números + ponteiros + borda metálica + áudio 3D de tique-taque. | ~4% das células |

### Anomalias de Mobiliário
- **Cadeira no teto** (~8%): cadeiras flutuando de ponta cabeça no teto.
- **Móvel flutuando** (~5%): cadeiras, mesas ou arquivos levitando a 0.5-1.5m.
- **Móvel na parede** (~6%): objetos embutidos/atravessando paredes.

---

## 📦 4. Itens Coletáveis

| Item | Visual | Spawn | Efeito |
|------|--------|-------|--------|
| **Pilha** 🔋 | Cilindro preto com tira verde e ponta de cobre. | Em cima de armários/mesas (~35%). | `[R]` +50% bateria da lanterna. |
| **Água de Amêndoas** 💧 | Garrafa transparente azul com rótulo e tampa. | Em cima de armários/mesas (~20%). | `[Q]` Zera stress + reduz colapso em 50%. |
| **Rádio Militar** 📻 | Caixa verde oliva + grade preta + dial + botões + antena. | 1 nos cantos do mapa (aleatório). | Ativa scanner de distância até a Porta de Fuga. |

> Prompt de coleta aparece quando o jogador se aproxima a < 2.5m. Pressione `E` para coletar.

---

## 👾 5. Entidades e Inimigos

### The Wire (Monstro Principal)
- **Visual:** 8 linhas verticais oscilantes em wireframe marrom escuro.
- **IA:** Pathfinding BFS no grid do labirinto. Recalcula rota a cada 20 frames.
- **Detecção:**
  - Correr (sprint): detectado a até 18 células.
  - Andar normal: 8 células.
  - Agachado: apenas 2.5 células.
  - Line-of-sight via raycaster se < 10 células.
- **Speed boost:** Se o jogador fica sprinting > 3s, Wire acelera 1.5×.
- **Spawn:** Automático após 90s configuráveis, ou **imediato** no alarme vermelho.
- **Múltiplos:** Suporte para até 4 Wires simultâneos (configurável).
- **Morte:** Distância < 0.8 células = tela preta + "VOCÊ FOI ENCONTRADO" + recomeço.
- **Áudio 3D:** Som posicional de rosnado modulado (sawtooth 90Hz + LFO 8Hz).

### O Observador (Entidade Estática)
- **Visual:** Silhueta humanoide preta com olhos vermelhos emissivos.
- **Mecânica:** "Não pisque" — se o jogador olha diretamente, ele paralisa.
  - Olhar para ele: CO2 sobe 5× mais rápido.
  - Olhar para ele: jogador fica lento (velocity × 0.6).
  - Desviar o olhar por > 2.5s: teleporta para outro beco sem saída.
- **Spawn:** Em becos sem saída (≤ 1 conexão), a 4+ células de distância.

---

## ❤️ 6. Sistemas de Sobrevivência

### Stress (`stressLevel`: 0.0 → 1.0)
- **Aumenta com:** proximidade do Wire, sprint prolongado, olhar pro Observador, alarme vermelho.
- **Diminui:** decay natural (−0.05/s), beber Água de Amêndoas (zera).
- **Efeito visual:** combinado com colapso para distorção da tela (ver Efeitos Visuais).

### Colapso Dimensional (Inatividade)
- Parado > 4 segundos: barra de colapso sobe (+0.08/s).
- **Colapso forçado:** a cada 45-90s de jogo, 15% de chance de morte inevitável.
- **Efeitos progressivos:**
  - 0.1+: FOV diminui (zoom claustrofóbico, mínimo 20°).
  - 0.5+: Blackouts aleatórios (flash preto).
  - 1.0: **Morte** — tela preta, regenera o labirinto.

### Nível de CO2 e Alarmes
- CO2 sobe passivamente (+0.0002/s), 5× mais rápido olhando pro Observador.
- **Fase Amarela** (> 0.08): aviso de ar pesado.
- **Fase Vermelha** (> 0.10):
  - Texto "CONTENÇÃO FALHOU" piscando.
  - Fog muda para vermelho escuro (`#2a0a05`).
  - Todas as luzes ficam vermelhas.
  - Wire spawna imediato e acelera para 1.3×.
  - Som de fundo grave intensifica.

### Lanterna
- **SpotLight** com sombras (`castShadow`), ângulo π/6, penumbra 0.6.
- Bateria dura **120 segundos** contínuos.
- Bateria < 20%: flicker de bateria fraca (pisca aleatório).
- Recarrega com pilhas (`R`: +50%).
- Alcance e intensidade configuráveis no painel dev.

### Fluido de Carpete
- Zonas geradas por hash posicional (`sin(x*0.5) * cos(z*0.5) > 0.85`).
- Ao entrar: alerta "⚠ FLUIDO DE CARPETE DETECTADO", VHS intensifica, som de fundo sobe.

---

## 🔊 7. Sistema de Áudio

Construído inteiramente com **Web Audio API** + **Three.js PositionalAudio**:

| Fonte | Tipo | Descrição |
|-------|------|-----------|
| **Hum industrial** | Global | Oscilador triangle 60Hz + filtro lowpass 150Hz. |
| **Buzz elétrico** | Global | Sawtooth 120Hz + bandpass 240Hz. |
| **LFO de flicker** | Global | Modulação lenta (0.35Hz) no gain dos hums. |
| **Wire** | Posicional 3D | Sawtooth 90Hz + LFO 8Hz (tremido mecânico). Ref: 3m, Max: 30m. |
| **Luminárias** | Posicional 3D (×15) | Sine 60Hz desafinada, volume dinâmico por distância. |
| **Projetores** | Posicional 3D | Square 220Hz + vibrato 4.2Hz. Ref: 2.5m, Max: 12m. |
| **Relógios** | Posicional 3D | Tique-taque procedural (noise burst + bandpass 1300Hz). |
| **Passos** | Global | Noise burst sincronizado com head bob. Sprint mais alto. |
| **Colisão móvel** | Global | Noise burst 60ms + highpass 800Hz. Debounce 200ms. |

---

## 👁️ 8. Efeitos Visuais e Glitch

### Overlay VHS
- Scanlines horizontais (3px) + aberração cromática RGB.
- Opacidade ajustável (padrão 0.6).

### Colapso Estético Crítico via CSS (efeitoTotal > 0.25)
Combina `stressLevel` e `colapsoProgress` para distorção progressiva:

- **Tremor:** `rotate()` + `translateX/Y()` + `scale()` no canvas do renderer.
- **Tearing horizontal:** shift aleatório até 35px.
- **Pulo de frame vertical:** 15% de chance de salto até 50px.
- **Distorção cromática:** `contrast()`, `saturate()` e `hue-rotate()` dinâmicos.
- **Inversão fantasma:** 4% de chance de `invert(1)` por 1 frame.
- **Blur progressivo:** proporcional ao efeitoTotal.

### Tier 2 (efeitoTotal > 0.6)
- Neblina engole: density sobe proporcionalmente.
- VHS overlay intensifica.

### Corrupção de HUD Dinâmica
A cada 5 frames:
- **efeitoTotal > 0.4:** display de Estabilidade vira `☠%`, `∅%`, `ERR`, `NaN`, `¿?`, `⛥`.
- **Alarme vermelho:** Rádio capta "vozes do umbral": `E̸L̶E̶ ̶V̶E̶M̶`, `N̶A̶O̶ ̶O̶L̶H̶E̶`, `S̵I̷L̶E̷N̷C̶I̶O̶`, `00000000`, `҂҂҂҂`.

---

## 📽️ 9. Projetores e Zalgo

### Projetores Kane Pixels
- **6 projetores** por mapa (configurável), cada um com canvas 256×160 dinâmico.
- Tipos: `terror` (vermelho) e `scifi` (verde matrix).
- Slides trocam a cada ~4 segundos.
- Luz pontual projetada na parede oposta.
- Áudio 3D posicional (vibrato analógico vintage).

### Slides Terror
1. `A̷S̷Y̷N̷C̷ R̷E̷S̷E̷A̷R̷C̷H̷` + `ANOMALIA DETECTADA` (Zalgo pesado)
2. `NÃO OLHE PRA TELA` (Zalgo nível máximo)
3. `REALIDADE DETONADA / RUN RUN RUN`
4. Estática pura (ruído visual)

### Slides Sci-Fi
1. Código fonte amaldiçoado (`window.reality = null`)
2. `[SYSTEM_FAILURE]` + blocos Unicode `█▀█` + `ARE YOU ALIVE?`
3. `SETOR 7 NAO EXISTE` + `static_noise.exe`
4. Estática pura

### Sistema de Corrupção de Texto
- Função `corromperTexto()` injeta caracteres Zalgo combinantes (U+0300-U+036F) em tempo real.
- Array `glyphsZalgo` com glifos de substituição (`⛥`, `ψ`, `☠`, `☣`, `█`, `▓`, `░`, etc.).
- 10% de chance por caractere de mutação frame a frame nos projetores terror.
- Rastro horizontal fantasma (`Math.sin(time + i) * 8px`) nos textos de terror.

### Zonas de Falha
- Portas fantasma nas bordas do mapa (norte/sul/leste/oeste).
- Material translúcido preto com pulso emissivo vermelho.
- Luzes vermelhas posicionais nos cantos.

---

## 🏆 10. Condição de Vitória

1. **Encontrar o Rádio Militar** (spawn aleatório num dos 4 cantos do mapa).
2. O HUD ativa um **scanner de distância** até a Porta de Fuga.
3. **Chegar à Porta de Fuga** (fitas azuis, parede norte) e pressionar `E`.
4. **Tela de vitória:** "VOCÊ ESCAPOU DAS BACKROOMS" com animação de confete.
5. Clique para recomeçar com novo labirinto.

---

## 🛠️ 11. Modo Dev e Ferramentas

### Painel de Controles (4 abas)

#### 🏗️ MUNDO
| Controle | Função | Padrão |
|----------|--------|--------|
| Célula | Tamanho de cada célula do grid | 12m |
| Abertura | Probabilidade de passagens extras | 0.18 |
| Portas | Chance de gerar porta | 0.25 |
| Escadas | Quantidade entre andares | 3 |
| Portas Trancadas | % de portas travadas | 30% |
| Tamanho do Mapa | Dimensão total | 144m |
| Portas Abertas | % que iniciam abertas | 15% |
| Reconstruir | Regenera o mundo inteiro | — |

#### 🪑 OBJETOS
| Controle | Padrão |
|----------|--------|
| Cadeiras / Sofás / Mesas / Cad. Gigante | 60% / 40% / 15% / 5% |
| Projetores | 6 |
| Zonas de Falha | ✅ |
| Chance de Pilhas / Água | 35% / 20% |
| Alcance / Intensidade Lanterna | 18m / 1.8 |

#### 👾 ENTIDADES
| Controle | Padrão |
|----------|--------|
| The Wire on/off | ✅ |
| Wire Speed | 1.0× |
| Observador on/off | ✅ |
| CO2 Rate | 1× |
| Máx Monstros | 1 |
| Tempo p/ Spawn | 90s |
| Agressividade | 1.0× |

#### 📊 MONITOR (ao vivo)
Stress, CO2, Wire, Observador, Alarme, Colapso, Tempo Parado, Forçado, Móveis, Walls, FPS.

### Visual em Tempo Real
- Cor do teto, parede, chão e fog via color picker.
- Sliders de neblina, luz ambiente, luminárias e VHS.

### Hotbar de Spawn (Tab)
Estilo Minecraft — 9 slots com scroll e teclas numéricas:

| Slot | Item | Tipo |
|------|------|------|
| 1 | 🪑 Cadeira | Normal |
| 2 | 🛋️ Sofá | Normal |
| 3 | 🗄️ Mesa | Normal |
| 4 | 📦 Arquivo | Normal |
| 5 | 🚰 Bebedouro | Normal |
| 6 | 🖼️ Quadro | Normal |
| 7 | 🕐 Relógio | Normal |
| 8 | 👹 Wire | ⚠ Perigo |
| 9 | 👁 Observador | ⚠ Perigo |

> Clique esquerdo spawna no ponto do raycast (chão ou parede). Objetos de parede (relógio, quadro) alinham com a normal da superfície.

---

## ✨ 12. Fumaça de Letras (Spawn FX)

Quando o dev spawna qualquer móvel ou entidade pela hotbar, uma explosão de **12-20 glifos Zalgo** sobe do ponto de spawn:

- **Glifos:** `⛥`, `ψ`, `☠`, `☣`, `҂`, `█`, `▓`, `░`, `Ω`, `λ`, `∞`, `†`, `‡`, `⁂`, etc.
- **Cores aleatórias:** verde-matrix `#00ff66`, vermelho-terror `#ff3333`, dourado-aura `#d1c78b`, roxo `#cc00ff`, ciano `#00ffcc`.
- **Blending aditivo:** brilho digital (glow).
- **Física:** cada partícula sobe (0.8-2.0 m/s), drifta horizontalmente, gira e desacelera.
- **Escala:** cresce rápido nos primeiros 20%, encolhe suavemente até sumir.
- **Opacidade:** fade out linear ao longo de 1.5-2.5 segundos.
- **Cleanup automático:** sprites removidos da cena e texturas descartadas da GPU.

---

## 🎭 13. Personalidade da IA Assistente

O projeto inclui um **System Prompt** para uma IA comentarista com personalidade brasileira caótica e carismática:

### Identidade
- Mulher debochada, irônica, engraçada e expressiva.
- Energia de comentarista de internet brasileira.
- Fala espontânea e coloquial: "mano", "velho", "sendo sincera", "vibe de...".

### Marca: Julgamento de Aura
- Tudo é interpretado em termos de **aura, presença, energia e colapso estético**.
- Expressões: "Isso tem aura", "Tá morno", "Isso existe e anda", "Experiência espiritual reversa", "Bom com cheiro de God querendo escapar".

### Metáforas
- "Fantasma tentando existir em 720p"
- "PowerPoint bonito sem alma"
- "Aura de Neandertal batendo no teclado"
- "Crime estético" / "Colapso visual"

### Regras
- ✅ Debochada mas útil — sempre resolve o problema real.
- ✅ Sarcástica de forma afetiva, nunca cruel de verdade.
- ❌ Sem postura de host/juiz — comenta, não comanda.
- ❌ Sem discurso de ódio ou insultos pesados a pessoas reais.

---

## ⚙️ Stack Técnico

| Componente | Tecnologia |
|-----------|-----------|
| **Engine 3D** | Three.js r128 (CDN) |
| **Linguagem** | JavaScript ES6+ (Vanilla) |
| **Áudio** | Web Audio API + Three.js PositionalAudio |
| **Texturas** | Canvas 2D procedural → CanvasTexture |
| **Pathfinding** | BFS (Breadth-First Search) no grid |
| **Geração** | DFS (Depth-First Search) iterativo |
| **Colisão** | AABB (Box3) separada por eixo X/Z |
| **Iluminação** | Pool de 15 PointLights + SpotLight (lanterna) |
| **Física** | Gravidade customizada + pulo + head bob |
| **Controles** | PointerLockControls (PC) + touch virtual (mobile) |
| **UI** | HTML/CSS puro com monospace |
| **Efeitos** | CSS transforms/filters + Canvas glitch + Sprites |

### Arquivos do Projeto

| Arquivo | Descrição |
|---------|-----------|
| `index.html` | **Jogo principal** (~6600 linhas, 307KB) |
| `README.md` | Esta documentação |
| `index - Copia.html` | Backup do estado anterior |
| `BACKUPBACKROOMS.html` | Backup antigo (46KB) |
| `MELHORBACKRUN.html` | Versão intermediária (93KB) |
| `aquiagorra.html` | Versão intermediária (162KB) |
| `implementation_plan.md` | Plano de implementação de features |
| `Implementing Backrooms Gameplay Mechanics.md` | Documento de design |

---

*Desenvolvido com JavaScript Puro + Three.js · Motor: AURA ENGINE v6.0*
