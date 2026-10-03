# Ilhas BR · EBAT — Realidade Aumentada

Experiência de realidade aumentada que roda **no navegador do celular** (sem instalar app): um arquipélago de ilhas flutuantes ao redor da pessoa.

- **Atmosfera verde e amarela:** névoa colorida, luz e partículas luminosas por todo o espaço (não só junto das ilhas); sobre a câmera entra um filtro verde/amarelo.
- **Abertura:** ao abrir o link a ilha da Petrobras aparece mais abaixo, com o **logo oficial em 3D no centro da tela**, e o painel de início fica embaixo.
- **Ilha central (Petrobras):** maior e mais volumosa, com o logo em 3D, iluminado e flutuando. É fixa.
- **Cinco ilhas de tema**, cada uma com um símbolo 3D: Inteligência Artificial, Programação Criativa, Eletrônica Criativa, Arte Interativa e Audiovisual Expandido.
- **Ilhas extras** com símbolos de arte e tecnologia. Ao todo são 14 ilhas **distribuídas em 360°**: aparecem em todo o redor, inclusive atrás de você.
- **Andar para se aproximar:** no AR completo (Android/Chrome) você caminha de verdade; no modo câmera, cada passo detectado pelo celular avança ~65 cm (botão "Passos" liga/desliga; ▲ ▼ também funcionam). Aproximar faz a ilha crescer.
- **Interação só com a câmera e o toque:** você gira, anda e se aproxima; as ilhas não são movidas nem ampliadas com o dedo. **Tocar numa ilha de tema, de perto,** abre a explicação como um **painel 3D flutuando no espaço**, ao lado da ilha: ele fica ancorado no mundo (ao andar, você chega perto dele), vira-se para você e liga-se ao símbolo por um fio de luz. Tocar no painel (ou longe dele) fecha; se você se afastar muito, ele fecha sozinho.

> **Status: versão 0.4, ainda não testada em aparelho real.** O código foi escrito e a lógica (distribuição das ilhas, tamanho do painel) tem testes automáticos, mas a renderização e os passos precisam ser conferidos num celular. Veja "O que testar primeiro".

## Dois modos de funcionamento

| | **AR completo (WebXR)** | **Câmera + giroscópio** |
|---|---|---|
| Aparelhos | Android com Chrome (ARCore), Meta Quest | Qualquer celular moderno (iPhone incluso) e computador com webcam |
| Andar pelo espaço | **Sim**, de verdade (6 graus de liberdade) | Passos detectados pelo celular + botões ▲ ▼ |
| Abrir explicação | Tocar na ilha (ou apertar o gatilho no Quest) | Tocar na ilha |

O iPhone (Safari) não oferece WebXR, por isso existe o segundo modo. Para ter caminhada real no iPhone seria preciso um app nativo (ou um serviço de WebAR de terceiros).

## Rodar no computador

```bash
npm start          # ou: python3 -m http.server 8080
# abra http://localhost:8080
```

`localhost` conta como endereço seguro, então a câmera funciona. No computador você olha arrastando o mouse, anda com a roda e clica nas ilhas para abrir as explicações.

## Colocar no GitHub e publicar (GitHub Pages)

Câmera e WebXR **só funcionam em HTTPS**; o GitHub Pages já entrega isso, de graça.

> **Jeito mais simples:** envie apenas o arquivo `dist/index.html`, renomeado para `index.html` na raiz do repositório. Ele é autossuficiente (CSS, JavaScript e logo dentro). Foi a falta das pastas `css/` e `js/` no repositório que deixou a primeira página sem estilo.

1. Em github.com, clique em **New repository** e crie `ilhas-br-ebat` (público ou privado, conforme o contrato).
2. Envie os arquivos: **Add file → Upload files**, arraste **todo o conteúdo desta pasta** (mantendo as subpastas) e clique em *Commit changes*.
3. Vá em **Settings → Pages**. Em *Build and deployment*, escolha *Deploy from a branch*, branch `main`, pasta `/ (root)`, e salve.
4. Em cerca de um minuto o endereço aparece na mesma tela: `https://SEU-USUARIO.github.io/ilhas-br-ebat/`.
5. Gere um QR Code desse endereço para o cartaz ou stand.

