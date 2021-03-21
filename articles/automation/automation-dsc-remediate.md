---
title: Nem megfelelő Azure Automation állapot-konfigurációs kiszolgálók szervizelése
description: Ez a cikk azt ismerteti, hogyan kell újraalkalmazni az igény szerinti konfigurációkat a kiszolgálókon, ahol a konfigurációs állapot sodródik.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 98164c20af2a5d6ae7ccac43331a2876c23ccfb7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500172"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Nem megfelelő Azure Automation állapot-konfigurációs kiszolgálók szervizelése

Ha a kiszolgálók Azure Automation állapot-konfigurációval vannak regisztrálva, a konfigurációs mód a következőre van beállítva:, `ApplyOnly` `ApplyandMonitor` vagy `ApplyAndAutoCorrect` . Ha a mód nem értékre `ApplyAndAutoCorrect` van állítva, akkor a megfelelő állapotú kiszolgálók bármilyen okból nem megfelelők maradnak, amíg azokat manuálisan nem javították.

Az Azure-beli számítási funkció a Run parancs nevű funkciót kínálja, amely lehetővé teszi az ügyfeleknek a parancsfájlok futtatását a virtuális gépeken belül.
Ez a dokumentum példákat tartalmaz a szolgáltatáshoz, amikor manuálisan kijavította a konfiguráció eltolódását.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Windows rendszerű virtuális gépek helyes elsodródása a PowerShell használatával

A Windows rendszerű virtuális gépeket a parancs funkció használatával lehet kijavítani `Run` . Lásd: [PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal](../virtual-machines/windows/run-command.md).

Egy Azure Automation állapot-konfigurációs csomópont kényszerítéséhez a legújabb konfiguráció letöltéséhez és alkalmazásához használja az [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) parancsmagot.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek helyes elsodródása

Linux rendszerű virtuális gépek esetén nem használhatja az `Run` parancsot. Ezeket a gépeket csak a regisztrációs folyamat megismétlésével lehet kijavítani. 

Az Azure-csomópontok esetében kiválaszthatja a Azure Portal vagy az az Module parancsmagok használatával történő eltolódást. A folyamat részleteiről a [virtuális gép engedélyezése Azure Portal használatával](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal)című cikkben olvashat.

Hibrid csomópontok esetén a Python-szkriptek használatával kiválaszthatja a drift használatát. Lásd: [DSC-műveletek végrehajtása a Linux rendszerű számítógépről](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Következő lépések

- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation/#automation).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.
