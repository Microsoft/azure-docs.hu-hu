---
title: Egészségügyi megoldások létrehozása az Azure IoT Central-vel | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egészségügyi megoldásokat az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 2aee5c56b267f9243b69a48dfd0f5f64196c5f23
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021487"
---
# <a name="building-healthcare-solutions-with-azure-iot-central"></a>Egészségügyi megoldások létrehozása az Azure IoT Centrallal 



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

## <a name="next-steps"></a>További lépések

A folyamatos beteg monitorozási megoldás létrehozásának megkezdéséhez:

* [Az alkalmazás sablonjának üzembe helyezése](tutorial-continuous-patient-monitoring.md)
* [Példa architektúrára](concept-continuous-patient-monitoring-architecture.md)