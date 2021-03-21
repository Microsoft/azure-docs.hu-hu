---
title: Azure bizalmas számítástechnika – GYIK
description: Válaszok az Azure bizalmas számítástechnikagal kapcsolatos gyakori kérdésekre.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: a5ecd3827bbdc12b098684f1feda2df652f11940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551912"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Gyakran ismételt kérdések az Azure bizalmas számítástechnikai szolgáltatásáról

Ez a cikk a [bizalmas számítási feladatok Azure-beli virtuális gépeken](overview.md)való futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

Ha az Azure-beli probléma nem szerepel ebben a cikkben, látogasson el az [MSDN webhelyen](https://azure.microsoft.com/support/forums/)található Azure-fórumokra, és stack overflow. Felteheti a problémát ezekben a fórumokon, vagy közzéteheti a [ @AzureSupport Twitteren](https://twitter.com/AzureSupport). Azure-támogatási kérést is küldhet. Ha támogatási kérést szeretne küldeni, az [Azure-támogatás lapon](https://azure.microsoft.com/support/options/)válassza a támogatás lekérése lehetőséget.

## <a name="confidential-computing-virtual-machines"></a>Bizalmas számítástechnika Virtual Machines <a id="vm-faq"></a>

**Hogyan helyezhetek üzembe DCsv2-sorozatú virtuális gépeket az Azure-ban?**

Az alábbi módokon telepítheti a DCsv2 virtuális gépeket:
   - [Azure Resource Manager sablon](../virtual-machines/windows/template-description.md) használata
   - A [Azure Portal](https://portal.azure.com/#create/hub)
   - Az [Azure bizalmas számítástechnika (virtuális gép)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Piactéri megoldás sablonjában. A Piactéri megoldás sablonja segít korlátozni az ügyfeleket a támogatott forgatókönyvekre (régiók, képek, rendelkezésre állás, lemez titkosítás). 

**Az operációsrendszer-lemezképek az Azure bizalmas számítástechnikai szolgáltatásával működnek?**

Nem. A virtuális gépek csak az Ubuntu Server 18,04, az Ubuntu Server 16,04, a Windows Server 2019 Datacenter és a Windows Server 2016 Datacenter rendszerű 2. generációs operációs rendszereken telepíthetők. További információ a 2. generációs virtuális gépekről [Linux](../virtual-machines/generation-2.md) és [Windows](../virtual-machines/generation-2.md) rendszeren

**A DCsv2 virtuális gépek szürkén jelennek meg a portálon, és nem tudok kiválasztani egyet**

A virtuális gép melletti információs buborék alapján különböző műveletek szükségesek:
   -    **UnsupportedGeneration**: módosítsa a virtuális gép rendszerképének generációját "Gen2" értékre.
   -    **NotAvailableForSubscription**: a régió még nem érhető el az előfizetéséhez. Válasszon a rendelkezésre álló régiók közül.
   -    **InsufficientQuota**: [hozzon létre egy támogatási kérést a kvóta növeléséhez](../azure-portal/supportability/per-vm-quota-requests.md). Az ingyenes próbaverziós előfizetések esetében nincs kvóta a bizalmas számítástechnikai virtuális gépekhez. 

**A DCsv2 virtuális gépek nem jelennek meg, amikor megpróbálok megkeresni őket a portál méretének választójában**

Győződjön meg arról, hogy egy [elérhető régiót](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)jelölt ki. Győződjön meg arról is, hogy a méret választóban az "összes szűrő törlése" lehetőséget választotta. 

**Engedélyezhető a gyorsított hálózatkezelés az Azure bizalmas számítástechnikai szolgáltatással?**

 Nem. A gyorsított hálózatkezelés DC-Series vagy DCsv2-Series virtuális gépeken nem támogatott. A gyorsított hálózatkezelés nem engedélyezhető az olyan bizalmas számítástechnikai eszközök üzembe helyezése vagy az Azure Kubernetes Service-fürt üzembe helyezése esetén, amelyek bizalmas számítástechnikai környezetben futnak.

**Használhatom az Azure dedikált gazdagépet ezekkel a gépekkel?**

Igen. Az Azure dedikált gazdagép támogatja a DCsv2-sorozatú virtuális gépeket. Az Azure dedikált gazdagép egyetlen bérlős fizikai kiszolgálót biztosít a virtuális gépek futtatásához. A felhasználók általában az Azure dedikált gazdagép használatával kezelik a megfelelőségi követelményeket a fizikai biztonság, az adatok integritása és a figyelés terén. 

**Hiba történt a Azure Resource Manager sablon üzembe helyezési hibája miatt: "a művelet nem hajtható végre, mert a jóváhagyott szabványos DcsV2-család magok kvótája meghaladja a műveletet"**

[Hozzon létre egy támogatási kérést a kvóta növeléséhez](../azure-portal/supportability/per-vm-quota-requests.md). Az ingyenes próbaverziós előfizetések esetében nincs kvóta a bizalmas számítástechnikai virtuális gépekhez. 

**Mi a különbség a DCsv2-Series és a DC-Series virtuális gépek között?**

DC-Series virtuális gépek régebbi 6 Magos Intel processzorokon futnak az Intel SGX ENKLÁVÉHOZ, és kevesebb teljes memóriával, kevesebb enklávé-gyorsítótárral (EPC) rendelkeznek, és csak két régióban érhetők el (az USA keleti régiójában és Nyugat-Európában Standard_DC2s és Standard_DC4s méretben). A virtuális gépeket nem lehet általánosan elérhetővé tenni, és éles használatra nem ajánlottak. Ezeknek a virtuális gépeknek a üzembe helyezéséhez használja a "  [bizalmas számítási DC-Series VM [előzetes verzió]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace-példányt.

**Globálisan elérhetők a DCsv2 virtuális gépek?**

Nem. Jelenleg ezek a virtuális gépek csak a kiválasztott régiókban érhetők el. A legújabb elérhető régiókat a [termékek régiók szerint lapon](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) találja. 

**A Hyper-Threading szolgáltatás ki van kapcsolva ezeken a gépeken?**

A Hyper-Threading szolgáltatás le van tiltva az összes Azure bizalmas számítástechnikai fürtben.

**Hogyan telepíteni az Open enklávé SDK-t a DCsv2 Virtual Machines szolgáltatásba?**
   
Az OE SDK Azure-beli vagy helyszíni gépen való telepítésével kapcsolatos utasításokért kövesse az [Open ENKLÁVÉ SDK githubon](https://github.com/openenclave/openenclave)megjelenő utasításokat.
     
Az operációs rendszerre vonatkozó telepítési útmutatót az Open enklávé SDK GitHubon is áttekintheti:
   - [Az OE SDK telepítése Windows rendszeren](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Az OE SDK telepítése Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Az OE SDK telepítése Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)