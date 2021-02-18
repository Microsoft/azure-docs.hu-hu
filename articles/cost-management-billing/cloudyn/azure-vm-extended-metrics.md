---
title: Kiterjesztett metrikák hozzáadása Azure-beli virtuális gépekhez
description: Ez a cikk segítséget nyújt a kiterjesztett diagnosztikai metrikák Azure-beli virtuális gépekhez történő engedélyezésében és konfigurálásában.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595319"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Kiterjesztett metrikák hozzáadása Azure-beli virtuális gépekhez

A Cloudyn az Azure-beli virtuális gépekről származó Azure-metrikaadatokat használ az erőforrások részletes adatainak megjelenítéséhez. A Cloudyn ezeket a metrikaadatokat, más néven teljesítményszámlálókat használja a jelentések létrehozásához. A Cloudyn azonban nem gyűjt össze automatikusan minden Azure-metrikaadatot a vendég virtuális gépekről. Ehhez engedélyeznie kell a metrikák gyűjtését. Ez a cikk segítséget nyújt a további diagnosztikai metrikák Azure-beli virtuális gépekhez történő engedélyezésében és konfigurálásában.

A metrikák gyűjtésének engedélyezése után a következőkre van lehetősége:

- Megtudhatja, hogy a virtuális gépei mikor érik el a memóriára, a lemezre és a processzorra vonatkozó korlátot.
- Megismerheti a használati trendeket és anomáliákat.
- Szabályozhatja a költségeit a használathoz igazított méretezéssel.
- Költséghatékony méretezés-optimalizálási javaslatokat kaphat a Cloudyntől.

Például előfordulhat, hogy szeretné monitorozni az Azure-beli virtuális gépek processzor- és memóriahasználatát (%). Az Azure-beli virtuális gép metrikái megfelelnek a _Százalékos processzorhasználat_ és a _\Memória\% Előjegyzett memória kihasználtsága_ értékeknek.

