---
title: Az Azure IoT Edge bemutatása | Microsoft Docs
description: Az Azure IoT Edge szolgáltatás áttekintése
author: kgremban
manager: philmea
ms.reviewer: chipalost
ms.service: iot-edge
services: iot-edge
ms.topic: overview
ms.date: 10/28/2019
ms.author: kgremban
ms.custom: mvc
ms.openlocfilehash: e6c8e83838240b989c8c782840d8c771a6f6b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487802"
---
# <a name="what-is-azure-iot-edge"></a>Az Azure IoT Edge bemutatása

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure IoT Edge szolgáltatás a felhőbeli elemzéseket és egyéni üzleti logikákat eszközökre helyezi át, hogy a szervezet az üzleti elemzésekre tudjon koncentrálni az adatkezelés helyett. Az üzleti logikát szabványos tárolóba csomagolva kibővítheti IoT-megoldását, majd bármely eszközön üzembe helyezheti ezeket a tárolókat, és figyelheti az összes felhőből.

Az elemzések jelentős üzleti értékeket teremthetnek az IoT-megoldásokban, de nem szükséges minden elemzést a felhőben végezni. Ha a lehető leggyorsabban szeretne reagálni a vészhelyzetekre, a biztonsági rendellenesség észlelése számítási feladatokat a peremhálózat szélén is futtathatja. Ha csökkenteni kívánja a sávszélességi költségeket, és nem kívánja átvinni az terabájtos nyers adatokat, akkor az adatokat helyileg törölheti és összesítheti, majd csak elemzés céljából elküldheti a felhőbe a bepillantást.

Az Azure IoT Edge három összetevőből áll:

* **IoT Edge modulok** olyan tárolók, amelyek Azure-szolgáltatásokat, harmadik féltől származó szolgáltatásokat vagy saját kódot futtatnak. A modulokat IoT Edge-eszközökön lehet üzembe helyezni, és a végrehajtásuk is helyileg, az eszközökön zajlik.
* A **IoT Edge futtatókörnyezet** minden IoT Edge eszközön fut, és az egyes eszközökön üzembe helyezett modulokat kezeli.
* A **felhőalapú felület** lehetővé teszi IoT Edge-eszközök távoli figyelését és kezelését.

>[!NOTE]
>Az Azure IoT Edge az IoT Hub ingyenes és standard szintű csomagjaiban érhető el. Az ingyenes szintű csomag csak tesztelési és értékelési célokra használható. Az alapszintű és standard csomagokról további információt [a megfelelő IoT Hub-csomag kiválasztását](../iot-hub/iot-hub-scaling.md) ismertető cikkben talál.

## <a name="iot-edge-modules"></a>IoT Edge-modulok

IoT Edge modulok olyan végrehajtási egységek, amelyek Docker-kompatibilis tárolóként lettek megvalósítva, amely az üzleti logikát az Edge-ben futtatja. Több modul is konfigurálható az egymás közötti kommunikációra, így a modulok egy adatfeldolgozó folyamatot alkothatnak. Kifejleszthet egyedi modulokat, vagy egy modulba csomagolhat bizonyos Azure-szolgáltatásokat is, amelyek elemzéseket nyújtanak offline és a peremhálózaton is.

### <a name="artificial-intelligence-at-the-edge"></a>Mesterséges intelligencia a peremen

A Azure IoT Edge lehetővé teszi az összetett események feldolgozását, a gépi tanulást, a képfelismerést és az egyéb nagy értékű mesterséges intelligencia üzembe helyezését a házon belüli írás nélkül. Az Azure-szolgáltatások, például a Azure Functions, a Azure Stream Analytics és a Azure Machine Learning egyaránt futtathatók a helyszínen Azure IoT Edge keresztül. Azonban nem korlátozódik az Azure-szolgáltatásokra. Bárki létrehozhat AI-modulokat, és a Közösség számára elérhetővé teheti őket az Azure piactéren való használatra.

### <a name="bring-your-own-code"></a>Saját kód használata

