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
ms.openlocfilehash: 9d40bbd7e08d8d3869166827a22f3f08536532bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590704"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>ARM-sablon előállítása Cloud Serviceshoz (kiterjesztett támogatás) a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan tölthető le az ARM-sablon és a paraméterérték a [Azure Portal](https://portal.azure.com) a Cloud Service (bővített támogatás) telepítése után. Az ARM-sablon és a paraméter a Felhőbeli szolgáltatás frissítéséhez vagy frissítéséhez használható a jövőbeli üzembe helyezésekben (kiterjesztett támogatás)

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
  
