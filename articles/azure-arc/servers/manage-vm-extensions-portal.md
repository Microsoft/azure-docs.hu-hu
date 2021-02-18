---
title: VM-bővítmény engedélyezése Azure Portal
description: Ez a cikk bemutatja, hogyan telepíthet virtuálisgép-bővítményeket a Azure Portal hibrid felhőalapú környezetekben futó Azure arc-kompatibilis kiszolgálókra.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: b0e114b314179d42ccd47b7d7bd534d3a824a411
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587658"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure VM-bővítmények engedélyezése a Azure Portal

Ez a cikk bemutatja, hogyan telepítheti és távolíthatja el az Azure arc-kompatibilis kiszolgálók által támogatott Azure-beli virtuálisgép-bővítményeket egy Linux vagy Windows rendszerű hibrid gépen a Azure Portalon keresztül.

> [!NOTE]
> A Key Vault virtuálisgép-bővítmény (előzetes verzió) nem támogatja a Azure Portalból történő telepítést, csak az Azure CLI, a Azure PowerShell vagy egy Azure Resource Manager sablon használatával.

## <a name="enable-extensions-from-the-portal"></a>Bővítmények engedélyezése a portálról

A virtuálisgép-bővítmények alkalmazhatók a kiszolgáló által felügyelt gépekre a Azure Portalon keresztül.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. A portálon keresse meg a **kiszolgálók – Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények**, majd a **Hozzáadás** lehetőséget. Válassza ki a kívánt bővítményt az elérhető bővítmények listájából, és kövesse a varázsló utasításait. Ebben a példában a Log Analytics virtuálisgép-bővítményt fogjuk telepíteni.

    ![Virtuálisgép-bővítmény kiválasztása a kiválasztott géphez](./media/manage-vm-extensions/add-vm-extensions.png)

    Az alábbi példa a Log Analytics virtuálisgép-bővítmény telepítését mutatja be a Azure Portal:

    ![Log Analytics VM-bővítmény telepítése](./media/manage-vm-extensions/mma-extension-config.png)

    A telepítés befejezéséhez meg kell adnia a munkaterület AZONOSÍTÓját és az elsődleges kulcsot. Ha nem ismeri ezt az információt, tekintse meg a [munkaterület-azonosító és-kulcs beszerzése](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)című témakört.

4. A szükséges információk megerősítése után válassza a **Létrehozás** lehetőséget. Megjelenik a központi telepítés összegzése, és megtekintheti a központi telepítés állapotát.

>[!NOTE]
>Habár több bővítmény kötegelt feldolgozását és feldolgozását is elvégezheti, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer a következő bővítmény telepítését kísérli meg.

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az arc-kompatibilis kiszolgálón található virtuálisgép-bővítmények listáját a Azure Portalból szerezheti be. A következő lépések végrehajtásával tekintheti meg őket.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. A portálon keresse meg a **kiszolgálók – Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények** lehetőséget, és a rendszer visszaadja a telepített bővítmények listáját.

    ![A kiválasztott gépre telepített virtuálisgép-bővítmény listázása](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Bővítmény eltávolítása

Egy vagy több bővítményt is eltávolíthat egy arc-kompatibilis kiszolgálóról a Azure Portalból. A bővítmény eltávolításához hajtsa végre az alábbi lépéseket.

1. A böngészőben nyissa meg a [Azure Portal](https://portal.azure.com).

2. A portálon keresse meg a **kiszolgálók – Azure arc** elemet, és válassza ki a hibrid gépet a listából.

3. Válassza a **bővítmények** lehetőséget, majd válasszon ki egy bővítményt a telepített bővítmények listájából.

4. Válassza az **Eltávolítás** lehetőséget, és amikor a rendszer kéri az ellenőrzésre, válassza az **Igen** lehetőséget a folytatáshoz.

## <a name="next-steps"></a>Következő lépések

- Virtuálisgép-bővítmények üzembe helyezése, kezelése és eltávolítása az [Azure CLI](manage-vm-extensions-cli.md), a [PowerShell](manage-vm-extensions-powershell.md)vagy a [Azure Resource Manager sablonok](manage-vm-extensions-template.md)használatával végezhető el.

- A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.