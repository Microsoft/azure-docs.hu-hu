---
title: ARM-sablon előállítása Cloud Serviceshoz (kiterjesztett támogatás) a Azure Portal használatával
description: ARM-sablon és-paraméterérték előállítása és letöltése a Cloud Serviceshoz (kiterjesztett támogatás) a Azure Portal használatával
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 215abb1ce8d65b5ecdd25aeb78c17c70e801a9d2
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555397"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>ARM-sablon előállítása Cloud Serviceshoz (kiterjesztett támogatás) a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan kérhető le az ARM-sablon és a paraméter a [Azure Portal](https://portal.azure.com) a Cloud Service (bővített támogatás) telepítése után. Az ARM-sablon és a paraméter a Felhőbeli szolgáltatás frissítéséhez vagy frissítéséhez használható a jövőbeli üzembe helyezésekben (kiterjesztett támogatás)

## <a name="get-arm-template-via-portal"></a>ARM-sablon beszerzése a portálon keresztül

  1. Nyissa meg az erőforráscsoportot, és válassza a központi telepítések lehetőséget.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="A rendszerkép a Azure Portal található erőforráscsoport alatti központi telepítések kiválasztását mutatja.":::
  
  2. Válassza ki a Cloud Service (bővített támogatás) elemet, majd kattintson a sablon elemre.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="A rendszerkép megjeleníti a sablon kiválasztását a Cloud Service (bővített támogatás) területen a Azure Portalon.":::
  
  3. Töltse le a sablon és a paraméter fájljait. Ezek a PowerShell használatával a későbbi üzembe helyezésekhez használhatók.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="A rendszerkép megjeleníti a sablon letöltését a Azure Portalon.":::
  
## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- Felhőalapú szolgáltatás (kiterjesztett támogatás) üzembe helyezése a [Azure Portal](deploy-portal.md) használatával
  
