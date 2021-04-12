---
title: Azure Key Vault Event Grid forrásként
description: A Azure Key Vault eseményekhez megadott tulajdonságokat és sémát ismerteti Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363406"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault Event Grid forrásként

Ez a cikk a [Azure Key Vault](../key-vault/index.yml)eseményeinek tulajdonságait és sémáját ismerteti. Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).


## <a name="available-event-types"></a>Elérhető események típusai

Egy Azure Key Vault fiók a következő típusú eseményeket hozza létre:

| Esemény teljes neve | Esemény megjelenítendő neve | Leírás |
| ---------- | ----------- |---|
| Microsoft. kulcstartó. CertificateNewVersionCreated | Tanúsítvány új verziója létrehozva | Új tanúsítvány vagy új tanúsítvány-verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. CertificateNearExpiry | A tanúsítvány hamarosan lejár | Akkor aktiválódik, ha a tanúsítvány aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| Microsoft. kulcstartó. CertificateExpired | A tanúsítvány lejárt | A tanúsítvány lejártakor aktiválódik. |
| Microsoft. kulcstartó. KeyNewVersionCreated | A kulcs új verziója létrehozva | Új kulcs vagy új kulcs létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. KeyNearExpiry | Kulcs közel lejárata | Akkor aktiválódik, ha a kulcs aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| A Microsoft. kulcstartó. kulcstartó lejárt | A kulcs lejárt | A kulcs lejártakor aktiválódik. |
| Microsoft. kulcstartó. SecretNewVersionCreated | A titkos új verzió létrehozva | Új titok vagy új titkos verzió létrehozásakor aktiválódik. |
| Microsoft. kulcstartó. SecretNearExpiry | Közel lejáró titkos kód | Akkor aktiválódik, ha a titkos kulcs aktuális verziója hamarosan lejár. (Az esemény a lejárati dátum előtt 30 nappal aktiválódik.) |
| Microsoft. kulcstartó. SecretExpired | A titkos kód lejárt | A titkos kód lejártakor aktiválódik. |
| Microsoft. kulcstartó. VaultAccessPolicyChanged | A tár hozzáférési szabályzata megváltozott | Akkor aktiválódik, ha egy Key Vault hozzáférési szabályzata megváltozott. Ez egy olyan forgatókönyvet tartalmaz, amikor Key Vault engedélyezési modellt az Azure szerepköralapú hozzáférés-vezérlési szolgáltatására módosítják.   |

## <a name="event-examples"></a>Példák az eseményekre

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)

Az alábbi példa a **Microsoft. kulcstartó. SecretNewVersionCreated** sémáját mutatja be:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Az alábbi példa a **Microsoft. kulcstartó. SecretNewVersionCreated** sémáját mutatja be:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Esemény tulajdonságai

# <a name="event-grid-event-schema"></a>[Event Grid-eseményséma](#tab/event-grid-event-schema)
Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `topic` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `eventType` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `eventTime` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Az alkalmazás konfigurációs eseményeinek adatkészlete. |
| `dataVersion` | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| `metadataVersion` | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |


# <a name="cloud-event-schema"></a>[Felhő-eseményséma](#tab/cloud-event-schema)

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| `source` | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| `subject` | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| `type` | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| `time` | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| `id` | sztring | Az esemény egyedi azonosítója. |
| `data` | object | Az alkalmazás konfigurációs eseményeinek adatkészlete. |
| `specversion` | sztring | A CloudEvents séma specifikációjának verziója. |

---
 

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| ---------- | ----------- |---|
| `id` | sztring | Az eseményt kiváltó objektum azonosítója |
| `vaultName` | sztring | Az eseményt kiváltó objektum kulcstárolójának neve |
| `objectType` | sztring | Az eseményt kiváltó objektum típusa |
| `objectName` | sztring | Az eseményt kiváltó objektum neve |
| `version` | sztring | Az eseményt kiváltó objektum verziója |
| `nbf` | szám | A nem az idő előtti dátum másodpercben 1970-01-01T00:00: az eseményt kiváltó objektum 00Z |
| `exp` | szám | Az eseményt kiváltó objektum lejárati dátuma (másodpercben) 1970-01-01T00:00:00Z |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím  |Leírás  |
|---------|---------|
| [Key Vault események figyelése a Azure Event Grid](../key-vault/general/event-grid-overview.md) | A Key Vault és a Event Grid integrálásának áttekintése. |
| [Oktatóanyag: Key Vault események létrehozása és figyelése a Event Grid](../key-vault/general/event-grid-logicapps.md) | Megtudhatja, hogyan állíthatja be a Key Vault Event Grid értesítéseit. |


## <a name="next-steps"></a>Következő lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
* További információ a Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

