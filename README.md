# BlendIn — Entra nel Lounge

![BlendIn](assets/bg-entrance.png)

**L'AI che trasforma eventi di networking da incontri casuali a connessioni progettate.**

BlendIn profila i partecipanti tramite interviste conversazionali su WhatsApp e compone tavoli dove ogni connessione conta. Questo repository contiene il sito web — un'esperienza cinematica scroll-driven attraverso il lounge bar di BlendIn, con la mascotte Blendy come host.

## 🍸 Il Concept

Non una landing page. Un viaggio immersivo: il visitatore entra nel lounge, incontra Blendy, scopre come funziona il profiling, vede il matching in azione, e riceve l'invito a trasformare il suo prossimo evento.

## 🎬 9 Scene

1. **L'ingresso** — facciata esterna del BlendIn Lounge di notte
2. **Blendy ti accoglie** — alla porta con il gesto di benvenuto
3. **Al bancone** — il problema degli eventi di networking
4. **Il viaggio** — 3 step: WhatsApp → Profiling → Tavoli
5. **Il profiling** — Blendy scrive, le 7 dimensioni appaiono
6. **Il matching** — complementarità, non somiglianza
7. **Composizione tavoli** — Blendy posiziona le card
8. **Eureka!** — i numeri del lounge
9. **L'invito dorato** — CTA finale

## ✨ Effetti Cinematici

- Film grain SVG overlay
- Texture fumo animata (mix-blend-mode: screen)
- Bokeh dorato pulsante
- Parallax sugli sfondi ambiente
- Glassmorphism (backdrop-filter: blur)
- Scroll-triggered reveals direzionali
- Counter animation a 60fps
- Vignette radiale

## 🛠 Stack

- HTML5 + CSS custom properties + vanilla JS
- Zero dipendenze esterne
- Google Fonts (Playfair Display, DM Sans, JetBrains Mono)
- IntersectionObserver per tutte le animazioni scroll
- Canvas-free (effetti via CSS puro + immagini overlay)
- `prefers-reduced-motion` supportato

## 🚀 Deploy

File statici — deploy su qualsiasi hosting:

```bash
# Vercel
npx vercel

# Netlify
netlify deploy --prod

# Oppure semplicemente apri index.html
```

## 📋 Progetto BlendIn

- **Prodotto**: agente AI WhatsApp per profiling & matching eventi di networking
- **Sviluppato da**: [PiirZ Digital](https://piirz.com)
- **Contatti**: blendin@piirz.com

---

*PiirZ Digital — Architetture AI scalabili, performanti, economiche.*
