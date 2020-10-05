---
title: 'Gyors útmutató: biztonsági javaslatok vizsgálata'
description: Tekintse meg a biztonsági javaslatokat a Defender for IoT Security szolgáltatással.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 859f1c4a1ed1b3d9139307c52f44a14e3089e31f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947158"
---
# <a name="quickstart-investigate-security-recommendations"></a>Gyors útmutató: biztonsági javaslatok vizsgálata


A Defender által a IoT vonatkozó javaslatok időben történő elemzése és enyhítése a legjobb módszer a biztonsági helyzetek javítására és a támadási felület csökkentésére a IoT-megoldáson belül.

Ebben a rövid útmutatóban megvizsgáljuk az egyes IoT biztonsági javaslatokban elérhető információkat, és megtudhatjuk, hogyan részletezheti és használhatja az egyes javaslatok és a kapcsolódó eszközök részleteit a kockázatok csökkentése érdekében.

Tegyük meg az első lépéseket!

## <a name="investigate-new-recommendations"></a>Új javaslatok vizsgálata

A IoT Hub javaslatok listája megjeleníti a IoT Hub összesített biztonsági javaslatait.

1.  A Azure Portalban nyissa meg **IoT Hub**az   új javaslatokkal vizsgálni kívánt IoT hub.

1.  A **Biztonság**   menüben válassza a **javaslatok**elemet. A IoT Hub összes biztonsági javaslata megjelenik, és az **új**   jelzővel kapcsolatos javaslatok az elmúlt 24 órában is megjelölhetik az ajánlásokat. 

    [![Biztonsági javaslatok vizsgálata az ASC-mel a IoT-hez](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Válasszon ki és nyisson meg egy javaslatot a listából, és nyissa meg a javaslat részleteit, és tekintse meg a részletes információkat.

## <a name="security-recommendation-details"></a>Biztonsági javaslat részletei

Nyissa meg az egyes összesített javaslatokat, hogy megjelenjenek a részletes ajánlások leírása, szervizelési lépések, eszköz azonosítója minden olyan eszközhöz, amely egy javaslatot váltott ki. Emellett az ajánlás súlyosságát és a közvetlen vizsgálathoz való hozzáférést is megjeleníti Log Analytics használatával.

1.  Válasszon ki és nyisson meg egy biztonsági javaslatot a **IoT hub**   \>  **biztonsági**   \>  **javaslatok**   listából.

1.  Tekintse át az ajánlás **leírását**, **súlyosságát**, az **eszköz részletes adatait**   az összesítési időszakban. 

1.  Az ajánlási javaslatok áttekintése után a **manuális szervizelési lépés**   utasításait követve javítsa ki az ajánlást okozó problémát. 

    [![Biztonsági javaslatok szervizelése az ASC-mel a IoT-hez](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  Az adott eszközre vonatkozó javaslat részleteinek megismeréséhez válassza ki a kívánt eszközt a részletezés oldalon.

    [Az ![ eszközökre vonatkozó konkrét biztonsági javaslatok kivizsgálása a IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Ha további vizsgálatra van szükség, a hivatkozás használatával **vizsgálja meg log Analytics ajánlását**   . 


## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan hozhat létre egyéni riasztásokat...

> [!div class="nextstepaction"]
> [Egyéni riasztások létrehozása](quickstart-create-custom-alerts.md)
