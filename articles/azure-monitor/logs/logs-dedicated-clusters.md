---
title: Azure Monitor a dedikált fürtöket naplózza
description: Azok az ügyfelek, akik több mint 1 TB-nál több megfigyelési adatot használnak, a megosztott fürtök helyett dedikáltak
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 1fbb8f82366ee961d10ce8a6bc098128bad6555a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041008"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor a dedikált fürtöket naplózza

Azure Monitor a dedikált fürtök naplózása olyan központi telepítési lehetőség, amely fejlett képességeket biztosít a Azure Monitor naplók ügyfelei számára. A dedikált fürtökkel rendelkező ügyfelek kiválaszthatják a fürtökön üzemeltetett munkaterületeket.

A dedikált fürtöket igénylő képességek a következők:

- **[Ügyfél által felügyelt kulcsok](../logs/customer-managed-keys.md)** – a fürtözött adattitkosítást az ügyfél által megadott és vezérelt kulcsok használatával titkosítja.
- **[Kulcstároló](../logs/customer-managed-keys.md#customer-lockbox-preview)** – az ügyfelek vezérelhetik a Microsoft támogatási mérnökök hozzáférési kérelmeit.
- A **[kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** védelmet biztosít olyan esetekben, amikor a titkosítási algoritmusok vagy kulcsok egyike sérül. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait.
- **[Több munkaterület](../logs/cross-workspace-query.md)** – ha egy ügyfél több munkaterületet használ az éles környezetben, érdemes lehet dedikált fürtöt használni. A több munkaterületre kiterjedő lekérdezések gyorsabban futnak, ha az összes munkaterület ugyanazon a fürtön található. Az is előfordulhat, hogy a dedikált fürt használata a hozzárendelt kapacitás foglalási szintjeinek figyelembevételével költséghatékony, és az összes munkaterületre vonatkozik, és az összes munkaterületére érvényes, még akkor is, ha ezek némelyike kicsi, és nem jogosult a kapacitás foglalási kedvezményére.

A dedikált fürtök esetében az ügyfeleknek legalább 1 TB-os adatfeldolgozási kapacitással kell véglegesíteni. A dedikált fürtre történő áttelepítés egyszerű. Nincs adatvesztés vagy-szolgáltatás megszakítása. 

## <a name="management"></a>Kezelés 

A dedikált fürtök kezelése egy Azure-erőforráson keresztül történik, amely Azure Monitor-naplózási fürtöket jelöl. Az erőforrás minden művelete a PowerShell vagy a REST API használatával történik.

A fürt létrehozása után konfigurálható és hozzárendelhető munkaterületek is. Ha egy munkaterület egy fürthöz van társítva, a munkaterületre érkező új adat a fürtön található. Csak a fürttel azonos régióban lévő munkaterületek kapcsolódhatnak a fürthöz. A munkaterületek bizonyos korlátozásokkal nem használhatók a fürtben. A korlátozásokról a jelen cikk további részleteket tartalmaz. 

A dedikált fürtökbe betöltött adatmennyiséget a szolgáltatás szintjén, a Microsoft által felügyelt kulcsokkal vagy az [ügyfél által felügyelt kulccsal](../logs/customer-managed-keys.md)egyszer, az infrastruktúra szintjén pedig két különböző titkosítási algoritmus és két különböző kulcs használatával titkosítjuk. A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) védelmet biztosít olyan esetekben, amikor a titkosítási algoritmusok vagy kulcsok egyike sérül. Ebben az esetben a további titkosítási réteg továbbra is védi az adatait. A dedikált fürt lehetővé teszi, hogy az adatai a [Kulcstároló](../logs/customer-managed-keys.md#customer-lockbox-preview) -vezérlővel is védve legyenek.

A fürt szintjén lévő összes művelethez a `Microsoft.OperationalInsights/clusters/write` művelet engedély szükséges a fürtön. Ez az engedély a műveletet tartalmazó tulajdonos vagy közreműködő vagy `*/write` a műveletet tartalmazó log Analytics közreműködő szerepkör használatával adható meg `Microsoft.OperationalInsights/*` . Log Analytics engedélyekkel kapcsolatos további információkért lásd: [hozzáférés kezelése a naplózási adatokhoz és munkaterületekhez Azure monitor](./manage-access.md). 


## <a name="cluster-pricing-model"></a>Fürt díjszabási modellje

Log Analytics dedikált fürtök kapacitás-foglalási díjszabási modellt használnak, amely legalább 1000 GB/nap. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel.  A kapacitás foglalásának díjszabási információi a [Azure monitor díjszabási oldalon]( https://azure.microsoft.com/pricing/details/monitor/)érhetők el.  

A fürt kapacitásának foglalási szintje programozott módon van konfigurálva a Azure Resource Manager használatával a (z `Capacity` ) paraméterrel `Sku` . A a `Capacity` GB egységben van megadva, és 1000 GB/nap vagy több értékkel rendelkezhet a 100 GB/nap növekményekben.

A fürtön két számlázási mód van használatban. Ezeket a paraméter megadhatja a `billingType` fürt konfigurálásakor. 

1. **Fürt**: ebben az esetben (ez az alapértelmezett beállítás) a betöltött adatmennyiség számlázása a fürt szintjén történik. A rendszer összesíti a fürthöz társított egyes munkaterületekről betöltött adatmennyiségeket a fürt napi számlájának kiszámításához. 

2. **Munkaterületek**: a fürt kapacitás-foglalási költségei arányosak a fürtben lévő munkaterületekhez (az egyes munkaterületek esetében a [Azure Security Center](../../security-center/index.yml) az egyes munkaterületek esetében a csomópontok közötti foglalások elszámolása után).

Ha a munkaterület örökölt, csomóponton alapuló árképzési szintet használ, akkor a fürthöz kapcsolt, a fürt kapacitásának lefoglalásakor betöltött adat alapján kell fizetnie, és a csomópontok száma már nem lehet. A Azure Security Center-ból származó csomópont-adatfoglalások továbbra is érvényben lesznek.

További részletek: Log Analytics dedikált fürtök számlázása [itt]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)érhető el.

## <a name="asynchronous-operations-and-status-check"></a>Aszinkron műveletek és állapot-ellenőrzések

Néhány konfigurációs lépés aszinkron módon fut, mert nem hajthatók végre gyorsan. A válaszban szereplő állapot a következők egyike lehet: "Inprogress", "frissítés", "Törlés", "sikeres vagy" sikertelen ", beleértve a hibakódot. A REST használatakor a válasz először egy 202-as HTTP-állapotkódot (elfogadva) és egy Azure-AsyncOperation tulajdonságú fejlécet ad vissza:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Az aszinkron művelet állapotát úgy tekintheti meg, hogy elküld egy GET kérelmet a Azure-AsyncOperation fejléc értékére:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

## <a name="creating-a-cluster"></a>Fürt létrehozása

Először létre kell hoznia egy fürt erőforrásait a dedikált fürt létrehozásának megkezdéséhez.

A következő tulajdonságokat kell megadni:

- **ClusterName**: felügyeleti célokra használatos. A felhasználók nem jelennek meg erre a névre.
- **ResourceGroupName**: bármely Azure-erőforrás esetében a fürtök egy erőforráscsoporthoz tartoznak. Javasoljuk, hogy egy központi informatikai erőforráscsoportot használjon, mivel a fürtöket általában számos csapat osztja meg a szervezeten belül. További tervezési szempontokért tekintse át [a Azure monitor naplók üzembe helyezésének megtervezése](../logs/design-logs-deployment.md) című szakaszt.
- **Hely**: a fürt egy adott Azure-régióban található. Ehhez a fürthöz csak a régióban található munkaterületek csatolhatók.
- **SkuCapacity**: a *kapacitás foglalási* szintjét (SKU) meg kell adnia a *fürterőforrás* létrehozásakor. A *kapacitás foglalási* szintje napi 1 000 gb és 3 000 GB között lehet. Ha szükséges, a 100-es lépésekben később is frissítheti. Ha napi 3 000 GB-nál nagyobb kapacitású foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: LAIngestionRate@microsoft.com . A fürttel kapcsolatos költségekkel kapcsolatos további információkért lásd: [log Analytics-fürtök költségeinek kezelése](./manage-cost-storage.md#log-analytics-dedicated-clusters)

A *fürterőforrás* létrehozása után további tulajdonságokat (például *SKU*, * keyVaultProperties vagy *billingType*) is szerkeszthet. További részletekért lásd alább.

Régiónként legfeljebb 2 aktív fürt lehet. Ha a fürt törölve van, a rendszer 14 napig is fenntart. Régiónként (aktív vagy nemrégiben törölt) legfeljebb 4 fenntartott fürt lehet.

> [!WARNING]
> A fürt létrehozása elindítja az erőforrás-kiosztást és az üzembe helyezést. A művelet elvégzése néhány órát is igénybe vehet. Azt javasoljuk, hogy aszinkron módon futtassa.

A fürtöket létrehozó felhasználói fióknak rendelkeznie kell a szabványos Azure-Erőforrás-létrehozási engedéllyel: `Microsoft.Resources/deployments/*` és a fürt írási engedélye úgy, hogy `Microsoft.OperationalInsights/clusters/write` az adott művelet vagy vagy a szerepkör-hozzárendelései vannak `Microsoft.OperationalInsights/*` `*/write` .

### <a name="create"></a>Létrehozás 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Válasz*

A következőnek kell lennie: 202 (elfogadva) és egy fejléc.

### <a name="check-cluster-provisioning-status"></a>Fürt kiépítési állapotának keresése

A Log Analytics-fürt üzembe helyezése eltarthat egy ideig. A kiépítési állapotot többféleképpen is megtekintheti:

- Futtassa Get-AzOperationalInsightsCluster PowerShell-parancsot az erőforráscsoport nevével, és keresse meg a ProvisioningState tulajdonságot. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *provisioningState* értéket. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
   Authorization: Bearer <token>
   ```

   **Válasz**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

A *principalId* GUID azonosítót a *fürterőforrás* felügyelt identitási szolgáltatása hozza létre.

## <a name="link-a-workspace-to-cluster"></a>Munkaterület összekapcsolása a fürttel

Ha egy munkaterület egy dedikált fürthöz van csatolva, a munkaterületre betöltött új adat az új fürtre lesz irányítva, miközben a meglévő adat a meglévő fürtön marad. Ha a dedikált fürtöt az ügyfél által felügyelt kulcsok (CMK-EK) használatával titkosítják, csak az új adat titkosítva lesz a kulccsal. A rendszer ezt a különbséget a felhasználóktól és a felhasználóktól csak a szokásos módon kérdezi le, miközben a rendszer a háttérben futtatja a fürtön végzett lekérdezéseket.

A fürtök akár 100 munkaterülethez is összekapcsolhatók. A csatolt munkaterületek ugyanabban a régióban találhatók, mint a fürt. A rendszerháttér védelme és az adattöredezettség elkerülése érdekében a munkaterületet nem lehet havonta kétszer több fürthöz kapcsolni.

A kapcsolati művelet végrehajtásához a munkaterülethez és a *fürt* erőforrásához "írási" engedélyekkel kell rendelkeznie:

- A munkaterületen: *Microsoft. OperationalInsights/munkaterületek/írás*
- A *fürterőforrás* : *Microsoft. OperationalInsights/fürtök/írás*

A számlázási szempontoktól eltérő módon a csatolt munkaterület megtartja a saját beállításait, például az adatmegőrzési időt.
A munkaterület és a fürt különböző előfizetésekben lehet. Lehetőség van arra, hogy a munkaterület és a fürt eltérő bérlők legyenek, ha az Azure Lighthouse mindkét példányát egyetlen bérlőhöz rendeli.

A munkaterületek összekapcsolását csak a Log Analytics-fürt kiépítésének befejezése után lehet elvégezni.

> [!WARNING]
> A munkaterület fürthöz való csatolásához több háttér-összetevő szinkronizálására és a gyorsítótár hidratációjának biztosítására van szükség. A művelet végrehajtása akár két órát is igénybe vehet. Javasoljuk, hogy aszinkron módon futtassa.


**PowerShell**

Fürthöz való hivatkozáshoz használja a következő PowerShell-parancsot:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Fürthöz való hivatkozáshoz használja a következő REST-hívást:

*Küldés*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Válasz*

202 (elfogadva) és fejléc.

### <a name="check-workspace-link-status"></a>Munkaterület-hivatkozás állapotának megtekintése
  
Ha ügyfél által felügyelt kulcsokat használ, a rendszer a felügyelt kulccsal titkosítja a betöltött adatot a társítási művelet után, amely akár 90 percet is igénybe vehet. 

A munkaterület-társítási állapotot kétféleképpen tekintheti meg:

- Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- Hajtsa végre a Get műveletet a munkaterületen, és figyelje meg, hogy a *clusterResourceId* tulajdonság szerepel-e a válaszban a *szolgáltatások* területen.

**Parancssori felület**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
Authorization: Bearer <token>
```

*Válasz*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="change-cluster-properties"></a>Fürt tulajdonságainak módosítása

Miután létrehozta a *fürterőforrás* -erőforrást, és teljes mértékben kiépítve van, a fürt szintjén további tulajdonságokat is szerkeszthet a PowerShell vagy a REST API használatával. A fürt létrehozása során elérhető tulajdonságoktól eltérő tulajdonságok csak a fürt üzembe helyezése után adhatók meg:

- **keyVaultProperties** – frissíti a kulcsot Azure Key Vaultban. Lásd: [a fürt frissítése a kulcs-azonosító részleteivel](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details). A következő paramétereket tartalmazza: *KeyVaultUri*, *Kulcsnév*, *Version*. 
- **billingType** – a *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
  - **Fürt** (alapértelmezett) – a fürt kapacitásának foglalási költségei a *fürterőforrás* számára vannak hozzárendelve.
  - **Munkaterületek** – a fürt kapacitásának foglalási költségei arányosak a fürtben lévő munkaterületekhez, és a *fürt* erőforrása egy bizonyos használatot számláz ki, ha a napi teljes betöltött adat a kapacitás foglalása alatt van. A fürt árképzési modelljével kapcsolatos további tudnivalókért tekintse meg [log Analytics dedikált fürtök](./manage-cost-storage.md#log-analytics-dedicated-clusters) című témakört. 

> [!NOTE]
> A *billingType* tulajdonság nem támogatott a PowerShellben.

### <a name="get-all-clusters-in-resource-group"></a>Az erőforráscsoport összes fürtjének beolvasása
  
**Parancssori felület**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Call*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Válasz*
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

### <a name="get-all-clusters-in-subscription"></a>Az összes fürt beolvasása az előfizetésben

**Parancssori felület**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Call*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Válasz*
    
Ugyanaz, mint az "erőforráscsoport", de az előfizetés hatókörében.



### <a name="update-capacity-reservation-in-cluster"></a>Kapacitás foglalásának frissítése a fürtben

Ha a csatolt munkaterületekhez tartozó adatmennyiség idővel módosul, és a kapacitás foglalási szintjét megfelelően szeretné frissíteni. A kapacitás GB-egységben van megadva, és 1000 GB/nap vagy több értékkel rendelkezhet a 100 GB/nap növekményekben. Vegye figyelembe, hogy nem kell megadnia a teljes REST-kérelem törzsét, de tartalmaznia kell az SKU-t.

**Parancssori felület**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>BillingType frissítése a fürtben

A *billingType* tulajdonság határozza meg a fürt és a hozzá tartozó információk számlázási hozzárendelését:
- *fürt* (alapértelmezett) – a számlázás a fürterőforrás üzemeltetéséhez használt előfizetéshez van hozzárendelve.
- *munkaterületek* – a számlázás a munkaterületek arányosan üzemelő előfizetésekhez van hozzárendelve.

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ```

### <a name="unlink-a-workspace-from-cluster"></a>Munkaterület leválasztása a fürtből

A munkaterületet leválaszthatja egy fürtről. A munkaterület fürtből való leválasztása után az ehhez a munkaterülethez társított új adatok nem lesznek elküldve a dedikált fürthöz. Emellett a munkaterület számlázása már nem a fürtön keresztül történik. Előfordulhat, hogy a nem összekapcsolt munkaterület régi adatmennyisége a fürtön marad. Ha az adatok titkosítása az ügyfél által felügyelt kulcsokkal (CMK) történik, a rendszer megőrzi a Key Vault titkot. A rendszer elvonta ezt a változást Log Analytics felhasználótól. A felhasználók csak a szokásos módon tudják lekérdezni a munkaterületet. A rendszer szükség szerint a háttérben végzi a fürtön keresztüli lekérdezéseket a felhasználókra vonatkozóan.  

> [!WARNING] 
> Egy adott munkaterülethez egy hónapon belül két összekapcsolási művelet van korlátozva. Szánjon időt arra, hogy vegye figyelembe és tervezze meg a tevékenységek leválasztását.

**Parancssori felület**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**PowerShell**

A következő PowerShell-paranccsal távolíthatja el a munkaterületet a fürtből:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Fürt törlése

A dedikált fürterőforrás törölhető. A törlés előtt le kell szüntetnie a fürt összes munkaterületének összekapcsolását. A művelet elvégzéséhez írási engedéllyel kell rendelkeznie a *fürt* erőforrásához. 

A fürt erőforrásának törlése után a fizikai fürt kiürítési és törlési folyamatba kerül. A fürt törlése törli a fürtön tárolt összes adatmennyiséget. Az adatok olyan munkaterületekről származnak, amelyek korábban a fürthöz voltak társítva.

Az elmúlt 14 napban törölt *fürterőforrás* törlési állapotban van, és visszaállítható az adott adattal. Mivel az összes munkaterület *a fürterőforrás* törlésével van társítva a *fürterőforrás* törlésekor, újra társítania kell a munkaterületeket a helyreállítás után. A felhasználó nem tudja elvégezni a helyreállítási műveletet, forduljon a Microsoft-csatornához, vagy támogassa a helyreállítási kérelmeket.

A törlést követő 14 napon belül a fürterőforrás neve le van foglalva, és más erőforrások nem használhatják.

> [!WARNING] 
> Az előfizetések legfeljebb három fürtből állnak. A rendszer az aktív és a Soft-Deleted fürtöket is ennek részeként számítja ki. Az ügyfeleknek nem lehet olyan ismétlődő eljárásokat létrehozni, amelyek fürtöket hoznak létre és törölnek. Jelentős hatással van Log Analytics háttérrendszer-rendszerekre.

**PowerShell**

Fürt törléséhez használja a következő PowerShell-parancsot:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Fürt törléséhez használja a következő REST-hívást:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Válasz**

  200 OK

## <a name="limits-and-constraints"></a>Korlátok és megkötések

- Az aktív fürtök maximális száma régiónként és előfizetésben 2

- A fenntartott fürtök maximális száma (aktív vagy legutóbb törölve) régiónként és előfizetésben 4 

- A fürthöz csatolt munkaterületek maximális száma 1000

- A munkaterületet összekapcsolhatja a fürttel, majd leválaszthatja azt. Az adott munkaterületen a munkaterület-csatolási műveletek száma legfeljebb 2 lehet, 30 napon belül.

- A fürt más erőforráscsoporthoz vagy előfizetéshez való áthelyezése jelenleg nem támogatott.

- A kulcstároló jelenleg nem érhető el Kínában. 

- A [kettős titkosítás](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) automatikusan konfigurálva van a támogatott régiókban a 2020 október 1-jétől létrehozott fürtökhöz. Megtekintheti, hogy a fürt kettős titkosításra van-e konfigurálva egy GET kérelem küldésével a fürtön, és annak megfigyelése, hogy az érték olyan fürtök esetében van-e, amelyeken engedélyezve van a `isDoubleEncryptionEnabled` `true` dupla titkosítás. 
  - Ha létrehoz egy fürtöt, és "<régió neve> nem támogatja a fürtök kettős titkosítását", akkor továbbra is létrehozhatja a fürtöt dupla titkosítás nélkül, `"properties": {"isDoubleEncryptionEnabled": false}` a REST-kérelem törzsének hozzáadásával.
  - A fürt létrehozása után a kettős titkosítási beállítás nem módosítható.

## <a name="troubleshooting"></a>Hibaelhárítás

- Ha a fürt létrehozásakor ütközési hiba lép fel, akkor előfordulhat, hogy az elmúlt 14 napban törölte a fürtöt, és a rendszer helyreállított állapotban van. A fürt neve a Soft-delete időszakban marad fenntartva, és nem hozhat létre ilyen nevű új fürtöt. A név akkor jelenik meg, ha a rendszer véglegesen törli a fürtöt.

- Ha frissíti a fürtöt, amíg a fürt üzembe helyezése vagy frissítése folyamatban van, a frissítés sikertelen lesz.

- Bizonyos műveletek hosszúak, és eltarthat egy ideig – ezek a fürtök létrehozása, a fürt kulcsának frissítése és a fürt törlése. A művelet állapotát kétféleképpen tekintheti meg:
  - A REST használatakor másolja az Azure-AsyncOperation URL értéket a válaszból, és kövesse az [aszinkron műveletek állapotának ellenőrzését](#asynchronous-operations-and-status-check).
  - GET kérelem küldése a fürtnek vagy a munkaterületnek, és figyelje meg a választ. A nem összekapcsolt munkaterület például nem rendelkezik a *szolgáltatások* *clusterResourceId* .

- A fürtre mutató hivatkozás sikertelen lesz, ha egy másik fürthöz van csatolva.

- Hibaüzenetek
  
  Fürt létrehozása:
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

  Fürt frissítése
  -  400 – a fürt törlési állapotban van. Az aszinkron művelet folyamatban van. A fürtnek a frissítési művelet végrehajtása előtt el kell végeznie a műveletet.
  -  400 – a KeyVaultProperties nem üres, de helytelen formátumú. Lásd a [kulcs-azonosító frissítését](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400 – nem sikerült érvényesíteni a kulcsot a Key Vaultban. Oka lehet az engedélyek hiánya, vagy ha a kulcs nem létezik. Ellenőrizze, hogy a [kulcs-és hozzáférési szabályzatot](../logs/customer-managed-keys.md#grant-key-vault-permissions) Key Vaultban állította-e be.
  -  400 – a kulcs nem helyreállítható. A Key Vaultt a Soft-delete és a Purge-Protection értékre kell beállítani. Lásd: [Key Vault dokumentáció](../../key-vault/general/soft-delete-overview.md)
  -  400 – a művelet most nem hajtható végre. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.
  -  400 – a fürt törlési állapotban van. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  Fürt beolvasása:
    -  404 – a fürt nem található, lehet, hogy törölték a fürtöt. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal helyreállíthatja, vagy egy másik név használatával új fürtöt hozhat létre. 

  Fürt törlése
    -  409 – a fürt nem törölhető üzembe helyezési állapotban. Várjon, amíg az aszinkron művelet befejeződik, és próbálkozzon újra.

  Munkaterület hivatkozása:
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.
  -  400 – a fürt nem található, a megadott fürt nem létezik vagy törölték. Ha ezzel a névvel próbál létrehozni egy fürtöt, és ütközésbe ütközik, a fürt 14 napig helyreállítható törlést végez. A támogatási szolgálattal való helyreállításhoz forduljon az ügyfélszolgálathoz.

  Munkaterület leválasztása:
  -  404 – a munkaterület nem található. A megadott munkaterület nem létezik vagy törölték.
  -  409 – a munkaterület hivatkozása vagy a művelet leválasztása folyamatban.

## <a name="next-steps"></a>Következő lépések

- További információ a [log Analytics dedikált fürt számlázásáról](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- A [log Analytics-munkaterületek megfelelő kialakításának](../logs/design-logs-deployment.md) megismerése