---
title: Közösségi eszközök – klasszikus erőforrások áthelyezése a Azure Resource Managerba
description: Ez a cikk a IaaS-erőforrások klasszikusról a Azure Resource Manager üzemi modellbe való áttelepítését segítő eszközöket katalogizálja a Közösség által biztosított eszközökkel.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f165acb72fdf881a0828c38db577be1f8741384e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674740"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök IaaS-erőforrások áttelepítéséhez klasszikusról Azure Resource Manager

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](classic-vm-deprecation.md#how-does-this-affect-me).

Ez a cikk a IaaS-erőforrások klasszikusról a Azure Resource Manager üzemi modellbe való áttelepítését segítő, a Közösség által biztosított eszközöket katalogizálja.

> [!NOTE]
> Ezeket az eszközöket Microsoft ügyfélszolgálata nem támogatja hivatalosan. Ezért nyílt forráskódú a GitHubon, és örömmel fogadjuk el a hibákat és a további forgatókönyveket. A probléma jelentéséhez használja a GitHub-problémák funkciót.
> 
> Ha ezekkel az eszközökkel végez áttelepítést, a klasszikus virtuális gép leállását okozhatja. Ha a platform támogatott áttelepítését keresi, látogasson el ide: 
> 
>   * [A platform támogatja a IaaS-erőforrások Klasszikusból Azure Resource Manager verembe való áttelepítését](migration-classic-resource-manager-overview.md)
>   * [A platform által támogatott, Klasszikusról Azure Resource Managerre való áttérés technikai részletes bemutatása](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-erőforrások migrálása Klasszikusról Azure Resource Managerra Azure PowerShell használatával](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Ez az Azure Service Management szolgáltatásból a Azure Resource Managerba való nagyvállalati Migrálás részeként létrehozott Segéd-eszközök gyűjteménye. Ez az eszköz lehetővé teszi, hogy az infrastruktúrát egy másik előfizetésbe replikálja, amely az áttelepítés teszteléséhez és az esetleges problémák kiszámításához használatos az éles előfizetése előtt.

[Az eszköz dokumentációjának hivatkozása](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
a migAz egy további lehetőség a klasszikus IaaS erőforrások teljes készletének áttelepítésére Azure Resource Manager IaaS-erőforrásokra. Az áttelepítés ugyanazon az előfizetésen belül vagy a különböző előfizetések és előfizetési típusok (pl.: CSP-előfizetések) között történhet.

[Az eszköz dokumentációjának hivatkozása](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Következő lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)
