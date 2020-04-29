---
title: Azure Monitor for VMs Térkép integrálása Operations Managerkal | Microsoft Docs
description: Azure Monitor for VMs automatikusan feltérképezi az alkalmazás-összetevőket Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ebből a cikkből megtudhatja, hogyan hozhat létre automatikusan elosztott alkalmazás-diagramokat a Operations Manager a Térkép funkcióval.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663453"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>System Center Operations Manager integrálása Azure Monitor for VMs Térkép funkcióval

Azure Monitor for VMs a felderített alkalmazás-összetevőket megtekintheti az Azure-ban vagy a környezetben futó Windows-és Linux-alapú virtuális gépeken (VM-EK). A Térkép funkció és a System Center Operations Manager közötti integráció révén automatikusan létrehozhat elosztott alkalmazási diagramokat a Azure Monitor for VMs dinamikus függőségi térképén alapuló Operations Manager. Ez a cikk azt ismerteti, hogyan konfigurálhatja a System Center Operations Manager felügyeleti csoportot a funkció támogatásához.

>[!NOTE]
>Ha már telepítette Service Map, megtekintheti a térképeit Azure Monitor for VMsban, amely a virtuális gépek állapotának és teljesítményének figyelésére szolgáló további funkciókat is tartalmaz. A Azure Monitor for VMs Térkép funkciója a különálló Service Map megoldás cseréjére szolgál. További információ: [Azure monitor for VMS Overview (áttekintés](vminsights-overview.md)).

## <a name="prerequisites"></a>Előfeltételek

* Egy System Center Operations Manager felügyeleti csoport (2012 R2 vagy újabb).
* Azure Monitor for VMs támogatására konfigurált Log Analytics munkaterület.
* Egy vagy több Windows-és Linux-alapú virtuális gép vagy fizikai számítógép, amelyet Operations Manager figyel, és adatokat küld a Log Analytics munkaterületre. A Operations Manager felügyeleti csoportnak jelentést küldő Linux-kiszolgálókat úgy kell konfigurálni, hogy közvetlenül kapcsolódjanak Azure Monitorhoz. További információkért tekintse át a [naplófájlok adatainak összegyűjtése a log Analytics ügynökkel](../platform/log-analytics-agent.md)című témakör áttekintését.
* Az Log Analytics munkaterülethez társított Azure-előfizetéshez hozzáférő egyszerű szolgáltatás. További információ: [egyszerű szolgáltatásnév létrehozása](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>A Service Map felügyeleti csomag telepítése

A Microsoft. SystemCenter. ServiceMap felügyeleti csomag (Microsoft. SystemCenter. ServiceMap. MPB) importálásával engedélyezheti a Operations Manager és a Térkép funkció közötti integrációt. A felügyeleti csomag csomagját a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=55763)töltheti le. A csomag a következő felügyeleti csomagokat tartalmazza:

* A Microsoft Service Map alkalmazás nézetei
* A Microsoft System Center Service Map belső
* A Microsoft System Center Service Map felülbírálásai
* Microsoft System Center Service Map

## <a name="configure-integration"></a>Integráció konfigurálása

Miután telepítette a Service Map felügyeleti csomagot, a Operations Manager operatív konzol **Adminisztráció** paneljén az **Operations Management Suite** alatt megjelenik egy új csomópont, **Service Map**.

>[!NOTE]
>Az [Operations Management Suite a log Analytics részét képező szolgáltatások gyűjteménye volt](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) , immár [Azure monitor](../overview.md)része.

Azure Monitor for VMs Map-integráció konfigurálásához tegye a következőket:

1. A konfigurációs varázsló megnyitásához a **Service Map áttekintés** ablaktáblán kattintson a **munkaterület hozzáadása**elemre.  

    ![Service Map áttekintés panel](media/service-map-scom/scom-configuration.png)

2. A **kapcsolatok konfigurációja** ablakban adja meg a bérlő nevét vagy azonosítóját, az alkalmazás azonosítóját (más néven Felhasználónév vagy clientID), valamint az egyszerű szolgáltatásnév jelszavát, majd kattintson a **tovább**gombra. További információ: egyszerű szolgáltatásnév létrehozása.

    ![A kapcsolatok konfigurációs ablaka](media/service-map-scom/scom-config-spn.png)

3. Az **előfizetés kiválasztása** ablakban válassza ki az Azure-előfizetést, az Azure-erőforráscsoportot (amely a log Analytics munkaterületet tartalmazza) és log Analytics munkaterületet, majd kattintson a **tovább**gombra.

    ![A Operations Manager konfigurációs munkaterület](media/service-map-scom/scom-config-workspace.png)

4. A **gépi csoport kiválasztása** ablakban kiválaszthatja, hogy mely Service Map a Operations Manager szinkronizálni kívánt számítógépcsoportok. Kattintson a számítógépcsoportok **hozzáadása/eltávolítása**elemre, válassza ki a csoportokat az **elérhető**számítógépcsoportok listájából, majd kattintson a **Hozzáadás**gombra.  Amikor befejezte a csoportok kijelölését, kattintson **az OK** gombra a befejezéshez.

    ![A Operations Manager konfigurációs számítógépcsoportok](media/service-map-scom/scom-config-machine-groups.png)

