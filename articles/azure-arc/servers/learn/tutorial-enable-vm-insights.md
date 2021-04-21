---
title: Oktatóanyag – Hibrid gép figyelése virtuális Azure Monitor elemzésekkel
description: Megtudhatja, hogyan gyűjthet és elemezhet adatokat egy hibrid gépről a Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834263"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Oktatóanyag: Hibrid gép figyelése virtuálisgép-elemzésekkel

[Azure Monitor](../../../azure-monitor/overview.md) közvetlenül a hibrid gépekről gyűjthet adatokat egy Log Analytics-munkaterületre részletes elemzés és korreláció céljából. Ez általában azzal jár, hogy a [Log Analytics-ügynököt](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) szkript, manuális vagy automatizált módszer használatával telepíti a gépre, a konfigurációkezelési szabványoknak megfelelően. Az Arc-kompatibilis kiszolgálók a közelmúltban bevezették a Log Analytics és [](../../../azure-monitor/vm/vminsights-overview.md) a Dependency [Agent](../manage-vm-extensions.md) virtuálisgép-bővítmények windowsos és linuxos telepítését, ami lehetővé teszi, hogy a virtuálisgép-elemzések adatokat gyűjtsenek a nem Azure-beli virtuális gépekről.

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja és gyűjtheti az adatokat a Linux vagy Windows rendszerű gépekről a vm-elemzések engedélyezésével egy egyszerűsített lépéskészlettel, amely leegyszerűsíti a felhasználói élményt, és rövidebb időt vesz igénybe.  

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* A virtuálisgép-bővítmény funkciói csak a támogatott régiók [listájában érhetők el.](../overview.md#supported-regions)

* Lásd: [Támogatott operációs rendszerek annak](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) biztosításához, hogy a virtuálisgép-elemzések támogatják-e az Ön által támogatott kiszolgálók operációs rendszerét.

* Tekintse át a Log Analytics-ügynök tűzfalkövetelményét a [Log Analytics-ügynök áttekintésében.](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements) A VM-elemzések függőségi leképezés ügynöke maga nem továbbít adatokat, és nem igényli a tűzfalak vagy portok módosításait.

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Virtuálisgép-elemzések engedélyezése

1. Indítsa el a Azure Arc szolgáltatást a Azure Portal a **Minden** szolgáltatás elemre kattintva, majd a Gépek **–** Azure Arc.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Arc-kompatibilis kiszolgálók keresése a Minden szolgáltatásban" border="false":::

1. A **Gépek – Azure Arc** lapon válassza ki a rövid útmutatóban létrehozott [csatlakoztatott](quick-enable-hybrid-vm.md) gépet.

1. A bal oldali panel Figyelés szakaszában **válassza** az **Elemzések,** majd az Engedélyezés **lehetőséget.**

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Válassza az Elemzések lehetőséget a bal oldali menüben" border="false":::

1. A Azure Monitor Insights **Onboarding (Elemzések létrehozása)** lapon a rendszer felkéri egy munkaterület létrehozására. Ebben az oktatóanyagban nem javasoljuk, hogy meglévő Log Analytics-munkaterületet válasszon, ha már rendelkezik ilyen munkaterülettel. Válassza ki az alapértelmezett munkaterületet, amely egyedi névvel rendelkezik ugyanabban a régióban, mint a regisztrált csatlakoztatott gép. Ez a munkaterület az Ön számára jön létre és konfigurálva lesz.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Virtuálisgép-elemzések engedélyezése oldal" border="false":::

1. A konfiguráció során állapotüzeneteket kap. Ez a folyamat eltarthat néhány percig, amíg a bővítmények telepítve vannak a csatlakoztatott gépen.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Virtuálisgép-elemzések állapotüzenetének engedélyezése" border="false":::

    Ha elkészült, egy üzenet jelenik meg arról, hogy a gép sikeresen üzembe lett automativa, és a betekintés sikeresen üzembe lett stb.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Az üzembe helyezés és a konfigurálás befejezése után válassza az **Elemzések** lehetőséget, majd a **Teljesítmény lapot.** A Teljesítmény lapon megjelenik a számítógép vendég operációs rendszeréről gyűjtött teljesítményszámlálók kiválasztott csoportja. Görgessen le további számlálók megtekintéséhez, és helyezze az egérmutatót egy grafikonra a Log Analytics virtuálisgép-bővítmény a gépre való telepítésekor kezdődő átlagos és percentilisek megtekintéséhez.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Vm Insights Teljesítménydiagramok a kiválasztott géphez" border="false":::

Válassza **a Térkép** lehetőséget a térkép funkció megnyitásához, amely megjeleníti a gépen futó folyamatokat és azok függőségeit. Válassza **a Tulajdonságok** lehetőséget a tulajdonságpanel megnyitásához, ha még nincs megnyitva.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Virtuálisgép-elemzések leképezés a kiválasztott gépre" border="false":::

Bontsa ki a gép folyamatait. Válassza ki az egyik folyamatot a részleteinek megtekintéséhez és a függőségek kiemeléshez.

Válassza ki ismét a gépet, majd válassza az **Események naplózása lehetőséget.** Megjelenik a gép Log Analytics-munkaterületén tárolt táblák listája. Ez a lista eltérő lesz attól függően, hogy Windows vagy Linux rendszerű gépet használ. Válassza ki az **Esemény táblát.** Az **Esemény** tábla a Windows-eseménynapló összes eseményét tartalmazza. Megnyílik a Log Analytics egy egyszerű lekérdezéssel, amely lekéri az összegyűjtött eseménynapló-bejegyzéseket.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Monitor, olvassa el a következő cikket:

> [!div class="nextstepaction"]
> [Azure Monitor – áttekintés](../../../azure-monitor/overview.md)