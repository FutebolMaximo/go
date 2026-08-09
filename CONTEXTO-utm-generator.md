# Gerador de Links UTM — Futebol Máximo

## O que é esse projeto
Página HTML single-file hospedada no GitHub Pages.
Gera links rastreados com parâmetros UTM para compartilhar nos vídeos do YouTube.
Repositório: FutebolMaximo/fantasy-link-generator

## Regra mais importante
NUNCA separar o HTML em múltiplos arquivos.
Tudo — HTML, CSS, JavaScript — deve permanecer em um único arquivo (index.html).

---

## Arquivo do projeto
- `index.html` — gerador completo (era utm-generator.html, renomeado para index.html no GitHub)

---

## Estrutura da interface (ordem de cima para baixo)

### Campos visíveis
1. **Formato** (`utm_campaign`) — toggle de seleção única com 2 botões visíveis:
   - 📹 Long Form → valor: `longform`
   - ⚡ Shorts → valor: `shorts`

2. **Destino** — toggle de seleção única com 2 botões visíveis:
   - 📱 App → base URL: `https://9fantasy.app/app`
   - 🌐 Web → base URL: `https://futebolmaximo.github.io/9fantasy-web/`
   - Default inicial: **App**
   - A troca de destino altera o link base usado na URL gerada

3. **Link do Vídeo** (`utm_source`) — campo de texto livre
   - Usuário cola qualquer link do YouTube
   - Sistema extrai o videoID automaticamente
   - Formatos suportados:
     - `https://youtu.be/O5XWejP8yLg` → `O5XWejP8yLg`
     - `https://youtube.com/watch?v=O5XWejP8yLg` → `O5XWejP8yLg`
     - `https://youtube.com/shorts/O5XWejP8yLg` → `O5XWejP8yLg`
   - Mostra badge verde com ID detectado quando válido
   - Mostra erro quando link inválido

### Campos ocultos (display:none — lógica ativa, não aparecem na tela)
- **Base** — exibe qual URL base está ativa (atualiza com toggle Destino)
- **Fonte** (`utm_medium`) — fixo sempre como `futebolmaximo`

---

## Estrutura do link gerado
```
{BASE}?source=youtube&utm_source={videoID}&utm_medium=futebolmaximo&utm_campaign={formato}
```

### Exemplos reais:
```
App + Long Form:
https://9fantasy.app/app?source=youtube&utm_source=O5XWejP8yLg&utm_medium=futebolmaximo&utm_campaign=longform

Web + Shorts:
https://futebolmaximo.github.io/9fantasy-web/?source=youtube&utm_source=O5XWejP8yLg&utm_medium=futebolmaximo&utm_campaign=shorts
```

### Parâmetros fixos (sempre presentes):
| Parâmetro     | Valor fixo        |
|---------------|-------------------|
| `source`      | `youtube`         |
| `utm_medium`  | `futebolmaximo`   |

### Parâmetros variáveis:
| Parâmetro      | Origem                    |
|----------------|---------------------------|
| `utm_source`   | videoID extraído do link  |
| `utm_campaign` | `longform` ou `shorts`    |

---

## Variáveis de estado (JavaScript)
```javascript
const BASES = {
  app: 'https://9fantasy.app/app',
  web: 'https://futebolmaximo.github.io/9fantasy-web/'
};

let selectedFormato = '';     // 'longform' | 'shorts'
let selectedDestino = 'app';  // 'app' | 'web'
let currentVideoId  = '';     // ID extraído do YouTube
```

---

## Funcionalidades
- Botão **Copiar link** — desabilitado até ter pelo menos 1 campo preenchido
- Feedback visual ao copiar: botão fica verde + toast "✓ Link copiado!"
- Botão **Limpar campos** — reseta tudo para o estado inicial
- Preview do link gerado em tempo real com cores por parte da URL:
  - Base URL: cinza
  - Chaves dos parâmetros: roxo
  - Valores dos parâmetros: verde

---

## Design / Visual
- Tema escuro (`#0f1117` background)
- Accent: roxo (`#6c63ff` / `#a78bfa`)
- Sucesso: verde (`#22c55e`)
- Erro: vermelho (`#ef4444`)
- Botões de toggle: borda roxa + fundo roxo translúcido quando ativo
- Card centralizado, max-width 640px
- Fonte: Inter / system-ui

---

## Próximo passo planejado (ainda não implementado)
Integração com Google Apps Script para registrar cliques automaticamente no Google Sheets.

### Como funcionará:
1. Links gerados apontarão para uma **página-ponte** (no GitHub Pages)
2. A página-ponte registra os parâmetros via POST no Apps Script (Web App)
3. Apps Script grava no Sheets e a página-ponte redireciona para o destino final

### Apps Script já criado (aguardando integração):
Colunas gravadas: Plataforma | ID do Vídeo | Canal | Formato | Dispositivo | País | ID Único
Função principal: `registrarClique(dados)` / endpoints: `doGet(e)` e `doPost(e)`

### O que falta fazer:
- Criar a página-ponte (`tracker.html` ou similar) no mesmo repositório
- Atualizar o gerador para que os links gerados passem pela página-ponte
- Publicar o Apps Script como Web App e conectar os dois
