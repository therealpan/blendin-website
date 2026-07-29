# BlendIn — sito v2 (scroll cinematico)

Sito statico autonomo. Caricalo così com'è: nessuna build, nessuna dipendenza esterna
a parte i font Google.

```
index.html          la pagina (monta il motore e contiene tutta la copy IT)
interview.html      demo intervista (linkata dal CTA secondario)
manifest.json       PWA + icone
favicon.ico
assets/
  scrub-engine.js   motore scroll→camera, vanilla JS, zero dipendenze
  *.webp            poster/fallback: il PRIMO frame di ogni clip
  vid/*.mp4         i 7 leg della ripresa continua
  brand/            wordmark + set icone (dal brand kit v1)
_src/               master grezzi 1080p + still 2k — NON serve online
```

## Logo

Il marchio in alto a sinistra è `assets/brand/logo.png`, cioè `assets/logo-dark.png` della
v1 (wordmark bianco+oro, la versione per fondi scuri) ridimensionato a 380px. Sostituisce
il segnaposto generato dal motore via due regole CSS in `index.html`: il motore incapsula
il proprio CSS in `@layer sw`, quindi le regole della pagina vincono senza `!important`.

Il testo "BlendIn" resta nel DOM ma nascosto visivamente — serve a dare un nome
accessibile al link del brand. Se cambi il logo, non cancellarlo.

## I due link in cima

`scrub-engine.js` accetta `cta` come singolo `{label, href}` **oppure come array**. Con più
di uno, il primo è pieno e gli altri ghost; `variant: 'ghost' | 'solid'` forza la scelta,
così il pieno può stare a destra senza riordinare l'array. Chiavi extra riconosciute:
`target` e `rel` per i link esterni, `data: {…}` per attributi `data-*` arbitrari (serve
all'integrazione in Next.js per agganciare un handler senza toccare il motore).

I due URL stanno in cima allo script di `index.html`, in due costanti:

```js
const LOUNGE = 'https://blendin.app/';        // dove si entra col codice del biglietto
const CALL   = 'https://cal.eu/piirz/30min';  // prenotazione call
```

`LOUNGE` punta all'app in produzione perché finché questa cartella è servita da sola non
esiste una rotta `/lounge`. **Quando la landing viene integrata in `blendin-chat` diventa
`/lounge`, ed è l'unica riga da cambiare**: le stesse costanti alimentano anche i due
bottoni della scena finale.

Il link al lounge non è decorativo. I biglietti stampati dal pannello organizzatore
dicono *"Inserisci il codice su blendin.app"*, quindi deve restare raggiungibile in un
click da qualunque punto della pagina. Per questo è lui a restare quando lo spazio manca.

### Comportamento ai vari formati

La barra tiene marchio, 7 pillole di nav e i CTA. Non ci stanno tutti a ogni larghezza:

| larghezza | nav | CTA in barra |
|---|---|---|
| < 861px | nascosta (regola preesistente del motore) | solo il pieno |
| 861-1240px | visibile, pillole compattate | solo il pieno |
| > 1240px | visibile, pillole piene | entrambi |

Verificato con uno sweep da 360 a 1600px a passi di 10: nessun overflow della barra,
margine minimo 18px a 360px, e la nav non va mai a capo. Contrasti misurati sui pixel
renderizzati, non sul CSS: 9,26:1 il pieno, 15,20:1 il ghost.

**Se cambi le etichette dei CTA o della nav, rifai lo sweep.** Le soglie 861 e 1240 sono
tarate sulle stringhe attuali, non su una regola generale.

Le voci nascoste in barra non spariscono dal sito: "Parliamone" resta il bottone primario
della scena finale.

## Il footer

Sta in `index.html` come `<footer class="bl-foot">`, **dopo** il container del motore e in
HTML statico, non generato da JS: i link legali devono esistere anche per un crawler che
non esegue script.

Funziona perché il motore occupa lo schermo con elementi `position: fixed`, ma la sua
altezza di scroll è data da `.sw-track`. Quando lo scroll supera il track, il footer risale
normalmente sopra l'ultimo fotogramma. Nessun hook, nessuna dipendenza dal motore.

Tre dettagli che lo tengono in piedi:

- **`.bl-foot::before`** è una sfumatura di 96px sopra il bordo. Senza, l'ultimo fotogramma
  viene tagliato di netto dalla riga del footer.
- **`body.is-foot`** azzera l'opacità di `.sw-copylayer`, `.sw-route` e `.sw-hint`, che sono
  `fixed` e resterebbero sopra il testo del footer. Lo decide un `IntersectionObserver` sul
  footer con `rootMargin: 0px 0px -80px 0px`, quindi scatta quando il footer è entrato per
  ~80px e non al primo pixel. Solo opacità, nessun movimento: regge anche a motion ridotto.
- **Il motore non fa più svanire l'ultima scena** oltre la sua fine (`read()`, la riga
  `i < NSEG - 1`). Prima, nella coda del track, l'ultimo fotogramma andava a zero e restava
  visibile solo il glow grigio dello sfondo. Con un footer sotto sarebbe stato ancora più
  evidente: si vedeva la macchia grigia per 700px di scroll.

