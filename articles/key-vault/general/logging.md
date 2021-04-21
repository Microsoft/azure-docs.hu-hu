---
title: Azure Key Vault naplózási | Microsoft Docs
description: Megtudhatja, hogyan monitorozható a kulcstartókhoz való hozzáférés a Azure Key Vault naplózásának engedélyezésével, amely egy Ön által adott Azure Storage-fiókba menti az adatokat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5847fcb2cf553e1fcc744877e52dbbdf1f24d992
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751831"
---
# <a name="azure-key-vault-logging"></a>Az Azure Key Vault naplózása

Egy vagy több kulcstartó létrehozása után valószínűleg figyelni szeretné, hogy ki és hogyan fér hozzá a kulcstartókhoz. Ehhez engedélyeznie kell a naplózást a Azure Key Vault, amely egy Ön által adott Azure Storage-fiókba menti az adatokat. Ennek beállításával kapcsolatos részletes útmutatásért lásd: A naplózás Key Vault [engedélyezése.](howto-logging.md)

A naplózási adatokat a Key Vault-művelet után 10 perccel (vagy 10 perccel) elérheti. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A naplókat a tárfiókban kezelheti:

* A tárfiókban szabványos Azure hozzáférés-vezérlési módszerekkel biztosíthatja a naplók biztonságát, és korlátozhatja, hogy ki férhet hozzájuk.
* Ha már nincs szüksége a tárfiókban tárolt naplókra, törölje azokat.