5. A **kiszolgáló kiválasztása** ablakban a Service Map-kiszolgálók csoportot a Operations Manager és a Térkép funkció között szinkronizálni kívánt kiszolgálókkal konfigurálja. Kattintson a **kiszolgálók hozzáadása/eltávolítása**elemre.

    Ahhoz, hogy az integráció egy elosztott alkalmazási diagramot hozzon létre egy kiszolgálóhoz, a kiszolgálónak a következőnek kell lennie:

   * Figyelte Operations Manager
   * Úgy van konfigurálva, hogy az Log Analytics munkaterületre jelentsen Azure Monitor for VMs
   * A Service Map-kiszolgálók csoportban szerepel

     ![A Operations Manager konfigurációs csoport](media/service-map-scom/scom-config-group.png)

6. Nem kötelező: válassza a minden felügyeleti kiszolgáló erőforráskészlet elemet, hogy kommunikáljon Log Analyticsokkal, majd kattintson a **munkaterület hozzáadása**lehetőségre.

    ![A Operations Manager konfigurációs erőforráskészlet](media/service-map-scom/scom-config-pool.png)

    A Log Analytics munkaterület konfigurálásához és regisztrálásához egy percet is igénybe vehet. A konfigurálást követően Operations Manager elindítja az első Térkép-szinkronizálást.

    ![A Operations Manager konfigurációs erőforráskészlet](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integráció monitorozása

A Log Analytics munkaterület csatlakoztatása után az Operations Manager operatív konzol **figyelés** ablaktábláján megjelenik egy új mappa, Service map.

![Az Operations Manager figyelési panel](media/service-map-scom/scom-monitoring.png)

A Service Map mappa négy csomóponttal rendelkezik:

* **Aktív riasztások**: felsorolja az Operations Manager és Azure monitor közötti kommunikációval kapcsolatos összes aktív riasztást.  

  >[!NOTE]
  >Ezek a riasztások nem Log Analytics a Operations Managersal szinkronizált riasztásokat, a Service Map felügyeleti csomagban meghatározott munkafolyamatok alapján jönnek létre a felügyeleti csoportban.

* **Kiszolgálók**: felsorolja azokat a figyelt kiszolgálókat, amelyek Azure monitor for VMS térképi szolgáltatásból való szinkronizálásra vannak konfigurálva.

    ![A Operations Manager figyelési kiszolgálók panel](media/service-map-scom/scom-monitoring-servers.png)

* **Számítógép-függőségi nézetek**: a Térkép szolgáltatásból szinkronizált összes számítógépcsoport listája. A bármely csoportra kattintva megtekintheti az elosztott alkalmazás diagramját.

    ![Az Operations Manager elosztott alkalmazás diagramja](media/service-map-scom/scom-group-dad.png)

* **Kiszolgáló-függőségi nézetek**: felsorolja az összes olyan kiszolgálót, amely szinkronizálva van a Térkép szolgáltatásból. A bármely kiszolgálóra kattintva megtekintheti az elosztott alkalmazás diagramját.

    ![Az Operations Manager elosztott alkalmazás diagramja](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Munkaterület szerkesztése vagy törlése

A konfigurált munkaterületet szerkesztheti vagy törölheti a **Service Map áttekintés** paneljén (**felügyeleti** ablaktábla > **Operations Management Suite** > **Service Map**).

>[!NOTE]
>Az [Operations Management Suite a log Analytics részét képező szolgáltatások gyűjteménye volt](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) , amely már [Azure monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)része.

Ebben az aktuális kiadásban csak egy Log Analytics munkaterületet lehet konfigurálni.

![A munkaterület szerkesztése ablaktábla Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Szabályok és felülbírálások konfigurálása

A szabály, a *Microsoft. SystemCenter. ServiceMapImport. Rule*, rendszeres időközönként beolvassa az adatokat Azure monitor for VMS Térkép szolgáltatásból. A szinkronizálási időköz módosításához felülbírálhatja a szabályt, és módosíthatja a **IntervalMinutes**paraméter értékét.

![A Operations Manager felülbírálások tulajdonságai ablak](media/service-map-scom/scom-overrides.png)

* **Engedélyezve**: az automatikus frissítések engedélyezése vagy letiltása.
* **IntervalMinutes**: a frissítések közötti időpontot határozza meg. Az alapértelmezett intervallum egy óra. Ha gyakrabban szeretné szinkronizálni a térképeket, módosíthatja az értéket.
* **TimeoutSeconds**: Megadja azt az időtartamot, ameddig a kérelem időtúllépése megtelt.
* **TimeWindowMinutes**: az adatlekérdezés időtartományát adja meg. Az alapértelmezett érték 60 perc, amely a maximálisan engedélyezett időköz.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az aktuális terv a következő problémákat és korlátozásokat mutatja be:

* Csak egyetlen Log Analytics-munkaterülethez csatlakozhat.
* Bár a **szerzői műveletek** ablaktáblán manuálisan adhat hozzá kiszolgálókat a Service Map-kiszolgálók csoportjához, a kiszolgálók leképezései nincsenek azonnal szinkronizálva. A következő szinkronizálási ciklusban szinkronizálva lesznek a Azure Monitor for VMs Térkép szolgáltatásból.
* Ha módosítja a felügyeleti csomag által létrehozott elosztott alkalmazás-diagramokat, a módosítások valószínűleg felül lesznek írva a következő szinkronizáláskor Azure Monitor for VMs.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos hivatalos Azure-dokumentációért lásd:

* [Egyszerű szolgáltatásnév létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Egyszerű szolgáltatásnév létrehozása az Azure CLI használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Egyszerű szolgáltatásnév létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Visszajelzés
Van-e visszajelzése a Azure Monitor for VMs Map szolgáltatással való integrációról vagy a dokumentációról? Látogasson el a [felhasználói hang oldalra](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), ahol javaslatot tehet a funkciókra, vagy szavazhat a meglévő javaslatokról.
