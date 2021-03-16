---
title: Azure IoT Edge fejlesztési környezet | Microsoft Docs
description: Ismerkedjen meg a támogatott rendszerekkel és az első féltől származó fejlesztői eszközökkel, amelyek segítségével IoT Edge modulokat hozhat létre
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 517bcdab375db819b0942306df82e7285b9473b1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487700"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>A fejlesztési és tesztelési környezet előkészítése IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge áthelyezi a meglévő üzleti logikát a peremhálózati eszközökre. Az alkalmazások és munkaterhelések [IoT Edge modulként](iot-edge-modules.md)való futtatásához tárolóként kell létrehoznia őket. Ez a cikk útmutatást nyújt a fejlesztési környezet konfigurálásához, hogy sikeresen létrehozzon egy IoT Edge megoldást. Miután beállította a fejlesztési környezetet, megtudhatja, hogyan [fejlesztheti saját IoT Edge modulait](module-development.md).

A IoT Edge-megoldásokban legalább két gépet kell figyelembe venni. Az egyik a IoT Edge modult futtató IoT Edge eszköz (vagy eszközök). A másik a modulok létrehozásához, teszteléséhez és üzembe helyezéséhez használt fejlesztői gép. Ez a cikk elsősorban a fejlesztői gépre koncentrál. Tesztelési célból a két gép lehet azonos. IoT Edge futtatható a fejlesztői gépen, és modulokat telepíthet rá.

## <a name="operating-system"></a>Operációs rendszer

Azure IoT Edge a [támogatott operációs rendszerek](support.md)adott készletén fut. A IoT Edge fejlesztéséhez használhatja a legtöbb operációs rendszert, amely képes a tároló motor futtatására. A tároló motor a modulok tárolóként való felépítésének és a tároló-beállításjegyzékbe való leküldésének követelménye a fejlesztői gépen.

