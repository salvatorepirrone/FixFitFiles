# Contesto: FixFitFiles come progetto a sé

Riepilogo dello stato del progetto, pensato per essere incollato come primo messaggio in una chat di Claude Code. Ciò che non è stato verificato è segnato come tale. Aggiornato il 2026-11-23.

## Cos'è
Un editor web dei **length di nuoto in vasca** nei file FIT di Garmin: permette di dividere e unire i length, cambiare lo stile, correggere incoerenze, poi scarica un `.fit` corretto da ricaricare su Garmin Connect (nome del file: `<originale>_corretto.fit`).

## Dove sta
- Repo GitHub pubblico: https://github.com/salvatorepirrone/FixFitFiles (ramo `main`).
- Sito: https://fixfitfiles.netlify.app/ . Netlify pubblica da `main` in automatico (verificato: dopo un push la nuova versione è online in pochi secondi). Le impostazioni di Netlify non sono state viste.
- File nella radice, nessuna build né dipendenze: `index.html` (editor con intestazione e menu), `fit-editor.html` (stesso editor senza intestazione), `help.html`, `faq.html`, `logo.svg`. Tutto in un solo file HTML con CSS e JavaScript inline, interfaccia in italiano.
- Fine riga: nel repository i file HTML sono memorizzati con LF (verificato con `git ls-files --eol`); sul PC Windows possono comparire come CRLF per la conversione automatica di Git. Non cambiare le fine riga a mano.
- Commit recenti: `2a4db6c` (marchio da VascaLab a FixFitFiles), `74cc81f` (index.html), `31af666` (supporto `.zip` e `.gz`).

## Cosa fa il codice (letto solo in parte)
Funzioni principali in JavaScript: `readFit`, `loadSwimActivity`, `repairSwimActivity` (ripara incoerenze e vasche senza lap), `recompute`, `handleFile`, scrittura del FIT con ricalcolo del CRC. Il codice non è stato letto per intero né eseguito in un browser.

## Correzione del 2026-11-23: statistiche dopo unione/divisione
`recompute` aggiornava solo tempo, distanza, passo medio e bracciate di lap e sessione. Ora ricalcola anche passo avanzato (`enhanced_avg_speed`), velocità massima, cadenza media/massima, SWOLF, bracciate per vasca, distanza per bracciata, tempo attivo, FC media/max/min del lap (dai record), inizio/fine del lap e calorie; i length mantengono `timestamp` e calorie coerenti. I numeri dei campi sono verificati sul profilo FIT ufficiale; **non provato con un file FIT reale**, solo con un'attività sintetica.

## Novità recente
Il caricamento accetta `.fit`, `.zip` (l'export originale di Garmin Connect, si prende il primo `.fit`) e `.gz`. Il tipo si riconosce dai primi byte e si decomprime nel browser con `DecompressionStream`. **Non provato con file reali**: va verificato.

## Cose da sapere
1. **Marchio incoerente:** in `index.html` l'intestazione mostra ancora "VascaLab" (testo e `alt` del logo), nonostante il commit sul cambio di marchio. Da sistemare.
2. **Copia gemella:** lo stesso editor esiste in un altro progetto personale (`mioallenamento`, file `editor.html`), con la grafica di quel sito. Le due copie divergono. Non modificare l'altro progetto senza una richiesta esplicita.
3. `help.html` e `faq.html` non citano il formato dei file: dopo la novità dei file compressi vanno aggiornate.
4. Il repo è pubblico: niente dati personali, token o file FIT reali.

## Lezioni dai dati reali Garmin
- Il numero di lap può differire tra il FIT e l'API di Garmin.
- Stile e flag "tecnica" dei lap, come li mostra Garmin Connect, includono correzioni fatte dopo il caricamento che il FIT originale non contiene.
- Nel nuoto il passo si esprime per 100 m, non per km.

## Convenzioni di lavoro
- Messaggi di commit in italiano.
- Prima di un push su `main` (che pubblica subito il sito) chiedere conferma, salvo richiesta esplicita.

## Da decidere (nessuna è già decisa)
- Obiettivo: solo strumento personale o per altri utenti? Dominio e nome definitivi?
- Struttura: separare codice, stile e script in file distinti con test, oppure restare a file unici?
- Test automatici su file FIT di prova (anche `.zip` e `.gz`) e come mantenere l'allineamento con la copia nell'altro progetto.
- Aggiornare guida e FAQ, correggere il marchio, aggiungere un'informativa sulla privacy (verificare prima nel codice che i file restino nel browser).
