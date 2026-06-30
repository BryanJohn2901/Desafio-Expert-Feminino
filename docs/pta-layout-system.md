# PTA Layout System — Landing Pages

Documento de referência e prompt para criar landing pages da Personal Trainer Academy com arquitetura, estilo e integrações padronizados.

**Página de referência:** `index.html` (Desafio Expert Feminino)

---

## 1. Stack obrigatória

| Camada | Tecnologia |
|--------|------------|
| Estrutura | HTML5 estático (`index.html` por produto) |
| Estilo | Tailwind CSS via CDN + bloco `<style>` com utilitários customizados |
| Tipografia | Google Fonts: **Inter** (corpo) + **Oswald** (títulos h1–h6) |
| Ícones | Font Awesome 6.4 (`cdnjs`) |
| Animações de scroll | AOS 2.3.1 (`unpkg`) |
| JavaScript | Vanilla JS inline no final do `<body>` |
| Deploy | Vercel (`vercel.json` com `cleanUrls: true`) |
| Build opcional | Tailwind CLI (`tailwindcss`, `src/input.css`) para otimização futura |

**Não usar:** React, Next.js, Vue ou frameworks SPA para estas landings.

---

## 2. Design tokens

### 2.1 Cores (definidas por projeto)

Cada landing tem sua própria paleta. Não há cores fixas no layout system.

Definir no `tailwind.config` inline do `<head>` sob o namespace `brand.*` e usar **somente** essas classes em todo o HTML e CSS — nunca valores hex soltos no markup.

**Tokens mínimos recomendados:**

| Token | Uso |
|-------|-----|
| `brand.bg` | Fundo principal da página |
| `brand.surface` | Cards, seções alternadas, popup |
| `brand.primary` | CTAs, destaques, links ativos |
| `brand.primaryHover` | Hover dos botões primários |
| `brand.accent` | Gradientes secundários, detalhes |
| `brand.textPrimary` | Títulos e texto principal |
| `brand.textSecondary` | Parágrafos |
| `brand.textMuted` | Labels, metadados, footer |
| `brand.border` | Bordas sutis (`rgba` ou cor derivada) |

Tokens extras (`purple`, `lilac`, `teal`, etc.) são opcionais — adicionar conforme a identidade visual do produto.

No prompt de geração (seção 10), informar a paleta completa do projeto.

### 2.2 Tipografia (obrigatória)

**Inter** para todo texto corrido (parágrafos, labels, botões, inputs, navegação).  
**Oswald** para títulos (`h1`–`h6`) e elementos com classe `font-display`.

#### Google Fonts (`<head>`)

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&family=Oswald:wght@400;500;600;700&display=swap" rel="stylesheet">
```

#### Tailwind config (`fontFamily`)

```js
fontFamily: {
  sans: ['Inter', 'sans-serif'],
  display: ['Oswald', 'sans-serif'],
}
```

#### CSS global (bloco `<style>`)

```css
body {
  font-family: 'Inter', sans-serif;
}

h1, h2, h3, h4, h5, h6,
.font-display {
  font-family: 'Oswald', sans-serif;
}
```

#### Uso no HTML

- `<body class="font-sans ...">` — garante Inter via Tailwind no corpo
- Títulos sempre em tags semânticas `h1`–`h6` (herdam Oswald automaticamente)
- Título fora de heading (raro): adicionar classe `font-display`
- **Nunca** usar outra família tipográfica na página

#### Labels de seção

- Uppercase, `tracking-widest`, `text-brand-primary`, classe `.label-deco` (linhas decorativas)
- Permanecem em `<p>` → **Inter** (são texto, não título)

### 2.3 Larguras máximas

```js
maxWidth: {
  'site': '1300px',
  'site-md': '1040px',
  'site-sm': '860px'
}
```

### 2.4 Classes utilitárias obrigatórias (bloco `<style>`)

| Classe | Uso |
|--------|-----|
| `.glass-strong` | Cards, modais, painéis com blur |
| `.btn-shine` | Todos os CTAs principais |
| `.card-lift` | Hover com elevação em cards |
| `.card-gb` | Card com borda gradiente |
| `.label-deco` | Subtítulos de seção com traços laterais |
| `.gradient-rule` | Divisor horizontal entre blocos |
| `.text-stroke` | Números decorativos de fundo em cards |
| `.faq-item` / `.faq-answer` | Accordion do FAQ |
| `.mobile-float` | CTA fixo no rodapé (apenas mobile) |
| `.blob` | Manchas de luz desfocadas no fundo |
| `.bg-grid` | Textura de grid sutil |

### 2.5 Cursor customizado (desktop ≥ 1024px)

- Dois elementos: `#cursor` (anel) e `#cursor-dot` (ponto)
- `body { cursor: none }` em desktop
- Classe `.hover` no cursor ao passar sobre links, botões e inputs