### Percorsi dei link legali

Sono relativi (`../privacy.html`) perché le 15 pagine legali stanno nella root del repo, un
livello sopra questa cartella. **Quando la landing va su blendin.app diventano assoluti** e
le pagine vanno portate su quel dominio: oggi vivono solo su `blendin.piirz.eu`.

### Scelte di contenuto

Niente emoji nei badge, a differenza della v1: al loro posto tre affermazioni verificabili
(GDPR by design, cancellazione a 30 giorni, età minima 16), che sono le stesse condizioni
dichiarate nell'informativa. È stato tolto il badge "PiirZ Prompt Router": è gergo interno
e a un organizzatore non dice niente.

C'è anche la ragione sociale completa con numero di registro e VAT. Su un sito che raccoglie
dati personali il titolare del trattamento va identificabile senza aprire l'informativa.

### Misurato, non stimato

Contrasti letti dai pixel renderizzati: 16,89:1 il nome, 11,12:1 il corpo, 11,09:1 i link,
8,21:1 i badge, 5,28:1 la riga societaria. Quest'ultima stava a 3,23:1 con l'opacità
iniziale e l'ho alzata: sotto la soglia AA. Aree cliccabili tutte a 44px o più, ottenute
con padding verticale e margine negativo, così la riga non si gonfia. Il padding
orizzontale è zero di proposito: staccava la punteggiatura dal testo del link.

## Perché in `index.html` c'è un blocco "chrome scuro"

`scrub-engine.js` è scritto per un **tema chiaro**: usa `#fff` come superficie di nav,
CTA, bottone primario ed etichette della route, e `--sw-ink` come testo sopra quelle
superfici. Con un tema scuro `--sw-ink` è crema, quindi ogni coppia diventa
bianco-su-bianco: il CTA "Parliamone" misurava **1,06:1** di contrasto.

Il blocco di override in `index.html` riporta quelle superfici a scuro. Contrasti dopo
la correzione (WCAG AA richiede 4,5:1):

| elemento | prima | dopo |
|---|---|---|
| CTA barra / bottone primario | 1,06 | 9,49 |
| voci del menu | ~1,05 | 8,90 |
| etichetta route | ~1,05 | 16,89 |
| hint "scorri" | variabile sul frame | 10,83 |

C'è anche un velo sfumato dietro la barra (`.sw-topbar::before`): senza, il chrome
sparisce quando la camera passa su un frame luminoso.

`.sw-brand{flex:none}` impedisce che il logo venga schiacciato a zero dal flex della
barra alle larghezze intermedie (verificato a 880px, il caso peggiore sopra il
breakpoint mobile).

Due regole di questo blocco esistono **solo** per la stratificazione: `.sw-topcta--ghost`
e il suo `display:none` sotto i 1240px. Il motore le dichiara già dentro `@layer sw`, ma la
regola `.sw-topcta` di questa pagina non è layered e quindi le batte. Senza il duplicato, il
secondo CTA tornerebbe oro identico al primo e resterebbe visibile anche dove non ci sta.
È lo stesso meccanismo che fa funzionare il logo: comodo finché lo si conosce, insidioso
se lo si scopre per caso.

**Se aggiorni `scrub-engine.js`, ricontrolla questo blocco**: se un giorno il motore
diventasse theme-aware, gli override andrebbero rimossi invece che accumulati.

## Com'è fatto

È **una sola ripresa continua senza tagli**, spezzata in 7 clip da 8s. Ogni clip parte
dall'ultimo frame reale del precedente, quindi le giunture sono frame-identiche e lo
scroll le attraversa senza stacchi. Lo scroll non fa play: pilota `currentTime`.

## Se rifai l'encoding — le due regole che rompono lo scrub

1. **GOP corto.** Scrubbare significa fare seek a ogni frame, e il costo di un seek
   dipende da quanti frame il decoder deve macinare dalla keyframe più vicina. I file
   attuali sono a `-g 8`. Alzarlo (o lasciare il default ~250) fa scattare lo scroll.

