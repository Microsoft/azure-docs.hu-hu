---
title: Azure-beli virtuálisgép-bővítmények és funkciók
description: További információ az Azure VM-bővítményekről
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559715"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure-beli virtuálisgép-bővítmények és funkciók
A bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációt és automatizálást biztosítanak az Azure virtuális gépeken. Az Azure platform számos, a virtuális gépek konfigurálására, figyelésére, biztonságára és segédprogram-alkalmazására kiterjedő bővítményt üzemeltet. A kiadók alkalmazást készítenek, egy bővítménybe csomagolják, és leegyszerűsítik a telepítést. Mindössze annyit kell tennie, hogy megadja a kötelező paramétereket. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Hogyan találhatom meg a rendelkezésre álló bővítményeket?
A rendelkezésre álló bővítményeket egy virtuális gép kiválasztásával, a bal oldali menüben a **bővítmények** kiválasztásával tekintheti meg. A bővítmények teljes listájának lekéréséhez tekintse meg a virtuálisgép- [bővítmények felfedése Linuxra](features-linux.md) és [a Windows rendszerhez](features-windows.md)készült virtuálisgép-bővítmények felfedezése című témakört.

## <a name="how-can-i-install-an-extension"></a>Hogyan telepíthetek bővítményt?
Az Azure virtuálisgép-bővítmények az Azure CLI, a PowerShell, a Resource Manager-sablonok és a Azure Portal használatával kezelhetők. Egy bővítmény kipróbálásához lépjen a Azure Portalra, válassza ki az egyéni parancsfájl-bővítményt, majd adja át a parancsot vagy parancsfájlt a bővítmény futtatásához.

További információ: a [Windows egyéni szkriptek bővítménye](custom-script-windows.md) és a [Linux Custom script bővítmény](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hogyan a bővítmény alkalmazási életciklusának kezelése?
A bővítmény telepítéséhez és törléséhez nem kell közvetlenül csatlakoznia a virtuális géphez. Az Azure-bővítmény életciklusának kezelése a virtuális gépen kívül történik, és integrálva van az Azure platformba.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Bármi mást érdemes meggondolni a bővítményekhez?
A virtuálisgép-bővítmények egyes alkalmazásai rendelkezhetnek saját környezeti előfeltételekkel, például egy végponthoz való hozzáféréssel. Minden bővítmény tartalmaz egy cikket, amely ismerteti az előfeltételeket, beleértve a támogatott operációs rendszereket is.

## <a name="troubleshoot-extensions"></a>Bővítmények hibaelhárítása

Az egyes bővítményekkel kapcsolatos hibaelhárítási információk a bővítmény áttekintésében, a **Hibaelhárítás és támogatás** szakaszban találhatók. Itt látható az elérhető hibaelhárítási információk listája:

| Névtér | Hibaelhárítás |
|-----------|-----------------|
| Microsoft. Azure. monitoring. dependencyagent. dependencyagentlinux | [Linux-függőség Azure Monitor](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. monitoring. dependencyagent. dependencyagentwindows | [Windows-függőség Azure Monitor](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Linux-Azure Disk Encryption](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Windows Azure Disk Encryption](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. számítás. customscriptextension | [Egyéni parancsfájl a Windowshoz](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [A Linux kívánt állapotának konfigurálása](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [A Windows kívánt állapotának konfigurálása](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [NVIDIA GPU illesztőprogram-bővítmény Linux rendszerhez](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [NVIDIA GPU illesztőprogram-bővítmény a Windowshoz](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Antimalware-bővítmény a Windows rendszerhez](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. monitoring. omsagentforlinux | [Linux-Azure Monitor](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. monitoring. microsoftmonitoringagent | [Windows Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. Extension. stackifylinuxagentextension | [Stackify újrarajzolása Linux rendszeren](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. microsoft. ostcextensions | [Linux-jelszó alaphelyzetbe állítása](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices szolgáltatónál. vmsnapshot | [Linux-pillanatkép](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices szolgáltatónál. vmsnapshot | [Windows rendszerhez készült pillanatkép](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Következő lépések
* A Linux-ügynök és-bővítmények működésével kapcsolatos további információkért lásd: Azure virtuálisgép- [bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
* További információ a Windows Guest Agent és a bővítmények működéséről: Azure-beli virtuálisgép- [bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).  
* A Windows Guest Agent telepítéséhez tekintse meg az [Azure Windows rendszerű virtuális gépek ügynökének áttekintése](agent-windows.md)című témakört.  
* A Linux-ügynök telepítéséhez tekintse meg az Azure-beli [linuxos virtuális gépek ügynökének áttekintése](agent-linux.md)című témakört.  