Ha a fejlesztői számítógép nem tudja futtatni a Azure IoT Edge, folytassa a jelen cikkben a helyi teszteléshez és hibakereséshez segítséget nyújtó [eszközök teszteléséhez](#testing-tools) .

A fejlesztői számítógép operációs rendszerének nem kell megegyeznie a IoT Edge eszköz operációs rendszerével. A tároló operációs rendszernek azonban konzisztensnek kell lennie a fejlesztői gép és a IoT Edge eszköz között. Létrehozhat például modulokat egy Windows rendszerű gépen, és telepítheti őket egy Linux-eszközre. A Windows rendszerű gépnek Linux-tárolókat kell futtatnia a Linux-eszköz moduljainak létrehozásához.

## <a name="container-engine"></a>Tároló motorja

IoT Edge központi fogalma az, hogy a tárolók csomagolásával távolról üzembe helyezheti az üzleti és a Felhőbeli logikát az eszközökön. A tárolók létrehozásához szükség van egy tároló motorra a fejlesztői gépen.

Az egyetlen támogatott tároló motor az éles környezetben lévő IoT Edge eszközökhöz: Moby. Azonban minden olyan tároló-motor, amely kompatibilis a nyílt tároló kezdeményezéssel, mint például a Docker, képes IoT Edge modul rendszerképeinek kiépítésére.

## <a name="development-tools"></a>Fejlesztési eszközök

A Visual Studio és a Visual Studio Code kiegészítő bővítményekkel segíti a IoT Edge-megoldások fejlesztését. Ezek a bővítmények nyelvspecifikus sablonokat biztosítanak az új IoT Edge forgatókönyvek létrehozásához és üzembe helyezéséhez. A Visual studióhoz és a Visual Studio Code-hoz készült Azure IoT Edge-bővítmények segítséget nyújtanak a IoT Edge-megoldások kódolásához, összeállításához, üzembe helyezéséhez és hibakereséséhez. Létrehozhat egy teljes IoT Edge megoldást, amely több modult is tartalmaz, és a bővítmények automatikusan frissítik a telepítési jegyzékfájlok sablonját minden új modul hozzáadásával. A bővítmények segítségével a IoT-eszközöket a Visual studióból vagy a Visual Studio Code-ból is kezelheti. Modulokat telepíthet egy eszközre, figyelheti az állapotot, és megtekintheti a IoT Hub megérkezéskor megjelenő üzeneteket. Mindkét bővítmény a [IoT EdgeHub dev Tool eszközzel](#iot-edgehub-dev-tool) lehetővé teszi a modulok helyi futtatását és hibakeresését a fejlesztői gépen is.

Ha más szerkesztővel vagy a parancssori felületről szeretne fejleszteni, a Azure IoT Edge dev eszköz parancsokat biztosít a parancssorból való fejlesztéshez és teszteléshez. Létrehozhat új IoT Edge forgatókönyveket, modul-lemezképeket készíthet, modulokat futtathat a szimulátorban, és figyelheti a IoT Hub küldött üzeneteket.

### <a name="visual-studio-code-extension"></a>Visual Studio Code-bővítmény

A Visual Studio Code-hoz készült Azure IoT Edge-bővítmény a programozási nyelvekre épülő, a C, a C#, a Java, a Node.js és a Python, valamint a C#-beli Azure functions által készített sablonok IoT Edge.

További információért és a letöltéshez tekintse meg a [Visual Studio Code-hoz készült Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)című témakört.

A IoT Edge bővítmények mellett hasznos lehet további bővítményeket telepíteni a fejlesztéshez. A [Visual Studio Code-hoz készült Docker-támogatással](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) például kezelheti a lemezképeket, a tárolókat és a jegyzékeket. Emellett az összes főbb támogatott nyelv rendelkezik olyan bővítményekkel a Visual Studio Code-hoz, amelyek segíthetnek a modulok fejlesztésében.

#### <a name="prerequisites"></a>Előfeltételek

Egyes nyelvekhez és szolgáltatásokhoz tartozó modul-sablonok előfeltételei szükségesek ahhoz, hogy a Visual Studio Code segítségével létrehozza a projekt mappáit a fejlesztői gépen.

| Modulsablon | Előfeltétel |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [A JAVA_HOME környezeti változó beállítása](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js modul generátora](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 bővítmény

A Visual studióhoz készült Azure IoT Edge Tools egy, a C# és C nyelvre épülő IoT Edge modul-sablont biztosít.

További információ és Letöltés: [Azure IoT Edge Tools for Visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) vagy [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge fejlesztői eszköz

A Azure IoT Edge fejlesztői eszköz leegyszerűsíti a IoT Edge fejlesztést parancssori képességekkel. Ez az eszköz CLI-parancsokat biztosít a modulok fejlesztéséhez, hibakereséséhez és teszteléséhez. A IoT Edge dev eszköz együttműködik a fejlesztői rendszerrel, függetlenül attól, hogy manuálisan telepítette-e a függőségeket a gépen, vagy az IoT Edge dev tárolót használja.

További információ és az első lépések: [IoT Edge dev Tool wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Tesztelési eszközök

Számos olyan tesztelési eszköz létezik, amely segít a IoT Edge-eszközök és a hibakeresési modulok hatékonyabb szimulálásában. Az alábbi táblázat az eszközök magas szintű összehasonlítását mutatja be, majd az egyes szakaszok pontosabban írják le az egyes eszközöket.

Az éles környezetekben csak a IoT Edge futtatókörnyezet támogatott, az alábbi eszközök azonban lehetővé teszik IoT Edge-eszközök szimulálását vagy egyszerű létrehozását fejlesztési és tesztelési célokra. Ezek az eszközök nem zárják ki egymást, de a teljes fejlesztési élmény érdekében együtt dolgozhatnak.

| Eszköz | Más néven | Támogatott platformok | A következőkre alkalmas |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub dev eszköz  | iotedgehubdev | Windows, Linux, macOS | Eszköz szimulálása hibakeresési modulokhoz. |
| IoT Edge dev-tároló | iotedgedev | Windows, Linux, macOS | Fejlesztés a függőségek telepítése nélkül. |
| IoT Edge-futtatókörnyezet egy tárolóban | iotedgec | Windows, Linux, macOS, ARM | Tesztelés olyan eszközön, amely esetleg nem támogatja a futtatókörnyezetet. |
| IoT Edge eszköz tárolója | toolboc/Azure-IOT-Edge-Device-Container | Windows, Linux, macOS, ARM | Egy forgatókönyv tesztelése számos IoT Edge eszközzel nagy léptékben. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub dev eszköz

Az Azure IoT EdgeHub dev eszköz helyi fejlesztési és hibakeresési élményt biztosít. Az eszköz megkönnyíti IoT Edge modulok indítását IoT Edge futtatókörnyezet nélkül, így helyileg hozhat létre, fejleszthet, tesztelheti, futtathatja és hibakeresési IoT Edge modulokat és megoldásokat. A lemezképeket nem kell leküldeni egy tároló-beállításjegyzékbe, és a teszteléshez telepítenie kell őket az eszközön.

A IoT EdgeHub dev eszköz úgy lett kialakítva, hogy a Visual Studio és a Visual Studio Code-bővítményekkel párhuzamosan működjön együtt, valamint a IoT Edge dev eszközzel. Támogatja a belső hurok-fejlesztést, valamint a külső hurok tesztelését, így a DevOps eszközökkel is integrálható.

További információért és a telepítéshez tekintse meg az [Azure IoT EdgeHub dev Tool](https://pypi.org/project/iotedgehubdev/)című témakört.

### <a name="iot-edge-dev-container"></a>IoT Edge dev-tároló

A Azure IoT Edge fejlesztői tároló egy olyan Docker-tároló, amely a IoT Edge-fejlesztéshez szükséges összes függőséggel rendelkezik. Ez a tároló megkönnyíti az első lépéseket, például a C#, a Python, a Node.js és a Java nyelveket. A telepítéshez csak a Docker vagy a Moby rendszernek kell telepítenie a tárolót a fejlesztői gépre.

További információ: [Azure IoT Edge fejlesztői tároló](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge-futtatókörnyezet egy tárolóban

A tárolóban lévő IoT Edge futtatókörnyezet teljes futtatókörnyezetet biztosít, amely az eszköz csatlakoztatási karakterláncát környezeti változóként veszi igénybe. Ez a tároló lehetővé teszi IoT Edge modulok és forgatókönyvek tesztelését olyan rendszeren, amely nem támogatja a futtatókörnyezetet natív módon, például a macOS-t. Az üzembe helyezett modulok a futásidejű tárolón kívül lesznek elindítva. Ha azt szeretné, hogy a futtatókörnyezet és a telepített modulok ugyanabban a tárolóban legyenek, vegye figyelembe a IoT Edge eszköz tárolóját.

További információ: [Azure IoT Edge futtatása tárolóban](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge eszköz tárolója

A IoT Edge Device Container egy teljes körű IoT Edge eszköz, amely minden olyan gépen elindítható, amely egy tároló motorral rendelkezik. Az eszköz tárolója magába foglalja a IoT Edge futtatókörnyezetet és a tároló motorját. A tároló minden példánya egy teljesen működőképes, önálló kiépíthető IoT Edge eszköz. Az eszköz-tároló támogatja a modulok távoli hibakeresését, feltéve, hogy a modulnak van hálózati útvonala. Az eszköz tárolója kiválóan alkalmas a nagy számú IoT Edge eszköz gyors létrehozására a méretezési forgatókönyvek vagy az Azure-folyamatok teszteléséhez. Támogatja a kubernetes-n keresztüli üzembe helyezést is.

További információ: [Azure IoT Edge eszköz tárolója](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps-eszközök

Ha készen áll a nagy teljesítményű megoldások széles körű fejlesztésére, használja ki a modern DevOps alapelveit, például az automatizálást, a figyelést és az egyszerűsített szoftverfejlesztés folyamatait. IoT Edge támogatja a DevOps-eszközök (például az Azure DevOps, a Azure DevOps Projects és a Jenkins) támogatását. Ha testre szeretné szabni egy meglévő folyamatot, vagy egy másik DevOps eszközt szeretne használni, például a CircleCI vagy a TravisCI, akkor a IoT Edge dev eszközben található CLI-funkciókkal teheti meg.

További információt, útmutatást és példákat a következő lapokon talál:

* [Folyamatos integráció és folyamatos üzembe helyezés Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [CI/CD-folyamat létrehozása IoT Edgehoz az Azure DevOps Starter-vel](how-to-devops-starter.md)
* [Azure IoT Edge Jenkins beépülő modul](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub-tárház](https://github.com/toolboc/IoTEdge-DevOps)