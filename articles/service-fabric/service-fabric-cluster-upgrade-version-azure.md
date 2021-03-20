---
title: Fürt Azure Service Fabric verziójának frissítése
description: Frissítse a Service Fabric kódot és/vagy konfigurációt, amely egy Service Fabric fürtöt futtat, beleértve a fürt frissítési módjának beállítását, a tanúsítványok frissítését, az alkalmazások portjainak hozzáadását, az operációs rendszer javítását stb. Mire számíthat a frissítések végrehajtásakor?
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 01fe916f0ee78c8481ac6b17b8f7409b47c852ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564287"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Egy fürt Service Fabric-verziójának frissítése

Bármely modern rendszer esetében a minőségének megtervezése kulcsfontosságú a termék hosszú távú sikerességének megvalósításához. Az Azure Service Fabric-fürt olyan erőforrás, amelyet Ön birtokol, de részben a Microsoft felügyeli. Ez a cikk az Azure-fürtön futó Service Fabric verziójának frissítését ismerteti.

Beállíthatja, hogy a fürt a Microsoft által kiadott automatikus háló-frissítéseket fogadja, vagy kiválaszthat egy olyan támogatott háló-verziót, amelyet be szeretne állítani a fürtön.

Ezt úgy teheti meg, hogy az "upgradeMode" fürtöt a portálon vagy a Resource Managert használja a létrehozáskor vagy később egy élő fürtön 

> [!NOTE]
> Ügyeljen arra, hogy a fürtön mindig a támogatott háló verziót futtassa. Ahogy és amikor bejelentjük a Service Fabric új verziójának kiadását, az előző verzió a támogatás végére van megjelölve, legalább 60 nappal az adott dátumtól számítva. Az új kiadások a [Service Fabric csapatának blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)jelennek meg. Az új kiadás elérhető lesz a választáshoz. 
> 
> 

a fürt kiadásának lejárta előtt 14 nappal egy állapot-esemény jön létre, amely figyelmeztetési állapotba helyezi a fürtöt. A fürt figyelmeztetési állapotban marad, amíg nem frissít egy támogatott Fabric-verzióra.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>A frissítési mód beállítása a Azure Portalban
A fürtöt beállíthatja automatikus vagy manuálisra a fürt létrehozásakor.

![Képernyőfelvétel: a Service Fabric-fürt létrehozása ablaktábla, 2. lehetőséggel kiválasztott fürtkonfiguráció, a fürtkonfiguráció ablaktábla pedig meg van nyitva.][Create_Manualmode]

A fürtöt beállíthatja automatikus vagy manuálisra, ha élő fürtön, a kezelés felületén keresztül. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Frissítés egy új verzióra egy olyan fürtön, amely manuális módra van beállítva a portálon keresztül.
Az új verzióra való frissítéshez mindössze annyit kell tennie, hogy kijelöli a rendelkezésre álló verziót a legördülő menüből, és menti a mentést. A háló frissítése automatikusan bekerül. A fürt állapot-házirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) be vannak tartva a frissítés során.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést. Görgessen le a dokumentumhoz, és olvassa el, hogyan állíthatja be ezeket az egyéni állapotú házirendeket. 

A visszaállítást eredményező hibák kijavítása után újra kell indítania a frissítést a korábban leírt lépések követésével.

![Képernyőfelvétel: a Service Fabric fürtök ablak, amelyen a háló frissítése panel meg van nyitva, és a Kiemelt frissítési lehetőségek, beleértve az automatikus és a manuális beállítást is.][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>A frissítési mód beállítása Resource Manager-sablon használatával
Adja hozzá a "upgradeMode" konfigurációt a Microsoft. ServiceFabric/Clusters erőforrás-definícióhoz, és állítsa be a "clusterCodeVersion" kifejezést az alább látható támogatott háló verziók egyikére, majd telepítse a sablont. A "upgradeMode" értéke "kézi" vagy "automatikus".

![A képernyőképen egy sablon látható, amely az egyszerű szöveg behúzásával ábrázolja a szerkezetet, és kiemeli a clusterCodeVersion és a upgradeMode.][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Frissítés egy új verzióra egy olyan fürtön, amely egy Resource Manager-sablonon keresztül manuális üzemmódra van beállítva.
Ha a fürt manuális módban van, az új verzióra való frissítéshez módosítsa a "clusterCodeVersion" kifejezést egy támogatott verzióra, és telepítse azt. A sablon üzembe helyezése, a háló frissítésének rúgásai automatikusan elindulnak. A fürt állapot-házirendjei (a csomópont állapotának és a fürtben futó összes alkalmazás állapotának kombinációja) be vannak tartva a frissítés során.

Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést.  

A visszaállítást eredményező hibák kijavítása után újra kell indítania a frissítést a korábban leírt lépések követésével.

## <a name="set-custom-health-polices-for-upgrades"></a>Egyéni állapot-házirendek beállítása a frissítésekhez
Megadhat egyéni állapot-házirendeket a háló frissítéséhez. Ha úgy állította be a fürtöt, hogy az automatikus háló frissítése megtörténjen, akkor ezek a szabályzatok az [automatikus háló frissítéseinek 1. fázisára](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)vonatkoznak.
Ha a fürtöt manuális hálós frissítésre állította be, akkor a rendszer minden alkalommal alkalmazza ezeket a házirendeket, amikor kiválaszt egy új verziót, amely elindítja a rendszerindítást a fürtben. Ha nem bírálja felül a szabályzatokat, a rendszer az alapértelmezett értékeket használja.

A speciális frissítési beállítások kiválasztásával megadhatja az egyéni állapotfigyelő házirendeket, vagy áttekintheti az aktuális beállításokat a "háló frissítése" panelen. Tekintse át a következő képet a című témakörben. 

![Egyéni állapotfigyelő házirendek kezelése][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Az összes elérhető verzió listázása egy adott előfizetés összes környezetéhez
Futtassa a következő parancsot, és ehhez hasonló kimenetet kell kapnia.

a "supportExpiryUtc" jelzi, ha egy adott kiadás lejár vagy lejárt. A legújabb kiadás nem rendelkezik érvényes dátummal – a "9999-12-31T23:59:59.9999999" értékkel rendelkezik, ami azt jelenti, hogy a lejárati dátum még nincs beállítva.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
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

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan szabhatja testre a [Service Fabric-fürtök néhány beállítását](service-fabric-cluster-fabric-settings.md)
* Ismerje meg, hogyan [méretezheti a fürtöt és ki](service-fabric-cluster-scale-in-out.md)
* Az [alkalmazások frissítéseinek](service-fabric-application-upgrade.md) megismerése

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
