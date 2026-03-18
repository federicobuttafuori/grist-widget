# Debug learnings: contorno in stampa (bianco → nero) e regressione outline

## Problema originale

- **Sintomo**: contorno impostato **bianco** (o chiaro) su testo/immagini; **in stampa** risultava **nero** o comunque errato, mentre a schermo poteva sembrare accettabile.
- **Contesto**: app etichette in `index.html`, contorno gestito da proprietà elemento (`textOutlineEnabled`, `textOutlineWidth`, `textOutlineColor`, contorno secondario opzionale).

## Cosa causava il bug (causa vera)

1. **Pipeline di stampa del browser / driver**: `text-shadow` e `filter: drop-shadow(...)` su contenuti stampati vengono spesso **rasterizzati in modo diverso** rispetto allo schermo. I colori **molto chiari / bianchi** in quelle ombre possono essere **interpretati male** (es. finire come inchiostro nero o “alone” sporco), soprattutto su output **monocromatico** (termica / PDF B/N).
2. **Non era un semplice “colore sbagliato nel CSS”**: cambiare leggermente l’hex del bianco non risolveva in modo affidabile perché il problema era nel **modello di compositing in stampa**, non nel valore `#ffffff` in sé.

## Tentativi che **non** hanno risolto (o hanno peggiorato)

| Approccio | Perché non è bastato / effetto collaterale |
|-----------|---------------------------------------------|
| Sostituire `#fff` / `#ffffff` con un bianco “quasi bianco” (es. `#fefefe`) **solo in stampa** nelle stringhe di shadow/drop-shadow | Non affronta la rasterizzazione; spesso **nessun miglioramento** visibile in stampa. |
| In stampa, **omettere** i contorni “quasi bianchi” (non applicare shadow se colore chiaro) | Evita il nero “fantasma” ma **cancella** il contorno bianco desiderato → non accettabile se l’utente vuole quell’effetto. |
| Passare a un **unico** filtro SVG fisso (`id="outline"`) + classe `.outlined` con `radius` e colore **fissi** | Stampa più stabile ma **ignora** spessore e colore dalle proprietà → regressioni (contorno sempre uguale); su icone può apparire un **alone nero grosso / a gradini** rispetto all’intento. |

## Fix finale (soluzione attuale)

1. **Host SVG** in pagina: `<defs id="outline-filters-defs">` (inizio `body`).
2. Per ogni combinazione **spessore + colore** (e doppio contorno se attivo), **`ensureOutlineSvgFilter(el)`** crea un `<filter>` con:
   - `feMorphology` (dilate) su `SourceAlpha` → contorno geometrico pulito attorno alla forma (testo o alpha immagine),
   - `feFlood` + `feComposite` per il **colore scelto**,
   - con **due anelli** se contorno secondario abilitato.
3. Applicazione **`filter: url(#id)` inline** su contenitore testo + span interni (regex) e su `<img>`, così **rispettano** `textOutlineWidth` / `textOutlineColor` (e secondario).

In sintesi: **non** affidarsi a shadow/drop-shadow per il contorno in stampa; usare **SVG filter** parametrico legato alle props.

## Cosa imparare per agenti futuri

- **Stampa ≠ schermo**: tecniche che “funzionano sempre” in CSS screen (`text-shadow`, catene di `drop-shadow`) possono **fallire in print** o con driver termici; verificare sempre su **Stampa → Anteprima / PDF**.
- **Workaround colore** (#fefefe, ecc.) su effetti non solidi spesso **maschera** il problema invece di risolverlo.
- **Filtro SVG “unico”** con parametri fissi è facile da introdurre ma **rompe** il binding con le proprietà UI → ogni fix deve **preservare** width/color (o documentare esplicitamente la semplificazione).
- **Pulizia**: dopo la soluzione definitiva, **rimuovere** helper e path morti (vecchie funzioni shadow/drop-shadow non più chiamate) per evitare confusione.

## File coinvolti

- Implementazione: [`index.html`](../index.html) (`outline-filters-defs`, `ensureOutlineSvgFilter`, `applySvgOutlineToTextContainer`, `applySvgOutlineToImage`).
