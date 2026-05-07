# HashiCorp Vault — Secret Manager Integration

> **Premium Feature** — Requires a **Pro** or **Ultimate** license.
> [View Bruno pricing](https://www.usebruno.com/pricing)

This collection demonstrates how to use [HashiCorp Vault](https://www.vaultproject.io/) as a secret manager inside Bruno. Secrets fetched from Vault are available across all requests in this collection (see `echo-bru`) via the `$secrets` syntax.

---

## Prerequisites

- Bruno **Pro** or **Ultimate** license
- HashiCorp Vault installed locally (instructions below)

---

## Step 1 — Install & Start Vault

### Windows

Run in **PowerShell** or **Command Prompt** (requires [Chocolatey](https://chocolatey.org/)):

```powershell
choco install vault
vault server -dev
```

### macOS

Run in **Terminal**:

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/vault
vault server -dev
```

> After the server starts, the terminal will print a **Root Token** and confirm the server is listening at `http://127.0.0.1:8200`. Keep this terminal open — copy the Root Token, you will need it in the next steps.

---

## Step 2 — Create a Secret in Vault

1. Open `http://127.0.0.1:8200` in your browser.
2. Log in using the **Root Token** copied from the terminal.
3. Click **Secrets** in the left sidebar.
4. Click **Create secret**.
5. Fill in the form:
   - **Secret path** — a string name, e.g. `usebruno`
   - **Secret key** — e.g. `api-key`
   - **Secret value** — the sensitive value
6. Click **Save**.

---

## Step 3 — Add Vault as a Secret Provider in Bruno

1. Open Bruno and click the **Preferences** icon in the bottom-left corner.
2. Go to the **Secret Manager** tab.
3. Click **Add Secret Provider** — an **Edit Provider** dialog will open.
4. Fill in the fields **one by one** in this order:
   - **Name** — enter a label for this provider, e.g. `HashiCorp Vault`
   - **Secret Manager** — select **Vault Server** from the dropdown
   - **URL** — enter `http://127.0.0.1:8200/`
   - **Namespace** — leave empty for local dev (used for Vault Enterprise namespaces only)
   - **Auth Method** — select **Token** from the dropdown
   - **Token** — paste the Root Token copied from your terminal
5. Click **Test Provider** — you should see a **"Test service provider working fine"** confirmation.
6. Click **Save**.

---

## Step 4 — Configure the Collection to Use Vault

1. Open **Collection Settings** (use the collection menu).
2. Go to the **Secret Manager** tab.
3. Select your Vault provider.
4. Enter the **Secret Name** and **Secret Path**.
   - Path must start from `secret/`, e.g. `secret/data/usebruno`
5. Click **Save**.

---

## Step 5 — Fetch Secrets

1. Click **Fetch Secrets** in the top-left corner of the Bruno window.
2. In the dialog, select **HashiCorp Vault**.
3. Click **Fetch** — secrets are pulled automatically and appear in the **Secrets** section of the collection.

---

## Using Secrets in Requests

Once fetched, secrets are available everywhere inside the collection using the pattern:

```
$secrets.<secret-name>.<key-name>
```

### In request body / headers / query params (template syntax)

```json
{
  "title": "{{$secrets.usebruno.api-key}}"
}
```

The `echo-bru` request in this collection already shows this pattern:

```yaml
body:
  type: json
  data: |-
    {
      "title": "{{$secrets.<secret-name>.<key-name>}}",
      "msg": "The API client developers love most"
    }
```

### In Pre-request / Post-request scripts

```javascript
const apiKey = bru.getSecretVar('usebruno.api-key');
req.setHeaders('x-api-key: ' + apiKey);
```

---

## Further Reading

- [Using secrets in Bruno](https://docs.usebruno.com/secrets-management/secret-managers/hashicorp-vault/using-secrets)
- [HashiCorp Vault overview (Bruno docs)](https://docs.usebruno.com/secrets-management/secret-managers/hashicorp-vault/overview)
