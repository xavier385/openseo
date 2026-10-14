# Installare OpenSEO su Cloudflare (Windows)

Guida in italiano per questa copia di [every-app/open-seo](https://github.com/every-app/open-seo) (licenza MIT, vedi `LICENSE`). Il codice è identico all'originale; questo file è l'unica aggiunta. La guida ufficiale completa è in `docs/SELF_HOSTING_CLOUDFLARE.md`.

Tempo: circa 15 minuti. Costo: zero con il credito di prova di DataForSEO.

## Prima di cominciare

1. **DataForSEO.** Crea un account su https://app.dataforseo.com/api-access: alla registrazione ricevi $1 di credito di prova, senza scadenza. Premi "Send by email" e copia le credenziali "Base64" (la stringa più lunga).
2. **R2 su Cloudflare.** Nel pannello Cloudflare apri R2 una volta e attivalo. Cloudflare chiede un metodo di pagamento anche per il piano gratuito; l'uso di OpenSEO resta nei limiti gratuiti.
3. **Node.js 22.6 o più recente** (`node -v` per controllare) e Git.

## Installazione

Apri PowerShell ed esegui un comando alla volta:

```powershell
git clone https://github.com/xavier385/openseo.git
cd openseo
corepack enable
pnpm install
```

Login a Cloudflare, una volta sola:

```powershell
pnpm alchemy login
```

Si apre il browser. Alla domanda "Customize OAuth scopes?" rispondi **yes** e abilita anche **access:write**, poi:

```powershell
pnpm alchemy cloudflare bootstrap
```

Crea il file di configurazione:

```powershell
Copy-Item deploy/.env.selfhost.example .env.selfhost
notepad .env.selfhost
```

Compila due righe e salva:

```text
DATAFORSEO_API_KEY=la-stringa-Base64-di-DataForSEO
ACCESS_ALLOWED_EMAILS=xavier@lcplocalizations.com
```

Se non vuoi inviare la telemetria anonima a OpenSEO, togli il `#` davanti a `OPENSEO_TELEMETRY_DISABLED=1`.

Il file `.env.selfhost` resta solo sul tuo PC: è escluso da Git e non va mai caricato nel repo.

Installa:

```powershell
pnpm deploy:selfhost --yes
```

Alla fine compare l'indirizzo dell'app, del tipo `https://open-seo-selfhost.<tuo-sottodominio>.workers.dev`. Aprilo, entra con il codice che Cloudflare Access ti manda per email e OpenSEO si carica.

## Se qualcosa non va

- Login rifiutato: controlla `ACCESS_ALLOWED_EMAILS` in `.env.selfhost` e rilancia `pnpm deploy:selfhost --yes`.
- Stato dell'app: `https://<indirizzo-dell-app>/api/health`.
- Errori del server: `pnpm exec wrangler tail`.

## Aggiornare

```powershell
git pull
pnpm install
pnpm deploy:selfhost --yes
```

## Collegarlo a Claude

Per usarlo nelle sessioni con Claude tramite il server MCP, segui `docs/SELF_HOSTING_CLOUDFLARE_OPERATIONS.md`.
