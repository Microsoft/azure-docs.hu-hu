---
title: VIRTUÁLIS gépekkel kapcsolatos ismeretek (GA) – gyakori kérdések | Microsoft Docs
description: A VM-információk az Azure-ban az Azure VM operációs rendszer állapotának és teljesítményének figyelését, valamint az alkalmazások összetevőinek és függőségeinek más erőforrásokkal való felismerését, valamint a közöttük zajló kommunikációt térképezik fel. Ez a cikk a GA kiadásával kapcsolatos gyakori kérdésekre ad választ.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 0c55463847e0bf55cf14db2a35de1de16526cd90
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710753"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>Általánosan elérhető a VM-ismeretek (GA) – gyakori kérdések
Ez az általános elérhetőségi gyakori kérdések a GA-ban felkészített, a 2019-as és a Q1 2020-es verzióban végrehajtott módosításokat fedik le.

## <a name="updates-for-vm-insights"></a>A VM-információk frissítései
Megjelent a VM-belátások új verziója a 2020-es verzióban, a GA-bejelentés előtt. A VM-megállapításokat lehetővé tévő ügyfelek mostantól megkapják a GA verzióját, de a meglévő ügyfelek a 2019-es és korábbi verziójú virtuálisgép-megállapítások verzióját fogják kérni a frissítésre. Ez a GYIK útmutatást nyújt a nagy méretekben történő frissítéshez, ha több munkaterületen van nagyméretű üzemelő példány.


Ezzel a frissítéssel Azure Monitor for VMs teljesítményadatokat a *InsightsMetrics* ugyanabban a táblázatban [tárolja, amely](../containers/container-insights-overview.md)megkönnyíti a két adathalmaz lekérdezését. Azt is megteheti, hogy több különböző adatkészletet is tárolhat, amelyeket nem sikerült tárolni a korábban használt táblázatban. 

A teljesítménnyel kapcsolatos nézeteink mostantól a *InsightsMetrics* táblában tárolt adathalmazokat használják.  Ha még nem frissítette a legújabb VMInsights-megoldás használatát a munkaterületen, a diagramok többé nem fognak adatokat megjeleníteni.  Az alább leírtak szerint frissíthet az **első lépések** lapról.


## <a name="what-is-changing"></a>Mi változik?
Megjelent egy VMInsights nevű új megoldás, amely az adatgyűjtés további funkcióit tartalmazza, valamint egy új helyet az adattároláshoz a Log Analytics munkaterületen. 

A múltban engedélyezte a ServiceMap megoldást a munkaterületen, és a Log Analytics munkaterületen a teljesítményszámlálók beállításával elküldheti az adatait a *perf* táblába. Ez az új megoldás a *InsightsMetrics* nevű táblába küldi az adatokat, amelyet a tároló-elemzések is használnak. Ez a táblázatos séma lehetővé teszi, hogy olyan további mérőszámokat és szolgáltatási adatkészleteket tároljon, amelyek nem kompatibilisek a *perf* Table formátumával.

Frissítettük a teljesítmény diagramokat a *InsightsMetrics* táblában tárolt adat használatához. Az alábbi lépésekkel frissítheti a *InsightsMetrics* táblázatot az **első lépések** oldaláról.


## <a name="how-do-i-upgrade"></a>Hogyan frissíteni?
Ha egy Log Analytics munkaterület a Azure Monitor legújabb verziójára frissül a virtuális gépekre, akkor a függőségi ügynököt az adott munkaterülethez csatlakoztatott összes virtuális gépre frissíti. A frissítést igénylő virtuális gépeket a Azure Portal virtuális gépek **beolvasása** lapján találhatja meg. Ha a virtuális gép frissítését választja, akkor az adott virtuális géphez tartozó munkaterületet a munkaterülethez csatolt többi virtuális géppel együtt frissíti. Egyetlen vagy több virtuális GÉPET, erőforráscsoportot vagy előfizetést is kijelölhet. 

A következő parancs használatával frissíthet egy munkaterületet a PowerShell használatával:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Mi a teendő a saját munkaterület teljesítményszámlálói esetében, ha a VMInsights-megoldást telepítem?

