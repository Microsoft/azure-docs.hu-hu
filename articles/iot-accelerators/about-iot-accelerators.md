---
title: Bevezetés a IoT megoldás-gyorsítók használatába – Azure | Microsoft Docs
description: Megismerheti az Azure IoT-megoldásgyorsítókat. Az IoT-megoldásgyorsítók teljes körű, átfogó, üzembe helyezésre kész IoT-megoldások.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 5012383e64a85ee025273f5339b828f5338e1d4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629068"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Mik az Azure IoT-megoldásgyorsítók?

A felhőalapú IoT-megoldás általában egyéni kódot és felhőalapú szolgáltatásokat használ az eszközök kapcsolatának kezeléséhez, az adatfeldolgozáshoz és az elemzéshez, valamint a megjelenítéshez.

Az IoT-megoldásgyorsítók teljes körű, üzembe helyezésre kész IoT-megoldások, amelyek gyakori IoT-forgatókönyveket implementálnak. A forgatókönyvek közé tartoznak a csatlakoztatott gyárak és az eszközszimuláció. A megoldásgyorsítók üzembe helyezésekor az üzemelő példány magában foglalja az összes szükséges felhőalapú szolgáltatást és minden szükséges alkalmazáskódot.

A megoldásgyorsítók az Ön saját IoT-megoldásainak kiindulópontjaiként szolgálnak. Az összes megoldásgyorsító forráskódja nyílt, és elérhető a GitHubban. A megoldásgyorsítókat letöltheti és saját igényei szerint testre szabhatja.

A megoldásgyorsítókat tanulási eszközként is használhatja, mielőtt létrehozná saját egyéni IoT-megoldását. A megoldásgyorsítók bevált gyakorlatokat alkalmaznak a felhőalapú IoT-megoldásokhoz, amelyeket követhet.

Az összes megoldásgyorsító alkalmazáskódja tartalmaz egy olyan webalkalmazást, amely lehetővé teszi az alkalmazásgyorsító kezelését.

> [!NOTE]
> A távoli figyelési és prediktív karbantartási megoldások el lettek távolítva az [Azure IoT megoldás-gyorsító](https://www.azureiotsolutions.com/Accelerators) webhelyről. További információ: [Mi az Azure IoT megoldás-gyorsító? (előző verzió)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Támogatott IoT-forgatókönyvek

Jelenleg két megoldási gyorssegéd áll rendelkezésre az üzembe helyezéséhez:

### <a name="connected-factory"></a>Csatlakoztatott gyár

A [Connected Factory megoldás-gyorsító](iot-accelerators-connected-factory-features.md) segítségével telemetria gyűjthet az olyan ipari eszközökről, amelyek egy [OPC egységes architektúra](https://opcfoundation.org/about/opc-technologies/opc-ua/) -felülettel rendelkeznek, és szabályozzák azokat. Az ipari objektumok lehetnek például a gyártósoron található összeszerelő- és tesztelőállomások.

A csatlakoztatott gyár irányítópultjának használatával a következő ipari eszközöket monitorozhatja és kezelheti:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="A csatlakoztatott gyári megoldás irányítópultját bemutató képernyőkép." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Eszközszimuláció

Az [eszköz-szimulációs megoldás gyorsítása](iot-accelerators-device-simulation-overview.md) használatával reális telemetria létrehozó szimulált eszközöket futtathat. Ez a megoldásgyorsító más megoldásgyorsítók viselkedésének vagy saját IoT-megoldásainak tesztelésére is használható.

Az eszközszimulációs webalkalmazás a következő szimulációk konfigurálására és futtatására használható:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Képernyőkép, amely az eszköz-szimulációs megoldás irányítópultját mutatja." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

## <a name="design-principles"></a>Tervezési alapelvek

Minden megoldásgyorsító ugyanazon tervezési elvek és célok figyelembevételével készült. Tervezésük során fontos volt:

* A **méretezhetőség**, így több millió csatlakoztatott eszközt csatlakoztathat és felügyelhet.
* A **bővíthetőség**, hogy saját elvárásainak megfelelően testre szabhassa megoldásgyorsítóit.
* Az **érthetőség**, hogy könnyedén megérthesse a működésüket és a megvalósításukat.
* A **modularitás**, hogy lecserélhessen egyes alkalmazásokat.
* A **biztonság**, amely az Azure biztonsági eszközeit a beépített csatlakozási és eszközbiztonsági funkciókkal kombinálja.

## <a name="architectures-and-languages"></a>Architektúra és nyelvek

Az eredeti megoldásgyorsítók a .NET és model-view-controller (MVC) architektúra használatával készültek. A Microsoft új mikroszolgáltatási architektúra használatára frissíti a megoldásgyorsítókat. Az alábbi táblázat ismerteti a megoldásgyorsítók jelenlegi állapotát, illetve tartalmazza a megfelelő GitHub-adattárakra mutató hivatkozásokat:

| Megoldásgyorsító   | Architektúra  | Nyelvek     |
| ---------------------- | ------------- | ------------- |
| Csatlakoztatott gyár      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Eszközszimuláció      | Mikroszolgáltatások | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

A Services architektúrával kapcsolatos további információkért lásd: [Bevezetés az Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot/).

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A megoldásgyorsítókat a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#) webhelyéről vagy a parancssorból helyezheti üzembe.

A megoldás-gyorsító futtatásának díja a [mögöttes Azure-szolgáltatások futtatásának összesített díja](https://azure.microsoft.com/pricing). Az igénybe vett Azure-szolgáltatások részleteit az üzembehelyezési beállítások kiválasztásakor tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Ha szeretné kipróbálni az egyik IoT-megoldás-gyorssegédet, tekintse meg a gyors üzembe helyezési [megoldást a csatlakoztatott gyár megoldásban](quickstart-connected-factory-deploy.md).
