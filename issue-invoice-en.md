# Technical Integration with **Dátil API** — Invoice Issuance (`/invoices/issue`)

> **Before you start:**
>
> 1. Create your account in **Dátil**.
> 2. Obtain your **API Key** from the dashboard.
> 3. Upload your **digital signature certificate** (.p12/.pfx) in Dátil.
> 4. Keep the **certificate password**, you will use it in the `X-Password` header when issuing invoices.

📚 Official docs: [https://datil.dev/#facturas](https://datil.dev/#facturas)

---

## 0) Requirements

- Outbound HTTP access to `https://link.datil.co`.
- Proper timezone handling when generating `fecha_emision` (ISO-8601 format).
- Unique **secuencial** per **series** (establishment + emission point).
- Use of **environment variables** to manage credentials.

---

## 1) Environment variables

Create a `.env` file:

```dotenv
# Dátil credentials
DATIL_API_URL=https://link.datil.co
DATIL_API_KEY=REPLACE_WITH_YOUR_API_KEY
DATIL_SIGN_PASSWORD=REPLACE_WITH_YOUR_CERT_PASSWORD

# Issuer
EMISOR_RUC=1719956854001
EMISOR_RAZON=Bryan Suárez
EMISOR_NOMBRE_COMERCIAL=Bryan Suárez
EMISOR_DIRECCION="Av. Primera 234 y calle 5ta"
ESTABLECIMIENTO_CODIGO=001
PUNTO_EMISION=002

# Invoice (example)
FACTURA_AMBIENTE=1
FACTURA_TIPO_EMISION=1
FACTURA_SECUENCIAL=43501
FACTURA_FECHA_EMISION=2025-08-27T10:28:56.782Z

# Buyer (example)
COMPRADOR_IDENT=0987654321
COMPRADOR_TIPO_IDENT=05
COMPRADOR_RAZON="Juan Pérez"
COMPRADOR_EMAIL=juan.perez@xyz.com
COMPRADOR_DIRECCION="Calle única Numero 987"
COMPRADOR_TELEFONO=046029400

```

# 2) Base payload (JSON)

Save this as payload.json:

```
{
  "ambiente": 1,
  "tipo_emision": 1,
  "secuencial": 43501,
  "fecha_emision": "2025-08-27T10:28:56.782Z",
  "emisor": {
    "ruc": "1719956854001",
    "obligado_contabilidad": false,
    "contribuyente_especial": "",
    "nombre_comercial": "Bryan Suárez",
    "razon_social": "Bryan Suárez",
    "direccion": "Av. Primera 234 y calle 5ta",
    "establecimiento": {
      "punto_emision": "002",
      "codigo": "001",
      "direccion": "Av. Primera 234 y calle 5ta"
    }
  },
  "moneda": "USD",
  "info_adicional": [
    { "nombre": "Tiempo de entrega", "valor": "5 días" }
  ],
  "totales": {
    "total_sin_impuestos": 4359.54,
    "impuestos": [
      {
        "base_imponible": 4359.54,
        "valor": 523.14,
        "codigo": "2",
        "codigo_porcentaje": "4"
      }
    ],
    "importe_total": 4882.68,
    "propina": 0.0,
    "descuento": 0.0
  },
  "comprador": {
    "email": "juan.perez@xyz.com",
    "identificacion": "0987654321",
    "tipo_identificacion": "05",
    "razon_social": "Juan Pérez",
    "direccion": "Calle única Numero 987",
    "telefono": "046029400"
  },
  "items": [
    {
      "cantidad": 622.0,
      "codigo_principal": "ZNC",
      "codigo_auxiliar": "050",
      "precio_unitario": 7.008907,
      "descuento": 0,
      "descripcion": "Zanahoria granel  50 Kg.",
      "precio_total_sin_impuestos": 4359.54,
      "impuestos": [
        {
          "base_imponible": 4359.54,
          "valor": 523.14,
          "tarifa": 12.0,
          "codigo": "2",
          "codigo_porcentaje": "2"
        }
      ],
      "detalles_adicionales": { "Peso": "5000.0000" },
      "descuento": 0.0,
      "unidad_medida": "Kilos"
    }
  ],
  "pagos": [
    {
      "medio": "cheque",
      "total": 2882.68,
      "propiedades": { "numero": "1234567890", "banco": "Banco Pacífico" },
      "notas": "Depositado en cuenta corriente"
    }
  ]
}
```

# 3) Issuing via cURL

```
curl --location "$DATIL_API_URL/invoices/issue" \
  --header 'Content-Type: application/json' \
  --header "X-Key: $DATIL_API_KEY" \
  --header "X-Password: $DATIL_SIGN_PASSWORD" \
  --data-raw @payload.json

```

# 4) Issuing in Node.js

```
npm init -y
npm i dotenv
```

issue-invoice.js

```
import 'dotenv/config';
import fs from 'node:fs';

const API_URL = process.env.DATIL_API_URL || 'https://link.datil.co';
const API_KEY = process.env.DATIL_API_KEY;
const SIGN_PWD = process.env.DATIL_SIGN_PASSWORD;

async function issueInvoice(payload) {
  const res = await fetch(`${API_URL}/invoices/issue`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-Key': API_KEY,
      'X-Password': SIGN_PWD,
    },
    body: JSON.stringify(payload),
  });

  const text = await res.text();
  if (!res.ok) throw new Error(`Dátil ${res.status}: ${text}`);
  try { return JSON.parse(text); } catch { return text; }
}

const payload = JSON.parse(fs.readFileSync('./payload.json', 'utf8'));

issueInvoice(payload)
  .then((data) => console.log('Invoice issued successfully:\n', data))
  .catch((err) => console.error('Error:\n', err.message));
```

# 5) Issuing in Python

```
python -m venv .venv && source .venv/bin/activate
pip install python-dotenv requests
```

issue_invoice.py

```
import os, json, requests
from dotenv import load_dotenv

load_dotenv()

API_URL = os.getenv("DATIL_API_URL", "https://link.datil.co")
API_KEY = os.getenv("DATIL_API_KEY")
SIGN_PWD = os.getenv("DATIL_SIGN_PASSWORD")

with open("payload.json", "r", encoding="utf-8") as f:
    payload = json.load(f)

resp = requests.post(
    f"{API_URL}/invoices/issue",
    headers={
        "Content-Type": "application/json",
        "X-Key": API_KEY,
        "X-Password": SIGN_PWD,
    },
    json=payload,
    timeout=60,
)

print("Status:", resp.status_code)
print(resp.text)
resp.raise_for_status()
```

# 6) Issuing in PHP (Laravel)

```
use Illuminate\Support\Facades\Http;

$payload = json_decode(file_get_contents(__DIR__.'/payload.json'), true);

$response = Http::withHeaders([
    'Content-Type' => 'application/json',
    'X-Key'       => env('DATIL_API_KEY'),
    'X-Password'  => env('DATIL_SIGN_PASSWORD'),
])->post(env('DATIL_API_URL', 'https://link.datil.co').'/invoices/issue', $payload);

if ($response->failed()) {
    abort(500, 'Dátil '.$response->status().' '.$response->body());
}

return $response->json();
```

# 7) Pre-validation (Node.js)

```
function round2(n) { return Math.round((n + Number.EPSILON) * 100) / 100; }

export function validateInvoiceMath(invoice) {
  const sumItems = round2(
    (invoice.items || []).reduce((acc, it) => acc + Number(it.precio_total_sin_impuestos || 0), 0)
  );
  const subtotal = round2(Number(invoice.totales.total_sin_impuestos || 0));
  if (sumItems !== subtotal) {
    throw new Error(`Subtotal mismatch: items=${sumItems} != total=${subtotal}`);
  }

  const sumTaxLines = round2(
    (invoice.totales.impuestos || []).reduce((acc, t) => acc + Number(t.valor || 0), 0)
  );

  const descuento = round2(Number(invoice.totales.descuento || 0));
  const propina = round2(Number(invoice.totales.propina || 0));
  const expectedTotal = round2(subtotal - descuento + sumTaxLines + propina);
  const importe = round2(Number(invoice.totales.importe_total || 0));

  if (expectedTotal !== importe) {
    throw new Error(`Total mismatch: expected=${expectedTotal} != importe=${importe}`);
  }
}
```

# 8) Error handling

```
	•	401/403 → Invalid API Key or certificate.
	•	4xx → Validation errors (tax codes, IDs, duplicate secuencial).
	•	5xx → Transient issue. Retry with backoff.
	•	Idempotency: ensure uniqueness with secuencial.

⸻

# 9) Production vs testing
	•	ambiente: set according to your account.
	•	Maintain control of secuencial per series.
	•	Switch to production only after successful certification tests.

⸻

# 10) Final checklist
	•	Credentials and certificate uploaded and valid.
	•	fecha_emision in ISO-8601.
	•	Totals, taxes and discounts balanced.
	•	Tax codes and tipo_identificacion valid.
	•	Logging and error handling implemented.
	•	Do not expose X-Key or X-Password in code repositories.

⸻

✅ With these steps, your dev team can issue invoices programmatically with Dátil safely and consistently.
```
