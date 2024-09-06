# Depurando solicitudes al ledger

El CLI tiene un argumento `--verbose` que se puede usar con todos los comandos. Este argumento imprime información detallada sobre la solicitud que se envió al ledger.

Todos los comandos CLI se comunican con el ledger usando APIs REST estándar, usar el parámetro `verbose` nos permitirá examinar esas solicitudes con más detalle. Esto es útil para depurar y también para aprender más sobre cómo funcionan las APIs del ledger.

Por ejemplo, imprimamos información detallada sobre la intención que acabamos de hacer:

> el Id del intent puedes obtenerlo del resumen que se genera en su creación. También podrías usar `minka intent list` este comando listará todas las intenciones de pago que se han realizado.

```powershell
minka intent show jqWRNXPwvY3TUQnPVyPDO --verbose

Request details:
GET https://ldg-dev.one/api/v2/intents/jqWRNXPwvY3TUQnPVyPDO

Headers:
  - Accept: application/json, text/plain, */*
  - Content-Type: undefined
  - User-Agent: MinkaCLI/2.14.0-alpha.2 LedgerSDK/2.13.0
  - Authorization: Bearer eyJhbGciOiJFZERTQSIsImtpZCI6InJwdGVaNGtPdWlCNmE5L0tIQ3FPYzhIY1c1SjIwSHJiNUQ0WkNiWllwU0U9In0.eyJpYXQiOjE3MjU0MTAwNjUsImV4cCI6MTcyNTQxMzY2NSwiaXNzIjoiY2xpIiwiYXVkIjoiYWNoIiwic3ViIjoic2lnbmVyOnRlc2xhYmFuayIsImhzaCI6ImZhNDczZTE2ZjYwYTFhYTI1YzY1ZDQwMGMyYWU1NjI5OGNlMmNlYzJkZDViZGQ4YTFmZmQzMDA4NmQwMjZkODI6eC1sZWRnZXIifQ.nHKx5adDlRmDEhRTipQH7KTmNQmUHBz6xvDpsirzn0aPe5ynDRhx7BKwE9_iQMgLJfNJW3-tSIGXYas6PDuUDQ
  - X-Ledger: ach
  - Accept-Encoding: gzip, compress, deflate, br


Response details:
Status: 200 OK

Headers:
  - x-powered-by: Express
  - access-control-allow-origin: *
  - content-type: application/json; charset=utf-8
  - etag: W/"954-UwsWDlos71qyRIZomCZr4o8CRQw"
  - x-cloud-trace-context: dbed3d5ea960c111e990711ed5709691;o=1
  - date: Wed, 04 Sep 2024 00:34:25 GMT
  - server: Google Frontend
  - content-length: 2388
  - via: 1.1 google
  - alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000

{
  "hash": "69ca147f8464afa07e74e532c4300ef07f076dcfc56a7f3c376e67b79dc9a520",
  "data": {
    "handle": "jqWRNXPwvY3TUQnPVyPDO",
    "claims": [
      {
        "action": "transfer",
        "amount": 1200,
        "source": {
          "handle": "svgs:1001001345@teslabank.io"
        },
        "symbol": {
          "handle": "usd"
        },
        "target": {
          "handle": "svgs:1001009422@mintbank.dev"
        }
      }
    ],
    "schema": "transfer",
    "access": [
      {
        "action": "any",
        "signer": {
          "public": "rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE="
        }
      },
      {
        "action": "read",
        "bearer": {
          "$signer": {
            "public": "rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE="
          }
        }
      }
    ],
    "config": {
      "commit": "auto"
    }
  },
  "luid": "$int.M24v_O240gvT4Ex5Z",
  "meta": {
    "proofs": [
      {
        "custom": {
          "moment": "2024-09-04T00:30:09.670Z",
          "status": "created"
        },
        "digest": "bab242d59b5c43dbad5f3c552e4eb8cb37069f61bc56ffc32e67db01fab11180",
        "method": "ed25519-v2",
        "public": "rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE=",
        "result": "jRBczKiIZKkRDKqLHJdA9tcEMACXAxXk1RTPDLh6VA14ONsfiWqcJI+/ncLWj/xb6qWqccodEqzQwdkIT7PDAg=="
      },
      {
        "custom": {
          "luid": "$int.M24v_O240gvT4Ex5Z",
          "moment": "2024-09-04T00:30:10.065Z",
          "status": "created"
        },
        "digest": "2bab539f1374e130224bf63f34354715b22c91fe20ad288c52115640a9df1f05",
        "method": "ed25519-v2",
        "public": "MrByXmC5wKLCV0irkNlLTeO/DmmyI0xVPwI29Os2njQ=",
        "result": "5+La+GjbkHFoF9YxAHHslT+gdCg7JwllD655KauQH5UtjRjHG1S3jHpDSeqb54umKtJiLpJkGTMLwLcceqf1BQ=="
      },
      {
        "custom": {
          "moment": "2024-09-04T00:30:10.151Z",
          "status": "prepared"
        },
        "digest": "db9970054df887ef816109e6e3be20189076b2a7253332a00ab98df485e9300a",
        "method": "ed25519-v2",
        "public": "MrByXmC5wKLCV0irkNlLTeO/DmmyI0xVPwI29Os2njQ=",
        "result": "sc/Swq6zXLP0FyhlEbE1HxNqq7mUcK5VVgx+9sDjn5re4H2VoGB26lQB1Y7A97BE1zuZ/WJOmqMQuSTr+9lcBw=="
      },
      {
        "custom": {
          "moment": "2024-09-04T00:30:10.273Z",
          "status": "committed"
        },
        "digest": "d2da64b9c1afa31318e9ccfa95f06d4e799194ba60a7fb22f96b1e8df7db6476",
        "method": "ed25519-v2",
        "public": "MrByXmC5wKLCV0irkNlLTeO/DmmyI0xVPwI29Os2njQ=",
        "result": "0Wk9fIbZDHJ1VDXJWwRwo6GWJQ6cudy1BV30m4r9umptThefKTEwryV00/lC4TL1jfZ32WQqGiGcsSmsgVISAg=="
      },
      {
        "custom": {
          "moment": "2024-09-04T00:30:10.320Z",
          "status": "completed"
        },
        "digest": "d14249c551019ad80652213e22d14028fbf48fc14e0aea3f082b1303bc780bdf",
        "method": "ed25519-v2",
        "public": "MrByXmC5wKLCV0irkNlLTeO/DmmyI0xVPwI29Os2njQ=",
        "result": "VprzAnj8WkVmy0EOmtPCeL5aiAAUWD7np/IoQbwN7OiIif4Pn4djOFliwZreT8kQecYlTGrbiaF5h3ovk9JzDA=="
      }
    ],
    "routed": true,
    "status": "completed",
    "thread": "5ToQK-JeSbjToNbse",
    "moment": "2024-09-04T00:30:10.070Z",
    "owners": [
      "rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE="
    ]
  }
}

Intent summary:
---------------------------------------------------------------------------
Handle: jqWRNXPwvY3TUQnPVyPDO
Schema: transfer

Action: transfer
 - Source: svgs:1001001345@teslabank.io
 - Target: svgs:1001009422@mintbank.dev
 - Symbol: usd
 - Amount: $12.00


Access rules:
#0
  - Action: any
  - Signer:
    - public: rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE=
#1
  - Action: read
  - Bearer:
    - $signer:
      - public:  rpteZ4kOuiB6a9/KHCqOc8HcW5J20Hrb5D4ZCbZYpSE=

Status: completed
```

En la salida podemos ver toda la información sobre la solicitud y la respuesta. Podemos ver la URL completa, el método HTTP, los encabezados y el cuerpo completo de la solicitud.

Esto funciona para todos los comandos CLI, así que puedes usarlo para aprender más sobre las APIs del ledger.

## ¿Qué sigue?

En el siguiente tutorial exploraremos cómo conectar un sistema bancario central a una red de pagos en tiempo real. Aprenderemos más sobre el protocolo de dos fases de confirmación que se utiliza para [sincronizar las operaciones entre los participantes de un sistema RTP](../orchestrando-participantes-externos/) de manera eficiente y confiable.