Ha a saját kódját szeretné üzembe helyezni az eszközein, az Azure IoT Edge ezt is támogatja. Az IoT Edge ugyanazt a programozási modellt használja, mint a többi Azure IoT-szolgáltatás. Ugyanazt a kódot futtathatja egy eszközön vagy a felhőben is. Az Azure IoT Edge a Linux és Windows rendszereket is támogatja, így szabadon kiválasztható a kódolás platformja. Támogatja a Java, a .NET Core 2,0, a Node.js, a C és a Python használatát, így a fejlesztők a már ismert nyelven tudják majd használni a meglévő üzleti logikát.

## <a name="iot-edge-runtime"></a>IoT Edge-futtatókörnyezet

Az Azure IoT Edge-futtatókörnyezet egyéni és felhőbeli logikák is használatát is lehetővé teszi az IoT Edge-eszközökön. A futtatókörnyezet a IoT Edge eszközön helyezkedik el, és felügyeleti és kommunikációs műveleteket hajt végre. A futtatókörnyezet számos funkciót lát el:

* A számítási feladatok telepítése és frissítése az eszközön.
* Fenntartja Azure IoT Edge biztonsági szabványait az eszközön.
* Biztosítja az IoT Edge-modulok folyamatos futását.
* Jelenti a modulok állapotát a felhőnek a távoli monitorozáshoz.
* Felügyeli az alárendelt levelek eszközei és egy IoT Edge eszköz közötti kommunikációt egy IoT Edge eszközön lévő modulok, valamint egy IoT Edge eszköz és a felhő között.

![Az IoT Edge-futtatókörnyezet elemzéseket és jelentéseket küld az IoT Hub felé](./media/about-iot-edge/runtime.png)

Azure IoT Edge-eszköz használata az Ön számára. A futtatókörnyezetet gyakran használják a mesterséges intelligenciák üzembe helyezésére olyan eszközökön, amelyek más helyszíni eszközökről összesítik és dolgozzák fel az adatokat, de ez az üzembe helyezési modell csupán egyetlen lehetőség.

Az Azure IoT Edge futtatókörnyezet számos IoT-eszközön fut, amely lehetővé teszi, hogy sokféle módon használja azt. Támogatja a Linux és a Windows operációs rendszereket, valamint az absztrakt hardver részleteit. Ha nem dolgoz fel nagy mennyiségű adat feldolgozását, vagy egy ipari kiszolgáló használatával használ erőforrás-igényes számítási feladatokat, használjon a málna PI 3-nál kisebb méretű eszközöket.

## <a name="iot-edge-cloud-interface"></a>Az IoT Edge felhőalapú interfésze

Nehezen kezelhető a szoftver életciklusa több millió IoT-eszközön, amelyek gyakran eltérő módon, illetve modellekben vagy földrajzilag elszórtan jelennek meg. A számítási feladatokat az adott eszköztípus sajátosságai szerint hozhatja létre és konfigurálhatja, az összes eszközön üzembe helyezheti, és megfigyelés útján minden helytelenül működő eszközt észlelhet. Ezeket a tevékenységeket nem lehet eszközönként végrehajtani, ezért nagy léptékben kell elvégezni őket.

Az Azure IoT Edge zökkenőmentesen integrálható az Azure IoT-megoldásgyorsítókkal, hogy a megoldás igényeit egyetlen felületről lehessen kezelni. A Cloud Services a következőket teszi lehetővé:

* Számítási feladatok létrehozása és konfigurálása egy adott típusú eszközön való futtatáshoz.
* Számítási feladatok küldése több eszközre.
* A használatban lévő eszközökön futó számítási feladatok monitorozása.

![Az eszközök telemetria és műveletei a felhővel vannak összehangolva](./media/about-iot-edge/cloud-interface.png)

## <a name="next-steps"></a>Következő lépések

A fogalmak gyakorlati alkalmazását is kipróbálhatja, ha [üzembe helyezi az IoT Edge szolgáltatást egy szimulált eszközön](quickstart.md).
