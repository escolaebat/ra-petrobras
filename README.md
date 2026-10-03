# Ilhas BR · EBAT — Realidade Aumentada

Experiência de realidade aumentada que roda **no navegador do celular** (sem instalar app): um arquipélago de ilhas flutuantes ao redor da pessoa.

- **Ilha central (Petrobras):** maior e mais volumosa, com o **logo em 3D, iluminado e flutuando**. É fixa e não pode ser segurada.
- **Cinco ilhas de tema**, cada uma com um símbolo 3D: **Inteligência Artificial** (rede neural), **Programação Criativa** (`</>`), **Eletrônica Criativa** (microchip com LED), **Arte Interativa** (paleta com ondas) e **Audiovisual Expandido** (play com barras de áudio).
- **Ilhas extras** com símbolos de arte e tecnologia (engrenagem, lâmpada, planeta, cubo, nota musical e lápis).
- **Girar a câmera** revela as ilhas ao redor, inclusive atrás de você. **Aproximar** faz a ilha crescer.
- **Chegar perto e tocar** numa ilha de tema abre um painel com a explicação do tema. Com a mão, uma pinça rápida faz o mesmo.
- **Segurar:** mantenha a pinça (ou toque e arraste) e a ilha acompanha a mão; girar a mão gira a ilha.

> **Status: versão 0.1, ainda não testada em aparelho real.** O código foi escrito e a lógica (pinça, mapeamento de tela, distribuição das ilhas) tem testes automáticos, mas a renderização e o rastreamento de mãos precisam ser conferidos num celular. Veja "O que testar primeiro".

## Dois modos de funcionamento

| | **AR completo (WebXR)** | **Câmera + giroscópio** |
|---|---|---|
| Aparelhos | Android com Chrome (ARCore), Meta Quest | Qualquer celular moderno (iPhone incluso) e computador com webcam |
| Andar pelo espaço | **Sim**, de verdade (6 graus de liberdade) | Não: usa os botões ▲ ▼ (ou pinça com dois dedos na tela) |
| Segurar ilhas | Tocar e arrastar na tela; no Quest, pinça com as mãos | Pinça com a mão na frente da câmera (MediaPipe) ou tocar e arrastar |

O iPhone (Safari) não oferece WebXR, por isso existe o segundo modo. Para ter caminhada real no iPhone seria preciso um app nativo (ou um serviço de WebAR de terceiros).

## Rodar no computador

```bash
npm start          # ou: python3 -m http.server 8080
# abra http://localhost:8080
```

`localhost` conta como endereço seguro, então a câmera funciona. No computador você olha arrastando o mouse, anda com a roda e segura ilhas clicando e arrastando (ou com a pinça na webcam).

## Colocar no GitHub e publicar (GitHub Pages)

Câmera e WebXR **só funcionam em HTTPS**; o GitHub Pages já entrega isso, de graça.

1. Em github.com, clique em **New repository** e crie `ilhas-br-ebat` (público ou privado, conforme o contrato).
2. Envie os arquivos: **Add file → Upload files**, arraste **todo o conteúdo desta pasta** (mantendo as subpastas) e clique em *Commit changes*.
3. Vá em **Settings → Pages**. Em *Build and deployment*, escolha *Deploy from a branch*, branch `main`, pasta `/ (root)`, e salve.
4. Em cerca de um minuto o endereço aparece na mesma tela: `https://SEU-USUARIO.github.io/ilhas-br-ebat/`.
5. Gere um QR Code desse endereço para o cartaz ou stand.

Atenção: GitHub Pages em repositório **privado** só existe em planos pagos. Se o repositório precisar ser privado, avise e vemos outra hospedagem (Netlify, Cloudflare Pages).

## Logo da Petrobras

Coloque o arquivo oficial em `assets/logos/petrobras.png` (PNG com fundo transparente, de preferência com 1024 px ou mais de largura). Ele vira um logo em 3D: o código empilha várias camadas da imagem para dar espessura e adiciona brilho, feixe de luz e uma luz que ilumina a ilha. Até lá aparece uma placa verde "COLOQUE O LOGO". Detalhes em `assets/logos/LEIAME.md`.

