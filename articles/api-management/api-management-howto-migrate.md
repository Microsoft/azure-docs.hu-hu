---
title: Azure-API Management migrálása régiók között
description: Megtudhatja, hogyan telepíthet át egy API Management példányt egyik régióból a másikba.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86250226"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Azure-API Management migrálása régiók között
API Management példányok az egyik Azure-régióból a másikba való áttelepíthetők a [biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md) funkció segítségével. A forrás-és a célcsoportokban ugyanazt a API Management árképzési szintet kell választania. 

> [!NOTE]
> A biztonsági mentés és a visszaállítás nem fog működni a különböző felhőalapú típusok közötti áttelepítés során. Ehhez [sablonként](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)kell exportálnia az erőforrást. Ezután igazítsa az exportált sablont a cél Azure-régióhoz, és hozza létre újra az erőforrást. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>1. lehetőség: eltérő API Management-példány nevének használata

1. A cél régióban hozzon létre egy új API Management példányt ugyanazzal az árképzési csomaggal, mint a forrás API Management példánya. Az új példánynak más névvel kell rendelkeznie. 
1. Meglévő API Management példány biztonsági mentése egy Storage-fiókba.
1. Állítsa vissza a 2. lépésben létrehozott biztonsági mentést az 1. lépésben az új régióban létrehozott új API Management példányra.
1. Ha van egy egyéni tartománya, amely a forrás régió API Management példányára mutat, frissítse az egyéni tartomány CNAME értékét úgy, hogy az új API Management példányra mutasson. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>2. lehetőség: használja ugyanazt a API Management-példány nevét

> [!NOTE]
> Ez a beállítás az áttelepítés során leállást eredményez.

1. A forrás régió API Management példányának biztonsági mentése egy Storage-fiókba.
1. Törölje a API Management a forrás régióban. 
1. Hozzon létre egy új API Management példányt a céltartományban a forrás régiójában megegyező névvel.
1. Állítsa vissza az 1. lépésben létrehozott biztonsági mentést a cél régióban lévő új API Management példányra.  


## <a name="next-steps"></a><a name="next-steps"> </a>Következő lépések
* A biztonsági mentési és visszaállítási szolgáltatással kapcsolatos további információkért lásd: a vész- [helyreállítás megvalósítása](api-management-howto-disaster-recovery-backup-restore.md).
* Az Azure-erőforrások áttelepítésével kapcsolatos információkért lásd: az [Azure régiók közötti áttelepítési útmutatója](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimalizálja és mentse a Felhőbeli kiadásait](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