### 2.6 Textura premium

- Grain fixo via `body::before` com SVG noise (`opacity: 0.028`)

### 2.7 Scrollbar (id da página)

Cada página define um **`PAGE_ID`** em kebab-case (ex.: `desafio-expert-feminino`).

O `<html>` recebe `id="page-{PAGE_ID}"`. O estilo da barra de scroll é escopado a esse ID — nunca estilos globais em `::-webkit-scrollbar` sem o seletor da página.

```html
<html lang="pt-BR" id="page-{PAGE_ID}" class="scroll-smooth overflow-x-hidden">
```

```css
/* Scrollbar — id da página */
html#page-{PAGE_ID} {
  scrollbar-width: thin;
  scrollbar-color: [brand.primary] [brand.bg];
}

html#page-{PAGE_ID}::-webkit-scrollbar {
  width: 8px;
}

html#page-{PAGE_ID}::-webkit-scrollbar-track {
  background: [brand.bg];
}

html#page-{PAGE_ID}::-webkit-scrollbar-thumb {
  background-color: [brand.primary];
  border-radius: 2px;
}

html#page-{PAGE_ID}::-webkit-scrollbar-thumb:hover {
  background-color: [brand.primaryHover];
}
```

Substituir `[brand.primary]`, `[brand.primaryHover]` e `[brand.bg]` pelos valores da paleta do projeto.

---

## 3. Anatomia da página

Toda landing segue esta estrutura, nesta ordem lógica:

```
1.  <html>           id="page-{PAGE_ID}" + scroll-smooth
2.  <head>           GTM + SEO + OG + Twitter + canonical + assets
3.  GTM noscript     imediatamente após `<body>`
3.  Cursor           #cursor + #cursor-dot (desktop)
4.  Mobile CTA       .mobile-float (lg:hidden)
5.  Navbar           #main-nav — fixa, glass, logo PTA, âncoras, botão Inscreva-se
6.  Hero             #home — min-h-screen, headline, preço, CTA
7.  [Seções]         Conteúdo variável por produto (credibilidade, dor, método, etc.)
8.  FAQ              #faq — accordion
9.  Footer           Logo, links âncora, copyright
10. Popup            #popup-captura — formulário de captura
11. Scripts          AOS, cursor, popup, máscara telefone, submit
```

### 3.1 Navbar (`#main-nav`)

- Fixa: `fixed top-4 z-50`
- Container com `backdrop-blur`, borda `white/15`, `max-w-[860px]`
- Logo: `assets/logoPTA.svg` — classe `h-14 w-auto`
- Links âncora em uppercase (`#metodo`, `#modulos`, etc. — adaptar por página)
- Botão `onclick="abrirPopup()"` — texto **Inscreva-se**

### 3.2 Hero (`#home`)

- `min-h-screen`, fundo escuro com imagem + gradientes
- Tag pill com borda `brand-primary/30`
- Headline com gradiente em destaque (`bg-clip-text`)
- Bloco de preço com `animate-pulse-slow` quando aplicável
- CTA principal: `btn-shine bg-brand-primary` → `onclick="abrirPopup()"`
- Scroll hint no desktop (`.scroll-hint`)

