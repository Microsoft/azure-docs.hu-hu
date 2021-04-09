---
title: Az elemzési architektúra tárolása
description: Ismerje meg, hogyan hozhat létre egy áruházbeli elemzési alkalmazást a pénztári alkalmazás sablonjának használatával IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 972ab8eaaf7c0b9fe8beb446d74bc8d2e89bbc55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832198"
---
# <a name="in-store-analytics-architecture"></a>Áruházbeli elemzési architektúra


Az áruházbeli elemzési megoldások lehetővé teszik a kiskereskedelmi tárolási környezet különböző feltételeinek figyelését. Ezek a megoldások a IoT Centralon belüli alkalmazások egyikével és az alábbi architektúrával kapcsolatos útmutatásként használhatók.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
- Telemetria küldő és összesített bepillantást IoT Central
- Folyamatos adatexportálás a kívánt Azure-szolgáltatásba a manipulációhoz
- Az adatszerkezet a kívánt formátumban strukturálható, és elküldhető egy tárolási szolgáltatásba
- Az üzleti alkalmazások lekérhetik az adatok lekérdezését, és elemzéseket készíthetnek a kiskereskedelmi működésről
 
Vessünk egy pillantást a kulcsfontosságú összetevőkre, amelyek általában egy részét egy áruházbeli elemzési megoldásban játszanak.

## <a name="condition-monitoring-sensors"></a>Állapot-figyelési érzékelők

Az IoT-megoldások érzékelők készletével kezdődnek a kereskedelmi tárolók környezetében lévő értelmes jelek. A fenti architektúra-diagram bal szélén különböző típusú érzékelők is tükröződnek.

## <a name="gateway-devices"></a>Átjáró-eszközök

Számos IoT érzékelő közvetlenül a felhőbe vagy a közelében található átjáró-eszközre képes nyers jeleket felvenni. Az átjáró-eszköz az adatok összesítését hajtja végre a peremen, mielőtt összefoglaló elemzéseket küld egy IoT Central alkalmazásnak. Az átjáró-eszközök feladata a parancs-és vezérlési műveletek továbbítása is a szenzoros eszközökre, ha vannak ilyenek. 

## <a name="iot-central-application"></a>IoT Central alkalmazás

Az Azure IoT Central-alkalmazás különböző típusú IoT-érzékelőkből származó adatok betöltését, valamint a kiskereskedelmi tároló környezetében található átjáró-eszközöket, valamint az értelmes elemzések készletét hozza létre.

Az Azure IoT Central egy személyre szabott élményt nyújt az áruház kezelője számára, amely lehetővé teszi számukra az infrastruktúra-eszközök távoli figyelését és felügyeletét.

## <a name="data-transform"></a>Adatátalakítás
A megoldáson belüli Azure IoT Central alkalmazás úgy konfigurálható, hogy nyers vagy összesített elemzéseket exportáljon az Azure-beli (szolgáltatásként nyújtott platform-) szolgáltatásokra, amelyek adatkezelést végezhetnek, és az üzleti alkalmazásba való kihelyezésük előtt gazdagítják ezeket a bepillantást. 

## <a name="business-application"></a>Üzleti alkalmazás
A IoT-ben a kiskereskedelmi környezetben üzembe helyezett különböző típusú üzleti alkalmazások is használhatók. A kiskereskedelmi áruház kezelője vagy a személyzet tagjai ezeket az alkalmazásokat felhasználhatják az üzleti eredmények megjelenítéséhez és a valós idejű, értelmes műveletek elvégzéséhez. Ha szeretné megtudni, hogyan hozhat létre valós idejű Power BI irányítópultot a kereskedelmi csapatának, kövesse az [oktatóanyagot](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg az [áruházbeli elemzési pénztár](https://aka.ms/checkouttemplate) és [az áruházbeli elemzési feltételek figyelése](https://aka.ms/conditiontemplate) alkalmazás-sablonokkal. 
* Tekintse meg a teljes [körű oktatóanyagot](https://aka.ms/storeanalytics-tutorial) , amely bemutatja, hogyan hozhat létre megoldást a In-Store Analytics alkalmazás-sablonok egyikével.
