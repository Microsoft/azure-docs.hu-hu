---
title: Service Fabric alkalmazás frissítése
description: Ez a cikk bevezetést nyújt egy Service Fabric alkalmazás frissítéséhez, többek között a frissítési módok kiválasztásához és az állapot-ellenőrzések végrehajtásához.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: f3fad8d0ede92004706d9a1f4e14353715361b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792014"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric alkalmazás frissítése
Az Azure Service Fabric-alkalmazás szolgáltatások gyűjteménye. A frissítés során Service Fabric összehasonlítja az új [alkalmazás-jegyzékfájlt](service-fabric-application-and-service-manifests.md) az előző verzióval, és meghatározza, hogy az alkalmazás mely szolgáltatásai igényelnek frissítéseket. Service Fabric összehasonlítja a szolgáltatási jegyzékfájlok verziószámait az előző verzió verziószámával. Ha egy szolgáltatás nem módosult, akkor a szolgáltatás nem frissül.

> [!NOTE]
> Az [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s nem őrződnek meg az alkalmazás verziófrissítése során. Az aktuális alkalmazás paramétereinek megőrzése érdekében a felhasználónak először le kell kérnie a paramétereket, és át kell adnia őket a frissítési API-híváshoz, például az alábbihoz:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>A működés közbeni frissítés áttekintése
A működés közbeni alkalmazások verziófrissítése során a rendszer a frissítést fázisokban hajtja végre. A frissítés minden fázisban a fürt csomópontjainak egy részhalmazára lesz alkalmazva, amelyet frissítési tartománynak nevezünk. Ennek eredményeképpen az alkalmazás a frissítés során továbbra is elérhető marad. A frissítés során a fürt a régi és az új verziók kombinációját is tartalmazhatja.

Ezért a két verziónak előre és visszafelé kompatibilisnek kell lennie. Ha nem kompatibilisek, az alkalmazás rendszergazdája a rendelkezésre állás fenntartása érdekében egy többfázisú frissítés előkészítésére szolgál. A többfázisú frissítés során az első lépés az alkalmazás egy közbenső verziójára frissül, amely kompatibilis az előző verzióval. A második lépés a végleges verzió frissítése, amely megszakítja a kompatibilitást a frissítés előtti verzióval, de kompatibilis a köztes verzióval.

Ha konfigurálja a fürtöt, a frissítési tartományok a fürt jegyzékfájljában vannak megadva. A frissítési tartományok megadott sorrendben nem kapják meg a frissítéseket. A frissítési tartomány az alkalmazások központi telepítésének logikai egysége. A frissítési tartományok lehetővé teszik, hogy a szolgáltatások magas rendelkezésre állásban maradjanak a frissítés során.

A nem működés közbeni verziófrissítések akkor lehetségesek, ha a frissítés a fürt összes csomópontjára vonatkozik, ami akkor fordul elő, ha az alkalmazásnak csak egy frissítési tartománya van. Ez a megközelítés nem ajánlott, mivel a szolgáltatás leáll, és a frissítéskor nem érhető el. Emellett az Azure nem biztosít garanciát, ha egy fürt csak egy frissítési tartománnyal van beállítva.

A frissítés befejeződése után az összes szolgáltatás és replika (példány) ugyanabban a verzióban marad – i. e., ha a frissítés sikeres, a rendszer frissíti az új verzióra; Ha a frissítés meghiúsul, és vissza lett állítva, a rendszer visszaállítja a régi verzióra.

## <a name="health-checks-during-upgrades"></a>Állapot-ellenőrzések a frissítések során
A frissítéshez meg kell adni az állapot-házirendeket (vagy az alapértelmezett értékeket lehet használni). A frissítés sikeresnek minősül, ha az összes frissítési tartomány frissül a megadott időkorláton belül, és ha az összes frissítési tartomány kifogástalannak minősül.  A kifogástalan állapotú frissítési tartomány azt jelenti, hogy a frissítési tartomány az állapotfigyelő házirendben megadott összes állapot-ellenőrzést átadta. Egy állapotházirend például megadhatja, hogy az alkalmazás példányain belüli összes szolgáltatásnak *kifogástalan* állapotú kell lennie, mivel az Service Fabric által meghatározott állapotot határozza meg.

Az állapot-szabályzatok és az ellenőrzések a Service Fabric általi frissítés során a szolgáltatás és az alkalmazás agnosztikus. Azaz nem végeznek szolgáltatás-specifikus teszteket.  Előfordulhat például, hogy a szolgáltatás átviteli követelményekkel rendelkezik, de Service Fabric nem rendelkezik az átviteli sebesség ellenőrzéséhez szükséges információkkal. A végrehajtott ellenőrzésekhez tekintse meg a [Health cikkeket](service-fabric-health-introduction.md) . A frissítés során végrehajtott ellenőrzések közé tartoznak a tesztek, hogy az alkalmazáscsomag megfelelően lett-e másolva, hogy a példány elindult-e, és így tovább.

Az alkalmazás állapota az alkalmazás alárendelt entitásának összesítése. Röviden, Service Fabric kiértékeli az alkalmazás állapotát az alkalmazáson jelentett állapottal. Ez a módszer az alkalmazás összes szolgáltatásának állapotát is kiértékeli. Service Fabric a gyermekek állapotának, például a szolgáltatás replikájának összesítésével tovább értékeli az alkalmazás-szolgáltatások állapotát. Ha az alkalmazás állapotára vonatkozó házirend teljesül, a frissítés folytatódhat. Ha az Állapotházirendek megsértették, az alkalmazás frissítése sikertelen lesz.

## <a name="upgrade-modes"></a>Frissítési módok
Az alkalmazások frissítéséhez javasolt mód a figyelt mód, amely a leggyakrabban használt mód. A figyelt mód egy frissítési tartományon hajtja végre a frissítést, és ha az összes állapot-ellenőrzés (a megadott házirend alapján), a rendszer automatikusan áthelyezi a következő frissítési tartományba.  Ha az állapot-ellenőrzés sikertelen és/vagy időtúllépést ér el, a rendszer visszaállítja a frissítést a frissítési tartományra, vagy a mód nem figyelt manuálisra változik. A frissítés konfigurálásával kiválaszthatja a következő két mód egyikét a sikertelen frissítésekhez. 

A nem figyelt manuális üzemmódnak manuális beavatkozásra van szüksége egy frissítési tartomány minden verziófrissítése után, hogy kirúgja a frissítést a következő frissítési tartományon. Nem történik Service Fabric állapot-ellenőrzés. A rendszergazda a következő frissítési tartomány frissítésének megkezdése előtt elvégzi az állapot-vagy az állapot-ellenőrzéseket.

## <a name="upgrade-default-services"></a>Alapértelmezett szolgáltatások frissítése
Az [alkalmazás jegyzékfájljában](service-fabric-application-and-service-manifests.md) meghatározott alapértelmezett szolgáltatási paraméterek az alkalmazások frissítésének részeként is frissíthetők. A frissítés részeként csak azok a szolgáltatási paraméterek módosíthatók, amelyek támogatják az [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice) használatával történő módosítást. Az alapértelmezett szolgáltatások az alkalmazás frissítése során történő megváltoztatásának viselkedése a következő:

1. A fürtben még nem létező új alkalmazás-jegyzékfájlban található alapértelmezett szolgáltatások jönnek létre.
2. Az előző és az új alkalmazás-jegyzékben található alapértelmezett szolgáltatások frissülnek. Az új alkalmazás jegyzékfájljában lévő alapértelmezett szolgáltatás paramétereinek felülírják a meglévő szolgáltatás paramétereit. Az alkalmazás frissítése automatikusan megtörténik, ha az alapértelmezett szolgáltatás frissítése sikertelen.
3. Az új alkalmazás jegyzékfájljában nem szereplő alapértelmezett szolgáltatások törlődnek, ha léteznek a fürtben. **Vegye figyelembe, hogy az alapértelmezett szolgáltatás törlésekor az összes szolgáltatás állapotát törölni fogja, és nem vonható vissza.**

Az alkalmazások frissítésének visszavonása után a rendszer az alapértelmezett szolgáltatási paramétereket visszaállítja a régi értékekre a frissítés megkezdése előtt, de a törölt szolgáltatások nem hozhatók létre újra régi állapotukkal.

> [!TIP]
> A [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) -fürt konfigurációs beállításának *igaznak* kell lennie a 2. és a 3. szabály engedélyezéséhez (az alapértelmezett szolgáltatás frissítése és törlése). Ez a funkció Service Fabric 5,5-es verziótól kezdődően támogatott.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Több alkalmazás frissítése HTTPS-végpontokkal
Ügyeljen arra, hogy ne **ugyanazt a portot** használja ugyanazon alkalmazás különböző példányaihoz, ha http-**t** használ. Ennek az az oka, hogy Service Fabric nem fogja tudni frissíteni az egyik alkalmazás példányának tanúsítványát. Ha például az 1. vagy a 2. alkalmazás egyaránt szeretné frissíteni az 1. tanúsítványát a 2. tanúsítványra. Ha a frissítés történik, előfordulhat, hogy Service Fabric megtisztította a tanúsítvány 1 regisztrációját http.sys annak ellenére, hogy a másik alkalmazás továbbra is használja azt. Ennek megelőzése érdekében Service Fabric észleli, hogy már van egy másik alkalmazás-példány regisztrálva a porton a tanúsítvánnyal (http.sys miatt), és a művelet sikertelen lesz.

Ezért Service Fabric nem támogatja két különböző szolgáltatás frissítését **ugyanazon a porton** keresztül különböző alkalmazás-példányokban. Más szóval nem használhatja ugyanazt a tanúsítványt ugyanazon a porton lévő különböző szolgáltatásokhoz. Ha megosztott tanúsítvánnyal kell rendelkeznie ugyanazon a porton, gondoskodnia kell arról, hogy a szolgáltatások elhelyezése a különböző gépeken történjen, elhelyezési korlátozásokkal. Ha lehetséges, érdemes Service Fabric dinamikus portokat használni, ha az egyes szolgáltatásokhoz az egyes alkalmazás-példányokban. 

Ha úgy látja, hogy a frissítés nem sikerül HTTPS-vel, a "Windows HTTP Server API nem támogatja több tanúsítvány használatát a portot használó alkalmazások számára."

## <a name="application-upgrade-flowchart"></a>Alkalmazás frissítési folyamatábrája
A bekezdést követő folyamatábra segít megérteni egy Service Fabric alkalmazás frissítési folyamatát. A folyamat konkrétan leírja, hogy az időtúllépések, beleértve a *HealthCheckStableDuration*, a *HealthCheckRetryTimeout* és a *UpgradeHealthCheckInterval*, Hogyan szabályozható, hogy az egyik frissítési tartományban való frissítés sikeresnek vagy meghibásodásnak minősül-e.

![Service Fabric alkalmazás frissítési folyamata][image]

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