> [!NOTE]
> A kiterjesztett metrikaadatok gyűjtése csak az Azure-beli vendégszintű monitorozással támogatott. A Cloudyn nem kompatibilis a [Log Analytics-ügynökkel](../../azure-monitor/agents/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Annak meghatározása, hogy engedélyezve vannak-e a kiterjesztett metrikák

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A **Virtuális gépek** területen válasszon ki egy virtuális gépet, majd a **Monitorozás** területen válassza a **Metrikák** lehetőséget. Megjelenik az elérhető metrikák listája.
3. Jelöljön ki néhány metrikát. Egy diagramon megjelennek a hozzájuk tartozó adatok.  
    ![Példametrika – Gazdagép százalékos processzorhasználata](./media/azure-vm-extended-metrics/metric01.png)

Az előző példában a gazdagépek számára korlátozott számú szabványos metrika érhető el, de a memória metrikái nem. A memória metrikái a kiterjesztett metrikák részét képezik. Ebben az esetben a kiterjesztett metrikák nincsenek engedélyezve a virtuális géphez. A kiterjesztett metrikák engedélyezéséhez további lépéseket kell végrehajtani. Az alábbi információk végigvezetik ezek engedélyezésének folyamatán.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Kiterjesztett metrikák engedélyezése az Azure Portalon

A standard metrikák a gazdagépek metrikái. A _Százalékos processzorhasználat_ metrika egy példa erre. A vendég virtuális gépekhez is tartoznak alapvető metrikák, és ezeket is kiterjesztett metrikáknak nevezik. A kiterjesztett metrikák közé tartozik a _\Memória\% Előjegyzett memória kihasználtsága_ és a _\Memória\Elérhető bájtok_.

A kiterjesztett metrikák egyszerűen engedélyezhetők. Mindegyik virtuális géphez engedélyezze a vendégszintű monitorozást. A vendégszintű monitorozás engedélyezésekor a rendszer az Azure Diagnostics-ügynököt is telepíti a virtuális gépre. Alapértelmezés szerint a rendszer kiterjesztett metrikák egy alapszintű készletét is hozzáadja. A következő folyamat azonos a klasszikus és általános, illetve a Windows és Linux rendszerű virtuális gépek esetében.

Ne feledje, hogy a Windows- és a Linux-beli vendégszintű monitorozáshoz is tárfiók szükséges. Ha nem választ ki meglévő tárfiókot a vendégszintű monitorozás engedélyezésekor, a rendszer létrehoz egyet.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Vendégszintű monitorozás engedélyezése meglévő virtuális gépeken

1. A **Virtuális gépek** területen tekintse meg a virtuális gépek listáját, majd válasszon ki egy virtuális gépet.
2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
3. A Diagnosztikai beállítások oldalon kattintson a **Vendégszintű monitorozás engedélyezése** lehetőségre.  
    ![Vendégszintű monitorozás engedélyezése az Áttekintés lapon](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Néhány perc elteltével a rendszer telepíti az Azure Diagnostics-ügynököt a virtuális gépre. A rendszer hozzáadja metrikák egy alapszintű készletét is. Frissítse az oldalt. A hozzáadott teljesítményszámlálók az Áttekintés lapon jelennek meg.
5. A Monitorozás területen kattintson a **Metrikák** elemre.
6. A **Metrika névtere** területen található metrikadiagramon válassza a **Vendég (klasszikus)** lehetőséget.
7. A Metrikalistában megtekintheti a vendég virtuális gép összes elérhető teljesítményszámlálóját.  
    ![a példaként szolgáló kiterjesztett metrikák listája](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Vendégszintű monitorozás engedélyezése új virtuális gépeken

Új virtuális gépek létrehozásakor a Kezelés lapon állítsa **Be** értékre a **Vendég operációs rendszer diagnosztikáját**.

![a vendég operációs rendszer diagnosztikájának bekapcsolása](./media/azure-vm-extended-metrics/new-enable-diag.png)

További információ az Azure-beli virtuális gépek kiterjesztett metrikáinak engedélyezéséről a következő részekben található: [Az Azure Linux-ügynök megismerése és használata](../../virtual-machines/extensions/agent-linux.md) és [Azure-beli virtuálisgép-ügynök áttekintése](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>A Resource Manager hitelesítő adatai

A kiterjesztett metrikák engedélyezése után győződjön meg arról, hogy a Cloudyn hozzáférhet a [Resource Manager hitelesítő adataihoz](./activate-subs-accounts.md). Ahhoz, hogy a Cloudyn összegyűjthesse és megjeleníthesse a virtuális gépek teljesítményadatait, szükség van a hitelesítő adatokra. Ezek a költségoptimalizálási javaslatok létrehozásához is szükségesek. A Cloudynnek egy példány legalább három napnyi teljesítményadataira van szüksége ahhoz, hogy megállapíthassa, a példány esetében javasolt-e a méret csökkentése.

## <a name="enable-vm-metrics-with-a-script"></a>Virtuálisgép-metrikák engedélyezése szkripttel

A virtuálisgép-metrikák PowerShell-szkriptekkel is engedélyezhetők. Ha sok virtuális gépen szeretné engedélyezni a metrikákat, egy szkripttel automatizálhatja a folyamatot. Példaszkriptek a GitHubon, az [Azure – Diagnosztika engedélyezése](https://github.com/Cloudyn/azure-enable-diagnostics) témakörben találhatók.

## <a name="view-azure-performance-metrics"></a>Az Azure-teljesítménymetrikák megtekintése

Az Azure-példányok teljesítménymetrikáinak a Cloudyn portálján való megtekintéséhez lépjen az **Assets** > **Compute** > **Instance Explorer** (Adategységek > Számítás > Példánykezelő) területre. A virtuálisgép-példányok listájában bontson ki egy példányt, majd bontson ki egy erőforrást a részletek megtekintéséhez.

![mintaadatok az Instance Explorerben](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Következő lépések

- Ha még nem engedélyezte az Azure Resource Manager API-hozzáférést a fiókokhoz, lépjen tovább az [Azure-előfizetések és -fiókok aktiválása](./activate-subs-accounts.md) részre.