Az előző módszer, amely lehetővé teszi, hogy a VM-megállapítások a munkaterületen lévő teljesítményszámlálókat használják. Az aktuális verzió egy nevű táblában tárolja ezeket az adattárakat `InsightsMetrics` . Dönthet úgy, hogy letiltja ezeket a teljesítményszámlálók a munkaterületen, ha már nincs szüksége rájuk. 

>[!NOTE]
>Ha vannak olyan riasztási szabályok, amelyek hivatkoznak ezekre a számlálóra a `Perf` táblában, frissítenie kell őket a táblázatban tárolt új adatértékekre való hivatkozáshoz `InsightsMetrics` . Tekintse meg a dokumentációt, például a táblázatra hivatkozó log-lekérdezéseket.
>

Ha úgy dönt, hogy a teljesítményszámlálók engedélyezve vannak, akkor a `Perf` (z) [log Analytics díjszabás [() alapján a táblázatban betöltött és tárolt adatmennyiség után kell fizetnie https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hogyan befolyásolja ez a változás a riasztási szabályokat?

Ha olyan [naplózási riasztásokat](../alerts/alerts-unified-log.md) hozott létre, amelyek a (z `Perf` ) munkaterületen engedélyezett teljesítményszámlálók célzási teljesítménymutatóit kérdezik le, akkor ezeket a szabályokat úgy kell frissíteni, hogy a `InsightsMetrics` táblázatra hivatkozzon. Ez az útmutató a és a használatával történő naplózási keresési szabályokra is vonatkozik `ServiceMapComputer_CL` `ServiceMapProcess_CL` , mivel ezek az adathalmazok `VMComputer` és táblákra vannak áthelyezve `VMProcess` .

Frissíteni fogjuk ezt a GYIK-t és a dokumentációt, amely tartalmazza például a begyűjtött adathalmazokra vonatkozó naplóbeli keresési riasztási szabályokat.

## <a name="how-will-this-affect-my-bill"></a>Hogyan befolyásolja ez a számla?

A számlázás továbbra is a Log Analytics munkaterületen betöltött és megőrzött adatmennyiségen alapul.

A begyűjtött gépi szintű teljesítményadatok azonos méretűek, mint a táblázatban tárolt adatok, `Perf` és körülbelül azonos mennyiségre lesznek számítva.

## <a name="what-if-i-only-want-to-use-service-map"></a>Mi a teendő, ha csak a Service Map szeretném használni?

Ez rendben van. A következő frissítéssel kapcsolatos virtuálisgép-információk megtekintésekor megjelenik a Azure Portal promptok. A kiadást követően az új verzióra való frissítést kérő üzenet jelenik meg. Ha inkább csak a [Maps](vminsights-maps.md) szolgáltatást szeretné használni, válassza a nem a frissítés lehetőséget, és továbbra is használja a Maps szolgáltatást a virtuális gépekhez, és a munkaterületről vagy az irányítópult csempéről elérhető Service Map megoldást.

Ha úgy dönt, hogy manuálisan engedélyezte a teljesítményszámlálók számát a munkaterületen, akkor előfordulhat, hogy az adatok megtekinthetők a Azure Monitor alapján megtekintett teljesítmény-diagramokban. Az új megoldás kiadása után a rendszer frissíti a teljesítmény diagramokat a táblázatban tárolt adat lekérdezéséhez `InsightsMetrics` . Ha az adott táblából származó adatokkal szeretné megtekinteni ezeket a diagramokat, a VM-elemzések új verziójára kell frissítenie.

Az adatok áthelyezésének módosításai és a Service Map és a VM-elemzések is `ServiceMapComputer_CL` `ServiceMapProcess_CL` hatással lesznek, így továbbra is meg kell terveznie ezt a frissítést.

Ha úgy döntött, hogy nem frissít a **VMInsights** -megoldásra, továbbra is biztosítjuk a teljesítmény-munkafüzetek örökölt verzióit, amelyek a táblázatban található adatokra hivatkoznak `Perf` .  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>A rendszer a Service Map adatkészleteket is tárolja a InsightsMetrics-ben?

Ha mindkét megoldást használja, az adatkészletek nem lesznek duplikálva. Mindkét ajánlat megosztja azokat az adatkészleteket, amelyeket a rendszer a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), a `VMConnection` és `VMBoundPort` a táblákban tárol, és így tárolja a gyűjtött térképi adatkészleteket.  

A `InsightsMetrics` táblázat tárolja azokat a virtuális gépeket, folyamatokat és szolgáltatási adatkészleteket, amelyeket gyűjtünk, és csak akkor lesznek feltöltve, ha a VM-elemzéseket és a virtuálisgép-elemzéseket használja. A Service Map megoldás nem gyűjti és nem tárolja az adatokat a `InsightsMetrics` táblában.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Kell-e dupla díjat fizetni, ha a saját munkaterületen van Service Map-és VMInsights-megoldásom?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), és rendszerekben tárolt Térkép-adatkészleteket `VMConnection` `VMBoundPort` . Ha mindkét megoldás szerepel a munkaterületen, akkor nem számítunk fel díjat.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Ha eltávolítom vagy a Service Map vagy a VMInsights megoldást, akkor eltávolítja az adataimat?

