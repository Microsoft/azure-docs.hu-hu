---
title: OMS-portál áthelyezése az Azure-ba | Microsoft Docs
description: A OMS-portál a Azure Portalra áthelyezett összes funkcióval elérhetővé vált. Ez a cikk az áttérés részleteit ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 0fc57b87e5ec1d7f47d9f9d74698af56172246ec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617495"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portál áthelyezése az Azure-ba

> [!NOTE]
> Ez a cikk az Azure-beli nyilvános felhőre és a kormányzati felhőre is érvényes, kivéve, ha ez másképpen szerepel.

**Az Azure nyilvános felhőhöz készült OMS-portált hivatalosan kivonták. Az Azure US government Cloud OMS-portált hivatalosan 2019. május 15-én visszavonták.** Izgatottan várjuk a Azure Portalre való áttérést, és az átállás egyszerűvé vált. De tisztában vagyunk vele, hogy a változások nehézek, és zavaró lehet. A cikk további részében a főbb forgatókönyvek és az áttérés ütemterve is meghalad.

A Azure Portal az összes Azure-szolgáltatás központja, és gazdag felügyeleti élményt nyújt olyan funkciókkal, mint az irányítópultok az erőforrások kitűzéséhez, az intelligens keresés az erőforrások kereséséhez és a címkézés az erőforrás-kezeléshez. A monitorozási és felügyeleti munkafolyamatok megszilárdítása és egyszerűsítése érdekében a OMS-portál képességeinek a Azure Portalba való felvételét kezdtük el. A OMS-portál összes funkciója mostantól a Azure Portal része. Valójában az új szolgáltatások, például a Traffic Analytics csak a Azure Portal érhetők el. A OMS-portálon mindent megtesz, ami a Azure Portal és egyebek mellett is elvégezhető. Ha még nem tette meg, kezdje el használni a Azure Portal még ma!

## <a name="what-is-changing"></a>Mi változik? 
Az alábbi módosítások a OMS-portál elavult változatával lesznek bejelentve. A módosításokat az alábbi szakaszokban részletesebben ismertetjük.