2. **Non allungare/accorciare i clip e non ritagliarli ai bordi.** Il primo e l'ultimo
   frame di ogni clip sono le giunture: se li tocchi, compare uno stacco.

Comando usato (da `_src/<nome>-raw.mp4`, che è il master — **non sovrascriverlo**, serve
come sorgente per ogni nuova passata):

```bash
ffmpeg -i in.mp4 -an -vf "unsharp=5:5:0.8:5:5:0.0" \
  -c:v libx264 -preset slow -crf 26 -pix_fmt yuv420p \
  -g 8 -keyint_min 8 -sc_threshold 0 -movflags +faststart out.mp4
```

`crf 26` + `GOP 8` è il punto scelto dopo averlo misurato su `entrance` (seek = tempo
da `currentTime` all'evento `seeked`, mediana su 40 posizioni; SSIM contro il master):

| encode | peso | SSIM | seek mediano | p90 |
|---|---|---|---|---|
| crf20 g8 | 7,5 MB | 0,9925 | 14,5 ms | 28 ms |
| **crf26 g8** | **3,4 MB** | **0,9878** | **10,9 ms** | **14,2 ms** |
| crf28 g8 | 2,7 MB | 0,9856 | 10,8 ms | 15,4 ms |
| crf?? g30 | 1,6 MB | 0,9742 | 25,4 ms | 39,7 ms |

Il GOP lungo risparmia byte proprio non scrivendo i keyframe che allo scrub servono:
a 1,6 MB il seek raddoppia ed è anche la qualità peggiore. Sotto `crf 26` il seek non
migliora più (è già limitato dal GOP, non dal bitrate), quindi scendere serve solo se
conta la banda. Il motore carica ogni clip come **Blob**, quindi non serve che l'host
supporti le richieste byte-range.

I `.webp` in `assets/` devono restare il **primo frame del rispettivo clip**, 1800px,
16:9 — sono il poster fino a che il video non dipinge, e sono anche il fallback per
`prefers-reduced-motion`. Se ci metti un'immagine diversa (o con un altro rapporto) si
vede un salto quando il video subentra.

Gli `still_*.png` in `_src/` sono i 2k generati in fase di art direction: **non finiscono
online**, servivano solo a fissare lo stile delle scene. Non ha senso convertirli in webp —
i poster veri sono già webp e pesano 66–129 KB contro gli 1,5 MB dei 2k.

## Prima di andare online

**La destinazione è blendin.app, non blendin.piirz.eu.** Deciso il 2026-07-28: questa
landing diventa la home della webapp Next.js `blendin-chat`, su due rotte, `/` per la
landing e `/lounge` per il flusso ticket → consenso → intervista. La valutazione completa
(rotte, video, metadata, nginx, pagine legali) sta in `INTEGRAZIONE_v2_blendin_app.md`.

Fatto:

- **Open Graph / Twitter**, immagine `assets/og-image.jpg` (1200×630, ripresa dalla v1),
  più `canonical` e il JSON-LD `SoftwareApplication` allineati alla v1.
- **`robots.txt` e `sitemap.xml`** in questa cartella, 16 URL con hreflang sui legali.
- **Due CTA in barra** e **`<h1>` sulla prima scena**: vedi "I due link in cima".
- **Footer con i link legali**: vedi "Il footer".

Resta da fare:

- **Riscrivere `canonical`, OG, `robots.txt` e `sitemap.xml` su `blendin.app`.** Oggi
  puntano tutti a `blendin.piirz.eu`, che era la destinazione precedente.
- **Portare le 15 pagine legali sul nuovo dominio** e rendere assoluti i percorsi del
  footer. Oggi quelle pagine esistono solo su `blendin.piirz.eu`.
- **`index.html` ha ancora `<meta name="robots" content="noindex, nofollow">`** — voluto,
  finché la landing non è al suo posto definitivo.
- **`LOUNGE` in `index.html` diventa `/lounge`** quando esiste quella rotta.
- Copy solo in italiano. Le altre 4 lingue si aggiungono senza rigenerare nulla: nei video
  non c'è testo. È però una modifica al motore, non solo copy: il config accetta una
  stringa per campo.

Attenzione a un dettaglio della v1: anche il suo `index.html` in produzione ha
`noindex, nofollow`, nonostante `robots.txt` sia aperto. Quindi oggi `blendin.piirz.eu`
non è indicizzato affatto. Se l'intenzione è esserlo, va tolto anche lì.
