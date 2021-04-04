---
title: Erőforráskészlet létrehozása az Azure-előfizetések leképezésével
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerteti, hogyan lehet erőforráskészletokat létrehozni saját felhőhöz az Azure-előfizetés leképezésével
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897076"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Erőforráskészlet létrehozása saját felhőhöz az Azure-előfizetés leképezésével
Az Azure-előfizetések leképezése lehetővé teszi erőforráskészlet létrehozását a saját felhőhöz az elérhető vSphere-erőforrás-készletekből. A CloudSimple-portálon megtekintheti és kezelheti a privát Felhőkhöz tartozó Azure-előfizetést.

> [!NOTE]
> Az erőforráskészlet leképezése szintén leképezi az összes alárendelt erőforrás-készletet. A szülő erőforráskészlet nem képezhető le, ha a gyermek-erőforrások készlete már le van képezve.

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).
2. Nyissa meg az **erőforrások** lapot, és válassza az **Azure-előfizetések hozzárendelése** elemet.  
3. Kattintson az **Azure-előfizetések leképezésének szerkesztése** elemre.  
4. Az elérhető erőforráskészlet hozzárendeléséhez jelölje ki őket a bal oldalon, majd kattintson a jobbra mutató nyílra. 
5. A leképezések eltávolításához jelölje ki őket a jobb oldalon, majd kattintson a balra mutató nyílra. 

    ![Azure-előfizetések](media/resources-azure-mapping.png)

6. Kattintson az **OK** gombra.