Use apenas as versões e aplicações autorizadas pelo manual de marca/contrato de patrocínio. O texto de crédito da tela inicial (`index.html`, classe `credit`) também deve ser conferido com o contrato.

## O que testar primeiro (checklist no celular)

1. A tela inicial abre e o botão leva à câmera (ou ao AR, no Android).
2. As ilhas aparecem; a da Petrobras fica à frente, as outras ao redor ao girar.
3. Os botões ▲ ▼ aproximam e as ilhas crescem. Ao chegar perto de uma ilha de tema aparece a dica "Toque ou clique na ilha para saber mais".
4. Tocar na ilha (de perto) abre o painel de explicação; de longe aparece o aviso para chegar mais perto.
5. O círculo branco segue sua mão; ao juntar polegar e indicador sobre uma ilha, ele fica amarelo. Pinça rápida abre a explicação; pinça mantida e movida leva a ilha com a mão.
6. Soltar os dedos solta a ilha; depois de alguns segundos ela volta devagar ao lugar.
7. O logo 3D da ilha central acompanha o seu olhar e brilha; se o arquivo `petrobras.png` ainda não existe, aparece a placa verde.
8. Desempenho: se travar, reduza `decorIslands` em `js/config.js` e/ou o `setPixelRatio` em `js/main.js`.

## Ajustes rápidos (`js/config.js`)

- `decorIslands`: quantas ilhas extras com símbolos decorativos (padrão 6).
- `infoDistance`: a que distância (em metros) o toque abre a explicação (padrão 2,4).
- `layoutSeed`: outro número = outra distribuição das ilhas.
- `pinch.on / pinch.off`: sensibilidade da pinça (menor = exige dedos mais juntos).
- `walkSpeed`, `returnAfterSeconds`, `holdReleaseGraceMs`.

Posição e tamanho das ilhas principais ficam em `js/logic.js` (`layoutIslands`). Os **textos das explicações** ficam em `js/topics.js` (título, texto, exemplo e cor de cada tema). Os símbolos 3D ficam em `js/symbols.js`.

## Rodar sem internet (eventos)

Por padrão, three.js e MediaPipe vêm de CDN (versões fixas). Para funcionar offline, com Node 18+ instalado:

```bash
node scripts/vendor.mjs
```

Isso baixa tudo para `vendor/` e aponta o `index.html` para os arquivos locais. Depois, envie a pasta `vendor/` junto ao GitHub.

## Estrutura

```
index.html            tela inicial, HUD e import map
css/style.css
js/main.js            orquestra tudo (modos, laço de render, botões)
js/island.js          ilha procedural: rocha, grama, árvores, partículas, logo 3D da ilha central
js/symbols.js         símbolos 3D das ilhas (IA, programação, eletrônica, arte, audiovisual e extras)
js/topics.js          textos das explicações de cada tema
js/world.js           distribuição das ilhas, descoberta, escolha de ilha por raio
js/rig.js             câmera com giroscópio / arrastar, andar
js/hands.js           MediaPipe Hand Landmarker (pinça)
js/interaction.js     segurar ilhas: mãos, toque/mouse, WebXR
js/xr.js              sessão WebXR
js/logic.js           lógica pura (testada)
js/audio.js           sons curtos
scripts/vendor.mjs    baixa as bibliotecas para uso offline
tests/logic.test.mjs  testes (npm test)
```

## Limitações conhecidas

- Sem oclusão com o mundo real: as ilhas são desenhadas sobre a imagem da câmera, sem ficar "atrás" de móveis ou pessoas.
- No modo câmera, a posição da mão no espaço é estimada pelo tamanho dela na imagem (precisão aproximada).
- O rastreamento de mãos baixa cerca de 10 MB na primeira vez; em celulares mais fracos pode reduzir a fluidez.
- Ilhas em modelo 3D artístico (estilo da imagem de referência) podem substituir as procedurais depois: o código de `island.js` foi isolado para isso.

## Licença

Ainda não definida. Sem arquivo de licença, o código fica com todos os direitos reservados à EBAT. Defina antes de tornar o repositório público.
