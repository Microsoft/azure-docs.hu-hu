---
title: Azure arc-kompatibilis kiszolgálók migrálása régiók között
description: Megtudhatja, hogyan telepíthet át egy Azure arc-kompatibilis kiszolgálót az egyik régióból a másikba.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650175"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Azure arc-kompatibilis kiszolgálók migrálása régiók között

Vannak olyan helyzetek, amikor át szeretné helyezni a meglévő Azure arc-kompatibilis kiszolgálót az egyik régióból a másikba. Tegyük fel például, hogy a gépet nem a megfelelő régióban regisztrálták, hogy javítsa a kezelhetőséget, vagy az irányítási okok miatt lépjen tovább.

Az Azure arc-kompatibilis kiszolgálók egyik Azure-régióból a másikba való áttelepítéséhez el kell távolítania a virtuálisgép-bővítményeket, törölnie kell az erőforrást az Azure-ban, majd újra létre kell hoznia a másik régióban. A lépések elvégzése előtt ellenőrizze a gépet, hogy a virtuálisgép-bővítmények telepítve vannak-e.

> [!NOTE]
> Amíg a telepített bővítmények továbbra is futnak, és az eljárás befejezése után normál műveletet hajtanak végre, nem fogja tudni kezelni őket. Ha megpróbálja újratelepíteni a bővítményeket a gépen, akkor kiszámíthatatlan működést tapasztalhat.

## <a name="move-machine-to-other-region"></a>Gép áthelyezése másik régióba

> [!NOTE]
> A művelet során a rendszer állásidőt eredményez az áttelepítés során.

1. Távolítsa el a [Azure Portalról](manage-vm-extensions-portal.md#uninstall-extension)telepített virtuálisgép-bővítményeket az [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)használatával vagy a [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)használatával.

2. A **azcmagent** eszköz és a [leválasztási](manage-agent.md#disconnect) paraméter használatával válassza le a gépet az Azure arc-ról, és törölje a gépi erőforrást az Azure-ból. A gép az arc-kompatibilis kiszolgálókról való leválasztása nem távolítja el a csatlakoztatott számítógép-ügynököt, és a folyamat részeként nem kell eltávolítania az ügynököt. Ezt manuálisan is futtathatja, ha interaktív módon jelentkezett be, vagy automatizálja ugyanazt a szolgáltatásnevet, amelyet több ügynök bevezetéséhez vagy egy Microsoft Identity platform [hozzáférési jogkivonatához](../../active-directory/develop/access-tokens.md)használ. Ha nem használ egyszerű szolgáltatásnevet a gép Azure arc-kompatibilis kiszolgálókhoz való regisztrálásához, tekintse meg az alábbi [cikket](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) egy egyszerű szolgáltatásnév létrehozásához.

3. Regisztrálja újra a csatlakoztatott számítógép ügynököt az ív használatára képes kiszolgálókkal a másik régióban. Futtassa az `azcmagent` eszközt a [csatlakozási](manage-agent.md#connect) paraméterrel, és fejezze be ezt a lépést.

4. Telepítse újra azokat a virtuálisgép-bővítményeket, amelyeket eredetileg az arc-kompatibilis kiszolgálókról telepítettek a gépre. Ha az Azure Monitor for VMs (elemzés) ügynököt vagy az Log Analytics ügynököt egy Azure-szabályzattal telepítette, az ügynököket a következő [értékelési ciklust követően újra](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)üzembe helyezi a rendszer.

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületre van-e bejelentve, és lehetővé teszi a figyelést a [Azure monitor a virtuális gépek](../../azure-monitor/vm/vminsights-enable-policy.md) házirendjével, és még sok más