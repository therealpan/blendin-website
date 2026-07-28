# Handoff — BlendIn sito v2

Landing page in cui **lo scroll pilota una camera cinematografica**: una sola ripresa
continua, senza tagli, che attraversa il lounge di BlendIn seguendo Blendy. Lo scroll non
fa partire un video, ne pilota il `currentTime`: si può andare avanti e indietro come una
moviola.

Stato: **completa e verificata, in attesa di review del team.** Non è ancora online sul
dominio pubblico.

> Nessuna credenziale in questo documento: la cartella `sito-v2/` viene servita pubblicamente,
> quindi tutto ciò che ci sta dentro è raggiungibile da chiunque abbia l'URL.

---

## 1. Dove sta tutto

| | |
|---|---|
| Repo | `github.com/therealpan/blendin-website` (**pubblico**) |
| Branch | `V2-anim` — 7 commit, non ancora mergiato |
| Cartella | `sito-v2/` — sito statico autonomo, nessuna build |
| Preview | `https://blendin-website-git-v2-anim-pans-projects-9cc4f4f5.vercel.app/sito-v2/` |
| Produzione | `https://blendin.piirz.eu` — **ancora la v1, intatta** |
| Progetto Vercel | `blendin-website`, production branch = `main` |

L'URL di preview è l'alias del branch: resta valido a ogni push su `V2-anim`.

**L'app del prodotto è un'altra cosa.** `blendin.app` è l'applicazione (Next.js su VPS
Hetzner, `89.167.117.20`, dietro nginx), codebase separato. Questo repo è solo la landing
di marketing.

## 2. Il percorso, in 7 scene

`entrance` → `bar` → `wrongroom` → `interview` → `dimensions` → `matching` → `tables`

Blendy ti apre la porta e ti guida dentro, passa dietro al bancone, ti mostra la sala dove
nessuno parla, si appoggia al bancone ad ascoltarti, apre l'ologramma delle sette
dimensioni, lega due profili con un filo di luce, e infine allarga il braccio sulla sala
piena. Copy e CTA sono in `index.html`, una voce per scena.

## 3. Come è costruito

Architettura a **ripresa continua** (una delle due possibili; l'altra, "tuffo + raccordo
aereo", è stata scartata perché inverte la direzione della camera a ogni giunzione e in un
ambiente realistico si legge come un salto all'indietro).

Ogni clip parte dall'**ultimo frame reale** del clip precedente, non dallo still di
riferimento. È questo che rende le giunture invisibili. Il motore
(`assets/scrub-engine.js`, vanilla JS, zero dipendenze) carica ogni clip come **Blob** —
quindi non serve che l'host supporti le richieste byte-range — e ne pilota il
`currentTime` in base allo scroll, con un crossfade di 0.12 sulle giunture.

Dettagli su encoding, logo e override del tema: **[README.md](README.md)**. Leggilo prima
di toccare i video o il CSS del chrome: contiene i due vincoli che rompono lo scrub.

## 4. Decisioni prese, e perché

- **Blendy nelle scene, non in overlay.** Costa più re-roll ma è quello che rende il sito
  suo. La coerenza del personaggio regge perché ogni clip continua il frame precedente.
- **Ordine narrativo piegato alla geografia.** In origine il problema veniva prima di
  Blendy. Il primo clip però finisce davanti al bancone, quindi la scena del bar è stata
  spostata al secondo posto — combacia comunque con l'ordine della v1 (hero → Blendy →
  problema).
- **Solo italiano.** Le altre quattro lingue si aggiungono senza rigenerare niente: nei
  video non c'è testo.
- **Solo desktop.** Lo scroll-scrub è nativamente desktop. Il motore è comunque robusto su
  telefono (poster, priming iOS, safe-area) ma senza encode dedicati: degrada, non si
  rompe.
- **Niente `vercel.json`.** Un rewrite che porta la v2 sulla radice renderebbe la preview
  più realistica, ma al merge farebbe passare la produzione alla v2 da sola. Il passaggio
  deve restare una decisione esplicita.

## 5. Cosa è stato verificato, e come

- **Giunture**: confrontate numericamente (RMS sui frame estratti dai clip pubblicati) e a
  vista. Tutte frame-locked, peggiore 23.9 — differenze di micro-zoom, nessun salto di
  contenuto. Un valore sopra ~40 significherebbe giuntura rotta.
