---
title: Azure-beli virtuális gépek (klasszikus) kivonása a 2023. március 1-jén
description: Ez a cikk magas szintű áttekintést nyújt a klasszikus üzemi modellel létrehozott virtuális gépek kivonásáról.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668204"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>A IaaS-erőforrások migrálása a Azure Resource Managerra 2023. március 1-től 

A 2014-es verzióban az infrastruktúra szolgáltatásként (IaaS) indult [Azure Resource Manageron](https://azure.microsoft.com/features/resource-manager/). Azóta is fejlesztjük a képességeket. Mivel Azure Resource Manager már teljes körű IaaS képességekkel és egyéb fejlesztésekkel rendelkezik, a IaaS virtuális gépek (VM-EK) felügyeletét az [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) segítségével, 2020. február 28-án érvénytelenítettük. Ez a funkció 2023 március 1-től teljesen megszűnik. 

Napjainkban a IaaS virtuális gépek körülbelül 90%-a használja a Azure Resource Manager. Ha a IaaS-erőforrásokat az ASM-en keresztül használja, kezdje el megtervezni az áttelepítés megtervezését. A [Azure Resource Manager](../azure-resource-manager/management/index.yml)kihasználása érdekében a 2023. március 1-től elvégezhető.

A klasszikus üzemi modellel létrehozott virtuális gépek a [modern életciklus-szabályzatot](https://support.microsoft.com/help/30881/modern-lifecycle-policy) fogják követni a nyugdíjazáshoz.

## <a name="how-does-this-affect-me"></a>Hogyan érint ez engem? 

- 2020. február 28-án a IaaS virtuális gépeket nem használó ügyfelek a 2020 februárjában már nem hozhatnak létre virtuális gépeket (klasszikus). 
- 2023. március 1-jén az ügyfelek többé nem tudják elindítani a IaaS virtuális gépeket az ASM használatával. A rendszer leállítja és lefoglalja a még futó vagy lefoglalt összes szolgáltatást. 
- 2023. március 1-jén a nem átAzure Resource Manager telepített előfizetéseket a rendszer a fennmaradó virtuális gépek (klasszikus) törlésére vonatkozó ütemtervekkel kapcsolatban tájékoztatja.  

Ez a nyugdíjazás *nem* érinti a következő Azure-szolgáltatásokat és-funkciókat: 
- [Azure Cloud Services (klasszikus)](../cloud-services/cloud-services-choose-me.md)
- Virtuális gépek által *nem* használt Storage-fiókok (klasszikus) 
- Virtuális gépek által *nem* használt virtuális hálózatok (klasszikus) 
- Egyéb klasszikus erőforrások

## <a name="what-resources-are-available-for-this-migration"></a>Milyen erőforrások érhetők el ehhez az áttelepítéshez?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): Microsoft és közösségi támogatás az áttelepítéshez.

- [Azure-áttelepítési támogatás](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): az áttelepítés során technikai segítséget nyújtó dedikált támogatási csoport. A technikai támogatás nélküli ügyfelek igénybe vehetik az [ingyenes támogatási képességet](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) , amely kifejezetten ehhez az áttelepítéshez biztosított. 

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): a Fast Track segítséget nyújthat a jogosult ügyfeleknek az áttelepítés megtervezéséhez & végrehajtásához. [Jelölje](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) ki a DC áttelepítési programját.  

- Ha a vállalata vagy szervezete a Microsofttal vagy a Microsoft képviselőivel (például a Cloud Solution Architects (CSAs) vagy a Technical Account Managers (TAMs) szolgáltatással együttműködve dolgozik, akkor további forrásokat is megtalálhat a Migrálás érdekében.

## <a name="what-actions-should-i-take"></a>Milyen műveleteket kell elvégeznie? 

Kezdje el megtervezni az áttelepítés megtervezését Azure Resource Managerre, ma. 

1. Készítse el az összes érintett virtuális gép listáját: 

   - A **Virtual Machines (klasszikus) típusú virtuális gépek a Azure Portal virtuálisgép-** [paneljén](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) az előfizetésen belüli összes érintett virtuális gépre vonatkoznak. 
   - Az Azure Resource Graph-t a [portál](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) vagy a [PowerShell](../governance/resource-graph/concepts/work-with-data.md) használatával is lekérdezheti az összes megjelölt virtuális gép (klasszikus) és a kapcsolódó információk listájának megtekintéséhez a kiválasztott előfizetések esetében. 
   - 2020. február 8-án és szeptember 2-án a "IaaS VM-Azure Resource Manager Migrálás megtervezése az előfizetések tulajdonosai számára" tárgyú e-maileket küldtünk. Az e-mail tartalmazza az összes előfizetést és virtuális gép (klasszikus) virtuális gépet tartalmazó listát. A lista létrehozásához használja őket. 

1. [További](./migration-classic-resource-manager-overview.md) információ a [Linux](./migration-classic-resource-manager-plan.md) és a [Windows rendszerű](./migration-classic-resource-manager-plan.md) virtuális gépek (klasszikus) Azure Resource Manager való áttelepítéséről. További információ: a [klasszikusról Azure Resource Manager áttelepítésre vonatkozó gyakori kérdések](./migration-classic-resource-manager-faq.md).

1. Javasoljuk, hogy indítsa el a tervezést a [platform-támogatás áttelepítési eszköz](./migration-classic-resource-manager-overview.md) használatával a meglévő virtuális gépek három egyszerű lépéssel történő áttelepítéséhez: érvényesítés, előkészítés és végrehajtás. Az eszköz úgy lett kialakítva, hogy a virtuális gépeket minimálisan, állásidő nélkül telepítse át. 

   - Az első lépés, a validate nem befolyásolja a meglévő telepítést, és felsorolja az áttelepítéshez nem támogatott forgatókönyveket. 
   - A központi telepítés kijavításához és az áttelepítéshez való felkészüléshez végezze el a [megkerülő megoldások listáját](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) . 
   - Ideális esetben az összes érvényesítési hiba kijavítása után az előkészítés és a végrehajtás lépései során nem merülhet fel probléma. A végrehajtás sikeres végrehajtása után a rendszer áttelepíti az üzemelő példányt Azure Resource Manager, és a Azure Resource Manager által elérhetővé tett új API-kkal felügyelhető. 

   Ha az áttelepítési eszköz nem alkalmas az áttelepítésre, az áttelepítéshez [más számítási ajánlatokat](/azure/architecture/guide/technology-choices/compute-decision-tree) is megvizsgálhat. Mivel számos Azure-beli számítási ajánlat létezik, és ezek különböznek egymástól, nem biztosítható a platform által támogatott áttelepítési útvonal.  

1. A technikai kérdésekkel, problémákkal és az előfizetések engedélyezési listához való hozzáadásával kapcsolatos segítségért [forduljon az ügyfélszolgálathoz](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. A lehető leghamarabb fejezze be az áttelepítést az üzleti hatás elkerülése és a Azure Resource Manager jobb teljesítményének, biztonságának és új funkcióinak kihasználásához. 
