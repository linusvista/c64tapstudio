# C64 TAP Studio

**Crea un `.tap` per Commodore 64, registralo su cassetta vera e verifica la registrazione: tutto in un solo programma.**

*Create a Commodore 64 `.tap` image (turbo loader or standard KERNAL), write it to WAV or play it out of your sound card to record a real cassette, and verify the recording against the source TAP. Single-file Python/Tkinter tool, Italian interface.*

Autore: (c) MMXXVI Linusvista

---

## Cosa fa

| Scheda / finestra | Funzione |
|---|---|
| **1 · Crea TAP** | Genera un `.tap` da un PRG: bootstrap BASIC a velocità standard + turbo loader 6502, schermata di caricamento con musica SID e barra di avanzamento, PRG diviso in blocchi con checksum. In alternativa modalità **KERNAL standard** (senza turbo), più tollerante sui Datasette poco allineati. Controlli di idoneità, mappa di memoria, scelta automatica degli indirizzi, preset per emulatore o Datasette. |
| **Finestra «Registrazione su cassetta»** | Scrive il TAP in un **WAV** oppure lo **suona sull'uscita audio** del PC per registrare la cassetta. Usa il TAP appena creato (senza doverlo salvare) o un file `.tap` qualsiasi. Preset audio, tono pilota di calibrazione, nastro di prova per la polarità. |
| **2 · WAV → TAP** | Ricostruisce un TAP da una registrazione WAV. |
| **3 · Verifica WAV vs TAP** | Confronta una registrazione (file WAV o ingresso audio del PC) con il TAP sorgente e produce un rapporto con verdetto, errori e grafico. |
| **Finestra «Registrazione dall'ingresso audio»** | Registra il Datasette tramite l'ingresso del PC e passa la registrazione alla verifica. |

L'interfaccia è ottimizzata per schermi **1280x720**. Non servono assembler esterni: il programma contiene un mini-assembler 6502.

## Requisiti

- Python 3.8 o superiore
- `tkinter` (interfaccia grafica)
- `numpy` (funzioni audio)
- `sounddevice` + PortAudio (solo per suonare e registrare in tempo reale)

```bash
pip install numpy sounddevice
# Debian / Ubuntu
sudo apt install python3-tk libportaudio2
```

Senza `numpy` funziona solo la creazione del TAP; senza `sounddevice` restano disponibili tutte le funzioni su file (TAP, WAV, verifica).

## Avvio

```bash
python3 c64tap_studio.py            # interfaccia grafica
python3 c64tap_studio.py --selftest # prova delle funzioni audio, senza interfaccia
```

## Uso dell'interfaccia grafica

### 1. Creare il TAP
1. Scegli il **PRG del gioco** e il **file .tap di destinazione**.
2. Scegli il **preset** (emulatore o Datasette) e la modalità: *Turbo loader* oppure *KERNAL standard*.
3. In modalità turbo scegli la musica (file SID, musica predefinita, nessuna), titolo e descrizione scorrevole. Se usi un SID puoi impostare la rilocazione (automatica, nessuna, manuale) e il pulsante **Proponi** calcola l'indirizzo ideale.
4. Controlla i messaggi nel riquadro **Controlli** e la **mappa di memoria**: errori in rosso bloccano la generazione, gli avvisi no.
5. **Genera .tap**.

Le temporizzazioni dei piloti e delle pause sono in **Temporizzazioni avanzate…** (i preset le impostano da soli: modificale solo se il caricamento da Datasette si blocca).

### 2. Registrare su cassetta
Premi **Registra su cassetta (WAV / audio)…** (nella scheda 1 o nella barra in basso).

- **Salva come WAV…** crea il file da registrare con un altro dispositivo.
- **Riproduci sull'uscita audio** suona il TAP direttamente: collega l'uscita del PC all'ingresso del registratore e registra.
- **Tono pilota di calibrazione**: regola il livello di registrazione senza saturare.
- **Nastro di prova polarità**: sul C64 `LOAD`, `PLAY`, `RUN`. Se compare *INVERSO* la tua catena inverte il segnale e serve «Inverti polarità» (salvabile in un preset).

Consigli: disattiva Dolby, riduzione rumore e AGC/limiter del registratore; evita la saturazione; prova prima la modalità KERNAL standard se il turbo dà errori.

### 3. Verificare
1. Nella scheda **Verifica** indica il TAP sorgente (o «Usa il TAP di Crea TAP»).
2. Sorgente: un file WAV oppure **Apri finestra di registrazione…** per registrare dal Datasette; con **Stop e verifica** il confronto parte da solo.
3. **Verifica coerenza**: al termine compare un messaggio e si apre il rapporto completo (salvabile).

## Riga di comando

```bash
python3 c64tap_studio.py gioco.prg -o gioco.tap
python3 c64tap_studio.py gioco.prg -o gioco.tap --wav gioco.wav --wav-sr 48000 --wav-shape seno
python3 c64tap_studio.py gioco.prg --no-turbo -o gioco_kernal.tap
python3 c64tap_studio.py gioco.prg --check          # solo controlli
```

Opzioni principali:

| Opzione | Significato |
|---|---|
| `-o`, `--output` | file `.tap` di uscita (default: nome del PRG + `.tap`) |
| `--preset` | preset di riproduzione |
| `--mode turbo\|kernal`, `--no-turbo` | modalità turbo o KERNAL standard |
| `--speed slow\|normal\|fast`, `--chunk N` | velocità e byte per blocco turbo (16-256) |
| `--name`, `--title`, `--subtitle` | nome sul nastro, titolo, descrizione |
| `--music sid\|default\|none`, `--sid FILE`, `--song N`, `--sid-reloc none\|auto\|ADDR` | musica di caricamento |
| `--run ADDR`, `--loader-addr ADDR` | indirizzo di avvio e del loader |
| `--check` | esegue solo i controlli |
| `--wav FILE` | scrive anche il WAV del TAP |
| `--wav-sr`, `--wav-shape quadra\|seno\|triangolare`, `--wav-volume`, `--wav-invert`, `--wav-channels` | parametri del WAV |

`python3 c64tap_studio.py --help` mostra l'elenco completo.

## File di configurazione

- `~/.config/c64tap_gui.json`: ultimi valori della scheda Crea TAP
- `~/.tap2wav_gui.json`: preset personalizzati e ultime impostazioni audio

## Test

`--selftest` genera segnali sintetici (con rumore, variazioni di velocità, polarità invertita) e controlla che decodifica e verifica funzionino.

## Origine del progetto

Questo programma riunisce due strumenti sviluppati separatamente: `c64tap.py` (creazione del TAP) e `tap2wav` (TAP ⇄ WAV e verifica).

## Licenza

Da scegliere prima della pubblicazione (ad esempio MIT). Aggiungi un file `LICENSE` al repository.
