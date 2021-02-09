---
title: Azure IoT Central Micro-teljesítési központ | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy Micro-teljesítési központ alkalmazást a Micro-beteljesülő központ alkalmazás-sablonnal IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 23e1880a3abac6ebda71eaa36276d2bf5815048e
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833779"
---
# <a name="micro-fulfillment-center-architecture"></a>Micro-teljesítési központ architektúrája

A Micro-bevezetési központ megoldásai lehetővé teszik a teljes mértékben automatizált megfelelési központ minden aspektusának digitális összekapcsolását, figyelését és felügyeletét a költségek csökkentése érdekében az állásidő kiiktatásával, a biztonság és az általános hatékonyság növelése mellett. Ezek a megoldások a IoT Centralon belüli alkalmazások egyikével és az alábbi architektúrával kapcsolatos útmutatásként használhatók.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

1. IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
2. Telemetria küldő és összesített bepillantást IoT Central
3. Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipulációhoz
4. Az adatszerkezet a kívánt formátumban strukturálható, és elküldhető egy tárolási szolgáltatásba
5. Az üzleti alkalmazások lekérhetik az adatok lekérdezését, és elemzéseket készíthetnek a kiskereskedelmi működésről
 
Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét a Micro-teljesítési központ megoldásában játszanak.

## <a name="robotic-carriers"></a>Robotkaros szolgáltatók

A mikro-teljesítési központ megoldása valószínűleg nagy mennyiségű robot-szolgáltatót generál, amelyek különböző típusú telemetria-jeleket hoznak létre. Ezeket a jeleket egy átjáró-eszköz is betöltheti, összesítve, majd az architektúra diagram bal oldalán látható IoT Central küldi el.  

## <a name="condition-monitoring-sensors"></a>Állapot-figyelési érzékelők

Az IoT-megoldások érzékelők készletével kezdődnek az értelmes jelek a teljesítési központban. A fenti architektúra-diagram bal szélén különböző típusú érzékelők is megjelennek.

## <a name="gateway-devices"></a>Átjáró-eszközök

Számos IoT érzékelő közvetlenül a felhőbe vagy a közelében található átjáró-eszközre képes nyers jeleket felvenni. Az átjáró-eszköz az adatok összesítését hajtja végre a peremen, mielőtt összefoglaló elemzéseket küld egy IoT Central alkalmazásnak. Az átjáró-eszközök feladata a parancs-és vezérlési műveletek továbbítása is a szenzoros eszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central alkalmazás különböző típusú IoT-érzékelőkből, robotokból, valamint a teljesítési központ környezetében található átjáró eszközökből származó adatokból áll, és értelmes elemzéseket hoz létre.

Az Azure IoT Central egy személyre szabott élményt nyújt az áruház kezelője számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és felügyeletét.

## <a name="data-transform"></a>Adatátalakítás
A megoldáson belüli Azure IoT Central alkalmazás úgy konfigurálható, hogy nyers vagy összesített elemzéseket exportáljon az Azure Pásti (szolgáltatásként nyújtott platform) szolgáltatásaiba, amelyek adatkezelést végezhetnek, és az üzleti alkalmazásba való kihelyezésük előtt gazdagítják ezeket a bepillantást. 

## <a name="business-application"></a>Üzleti alkalmazás
A IoT-ben a kiskereskedelmi környezetben üzembe helyezett különböző típusú üzleti alkalmazások is használhatók. A teljesítési központ kezelője vagy alkalmazottai ezeket az alkalmazásokat felhasználhatják az üzleti eredmények megjelenítéséhez, és valós időben is elvégezhetik az értelmes műveleteket. Ha szeretné megtudni, hogyan hozhat létre valós idejű Power BI irányítópultot a kereskedelmi csapatának, kövesse az [oktatóanyagot](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg a [Micro-beteljesülő központ](https://aka.ms/checkouttemplate) alkalmazás sablonnal. 
* Tekintse meg az [oktatóanyagot](https://aka.ms/mfc-tutorial) , amely végigvezeti a megoldásnak a Micro-teljesítési központ alkalmazás sablonnal történő létrehozásán.
