---
title: Azure Confidential Computing – gyakori kérdések
description: Válaszok a bizalmas Azure-számítástechnikával kapcsolatos gyakori kérdésekre.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 05c98102109d1925eb78d4558faf62b366801e77
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538980"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Gyakori kérdések az Azure Confidential Computingról

Ez a cikk a bizalmas számítási feladatok Azure-beli virtuális gépeken való futtatásával kapcsolatos leggyakoribb [kérdésekre ad választ.](overview.md)

Ha a cikk nem foglalkozik az Azure-ral kapcsolatos probléma megoldásával, keresse fel az Azure fórumait az [MSDN-ről,](https://azure.microsoft.com/support/forums/)és Stack Overflow. A problémát közzétenheti ezeken a fórumokon, vagy közzétenheti a [ @AzureSupport twitteren.](https://twitter.com/AzureSupport) Elküldhet egy Azure-támogatás is. Ha támogatási kérést küld, a Azure-támogatás [válassza](https://azure.microsoft.com/support/options/)a Támogatás kérése lehetőséget.

## <a name="confidential-computing-virtual-machines"></a>Bizalmas számítástechnika Virtual Machines <a id="vm-faq"></a>

**Hogyan helyezhetek üzembe DCsv2 sorozatú virtuális gépeket az Azure-ban?**

Íme néhány módszer a DCsv2 virtuális gépek üzembe helyezésére:
   - Sablon [Azure Resource Manager használata](../virtual-machines/windows/template-description.md)
   - A [Azure Portal](https://portal.azure.com/#create/hub)
   - Az [Azure Confidential Computing (Virtual Machine) Piactér](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) megoldássablonban. A Marketplace-megoldássablon segít az ügyfeleket a támogatott forgatókönyvek (régiók, rendszerképek, rendelkezésre állás, lemeztitkosítás) korlátozásában. 

**Minden operációsrendszer-lemezkép működni fog az Azure Confidential Computinggal?**

Nem. A virtuális gépek csak Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter és Windows Server 2016 Datacenter rendszert üzemeltető 2. generációs gépeken helyezhetők üzembe. További információ a 2. generációs virtuális gépekről [Linux és](../virtual-machines/generation-2.md) [Windows rendszeren](../virtual-machines/generation-2.md)

**A DCsv2 virtuális gépek szürkén jelennek meg a portálon, és nem tudok kiválasztani egyet**

A virtuális gép információs buborékja alapján különböző műveleteket kell végre vennie:
   -    **Nem támogatottgeneration:** Módosítsa a virtuálisgép-rendszerkép generációját a "Gen2" generációra.
   -    **NotAvailableForSubscription:** A régió még nem érhető el az előfizetéséhez. Válasszon a rendelkezésre álló régiók közül.
   -    **InsufficientQuota:** [Hozzon létre egy támogatási kérést a kvóta növeléséhez.](../azure-portal/supportability/per-vm-quota-requests.md) Az ingyenes próbaverziós előfizetések nem tartalmaznak kvótát a bizalmas számítási virtuális gépekhez. 

**A DCsv2 virtuális gépek nem viselkednek, amikor megpróbálom keresni őket a portál méretválasztóján**

Győződjön meg arról, hogy egy elérhető [régiót választott.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Ügyeljen arra is, hogy a méretválasztóban az "Összes szűrő törlése" lehetőséget válassza. 

**Engedélyezem a gyorsított hálózatépítést az Azure Confidential Computing használatával?**

 Nem. A gyorsított hálózat a virtuális gépeken DC-Series és DCsv2-Series nem támogatott. A gyorsított hálózat nem engedélyezhető bizalmas számításokat futtató virtuális gépek üzembe helyezéséhez vagy Azure Kubernetes Service bizalmas számításon futó fürtök üzembe helyezéséhez.

**Használhatok ilyen Azure Dedicated Host gépekkel?**

Igen. Azure Dedicated Host DCsv2-sorozatú virtuális gépek támogatása. Azure Dedicated Host egybérlős fizikai kiszolgálót biztosít a virtuális gépek futtatásához. A felhasználók általában Azure Dedicated Host a fizikai biztonsággal, az adatok integritásával és a monitorozásával kapcsolatos megfelelőségi követelmények teljesítésére. 

**A következő Azure Resource Manager hiba jelenik meg: "A műveletet nem sikerült befejezni, mert túllépi a jóváhagyott szabványos DcsV2 család magkvótáját"**

[Hozzon létre egy támogatási kérést a kvóta növeléséhez.](../azure-portal/supportability/per-vm-quota-requests.md) Az ingyenes próbaverziós előfizetések nem tartalmaznak kvótát a bizalmas számítási virtuális gépekhez. 

**Mi a különbség a virtuális gépek DCsv2-Series és DC-Series között?**

DC-Series virtuális gépek régebbi, 6 magos Intel SGX processzorokkal futnak, és kevesebb memóriával és kevesebb Enklávé Lapgyorsítótár (EPC) memóriával rendelkezik, és csak két régióban érhetők el (az USA keleti régiójában és Nyugat-Európában az Standard_DC2s-ben és Standard_DC4s-méretekben). Ezeket a virtuális gépeket nem tervezik általánosan elérhetővé tenni, és éles környezetben való használatra nem ajánlottak. A virtuális gépek üzembe helyezéséhez használja a Confidential Compute virtuális  [DC-Series [előzetes verzió]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace-példányt.

**Globálisan elérhetők a DCsv2 virtuális gépek?**

Nem. Ezek a virtuális gépek jelenleg csak bizonyos régiókban érhetők el. A legújabb [elérhető régiókért tekintse meg a](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) termékek régiók szerint oldalát. 

**Ki van kapcsolva a hyper-threading ezeken a gépeken?**

A hyper-threading le van tiltva az összes bizalmas Azure-számítástechnikai fürtön.

**Hogyan az Open Enclave SDK-t a DCsv2 virtuális gépekre?**
   
Ha útmutatásra van szükség az OE SDK Azure-beli vagy helyi gépen való telepítéséhez, kövesse az [Open Enclave SDK GitHubon található utasításokat.](https://github.com/openenclave/openenclave)
     
Az Open Enclave SDK GitHubon az operációs rendszerre vonatkozó telepítési utasításokat is itt találja:
   - [Az OE SDK telepítése Windows rendszeren](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Az OE SDK telepítése Ubuntu 18.04-re](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)