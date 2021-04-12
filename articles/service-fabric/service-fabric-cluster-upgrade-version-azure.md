---
title: Service Fabric-fürt frissítéseinek kezelése
description: A Service Fabric-fürt futtatókörnyezetének frissítése és kezelése
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 98c3300e5cc51c32d894397839879e25190d979b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731165"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Service Fabric-fürt frissítéseinek kezelése

Az Azure Service Fabric-fürt egy Ön tulajdonában lévő erőforrás, de részben a Microsoft felügyeli. A következőképpen felügyelheti, hogy mikor és hogyan frissíti a Microsoft az Azure Service Fabric-fürtöt.

További információ a fürt frissítésével kapcsolatos fogalmakról és folyamatokról: [Azure Service Fabric-fürtök frissítése és frissítése](service-fabric-cluster-upgrade.md)

## <a name="set-upgrade-mode"></a>Frissítési mód beállítása

Beállíthatja, hogy a fürt a Microsoft által kiadott automatikus Service Fabric-frissítéseket fogadja, vagy manuálisan is választhat a jelenleg támogatott verziók listájáról a fürt frissítési módjának beállításával. Ezt a *háló frissítési módjának* vezérlésével végezheti Azure Portal vagy a `upgradeMode` fürt telepítési sablonjának beállításával.

### <a name="azure-portal"></a>Azure Portal

A Azure Portal használatával az új Service Fabric-fürt létrehozásakor az automatikus vagy manuális frissítések közül választhat.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="Válassza az automatikus vagy manuális frissítések lehetőséget, amikor új fürtöt hoz létre Azure Portal a &quot;speciális&quot; lehetőségek közül.":::

A meglévő fürterőforrás **háló verziófrissítések** szakaszának automatikus vagy manuális frissítései között is válthat.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Válassza az automatikus vagy manuális frissítés lehetőséget a fürterőforrás &quot;Fabric Upgrades&quot; szakaszában a Azure Portal":::

### <a name="manual-upgrades-with-azure-portal"></a>Manuális frissítések a Azure Portal

Ha a manuális frissítés lehetőséget választja, akkor a frissítés elindításához minden szükséges, hogy kiválassza az elérhető verziók legördülő menüt, majd *mentse*. Innentől kezdve a fürt frissítése azonnal kikerül.

