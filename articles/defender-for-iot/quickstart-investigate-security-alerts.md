---
title: 'Gyors útmutató: biztonsági riasztások vizsgálata'
description: Ismerje meg, részletezje és vizsgálja meg a Defender IoT biztonsági riasztásokat a IoT-eszközökön.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90947073"
---
# <a name="quickstart-investigate-security-alerts"></a>Gyors útmutató: biztonsági riasztások vizsgálata

A Defender által a IoT-hez kiadott riasztások ütemezett vizsgálata és szervizelése a legjobb módszer a IoT-megoldás megfelelőségének és védelmének biztosítására.

Ebben a rövid útmutatóban megvizsgáljuk az egyes IoT biztonsági riasztásokban elérhető információkat, és megtudhatjuk, hogyan részletezheti és kezelheti az egyes riasztások és a kapcsolódó eszközök részleteit a válaszadáshoz és a szervizeléshez. 

Lássunk neki! 


## <a name="investigate-new-security-alerts"></a>Új biztonsági riasztások vizsgálata

A IoT Hub biztonsági riasztások listája a IoT Hub összesített biztonsági riasztásait jeleníti meg. 

1. A Azure Portalban nyissa meg az új riasztások kivizsgálásához használni kívánt **IoT hub** .
1. A **Biztonság** menüben válassza a **riasztások** lehetőséget. A IoT Hub összes biztonsági riasztása megjelenik, és a riasztások **új** jelzővel, az elmúlt 24 órában megjelölhetik a riasztásokat.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Új IoT biztonsági riasztások vizsgálata az új riasztási jelző használatával":::
1. Válassza ki és nyissa meg a listából a riasztások részleteit, és tekintse meg a riasztásra vonatkozó adatokat. 

## <a name="security-alert-details"></a>Biztonsági figyelmeztetés részletei

Az egyes összesített riasztások megnyitásakor megjelenik a riasztás részletes leírása, a szervizelés lépései, a riasztást kiváltó eszközök AZONOSÍTÓi, valamint a riasztás súlyossága és a közvetlen nyomozási hozzáférés a Log Analytics használatával. 

1. Válassza ki és nyissa meg a biztonsági riasztásokat a **IoT hub**  >  **biztonsági**  >  **riasztások** listából. 
1. Tekintse át a riasztás **leírását**, **súlyosságát**, **az észlelés forrását, a** riasztást kiállító összes eszköz **adatait** az összesítési időszakban.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Részletezés és az egyes eszközök részleteinek áttekintése összesített riasztásban "::: 
1. A riasztási sajátosságok áttekintése után használja a **manuális szervizelési lépés** utasításait a riasztást okozó probléma megoldásához és/vagy megoldásához. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Kövesse a manuális szervizelési lépéseket az eszköz biztonsági értesítéseinek feloldásához vagy szervizeléséhez":::

1. Ha további vizsgálatra van szükség, **vizsgálja meg a log Analytics riasztásait** a hivatkozás használatával. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="A riasztások további vizsgálatához használja a következő képernyőn elérhető log Analytics-hivatkozás használatával":::

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a Defender-riasztási típusok és a lehetséges testreszabások megismeréséhez...

> [!div class="nextstepaction"]
> [A IoT biztonsági riasztások ismertetése](concept-security-alerts.md)
