---
title: Azure Key Vault naplózása | Microsoft Docs
description: Megtudhatja, hogyan figyelheti a kulcstartók hozzáférését úgy, hogy engedélyezi a Azure Key Vault naplózását, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 99313cf1248ef5e90d7cd60d528a58c7925298d0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499526"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

Egy vagy több kulcstartó létrehozása után valószínűleg figyelnie kell a kulcstartók elérésének módját és időpontját. Ezt úgy teheti meg, hogy engedélyezi a Azure Key Vault naplózását, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat. További információ a beállításáról: a [Key Vault naplózásának engedélyezése](howto-logging.md).

A naplózási adatokat a Key Vault művelet után 10 perccel (legfeljebb) érheti el. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A naplókat a tárfiókban kezelheti:

* A Storage-fiókban a szabványos Azure-hozzáférés-vezérlési módszerekkel biztonságossá teheti a naplókat, és korlátozhatja, hogy ki férhet hozzá.
* Ha már nincs szüksége a tárfiókban tárolt naplókra, törölje azokat.

További információ a Key Vaultről: [Mi az Azure Key Vault?](overview.md). További információ a Key Vault rendelkezésre állásáról: [díjszabási oldal](https://azure.microsoft.com/pricing/details/key-vault/). További információ a [Key Vault Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md)használatáról.

## <a name="interpret-your-key-vault-logs"></a>A Key Vault naplóinak értelmezése

Ha engedélyezi a naplózást, a rendszer automatikusan létrehozza az elemzések – **naplók-AuditEvent** nevű új tárolót a megadott Storage-fiókhoz. Ugyanazt a Storage-fiókot használhatja a naplók több kulcstartóhoz való gyűjtéséhez.

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Nézzük meg egy példa naplóbejegyzést. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A következő táblázat a mezőneveket és a leírásokat tartalmazza:

| Mező neve | Description |
| --- | --- |
| **idő** |Dátum és idő (UTC). |
| **resourceId** |Azure Resource Manager erőforrás-azonosító. Key Vault naplók esetében ez mindig a Key Vault erőforrás-azonosító. |
| **operationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |REST API az ügyfél által kért verziót. |
| **Kategória** |Az eredmény típusa. Key Vault naplók esetében `AuditEvent` az egyetlen elérhető érték. |
| **resultType** |A REST API kérelem eredménye. |
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **Átl** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. |
| **correlationId** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **identitás** |Az REST API kérelemben bemutatott jogkivonat identitása. Ez általában a "felhasználó", "a" szolgáltatásnév "vagy" felhasználó + appId "kombinációja, amely egy Azure PowerShell-parancsmagból származó kérelem esetében van. |
| **Tulajdonságok** |A művelettől (**operationName**) függően változó információk. A legtöbb esetben ez a mező tartalmazza az ügyfél adatait (az ügyfél által átadott felhasználói ügynök sztringjét), a pontos REST API kérelem URI-JÁT és a HTTP-állapotkódot. Emellett, ha egy objektum egy kérelem eredményeképpen érkezik (például a Key **create** vagy a **VaultGet**), a kulcs URI-ját (as), a tároló `id` URI-ját vagy a titkos kódot is tartalmazza. |

A **OperationName** *ObjectVerb* formátumban vannak. Például:

* A Key Vault összes műveletének `Vault<action>` formátuma, például `VaultGet` és `VaultCreate` .
* Az összes kulcsfontosságú művelet `Key<action>` formátuma, például `KeySign` és `KeyList` .
* Minden titkos művelet `Secret<action>` formátuma, például `SecretGet` és `SecretListVersions` .

A következő táblázat felsorolja a **operationName** és a hozzá tartozó REST API parancsokat:

### <a name="operation-names-table"></a>Műveleti nevek tábla

# <a name="vault"></a>[Tároló](#tab/Vault)

| operationName | REST API parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés Azure Active Directory végponton keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](/rest/api/keyvault/vaults) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Kulcstároló törlése](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Kulcstároló frissítése](/rest/api/keyvault/vaults) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Törölt tár kiürítése](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Törölt tár helyreállítása|
| **VaultGetDeleted** |[Törölt tár beolvasása](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Törölt tárolók listázása](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | A tár hozzáférési szabályzatának változási eseménye közzétéve |

# <a name="keys"></a>[Kulcsok](#tab/Keys)

| operationName | REST API parancs |
| --- | --- |
| **KeyCreate** |[Kulcs létrehozása](/rest/api/keyvault/createkey) |
| **KeyGet** |[Kulcs adatainak lekérése](/rest/api/keyvault/getkey) |
| **KeyImport** |[Kulcs importálása egy tárolóba](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Kulcs törlése](/rest/api/keyvault/deletekey) |
| **KeySign** |[Aláírás kulccsal](/rest/api/keyvault/sign) |
| **KeyVerify** |[Ellenőrzés kulccsal](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Kulcs becsomagolása](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Kulcs kicsomagolása](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Titkosítás kulccsal](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Visszafejtés kulccsal](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Kulcs frissítése](/rest/api/keyvault/updatekey) |
| **KeyList** |[Egy tároló kulcsainak listázása](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Kulcs verzióinak listázása](/rest/api/keyvault/getkeyversions) |
| **Kiürítés** |[Kulcs kiürítése](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Kulcs biztonsági mentése](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Kulcs helyreállítása](/rest/api/keyvault/restorekey) |
| **Visszaállítás** |[Kulcs helyreállítása](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Törölt kulcs lekérése](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Tárolóban lévő törölt kulcsok listázása](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Kulcs közel lejárati eseményének közzététele |
| **KeyExpiredEventGridNotification** |A kulcs lejárt eseményének közzététele |

# <a name="secrets"></a>[Titkos kódok](#tab/Secrets)

| operationName | REST API parancs |
| --- | --- |
| **SecretSet** |[Titkos kulcs létrehozása](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Titkos kód beszerzése](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Titkos kulcs frissítése](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Titkos kulcs törlése](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Titkos kód kiürítése](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Titkos másolat készítése](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Titkos kód visszaállítása](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Titkos kód helyreállítása](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Törölt titkos kód beolvasása](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Tár törölt titkainak listázása](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Secret Near lejárat esemény közzététele |
| **SecretExpiredEventGridNotification** |Titokban lejárt esemény közzétéve |

# <a name="certificates"></a>[Tanúsítványok](#tab/Cerificates)

| operationName | REST API parancs |
| --- | --- |
| **CertificateGet** |[Tanúsítvány adatainak beolvasása](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Tanúsítvány létrehozása](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Tanúsítvány importálása egy tárba](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Tanúsítvány frissítése](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[Tárban lévő tanúsítványok listázása](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[A tanúsítvány verzióinak listázása](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Tanúsítvány törlése](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Tanúsítvány kiürítése](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Tanúsítvány biztonsági mentése](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Tanúsítvány visszaállítása](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Tanúsítvány helyreállítása](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Törölt tanúsítvány beolvasása](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Tárolóban lévő törölt tanúsítványok listázása](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Tanúsítvány-házirend beszerzése](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Tanúsítvány-házirend frissítése](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Tanúsítvány-házirend létrehozása](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Tanúsítványhoz tartozó névjegyek beolvasása](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[A tanúsítvány névjegyeinek beállítása](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Tanúsítványhoz tartozó névjegyek törlése](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Tanúsítvány kiállítójának beolvasása](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Tanúsítvány kiállítójának beállítása](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Tanúsítvány kiállítójának frissítése](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Tanúsítvány kiállítójának törlése](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[A tanúsítvány kiállítóinak listázása](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Tanúsítvány regisztrálása |
| **CertificateRenew** |Tanúsítvány megújítása |
| **CertificatePendingGet** |Függőben lévő tanúsítvány beolvasása |
| **CertificatePendingMerge** |Tanúsítvány egyesítése folyamatban |
| **CertificatePendingUpdate** |Tanúsítvány frissítése függőben |
| **CertificatePendingDelete** |Függőben lévő tanúsítvány törlése |
| **CertificateNearExpiryEventGridNotification** |Tanúsítvány közel lejárati eseménye közzétéve |
| **CertificateExpiredEventGridNotification** |A tanúsítvány lejárt esemény közzétéve |

---

## <a name="use-azure-monitor-logs"></a>Az Azure Monitor-naplók használata

A Key Vault naplók áttekintéséhez használhatja a Azure Monitor naplók Key Vault megoldását `AuditEvent` . Azure Monitor naplókban a naplók segítségével elemezheti az adatokat, és lekérheti a szükséges információkat. 

További információk, például a beállításának módja: [Azure Key Vault Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Következő lépések

- [Key Vault naplózás engedélyezése](howto-logging.md)
- [Azure-figyelő](../../azure-monitor/index.yml)
- A .NET-alapú webalkalmazásokban Azure Key Vaultt használó oktatóanyagért lásd: [Azure Key Vault használata webalkalmazásból](tutorial-net-create-vault-azure-web-app.md).
- Programozási hivatkozások: [Azure Key Vault developer’s guide](developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).
- Azure Key Vault Azure PowerShell 1,0-parancsmagok listáját itt tekintheti meg: [Azure Key Vault parancsmagok](/powershell/module/az.keyvault/#key_vault).