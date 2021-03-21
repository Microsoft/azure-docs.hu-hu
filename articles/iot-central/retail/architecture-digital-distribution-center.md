---
title: Architektúra IoT Central Digital Distribution Center | Microsoft Docs
description: A IoT Centralhez készült Digital Distribution Center alkalmazás-sablon architektúrája
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.openlocfilehash: f5f1a9a51c4aa68c11f1843424186a727be94621
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99834306"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>A IoT Central Digital Distribution Center alkalmazás-sablon architektúrája



A partnerek és az ügyfelek az alkalmazás sablonját & alábbi útmutatást követve teljes körű **digitális terjesztési központú** megoldásokat fejleszthet.

> [!div class="mx-imgBorder"]
> ![digitális terjesztési központ](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
2. Telemetria küldő és összesített bepillantást IoT Central
3. Az adattovábbítás a kívánt Azure-szolgáltatásba történik a manipulációhoz
4. Az olyan Azure-szolgáltatások, mint az ASA vagy a Azure Functions használhatók az adatfolyamok újraformázására és a kívánt Storage-fiókokba való küldésre
5. A feldolgozott adatok tárolása a közel valós idejű műveletekhez vagy a hideg tároláshoz szükséges, és az olyan további elemzéseket is tartalmaz, amelyek az ML-vagy batch-elemzésen alapulnak. 
6. A Logic Apps a végfelhasználói üzleti alkalmazások különböző üzleti munkafolyamatainak kikapcsolására is használható

## <a name="details"></a>Részletek
A következő szakasz a fogalmi architektúra egyes részeit ismerteti

## <a name="video-cameras"></a>Videokamerák 
A videokamerák a digitális kapcsolatban álló, nagyvállalati szintű ökoszisztémában található elsődleges érzékelők. A gépi tanulás és a mesterséges intelligencia fejlődése, amely lehetővé teszi, hogy a videó strukturált adatokba kerüljön, és a felhőbe való küldés előtt feldolgozza azt a peremen. Az IP-kamerák használatával képeket rögzíthet, tömörítheti a kamerát, majd elküldheti a tömörített adatok használatát a video Analytics-folyamathoz, vagy használhatja a GigE látási kamerákat a lemezképek az érzékelőn való rögzítéséhez, majd ezeket a képeket közvetlenül a Azure IoT Edgehoz is elküldheti, amely a video Analytics-folyamatokban végzett feldolgozás előtt tömöríti. 

## <a name="azure-iot-edge-gateway"></a>Átjáró Azure IoT Edge
A "kamerák – érzékelők" és a peremhálózati munkaterhelések kezelése a Azure IoT Edge helyileg történik, és a kamera streamjét az elemzési folyamat dolgozza fel. A video Analytics feldolgozási folyamata Azure IoT Edge számos előnnyel jár, többek között a csökkent válaszidő, az alacsony sávszélességű felhasználás, ami alacsony késést eredményez a gyors adatfeldolgozáshoz. A felhőbe csak a legfontosabb metaadatokat, elemzéseket vagy műveleteket kell elküldeni, ha további műveletre vagy vizsgálatra van szükség. 

## <a name="device-management-with-iot-central"></a>Eszközkezelés IoT Central 
Az Azure IoT Central egy megoldás-fejlesztői platform, amely leegyszerűsíti a IoT-eszköz & Azure IoT Edge az átjárók kapcsolódását, konfigurálását és felügyeletét. A platform jelentősen csökkenti az IoT, a műveletek és a kapcsolódó fejlesztések terheit és költségeit. Az ügyfelek & partnereink teljes körű vállalati megoldásokat hozhatnak létre a terjesztési központokban lévő digitális visszajelzési hurok eléréséhez.

## <a name="business-insights-and-actions-using-data-egress"></a>Üzleti elemzések és műveletek a kimenő adatforgalom használatával 
A IoT Central platform a folyamatos adatexportálás (CDE) és az API-k révén gazdag bővíthetőségi lehetőségeket biztosít. A telemetria adatfeldolgozási vagy nyers telemetria alapuló üzleti elemzéseket általában egy előnyben részesített üzletági alkalmazásba exportáljuk. A webhook, a Service Bus, az Event hub vagy a blob Storage szolgáltatással a gépi tanulási modellek létrehozásához, betanításához és további gazdagítása céljából is elérhetővé válik.

## <a name="next-steps"></a>Következő lépések
* Útmutató a [digitális terjesztési központ sablonjának](./tutorial-iot-central-digital-distribution-center.md) üzembe helyezéséhez
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central.md)
