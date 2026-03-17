# BlendIn — Entra nel Lounge

![BlendIn](assets/bg-entrance.webp)

**L'AI che trasforma eventi di networking da incontri casuali a connessioni progettate.**

BlendIn profila i partecipanti tramite interviste conversazionali su WhatsApp e compone tavoli dove ogni connessione conta.

## Il Concept

Non una landing page. Un viaggio immersivo: il visitatore entra nel lounge, incontra Blendy, scopre il profiling, vede il matching in azione, e viene invitato a trasformare il suo prossimo evento.

## 9 Scene

1. **L'ingresso** — facciata esterna del BlendIn Lounge
2. **Blendy ti accoglie** — alla porta con il gesto di benvenuto
3. **Al bancone** — il problema degli eventi di networking
4. **Il viaggio** — 3 step: WhatsApp → Profiling → Tavoli
5. **Il profiling** — Blendy scrive, le 7 dimensioni appaiono
6. **Il matching** — complementarità, non somiglianza
7. **Composizione tavoli** — Blendy posiziona le card
8. **Eureka!** — i numeri del lounge
9. **L'invito dorato** — CTA finale

## Performance

| Metrica | Valore |
|---|---|
| Peso totale pagina | ~910KB (HTML 24KB + 18 WebP) |
| Dipendenze esterne | Zero (vanilla HTML/CSS/JS) |
| Font | Google Fonts (Playfair Display, DM Sans, JetBrains Mono) |
| Animazioni | Solo `transform` + `opacity` via IntersectionObserver |
| Accessibilità | `prefers-reduced-motion` supportato |
| Mobile | Overlay atmosferici disabilitati, blur ridotto |

## Deploy

```bash
# Qualsiasi hosting statico
npx vercel
# oppure
netlify deploy --prod
```

## Progetto

- **Prodotto**: agente AI WhatsApp per profiling & matching
- **Team**: [PiirZ Digital](https://piirz.com)
- **Contatti**: blendin@piirz.com
