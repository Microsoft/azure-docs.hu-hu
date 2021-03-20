---
title: Recovery Services tároló konfigurációjának frissítése REST API
description: Ebből a cikkből megtudhatja, hogyan frissítheti a tár konfigurációját a REST API használatával.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91567825"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Az Azure Recovery Services-tároló konfigurációinak frissítése REST API használatával

Ez a cikk azt ismerteti, hogyan frissítheti az Azure Recovery Services-tároló biztonsági másolatokkal kapcsolatos konfigurációit REST API használatával.

## <a name="soft-delete-state"></a>Helyreállítható törlés állapota

A védett elemek biztonsági másolatának törlése olyan jelentős művelet, amelyet figyelni kell. A véletlen törlésekkel szembeni védelem érdekében az Azure Recovery Services-tárolónak van egy puha törlési képessége. Ez a funkció lehetővé teszi, hogy szükség esetén visszaállítsa a törölt biztonsági másolatokat a törlést követő időszakon belül.

Vannak azonban olyan forgatókönyvek, amelyekben ez a képesség nem szükséges. Az Azure Recovery Services-tároló nem törölhető, ha olyan biztonsági másolati elemek találhatók benne, amelyeken még nem is törlődnek. Ez problémát jelenthet, ha a tárolót azonnal törölni kell. Például: az üzembe helyezési műveletek gyakran törlik a létrehozott erőforrásokat ugyanabban a munkafolyamatban. A központi telepítés létrehozhat egy tárolót, konfigurálhatja a biztonsági másolatokat egy elemhez, tesztet állíthat vissza, majd folytathatja a biztonsági mentési elemek és a tár törlését. Ha a tár törlése sikertelen, a teljes telepítés sikertelen lehet. A törlés letiltásával garantálható az azonnali törlés.

Ezért alaposan ki kell választania, hogy le kell-e tiltani egy adott tár helyreállítható törlését a forgatókönyvtől függően. További információ: [Soft-delete cikk](backup-azure-security-feature-cloud.md).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Helyreállítható törlési állapot beolvasása REST API használatával

Alapértelmezés szerint a rendszer minden újonnan létrehozott Recovery Services-tárolóra engedélyezi a helyreállítható törlési állapotot. Egy tár helyreállítható törlési állapotának lekéréséhez/frissítéséhez használja a Backup-tároló konfigurációval kapcsolatos [REST API dokumentumát](/rest/api/backup/backupresourcevaultconfigs) .

A tárolóhoz tartozó helyreállítható törlés aktuális állapotának beolvasásához használja a következő *lekérési* műveletet

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

A Get URI a, a `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` paraméterekkel rendelkezik. Ebben a példában a `{vaultName}` "testVault" és a `{vaultresourceGroupName}` "testVaultRG". Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelem törzsére.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Válaszok

A "GET" művelet sikeres válasza alább látható:

|Név  |Típus  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Példaválasz

A "GET" kérés elküldése után a rendszer 200 (sikeres) választ ad vissza.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Helyreállítható törlési állapot frissítése REST API használatával

A Recovery Services-tároló törlési állapotának REST API használatával történő frissítéséhez használja a következő *put* műveletet:

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

A Put URI a, a `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` paraméterekkel rendelkezik. Ebben a példában a `{vaultName}` "testVault" és a `{vaultresourceGroupName}` "testVaultRG". Ha az URI-t a fenti értékekre cseréljük, akkor az URI a következőképpen fog kinézni.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

A kérelem törzsének létrehozásához a következő általános definíciók használhatók

További részletekért tekintse meg [a REST API dokumentációját](/rest/api/backup/backupresourcevaultconfigs/update#request-body) .

|Name  |Kötelező  |Típus  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   Sztring      |  Opcionális eTag       |
|location     |  true       |Sztring         |   Erőforrás helye      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  A tár tulajdonságai       |
|tags     |         | Objektum        |     Erőforráscímkék    |

#### <a name="example-request-body"></a>Példa kérelem törzsére

A következő példa a helyreállítható törlési állapot frissítésére szolgál a "Letiltva" értékre.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>A javítási műveletre adott válaszok

A "PATCH" művelet sikeres válasza alább látható:

|Név  |Típus  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Válasz példa a javítási műveletre

A "javítás" kérés elküldése után a rendszer egy 200 (sikeres) választ ad vissza.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy biztonsági mentési szabályzatot egy Azure-beli virtuális gép biztonsági mentéséhez ebben a tárolóban](backup-azure-arm-userestapi-createorupdatepolicy.md).

Az Azure REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
