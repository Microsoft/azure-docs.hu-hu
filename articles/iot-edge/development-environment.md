---
title: Azure IoT Edge fejlesztési környezet | Microsoft Docs
description: Ismerje meg a támogatott rendszereket és a külső fejlesztői eszközöket, amelyek segítenek a IoT Edge létrehozásában
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1070a5ddf298ad88100e7803635e970f9a314e52
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869580"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>A fejlesztési és tesztelési környezet előkészítése IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge meglévő üzleti logikát áthelyezi a peremhálózaton működő eszközökre. Ahhoz, hogy előkészítse az alkalmazásokat és a számítási feladatokat [IoT Edge modulokként](iot-edge-modules.md)való futtatásra, tárolóként kell felépítenie őket. Ez a cikk útmutatást nyújt a fejlesztési környezet konfiguráláshoz, hogy sikeresen létrehozható legyen egy IoT Edge megoldás. Miután beállította a fejlesztési környezetet, megtudhatja, hogyan fejleszthet saját IoT Edge [modulokat.](module-development.md)

Bármely IoT Edge megoldásban legalább két gépet érdemes figyelembe venni. Az egyik az IoT Edge eszköz (vagy eszközök), amely a modult IoT Edge futtatja. A másik a fejlesztői gép, amely modulok fejlesztésére, tesztelésére és üzembe helyezésére használható. Ez a cikk elsősorban a fejlesztői géppel foglalkozik. Tesztelési célból a két gép azonos lehet. A fejlesztői IoT Edge futtathatja a modulokat, és modulokat helyezhet üzembe rajta.

## <a name="operating-system"></a>Operációs rendszer

Azure IoT Edge támogatott operációs rendszerek egy adott [halmazán fut.](support.md) A fejlesztéshez IoT Edge a legtöbb olyan operációs rendszert használhatja, amely képes tárolómotort futtatni. A tárolómotor egy követelmény a fejlesztői gépen, hogy a modulokat tárolókként buildeljük és lekulljuk egy tároló-beállításjegyzékbe.

