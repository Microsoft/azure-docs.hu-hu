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
ms.openlocfilehash: ff785bf3ace7c65f83fe8e505f0544edd24776d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836855"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Nem megfelelő Azure Automation állapot-konfigurációs kiszolgálók szervizelése

Ha a kiszolgálók Azure Automation állapot-konfigurációval vannak regisztrálva, a konfigurációs mód a következőre van beállítva:, `ApplyOnly` `ApplyandMonitor` vagy `ApplyAndAutoCorrect` . Ha a mód nem értékre `ApplyAndAutoCorrect` van állítva, akkor a megfelelő állapotú kiszolgálók bármilyen okból nem megfelelők maradnak, amíg azokat manuálisan nem javították.

Az Azure-beli számítási funkció a Run parancs nevű funkciót kínálja, amely lehetővé teszi az ügyfeleknek a parancsfájlok futtatását a virtuális gépeken belül.
Ez a dokumentum példákat tartalmaz a szolgáltatáshoz, amikor manuálisan kijavította a konfiguráció eltolódását.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Windows rendszerű virtuális gépek helyes elsodródása a PowerShell használatával

A Windows rendszerű virtuális gépeket a parancs funkció használatával lehet kijavítani `Run` . Lásd: [PowerShell-parancsfájlok futtatása a Windows rendszerű virtuális gépen a Run paranccsal](/azure/virtual-machines/windows/run-command).

Egy Azure Automation állapot-konfigurációs csomópont kényszerítéséhez a legújabb konfiguráció letöltéséhez és alkalmazásához használja az [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) parancsmagot.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>A Linux rendszerű virtuális gépek helyes elsodródása

Linux rendszerű virtuális gépek esetén nem használhatja az `Run` parancsot. Ezeket a gépeket csak a regisztrációs folyamat megismétlésével lehet kijavítani. 

Az Azure-csomópontok esetében kiválaszthatja a Azure Portal vagy az az Module parancsmagok használatával történő eltolódást. A folyamat részleteiről a [virtuális gép engedélyezése Azure Portal használatával](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal)című cikkben olvashat.

Hibrid csomópontok esetén a Python-szkriptek használatával kiválaszthatja a drift használatát. Lásd: [DSC-műveletek végrehajtása a Linux rendszerű számítógépről](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>További lépések

- A PowerShell-parancsmagok leírása: [az. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse [meg a folyamatos üzembe helyezés a chocolatey](automation-dsc-cd-chocolatey.md)használatával című témakört.