### 3.3 Seções de conteúdo

Padrão de cada `<section>`:

```html
<section id="nome-secao" class="py-24 bg-brand-surface border-y border-white/5 relative overflow-hidden">
  <div class="blob ..."></div> <!-- opcional -->
  <div class="container mx-auto px-6 max-w-site relative z-10">
    <div class="text-center mb-16" data-aos="fade-up">
      <p class="text-brand-primary font-bold uppercase tracking-widest text-xs md:text-sm mb-3 label-deco">
        LABEL DA SEÇÃO
      </p>
      <h2 class="text-3xl md:text-4xl font-black text-white tracking-tight">Título</h2>
    </div>
    <!-- conteúdo -->
  </div>
</section>
```

Alternar fundos: `bg-brand-bg` ↔ `bg-brand-surface` entre seções.

**Cards com número decorativo:** sempre `overflow-hidden` no container e posicionar o número com `absolute right-2 -top-4` (nunca `-right-2`, para não vazar da caixa).

### 3.4 FAQ

- Items com `onclick="toggleFaq(this)"`
- Ícone `+` que rotaciona 45° quando aberto
- Um item aberto por vez

### 3.5 Footer

- Logo PTA (`h-14 w-auto`), links âncora, `© [ano] Personal Trainer Academy`
- `.gradient-rule` antes da linha final descritiva do produto

---

## 4. Popup de captura (`#popup-captura`)

### 4.1 Estrutura HTML obrigatória

```html
<div id="popup-captura"
  class="fixed inset-0 z-[100] hidden items-center justify-center p-4 bg-black/95 backdrop-blur-sm"
  aria-hidden="true">
  <div class="relative bg-brand-surface border border-brand-primary/30 rounded-sm shadow-2xl max-w-md w-full p-8 md:p-10">
    <div class="absolute top-0 left-0 w-full h-0.5 bg-gradient-to-r from-brand-primary to-brand-accent rounded-t-sm"></div>

    <button type="button" onclick="fecharPopup()" aria-label="Fechar" class="absolute top-6 right-6 ...">
      <i class="fas fa-times text-sm"></i>
    </button>

    <div class="text-center mb-6">
      <h3 class="font-black text-2xl text-white uppercase tracking-tight mb-1">Garanta sua vaga</h3>
      <p class="text-sm text-brand-textMuted">Preencha seus dados para se inscrever.</p>
    </div>

    <form id="form-captura" class="space-y-4">
      <!-- Campos visíveis -->
      <input type="text" id="lead-nome" name="nome" required>
      <input type="email" id="lead-email" name="email" required>
      <input type="tel" id="lead-telefone" name="telefone" required maxlength="15">

      <!-- Campos ocultos UTM (obrigatórios) -->
      <input type="hidden" id="utm_source" name="utm_source">
      <input type="hidden" id="utm_term" name="utm_term">
      <input type="hidden" id="utm_campaign" name="utm_campaign">
      <input type="hidden" id="utm_medium" name="utm_medium">
      <input type="hidden" id="utm_content" name="utm_content">
      <input type="hidden" id="url" name="url">

      <p class="error-msg text-red-400 text-xs hidden">Informe o DDD e o número completo.</p>

      <button type="submit" class="w-full btn-shine bg-brand-primary ... btn-submit">
        QUERO MINHA VAGA
      </button>
    </form>
  </div>
</div>
```

### 4.2 Campo telefone

- Prefixo visual **+55** fixo (não editável)
- Máscara brasileira: `(11) 99999-9999`
- Validar mínimo 10 dígitos (sem contar +55)
- No payload, enviar como `telefone: '+55' + rawPhone` (somente dígitos do input)

### 4.3 Comportamento do popup

```js
function abrirPopup() {
  popup.classList.remove('hidden');
  popup.classList.add('flex');
  document.body.style.overflow = 'hidden';
}
function fecharPopup() {
  popup.classList.add('hidden');
  popup.classList.remove('flex');
  document.body.style.overflow = '';
}
// Fechar: clique no overlay, tecla Escape
```

