# 📘 x-k8s

## 📌 Descrizione Generale

Questo script Bash permette di gestire facilmente **multi-cluster Kubernetes** su ambienti con proxy aziendale. Consente di:

- Caricare automaticamente i file kubeconfig da una directory
- Selezionare namespace da **tutti i cluster disponibili**
- Impostare `KUBECONFIG` e `K8S_NS` in modo semplice e interattivo
- Fornire comandi wrapper come `k` (kubectl con namespace) e `kns` (cambio namespace)

Ideale per chi lavora con **cluster Rancher** e **proxy aziendale**.

---

## 🧩 Funzionalità Principali

| Funzione | Descrizione |
|----------|-------------|
| `setup_k8s_environment` | Configura l'ambiente Kubernetes selezionando un namespace e il relativo kubeconfig |
| `k` | Esecuzione di `kubectl` con il namespace attivo |
| `kns` | Cambia namespace (interattivo o con argomento) |
| `list` | Elenca tutti i namespace disponibili (dalla cache) |
| `reload` | Ricostruisce la cache dei namespace chiamando le API |
| `help` | Mostra la guida rapida |

---

## 🚀 Come Usarlo

### Alias

Impostare alias in .bashrc 

```bash
alias k8s='source /path-script/x-k8s'
```

### Comandi supportati

#### a) Imposta namespace direttamente

```bash
k8s my-namespace
```

Imposta `KUBECONFIG` sul file corretto e `K8S_NS=my-namespace`.

#### b) Selezione interattiva

```bash
k8s
```

Mostra una lista di tutti i namespace (ordinati alfabeticamente) e chiede quale usare.

#### c) Cambia namespace (wrapper)

```bash
k8s kns
```

Mostra la lista interattiva e cambia namespace.

Oppure direttamente:

```bash
k8s kns my-namespace
```

#### d) Ricostruisci cache

```bash
k8s reload
```

Chiama le API di ogni cluster per leggere i namespace e salva la cache in `~/.kube/ns_cache`.

#### e) Elenca namespace in cache

```bash
k8s list
```

Mostra tutti i namespace con il relativo file kubeconfig.

---

## 📁 Struttura della Cache

La cache è un file di testo (delimitato da `|`):

```
namespace1|kubeconfig-prod.yaml
namespace2|kubeconfig-svil.yaml
```

**Posizione**: `~/.kube/ns_cache`

---

## 📌 Variabili d'Ambiente

| Variabile | Descrizione |
|-----------|-------------|
| `KUBECONFIG` | Percorso del file kubeconfig attivo |
| `K8S_NS`    | Namespace attivo |
| `K8S_ENV`   | Nome del file kubeconfig (es. `kubeconfig-prod.yaml`) |
| `HTTP_PROXY` | Impostato a `http://proxy...:3128` |
| `HTTPS_PROXY` | Impostato a `http://proxy...:3128` |

---

## ⚠️ Note e Dipendenze

### Dipendenze necessarie:
- `kubectl` installato e nel PATH
- `jq` per il parsing JSON
- `curl` per le chiamate API Rancher
- `yq` (opzionale) per estrarre il token da kubeconfig

### Funzionamento con Rancher:
Lo script è ottimizzato per cluster Rancher, usando l'API `/v3/clusters/<cluster-id>/namespaces`.

### Gestione proxy:
Le variabili proxy vengono impostate all'inizio dello script.

---

## 🐛 Errori Comuni e Soluzioni

| Errore | Causa | Soluzione |
|--------|-------|-----------|
| `❌ Directory /mnt/c/Users/.../.kube non trovata` | Utente `root` e `USERNAME` non valorizzata | Imposta `USERNAME` manualmente o modifica la logica per `$HOME` |
| `❌ Nessun namespace trovato` | Token scaduto o cluster irraggiungibile | Esegui `k8s reload` per aggiornare |
| `curl fallito` | Proxy non raggiungibile | Verifica le variabili `HTTP_PROXY` |
| `KUBECONFIG non impostato` | Selezione fallita | Esegui `k8s` senza argomenti |

---

## 📋 Riepilogo Comandi per l'Utente

| Comando | Azione |
|---------|--------|
| `k8s` | Configura ambiente interattivamente |
| `k8s my-ns` | Imposta `my-ns` |
| `k get pods` | `kubectl -n $K8S_NS get pods` |
| `kns` | Cambia namespace interattivo |
| `kns my-ns` | Cambia namespace a `my-ns` |
| `k8s list` | Mostra namespace in cache |
| `k8s reload` | Ricostruisce cache |
| `k8s help` | Mostra questa guida |
