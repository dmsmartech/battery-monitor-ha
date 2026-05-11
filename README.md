# 🔋 Battery Monitor per Home Assistant

Questo progetto per Home Assistant permette di creare un’automazione che, una volta configurata, controlla ogni giorno, all’orario indicato, la carica di tutti i dispositivi smart a batteria presenti nel sistema.
I dispositivi vengono rilevati automaticamente tramite la classe `device_class: battery` ed esclude quelli in stato `unknown` e `unavailable`.

Quando uno o più dispositivi scende sotto la soglia impostata, viene inviata una notifica ai destinatari configurati con l’indicazione del numero dei dispositivi sotto quella soglia.

Toccando la notifica, l’app Companion di HomeAssistant si apre direttamente sulla plancia configurata.
Funziona sia su iOS che su Android.

---

## Prima di iniziare

Se vuoi usare la plancia **Manutenzione** inclusa di default in **Home Assistant 2026.5** o superiore, puoi ignorare il Passo 3.

Se invece preferisci creare una plancia personalizzata per visualizzare i tuoi dispositivi a batteria (Passo 3), è necessario avere **HACS** installato e l'integrazione **auto-entities** scaricata da HACS.

---

## Installazione — 3 passi

### Passo 1 — Importa il Progetto

1. Vai in **Impostazioni → Automazioni e Scenari** e clicca sulla tab **Progetti** (la voce a destra)
2. Clicca **Importa Progetto** (in basso a destra)
3. Incolla questo URL:
   ```
   https://raw.githubusercontent.com/dmsmartech/battery-monitor-ha/main/battery_monitor_blueprint.yaml
   ```
4. Clicca **Anteprima Progetto → Importa Progetto**

### Passo 2 — Crea l'automazione

1. Dopo l'importazione il progetto apparirà nella lista della sezione **Progetti**
2. Aprilo per creare una nuova automazione direttamente dal progetto. In alternativa puoi andare nella tab **Automazioni**, cliccare su **Crea automazione** e selezionare il progetto **Battery Monitor**
3. Configura le opzioni (vedi il paragrafo **Opzioni di configurazione** più in basso)
4. Salva: verrà creata una nuova automazione basata sul progetto Battery Monitor

### Passo 3 — Crea la plancia personalizzata (opzionale)

Questo passaggio è necessario solo se vuoi creare una plancia personalizzata dedicata alle batterie. Se preferisci usare la plancia **Manutenzione** di Home Assistant, salta questo passo e imposta `/maintenance` nel campo **Path della plancia batterie** al Passo 2.

**Prerequisiti:** HACS installato e integrazione **auto-entities** installata tramite HACS.

1. Vai in **Impostazioni → Plance → Aggiungi Plancia**
2. Nella finestra che appare seleziona **Nuova plancia da zero**
3. Compila i campi esattamente come indicato:

| Campo | Valore |
|---|---|
| Titolo | `Monitor Batterie` |
| Icona | `mdi:battery-alert` |
| URL | `battery-monitor` |

4. Clicca **Crea**
5. Apri la plancia appena creata e clicca sulla **matita** ✏️ in alto a destra per entrare in modalità modifica
6. Clicca sull'icona con i **3 puntini** della sezione vuota → seleziona **Modifica** → e nella finestra che si apre clicca sull'icona con i **3 puntini** in alto a destra → infine seleziona **Modifica in YAML**
7. Seleziona tutto il contenuto nell'area di testo e sostituiscilo con il contenuto del file `lovelace_dashboard.yaml` che trovi tra i file di questo progetto
8. Salva

---

## Opzioni di configurazione

### Soglia batteria scarica (%)
La percentuale sotto cui un dispositivo viene considerato scarico e incluso nella notifica. Default: **15%**. Modificabile con uno slider da 0% a 60%.

### Orario notifica giornaliera
A che ora ricevere la notifica. Default: **09:00**. La notifica viene inviata solo se ci sono effettivamente dispositivi sotto soglia — se tutto è carico non arriva nulla.

### Path della plancia batterie
Il percorso della plancia da aprire al tap sulla notifica. Default: **/maintenance**.

> **Nota per utenti iOS:** il tap sulla notifica apre direttamente la plancia se l'app è aperta o in background. Se l'app è completamente chiusa, iOS aprirà l'app sulla schermata principale — naviga manualmente alla plancia dalla barra laterale. È un limite di iOS e non dipende da Home Assistant.

### Destinatari notifica

#### Tutti i dispositivi Companion (consigliato)
Impostazione default. Usa `notify.notify`, l'alias universale di Home Assistant che raggiunge automaticamente **tutti i dispositivi** con l'app Companion collegati all'account. Non richiede nessuna configurazione aggiuntiva — funziona subito.

Ideale per uso personale o per famiglie con più telefoni sullo stesso account HA.

#### Dispositivi specifici (avanzato)
Permette di scegliere esattamente quali dispositivi ricevono la notifica. Utile se vuoi notificare solo alcune persone o se hai più account HA e vuoi controllo granulare.

**Come si configura:**
1. Seleziona **Dispositivi specifici** nel menu a tendina
2. Nel campo testo inserisci i nomi dei servizi separati da virgola, **senza** il prefisso `notify.`
   ```
   mobile_app_iphone_mario, mobile_app_pixel_luigi
   ```
3. Salva l'automazione

**Come trovo i nomi dei miei dispositivi?**
Vai in **Strumenti sviluppatore → Servizi**, cerca `notify.mobile_app_` — trovi la lista completa dei dispositivi registrati. Il nome da inserire è tutto quello che viene dopo `notify.`.

> **Nota:** se selezioni "Dispositivi specifici" ma lasci il campo vuoto, il sistema ricade automaticamente sul comportamento "Tutti" — quindi non si rompe nulla.

---

## Come funziona la plancia personalizzata

La plancia creata con il file `lovelace_dashboard.yaml` e l'integrazione **auto-entities**:

- Rileva automaticamente tutti i sensori, nuovi e vecchi, con `device_class: battery`
- Esclude gli stati `unknown` e `unavailable`
- Mostra tutti i dispositivi ordinati dal più scarico al più carico
- Aggiorna i colori automaticamente in base al livello: 🟢 carico → 🟡 sotto 30% → 🟠 sotto 15% → 🔴 sotto 5%
- Nuovi dispositivi compaiono automaticamente senza nessuna configurazione aggiuntiva

---

## Requisiti

- Home Assistant 2026.5 o superiore per usare la plancia **Manutenzione** predefinita
- Home Assistant 2023.4 o superiore per usare la plancia personalizzata + 
- App Companion installata sul telefono (per le notifiche)
- **HACS** e integrazione **auto-entities** (solo per la plancia personalizzata)
