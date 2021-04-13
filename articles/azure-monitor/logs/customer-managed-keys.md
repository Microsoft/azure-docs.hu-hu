---
title: Azure Monitor – ügyfél által kezelt kulcs
description: Információk és lépések a Felhasználó által felügyelt kulcs log Analytics-munkaterületeken lévő adatok titkosításához egy Azure Key Vault kulcs használatával.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364855"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor – ügyfél által kezelt kulcs 

A Azure Monitor Microsoft által kezelt kulcsokkal vannak titkosítva. A saját titkosítási kulcsát használhatja a munkaterületeken lévő adatok és mentett lekérdezések védelmére. Ha felhasználó által kezelt kulcsot ad meg, a rendszer ezzel a kulccsal védi és vezéreli az adatokhoz való hozzáférést, és a konfigurálás után a munkaterületre küldött összes adat titkosítva lesz a Azure Key Vault kulccsal. A felhasználó által kezelt kulcsokkal rugalmasabban kezelhető a hozzáférés-vezérlés.

Javasoljuk, hogy a konfigurálás előtt tekintse át az alábbi [korlátozásokat](#limitationsandconstraints) és korlátozásokat.

## <a name="customer-managed-key-overview"></a>Felhasználó által kezelt kulcsok áttekintése

[Az rest-titkosítás](../../security/fundamentals/encryption-atrest.md) gyakori adatvédelmi és biztonsági követelmény a szervezeteknél. Az Azure-nak teljes mértékben felügyelheti az adattitkosítást, miközben számos lehetőség áll rendelkezésre a titkosítási és titkosítási kulcsok szoros kezelésére.

Azure Monitor biztosítja, hogy az összes adat és mentett lekérdezés titkosítva legyen a Microsoft által felügyelt kulcsokkal (MMK). Azure Monitor az Azure Key Vault-ban tárolt saját kulcs használatával is lehetővé [](../../key-vault/general/overview.md)teszi a titkosítást, így bármikor visszavonhatja az adatokhoz való hozzáférést. Azure Monitor titkosítás használata megegyezik az [Azure Storage-titkosítás működésével.](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption)

Az ügyfél által felügyelt [](./logs-dedicated-clusters.md) kulcs dedikált fürtökön van kézbesítve, így magasabb védelmi szintet és irányítást biztosít. A dedikált fürtökbe betöltött adatokat a rendszer kétszer titkosítja – egyszer a szolgáltatás szintjén a Microsoft által felügyelt vagy ügyfél által kezelt kulcsokkal, egyszer pedig infrastruktúraszinten két különböző titkosítási algoritmus és két különböző kulcs használatával. [A dupla titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) védelmet nyújt az olyan forgatókönyvek ellen, ahol az egyik titkosítási algoritmus vagy kulcs biztonsága sérülhet. Ebben az esetben a további titkosítási réteg továbbra is védi az adatokat. A dedikált fürt az adatok védelmét is lehetővé teszi a [Lockbox vezérlővel.](#customer-lockbox-preview)

Az elmúlt 14 napban betöltött adatokat a rendszer a gyors gyorsítótárban (SSD-alapú) is megtartja a hatékony lekérdezési motorművelet érdekében. Ezek az adatok az ügyfél által felügyelt kulcskonfigurációtól függetlenül Microsoft-kulcsokkal titkosítva maradnak, de az SSD-adatok fölötti irányítás megfelel a [kulcs-visszavonásnak.](#key-revocation) Dolgozunk azon, hogy 2021 első felében ügyfél által kezelt kulccsal titkosított SSD-adatokkal dolgozunk.

A dedikált Log Analytics-fürtök a napi 1000 GB-os kapacitásfoglalási díjszabási modellt használják. [](./logs-dedicated-clusters.md#cluster-pricing-model)

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>A felhasználó által felügyelt kulcs működése a Azure Monitor

Azure Monitor felügyelt identitással biztosít hozzáférést a Azure Key Vault. A Log Analytics-fürt identitása fürtszinten támogatott. Annak érdekében, hogy az ügyfél által felügyelt kulcsvédelem több munkaterületen is lehetővé tegye, egy új Log  Analytics-fürterőforrás köztes identitáskapcsolatként teljesít a Key Vault és a Log Analytics-munkaterületek között. A fürt tárolója a fürterőforráshoz társított felügyelt identitást használja a hitelesítéshez a Azure Key Vault \' Azure Active Directory.  

Az Ügyfél által felügyelt kulcs konfigurálása után a dedikált fürthöz kapcsolt munkaterületeken az új betöltött adatokat a rendszer az Ön kulcsával titkosítja. A munkaterületeket bármikor leválaszthatja a fürtről. Az új adatok ezután a Log Analytics-tárolóba vannak beolvasva, és Microsoft-kulccsal vannak titkosítva, miközben zökkenőmentesen lekérdezheti az új és a régi adatokat.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcs képessége regionális. A Azure Key Vault, a fürtöknek és a csatolt Log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de eltérő előfizetésben is lehetnek.

![Az ügyfél által kezelt kulcsok áttekintése](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log *Analytics-fürterőforrás,* amely felügyelt identitással rendelkezik Key Vault – Az identitás propagálva lesz a dedikált Log Analytics-fürttárolóba
3. Dedikált Log Analytics-fürt
4. Fürterőforráshoz csatolt *munkaterületek* 

### <a name="encryption-keys-operation"></a>Titkosítási kulcsok működése

A Storage-adattitkosítás háromféle kulcsot tartalmaz:

- **KEK** – Kulcstitkosítási kulcs (az Ügyfél által felügyelt kulcs)
- **AEK –** Fióktitkosítási kulcs
- **DEK** – Adattitkosítási kulcs

A következő szabályok érvényesek:

- A Log Analytics-fürt tárfiókja minden tárfiókhoz egyedi titkosítási kulcsot hoz létre, amelyet AEK-nek is nevezik.
- Az AEK az adattitkok származtatására használható, amelyek a lemezre írt adatblokkok titkosítására használt kulcsok.
- Amikor konfigurálja a kulcsot az Key Vault-ban, és hivatkozik rá a fürtben, az Azure Storage kéréseket küld az Azure Key Vault-nek az AEK becsomagolása és kicsomagzása érdekében az adattitkosítási és -visszafejtési műveletek végrehajtásához.
- A KEK soha nem hagyja el a Key Vault.
- Az Azure Storage a fürterőforráshoz  társított felügyelt identitást használja a hitelesítéshez és a Azure Key Vault hozzáféréséhez Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed kulcs üzembe Customer-Managed lépései

1. Kulcs Azure Key Vault és tárolása
1. Fürt létrehozása
1. Engedélyek megadása a Key Vault
1. Fürt frissítése a kulcsazonosító részleteivel
1. Log Analytics-munkaterületek összekapcsolása

Az ügyfél által felügyelt kulcs konfigurálása jelenleg nem támogatott a Azure Portal és a kiépítés [PowerShell-,](/powershell/module/az.operationalinsights/) [CLI-](/cli/azure/monitor/log-analytics) vagy [REST-kérésekkel](/rest/api/loganalytics/) hajtható végre.

### <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapotellenőrzés

Egyes konfigurációs lépések aszinkron módon futnak, mert nem használhatja őket gyorsan. A válasza a következők egyike `status` lehet: "InProgress", "Frissítés", "Törlés", "Sikeres" vagy "Sikertelen" hibakóddal.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="rest"></a>[REST](#tab/rest)

REST használata esetén a válasz először a 202 (Elfogadva) HTTP-állapotkódot és az *Azure-AsyncOperation* tulajdonsággal megadott fejlécet adja vissza:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Az aszinkron művelet állapotát úgy ellenőrizheti, hogy egy GET kérést küld a végpontnak az *Azure-AsyncOperation fejlécben:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Létrehozhat vagy használhat egy Azure Key Vault, amit már létre kell hoznia, vagy importálhatja az adattitkosításhoz használt kulcsot. A Azure Key Vault konfigurálni kell helyreállíthatóként a kulcs és az adatokhoz való hozzáférés védelme érdekében a Azure Monitor. Ezt a konfigurációt a konfiguráció tulajdonságai között ellenőrizheti,  Key Vault a Törlés *és* a Végleges törlés elleni védelmet is engedélyezni kell.

![A törlés és a végleges törlés elleni védelem beállításai](media/customer-managed-keys/soft-purge-protection.png)

Ezek a beállítások a parancssori felület Key Vault PowerShell használatával frissíthetők:

- [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
- [A végleges törlés elleni](../../key-vault/general/soft-delete-overview.md#purge-protection) védelem a titkos kulcs/tároló kényszerített törlését a törlés után is védi

## <a name="create-cluster"></a>Fürt létrehozása

A fürtök két felügyelt [identitástípust támogatnak:](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)a rendszer által hozzárendelt és a felhasználó által hozzárendelt identitást, míg a forgatókönyvtől függően a fürtben egyetlen identitás is definiálható. 
- A rendszer által hozzárendelt felügyelt identitás egyszerűbb, és automatikusan jön létre a fürtlétrehozás során, ha az identitás `type` *"SystemAssigned" (Rendszer által hozzárendelt)* értékre van állítva. Ez az identitás később arra használható, hogy tárolási hozzáférést biztosítsunk a Key Vault a burkot és a kicsomagot burkoló műveletekhez. 
  
  Identitásbeállítások a fürtben a rendszer által hozzárendelt felügyelt identitáshoz
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Ha a fürt létrehozásakor ügyfél által felügyelt kulcsot szeretne konfigurálni, akkor az Key Vault-ban előre meg kell adni egy kulcsot és egy felhasználó által hozzárendelt identitást, majd létre kell hoznia a fürtöt a következő beállításokkal: identity `type` as "*UserAssigned*", az identitás erőforrás-azonosítójával. `UserAssignedIdentities` 

  Felhasználó által hozzárendelt felügyelt identitás identitásbeállítása a fürtben
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Nem használhat felhasználó által hozzárendelt felügyelt identitást, ha a Key Vault található Private-Link (vNet). Ebben a forgatókönyvben használhatja a rendszer által hozzárendelt felügyelt identitást.

Kövesse a Dedikált fürtök cikkben [látható eljárást.](./logs-dedicated-clusters.md#creating-a-cluster) 

## <a name="grant-key-vault-permissions"></a>Engedélyek Key Vault megadása

Hozzon létre hozzáférési szabályzatot a Key Vault, hogy engedélyeket biztosítson a fürtnek. Ezeket az engedélyeket a tároló Azure Monitor használja. Nyissa meg a Key Vault a Azure Portal kattintson a *"Hozzáférési szabályzatok"* elemre, majd a *"+ Hozzáférési* szabályzat hozzáadása" elemre egy szabályzat létrehozásához a következő beállításokkal:

- Kulcsengedélyek: válassza a *Get,* *a Wrap Key és* a *Unwrap Key lehetőséget.*
- Rendszerbiztonsági tag kiválasztása: A fürtben használt identitástípustól (rendszer- vagy felhasználó által hozzárendelt felügyelt identitás) függően adja meg a fürt nevét vagy a rendszer által hozzárendelt felügyelt identitás vagy a felhasználó által hozzárendelt felügyelt identitás nevét.

![engedélyek Key Vault megadása](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault konfigurálva van-e helyreállíthatóként a kulcs és a Azure Monitor hozzáférésének védelme érdekében.

## <a name="update-cluster-with-key-identifier-details"></a>Fürt frissítése a kulcsazonosító részleteivel

A fürt összes műveletéhez műveleti `Microsoft.OperationalInsights/clusters/write` engedélyre van szükség. Ez az engedély a műveletet tartalmazó Tulajdonos vagy Közreműködő szerepkörön vagy a műveletet tartalmazó Log Analytics-közreműködői szerepkörön keresztül `*/write` adható `Microsoft.OperationalInsights/*` meg.

Ez a lépés frissíti Azure Monitor Storage-tárfiókot az adattitkosításhoz használt kulccsal és verzióval. A frissítéskor a rendszer az új kulcsot használja a Storage-kulcs (AEK) becsomagolása és kicsomaglása érdekében.

Válassza ki a kulcs aktuális verzióját a Azure Key Vault a kulcs azonosítójának részleteiért.

![Engedélyek Key Vault jogosultságok megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a KeyVaultProperties adatokat a fürtben a kulcsazonosító részleteivel.

>[!NOTE]
>A kulcsrotáció két módot támogat: automatikus [](#key-rotation) rotációt vagy explicit kulcsverzió-frissítést. A legjobb módszer meghatározásához tekintse meg a kulcsrotációt.

A művelet aszinkron, és a befejezése több ideig is igénybe vehet.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Válasz**

A kulcs propagálása néhány percet vesz igénybe. A frissítési állapotot kétféleképpen ellenőrizheti:
1. Másolja ki a Azure-AsyncOperation URL-címét a válaszból, és kövesse [az aszinkron műveleti állapotellenőrzést.](#asynchronous-operations-and-status-check)
2. Küldjön egy GET kérést a fürtön, és nézze meg a *KeyVaultProperties tulajdonságait.* A nemrégiben frissített kulcsnak vissza kell térnie a válaszban.

A GET kérésre adott válasznak a következőnek kell lennie a kulcsfrissítés befejezésekor: 202 (Elfogadva) és fejléc
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Munkaterület csatolása fürthöz

> [!IMPORTANT]
> Ezt a lépést csak a Log Analytics-fürt kiépítésének befejezése után kell végrehajtani. Ha munkaterületeket kapcsol össze és az adatokat a kiépítés előtt fogadja, a betöltött adatok el lesznek dobva, és nem állíthatók helyre.

A művelet végrehajtásához a munkaterületre és a fürtre is "írási" engedélyekkel kell rendelkeznie, amelyek közé tartozik a és a `Microsoft.OperationalInsights/workspaces/write` `Microsoft.OperationalInsights/clusters/write` .

Kövesse a Dedikált fürtök cikkben [látható eljárást.](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)

## <a name="key-revocation"></a>Kulcs visszavonása

> [!IMPORTANT]
> - Az adatokhoz való hozzáférés visszavonásának ajánlott módja a kulcs letiltása vagy a hozzáférési szabályzat törlése a Key Vault.
> - A fürtök "Nincs" beállításával az adatokhoz való hozzáférés is vissza lesz vonva, de ez a megközelítés nem ajánlott, mivel nem lehet visszavonni a visszavonást, amikor a fürtben a kérést a támogatási kérelem megnyitása nélkül állítja `identity` `type` `identity` vissza.

A fürt tárterülete egy órán belül mindig tiszteletben tartja a kulcsengedélyek változásait, és a tárterület elérhetetlenné válik. A fürthöz csatolt munkaterületekre betöltött új adatok el lesznek dobva, és nem állíthatók helyre, az adatok elérhetetlenné válnak, és a munkaterületeken való lekérdezések meghiúsulnak. A korábban betöltött adatok addig maradnak a tárolóban, amíg a fürt és a munkaterületek nem törlődnek. A nem elérhető adatokat az adatmegőrzési szabályzat szabályozza, és a megőrzés elérésekor törlődnek. Az elmúlt 14 napban betöltött adatokat a rendszer a gyors gyorsítótárban (SSD-alapú) is megtartja a hatékony lekérdezési motorművelet érdekében. Ez a kulcs-visszavonási művelet során törlődik, és elérhetetlenné válik.

A fürt tárolója rendszeresen ellenőrzi a Key Vault, hogy megpróbálja kicsomaglani a titkosítási kulcsot, és ha hozzáfért, az adatbecsülés és a lekérdezés 30 percen belül folytatódik.

## <a name="key-rotation"></a>Kulcsrotálás

A kulcsrotációnak két módja van: 
- Automatikus rotáció – ha a fürtöt a használatával frissíti, de kihagyja a tulajdonságot, vagy a tulajdonságot a következőre adja meg: , a tároló automatikusan a legújabb verziókat ```"keyVaultProperties"``` ```"keyVersion"``` fogja ```""``` használni.
- Explicit kulcsverzió-frissítés – amikor frissíti a fürtöt, és a tulajdonságban adja meg a kulcsverziót, minden új kulcsverzióhoz explicit frissítés szükséges a fürtben, lásd: Fürt frissítése kulcsazonosító ```"keyVersion"``` ```"keyVaultProperties"``` [részleteivel.](#update-cluster-with-key-identifier-details) Ha új kulcsverziót hoz létre a Key Vault de nem frissíti a fürtben, a Log Analytics-fürt tárolója továbbra is az előző kulcsot fogja használni. Ha a fürtben az új kulcs frissítése előtt letiltja vagy törli a régi kulcsot, a kulcs visszavonási [állapotba kerül.](#key-revocation)

A kulcsrotációs művelet után minden adat elérhető marad, mivel az adatok mindig fióktitkosítási kulccsal (AEK) vannak titkosítva, az AEK titkosítása pedig az új kulcstitkosítási kulcs (KEK) verziójával történik a Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Felhasználó által felügyelt kulcs mentett lekérdezésekhez

A Log Analyticsben használt lekérdezési nyelv kifejező jellegű, és bizalmas információkat tartalmazhat a lekérdezésekhez vagy a lekérdezési szintaxishoz küldött megjegyzésekben. Egyes szervezetek megkövetelik, hogy az ilyen információk védve maradnak az Ügyfél által felügyelt kulcs házirendben, önnek pedig titkosítania kell a lekérdezéseket a kulcsával. Azure Monitor segítségével a saját  kulcsával titkosított mentett kereséseket és *naplóriasztásokat* tárolhatja a saját tárfiókjában, amikor a munkaterülethez csatlakozik. 

> [!NOTE]
> A Log Analytics-lekérdezések a használt forgatókönyvtől függően különböző tárolókba menthetők. A lekérdezések titkosítása Microsoft-kulccsal (MMK) történik az alábbi forgatókönyvekben, függetlenül az ügyfél által felügyelt kulcs konfigurációtól: Munkafüzetek az Azure Monitor-ban, Azure-irányítópultok, Azure Logic App, Azure Notebooks és Automation-runbookok.

Ha saját tárolót (BYOS) hoz létre, és a  munkaterülethez csatolja, a szolgáltatás mentett kereséseket és *naplóriasztásokat* tölt fel a tárfiókba. Ez azt jelenti, hogy a tárfiókot és az adattitkosítási szabályzatot ugyanazokkal a kulcsokkal vezéreli, mint a Log [Analytics-fürtben](../../storage/common/customer-managed-keys-overview.md) található adatok titkosításához, vagy egy másik kulcsot. Azonban Ön a felelős a tárfiókhoz kapcsolódó költségekért. 

**A felhasználó által kezelt kulcs lekérdezésekhez való beállítása előtt megfontolandó szempontok**
* A munkaterülethez és a Storage-fiókhoz is "írási" engedélyekkel kell rendelkeznie
* Győződjön meg arról, hogy a Storage-fiókot ugyanabban a régióban hozza létre, mint ahol a Log Analytics-munkaterület található.
* A *tárolóban a mentések keresései* szolgáltatás-összetevőknek minősülnek, és a formátumuk megváltozhat
* A *meglévő mentések keresései* el vannak távolítva a munkaterületről. Másolja és *mentse a konfiguráció előtt* szükséges kereséseket. A mentett *kereséseket a* [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) használatával is megtekintheti
* A lekérdezési előzmények nem támogatottak, és nem fogja látni a futtatott lekérdezéseket
* A lekérdezések mentése céljából egyetlen tárfiókot is csatolhat a munkaterülethez, de a mentett és a naplóriasztásos lekérdezések is *használhatók* 
* Az irányítópulton való rögzítés nem támogatott

**BYOS konfigurálása mentett keresésekhez**

Csatolja a *Lekérdezés tárfiókot* a munkaterülethez – a *mentett keresések* lekérdezései a tárfiókban vannak mentve. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

A konfiguráció után  az új mentett keresési lekérdezések a tárolóba lesznek mentve.

**BYOS konfigurálása naplóriasztásos lekérdezésekhez**

Tárfiók csatolása a *riasztások számára* a munkaterülethez – a *naplóriasztásos* lekérdezések a tárfiókba vannak mentve. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

A konfiguráció után minden új riasztáslekérdezés mentve lesz a tárolóban.

## <a name="customer-lockbox-preview"></a>Ügyfélszéf (előzetes verzió)

A Lockbox lehetővé teszi, hogy jóváhagyja vagy elutasítsa a Microsoft mérnökének az adatokhoz való hozzáférésre vonatkozó kérelmét egy támogatási kérés során.

Ebben Azure Monitor a dedikált Log Analytics-fürthöz csatolt munkaterületeken lévő adatokra vonatkozó szabályozást. A Lockbox vezérlő a dedikált Log Analytics-fürtben tárolt adatokra vonatkozik, amelyek a fürt tárfiókjaiban vannak elkülönítve a Lockbox által védett előfizetés alatt.  

További információ a [Ügyfélszéf való Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed kulcsműveletek

Customer-Managed kulcs dedikált fürtön található, és ezeket a műveleteket a dedikált fürtökről [cikkben olvashatja](./logs-dedicated-clusters.md#change-cluster-properties)

- Az erőforráscsoportban az összes fürt lekérte  
- Az előfizetésben az összes fürt lekérte
- Kapacitásfoglalás *frissítése a* fürtben
- A *billingType frissítése a* fürtben
- Munkaterület leválasztása a fürtről
- Fürt törlése

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A fürtök maximális száma régiónként és előfizetésenként 2

- Egy fürthöz legfeljebb 1000 munkaterület csatolható

- Összekapcsolhat egy munkaterületet a fürttel, majd leválaszthatja azt. A munkaterület-hivatkozási műveletek száma egy adott munkaterületen 2-re van korlátozva 30 napon belül.

- Az ügyfél által kezelt kulcstitkosítás a konfigurációs idő után újonnan betöltött adatokra vonatkozik. A konfiguráció előtt betöltött adatok Microsoft-kulccsal vannak titkosítva. Zökkenőmentesen lekérdezheti az ügyfél által kezelt kulcs konfigurálása előtt és után betöltött adatokat.

- A Azure Key Vault konfigurált helyreállíthatóként kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a parancssori felület vagy a PowerShell használatával kell konfigurálni:<br>
  - [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
  - [A végleges törlés elleni](../../key-vault/general/soft-delete-overview.md#purge-protection) védelmet be kell kapcsolva, hogy a titkos kulcs/tároló kényszerített törlésével szemben a törlés után is védelmet nyújtsunk.

- A fürtök másik erőforráscsoportba vagy előfizetésbe való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, fürtöknek és munkaterületeknek ugyanabban a régióban és ugyanabban a Azure Active Directory-bérlőben (Azure AD) kell lennie, de különböző előfizetések is lehetnek.

- A zárolási doboz jelenleg nem érhető el Kínában. 

- [A dupla titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) automatikusan konfigurálva van a támogatott régiókban 2020 októberében létrehozott fürtökhöz. Ha ellenőrizni tudja, hogy a fürt kettős titkosításra van-e konfigurálva, egy GET kérést küld a fürtre, és megfigyelheti, hogy az érték olyan fürtökre van beállítva, amelyeken engedélyezve van a dupla `isDoubleEncryptionEnabled` `true` titkosítás. 
  - Ha létrehoz egy fürtöt, és a "<régiónév> nem támogatja a fürtök dupla titkosítását" hibaüzenetet kap, akkor is létrehozhatja a fürtöt dupla titkosítás nélkül, ha hozzáadja a kódot a `"properties": {"isDoubleEncryptionEnabled": false}` REST-kérelem törzséhez.
  - A dupla titkosítási beállítás a fürt létrehozása után nem módosítható.

  - Ha a fürt felhasználó által hozzárendelt felügyelt identitással van beállítva, a beállítása felfüggeszti a fürtöt, és megakadályozza az adatokhoz való hozzáférést, de nem állíthatja vissza a visszavonást, és nem aktiválhatja a fürtöt a támogatási kérelem megnyitása `UserAssignedIdentities` `None` nélkül. Ez a korlátozás a rendszer által hozzárendelt felügyelt identitásra vonatkozik.

  - Nem használhat felhasználó által felügyelt kulcsot felhasználó által hozzárendelt felügyelt identitással, ha a Key Vault található Private-Link (vNet). Ebben a forgatókönyvben használhatja a rendszer által hozzárendelt felügyelt identitást.

## <a name="troubleshooting"></a>Hibaelhárítás

- Viselkedés a Key Vault rendelkezésre állásával
  - Normál működés esetén – A tároló rövid ideig gyorsítótárazza az AEK-t, és rendszeres időközönként visszatér Key Vault kicsomaglása érdekében.
    
  - Átmeneti kapcsolati hibák – A tárterület úgy kezeli az átmeneti hibákat (időtúllépéseket, kapcsolati hibákat, DNS-problémákat), hogy lehetővé teszi, hogy a kulcsok egy rövid ideig a gyorsítótárban maradjanak, és ez áthidalja a rendelkezésre állás esetleges apró hibáit. A lekérdezési és a belési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces elérhetetlenség miatt a Tárfiók elérhetetlenné válik. A lekérdezési funkció nem érhető el, és a betöltött adatok több órán át gyorsítótárazva lettek a Microsoft Key használatával az adatvesztés elkerülése érdekében. A Key Vault visszaállításakor a lekérdezés elérhetővé válik, és az ideiglenesen gyorsítótárazott adatok be vannak olva az adattárba, és az ügyfél által felügyelt kulccsal vannak titkosítva.

  - Key Vault hozzáférési sebesség – A Storage Azure Monitor hozzáférési gyakorisága Key Vault 6 és 60 másodperc közötti lehet.

- Ha frissíti a fürtöt, miközben a fürt kiépítési vagy frissítési állapotban van, a frissítés sikertelen lesz.

- Ha ütközési hibát kap a fürt létrehozásakor – Előfordulhat, hogy az elmúlt 14 napban törölte a fürtöt, és a törlési időszakban volt. A fürt neve továbbra is fenntartva marad a törlési időszak alatt, és nem hozhat létre új fürtöt ezzel a névvel. A név a törlési időszak után szabadul fel, amikor a fürt véglegesen törölve lesz.

- A munkaterület fürtre mutató hivatkozása sikertelen lesz, ha egy másik fürthöz van kapcsolva.

- Ha létrehoz egy fürtöt, és azonnal megadja a KeyVaultProperties értéket, a művelet meghiúsulhat, mivel a hozzáférési szabályzat csak akkor határozható meg, ha a rendszeridentitás hozzá van rendelve a fürthöz.

- Ha a meglévő fürtöt a KeyVaultProperties rel frissíti, és hiányzik a "Get" kulcs hozzáférési szabályzata a Key Vault, a művelet sikertelen lesz.

- Ha nem tudja üzembe helyezni a fürtöt, ellenőrizze, hogy a Azure Key Vault, a fürt és a csatolt Log Analytics-munkaterületek azonos régióban vannak-e. A különböző előfizetésekbe is lehet.

- Ha az Key Vault-ban frissíti a kulcsverziót, és nem frissíti az új kulcsazonosító részleteit a fürtben, a Log Analytics-fürt továbbra is az előző kulcsot fogja használni, és az adatok elérhetetlenné válnak. Frissítse a fürt új kulcsazonosító-adatait az adatbevezetés folytatásához és az adatok lekérdezésének lehetőségéhez.

- Egyes műveletek hosszúak, és hosszabb időt is igénybe vehet – ezek a fürt létrehozása, a fürtkulcs frissítése és a fürt törlése. A művelet állapotát kétféleképpen ellenőrizheti:
  1. REST használata esetén másolja ki a Azure-AsyncOperation URL-címét a válaszból, és kövesse az aszinkron műveleti [állapotellenőrzést.](#asynchronous-operations-and-status-check)
  2. Küldjön GET kérést a fürtnek vagy munkaterületnek, és figyelje meg a választ. Például a nem összekapcsolt munkaterületen nem lesz a *clusterResourceId a* *funkciók alatt.*

- Hibaüzenetek
  
  **Fürt létrehozása**
  -  400 – A fürt neve érvénytelen. A fürt neve a–z, A–Z, 0–9 és 3–63 karakter hosszúságú lehet.
  -  400 – A kérés törzse null vagy hibás formátumú.
  -  400 – A termékváltozat neve érvénytelen. Állítsa a termékváltozat nevét capacityReservation névre.
  -  400 – A kapacitás meg lett téve, de a termékváltozat nem capacityReservation. Állítsa a termékváltozat nevét capacityReservation névre.
  -  400 – Hiányzó kapacitás az SKU-ban. Állítsa a Kapacitás értékét 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – A termékváltozatban a kapacitás nincs tartományon belül. Legalább 1000-nek kell lennie, és a maximálisan engedélyezett kapacitásnak kell lennie, amely a munkaterület Használat és becsült költségek alatt érhető el.
  -  400 – A kapacitás 30 napig zárolva van. A kapacitás csökkentése a frissítés után 30 nappal engedélyezett.
  -  400 – Nincs beállítva termékváltozat. Állítsa a termékváltozat nevét capacityReservation értékre, a Kapacitás értékét pedig 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – Az identitás null vagy üres. Az Identitás beállítása systemAssigned típussal.
  -  400 – A KeyVaultProperties a létrehozáskor van beállítva. Frissítse a KeyVaultProperties et a fürt létrehozása után.
  -  400 – A művelet jelenleg nem hajtható végre. Az aszinkron művelet nem sikeres állapotban van. A fürtnek a frissítési művelet előtt be kell fejeződnie.

  **Fürtfrissítés**
  -  400 – A fürt törlési állapotban van. Aszinkron művelet van folyamatban. A fürtnek a frissítési művelet előtt be kell fejeződnie.
  -  400 – A KeyVaultProperties nem üres, de hibás formátummal rendelkezik. Lásd [a kulcsazonosító frissítését.](#update-cluster-with-key-identifier-details)
  -  400 – A kulcs érvényesítése sikertelen volt a Key Vault. Ennek oka lehet az engedélyek hiánya, vagy ha a kulcs nem létezik. Ellenőrizze, hogy [beállította-e a kulcs- és hozzáférési](#grant-key-vault-permissions) Key Vault.
  -  400 – A kulcs nem állítható helyre. Key Vault a Soft-delete és a Purge-protection (Végleges törlés és végleges törlés elleni védelem) beállításra kell állítani. Lásd [Key Vault dokumentációt](../../key-vault/general/soft-delete-overview.md)
  -  400 – A művelet jelenleg nem hajtható végre. Várjon, amíg az Async művelet befejeződik, majd próbálkozzon újra.
  -  400 – A fürt törlési állapotban van. Várjon, amíg az Async művelet befejeződik, majd próbálkozzon újra.

  **Fürt lekért**
    -  404 – A fürt nem található, lehet, hogy a fürt törölve lett. Ha ezzel a névvel próbál fürtöt létrehozni, és ütközést kap, a fürt 14 napig nem törölhető. A helyreállításhoz vegye fel a kapcsolatot az ügyfélszolgálattal, vagy egy másik névvel hozzon létre egy új fürtöt. 

  **Fürt törlése**
    -  409 – Kiépítési állapotban nem lehet fürtöt törölni. Várjon, amíg az Async művelet befejeződik, majd próbálkozzon újra.

  **Munkaterület hivatkozása**
  -  404 – A munkaterület nem található. A megadott munkaterület nem létezik vagy törölve lett.
  -  409 – A munkaterület csatolása vagy leválasztása folyamatban van.
  -  400 – A fürt nem található, a megadott fürt nem létezik vagy törölve lett. Ha ezzel a névvel próbál fürtöt létrehozni, és ütközést kap, a fürt 14 napig nem törölhető. A helyreállításhoz forduljon az ügyfélszolgálathoz.

  **Munkaterület leválasztása**
  -  404 – A munkaterület nem található. A megadott munkaterület nem létezik vagy törölve lett.
  -  409 – A munkaterület csatolása vagy leválasztása folyamatban van.
## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Log Analytics dedikált fürtök számlázásával kapcsolatban](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- A [Log Analytics-munkaterületek megfelelő kialakításának megismerése](./design-logs-deployment.md)