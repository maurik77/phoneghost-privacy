# phoneghost-privacy — il sito pubblico di Nomira

Questo repository **non contiene un'app**: contiene le pagine che le schede
App Store e Play Console linkano, pubblicate da GitHub Pages su

> **https://maurik77.github.io/phoneghost-privacy/**

Sono due, ed entrambe sono viste da chi valuta l'app:

| Pagina | URL | A cosa serve |
|---|---|---|
| Privacy policy | `/` | **URL obbligatorio** su entrambi gli store, e usato anche come **URL di supporto** |
| Manuale utente | `/manual/<lingua>/` | Documentazione d'uso, sei lingue, linkata dal footer della privacy |

Il codice dell'app sta in un **altro repository**,
[`maurik77/phoneghost`](https://github.com/maurik77/phoneghost), ed è da lì che
viene quasi tutto ciò che trovi qui. Questo file esiste per dire **da dove** —
perché senza, il contenuto di un repository diverge da quello dell'altro senza
che nessuno se ne accorga. È già successo: vedi «Deriva nota» in fondo.

---

## Perché il repository si chiama ancora `phoneghost`

Il prodotto si chiamava PhoneGhost; dal 2026-09-06 il marchio è **Nomira**
(rifiuto Apple 2.3.8, vedi
[ADR-0016](https://github.com/maurik77/phoneghost/blob/master/docs/adr/0016-nome-generico-sul-dispositivo-marchio-sullo-store.md)).
Il **testo** delle pagine è stato rinominato — dicono «Nomira» ovunque — ma
**l'URL no, ed è una decisione deliberata**, non una dimenticanza:

- La Guideline 2.3.8 confronta il nome sullo Store con quello sul dispositivo.
  Un URL non è nessuno dei due: il revisore lo apre per leggere cosa c'è
  scritto dentro, e dentro c'è «Nomira».
- Stessa logica del Bundle Identifier `it.phoneghost.app`, che Apple ci ha
  detto **esplicitamente** di non toccare, e delle directory e classi nel repo
  dell'app: il marchio è Nomira, gli identificatori restano `phoneghost`.
- Rinominare costerebbe l'aggiornamento del privacy URL **e** del support URL
  su due store — un support URL che dà 404 è di per sé un motivo di rifiuto
  (Guideline 1.5) — e ci farebbe dipendere dai redirect delle Pages di
  progetto dopo la rinomina del repo, che non sono garantiti.

Se un giorno si vuole togliere la stonatura, il momento è **prima** della 1.0
pubblica e **fuori** da una review in corso. Non oggi.

---

## Da dove viene ogni file

Nulla in questo repository è generato: è tutto HTML scritto a mano e immagini
copiate. Non esiste uno script, non esiste un workflow. La colonna «Sorgente»
dice qual è la copia autorevole quando le due divergono.

| Qui | Sorgente nel repo dell'app | Rapporto |
|---|---|---|
| `index.html` | `docs/privacy/index.html` | **Copia esatta.** Le sei lingue stanno tutte in questo unico file. Al 2026-09-09 i due file sono identici byte per byte. |
| `manual/<lingua>/index.html` | `docs/funzionalita.md` (solo italiano) | **Derivazione a mano.** Il markdown italiano è la sorgente del contenuto; le sei pagine HTML — italiano compreso — sono state scritte a partire da lì e **non esistono nel repo dell'app**. Questo repository è la loro unica copia. |
| `manual/index.html` | — | Solo di qui: la pagina di scelta della lingua. |
| `manual/screenshots/…` | `docs/screenshots/…` | **Copia esatta, ma con i nomi delle cartelle cambiati.** Vedi sotto. |
| `favicon.png` | `app/ios/Runner/Assets.xcassets/AppIcon.appiconset/Icon-App-60x60@3x.png` | Copia esatta dell'icona iOS 180×180. Quell'icona è **generata** da `tool/generate_icons.py`: se il marchio cambia, si rigenera là e si ricopia qui. |

### La convenzione dei nomi delle cartelle degli screenshot

È l'unica trasformazione non ovvia di tutto il repository, e non era scritta da
nessuna parte:

```
docs/screenshots/android-it/   →   manual/screenshots/android-it/    (prefisso mantenuto)
docs/screenshots/ios-it/       →   manual/screenshots/it/            (prefisso ios- RIMOSSO)
```

Sei lingue per due piattaforme, 28 schermate ciascuna: **336 file**. Le cartelle
`ios-*-1284` e `ios-it-ipad` del repo dell'app **non** vanno copiate — servono
alle schede store, non al manuale.

Chi copia a mano dimenticando che `ios-` cade produce una cartella che il
manuale non trova, e il risultato è una pagina con le immagini rotte in una
lingua sola. **È la causa più probabile della deriva descritta qui sotto.**

---

## Come si aggiorna e si pubblica

GitHub Pages serve il branch **`main`** dalla radice. Non c'è build, non c'è
Jekyll da attendere in modo significativo: si committa e si pusha, e la pagina
è online in un minuto.

```bash
# dal repo dell'app, per riallineare la privacy policy:
cp docs/privacy/index.html ../phoneghost-privacy/index.html

# per riallineare gli screenshot (attenzione al prefisso ios-):
for l in it en es fr de pt; do
  cp docs/screenshots/android-$l/*.png ../phoneghost-privacy/manual/screenshots/android-$l/
  cp docs/screenshots/ios-$l/*.png     ../phoneghost-privacy/manual/screenshots/$l/
done

cd ../phoneghost-privacy && git add -A && git commit && git push
```

**Il manuale non si rigenera così**: le sei pagine HTML sono scritte a mano. Se
`docs/funzionalita.md` cambia, il contenuto va riportato in sei file, e cinque
di questi sono traduzioni.

### Quando questo repository conta per una review

Sempre, ma in particolare:

- **Prima di inviare una build**, non dopo: è un URL che il revisore apre, e la
  privacy policy deve già descrivere la versione che sta valutando.
- **Se il nome dell'app cambia**, perché queste pagine lo dichiarano — è
  esattamente ciò che la 2.3.8 confronta.
- **Se cambia dove finiscono i dati.** Oggi la policy afferma, in tutte e sei
  le lingue, che «nessun dato viene trasmesso a server esterni, cloud o servizi
  di terze parti». La messaggistica cifrata in progetto introduce un relay e
  una subscription: quella frase diventerà **falsa**, e va riscritta **prima**
  della build che la contraddice. Il dettaglio è nel repo dell'app, §11 di
  `docs/superpowers/specs/2026-09-07-phoneghost-messaging-design.md`.

---

## Deriva nota (2026-09-09)

**13 screenshot iOS pubblicati qui sono più vecchi di quelli nel repo
dell'app.** Sono le versioni del 2026-09-01; il repo dell'app le ha aggiornate
il giorno dopo con il commit `dc9f6f0`, «shots 09/12 aggiornati per tutte e 6
le lingue (build corrente post-fix `AdaptiveScaffold` e `helperMaxLines`)».

I file sono:

- `manual/screenshots/{it,en,es,fr,de,pt}/09-nuovo-contatto-compilato.png`
- `manual/screenshots/{it,en,es,fr,de,pt}/12-impostazioni.png`
- `manual/screenshots/pt/06-codice-amministrativo-impostato.png`

Conseguenza: il manuale mostra, in tutte e sei le lingue, schermate iOS di una
build con un difetto d'interfaccia che è stato corretto. I 168 screenshot
Android combaciano tutti, e la privacy policy è allineata.

**Non è ancora stato corretto** — è una scelta, non una svista: al momento
della scoperta la build 3 era in coda alla App Review, e si è preferito non
toccare un sito che il revisore poteva aprire. Da fare quando la review è
chiusa, con il comando di riallineamento qui sopra.