Todos os CTAs da página chamam `abrirPopup()` — nunca link direto para checkout.

---

## 5. UTMs e rastreamento

### 5.1 Parâmetros suportados

| Parâmetro | Origem |
|-----------|--------|
| `utm_source` | URL → hidden field |
| `utm_medium` | URL → hidden field |
| `utm_campaign` | URL → hidden field |
| `utm_term` | URL → hidden field |
| `utm_content` | URL → hidden field |
| `url` | `window.location.href` no load |

### 5.2 Preenchimento no load da página

```js
(function preencherUTMs() {
  const params = new URLSearchParams(window.location.search);
  const campos = ['utm_source', 'utm_term', 'utm_campaign', 'utm_medium', 'utm_content'];
  campos.forEach((campo) => {
    const el = document.getElementById(campo);
    if (el) el.value = params.get(campo) || '';
  });
  const urlField = document.getElementById('url');
  if (urlField) urlField.value = window.location.href;
})();
```

### 5.3 Query string para redirect pós-submit (Make / checkout)

Ao redirecionar após o envio, propagar os dados neste formato:

```
?name={nome}&email={email}&telefone={telefone}&utm_source={utm_source}&utm_term={utm_term}&utm_campaign={utm_campaign}&utm_medium={utm_medium}&utm_content={utm_content}&url={url}
```

> **Importante:** usar `telefone`, nunca `phone`.

---

## 6. Webhook e payload

### 6.1 URLs do webhook

| Tipo de produto | URL base |
|-----------------|----------|
| **Qualquer produto EXCETO Pós-Graduação** | `https://hook.us1.make.com/bj1q14346ezeybpcqjb9ctea89aoatvn` |
| **APENAS Pós-Graduação** | `https://hook.us1.make.com/bhigavwc6bfhskmnicov25r6jv9fnt1p` |

### 6.2 Parâmetro `produto` (obrigatório)

Sempre anexar `?produto=NOMENCLATURA` à URL do webhook.

**Convenção de nomenclatura:** PascalCase ou snake_case descritivo, sem espaços.

Exemplos:
- `2_Dias_Expert_Feminino`
- `Pos_Saude_da_Mulher`
- `Masterclass_Biomecanica`

```js
const PRODUTO = 'NOMENCLATURA_DO_PRODUTO'; // ← alterar por landing
const WEBHOOK_BASE = 'https://hook.us1.make.com/bj1q14346ezeybpcqjb9ctea89aoatvn'; // ou URL da pós
const webhookUrl = `${WEBHOOK_BASE}?produto=${PRODUTO}`;
```

### 6.3 Payload JSON (POST)

```js
const payload = {
  nome: nome,
  email: email,
  telefone: telefone,
  utm_source: document.getElementById('utm_source')?.value || '',
  utm_term: document.getElementById('utm_term')?.value || '',
  utm_campaign: document.getElementById('utm_campaign')?.value || '',
  utm_medium: document.getElementById('utm_medium')?.value || '',
  utm_content: document.getElementById('utm_content')?.value || '',
  url: document.getElementById('url')?.value || window.location.href
};
```

### 6.4 Submit completo

