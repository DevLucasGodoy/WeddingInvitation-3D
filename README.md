# 💍 Convite de Casamento 3D — Lucas & Eduarda

> Convite de casamento digital interativo com efeito de flip 3D, desenvolvido em HTML, CSS e JavaScript puro.

🔗 **Site do Casal:** [lucaseeduarda.com](https://lucaseeduarda.com)  
📦 **Repositório das imagens:** [DevLucasGodoy/WeddingInvitation-3D](https://github.com/DevLucasGodoy/WeddingInvitation-3D)

---

## ✨ Sobre o Projeto

Uma página web single-file que simula a experiência de abrir um convite de casamento físico. O usuário interage clicando na tela para revelar progressivamente o envelope, a frente e o verso do convite através de animações 3D suaves.

---

## 🎬 Funcionamento

A interação acontece em 3 estados sequenciais, todos ativados com um clique na tela:

| Estado          | O que aparece                                       |
| --------------- | --------------------------------------------------- |
| **1 → Inicial** | Envelope fechado                                    |
| **2 → Aberto**  | Frente do convite (flip 180°)                       |
| **3 → Verso**   | Verso do convite (flip 360°) + botão "Acessar Site" |

No estado 3, um botão aparece na parte inferior redirecionando para o site oficial do casal. Clicar novamente volta ao estado 2 (frente do convite), criando um loop entre frente e verso.

---

## 🛠️ Tecnologias

- **HTML5** — estrutura e semântica
- **CSS3** — animações 3D (`transform-style: preserve-3d`, `rotateY`, `backface-visibility`), responsividade com media queries, keyframes
- **JavaScript Vanilla** — controle de estados e eventos de clique
- **GitHub Raw** — hospedagem das imagens do convite (`/public/1.png`, `2.png`, `3.png`)

Nenhuma dependência externa ou framework necessário.

---

## 📁 Estrutura

```
WeddingInvitation-3D/
├── index.html        # Arquivo único com HTML, CSS e JS
├── .gitattributes
└── public/
    ├── 1.png         # Frente do convite
    ├── 2.png         # Verso do convite
    └── 3.png         # Envelope
```

---

## ⚙️ Como Usar

Por ser um projeto sem dependências, basta abrir o arquivo no navegador:

```bash
# Clone o repositório
git clone https://github.com/DevLucasGodoy/WeddingInvitation-3D.git

# Abra o arquivo no navegador
open index.html
```

Ou hospede diretamente no **GitHub Pages**, **Vercel** ou qualquer servidor estático — o arquivo `index.html` é totalmente autossuficiente.

---

## 🎨 Personalização

Para adaptar o convite para outro casal ou evento, edite o `index.html`:

**Imagens do convite** — substitua as URLs das imagens no bloco de cards:

```html
<!-- Envelope -->
<img src="SUA_IMAGEM_ENVELOPE.png" />
<!-- Frente -->
<img src="SUA_IMAGEM_FRENTE.png" />
<!-- Verso -->
<img src="SUA_IMAGEM_VERSO.png" />
```

**Link do botão final** — altere a URL do site do casal:

```js
window.open("https://seusite.com", "_blank");
```

**Cor de fundo** — altere a variável no CSS:

```css
body {
  background-color: #e5ffe6;
}
```

**Mensagens de instrução** — edite o array `messages` no JavaScript:

```js
const messages = [
  "Clique para abrir o convite ✨",
  "Clique para ver o verso 🌸",
  "Clique para mais informações",
];
```

---

## 📱 Responsividade

O layout é adaptado para diferentes tamanhos de tela via media queries:

- **Desktop** — largura máxima de 600px centralizada
- **Tablet** (`≤ 768px`) — margens e fonte reduzidas
- **Mobile** (`≤ 480px`) — layout compacto
- **Telas baixas** (`altura ≤ 600px`) — card menor para caber na viewport

---

## 📜 Licença

Projeto pessoal de uso livre. Sinta-se à vontade para usar como base para o seu próprio convite digital.