Ha a fejlesztői gép nem tud helyileg Azure IoT Edge, akkor [](#testing-tools) ebben a cikkben megismerheti a helyi tesztelést és hibakeresést segítő tesztelési eszközöket.

A fejlesztői gép operációs rendszerének nem kell megegyezni a saját IoT Edge operációs rendszerével. A tároló operációs rendszerének azonban konzisztensnek kell lennie a fejlesztői gép és a IoT Edge között. Fejleszthet például modulokat egy Windows rendszerű gépen, és üzembe helyezheti őket egy Linux-eszközön. A Windows rendszerű gépnek Linux-tárolókat kell futtatnia a Linux-eszköz moduljainak felépítéséhez.

## <a name="container-engine"></a>Tárolómotor

A felhőbeli IoT Edge az, hogy az üzleti és felhőbeli logikát távolról üzembe helyezheti az eszközökön úgy, hogy tárolókba csomagolással telepíti őket. A tárolók felépítéséhez egy tárolómotorra van szükség a fejlesztői gépen.

Az éles környezetben IoT Edge eszközök egyetlen támogatott tárolómotorja a Moby. Az Open Container Initiative-sel kompatibilis tárolómotorok, például a Docker azonban képes IoT Edge modul rendszerképének építésére.

## <a name="development-tools"></a>Fejlesztési eszközök

A Visual Studio és Visual Studio Code bővítményekkel is bővíti a IoT Edge megoldásokat. Ezek a bővítmények nyelvspecifikus sablonokat biztosítanak az új forgatókönyvek létrehozásához és IoT Edge telepítéséhez. A Azure IoT Edge és a Visual Studio Code Visual Studio-bővítményei segítségével kódokat építhet be, helyezhet üzembe és IoT Edge megoldásokat. Létrehozhat egy teljes IoT Edge, amely több modult tartalmaz, és a bővítmények automatikusan frissítik az üzembe helyezési jegyzéksablont minden új modulbővítménysel. A bővítményekkel az IoT-eszközöket a Kód Visual Studio vagy Visual Studio is kezelheti. Modulok üzembe helyezése egy eszközön, az állapot figyelése és az üzenetek megtekintése, amint megérkeznek IoT Hub. Mindkét bővítmény az [IoT EdgeHub](#iot-edgehub-dev-tool) fejlesztői eszközzel teszi lehetővé a modulok helyi futtatását és hibakeresését a fejlesztői gépen.

Ha inkább más szerkesztővel vagy a parancssori felületről szeretne fejleszteni, a Azure IoT Edge dev eszköz olyan parancsokat biztosít, amelyek segítségével a parancssorból fejleszthet és tesztelhet. Létrehozhat új forgatókönyveket IoT Edge modul rendszerképeket hozhat létre, modulokat futtathat egy szimulátorban, és figyelheti a modulnak küldött IoT Hub.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-bővítmény

Az Azure IoT Edge Code Azure IoT Edge Visual Studio bővítménye olyan programozási nyelvekre IoT Edge modulsablonokat biztosít, mint IoT Edge C, C#, Java, Node.js és Python, valamint a C# azure-függvénye.

További információért és letöltésért lásd: [Azure IoT Tools a Visual Studio Code-hoz.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

A bővítménybővítmények IoT Edge mellett hasznosnak találhat további bővítményeket is telepíteni a fejlesztéshez. A docker support [for Visual Studio Code (Docker-támogatás](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) a Visual Studio Code-hoz) használatával kezelheti a rendszerképeket, a tárolókat és a beállításregisztrálókat. Emellett az összes főbb támogatott nyelvhez elérhető a Visual Studio Code bővítménye, amely segíthet a modulok fejlesztése során.

#### <a name="prerequisites"></a>Előfeltételek

Egyes nyelvekhez és szolgáltatásokhoz a modulsablonok előfeltételekkel vannak felépítve a projektmappák a fejlesztői gépen az Visual Studio használatával.

| Modulsablon | Előfeltétel |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [A JAVA_HOME környezeti változó beállítása](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modulgenerátor](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019-es bővítmény

A Azure IoT Edge eszközök Visual Studio C#-ra IoT Edge C#-ra és C-ra épített modulsablont biztosítanak.

További információ és letöltés: [Azure IoT Edge Tools 2017 Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) vagy Azure IoT Edge Tools [for Visual Studio 2019.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

### <a name="iot-edge-dev-tool"></a>IoT Edge fejlesztői eszköz

A Azure IoT Edge fejlesztői eszköz leegyszerűsíti IoT Edge parancssori képességekkel való fejlesztést. Ez az eszköz parancssori felületi parancsokat biztosít a modulok fejlesztéséhez, hibakereséséhez és teszteléséhez. A IoT Edge fejlesztői eszköz együttműködik a fejlesztői rendszerrel, függetlenül attól, hogy manuálisan telepítette a függőségeket a gépre, vagy a IoT Edge Dev-tárolót használja.

További információkért és az első lépésekért lásd a fejlesztői [IoT Edge wikit.](https://github.com/Azure/iotedgedev/wiki)

## <a name="testing-tools"></a>Tesztelési eszközök

Számos tesztelési eszköz létezik, amelyek segítségével hatékonyabban szimulálhat IoT Edge vagy hibakeresést futtathat a modulokban. Az alábbi táblázat az eszközök magas szintű összehasonlítását mutatja be, majd az egyes szakaszok az egyes eszközöket pontosabban ismertetik.

Éles környezetben üzemelő példányok esetében csak a IoT Edge támogatott, de az alábbi eszközökkel szimulálhatja vagy egyszerűen hozhat létre IoT Edge-eszközöket fejlesztési és tesztelési célokra. Ezek az eszközök nem kölcsönösen kizárják egymást, de teljes fejlesztési élmény érdekében együtt tudnak működni.

| Eszköz | Néven | Támogatott platformok | A következőkre alkalmas |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub fejlesztői eszköz  | iotedgehubdev | Windows, Linux, macOS | Eszköz szimulálása modulok hibakereséséhez. |
| IoT Edge dev tároló | iotedgedev | Windows, Linux, macOS | Fejlesztés függőségek telepítése nélkül. |
| IoT Edge futtatása tárolóban | iotedgec | Windows, Linux, macOS, ARM | Tesztelés olyan eszközön, amely nem támogatja a futásidőt. |
| IoT Edge eszköztároló használata | tool mobileszköz/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Egy forgatókönyv tesztelése számos IoT Edge eszköz nagy méretekben. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub fejlesztői eszköz

Az Azure IoT EdgeHub fejlesztői eszköz helyi fejlesztési és hibakeresési élményt biztosít. Az eszköz segít IoT Edge modulok IoT Edge nélküli futtatásában, így helyileg hozhat létre, fejleszthet, tesztelhet, futtathat és IoT Edge modulokat és megoldásokat. Nem kell rendszerképeket leküldéssel egy tároló-beállításjegyzékbe lekért és üzembe helyezni egy eszközön tesztelés céljából.

Az IoT EdgeHub fejlesztői eszköz úgy lett kialakítva, hogy az Visual Studio és az Visual Studio Code bővítményekkel, valamint a IoT Edge fejlesztői eszközzel is működjön együtt. Támogatja a belső ciklusfejlesztést és a külső ciklustesztelést, így a DevOps-eszközökkel is integrálható.

További információkért és a telepítésért lásd: [Azure IoT EdgeHub fejlesztői eszköz.](https://pypi.org/project/iotedgehubdev/)

### <a name="iot-edge-dev-container"></a>IoT Edge dev tároló

A Azure IoT Edge dev tároló egy Docker-tároló, amely a fejlesztéshez szükséges IoT Edge rendelkezik. Ez a tároló megkönnyíti az első lépések kidolgozását azzal a nyelvvel, amelyiken fejleszteni szeretne, beleértve a C#, a Python, a Node.js és a Java nyelvet. Mindössze egy olyan tárolómotort kell telepítenie, mint a Docker vagy a Moby, hogy le tudja húzni a tárolót a fejlesztői gépre.

További információ: Azure IoT Edge [dev tároló.](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container)

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge futtatása tárolóban

A IoT Edge egy tárolóban található futtatás teljes futásidejű környezetet biztosít, amely környezeti változóként használja az eszköz kapcsolati sztringet. Ez a tároló lehetővé teszi IoT Edge modulok és forgatókönyvek tesztelését olyan rendszeren, amely nem támogatja natív módon a futásidőt, például macOS. Az üzembe helyezett modulok a futásidejű tárolón kívül lesznek elindítva. Ha azt szeretné, hogy a futásidő és az üzembe helyezett modulok ugyanazon a tárolón belül létezni tudjanak, fontolja meg a IoT Edge eszköztárolót.

További információ: [Azure IoT Edge futtatása tárolóban.](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)

### <a name="iot-edge-device-container"></a>IoT Edge eszköztároló használata

A IoT Edge eszköztároló egy teljes IoT Edge, amely bármely, tárolómotorral felszerelt gépen elindítható. Az eszköztároló tartalmazza a IoT Edge-t és magát a tárolómotort. A tároló minden példánya egy teljesen működőképes önkiépítési IoT Edge eszköz. Az eszköztároló támogatja a modulok távoli hibakeresését, ha van hálózati útvonal a modulhoz. Az eszköztároló nagy számú virtuális eszköz gyors létrehozásához IoT Edge nagy léptékű forgatókönyvek vagy Az Azure Pipelines teszteléséhez. Emellett a Helmen keresztüli kubernetes-üzembe helyezést is támogatja.

További információ: [eszköztároló Azure IoT Edge használata.](https://github.com/toolboc/azure-iot-edge-device-container)

## <a name="devops-tools"></a>DevOps-eszközök

Ha készen áll nagy léptékű megoldások fejlesztésére kiterjedt éles forgatókönyvekhez, használja ki a modern DevOps-alapelveket, például az automatizálást, a monitorozást és az egyszerűsített szoftvermérnöki folyamatokat. IoT Edge rendelkezik olyan bővítményekkel, amelyek támogatják a DevOps-eszközöket, például az Azure DevOpsot, a Azure DevOps Projects és a Jenkinst. Ha egy meglévő folyamatot szeretne testreszabni, vagy egy másik DevOps-eszközt szeretne használni, például a CircleCI-t vagy a CircleCI-t, ezt az IoT Edge fejlesztői eszköz CLI-funkcióival használhatja.

További információt, útmutatást és példákat az alábbi oldalakon talál:

* [Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [CI-/CD-folyamat létrehozása IoT Edge Azure DevOps Starter használatával](how-to-devops-starter.md)
* [IoT Edge DevOps GitHub-adattár](https://github.com/toolboc/IoTEdge-DevOps)