Atenção: GitHub Pages em repositório **privado** só existe em planos pagos. Se o repositório precisar ser privado, avise e vemos outra hospedagem (Netlify, Cloudflare Pages).

## Logo da Petrobras

O logo oficial já está em `assets/logos/petrobras.png` (PNG com fundo transparente). Para trocar, substitua o arquivo mantendo o nome. No `dist/index.html` o logo vai embutido; para trocá-lo ali, rode `node scripts/build-single.mjs` (precisa de `npm i -D esbuild`). Ele vira um logo em 3D: o código empilha várias camadas da imagem para dar espessura e adiciona brilho, feixe de luz e uma luz que ilumina a ilha. Se o arquivo faltar, aparece uma placa verde "COLOQUE O LOGO". Detalhes em `assets/logos/LEIAME.md`.

Use apenas as versões e aplicações autorizadas pelo manual de marca/contrato de patrocínio. O texto de crédito da tela inicial (`index.html`, classe `credit`) também deve ser conferido com o contrato.

## O que testar primeiro (checklist no celular)

1. A tela inicial abre e o botão leva à câmera (ou ao AR, no Android).
2. As ilhas aparecem; a da Petrobras fica à frente, as outras ao redor ao girar.
3. Os botões ▲ ▼ aproximam e as ilhas crescem. Ao chegar perto de uma ilha de tema aparece a dica "Toque ou clique na ilha para saber mais".
4. Tocar na ilha (de perto) abre o painel de explicação; de longe aparece o aviso para chegar mais perto.
5. Ao tocar numa ilha de tema, o painel de explicação surge flutuando entre você e a ilha, com um fio de luz até o símbolo. Ande até ele: o painel fica no lugar. Toque nele para fechar.
6. Toque numa ilha longe: aparece o aviso para chegar mais perto.
7. O logo 3D da ilha central acompanha o seu olhar e brilha; se o arquivo `petrobras.png` ainda não existe, aparece a placa verde.
8. Desempenho: se travar, reduza `decorIslands` em `js/config.js` e/ou o `setPixelRatio` em `js/main.js`.

## Ajustes rápidos (`js/config.js`)

- `decorIslands`: quantas ilhas extras com símbolos decorativos (padrão 8).
- `infoDistance`: a que distância (em metros) o toque abre a explicação (padrão 2,4).
- `layoutSeed`: outro número = outra distribuição das ilhas.
- `walkSpeed`, `stepLength`, `stepThreshold`.

Posição e tamanho das ilhas principais ficam em `js/logic.js` (`layoutIslands`). Os **textos das explicações** ficam em `js/topics.js` (título, texto, exemplo e cor de cada tema). Os símbolos 3D ficam em `js/symbols.js`.

## Rodar sem internet (eventos)

Por padrão, o three.js vem de CDN (versões fixas). Para funcionar offline, com Node 18+ instalado:

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
js/atmosphere.js      névoa e partículas verde/amarelas em todo o espaço
js/rig.js             câmera com giroscópio / arrastar, andar, detector de passos
js/infopanel.js       painel 3D flutuante com a explicação
js/interaction.js     toque/mouse e WebXR (só tocar)
js/xr.js              sessão WebXR
js/logic.js           lógica pura (testada)
js/audio.js           sons curtos
scripts/vendor.mjs    baixa as bibliotecas para uso offline
scripts/build-single.mjs  gera dist/index.html (arquivo único)
tests/logic.test.mjs  testes (npm test)
```

## Limitações conhecidas

- Sem oclusão com o mundo real: as ilhas são desenhadas sobre a imagem da câmera, sem ficar "atrás" de móveis ou pessoas.
- A detecção de passos usa o acelerômetro e é aproximada; o AR completo (Android/Chrome) mede a caminhada de verdade.
- Ilhas em modelo 3D artístico (estilo da imagem de referência) podem substituir as procedurais depois: o código de `island.js` foi isolado para isso.

## Licença

Ainda não definida. Sem arquivo de licença, o código fica com todos os direitos reservados à EBAT. Defina antes de tornar o repositório público.
