Quero refazer o nosso vídeo sobre frutas num novo estilo visual: animação em **papel recortado** (colagem), desenhada quadro a quadro em JavaScript/Canvas 2D. O roteiro, a narração e a duração continuam iguais. Só o visual muda.

## 1. Antes de tudo, entenda o vídeo atual
- Ache no projeto o vídeo de frutas: roteiro, texto da narração, lista de cenas, duração de cada cena, resolução/proporção, fps e como ele é renderizado hoje.
- Monte uma tabela de cenas (início, fim, o que é dito, o que aparece na tela) e me mostre antes de desenhar.
- Não quebre o pipeline atual (pixel art). Crie o novo renderizador ao lado dele, por exemplo numa pasta `papel/`.

## 2. Implementação de referência (siga este estilo)
Existe uma cena de teste pronta que eu aprovei: https://raw.githubusercontent.com/lmoraes9/stickopedia-uploader/claude/level-animation-a6olj3/animacao/urso-laboratorio.html
(repo público `lmoraes9/stickopedia-uploader`, arquivo `animacao/urso-laboratorio.html`). Leia o arquivo inteiro e reaproveite o kit de desenho dele em vez de reinventar:
- `paper(pts, cor, opções)`: cada forma é um pedaço de papel. Primeiro vem uma borda clara rasgada (a forma um pouco maior, com jitter), com sombra suave. Por cima vai a cor, com um jitter leve.
- **Boil de stop-motion**: o jitter das bordas troca de semente cerca de 7 vezes por segundo (`BOIL`). Cenário parado usa `still: true`.
- Geradores de forma: `ell`, `rr` (retângulo arredondado), `cap` (cápsula, para braços e caules), `poly`, `starPts`.
- Textura de papel: ruído 256×256 com fibras, aplicado com `multiply` por cima do quadro inteiro, mais uma vinheta radial leve.
- Letras manuscritas com a fonte **Caveat** (Google Fonts) em azul caneta `#2E5E9E`. O texto é "escrito" revelando um clip da esquerda para a direita.
- Animação por timeline: `key(t, [[t0, v0], [t1, v1, 'easing']])` com easings `io`, `o`, `i` e `back` (overshoot). Tudo sai de uma função pura `draw(t)`, sem estado, para o render ser determinístico.
- Câmera: zoom e pan por keyframes, tremor que decai nos impactos, e transição de "folha de caderno" passando pela tela entre cenas.
- Personagens: formas simples com rosto (olhos ovais com brilho branco, piscadas, bochechas rosadas, boca que muda: ω, "o", sorriso aberto). Expressões e poses são keyframes.
- Coordenadas lógicas fixas (a referência usa 1000×1000) escaladas para o tamanho de saída. Aqui, deixe `W` e `H` configuráveis para a proporção do nosso vídeo.

## 3. Direção para as frutas
- Cada fruta é um personagem de papel com rosto e personalidade. Use camadas: casca, polpa, sementes, folhinha com a borda rasgada.
- Aproveite o que só fruta tem: corte ao meio mostrando o interior (gomos da laranja, sementes da melancia/kiwi/maçã), casca da banana abrindo, cachos de uva.
- Fatos e números da narração aparecem em tiras de papel ou fichas de caderno pautado, presas com fita adesiva e escritas à mão.
- Fundos lisos por cena com cores fortes e diferentes (amarelo, rosa, verde-água, azul-noite), como no vídeo de referência do Kevin Ngo. O movimento nasce de "pops" com overshoot, não de fades.

## 4. Render e entrega
- Renderize quadro a quadro com Playwright (Chromium) chamando `draw(t)` para cada frame. Depois junte com ffmpeg (H.264, yuv420p, `+faststart`) e faça o mux com o áudio de narração original, se existir.
- **Fontes**: confirme que a Caveat realmente carregou antes de capturar (`document.fonts` com `status === 'loaded'`). Se o headless não conseguir baixar, sirva os `.woff2` localmente via `page.route`. Na cena de teste o vídeo saiu com fonte serifada até eu fazer isso.
- Mesma resolução, fps e duração do vídeo atual. A animação precisa bater com os tempos da narração.

## 5. Como trabalhar comigo
1. Primeiro me mostre a tabela de cenas.
2. Depois faça **só a primeira cena (~10 s)** e me mande um MP4 e uma folha de contato (grade com um frame a cada ~0,8 s). Só siga depois que eu aprovar.
3. Revise pelas folhas de contato, cena a cena, e me mande o vídeo completo no final.