- **Motore, sull'URL pubblico**: 7 sezioni che si attivano una alla volta, ogni clip
  `readyState 4` e seekable a 8s, `currentTime ≈ 4.0` a metà di ogni scena, zero errori
  console, nessun poster rotto.
- **Contrasti**: tutti sopra WCAG AA, misurati risolvendo i colori col canvas del browser.
  Un probe a regex qui sbaglia — non legge `color-mix()` né i gradienti e restituisce
  numeri falsi.
- **Encoding**: scelto misurando costo di seek e SSIM, non a occhio. Tabella nel README.
- **Produzione**: ricontrollata dopo ogni push. Serve ancora la v1.

## 6. Problemi noti, aperti

1. **Scena 6 "Il match": primo piano sulle mani.** L'inquadratura è goffa. Un tentativo di
   rifarla più larga è riuscito nella composizione ma **ha rotto entrambe le giunture**
   (RMS 78): chiedendo "camera larga" il modello ignora il frame di partenza. Il clip
   attuale è quello cucito. Sistemarla davvero richiede di rigenerare a catena da
   `matching` in poi — 2 clip, ~144 crediti.
2. **Deriva di palette.** I primi due clip sono caldi come la v1; dal terzo le pareti
   virano su un blu più acceso. È coerente al suo interno, ma l'ingresso è più caldo del
   resto. Riallineare = rigenerare 5 clip, ~360 crediti.
3. **Manca il set Open Graph / Twitter.** I link condivisi non mostrano anteprima. In v1
   c'è `assets/og-image.jpg` (1200×630) già pronto da riusare.
4. **Mancano `robots.txt` e `sitemap.xml`** in questa cartella: se sostituisce il sito
   attuale vanno ripresi dalla root della v1.
5. **`index.html` ha ancora `noindex, nofollow`.** Corretto per una preview, da togliere al
   go-live.

## 7. Per andare online

Mergiare `V2-anim` in `main` **non** cambia `blendin.piirz.eu`: la v2 finirebbe in
produzione ma sotto `/sito-v2/`, con la radice ancora sulla v1. Il passaggio vero è una
mossa separata — spostare il contenuto di `sito-v2/` sulla radice del repo, oppure
aggiungere un rewrite. Prima di farlo, chiudere i punti 3, 4 e 5 qui sopra.

Nota sulla protezione: per la review è stata **disattivata la Vercel Deployment
Protection** sul progetto (era `all_except_custom_domains`), così il team può aprire la
preview senza account. Vale la pena ripristinarla dopo la review; il comando è nella
cronologia della sessione, e l'impostazione sta in Vercel → Project → Deployment
Protection.

## 8. Rigenerare o modificare le scene

Pipeline **Higgsfield** (workspace `Private`). I master 1080p e gli still 2k sono in
`_src/` — non finiscono online (esclusi via `.vercelignore`) e **non vanno sovrascritti**:
sono la sorgente per ogni nuova passata di encoding.

| | modello | costo |
|---|---|---|
| Still di scena | `nano_banana_2` | ~2 crediti |
| Clip 8s 1080p | `seedance_2_0` | **~72 crediti** |

Regole non negoziabili se rigeneri un clip:

- **Solo `--start-image`.** Passare anche un `--image` con lo still della scena
  sovrascrive il frame di partenza e rompe la giuntura (misurato: RMS 65 contro ~0).
- **La catena è sequenziale.** Rigenerare il clip *n* invalida tutti quelli dopo, perché
  ognuno parte dall'ultimo frame del precedente. Unica eccezione: bloccare anche
  `--end-image` sull'ultimo frame attuale del clip, così le giunture a monte e a valle
  reggono e si rifà un solo clip.
- **Il filtro NSFW colpisce le scene di interni**, in particolare `wrongroom`. Si sblocca
  togliendo parole come "dead", "lifeless", "fully dressed", oppure ritentando: è
  parzialmente non deterministico.
- **Guarda l'ultimo frame prima di concatenare.** Deve sembrare un fotogramma di una
  carrellata in avanti calma. Se non lo è, rifai quel clip: un frame di handoff sbagliato
  avvelena tutti i successivi.

Crediti residui al momento dell'handoff: ~663.
