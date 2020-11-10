---
title: Azure Monitor a dedikált fürtöket naplózza
description: Azok az ügyfelek, akik több mint 1 TB-nál több megfigyelési adatot használnak, a megosztott fürtök helyett dedikáltak
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 293a3fc10920a29cd41e4bdb946e5bb06762eb52
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427496"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor a dedikált fürtöket naplózza

Azure Monitor a dedikált fürtök a nagy mennyiségű ügyfelek jobb kiszolgálására szolgáló központi telepítési lehetőség. Azok az ügyfelek, akik naponta több mint 4 TB-nyi adatot töltenek fel, dedikált fürtöket fognak használni. A dedikált fürtökkel rendelkező ügyfelek kiválaszthatják a fürtökön üzemeltetett munkaterületeket.

A nagy mennyiségű támogatáson kívül más előnyökkel is jár a dedikált fürtök használata:

- **Díjszabási korlát** – az ügyfelek magasabb betöltési [arányra vonatkozó korlátját](../service-limits.md#data-ingestion-volume-rate) csak dedikált fürtön lehet megszabni.
- **Szolgáltatások** – bizonyos vállalati funkciók csak dedikált fürtökön érhetők el – különösen az ügyfél által felügyelt kulcsokra (CMK) és a kulcstároló-támogatásra. 
- **Konzisztencia** – az ügyfelek saját dedikált erőforrásaikkal rendelkeznek, így az ugyanazon a megosztott infrastruktúrán futó többi ügyféltől sincs befolyása.
- **Költséghatékonyság** – a dedikált fürt használata költséghatékonyabb lehet, mivel a hozzárendelt kapacitás foglalási szintjei figyelembe veszik az összes fürt betöltését, és az összes munkaterületére érvényesek, még akkor is, ha ezek némelyike kicsi, és nem jogosult a kapacitás foglalási kedvezményére.
- A **több munkaterületet tartalmazó** lekérdezések gyorsabban futnak, ha az összes munkaterület ugyanazon a fürtön található.

A dedikált fürtök esetében az ügyfeleknek legalább 1 TB-os adatfeldolgozási kapacitással kell véglegesíteni. A dedikált fürtre történő áttelepítés egyszerű. Nincs adatvesztés vagy-szolgáltatás megszakítása. 

> [!IMPORTANT]
> A dedikált fürtök jóváhagyva és teljes mértékben támogatottak az éles környezetben üzemelő példányok esetében. Az ideiglenes kapacitás megkötése miatt azonban a szolgáltatás használatához előzetes regisztráció szükséges. Az előfizetések azonosítóinak megadásához használja a Microsoft névjegyeit.

## <a name="management"></a>Kezelés 

A dedikált fürtök kezelése egy Azure-erőforráson keresztül történik, amely Azure Monitor-naplózási fürtöket jelöl. Az erőforrás minden művelete a PowerShell vagy a REST API használatával történik.

A fürt létrehozása után konfigurálható és hozzárendelhető munkaterületek is. Ha egy munkaterület egy fürthöz van társítva, a munkaterületre érkező új adat a fürtön található. Csak a fürttel azonos régióban lévő munkaterületek kapcsolódhatnak a fürthöz. A munkaterületek bizonyos korlátozásokkal nem használhatók a fürtben. A korlátozásokról a jelen cikk további részleteket tartalmaz. 

A fürt szintjén lévő összes művelethez a `Microsoft.OperationalInsights/clusters/write` művelet engedély szükséges a fürtön. Ez az engedély a műveletet tartalmazó tulajdonos vagy közreműködő vagy `*/write` a műveletet tartalmazó log Analytics közreműködő szerepkör használatával adható meg `Microsoft.OperationalInsights/*` . Log Analytics engedélyekkel kapcsolatos további információkért lásd: [hozzáférés kezelése a naplózási adatokhoz és munkaterületekhez Azure monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Fürt díjszabási modellje

Log Analytics dedikált fürtök kapacitás-foglalási díjszabási modellt használnak, amely legalább 1000 GB/nap. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel.  A kapacitás foglalásának díjszabási információi a [Azure monitor díjszabási oldalon]( https://azure.microsoft.com/pricing/details/monitor/)érhetők el.  

A fürt kapacitásának foglalási szintje programozott módon van konfigurálva a Azure Resource Manager használatával a (z `Capacity` ) paraméterrel `Sku` . A a `Capacity` GB egységben van megadva, és 1000 GB/nap vagy több értékkel rendelkezhet a 100 GB/nap növekményekben.

A fürtön két számlázási mód van használatban. Ezeket a paraméter megadhatja a `billingType` fürt konfigurálásakor. 

1. **Fürt** : ebben az esetben (ez az alapértelmezett beállítás) a betöltött adatmennyiség számlázása a fürt szintjén történik. A rendszer összesíti a fürthöz társított egyes munkaterületekről betöltött adatmennyiségeket a fürt napi számlájának kiszámításához. 

2. **Munkaterületek** : a fürt kapacitás-foglalási költségei arányosak a fürtben lévő munkaterületekhez (az egyes munkaterületek esetében a [Azure Security Center](../../security-center/index.yml) az egyes munkaterületek esetében a csomópontok közötti foglalások elszámolása után).

Vegye figyelembe, hogy ha a munkaterület csomópont-díjszabási szinten használ örökölt, akkor a fürthöz való csatlakozáskor a rendszer a fürt kapacitásának lefoglalása során betöltött adatmennyiség alapján számlázza a díjat, és a csomópontok után már nem. A Azure Security Center-ból származó csomópont-adatfoglalások továbbra is érvényben lesznek.

További részletek: Log Analytics dedikált fürtök számlázása [itt]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)érhető el.


## <a name="creating-a-cluster"></a>Fürt létrehozása

Először létre kell hoznia egy fürt erőforrásait a dedikált fürt létrehozásának megkezdéséhez.

A következő tulajdonságokat kell megadni:

- **ClusterName** : felügyeleti célokra használatos. A felhasználók nem jelennek meg erre a névre.
- **ResourceGroupName** : bármely Azure-erőforrás esetében a fürtök egy erőforráscsoporthoz tartoznak. Javasoljuk, hogy egy központi informatikai erőforráscsoportot használjon, mivel a fürtöket általában számos csapat osztja meg a szervezeten belül. További tervezési szempontokért tekintse át [a Azure monitor naplók üzembe helyezésének megtervezése](../platform/design-logs-deployment.md) című szakaszt.
- **Hely** : a fürt egy adott Azure-régióban található. Ehhez a fürthöz csak a régióban található munkaterületek csatolhatók.
- **SkuCapacity** : a *kapacitás foglalási* szintjét (SKU) meg kell adnia a *fürterőforrás* létrehozásakor. A *kapacitás foglalási* szintje napi 1 000 gb és 3 000 GB között lehet. Ha szükséges, a 100-es lépésekben később is frissítheti. Ha napi 3 000 GB-nál nagyobb kapacitású foglalásra van szüksége, lépjen kapcsolatba velünk a következő címen: LAIngestionRate@microsoft.com . A fürttel kapcsolatos költségekkel kapcsolatos további információkért lásd: [log Analytics-fürtök költségeinek kezelése](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

A *fürterőforrás* létrehozása után további tulajdonságokat (például *SKU* , * keyVaultProperties vagy *billingType* ) is szerkeszthet. További részletekért lásd alább.

> [!WARNING]
> A fürt létrehozása elindítja az erőforrás-kiosztást és az üzembe helyezést. A művelet elvégzése akár egy órát is igénybe vehet. Azt javasoljuk, hogy aszinkron módon futtassa.

A fürtöket létrehozó felhasználói fióknak rendelkeznie kell a szabványos Azure-Erőforrás-létrehozási engedéllyel: `Microsoft.Resources/deployments/*` és a fürt írási engedélyével `(Microsoft.OperationalInsights/clusters/write)` .

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

200 OK és fejlécnek kell lennie.

### <a name="check-provisioning-status"></a>Az átadási állapot ellenőrzése

A Log Analytics-fürt üzembe helyezése eltarthat egy ideig. A kiépítési állapotot többféleképpen is megtekintheti:

- Futtassa Get-AzOperationalInsightsCluster PowerShell-parancsot az erőforráscsoport nevével, és keresse meg a ProvisioningState tulajdonságot. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *provisioningState* értéket. Az érték a kiépítés és a *sikeres* Befejezés *ProvisioningAccount* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
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

## <a name="change-cluster-properties"></a>Fürt tulajdonságainak módosítása

Miután létrehozta a *fürterőforrás* -erőforrást, és teljes mértékben kiépítve van, a fürt szintjén további tulajdonságokat is szerkeszthet a PowerShell vagy a REST API használatával. A fürt létrehozása során elérhető tulajdonságoktól eltérő tulajdonságok csak a fürt üzembe helyezése után adhatók meg:

- **keyVaultProperties** : a [Azure monitor ügyfél által felügyelt kulcs](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure)kiépítéséhez használt Azure Key Vault konfigurálására szolgál. A következő paramétereket tartalmazza:  *KeyVaultUri* , *Kulcsnév* , *Version*. 
- **billingType** – a *billingType* tulajdonság határozza meg a *fürterőforrás* és a hozzá tartozó adatforrások számlázási hozzárendelését:
  - **Fürt** (alapértelmezett) – a fürt kapacitásának foglalási költségei a *fürterőforrás* számára vannak hozzárendelve.
  - **Munkaterületek** – a fürt kapacitásának foglalási költségei arányosak a fürtben lévő munkaterületekhez, és a *fürt* erőforrása egy bizonyos használatot számláz ki, ha a napi teljes betöltött adat a kapacitás foglalása alatt van. A fürt árképzési modelljével kapcsolatos további tudnivalókért tekintse meg [log Analytics dedikált fürtök](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) című témakört. 

> [!NOTE]
> A *billingType* tulajdonság nem támogatott a PowerShellben.
> Előfordulhat, hogy a fürt tulajdonságainak frissítései aszinkron módon futnak, és eltarthat egy ideig.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> A *fürt* erőforrás- *SKU* -t, a *KeyVaultProperties* -t vagy a *billingType* -t a patch használatával frissítheti.

Például: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

A "KeyVaultProperties" a Key Vault kulcs azonosítójának részleteit tartalmazza.

*Válasz*

200 OK és fejléc

### <a name="check-cluster-update-status"></a>Fürt frissítési állapotának megtekintése

A kulcs-azonosító propagálásának elvégzése néhány percet vesz igénybe. A frissítési állapotot kétféleképpen is megtekintheti:

- Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését. 

   OR

- Küldjön egy GET-kérést a *fürterőforrás* számára, és tekintse meg a *KeyVaultProperties* tulajdonságait. A válaszban a legutóbb frissített kulcs-azonosító részleteit kell visszaadnia.

   A *fürt* erőforrására vonatkozó kérésre adott válasznak a következőhöz hasonlóan kell kinéznie:

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

## <a name="link-a-workspace-to-the-cluster"></a>Munkaterület összekapcsolása a fürttel

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Válasz*

200 OK és fejléc.

### <a name="using-customer-managed-keys-with-linking"></a>Ügyfél által felügyelt kulcsok használata a csatolással

Ha ügyfél által felügyelt kulcsokat használ, a rendszer a felügyelt kulccsal titkosítja a betöltött adatot a társítási művelet után, amely akár 90 percet is igénybe vehet. 

A munkaterület-társítási állapotot kétféleképpen tekintheti meg:

- Másolja a Azure-AsyncOperation URL-címet a válaszból, és kövesse az aszinkron műveletek állapotának ellenőrzését.

- [Munkaterületek küldése –](/rest/api/loganalytics/workspaces/get) kérelem kérése és a válasz betartása. A társított munkaterülethez tartozik egy clusterResourceId a "szolgáltatások" alatt.

A küldési kérelem a következőhöz hasonló:

*Küldés*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Munkaterület leválasztása dedikált fürtből

A munkaterületet leválaszthatja egy fürtről. A munkaterület fürtből való leválasztása után az ehhez a munkaterülethez társított új adatok nem lesznek elküldve a dedikált fürthöz. Emellett a munkaterület számlázása már nem a fürtön keresztül történik. Előfordulhat, hogy a nem összekapcsolt munkaterület régi adatmennyisége a fürtön marad. Ha az adatok titkosítása az ügyfél által felügyelt kulcsokkal (CMK) történik, a rendszer megőrzi a Key Vault titkot. A rendszer elvonta ezt a változást Log Analytics felhasználótól. A felhasználók csak a szokásos módon tudják lekérdezni a munkaterületet. A rendszer szükség szerint a háttérben végzi a fürtön keresztüli lekérdezéseket a felhasználókra vonatkozóan.  

> [!WARNING] 
> Egy hónapon belül legfeljebb két összekapcsolási művelet lehet a munkaterületen. Szánjon időt arra, hogy vegye figyelembe és tervezze meg a tevékenységek leválasztását. 

## <a name="delete-a-dedicated-cluster"></a>Dedikált fürt törlése

A dedikált fürterőforrás törölhető. A törlés előtt le kell szüntetnie a fürt összes munkaterületének összekapcsolását. A művelet elvégzéséhez írási engedéllyel kell rendelkeznie a *fürt* erőforrásához. 

A fürt erőforrásának törlése után a fizikai fürt kiürítési és törlési folyamatba kerül. A fürt törlése törli a fürtön tárolt összes adatmennyiséget. Az adatok olyan munkaterületekről származnak, amelyek korábban a fürthöz voltak társítva.

Az elmúlt 14 napban törölt *fürterőforrás* törlési állapotban van, és visszaállítható az adott adattal. Mivel az összes munkaterület *a fürterőforrás* törlésével van társítva a *fürterőforrás* törlésekor, újra társítania kell a munkaterületeket a helyreállítás után. A felhasználó nem tudja elvégezni a helyreállítási műveletet, forduljon a Microsoft-csatornához, vagy támogassa a helyreállítási kérelmeket.

A törlést követő 14 napon belül a fürterőforrás neve le van foglalva, és más erőforrások nem használhatják.

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



## <a name="next-steps"></a>Következő lépések

- További információ a [log Analytics dedikált fürt számlázásáról](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- A [log Analytics-munkaterületek megfelelő kialakításának](../platform/design-logs-deployment.md) megismerése