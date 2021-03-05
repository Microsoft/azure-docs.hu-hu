---
title: Az Azure Managed HSM naplózása
description: Ez az oktatóanyag segítséget nyújt a felügyelt HSM-naplózás használatának megkezdésében.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7420ffbe5b365c635c1eac2620cfd54ceb649ebf
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211804"
---
# <a name="managed-hsm-logging"></a>Felügyelt HSM-naplózás 

Egy vagy több felügyelt HSM létrehozása után valószínűleg figyelnie kell, hogy a HSMss hogyan és mikor legyenek elérhetők, és kinek. Ezt a naplózás engedélyezésével teheti meg, amely az Ön által megadott Azure Storage-fiókban tárolja az adatokat. A megadott Storage-fiókhoz automatikusan létrejön egy elemzések – **naplók – AuditEvent** nevű új tároló. Ugyanezt a Storage-fiókot használhatja a több felügyelt HSM tartozó naplók összegyűjtéséhez.

A naplózási adatokat a felügyelt HSM-művelet után 10 percen belül elérheti. A legtöbb esetben azonban ez nem fog ennyi ideig tartani.  A naplókat a tárfiókban kezelheti:

* A standard Azure-beli hozzáférés-vezérlési módszerekkel szabályozhatja, hogy kik férhetnek hozzá a naplókhoz.
* Ha már nincs szüksége a tárfiókban tárolt naplókra, törölje azokat.

Ez az oktatóanyag segítséget nyújt a felügyelt HSM-naplózás használatának megkezdésében. Hozzon létre egy Storage-fiókot, engedélyezze a naplózást, és értelmezze az összegyűjtött napló adatait.  

> [!NOTE]
> Ez az oktatóanyag nem tartalmaz útmutatást a felügyelt HSM vagy kulcsok létrehozásához. Ez a cikk az Azure CLI-utasításokat tartalmazza a diagnosztikai naplózás frissítéséhez.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő elemek szükségesek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure CLI verziója 2.12.0 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Felügyelt HSM az előfizetésében. Lásd [: gyors útmutató: felügyelt HSM kiépítése és aktiválása az Azure CLI használatával](quick-create-cli.md) a FELÜGYELt HSM üzembe helyezéséhez és aktiválásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

A legfontosabb naplózás beállításának első lépése az Azure CLI átirányítása a naplózni kívánt felügyelt HSM-be.

```azurecli-interactive
az login
```

Ha további információt szeretne a bejelentkezési lehetőségekről a CLI-n keresztül, tekintse meg az [Azure CLI-vel való bejelentkezést](/cli/azure/authenticate-azure-cli) ismertető részt.

Előfordulhat, hogy meg kell adnia a felügyelt HSM létrehozásához használt előfizetést. Adja meg a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

## <a name="identify-the-managed-hsm-and-storage-account"></a>A felügyelt HSM-és Storage-fiók azonosítása

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Naplózás engedélyezése

A felügyelt HSM naplózásának engedélyezéséhez használja az az **monitor Diagnostics-Settings Create** parancsot az új Storage-fiókhoz és a felügyelt HSM-hez létrehozott változókkal együtt. Az **-enabled** jelzőt úgy is beállítjuk, hogy **$true** és a kategóriát a **AUDITEVENT** (a felügyelt HSM-naplózás egyetlen kategóriája) adja meg:

Ez a kimenet megerősíti, hogy a naplózás már engedélyezve van a felügyelt HSM-hez, és az adatokat a Storage-fiókjába menti.

Igény szerint megadhat egy adatmegőrzési házirendet a naplókhoz, így a régebbi naplók automatikusan törlődnek. Például állítsa be az adatmegőrzési házirendet úgy, hogy a **-RetentionEnabled** jelzőt **$true** értékre állítja, és a **-RetentionInDays** paramétert állítsa **90** -re, hogy a 90 napnál régebbi naplók automatikusan törlődjenek.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Mi kerül naplózásra?

