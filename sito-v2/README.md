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

Comando usato (da `_src/<nome>-raw.mp4`):

```bash
ffmpeg -i in.mp4 -an -vf "unsharp=5:5:0.8:5:5:0.0" \
  -c:v libx264 -preset slow -crf 20 -pix_fmt yuv420p \
  -g 8 -keyint_min 8 -sc_threshold 0 -movflags +faststart out.mp4
```

Per alleggerire: alza `crf` (22–24) o scendi a 720p. Il motore carica ogni clip come
**Blob**, quindi non serve che l'host supporti le richieste byte-range.

I `.webp` devono restare il **primo frame del rispettivo clip** — sono il poster fino a
che il video non dipinge, e sono anche il fallback per `prefers-reduced-motion`. Se ci
metti un'immagine diversa si vede un lampo al cambio scena.

## Prima di andare online

- `index.html` ha `<meta name="robots" content="noindex, nofollow">` — toglilo quando è
  il momento.
- Mancano `robots.txt` e `sitemap.xml`: se questa cartella sostituisce il sito attuale
  vanno ripresi dalla root della v1.
- Mancano i meta Open Graph / Twitter: i link condivisi non mostrano anteprima. In v1
  c'è `assets/og-image.jpg` (1200x630) già pronto da riusare.
- Copy solo in italiano. Le altre 4 lingue si aggiungono senza rigenerare nulla: nei video
  non c'è testo.
