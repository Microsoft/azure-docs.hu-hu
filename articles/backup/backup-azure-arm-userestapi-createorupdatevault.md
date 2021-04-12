---
title: Recovery Services-tárolók létrehozása REST API használatával
description: Ebből a cikkből megtudhatja, hogyan kezelheti az Azure-beli virtuális gépek biztonsági mentési és visszaállítási műveleteit REST API használatával.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: a37808548ec58977b7d6af16c75b94b7b5efe446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002936"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Azure Recovery Services-tároló létrehozása REST API használatával

Az Azure Recovery Services-tárolók REST API használatával történő létrehozásának lépései a tár [létrehozása REST API](/rest/api/recoveryservices/vaults/createorupdate) dokumentációjában találhatók. Ezt a dokumentumot hivatkozásként használjuk a "testVault" nevű tároló létrehozásához az "USA nyugati régiójában".

Azure Recovery Services-tároló létrehozásához vagy frissítéséhez használja a következő *put* műveletet.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Kérelem létrehozása

A *put* kérelem létrehozásához a `{subscription-id}` paraméter megadása kötelező. Ha több előfizetéssel rendelkezik, tekintse meg [a több előfizetés használata](/cli/azure/manage-azure-subscriptions-azure-cli)című témakört. Megadhatja a `{resourceGroupName}` és a `{vaultName}` erőforrásait, valamint a `api-version` paramétert. Ez a cikk a következőt használja: `api-version=2016-06-01` .

A következő fejlécek megadása kötelező:

| Kérelem fejléce   | Leírás |
|------------------|-----------------|
| *Content-Type* (Tartalomtípus):  | Kötelező. Állítsa `application/json` értékre. |
| *Authorization* (Engedélyezés): | Kötelező. Állítsa egy érvényes `Bearer` [hozzáférési jogkivonatra](/rest/api/azure/#authorization-code-grant-interactive-clients). |

További információ a kérelem létrehozásáról: [REST API kérelem/válasz összetevői](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók:

|Name  |Kötelező  |Típus  |Leírás  |
|---------|---------|---------|---------|
|eTag     |         |   Sztring      |  Opcionális eTag       |
|location     |  true       |Sztring         |   Erőforrás helye      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tár tulajdonságai       |
|SKU     |         |  [SKU](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Az egyes Azure-erőforrások egyedi rendszerazonosítójának azonosítása     |
|tags     |         | Objektum        |     Erőforráscímkék    |

Vegye figyelembe, hogy a tár neve és az erőforráscsoport neve a PUT URI-ban van megadva. A kérelem törzse határozza meg a helyet.

## <a name="example-request-body"></a>Példa kérelem törzsére

A következő példában egy tárolót hoz létre a "West US"-ben. Itt adhatja meg a helyet. Az SKU mindig "standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Válaszok

Két sikeres válasz van a művelethez egy Recovery Services-tároló létrehozásához vagy frissítéséhez:

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |   [Tároló](/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 létrehozva     | [Tároló](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Létrehozva      |

További információ a REST API válaszokról: [a válaszüzenet feldolgozása](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Példaválasz

Az előző példában szereplő kérelem törzsében létrehozott, sűrített *201* -válasz egy *azonosítót* rendelt hozzá, és a *provisioningState* *sikeres*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure-beli virtuális gép biztonsági mentéséhez ebben a tárolóban](backup-azure-arm-userestapi-createorupdatepolicy.md).

Az Azure REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