```js
document.getElementById('form-captura').addEventListener('submit', async function (e) {
  e.preventDefault();

  const btnSubmit = this.querySelector('.btn-submit');
  const errorMsg = this.querySelector('.error-msg');
  const telInput = document.getElementById('lead-telefone');

  const rawPhone = telInput.value.replace(/\D/g, '');
  if (rawPhone.length < 10) {
    errorMsg.classList.remove('hidden');
    telInput.focus();
    return;
  }

  btnSubmit.disabled = true;
  btnSubmit.innerHTML = '<i class="fas fa-spinner fa-spin mr-2"></i> PROCESSANDO...';

  const payload = {
    nome: document.getElementById('lead-nome').value.trim(),
    email: document.getElementById('lead-email').value.trim(),
    telefone: '+55' + rawPhone,
    utm_source: document.getElementById('utm_source')?.value || '',
    utm_term: document.getElementById('utm_term')?.value || '',
    utm_campaign: document.getElementById('utm_campaign')?.value || '',
    utm_medium: document.getElementById('utm_medium')?.value || '',
    utm_content: document.getElementById('utm_content')?.value || '',
    url: document.getElementById('url')?.value || window.location.href
  };

  const PRODUTO = 'NOMENCLATURA_DO_PRODUTO';
  const WEBHOOK_BASE = 'https://hook.us1.make.com/bj1q14346ezeybpcqjb9ctea89aoatvn';
  const webhookUrl = `${WEBHOOK_BASE}?produto=${PRODUTO}`;
  const redirectUrl = 'URL_DE_REDIRECT_APOS_SUBMIT'; // checkout ou obrigado

  try {
    await fetch(webhookUrl, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
      mode: 'no-cors'
    });
  } catch (error) {
    console.error(error);
  } finally {
    const query = new URLSearchParams(payload).toString();
    window.location.href = redirectUrl + '?' + query;
  }
});
```

### 6.5 Máscara de telefone

```js
telInput.addEventListener('input', function (e) {
  let value = e.target.value.replace(/\D/g, '');
  if (value.startsWith('55') && value.length > 2) value = value.slice(2);
  value = value.slice(0, 11);
  let formatado = value;
  if (value.length > 2) formatado = `(${value.slice(0, 2)}) ` + value.slice(2);
  if (value.length > 7) formatado = formatado.slice(0, 10) + '-' + formatado.slice(10);
  e.target.value = formatado;
  document.querySelector('.error-msg')?.classList.add('hidden');
});
```

---

## 7. Google Tag Manager (obrigatório)

ID: **GTM-WP5ZCH6J**

Script no `<head>`, logo após `charset` e `viewport`:

```html
<!-- Google Tag Manager -->
<script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dataLayer','GTM-WP5ZCH6J');</script>
```

`noscript` imediatamente após a abertura do `<body>`:

```html
<!-- Google Tag Manager (noscript) -->
<noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-WP5ZCH6J"
height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
```

---

## 8. SEO mínimo (toda página)

```html
<title>[Produto] | Personal Trainer Academy</title>
<meta name="description" content="...">
<meta name="keywords" content="...">
<meta name="robots" content="index, follow">
<meta property="og:type" content="website">
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:image" content="img/og-image.jpg">
<meta property="og:locale" content="pt_BR">
<meta name="twitter:card" content="summary_large_image">
<link rel="canonical" href="https://[dominio]/">
```

---

## 9. Assets padrão

```
/
├── index.html
├── vercel.json
├── assets/
│   ├── logoPTA.svg
│   └── [logo-do-produto].svg
├── img/
│   ├── og-image.jpg
│   └── [imagens do produto]
└── docs/
    └── pta-layout-system.md
```

---

## 10. Checklist antes de publicar

- [ ] GTM (`GTM-WP5ZCH6J`) no `<head>` e `noscript` após `<body>`
- [ ] `<html id="page-{PAGE_ID}">` com scrollbar estilizada pelo ID da página
- [ ] Google Fonts Inter + Oswald carregadas; `body` com `font-sans`
- [ ] Paleta `brand.*` definida no Tailwind config do projeto
- [ ] Logo PTA no menu e footer com `h-14 w-auto`
- [ ] Popup com campos `nome`, `email`, `telefone` + hidden UTMs + `url`
- [ ] Payload usa `telefone` (não `phone`)
- [ ] Webhook correto (pós ou não-pós) com `?produto=NOMENCLATURA`
- [ ] Redirect pós-submit com query `telefone=` (não `phone=`)
- [ ] Todos os CTAs abrem o popup (`abrirPopup()`)
- [ ] Mobile float CTA visível apenas em `< lg`
- [ ] Cards com número decorativo têm `overflow-hidden`
- [ ] AOS inicializado: `AOS.init({ once: true, offset: 50, duration: 800 })`
- [ ] OG image e canonical configurados
- [ ] `vercel.json` com `cleanUrls: true`