A folyamatokkal kapcsolatos Key Vault [lásd: Mi a Azure Key Vault?](overview.md). A szolgáltatás rendelkezésre Key Vault információkért tekintse meg a [díjszabási oldalt.](https://azure.microsoft.com/pricing/details/key-vault/) További információ a Azure Monitor [való Key Vault.](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="interpret-your-key-vault-logs"></a>A Key Vault naplóinak értelmezése

Ha engedélyezi a naplózást, a rendszer automatikusan létrehoz egy **insights-logs-auditevent** nevű új tárolót a megadott tárfiókhoz. Ugyanazt a tárfiókot több kulcstartó naplóinak gyűjtésére is használhatja.

Az egyes blobok JSON-blobként, szöveges formában vannak tárolva. Nézzünk meg egy példa naplóbejegyzést. 

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

A következő táblázat a mezőneveket és -leírásokat sorolja fel:

| Mező neve | Description |
| --- | --- |
| **Idő** |Dátum és idő (UTC). |
| **resourceId** |Azure Resource Manager erőforrás-azonosító. A Key Vault naplók esetén mindig ez a Key Vault erőforrás-azonosító. |
| **operationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion (műveletverzió)** |REST API az ügyfél által kért verziót. |
| **Kategória** |Az eredmény típusa. A Key Vault `AuditEvent` a egyetlen elérhető érték. |
| **resultType (eredménytípus)** |A kérelem REST API eredménye. |
| **resultSignature (Eredmény aláírva)** |A HTTP-állapot. |
| **resultDescription (resultDescription)** |Az eredmény további leírása, amennyiben elérhető. |
| **durationMs (időtartamok)** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |Annak az ügyfélnek az IP-címe, amely a kérelmet bekérte. |
| **correlationId (korrelációsazonosító)** |Egy nem kötelező GUID, amelyet az ügyfél alkalmazhat az ügyféloldali és a szolgáltatásoldali (Key Vault) naplók egyeztetéséhez. |
| **Identitás** |Identitás a kérelemben bemutatott jogkivonatból REST API jogkivonatból. Ez általában egy "felhasználó", egy "szolgáltatásnév" vagy a "user+appId" kombináció, mint például egy Azure PowerShell parancsmagból származó kérés esetén. |
| **Tulajdonságok** |A művelettől függően változó információk (**operationName**). A legtöbb esetben ez a mező ügyféladatokat (az ügyfél által átadott felhasználóiügynök-sztringet), a kérés URI REST API pontos adatait és a HTTP-állapotkódot tartalmazza. Emellett ha egy kérés eredményeként egy objektumot ad vissza (például **KeyCreate** vagy **VaultGet),** akkor a kulcs URI-ját is tartalmazza (mint ), a tároló URI-ját vagy a titkos kulcs `id` URI-ját. |

Az **operationName mezőértékek** *ObjectVerb formátumban* vannak. Például:

* Minden key vault-művelet formátuma `Vault<action>` meg van formában, például `VaultGet` és `VaultCreate` .
* Minden kulcsművelet formátuma , például `Key<action>` `KeySign` és `KeyList` .
* Minden titkos művelet formátuma , például `Secret<action>` `SecretGet` és `SecretListVersions` .

Az alábbi táblázatban az **operationName** értékek és a hozzájuk tartozó REST API vannak felsorolva:

### <a name="operation-names-table"></a>Műveletnevek táblázata

# <a name="vault"></a>[Tároló](#tab/Vault)

| operationName | REST API parancs |
| --- | --- |
| **Hitelesítés** |Hitelesítés Azure Active Directory végponton keresztül |
| **VaultGet** |[Kulcstároló adatainak lekérése](/rest/api/keyvault/vaults) |
| **VaultPut** |[Kulcstároló létrehozása vagy frissítése](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Kulcstároló törlése](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Kulcstároló frissítése](/rest/api/keyvault/vaults) |
| **VaultList** |[Az erőforráscsoport összes kulcstárolójának listázása](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Törölt tároló végleges törlése](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Törölt tároló helyreállítása|
| **VaultGetDeleted (Tároló törlése)** |[Törölt tároló lekérte](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted (Tárolólista törölve)** |[Törölt tárolók listába sorolva](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Tároló-hozzáférési szabályzat módosított esemény közzétéve |

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
| **KeyPurge** |[Kulcs végleges végleges kiürítés](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Kulcs biztonsági mentése](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Kulcs helyreállítása](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Kulcs helyreállítása](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted (Kulcs törlése)** |[Törölt kulcs lekérte](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted (Kulcslista törölve)** |[Egy tároló törölt kulcsait listába sorolja](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Közzétett kulcs a lejárathoz közeli eseményhez |
| **KeyExpiredEventGridNotification** |Kulcs lejárt esemény közzétéve |

# <a name="secrets"></a>[Titkos kódok](#tab/Secrets)

| operationName | REST API parancs |
| --- | --- |
| **SecretSet** |[Titkos kulcs létrehozása](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Titkos secret lekért](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Titkos kulcs frissítése](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Titkos kulcs törlése](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Egy tároló titkos kulcsainak listázása](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Titkos kulcs verzióinak listázása](/rest/api/keyvault/getsecretversions) |
| **SecretPurge (Titkos vásárlás)** |[Titkos titok végleges kiürítés](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Titkos adatok biztonsági mentése](/rest/api/keyvault/backupsecret) |
| **SecretRestore (Titkos kulcstár)** |[Titkos adatok visszaállítása](/rest/api/keyvault/restoresecret) |
| **SecretRecover (Titkos feladatátvétel)** |[Titkos adatok helyreállítása](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted (Titkos adatokat törölve)** |[Törölt titkos adatokat lekért](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted (Titkos listára törölve)** |[Egy tároló törölt titkos kulcsának listába való belistzása](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |A titkos adatok lejáratának közeledtére tett esemény közzé van téve |
| **SecretExpiredEventGridNotification** |Lejárt titkos esemény közzétéve |

# <a name="certificates"></a>[Tanúsítványok](#tab/Cerificates)

| operationName | REST API parancs |
| --- | --- |
| **CertificateGet (Tanúsítvány lekérve)** |[Tanúsítványsal kapcsolatos információk lekéri](/rest/api/keyvault/getcertificate) |
| **TanúsítványLétrehozás** |[Tanúsítvány létrehozása](/rest/api/keyvault/createcertificate) |
| **Tanúsítványimportálás** |[Tanúsítvány importálása tárolóba](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate (Tanúsítványupdate)** |[Tanúsítvány frissítése](/rest/api/keyvault/updatecertificate) |
| **Tanúsítványlista** |[Tárolóban található tanúsítványok felsorolása](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions (Tanúsítványlistaverziók)** |[Egy tanúsítvány verzióinak felsorolása](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete (Tanúsítvány-visszatűnés)** |[Tanúsítvány törlése](/rest/api/keyvault/deletecertificate) |
| **Tanúsítványvásárlás** |[Tanúsítvány végleges végleges végleges kiürítés](/rest/api/keyvault/purgedeletedcertificate) |
| **Tanúsítvány-visszacsatolás** |[Tanúsítvány biztonsági mentése](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore (Tanúsítvány-adattár)** |[Tanúsítvány visszaállítása](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover (Tanúsítvány-visszavétel)** |[Tanúsítvány helyreállítása](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted (Tanúsítvány törlése)** |[Törölt tanúsítvány lekérve](/rest/api/keyvault/getdeletedcertificate) |
| **Tanúsítványlista törölve** |[List thedeleted certificates in a vault (Tároló törölt tanúsítványai)](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Tanúsítvány-házirend lekérve](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate (TanúsítványpolicyUpdate)** |[Tanúsítvány-házirend frissítése](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet (TanúsítványpolicySet)** |[Tanúsítvány-házirend létrehozása](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Tanúsítvány névjegyének lekérni](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Tanúsítvány névjegyének beállítása](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Tanúsítvány névjegyének törlése](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Tanúsítványkiállító lekéri](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Tanúsítványkiállító beállítása](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Tanúsítványkiállító frissítése](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Tanúsítványkiállító törlése](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[A tanúsítványkiállítók felsorolása](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll (Tanúsítványigénylés)** |Tanúsítvány regisztrálása |
| **TanúsítványÚj** |Tanúsítvány megújítása |
| **CertificatePendingGet (Tanúsítvány lekérve)** |Függőben lévő tanúsítvány lekérése |
| **CertificatePendingMerge (Tanúsítvány függőben)** |Tanúsítványegyesítés függőben |
| **CertificatePendingUpdate (Tanúsítvány függőben lévőupdate)** |Tanúsítványfrissítés függőben |
| **CertificatePendingDelete (Tanúsítvány függőben)** |Függőben lévő tanúsítvány törlése |
| **CertificateNearExpiryEventGridNotification** |A tanúsítvány hamarosan lejár– közzétett esemény |
| **CertificateExpiredEventGridNotification** |Lejárt tanúsítvány közzététele |

---

## <a name="use-azure-monitor-logs"></a>Az Azure Monitor-naplók használata

A naplókban Key Vault megoldással Azure Monitor áttekintheti a Key Vault `AuditEvent` naplókat. A Azure Monitor naplókban naplólekérdezésekkel elemezheti az adatokat, és lekérdezheti a szükséges információkat. 

További információért, beleértve a beállítását is, tekintse meg a Azure Key Vault [a Azure Monitor.](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="next-steps"></a>Következő lépések

- [A naplózás Key Vault engedélyezése](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- A .NET-webalkalmazásban Azure Key Vault oktatóanyagért lásd: Use Azure Key Vault from a web application (Webalkalmazásból származó [Azure Key Vault használata).](tutorial-net-create-vault-azure-web-app.md)
- Programozási hivatkozások: [Azure Key Vault developer’s guide](developers-guide.md) (Az Azure Key Vault fejlesztői útmutatója).
- A Azure PowerShell 1.0-s parancsmagok listáját a Azure Key Vault parancsmagok [Azure Key Vault meg.](/powershell/module/az.keyvault/#key_vault)