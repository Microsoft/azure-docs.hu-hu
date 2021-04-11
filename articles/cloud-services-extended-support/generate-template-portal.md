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
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077105"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>ARM-sablon előállítása Cloud Serviceshoz (kiterjesztett támogatás) a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan tölthető le az ARM-sablon és a paraméterérték a Cloud Service [Azure Portal](https://portal.azure.com) . Az ARM-sablon és a paraméter-fájl a PowerShell használatával, a Cloud Service létrehozásához vagy frissítéséhez használható.

## <a name="get-arm-template-via-portal"></a>ARM-sablon beszerzése a portálon keresztül

  1. Lépjen a Azure Portalra, és [hozzon létre egy új felhőalapú szolgáltatást](deploy-portal.md). Adja hozzá a Cloud Service-konfigurációt, a csomag-és definíciós fájlokat. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="A rendszerkép az alapok lap feltöltés szakaszát mutatja a létrehozás során.":::
  
  2. Az összes mező befejezése után lépjen a felülvizsgálat és létrehozás lapra a telepítési konfiguráció ellenőrzéséhez, majd kattintson a sablon letöltése elemre a Cloud Service **automatizálásához** (kiterjesztett támogatás).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="A rendszerkép megjeleníti a sablon letöltését a Cloud Service-ben (bővített támogatás) a Azure Portalon.":::
  
  3. Töltse le a sablon és a paraméter fájljait. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="A rendszerkép megjeleníti a sablon letöltését a Azure Portalon.":::
  
  4. Másolja a csomag SAS URI-jét és a konfigurációs SAS URI-t a felülvizsgálat és létrehozás lapról, és adja hozzá őket a fájl parameter.js. Ezek a fájlok mostantól használhatók egy új felhőalapú szolgáltatás létrehozásához a PowerShell használatával.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="A képen látható a csomag SAS URI-ja és a konfiguráció SAS URI-paramétere a Azure Portalon.":::
  
## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- Felhőalapú szolgáltatás (kiterjesztett támogatás) üzembe helyezése a [Azure Portal](deploy-portal.md) használatával
  
