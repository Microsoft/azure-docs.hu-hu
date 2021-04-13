---
title: Azure Monitor – ügyfél által kezelt kulcs
description: Információk és lépések az ügyfél által felügyelt kulcs konfigurálásához a Log Analytics munkaterületen lévő adatok Azure Key Vault kulcs használatával történő titkosításához.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: fdd62ebfe992398d33d2851a1aa1c66497296b5d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311192"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor – ügyfél által kezelt kulcs 

A Azure Monitorban lévő, a Microsoft által felügyelt kulcsokkal titkosított adatforgalom. Használhatja a saját titkosítási kulcsát a munkaterületeken tárolt és mentett lekérdezések védelme érdekében. Ha ügyfél által felügyelt kulcsot ad meg, a kulcs az adataihoz való hozzáférés védelme és szabályozása, valamint a konfigurálás után a munkaterületekre eljuttatott összes információ titkosítva lesz a Azure Key Vault kulccsal. A felhasználó által kezelt kulcsokkal rugalmasabban kezelhető a hozzáférés-vezérlés.

Javasoljuk, hogy a konfiguráció előtt tekintse át [az alábbi korlátozásokat és korlátozásokat](#limitationsandconstraints) .

## <a name="customer-managed-key-overview"></a>Ügyfél által felügyelt kulcs áttekintése

A inaktív adatok [titkosítása](../../security/fundamentals/encryption-atrest.md) a szervezetek közös adatvédelmi és biztonsági követelménye. Lehetővé teheti, hogy az Azure teljes mértékben kezelhesse a titkosítást, míg számos különböző lehetőség áll rendelkezésre a titkosítási és titkosítási kulcsok szoros kezeléséhez.

Azure Monitor biztosítja, hogy az összes adatok és mentett lekérdezések a Microsoft által felügyelt kulcsok (MMK-EK) használatával titkosítva legyenek. A Azure Monitor a saját [Azure Key Vault](../../key-vault/general/overview.md)tárolt saját kulcs használatával is lehetővé teszi a titkosítást, amely lehetővé teszi a vezérlő számára, hogy bármikor visszavonja az adataihoz való hozzáférést. Azure Monitor a titkosítás használata azonos az [Azure Storage-titkosítás](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) működésének módjával.

Az ügyfél által felügyelt kulcs olyan [dedikált fürtökön](./logs-dedicated-clusters.md) érhető el, amelyek magasabb szintű védelmi szintet és irányítást biztosítanak. A dedikált fürtökbe betöltött adat kétszer, a Microsoft által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal, illetve az infrastruktúra szintjén egyszer, két különböző titkosítási algoritmus és két különböző kulcs használatával titkosítva van. A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) védelmet biztosít olyan esetekben, amikor a titkosítási algoritmusok vagy kulcsok egyike sérül. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait. A dedikált fürt lehetővé teszi, hogy az adatai a [Kulcstároló](#customer-lockbox-preview) -vezérlővel is védve legyenek.

Az elmúlt 14 napban betöltött adatok a hatékony lekérdezési motor működéséhez a gyors gyorsítótárban (SSD-alapú) is megmaradnak. Ezek az [adat a Microsoft](#key-revocation)kulcsaival is titkosítva van, függetlenül az ügyfél által felügyelt kulcs konfigurációjától Dolgozunk arra, hogy az SSD-adatmennyiséget a 2021 első felében az ügyfél által felügyelt kulccsal titkosítjuk.

Log Analytics dedikált fürtök kapacitás foglalási [díjszabási modellt](./logs-dedicated-clusters.md#cluster-pricing-model) használnak, amely 1000 GB/nap.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Az ügyfél által felügyelt kulcs működése Azure Monitor

Azure Monitor felügyelt identitást használ a Azure Key Vaulthoz való hozzáférés biztosításához. A fürt szintjén a Log Analytics-fürt identitása támogatott. Az ügyfél által felügyelt kulcsok több munkaterületen való engedélyezéséhez az új Log Analytics *fürterőforrás* közbenső identitás-kapcsolatként működik a Key Vault és a log Analytics munkaterületek között. A fürt tárterülete a fürt erőforrásához társított felügyelt identitást használja a \' Azure Key Vault Azure Active Directory használatával történő hitelesítéséhez.  

Az ügyfél által felügyelt kulcs konfigurálását követően a dedikált fürthöz kapcsolódó új betöltött adatmennyiségeket a kulcsával titkosítja a szolgáltatás. A munkaterületeket bármikor leválaszthatja a fürtből. A rendszer betölti az új adatait, és a Microsoft-kulccsal titkosítja Log Analytics a tárterületet, míg az új és a régi adatait zökkenőmentesen kérdezheti le.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsfontosságú képesség a regionális. A Azure Key Vault, a fürtnek és a csatolt Log Analytics-munkaterületnek ugyanabban a régióban kell lennie, de különböző előfizetésekben lehet.

![Ügyfél által felügyelt kulcs áttekintése](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics a Key Vault számára engedélyekkel rendelkező, felügyelt identitással rendelkező *fürterőforrás* – az identitást a dedikált log Analytics fürt tárterületére propagálja a rendszer.
3. Dedikált Log Analytics-fürt
4. *Fürterőforrás* -kapcsolattal társított munkaterületek 

### <a name="encryption-keys-operation"></a>Titkosítási kulcsok művelete

A Storage adattitkosítása 3 típusú kulcsot vesz fel:

- **KEK** – kulcs titkosítási kulcsa (az ügyfél által felügyelt kulcs)
- **AEK** – fiók titkosítási kulcsa
- **Adattitkosítási kulcsot** – adattitkosítási kulcs

A következő szabályok érvényesek:

- A Log Analytics fürt Storage-fiókjai egyedi titkosítási kulcsot hoznak az összes Storage-fiókhoz, amely a "AEK" néven ismert.
- A AEK a DEKs származtatása céljából használható, amelyek a lemezre írt adatblokkok titkosításához használt kulcsok.
- Ha a kulcsot Key Vaultban konfigurálja, és a fürtben hivatkozik rá, az Azure Storage kérelmeket küld a Azure Key Vaultnak a AEK becsomagolásához és az adattitkosítási és visszafejtési műveletek elvégzéséhez.
- A KEK soha nem hagyja el a Key Vault.
- Az Azure Storage a *fürterőforrás* -hez társított felügyelt identitást használja a Azure Key Vault hitelesítésére és elérésére Azure Active Directory használatával.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed kulcs létesítésének lépései

1. Azure Key Vault létrehozása és a kulcs tárolása
1. Fürt létrehozása
1. Engedélyek megadása a Key Vault számára
1. A fürt frissítése a kulcs-azonosító részleteivel
1. Log Analytics-munkaterületek összekapcsolása

Az ügyfél által felügyelt kulcs konfigurációja nem támogatott Azure Portal jelenleg és a kiépítés a [PowerShell](/powershell/module/az.operationalinsights/), a [CLI](/cli/azure/monitor/log-analytics) vagy a [Rest](/rest/api/loganalytics/) -kérelmek használatával végezhető el.

### <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapot-ellenőrzések

Néhány konfigurációs lépés aszinkron módon fut, mert nem hajthatók végre gyorsan. A `status` Válasz a következők egyike lehet: "Inprogress", "frissítés", "Törlés", "sikeres" vagy "sikertelen" a hibakódtal.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N/A

# <a name="rest"></a>[REST](#tab/rest)

A REST használatakor a válasz a 202 (elfogadva) HTTP-állapotkódot és az *Azure-AsyncOperation* tulajdonsággal rendelkező fejlécet adja vissza:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Az aszinkron művelet állapotát úgy tekintheti meg, hogy egy GET kérelmet küld a végpontnak az *Azure-AsyncOperation* fejlécben:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Titkosítási kulcs (KEK) tárolása

Hozzon létre vagy használjon olyan Azure Key Vault, amelyet már létre kell hoznia, vagy importálnia kell az adattitkosításhoz használni kívánt kulcsot. A Azure Key Vaultt helyreállítható kell konfigurálni, hogy megvédje a kulcsot és az adataihoz való hozzáférést Azure monitorban. Ezt a konfigurációt a Key Vault tulajdonságok területén ellenőrizheti, ha engedélyezni szeretné a *Soft delete* és a *Purge Protection* szolgáltatást.

![A védelem törlésére és eltávolítására vonatkozó védelmi beállítások](media/customer-managed-keys/soft-purge-protection.png)

Ezek a beállítások a CLI-n és a PowerShellen keresztül Key Vault frissíthetők:

- [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
- A [védelem kiürítése](../../key-vault/general/soft-delete-overview.md#purge-protection) a titkos vagy a tár kényszerített törlésével, a törlést követően is

## <a name="create-cluster"></a>Fürt létrehozása

A fürtök két [felügyelt identitási típust](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)támogatnak: a rendszer által hozzárendelt és felhasználó által hozzárendelt, míg a forgatókönyvtől függően egyetlen identitást lehet definiálni a fürtben. 
- A rendszer által hozzárendelt felügyelt identitás egyszerűbb, és automatikusan jön létre a fürt létrehozásával, ha az identitás `type` "*SystemAssigned*" értékre van állítva. Ezt az identitást később is felhasználhatja a tárhelyhez való hozzáférés biztosításához a Key Vault a becsomagolási és a kicsomagolási műveletekhez. 
  
  Identitás beállításai a fürtben a rendszer által hozzárendelt felügyelt identitáshoz
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Ha az ügyfél által felügyelt kulcsot a fürt létrehozásakor kívánja konfigurálni, rendelkeznie kell egy kulcs-és felhasználó által hozzárendelt identitással, amelyet előzőleg a Key Vault kell megadni, majd létre kell hoznia a fürtöt a következő beállításokkal: Identity `type` as "*UserAssigned*", `UserAssignedIdentities` az identitás *erőforrás-azonosítójával* .

  Identitás beállításai a fürtben felhasználó által hozzárendelt felügyelt identitáshoz
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> A felhasználó által hozzárendelt felügyelt identitások nem használhatók, ha a Key Vault Private-Link (vNet). Ebben a forgatókönyvben a rendszerhez rendelt felügyelt identitást is használhatja.

Kövesse a [dedikált fürtök című cikkben](./logs-dedicated-clusters.md#creating-a-cluster)bemutatott eljárást. 

## <a name="grant-key-vault-permissions"></a>Key Vault engedélyek megadása

Hozzáférési szabályzat létrehozása Key Vaultban, hogy engedélyeket biztosítson a fürtnek. Ezeket az engedélyeket az alátét Azure Monitor tárolója használja. Nyissa meg a Key Vault a Azure Portalban, és kattintson a *"hozzáférési szabályzatok"* , majd a *"hozzáférési házirend hozzáadása"* lehetőségre a szabályzat létrehozásához a következő beállításokkal:

- Kulcs engedélyei: válassza a *"Get"*, a *"wrap Key"* és a *"kicsomagolási kulcs*" lehetőséget.
- Válasszon rendszerbiztonsági tag: a fürtben használt identitás típusától (a rendszer vagy a felhasználó által hozzárendelt felügyelt identitás) függően adja meg a fürt nevét vagy a fürt rendszerbiztonsági AZONOSÍTÓját a rendszerhez rendelt felügyelt identitáshoz, vagy a felhasználóhoz rendelt felügyelt identitás nevét.

![Key Vault engedélyek megadása](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

A *Get* engedély szükséges annak ellenőrzéséhez, hogy a Key Vault helyreállítható-e a kulcs védelme érdekében, valamint a Azure monitor adataihoz való hozzáféréshez.

## <a name="update-cluster-with-key-identifier-details"></a>Fürt frissítése a kulcs-azonosító részleteivel

A fürtön lévő összes művelethez a `Microsoft.OperationalInsights/clusters/write` művelet engedély szükséges. Ez az engedély a műveletet tartalmazó tulajdonos vagy közreműködő vagy `*/write` a műveletet tartalmazó log Analytics közreműködő szerepkör használatával adható meg `Microsoft.OperationalInsights/*` .

Ez a lépés frissíti Azure Monitor tárterületet az adattitkosításhoz használandó kulccsal és verzióval. A frissítés során az új kulcs a Storage-kulcs (AEK) becsomagolására és kicsomagolására szolgál.

Válassza ki a kulcs aktuális verzióját Azure Key Vault a kulcs azonosítójának részleteinek beszerzéséhez.

![Key Vault engedélyek megadása](media/customer-managed-keys/key-identifier-8bit.png)

Frissítse a KeyVaultProperties a fürtben a kulcs azonosítójának részleteivel.

>[!NOTE]
>A Key rotációs szolgáltatás két módot támogat: az automatikus rotációt vagy az explicit verziójú verziófrissítést. a [kulcs elforgatásával](#key-rotation) határozhatja meg az Ön számára legmegfelelőbb módszert.

A művelet aszinkron, és hosszabb időt is igénybe vehet.

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

A kulcs propagálásának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:
1. Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
2. Küldjön egy GET kérelmet a fürtre, és tekintse meg a *KeyVaultProperties* tulajdonságait. A legutóbb frissített kulcsnak a válaszban kell visszaadnia.

A kérésre adott válasznak a következőhöz hasonlóan kell kinéznie, amikor a kulcs frissítése befejeződött: 202 (elfogadva) és fejléc
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

## <a name="link-workspace-to-cluster"></a>Munkaterület csatolása a fürthöz

> [!IMPORTANT]
> Ezt a lépést csak a Log Analytics-fürt üzembe helyezésének befejezése után kell végrehajtani. Ha a munkaterületeket a kiépítés előtt rendeli hozzá, és az adatot betölti, a rendszer elveti a betöltött adatmennyiséget, és nem lesz helyreállítható.

A művelet végrehajtásához "írási" engedélyekkel kell rendelkeznie a munkaterülethez és a fürthöz, beleértve a következőt: `Microsoft.OperationalInsights/workspaces/write` és `Microsoft.OperationalInsights/clusters/write` .

Kövesse a [dedikált fürtök című cikkben](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)bemutatott eljárást.

## <a name="key-revocation"></a>Kulcs visszavonása

> [!IMPORTANT]
> - Az adataihoz való hozzáférés visszavonásának ajánlott módja a kulcs letiltásával vagy a hozzáférési szabályzat törlésével a Key Vault.
> - Ha a fürt értéke "None" értékre van állítva `identity` `type` , az adataihoz való hozzáférés is visszavonásra kerül, de ez a módszer nem ajánlott, mert nem állíthatja vissza a visszavonást, ha a támogatási kérelem megnyitása nélkül nem tudja visszaállítani a-t a `identity` fürtben.

A fürt tárterülete mindig egy órán belül betartja a kulcsfontosságú engedélyek változásait, a tárterület pedig elérhetetlenné válik. A fürthöz kapcsolódó munkaterületekre betöltött új adatmennyiség eldobásra kerül, és nem állítható vissza, az adat elérhetetlenné válik, és sikertelen lesz a munkaterületek lekérdezése. A korábban betöltött adatmennyiség mindaddig a tárolóban marad, amíg a fürt és a munkaterületek nem törlődnek. A nem elérhető adatokra az adatmegőrzési szabályzat vonatkozik, és a rendszer törli az adatmegőrzési időtartamot. Az elmúlt 14 napban betöltött adatok gyors gyorsítótárban (SSD-vel) is megmaradnak a hatékony lekérdezési motor működéséhez. Ez törölve lesz a kulcs-visszavonási művelet során, és elérhetetlenné válik.

A fürt tárolója rendszeresen ellenőrzi a Key Vault a titkosítási kulcs kicsomagolásának, valamint a hozzáférés után, az adatfeldolgozás és a lekérdezés 30 percen belül folytatódik.

## <a name="key-rotation"></a>Kulcsrotálás

A kulcs forgatásának két módja van: 
- Automatikus elforgatás – ha frissíti a fürtöt, ```"keyVaultProperties"``` de kihagyja a ```"keyVersion"``` tulajdonságot, vagy beállítja a értékre, ```""``` a Storage a legújabb verziókat fogja használni a autoamatically.
- Kiadás explicit verziójának frissítése – ha frissíti a fürtöt, és a tulajdonságban megadja a kulcs verzióját ```"keyVersion"``` , minden új kulcs-verzióhoz explicit ```"keyVaultProperties"``` frissítés szükséges a fürtben: [a fürt frissítése a kulcs-azonosító részleteivel](#update-cluster-with-key-identifier-details). Ha Key Vault, de nem frissíti a fürtben, a Log Analytics fürt tárterülete továbbra is az előző kulcsot használja. Ha letiltja vagy törli a régi kulcsot, mielőtt frissíti az új kulcsot a fürtben, a [visszavonási](#key-revocation) állapotba kerül.

Az összes adatai elérhetők maradnak a kulcs elforgatási művelete után, mivel az adatai mindig titkosítva vannak a fiók titkosítási kulcsával (AEK), míg a AEK mostantól titkosítva van az új kulcs titkosítási kulcs (KEK) verziójával Key Vaultban.

## <a name="customer-managed-key-for-saved-queries"></a>Ügyfél által felügyelt kulcs mentett lekérdezésekhez

A Log Analyticsben használt lekérdezési nyelv kifejező, és bizalmas információkat tartalmazhat a lekérdezésekben vagy a lekérdezési szintaxisban hozzáadott megjegyzésekben. Egyes szervezetek megkövetelik, hogy az ilyen információk védelme az ügyfél által felügyelt kulcs házirendje alapján történjen, és a kulcsával titkosított lekérdezéseket kell mentenie. A Azure Monitor lehetővé teszi, hogy a munkaterülethez való csatlakozáskor a saját kulcsával titkosított *mentett kereséseket* és *napló-riasztásokat* tartalmazó lekérdezéseket tárolja. 

> [!NOTE]
> Log Analytics lekérdezések a használt forgatókönyvtől függően különböző áruházakban menthetők. A lekérdezések a Microsoft Key (MMK) szolgáltatással is titkosítva maradnak a következő esetekben, függetlenül az ügyfél által felügyelt kulcs konfigurációjától: Azure Monitor, Azure-irányítópultok, Azure Logic app, Azure Notebooks és Automation Runbookok-munkafüzetek.

Ha saját tárolót (BYOS) használ, és összekapcsolja azt a munkaterülettel, a szolgáltatás feltölti a *mentett-kereséseket* és a *log-riasztási* lekérdezéseket a Storage-fiókjába. Ez azt jelenti, hogy a Storage-fiókot és a [titkosítás-nyugalmi szabályzatot](../../storage/common/customer-managed-keys-overview.md) a log Analytics fürtben lévő adatok titkosításához használt kulcs vagy egy másik kulcs használatával szabályozhatja. A Storage-fiókkal kapcsolatos költségekért azonban felelősnek kell lennie. 

**Az ügyfél által felügyelt kulcs lekérdezésekhez való beállítása előtt megfontolandó szempontok**
* A munkaterület és a Storage-fiók "Write" engedélyekkel kell rendelkeznie
* Győződjön meg arról, hogy a Storage-fiókot ugyanabban a régióban hozza létre, mint ahol a Log Analytics munkaterület található.
* A tárolóban végzett *keresések* szolgáltatásbeli összetevőknek számítanak, és a formátumuk változhat
* A meglévő *mentett keresések* el lesznek távolítva a munkaterületről. A konfiguráció előtt másolja és mentse a szükséges *kereséseket* . A *mentett keresések* megtekinthetők a [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) használatával
* A lekérdezési előzmények nem támogatottak, és nem láthatja a futtatott lekérdezéseket
* A lekérdezések mentése céljából egyetlen Storage-fiókot kapcsolhat a munkaterülethez, de a *mentett keresések* és a *log-riasztások* lekérdezései között is használható.
* A rögzítés az irányítópulton nem támogatott

**BYOS konfigurálása mentett keresési lekérdezésekhez**

Storage-fiók összekapcsolása *a* munkaterülethez – a *mentett keresési* lekérdezések a Storage-fiókba lesznek mentve. 

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

A konfiguráció után a rendszer minden új *mentett keresési* lekérdezést ment a tárolóba.

**BYOS konfigurálása a log-riasztási lekérdezésekhez**

Storage-fiók csatolása a munkaterülethez tartozó *riasztásokhoz* – a *log-riasztási* lekérdezések mentése a Storage-fiókba történik. 

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

A konfiguráció után a rendszer minden új riasztási lekérdezést ment a tárolóba.

## <a name="customer-lockbox-preview"></a>Ügyfélszéf (előzetes verzió)

A kulcstároló segítségével engedélyezheti vagy elutasíthatja a Microsoft mérnök kérelmét, hogy támogatási kérelem során hozzáférjen az adataihoz.

A Azure Monitor a Log Analytics dedikált fürthöz társított munkaterületeken található adathoz irányítja ezt a vezérlőt. A kulcstároló-vezérlő a Log Analytics dedikált fürtben tárolt adatra vonatkozik, ahol a rendszer elkülönítetten tárolja a fürt Storage-fiókjaiban a kulcstároló által védett előfizetését.  

További információ a [Microsoft Azure Ügyfélszéfről](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed kulcsfontosságú műveletek

A Customer-Managed kulcs dedikált fürtön van megadva, és ezek a műveletek [dedikált fürtben](./logs-dedicated-clusters.md#change-cluster-properties) jelennek meg.

- Az erőforráscsoport összes fürtjének beolvasása  
- Az összes fürt beolvasása az előfizetésben
- *Kapacitás foglalásának* frissítése a fürtben
- *BillingType* frissítése a fürtben
- Munkaterület leválasztása a fürtből
- Fürt törlése

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

- A fürt maximális száma régiónként és az előfizetés 2

- A fürthöz társítható munkaterületek maximális száma 1000

- A munkaterületet összekapcsolhatja a fürttel, majd leválaszthatja azt. Az adott munkaterületen a munkaterület-csatolási műveletek száma legfeljebb 2 lehet, 30 napon belül.

- Az ügyfél által felügyelt kulcs titkosítása a konfigurációs idő után az újonnan betöltött adatmennyiségre vonatkozik. A konfiguráció előtt betöltött adatmennyiség továbbra is titkosítva marad a Microsoft-kulccsal. Az ügyfél által felügyelt kulcsok zökkenőmentes konfigurálása előtt és után lekérdezheti az adatmennyiséget.

- A Azure Key Vault helyreállítható kell konfigurálni. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve, és a CLI vagy a PowerShell használatával kell konfigurálni:<br>
  - [Helyreállítható törlés](../../key-vault/general/soft-delete-overview.md)
  - A [védelem kiürítését](../../key-vault/general/soft-delete-overview.md#purge-protection) be kell kapcsolni ahhoz, hogy védelmet biztosítson a titkos/tár kényszerített törlése után is.

- A fürt más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A Azure Key Vault, a fürtnek és a munkaterületnek ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet.

- A kulcstároló jelenleg nem érhető el Kínában. 

- A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) automatikusan konfigurálva van a támogatott régiókban a 2020 október 1-jétől létrehozott fürtökhöz. Megtekintheti, hogy a fürt kettős titkosításra van-e konfigurálva egy GET kérelem küldésével a fürtön, és annak megfigyelése, hogy az érték olyan fürtök esetében van-e, amelyeken engedélyezve van a `isDoubleEncryptionEnabled` `true` dupla titkosítás. 
  - Ha létrehoz egy fürtöt, és "<régió neve> nem támogatja a fürtök kettős titkosítását", akkor továbbra is létrehozhatja a fürtöt dupla titkosítás nélkül, `"properties": {"isDoubleEncryptionEnabled": false}` a REST-kérelem törzsének hozzáadásával.
  - A fürt létrehozása után a kettős titkosítási beállítás nem módosítható.

  - Ha a fürt felhasználó által hozzárendelt felügyelt identitással van beállítva, akkor a beállítás `UserAssignedIdentities` `None` felfüggeszti a fürtöt, és meggátolja az adatokhoz való hozzáférést, de nem tudja visszaállítani a visszavonást, és nem aktiválja a fürtöt a támogatási kérelem megnyitása nélkül. Ez a korlátozás nem vonatkozik a rendszerhez rendelt felügyelt identitásra.

  - Ha a Key Vault Private-Link (vNet), nem használhatja az ügyfél által felügyelt kulcsot a felhasználóhoz rendelt felügyelt identitással. Ebben a forgatókönyvben a rendszerhez rendelt felügyelt identitást is használhatja.

## <a name="troubleshooting"></a>Hibaelhárítás

- Működés Key Vault rendelkezésre állással
  - Normál működés esetén – a tárolási gyorsítótárak rövid időre visszamenőleges gyorsítótárazást biztosítanak, és visszakerül a Key Vaultra a rendszeres kicsomagoláshoz.
    
  - Átmeneti kapcsolódási hibák – a tároló átmeneti hibákat (időtúllépések, kapcsolódási hibák, DNS-problémák) biztosít, mivel a kulcsok rövid ideig nem maradhatnak a gyorsítótárban, és ez a rendelkezésre állásban lévő kisméretű rendszerállapot-visszaírásokat eredményezi. A lekérdezési és a betöltési képességek megszakítás nélkül folytatódnak.
    
  - Élő webhely – a körülbelül 30 perces leállása miatt a Storage-fiók elérhetetlenné válik. A lekérdezési képesség nem érhető el, és a rendszer az adatvesztés elkerülése érdekében a Microsoft Key használatával több órán keresztül gyorsítótárazza az adatmennyiséget. Ha a rendszer visszaállítja a Key Vault, a lekérdezés elérhetővé válik, és az ideiglenes gyorsítótárazott adatot az adattárba tölti be, és az ügyfél által felügyelt kulccsal titkosítja.

  - Key Vault hozzáférési arány – a Azure Monitor Storage Key Vault a becsomagolási és a kicsomagolási műveletekhez való hozzáférésének gyakorisága 6 – 60 másodperc.

- Ha frissíti a fürtöt, amíg a fürt üzembe helyezése vagy frissítése folyamatban van, a frissítés sikertelen lesz.

- Ha a fürt létrehozásakor ütközési hiba lép fel, akkor előfordulhat, hogy az elmúlt 14 napban törölte a fürtöt, és ez egy nem kötelező törlési időszak. A fürt neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A név akkor jelenik meg, ha a rendszer véglegesen törli a fürtöt.

- A fürtre mutató hivatkozás sikertelen lesz, ha egy másik fürthöz van csatolva.

- Ha létrehoz egy fürtöt, és azonnal megadja a KeyVaultProperties, a művelet sikertelen lehet, mivel a hozzáférési házirend nem definiálható, amíg a rendszer identitása hozzá nem rendeli a fürthöz.

- Ha a meglévő fürtöt frissíti a KeyVaultProperties, és a "Get" kulcs-hozzáférési szabályzat hiányzik a Key Vault, a művelet sikertelen lesz.

- Ha nem sikerül telepíteni a fürtöt, ellenőrizze, hogy a Azure Key Vault, a fürt és a csatolt Log Analytics-munkaterületek ugyanabban a régióban találhatók-e. A lehet különböző előfizetésekben.

- Ha Key Vaultban frissíti a kulcs verzióját, és nem frissíti az új kulcs-azonosító részleteit a fürtben, a Log Analytics-fürt továbbra is az előző kulcsot fogja használni, és az adatai elérhetetlenné válnak. Frissítse az új kulcs-azonosító részleteit a fürtben az adatok feldolgozásának folytatásához és az adatok lekérdezési képességéhez.

- Bizonyos műveletek hosszúak, és eltarthat egy ideig – ezek a fürtök létrehozása, a fürt kulcsának frissítése és a fürt törlése. A művelet állapotát kétféleképpen tekintheti meg:
  1. a REST használatakor másolja az Azure-AsyncOperation URL értéket a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  2. GET kérelem küldése a fürtnek vagy a munkaterületnek, és figyelje meg a választ. A nem összekapcsolt munkaterület például nem rendelkezik a *szolgáltatások* *clusterResourceId* .

- Hibaüzenetek
  
  **Fürt létrehozása**
  -  400 – a fürt neve érvénytelen. A fürt neve az a-z, A-Z, 0-9 és a 3-63 hosszúságú karaktereket tartalmazhat.
  -  400 – a kérelem törzse null értékű vagy helytelen formátumú.
  -  400 – az SKU neve érvénytelen. Adja meg az SKU nevét a capacityReservation értékre.
  -  400 – a kapacitás meg lett biztosítva, de az SKU nem capacityReservation. Adja meg az SKU nevét a capacityReservation értékre.
  -  400 – hiányzó kapacitás az SKU-ban. Állítsa a kapacitás értékét 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – a kapacitás az SKU-ban nincs a tartományon belül. A minimálisan 1000-as és a maximálisan engedélyezett kapacitásnak kell lennie, amely a munkaterület "használat és becsült költségek" területén érhető el.
  -  400 – a kapacitás 30 napig zárolva van. A csökkenő kapacitás 30 nappal a frissítés után engedélyezett.
  -  400 – nincs beállítva SKU. Állítsa az SKU nevét a capacityReservation és a kapacitás értékének 1000-re vagy magasabbra a 100 (GB) lépésekben.
  -  400 – az identitás null értékű vagy üres. Identitás beállítása systemAssigned típussal.
  -  400 – a KeyVaultProperties beállítása a létrehozáskor történik. A KeyVaultProperties frissítése a fürt létrehozása után.
  -  400 – a művelet most nem hajtható végre. Az aszinkron művelet a sikeres állapottól eltérő állapotban van. A fürtnek a frissítési művelet végrehajtása előtt el kell végeznie a műveletet.

  **Fürt frissítése**
  -  400 – a fürt törlési állapotban van. Az aszinkron művelet folyamatban van. A fürtnek a frissítési művelet végrehajtása előtt el kell végeznie a műveletet.
  -  400 – a KeyVaultProperties nem üres, de helytelen formátumú. Lásd a [kulcs-azonosító frissítését](#update-cluster-with-key-identifier-details).
  -  400 – nem sikerült érvényesíteni a kulcsot a Key Vaultban. Oka lehet az engedélyek hiánya, vagy ha a kulcs nem létezik. Ellenőrizze, hogy a [kulcs-és hozzáférési szabályzatot](#grant-key-vault-permissions) Key Vaultban állította-e be.
  -  400 – a kulcs nem helyreállítható. A Key Vaultt a Soft-delete és a Purge-Protection értékre kell beállítani. Lásd: [Key Vault dokumentáció](../../key-vault/general/soft-delete-overview.md)
  -  400 – a művelet most nem hajtható végre. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.
  -  400 – a fürt törlési állapotban van. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  **Fürt beolvasása**
    -  404 – a fürt nem található, lehet, hogy törölték a fürtöt. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal helyreállíthatja, vagy egy másik név használatával új fürtöt hozhat létre. 

  **Fürt törlése**
    -  409 – a fürt nem törölhető üzembe helyezési állapotban. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  **Munkaterület hivatkozása**
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.
  -  400 – a fürt nem található, a megadott fürt nem létezik vagy törölték. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal való helyreállításhoz forduljon az ügyfélszolgálathoz.

  **Munkaterület leválasztása**
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.
## <a name="next-steps"></a>Következő lépések

- További információ a [log Analytics dedikált fürt számlázásáról](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- A [log Analytics-munkaterületek megfelelő kialakításának](./design-logs-deployment.md) megismerése