- Új [munkaterületeket csak](#new-workspaces) a Azure Portal hozhat létre.
- Az új riasztások kezelése [a Alert Management megoldást váltja](#changes-to-alerts)fel.
- A [felhasználói hozzáférés kezelése](#user-access-and-role-migration) a Azure Portal az Azure szerepköralapú hozzáférés-vezérlés használatával történik.
- A [Application Insights Connectorra már nincs szükség,](#application-insights-connector-and-solution) mert ugyanaz a funkció több munkaterület-lekérdezésen keresztül is engedélyezve van.
- A [OMS Mobile alkalmazás](#oms-mobile-app) elavult. 
- Az [NSG-megoldás cseréje](#azure-network-security-group-analytics) Traffic Analytics megoldáson keresztül elérhető továbbfejlesztett funkciókkal történik.
- A System Center Operations Manager új kapcsolatainak Log Analytics a [frissített felügyeleti csomagokat](#system-center-operations-manager)kell megkövetelniük.
- A [Update Management](../../automation/update-management/overview.md)változásairól további információt a [OMS-frissítés telepítése az Azure-](../../automation/migrate-oms-update-deployments.md) ban című témakörben talál.


## <a name="what-should-i-do-now"></a>Mit tegyek most?
Habár a legtöbb funkció az áttelepítés végrehajtása nélkül is működni fog, a következő feladatokat kell végrehajtania:

- A [felhasználói engedélyeket át kell telepítenie](#user-access-and-role-migration) a Azure Portalra.
- A Update Management megoldás áttelepítésének részleteiért tekintse meg a [OMS-frissítés telepítése az Azure-ba](../../automation/migrate-oms-update-deployments.md) című témakört.

Tekintse át a [OMS Portálról való áttérés gyakori kérdéseit, hogy log Analytics felhasználók számára Azure Portal](../overview.md) a Azure Portalre való áttéréssel kapcsolatos tudnivalókat. 

## <a name="user-access-and-role-migration"></a>Felhasználói hozzáférés és szerepkör áttelepítése
A Azure Portal hozzáférés-vezérlés gazdagabb és hatékonyabb, mint a OMS-portálon a hozzáférés-kezelés. A Log Analytics hozzáférés-kezelésének részleteiért lásd: [a Azure monitor naplók munkaterületének megtervezése](../logs/design-logs-deployment.md) .

> [!NOTE]
> A cikk korábbi verziói azt nyilatkozták, hogy az engedélyek automatikusan át lesznek alakítva a OMS Portálról a Azure Portalra. Ez az automatikus átalakítás már nem tervezett, és magát a konverziót kell végrehajtania.

Lehetséges, hogy már rendelkezik megfelelő hozzáféréssel a Azure Portalban, amely esetben nem szükséges módosítania. Vannak olyan esetek, amikor Ön nem rendelkezik megfelelő hozzáféréssel, ebben az esetben a rendszergazdának Önnek kell engedélyeket rendelnie.

- ReadOnly felhasználói engedélyekkel rendelkezik a OMS-portálon, de a Azure Portal nem rendelkezik engedélyekkel. 
- Közreműködői engedélyekkel rendelkezik a OMS-portálon, de csak az olvasó fér hozzá a Azure Portal.
 
Mindkét esetben a rendszergazdának manuálisan kell hozzárendelnie a megfelelő szerepkört a következő táblázatból. Azt javasoljuk, hogy ezt a szerepkört az erőforráscsoport vagy az előfizetés szintjén rendelje hozzá.  Ezekben az esetekben a rendszer hamarosan részletesebb útmutatást nyújt.

| OMS-portál engedélye | Azure-szerepkör |
|:---|:---|
| ReadOnly | Log Analytics olvasó |
| Közreműködő | Log Analytics közreműködő |
| Rendszergazda | Tulajdonos | 
 

## <a name="new-workspaces"></a>Új munkaterületek
Már nem tud új munkaterületeket létrehozni a OMS-portál használatával. A Azure Portal új munkaterületének létrehozásához kövesse az [log Analytics munkaterületének létrehozása a Azure Portalban](../learn/quick-create-workspace.md) című témakör útmutatását.

## <a name="changes-to-alerts"></a>Riasztások módosításai

### <a name="alert-extension"></a>Riasztási bővítmény  

A riasztások [kiterjeszthetők a Azure Portal](../alerts/alerts-unified-log.md) meglévő riasztásokra továbbra is MEGJELENNEK a OMS-portálon, de csak Azure Portal kezelhetik. Ha programozott módon fér hozzá a riasztásokhoz a Log Analytics riasztási REST API vagy Log Analytics a riasztási erőforrás-sablon használatával, az API-hívások, a Azure Resource Manager sablonok és a PowerShell-parancsok műveletei helyett műveleti csoportokat kell használnia.

### <a name="alert-management-solution"></a>Riasztások kezelése megoldás
Egy korábbi bejelentés változása esetén a [riasztások kezelésére szolgáló megoldás](../platform/alert-management-solution.md) továbbra is elérhető lesz, és a Azure Portal teljes mértékben támogatja. Továbbra is telepítheti a megoldást az Azure Marketplace-ről.

Habár a riasztások kezelésére szolgáló megoldás továbbra is elérhető, javasoljuk, hogy [Azure monitor egyesített riasztási felületét](../platform/alerts-overview.md) használja az összes Azure-beli riasztás megjelenítéséhez és kezeléséhez. Ez az új élmény natív módon összesíti a több forrásból származó riasztásokat az Azure-ban, beleértve a Log Analytics érkező riasztásokat. Ha Azure Monitor egyesített riasztási felületét használja, a riasztások kezelésére szolgáló megoldás csak a riasztások a System Center Operation Managerből az Azure-ba való integrálásának engedélyezéséhez szükséges. Azure Monitor egyesített riasztási felületén láthatja a riasztások eloszlását, kihasználhatja a kapcsolódó riasztások automatizált csoportosítását intelligens csoportok használatával, és több előfizetésben is megtekintheti a riasztásokat, miközben gazdag szűrőket alkalmaz. A riasztások kezelésének jövőbeli fejlődése elsődlegesen az új felhasználói felületről lesz elérhető. 

A riasztás-felügyeleti megoldás által gyűjtött adatok (a riasztási típussal rendelkező rekordok) továbbra is Log Analytics, amíg a megoldás telepítve van a munkaterületre. 

## <a name="oms-mobile-app"></a>OMS Mobile-alkalmazás
A OMS Mobile alkalmazás a OMS-portálral együtt lesz kivezetve. A OMS Mobile App helyett az informatikai infrastruktúrával, az irányítópultokkal és a mentett lekérdezésekkel kapcsolatos információkhoz férhet hozzá a Azure Portal közvetlenül a mobileszközön lévő böngészőjéből. Riasztások beszerzéséhez konfigurálnia kell az [Azure-műveleti csoportokat](../alerts/action-groups.md) , hogy SMS vagy hanghívás formájában fogadják az értesítéseket

## <a name="application-insights-connector-and-solution"></a>Application Insights Connector és megoldás
A [Application Insights Connector](../logs/app-insights-connector.md) lehetővé teszi Application Insights-adatLog Analytics munkaterületre való felvételét. Ez az adatismétlődés az infrastruktúra és az alkalmazásadatok láthatóságának engedélyezéséhez szükséges. A 2019 márciusában kibővített adatmegőrzési támogatással és az [erőforrások több Azure Monitor Application Insights erőforrással](../log-query/unify-app-resource-data.md) [való ellátásának](../logs/cross-workspace-query.md) lehetősége mellett nem szükséges duplikálni az Application Insights-erőforrások adatait, és nem kell elküldeni a log Analyticsba. Application Insights Emellett az összekötő az alkalmazások tulajdonságainak egy részhalmazát is elküldi Log Analyticsra, a több erőforrásra kiterjedő lekérdezések pedig fokozott rugalmasságot biztosítanak.  

Ennek megfelelően a Application Insights Connector elavult, és az Azure Marketplace-en lett eltávolítva, valamint a OMS-portál elavult a 2019. március 30-án. A meglévő kapcsolatok 2019 június 30-ig továbbra is működni fognak. A OMS-portál elavult használata esetén nincs lehetőség a meglévő kapcsolatok konfigurálására és eltávolítására a portálon. Ez a 2019 januárban elérhetővé válik, és REST API az [Azure Updates](https://azure.microsoft.com/updates/)szolgáltatásban értesítést fog kapni. 

## <a name="azure-network-security-group-analytics"></a>Azure Network Security Group Analytics
A [Azure Network Security Group Analytics megoldást](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) a nemrég elindított [Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) váltja fel, amely betekintést nyújt a felhasználói és alkalmazási tevékenységekre a felhőalapú hálózatokon. Traffic Analytics segítségével naplózhatja a szervezet hálózati tevékenységeit, biztonságossá teheti az alkalmazásokat és az adatok védelmét, optimalizálhatja a számítási feladatok teljesítményét és megfelelő marad. 

Ez a megoldás elemzi a NSG, és betekintést nyújt a következőkbe.

- A forgalom az Azure és az Internet, a nyilvános felhő régiói, a virtuális hálózatok és az alhálózatok között zajlik.
- A hálózaton lévő alkalmazások és protokollok anélkül, hogy a szippantás vagy a dedikált flow gyűjtemény berendezésekre van szükség.
- A legfontosabb beszélők, a csevegő alkalmazások, a felhőben futó virtuális gépek, a forgalmi pontok.
- A virtuális hálózatok közötti forgalom forrásai és célhelyei, a kritikus fontosságú üzleti szolgáltatások és alkalmazások közötti kapcsolatok közötti kapcsolat.
- Biztonság, beleértve a rosszindulatú adatforgalmat, az internetre nyitott portokat, az alkalmazásokat vagy a virtuális gépeket, amelyek internetes hozzáférést próbálnak meg.
- Kapacitás kihasználtsága, amely segít a kiépítési vagy kihasználatlan problémák elhárításában.

Továbbra is használhatja a diagnosztikai beállításokat, hogy NSG-naplókat küldjön, hogy Log Analytics, így a meglévő mentett keresések, riasztások, irányítópultok továbbra is működni fognak. Azok a felhasználók, akik már telepítették a megoldást, továbbra is használhatják a további értesítésig. Szeptember 5-ig a hálózati biztonsági csoport elemzési megoldását a rendszer eltávolítja a piactérről, és a Közösségen keresztül elérhetővé teszi az Azure-beli [Gyorsindítás sablonként](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Ha [log Analyticshoz csatlakoztatta a Operations Manager felügyeleti csoportot](../agents/om-agents.md), akkor továbbra is a módosítások nélkül fog működni. Az új kapcsolatok esetében azonban az [Operations Management Suite konfigurálásához követnie kell a Microsoft System Center Operations Manager felügyeleti csomag](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog)útmutatását.

## <a name="next-steps"></a>Következő lépések
- Tekintse át a [OMS Portálról való áttérés gyakori kérdéseit, hogy Azure Portal log Analytics felhasználók](../overview.md) számára a OMS Portálról a Azure Portalre való áthelyezéshez.