* Az összes hitelesített REST API kérelem, beleértve a sikertelen kérelmeket a hozzáférési engedélyek, a rendszerhibák vagy a hibás kérelmek eredményeképpen.
* A felügyelt HSM-ben végzett műveletek, beleértve az olyan attribútumok létrehozását, törlését és frissítését, mint a címkék.
* A biztonsági tartományhoz kapcsolódó műveletek, például az inicializálás & letöltés, a helyreállítás inicializálása, feltöltés
* Teljes HSM biztonsági mentés, visszaállítás és szelektív visszaállítási műveletek
* Kulcsokon végrehajtott műveletek, beleértve a következőket:
  * A kulcsok létrehozása, módosítása vagy törlése.
  * A kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, becsomagolása és kicsomagolása.
  * Kulcs biztonsági mentése, visszaállítása, kiürítése
* A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek olyan tulajdonosi jogkivonattal, amely nem formázott vagy lejárt, vagy érvénytelen tokent tartalmaz.  

## <a name="access-your-logs"></a>A naplók elérése

A felügyelt HSM-naplókat a megadott Storage-fiókban található elemzések – **naplók – AuditEvent** tároló tárolja. A naplók megtekintéséhez le kell töltenie a blobokat. További információ az Azure Storage-ról: [Blobok létrehozása, letöltése és listázása az Azure CLI-vel](../../storage/blobs/storage-quickstart-blobs-cli.md).

Az egyes Blobok szövegként vannak tárolva, és JSON-ként vannak formázva. Nézzük meg egy példa naplóbejegyzést. Az alábbi példa a naplóbejegyzést mutatja, ha a teljes biztonsági mentés létrehozására vonatkozó kérést küld a rendszer a felügyelt HSM-nek.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

A következő táblázat a mezőneveket és a leírásokat tartalmazza:

| Mező neve | Description |
| --- | --- |
| **TenantId** | Azure Active Directory a felügyelt HSM létrehozásához használt előfizetés bérlői AZONOSÍTÓját |
| **idő** |Dátum és idő (UTC). |
| **resourceId** |Azure Resource Manager erőforrás-azonosító. A felügyelt HSM-naplók esetében ez mindig a felügyelt HSM erőforrás-azonosító. |
| **operationName** |A művelet neve, ahogy a következő táblázat is mutatja. |
| **operationVersion** |REST API az ügyfél által kért verziót. |
| **Kategória** |Az eredmény típusa. A felügyelt HSM-naplók esetében a **AuditEvent** az egyetlen elérhető érték. |
| **resultType** |A REST API kérelem eredménye. |
| **Tulajdonságok** |A művelettől (**operationName**) függően változó információk|
| **resultSignature** |A HTTP-állapot. |
| **resultDescription** |Az eredmény további leírása, amennyiben elérhető. |
| **Átl** |A REST API-kérelem végrehajtásának ideje ezredmásodpercben. Ebbe nincs beleszámítva a hálózati késés, így az ügyféloldalon mért idő ettől eltérhet. |
| **callerIpAddress** |Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. |
| **correlationId** |Egy opcionális GUID, amelyet az ügyfél továbbíthat az ügyféloldali naplók és a szolgáltatási oldali naplók összekapcsolásához. |
| **identitás** |Az REST API kérelemben bemutatott jogkivonat identitása. Ez általában egy "felhasználó", "egy egyszerű szolgáltatásnév". |
| **requestUri** | A REST API kérelem URI-ja |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Az Azure Monitor-naplók használata

A felügyelt HSM **AuditEvent** -naplók áttekintéséhez használhatja a Azure monitor-naplók Key Vault megoldását is. Azure Monitor naplókban a naplók segítségével elemezheti az adatokat, és lekérheti a szükséges információkat. 

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a felügyelt HSM üzembe helyezésének és használatának [ajánlott eljárásait](best-practices.md)
- Tudnivalók a felügyelt HSM [biztonsági mentéséről és visszaállításáról](backup-restore.md)
