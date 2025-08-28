# Integración técnica con **Dátil API** para emitir Facturas (`/invoices/issue`)

> **Antes de todo:**
>
> 1. Crea tu cuenta en **Dátil**.
> 2. Obtén tu **API Key** desde el panel.
> 3. Carga tu **certificado de firma electrónica** (.p12/.pfx) en Dátil.
> 4. Conserva la **clave del certificado**, la usarás como header `X-Password`.

📚 Documentación oficial: [https://datil.dev/#facturas](https://datil.dev/#facturas)

---

## 0) Requisitos

- Acceso HTTP saliente a `https://link.datil.co`.
- Zona horaria correcta para `fecha_emision` (formato ISO-8601).
- Control de **secuencial** único por **serie** (establecimiento + punto de emisión).
- Manejo de credenciales mediante **variables de entorno**.

---

## 1) Variables de entorno

Crea un archivo `.env`:

```dotenv
# Credenciales Dátil
DATIL_API_URL=https://link.datil.co
DATIL_API_KEY=REEMPLAZA_CON_TU_API_KEY
DATIL_SIGN_PASSWORD=REEMPLAZA_CON_TU_PASS

# Emisor
EMISOR_RUC=1719956854001
EMISOR_RAZON=Bryan Suárez
EMISOR_NOMBRE_COMERCIAL=Bryan Suárez
EMISOR_DIRECCION="Av. Primera 234 y calle 5ta"
ESTABLECIMIENTO_CODIGO=001
PUNTO_EMISION=002

# Factura (ejemplo)
FACTURA_AMBIENTE=1
FACTURA_TIPO_EMISION=1
FACTURA_SECUENCIAL=43501
FACTURA_FECHA_EMISION=2025-08-27T10:28:56.782Z

# Comprador (ejemplo)
COMPRADOR_IDENT=0987654321
COMPRADOR_TIPO_IDENT=05
COMPRADOR_RAZON="Juan Pérez"
COMPRADOR_EMAIL=juan.perez@xyz.com
COMPRADOR_DIRECCION="Calle única Numero 987"
COMPRADOR_TELEFONO=046029400

```

# 2) Payload base (JSON)

Guarda como payload.json:

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

# 3) Emisión con cURL

```
curl --location "$DATIL_API_URL/invoices/issue" \
  --header 'Content-Type: application/json' \
  --header "X-Key: $DATIL_API_KEY" \
  --header "X-Password: $DATIL_SIGN_PASSWORD" \
  --data-raw @payload.json

```

# 4) Emisión en Node.js

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
  .then((data) => console.log('Factura emitida OK:\n', data))
  .catch((err) => console.error('Error:\n', err.message));

```

# 5) Emisión en Python

```
  python -m venv .venv && source .venv/bin/activate
pip install python-dotenv requests

```

# 6) Emisión en PHP (Laravel)

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

# 7) Validación previa (Node.js)

```
function round2(n) { return Math.round((n + Number.EPSILON) * 100) / 100; }

export function validateInvoiceMath(invoice) {
  const sumItems = round2(
    (invoice.items || []).reduce((acc, it) => acc + Number(it.precio_total_sin_impuestos || 0), 0)
  );
  const subtotal = round2(Number(invoice.totales.total_sin_impuestos || 0));
  if (sumItems !== subtotal) {
    throw new Error(`Subtotal no cuadra: items=${sumItems} != total=${subtotal}`);
  }

  const sumTaxLines = round2(
    (invoice.totales.impuestos || []).reduce((acc, t) => acc + Number(t.valor || 0), 0)
  );

  const descuento = round2(Number(invoice.totales.descuento || 0));
  const propina = round2(Number(invoice.totales.propina || 0));
  const expectedTotal = round2(subtotal - descuento + sumTaxLines + propina);
  const importe = round2(Number(invoice.totales.importe_total || 0));

  if (expectedTotal !== importe) {
    throw new Error(`Importe no cuadra: esperado=${expectedTotal} != importe=${importe}`);
  }
}

```

# 8) Manejo de errores

    •	401/403 → API Key o certificado inválido.
    •	4xx → Errores de validación (impuestos, identificación, secuencial).
    •	5xx → Problema transitorio. Reintentar con backoff.
    •	Idempotencia: controla con secuencial.

⸻

# 9 Producción vs pruebas

    •	ambiente: usa el valor que corresponda a tu cuenta.
    •	Mantén control de secuencial por serie.
    •	Cambia a producción solo tras pruebas exitosas.

⸻

# 10) Checklist final

    •	Credenciales y certificado vigentes.
    •	fecha_emision en ISO-8601.
    •	Totales cuadran.
    •	Códigos de impuestos y tipo de identificación válidos.
    •	Logs y manejo de errores implementados.
    •	Sin exponer X-Key ni X-Password.

⸻

✅ Con estos pasos podrás emitir facturas en Dátil de forma programática y sin ambigüedades.