A [fürt állapot-házirendjei](#custom-policies-for-manual-upgrades) (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) be vannak tartva a frissítés során. Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést.

Miután kijavította a visszaállítást eredményező problémákat, újra kell indítania a frissítést a korábban leírt lépések követésével.

### <a name="resource-manager-template"></a>Resource Manager-sablon

A fürt frissítési módjának Resource Manager-sablonnal való módosításához adja meg a   `upgradeMode` *Microsoft. ServiceFabric/Clusters* erőforrás-definíció tulajdonságának automatikus vagy manuális beállítását. Ha a manuális frissítés lehetőséget választja, a-t is állítsa a `clusterCodeVersion` jelenleg [támogatott háló verzióra](#query-for-supported-cluster-versions).

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="A képernyőfelvétel egy sablont mutat be, amely az egyszerű szöveg behúzása a szerkezetnek megfelelően. A &quot;clusterCodeVersion&quot; és a &quot;upgradeMode&quot; tulajdonság ki van emelve.":::

A sablon sikeres üzembe helyezése után a rendszer a fürt frissítési módjának módosításait fogja alkalmazni. Ha a fürt manuális módban van, akkor a fürt frissítése automatikusan kiindul.

A [fürt állapot-házirendjei](#custom-policies-for-manual-upgrades) (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) be vannak tartva a frissítés során. Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést.

Miután kijavította a visszaállítást eredményező problémákat, újra kell indítania a frissítést a korábban leírt lépések követésével.

## <a name="wave-deployment-for-automatic-upgrades"></a>A Wave üzembe helyezése automatikus frissítésekhez

Az automatikus frissítési móddal engedélyezheti a fürt számára a Wave-telepítést. A Wave üzembe helyezése során létrehozhat egy folyamatot, amely a tesztelési, a fázis-és a termelési fürtöket egymás utáni verzióra frissíti, és az éles fürtök frissítése előtt a közelgő Service Fabric verziók ellenőrzéséhez a "Bake Time" beépített időpontot választja.

### <a name="enable-wave-deployment"></a>A Wave-telepítés engedélyezése

> [!NOTE]
> A Wave üzembe helyezéséhez a `2020-12-01-preview` (vagy újabb) API-verzió szükséges a *Microsoft. ServiceFabric/Clusters* erőforráshoz.

Ha engedélyezni szeretné a Wave-telepítést az automatikus frissítéshez, először határozza meg, hogy melyik hullámot szeretné hozzárendelni a fürthöz:

* **Wave 0** ( `Wave0` ): a fürtök frissítése azonnal megtörténik, amikor új Service Fabric buildet szabadítanak fel. Tesztelési/fejlesztési fürtökhöz.
* **1. hullám** ( `Wave1` ): a fürtök egy héttel (hét nap) frissültek egy új Build felszabadítása után. Előzetes előkészítési/előkészítési fürtökhöz készült.
* **2. hullám** ( `Wave2` ): a fürtök két hetet (14 nap) frissítenek egy új Build felszabadítása után. Éles fürtökhöz készült.

Ezután egyszerűen adjon hozzá egy `upgradeWave` tulajdonságot a fürterőforrás-sablonhoz a fent felsorolt hullám-értékek egyikével. Győződjön meg arról, hogy a fürterőforrás API `2020-12-01-preview` -verziója vagy újabb.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

A frissített sablon telepítése után a fürt regisztrálása a megadott hullámban történik a következő verziófrissítési időszakban és után.

Az [e-mailes értesítések regisztrálásával](#register-for-notifications) további segítségre lehet szüksége, ha a fürt frissítése sikertelen.

### <a name="register-for-notifications"></a>Regisztráció értesítésekre

Ha a fürt frissítése sikertelen, akkor regisztrálhatja az értesítéseket. A rendszer e-mailt küld a kijelölt e-mail-címére, ahol további részleteket talál a frissítési hibával kapcsolatban, és további segítségre mutató hivatkozásokat is tartalmaz.

> [!NOTE]
> Nem szükséges bejelentkezni a frissítési hibákra vonatkozó értesítések fogadására a Wave-telepítésben.

Az értesítések beléptetéséhez vegyen fel egy `notifications` szakaszt a fürterőforrás-sablonba, és jelöljön ki egy vagy több e-mail-címet (*fogadót*) az értesítések fogadásához:

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

A frissített sablon telepítése után a rendszer regisztrálja a frissítési hibákra vonatkozó értesítéseket.

## <a name="custom-policies-for-manual-upgrades"></a>Egyéni szabályzatok manuális frissítésekhez

Egyéni állapot-házirendeket is megadhat a fürtök kézi frissítéseihez. Ezeket a házirendeket a rendszer minden alkalommal alkalmazza, amikor kiválaszt egy új futtatókörnyezet-verziót, amely elindítja a számítógépet a fürt frissítésének elindításához. Ha nem bírálja felül a szabályzatokat, a rendszer az alapértelmezett értékeket használja.

Megadhatja az egyéni állapotfigyelő házirendeket, vagy áttekintheti a fürt erőforrásának **háló frissítése** szakaszában található aktuális beállításokat Azure Portal a **frissítési házirend** *Egyéni* lehetőség választásával.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="A frissítés során az egyéni állapotházirendek beállításához válassza az &quot;egyéni&quot; frissítési házirend beállítást a Azure Portal a fürt erőforrásának &quot;háló frissítése&quot; szakaszában.":::

## <a name="query-for-supported-cluster-versions"></a>Támogatott fürtözött verziók lekérdezése

Az [Azure REST API](/rest/api/azure/) használatával listázhatja az összes rendelkezésre álló Service Fabric Runtime-verziót ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) a megadott helyhez és az előfizetéséhez.

A támogatott verziókról és operációs rendszerekről további részleteket a [Service Fabric verziókra](service-fabric-versions.md) is hivatkozhat.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

A `supportExpiryUtc` kimeneti jelentésekben, ha egy adott kiadás lejár vagy lejárt. A legújabb kiadásokhoz nem tartozik érvényes dátum, hanem *9999-12-31T23:59:59.9999999* érték, ami azt jelenti, hogy a lejárati dátum még nincs beállítva.


## <a name="next-steps"></a>Következő lépések

* [Service Fabric-frissítések kezelése](service-fabric-cluster-upgrade-version-azure.md)
* A [Service Fabric-fürt beállításainak](service-fabric-cluster-fabric-settings.md) testreszabása
* [A fürt méretezése és kicsinyítése](service-fabric-cluster-scale-in-out.md)
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md) megismerése


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