---

## 11. Prompt para gerar nova landing

Copie o bloco abaixo e preencha os campos `[...]` ao criar uma página com IA.

---

```
Crie uma landing page HTML completa para a Personal Trainer Academy seguindo RIGOROSAMENTE o PTA Layout System (docs/pta-layout-system.md).

## Produto
- Nome: [NOME DO PRODUTO]
- PAGE_ID (kebab-case): [ex: desafio-expert-feminino]
- Nomenclatura webhook (produto=): [NOMENCLATURA]
- Tipo: [desafio | masterclass | pós-graduação | outro]
- Webhook: [usar URL não-pós OU URL pós conforme tipo]
- URL redirect pós-submit: [URL]
- Domínio canonical: [https://...]
- Preço: [R$ X,XX ou "gratuito"]
- Datas do evento: [se aplicável]

## Paleta de cores
- bg: [#______]
- surface: [#______]
- primary: [#______]
- primaryHover: [#______]
- accent: [#______]
- textPrimary / textSecondary / textMuted: [#______]
- (outros tokens se necessário)

## Conteúdo das seções
1. Hero — headline: [TEXTO]
2. [Nome da seção 2]: [conteúdo/resumo]
3. [Nome da seção 3]: [conteúdo/resumo]
... (listar todas as seções desejadas)

## SEO
- Title: [texto]
- Description: [texto]
- Keywords: [lista]

## Regras técnicas OBRIGATÓRIAS
- HTML estático único (index.html)
- `<html id="page-{PAGE_ID}">` com scrollbar estilizada via `html#page-{PAGE_ID}` (cores da paleta)
- Tailwind via CDN com tokens `brand.*` da paleta informada acima
- Tipografia obrigatória: Inter para textos (`body` com `font-sans`), Oswald para títulos (`h1`–`h6` + `.font-display`)
- Google Fonts: Inter (300–900) + Oswald (400–700)
- Font Awesome 6.4 + AOS 2.3.1
- GTM GTM-WP5ZCH6J (script no head + noscript após body)
- Navbar fixa com logo PTA (`h-14`) + botão Inscreva-se
- Mobile floating CTA
- Cursor customizado desktop
- Popup #popup-captura com form #form-captura
- Campos: nome, email, telefone (+55 prefixo visual)
- Hidden fields: utm_source, utm_term, utm_campaign, utm_medium, utm_content, url
- Preencher UTMs da URL no load
- Payload com telefone (NÃO phone)
- Submit POST para webhook?produto=[NOMENCLATURA] em JSON
- Redirect com query: name, email, telefone, utms, url
- Todos os CTAs chamam abrirPopup()
- Classes: glass-strong, btn-shine, label-deco, card-lift
- Cards com número decorativo: overflow-hidden, right-2 (não -right-2)
- FAQ com toggleFaq()
- Footer padrão PTA
- Vanilla JS inline, sem frameworks
- vercel.json incluído

## Tom de copy
- Direto, autoridade técnica, público de Educação Física
- Destaques em text-brand-primary
- CTAs em uppercase

Gere o arquivo index.html completo e funcional, pronto para deploy na Vercel.
```

---

## 12. Variáveis por projeto

| Variável | Onde definir | Exemplo |
|----------|--------------|---------|
| `PAGE_ID` | `<html id="page-{PAGE_ID}">` + CSS scrollbar | `desafio-expert-feminino` |
| Paleta `brand.*` | `tailwind.config` no `<head>` | Ver seção 2.1 |
| `PRODUTO` | JS do submit | `2_Dias_Expert_Feminino` |
| `WEBHOOK_BASE` | JS do submit | URL Make conforme tipo |
| `redirectUrl` | JS do submit | URL do checkout |
| `form id` | HTML | `form-captura` (ou nome descritivo) |
| Seções | HTML | IDs únicos para âncoras da navbar |
| `og:image` | `<head>` | `img/og-image.jpg` |

---

*Última atualização: junho/2026 — baseado em Desafio Expert Feminino.*
