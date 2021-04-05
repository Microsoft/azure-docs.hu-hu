---
title: A Cloud Services (kiterjesztett támogatás) figyelésének engedélyezése a Azure Portal használatával
description: Cloud Services (kiterjesztett támogatás) példányok figyelésének engedélyezése a Azure Portal használatával
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744598"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Cloud Services figyelésének engedélyezése (kiterjesztett támogatás) a Azure Portal használatával

Ez a cikk bemutatja, hogyan engedélyezheti a riasztásokat a meglévő felhőalapú szolgáltatásokra (kiterjesztett támogatás) való üzembe helyezéshez. 

## <a name="add-monitoring-rules"></a>Figyelési szabályok hozzáadása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Válassza ki a felhőalapú szolgáltatás (bővített támogatás) központi telepítését, amely számára engedélyezni kívánja a riasztásokat. 
3. Válassza a **riasztások** panelt. 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="A rendszerkép a Azure Portal riasztások lapját jeleníti meg.":::

4. Válassza az **új riasztás** ikont.
     :::image type="content" source="media/enable-alerts-2.png" alt-text="A képen az új riasztás hozzáadása lehetőség látható.":::

5. Adja meg a kívánt feltételeket és a szükséges műveleteket a nyomon követett mérőszámok alapján. A szabályokat külön mérőszámok vagy a tevékenység naplója alapján határozhatja meg. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="A rendszerkép megjeleníti a riasztási feltételek hozzáadásának helyét.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="A képen látható a jel logikájának konfigurálása.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="A rendszerkép a műveleti csoport logikájának konfigurálását mutatja be.":::

6. Ha befejezte a riasztások beállítását, mentse a módosításokat, és a beállított mérőszámok alapján megtekintheti a **riasztások** panelt az idő múlásával.

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).