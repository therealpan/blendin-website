# Questo repo non serve piu un sito

Dal 2026-07-29 la landing di BlendIn vive su **https://blendin.app**, dentro la webapp
Next.js `blendin-chat`. `blendin.piirz.eu` resta solo come indirizzo che rimanda.

`vercel.json` contiene tre regole di redirect, e l'ordine conta perche vince la prima che
corrisponde:

1. **`/`** ha una regola sua: `/:path*` non corrisponde al percorso vuoto, quindi senza
   questa la home continuerebbe a servire la v1.
2. **Le tre pagine legali italiane** esistono anche a destinazione, quindi mantengono il
   percorso: `/privacy.html` va su `/privacy.html`.
3. **Tutto il resto** va sulla home. Le dodici pagine legali in EN/FR/DE/ES e `llms.txt`
   su blendin.app non esistono: mandarle allo stesso percorso significherebbe mandarle
   su un 404.

I commenti stanno qui e non dentro `vercel.json`: quel file viene validato in modo
stretto e una chiave non prevista fa fallire il deployment senza log utili.

## Cosa resta nel repo

Il codice della v1 e la cartella `sito-v2/` sul branch `V2-anim`, che e la sorgente del
motore scroll-scrub e della sua documentazione. `sito-v2/README.md` spiega encoding dei
clip, soglie responsive e le trappole di stratificazione CSS: serve ancora, perche il
motore girato in produzione e lo stesso file.

Le versioni EN/FR/DE/ES delle pagine legali esistono solo qui: quando la landing tornera
multilingua andranno portate su blendin.app.
