---
title: Virtuálisgép-bővítmény engedélyezése Azure Portal
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-bővítményeket Azure Arc hibrid felhőkörnyezetekben futó, engedélyezett kiszolgálókon a Azure Portal.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388278"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure-beli virtuálisgép-bővítmények engedélyezése a Azure Portal

Ez a cikk bemutatja, hogyan helyezhet üzembe és távolíthat el az Azure Arc-kompatibilis kiszolgálók által támogatott Azure-beli virtuálisgép-bővítményeket Linux vagy Windows rendszerű hibrid gépekre a Azure Portal.

> [!NOTE]
> A Key Vault virtuálisgép-bővítmény (előzetes verzió) nem támogatja az Azure Portal-ból való üzembe helyezést, csak az Azure CLI, a Azure PowerShell vagy egy Azure Resource Manager használata.

> [!NOTE]
> Azure Arc kompatibilis kiszolgálók nem támogatják a virtuálisgép-bővítmények Azure-beli virtuális gépeken való üzembe helyezését és felügyeletét. Azure-beli virtuális gépekhez tekintse meg a virtuálisgép-bővítmények [áttekintését ismertető cikket.](../../virtual-machines/extensions/overview.md)

## <a name="enable-extensions-from-the-portal"></a>Bővítmények engedélyezése a portálról

A virtuálisgép-bővítmények a kiszolgáló által felügyelt gép arc-bővítményei a Azure Portal.

1. A böngészőből a következőt kell [Azure Portal.](https://portal.azure.com)

2. A portálon lépjen a **Kiszolgálók** – Azure Arc, és válassza ki a hibrid gépet a listából.

3. Válassza **a Bővítmények,** majd a **Hozzáadás lehetőséget.** Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait. Ebben a példában a Log Analytics virtuálisgép-bővítményt fogjuk üzembe helyezni.

    ![Virtuálisgép-bővítmény kiválasztása a kiválasztott géphez](./media/manage-vm-extensions/add-vm-extensions.png)

    Az alábbi példa a Log Analytics virtuálisgép-bővítmény telepítését mutatja be a Azure Portal:

    ![Log Analytics virtuálisgép-bővítmény telepítése](./media/manage-vm-extensions/mma-extension-config.png)

    A telepítés befejezéséhez meg kell adnia a munkaterület azonosítóját és az elsődleges kulcsot. Ha nem tudja, hogyan találhatja meg ezeket az információkat, tekintse meg a munkaterület azonosítójának és [kulcsának beszerzését.](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

4. A szükséges információk megerősítését követően válassza a **Létrehozás lehetőséget.** Megjelenik a telepítés összegzése, és áttekintheti a telepítés állapotát.

>[!NOTE]
>Bár több bővítmény kötege és feldolgozása is lehet, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer megkísérli a következő bővítmény telepítését.

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az Arc-kompatibilis kiszolgálón található virtuálisgép-bővítmények listáját a következő Azure Portal. A következő lépések elvégzésével láthatja őket.

1. A böngészőben a következőre [kell Azure Portal.](https://portal.azure.com)

2. A portálon lépjen a **Kiszolgálók** – Azure Arc, és válassza ki a hibrid gépet a listából.

3. Válassza **a Bővítmények** lehetőséget, és a rendszer visszaadja a telepített bővítmények listáját.

    ![A kiválasztott gépen üzembe helyezett virtuálisgép-bővítmény felsorolása](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Eltávolítási bővítmény

Egy vagy több bővítményt eltávolíthat egy Arc-kompatibilis kiszolgálóról a Azure Portal. A bővítmények eltávolításához hajtsa végre az alábbi lépéseket.

1. A böngészőben a következőre [kell Azure Portal.](https://portal.azure.com)

2. A portálon lépjen a **Kiszolgálók** – Azure Arc, és válassza ki a hibrid gépet a listából.

3. Válassza **a Bővítmények** lehetőséget, majd válasszon ki egy bővítményt a telepített bővítmények listájából.

4. Válassza **az Eltávolítás lehetőséget,** és amikor a rendszer megerősítést kér, válassza az **Igen lehetőséget** a folytatáshoz.

## <a name="next-steps"></a>Következő lépések

- Virtuálisgép-bővítményeket telepíthet, kezelhet és távolíthat el az [Azure CLI,](manage-vm-extensions-cli.md) [a PowerShell](manage-vm-extensions-powershell.md)vagy a [Azure Resource Manager használatával.](manage-vm-extensions-template.md)

- A hibaelhárítási információkat a Virtuálisgép-bővítmények [hibaelhárítása útmutatóban talál.](troubleshoot-vm-extensions.md)