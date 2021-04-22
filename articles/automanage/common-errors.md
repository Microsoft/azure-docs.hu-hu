---
title: A gyakori Azure Automanage hibák elhárítása
description: Gyakori automatikus alkalmazásbeállítási hibák és elhárításuk
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862974"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Az automatikus alkalmazásbeállítás gyakori hibáinak elhárítása
Előfordulhat, hogy az automatikus automatizálás nem fog tudni gépet a szolgáltatásba bevetni. Ez a dokumentum ismerteti, hogyan háríthatja el az üzembe helyezési hibákat, ismertet néhány gyakori okot az üzembe helyezés meghiúsulása mellett, valamint ismerteti a lehetséges következő hibaelhárítási lépéseket.

## <a name="troubleshooting-deployment-failures"></a>Üzembehelyezéssel kapcsolatos hibák elhárítása
Ha a gép automatikus automatizálva lesz, létrejön egy Azure Resource Manager üzembe helyezése. További információért tekintse meg az üzembe helyezéssel kapcsolatos további részleteket arról, hogy miért nem sikerült. A hiba részleteinek úszó paneljére mutató hivatkozások az alábbi képen láthatóak.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Automatikusmanage hiba részleteinek úszó panelje.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Ellenőrizze a meghibásodott virtuális gépet tartalmazó erőforráscsoport üzemelő példányát
A hiba úszó panelje tartalmazni fog egy hivatkozást az erőforráscsoportban található üzemelő példányokkal, amely a sikertelen bevezetést tartalmazó gépet tartalmazza. Az úszó panel tartalmazni fog egy előtagnevet is, amely alapján szűrheti az üzemelő példányokat. Az üzembe helyezési hivatkozásra kattintva az Üzemelő példányok panelre ugorhat, ahol szűrheti az üzemelő példányokat, és láthatja a gép automatikus központi telepítéseit. Ha több régióban helyez üzembe, győződjön meg arról, hogy a megfelelő régióban lévő üzemelő példányra kattint.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Ellenőrizze a sikertelen virtuális gépet tartalmazó előfizetés üzemelő példányát
Ha nem lát hibákat az erőforráscsoport üzembe helyezésében, akkor a következő lépés az előfizetésben található üzemelő példányok, amelyekben a sikertelen bevezetést tartalmazó virtuális gép található. Kattintson az **Előfizetés üzemelő példányai** hivatkozásra a hiba úszó panelén, és szűrje az üzemelő példányokat az **Automanage-DefaultResourceGroup szűrővel.** Az üzemelő példányok szűréséhez használja az erőforráscsoport nevét a hiba panelen. Az üzembe helyezés neve régiónévvel lesz utótaggal ékelve. Ha több régióban helyez üzembe, győződjön meg arról, hogy a megfelelő régióban lévő üzemelő példányra kattint.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics-munkaterülethez kapcsolt előfizetés üzemelő példányának ellenőrzése
Ha nem lát sikertelen üzembe helyezéseket a meghibásodott virtuális gépet tartalmazó erőforráscsoportban vagy előfizetésben, és ha a meghibásodott virtuális gép egy másik előfizetésben lévő Log Analytics-munkaterülethez csatlakozik, lépjen a Log Analytics-munkaterülethez csatolt előfizetéshez, és ellenőrizze a sikertelen üzembe helyezéseket.

## <a name="common-deployment-errors"></a>Gyakori telepítési hibák

Hiba |  Kockázatcsökkentés
:-----|:-------------|
A fiók nem megfelelő engedélyekkel való automatikus automatizálva – hiba | Ez a hiba akkor fordulhat elő, ha a közelmúltban új, automatikus adatokat tartalmazó fiókot tartalmazó előfizetést áthelyezett egy új bérlőbe. A hiba elhárításának lépései itt [találhatók:](./repair-automanage-account.md).
A munkaterület-régió nem egyezik a régióleképezési követelményekkel | Az automatikus automatizáció nem tudta beléptetni a gépet, mert az a Log Analytics-munkaterület, amelyhez a gép jelenleg kapcsolódik, nincs leképezve egy támogatott Automation-régióra. Győződjön meg arról, hogy a meglévő Log Analytics-munkaterület és Automation-fiók egy támogatott [régióleképezésben található.](../automation/how-to/region-mappings.md)
"Hozzáférés megtagadva a "Felügyelt alkalmazás által létrehozott rendszer megtagadási hozzárendelése" nevű megtagadási hozzárendelés miatt" | A [rendszer egy denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) hozzárendelést hozott létre az erőforráson, ami megakadályozta az automatikus elérést az erőforráshoz való hozzáférésben. Előfordulhat, hogy ezt a denyAssignment hozzárendelést egy [terv vagy](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) egy felügyelt [alkalmazás hozta létre.](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview)
"Operációs rendszer adatai: Name='(null)', ver='(null)', agent status='Not Ready'." | Győződjön meg arról, [](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)hogy a támogatott ügynök minimális verzióját futtatja, az ügynök fut ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) és [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), és hogy az ügynök naprakész ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) és [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Nem sikerült meghatározni a virtuális gép operációs rendszerének nevét: , ver. Ellenőrizze, hogy a virtuálisgép-ügynök fut-e, és hogy az aktuális állapot Kész." | Győződjön meg arról, [](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)hogy a támogatott ügynök minimális verzióját futtatja, az ügynök fut ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) és [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), és hogy az ügynök naprakész ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) és [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"A virtuális gép hibát jelentett az "IaaSAntimalware" bővítmény feldolgozásakor" | Győződjön meg arról, hogy nincs másik kártevőirtó/víruskereső ajánlat telepítve a virtuális gépen. Ha ez nem sikerül, forduljon az ügyfélszolgálathoz.
ASC-munkaterület: Az automatikus automatizálás jelenleg nem támogatja a Log Analytics szolgáltatást a _helyen._ | Ellenőrizze, hogy a virtuális gép támogatott régióban [található-e.](./automanage-virtual-machines.md#supported-regions)
A sablon üzembe helyezése szabályzatsértés miatt meghiúsult. További információért tekintse meg a részleteket. | Van egy szabályzat, amely megakadályozza, hogy az automatikus alkalmazás-automatizálja a virtuális gép beállítását. Ellenőrizze az előfizetésre vagy a virtuális gépet tartalmazó erőforráscsoportra alkalmazott szabályzatokat, amelyek az automatikus automatizálni kívánt virtuális gépet tartalmazzák.
"A hozzárendelés sikertelen volt; nem áll rendelkezésre további információ" | Nyisson egy esetet a Microsoft Azure támogatásával.

## <a name="next-steps"></a>Következő lépések

* [További információ a Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus Azure Portal](quick-create-virtual-machines-portal.md)