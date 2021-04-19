---
title: Bevezetés az IoT-megoldásgyorsítókba – Azure | Microsoft Docs
description: Megismerheti az Azure IoT-megoldásgyorsítókat. Az IoT-megoldásgyorsítók teljes körű, átfogó, üzembe helyezésre kész IoT-megoldások.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: c966051ed5699d408fe83f1e9c862ca78b3282c4
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714541"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Mik az Azure IoT-megoldásgyorsítók?

A felhőalapú IoT-megoldások általában egyéni kódot és felhőszolgáltatásokat használnak az eszközkapcsolat, az adatfeldolgozás, az elemzés és a megjelenítés kezeléséhez.

Az IoT-megoldásgyorsítók teljes körű, üzembe helyezésre kész IoT-megoldások, amelyek gyakori IoT-forgatókönyveket implementálnak. A forgatókönyvek közé tartoznak a csatlakoztatott gyárak és az eszközszimuláció. A megoldásgyorsítók üzembe helyezésekor az üzemelő példány magában foglalja az összes szükséges felhőalapú szolgáltatást és minden szükséges alkalmazáskódot.

A megoldásgyorsítók az Ön saját IoT-megoldásainak kiindulópontjaiként szolgálnak. Az összes megoldásgyorsító forráskódja nyílt, és elérhető a GitHubban. A megoldásgyorsítókat letöltheti és saját igényei szerint testre szabhatja.

A megoldásgyorsítókat tanulási eszközként is használhatja, mielőtt létrehozná saját egyéni IoT-megoldását. A megoldásgyorsítók bevált gyakorlatokat alkalmaznak a felhőalapú IoT-megoldásokhoz, amelyeket követhet.

Az összes megoldásgyorsító alkalmazáskódja tartalmaz egy olyan webalkalmazást, amely lehetővé teszi az alkalmazásgyorsító kezelését.

> [!NOTE]
> A távoli figyelési és prediktív karbantartási megoldások el vannak távolítva a [Azure IoT solution accelerators](https://www.azureiotsolutions.com/Accelerators) oldalról. További információ: [Mik azok a Azure IoT solution accelerators? (előző verzió)](/previous-versions/azure/iot-accelerators/about-iot-accelerators).

## <a name="supported-iot-scenarios"></a>Támogatott IoT-forgatókönyvek

Jelenleg két megoldásgyorsítót helyezhet üzembe:

### <a name="connected-factory"></a>Csatlakoztatott gyár

A [csatlakoztatottgyár-gyorsítóval](iot-accelerators-connected-factory-features.md) telemetriai adatokat gyűjt az ipari eszközökről egy [OPC egységes](https://opcfoundation.org/about/opc-technologies/opc-ua/) architektúrafelületen, és vezérelhetőek. Az ipari objektumok lehetnek például a gyártósoron található összeszerelő- és tesztelőállomások.

A csatlakoztatott gyár irányítópultjának használatával a következő ipari eszközöket monitorozhatja és kezelheti:

:::image type="content" source="./media/about-iot-accelerators/cf-dashboard-inline.png" alt-text="A csatlakoztatott gyár megoldás irányítópultját bemutató képernyőkép." lightbox="./media/about-iot-accelerators/cf-dashboard-expanded.png":::

### <a name="device-simulation"></a>Eszközszimuláció

Az [eszközszimulációs megoldásgyorsítóval](iot-accelerators-device-simulation-overview.md) valósághű telemetriát generáló szimulált eszközöket futtathat. Ez a megoldásgyorsító más megoldásgyorsítók viselkedésének vagy saját IoT-megoldásainak tesztelésére is használható.

Az eszközszimulációs webalkalmazás a következő szimulációk konfigurálására és futtatására használható:

:::image type="content" source="./media/about-iot-accelerators/ds-dashboard-inline.png" alt-text="Képernyőkép az eszközszimulációs megoldás irányítópultjáról." lightbox="./media/about-iot-accelerators/ds-dashboard-expanded.png":::

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
| Eszközszimuláció      | Mikroszolgáltatások | [.NET](https://github.com/Azure/azure-iot-pcs-device-simulation)          |

A mikroszolgáltatás-architektúrával kapcsolatos további információkért lásd: Bevezetés az [Azure IoT referenciaarchitektúrába.](/azure/architecture/reference-architectures/iot/)

## <a name="deployment-options"></a>Üzembe helyezési lehetőségek

A megoldásgyorsítókat a [Microsoft Azure IoT-megoldásgyorsítók](https://www.azureiotsolutions.com/Accelerators#) webhelyéről vagy a parancssorból helyezheti üzembe.

A megoldásgyorsítók futtatásának költsége a mögöttes [Azure-szolgáltatások futtatásának kombinált költsége.](https://azure.microsoft.com/pricing) Az igénybe vett Azure-szolgáltatások részleteit az üzembehelyezési beállítások kiválasztásakor tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Az IoT-megoldásgyorsítók egyikének kipróbálásért tekintse meg a következő rövid útmutatót: Try a connected factory solution (Csatlakoztatott gyár [megoldás kipróbálja a csatlakoztatott gyár megoldását).](quickstart-connected-factory-deploy.md)