Nem, a két megoldás közösen tárolja a `VMComputer` (korábban ServiceMapComputer_CL), `VMProcess` (korábban ServiceMapProcess_CL), és rendszerekben tárolt Térkép-adatkészleteket `VMConnection` `VMBoundPort` . Ha eltávolítja az egyik megoldást, ezek az adatkészletek azt észlelik, hogy még mindig van olyan megoldás, amely az adatmennyiséget használja, és a Log Analytics munkaterületen marad. Mindkét megoldást el kell távolítania a munkaterületről abból a célból, hogy az adatok el legyenek távolítva belőle.

## <a name="health-feature-is-in-limited-public-preview"></a>A Health funkció korlátozott nyilvános előzetes verzióban érhető el

Nagy mennyiségű visszajelzést kaptunk az ügyfelektől a VM Health szolgáltatás készletével kapcsolatban. Ez a funkció nagy érdeklődést mutat a figyelési munkafolyamatok támogatásával kapcsolatban. Azt tervezzük, hogy egy sor módosítást teszünk elérhetővé a funkciók hozzáadásához és a kapott visszajelzések kezeléséhez. 

Ennek a változásnak az új ügyfelekre gyakorolt hatásának csökkentése érdekében a funkciót **korlátozott nyilvános előzetes** verzióra helyezte át. Ez a frissítés 2019 októberében történt.

Azt tervezzük, hogy az 2020-as verzióban indítsa újra ezt az állapot-szolgáltatást, miután a VM-bepillantást követően a GA-ban található.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hogyan érik el a meglévő ügyfelek az állapotfigyelő funkciót?

Az állapotfigyelő funkciót használó meglévő ügyfelek továbbra is hozzáférhetnek hozzájuk, de nem lesznek elérhetők az új ügyfelek számára.  

A szolgáltatás eléréséhez adja hozzá a következő funkció jelzőjét `feature.vmhealth=true` a Azure Portal URL-címhez [https://portal.azure.com](https://portal.azure.com) . Példa `https://portal.azure.com/?feature.vmhealth=true` .

Ezt a rövid URL-címet is használhatja, amely automatikusan beállítja a szolgáltatás jelölőjét: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Meglévő ügyfélként továbbra is használhatja az állapotfigyelő szolgáltatást olyan virtuális gépeken, amelyek a meglévő munkaterület-beállításhoz vannak csatlakoztatva az állapotfigyelő funkcióval.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>A VM Health-t most egy környezettel használom, és egy újat szeretnék telepíteni

Ha Ön az a felhasználó, aki az állapotfigyelő funkciót használja, és új bevezetésre szeretné használni, lépjen kapcsolatba velünk a következő címen: vminsights@microsoft.com .

## <a name="next-steps"></a>Következő lépések

A virtuális gépek figyeléséhez szükséges követelmények és módszerek megismeréséhez tekintse át a VM-alapú adatvizsgálatok [üzembe helyezését ismertető témakört](./vminsights-enable-overview.md).
