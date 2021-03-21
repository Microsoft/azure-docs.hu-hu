---
title: Mik az Azure IoT Central Healthcare-megoldások | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: e8a72195f0fcacce2c994e8770157b05b65d70ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833150"
---
# <a name="what-are-the-iot-central-healthcare-solutions"></a>Mik a IoT Central egészségügyi megoldások?

Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.

## <a name="what-is-continuous-patient-monitoring-template"></a>Mi az a folyamatos beteg-figyelési sablon?

Az egészségügyi IoT terén a folyamatos beteg monitorozás az egyik legfontosabb segítő, amely csökkenti a visszafogadások kockázatát, hatékonyabban kezeli a krónikus betegségeket, és javítja a betegek eredményeit. A folyamatos beteg monitorozás két fő kategóriába osztható:

1. **Fekvőbeteg-figyelés**: az orvosi wearables és más kórházi eszközök használata esetén a Care Teams a beteg létfontosságú jeleinek és orvosi feltételeinek elküldését anélkül, hogy egy ápolót naponta többször is fel kellene vennie. Az ápolási csapatok tudomásul veszik, hogy a pácienseknek kritikus figyelmet kell fordítaniuk az értesítéseken keresztül, és hatékonyan rangsorolják az idejüket.
1. **Távoli beteg figyelése**: a kórházon kívüli betegek megfigyelésére szolgáló orvosi wearables és betegeknek szóló jelentés (profik) használatával csökkenthető az Újrafogadás kockázata. A krónikus betegeknek és a rehabilitációs betegeknek származó adatokat össze lehet gyűjteni annak biztosítására, hogy a betegek betartsák a gondozási terveket, és hogy a páciensek károsodásával kapcsolatos riasztások a kritikus fontosságúak legyenek.

Ez az alkalmazás-sablon a folyamatos beteg-figyelés mindkét kategóriájának megoldására használható. Ez a következő előnyöket nyújtja:

* Zökkenőmentesen csatlakoztathatók különböző típusú orvosi hordható IoT Central-példányokhoz.
* Az eszközök monitorozása és kezelése, hogy azok egészségesek maradjanak.
* Az eszközökre vonatkozó egyéni szabályok létrehozása a megfelelő riasztások elindításához.
* Exportálja a beteg Health-adatait a FHIR készült Azure API-ra, amely egy megfelelő adattár.
* Exportálja az összesített betekintést meglévő vagy új üzleti alkalmazásokba.

>[!div class="mx-imgBorder"] 
>![CPM – irányítópult](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Következő lépések

A folyamatos beteg monitorozási megoldás létrehozásának megkezdéséhez:

* [Az alkalmazás sablonjának üzembe helyezése](tutorial-continuous-patient-monitoring.md)
* [Példa architektúrára](concept-continuous-patient-monitoring-architecture.md)