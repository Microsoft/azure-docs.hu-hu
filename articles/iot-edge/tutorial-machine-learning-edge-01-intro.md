---
title: 'Oktatóanyag: a Azure IoT Edge Machine Learning részletes bemutatója'
description: Magas szintű oktatóanyag, amely végigvezeti a teljes körű, gépi tanuláshoz szükséges különféle feladatokkal az Edge-forgatókönyvben.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 67cc470b4f7f119b7f5b86bcb82ea284ab662dfe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463238"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Oktatóanyag: teljes körű megoldás Azure Machine Learning és IoT Edge használatával

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

A IoT-alkalmazások gyakran szeretnék kihasználni az intelligens felhő és az intelligens peremhálózat előnyeit. Ebben az oktatóanyagban bemutatjuk a gépi tanulási modellek betanítását a felhőben lévő IoT-eszközökről gyűjtött adatokkal, a modell üzembe helyezésével IoT Edge, valamint a modell karbantartásával és finomításával rendszeresen.

>[!NOTE]
>Az ebben az oktatóanyagban szereplő fogalmak a IoT Edge összes verziójára érvényesek, de a forgatókönyv kipróbálásához létrehozott minta-eszköz IoT Edge 1,1-es verzióban fut.

Az oktatóanyag elsődleges célja, hogy bevezesse a IoT-adatfeldolgozást a gépi tanulással, különösen a szélén. Habár az általános gépi tanulási munkafolyamatok számos aspektusát érintik, ez az oktatóanyag nem a gépi tanulás részletes bevezetését célozza. Ebben az esetben a használati esethez nem próbálunk meg kiválóan optimalizált modellt létrehozni – csak annyit teszünk, hogy egy életképes modell létrehozására és használatára vonatkozó folyamatot IoT az adatfeldolgozáshoz.

Az oktatóanyag ezen szakasza a következőket tárgyalja:

> [!div class="checklist"]
>
> * Az oktatóanyag következő részeinek végrehajtásához szükséges előfeltételek.
> * Az oktatóanyag célközönsége.
> * Az oktatóanyag által szimulált használati eset.
> * Az oktatóanyag általános folyamata a használati eset teljesítése érdekében következik be.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez hozzá kell férnie egy Azure-előfizetéshez, amelyben jogosultságokkal rendelkezik az erőforrások létrehozásához. Az oktatóanyagban használt szolgáltatások közül több Azure-díjat számol fel. Ha még nem rendelkezik Azure-előfizetéssel, akkor lehet, hogy megkezdheti az [ingyenes Azure-fiók](https://azure.microsoft.com/offers/ms-azr-0044p/)megkezdését.

Szükség van egy olyan gépre is, amelyen telepítve van a PowerShell, ahol parancsfájlokat futtathat egy Azure-beli virtuális gép fejlesztői számítógépként való beállításához.

Ebben a dokumentumban a következő eszközkészleteket használjuk:

* Azure IoT hub adatrögzítéshez

* Az adatelőkészítés és a gépi tanulási kísérletezés fő kezelőfelületének Azure Notebooks. A Python-kód a mintaadatok egy részhalmazán való futtatása nagyszerű módja annak, hogy gyors iterációs és interaktív fordulatot kapjon az adat-előkészítés során. A Jupyter-jegyzetfüzetek felhasználhatók a parancsfájlok futtatására is a számítási háttérbeli méretezési műveletekben.

* Azure Machine Learning háttérként a gépi tanuláshoz a nagy léptékű és a gépi tanulási rendszerkép generálásához. A Azure Machine Learning hátteret a Jupyter-jegyzetfüzetekben előkészített és tesztelt parancsfájlok használatával hajtjuk.

* Azure IoT Edge gépi tanulási rendszerkép nem felhőalapú alkalmazásához

Természetesen vannak más elérhető lehetőségek is. Bizonyos helyzetekben például a IoT Central a IoT-eszközökből származó kezdeti betanítási adatok rögzítésére szolgáló, kód nélküli Alternatív megoldásként használható.

## <a name="target-audience-and-roles"></a>Célközönség és szerepkörök

Ez a cikksorozat olyan fejlesztők számára készült, akik korábbi felhasználói élmény nélkül IoT a fejlesztést vagy a gépi tanulást. A gépi tanulás élvonalbeli üzembe helyezéséhez ismernie kell a technológiák széles körének összekapcsolását. Ebből az oktatóanyagból megtudhatja, hogy a teljes végpontok közötti forgatókönyvvel miként lehet a technológiák összekapcsolásának egyik módját egy IoT-megoldáshoz csatlakoztatni. A valós környezetekben ezek a feladatok különböző specializációkkal rendelkező személyek között terjeszthetők. A fejlesztők például az eszközre vagy a felhőre összpontosítanak, az adatszakértők pedig az elemzési modelleket tervezték. Ahhoz, hogy egy egyéni fejlesztő sikeresen elvégezze ezt az oktatóanyagot, kiegészítő útmutatást biztosítunk az információkhoz, és további információra mutató hivatkozásokat kaptunk, amelyekkel tisztában lehet azzal, hogy mi történik, illetve miért.

Azt is megteheti, hogy a különböző szerepkörökkel rendelkező munkatársakkal együtt követheti az oktatóanyagot, így teljes körű szakértelmét kihasználhatja, és megtanulhatja, hogyan illeszkednek egymáshoz a dolgok.

Mindkét esetben az olvasó (k) elérésének elősegítése érdekében az oktatóanyagban szereplő cikkek a felhasználó szerepkörét jelzik. Ezek a szerepkörök a következők:

* Felhőalapú fejlesztés (beleértve a DevOps-kapacitásban dolgozó felhőalapú fejlesztőket)
* Adatelemzés

## <a name="use-case-predictive-maintenance"></a>Használati eset: prediktív karbantartás

Ezt a forgatókönyvet a 2008-es, a PHM08-konferencián bemutatott használati esetünk alapján ismertetjük. A cél a turbofan-RUL hátralévő hasznos élettartamának előrejelzése. Ezek az adatmennyiségek a C-MAPS használatával lettek létrehozva, a MAPSs (moduláris Aero-Propulsion rendszer-szimulációs) szoftver kereskedelmi verziója. Ez a szoftver rugalmas turbofan motor-szimulációs környezetet biztosít az állapot, a vezérlés és a motor paramétereinek megfelelő szimulálása érdekében.

Az oktatóanyagban használt adatok a [Turbofan motor degradációs szimulációs adatkészletből](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)származnak.

A readme fájlból:

***Kísérleti forgatókönyv***

*Az adatkészletek több többváltozós idősorozatból állnak. Az egyes adatkészletek tovább vannak osztva a betanítási és tesztelési részhalmazokra. Az egyes idősorozatok egy másik motortól származnak, azaz az adatok ugyanabba a típusba tartozó hajtóművekből származnak. Az egyes motorok a kezdeti kopás és a gyártási variáció különböző mértékével kezdődnek, ami ismeretlen a felhasználó számára. Ez a kopás és a változás normálisnak számít, azaz nem minősül hibás feltételnek. Három működési beállítás van, amelyek jelentős hatással vannak a motor teljesítményére. Ezeket a beállításokat az adatközpont is tartalmazza. Az adatsérülést érzékelő zaj okozta.*

*A motor általában az egyes idősorok elején működik, és az adatsorozat során egy bizonyos ponton hibát dolgoz fel. A betanítási készletben a hiba a rendszerhibaig növekszik. A tesztelési készletben az idősorozat a rendszerhiba előtt némi időt ér el. A verseny célja, hogy előre megjósolja a hátralévő működési ciklusok számát a tesztelési csoport meghibásodása előtt, azaz a motor utolsó ciklusa utáni működési ciklusok számát. A tesztelési adatok valódi hátralévő hasznos élettartamának (RUL) a vektorát is biztosította.*

Mivel az adatgyűjtés a versenyre történt, a gépi tanulási modellek elsajátításának számos módszerét egymástól függetlenül tették közzé. Azt találtuk, hogy a példák tanulmányozása hasznos lehet egy adott gépi tanulási modell létrehozásához szükséges folyamat és indoklás megismeréséhez. Lásd például:

[Repülőgép-hajtómű meghibásodásának előrejelzési modellje](https://github.com/jancervenka/turbofan_failure) a GitHub felhasználói jancervenka.

A [Turbofan-motor csökkenése](https://github.com/hankroark/Turbofan-Engine-Degradation) a GitHub-felhasználók hankroark.

## <a name="process"></a>Folyamat

Az alábbi képen láthatók az oktatóanyagban követett durva lépések:

![Architektúra-diagram a folyamat lépéseihez](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. A **betanítási adatok gyűjtése**: a folyamat a betanítási adatok összegyűjtésével kezdődik. Bizonyos esetekben az adatok gyűjtése már megtörtént, és egy adatbázisban, vagy adatfájlok formájában érhető el. Más esetekben – különösen a IoT forgatókönyvek esetében – az adatokat a IoT-eszközökről és-érzékelőkről kell gyűjteni, és a felhőben kell tárolni.

   Feltételezzük, hogy nem rendelkezik turbofan-motorokkal, így a projektfájlok egy egyszerű eszköz-szimulátort is tartalmaznak, amely a NASA-eszköz adatokat küldi el a felhőbe.

1. **Készítse elő az adatfeldolgozást**. A legtöbb esetben az eszközökből és érzékelőkből gyűjtött nyers adatoknak a gépi tanulás előkészítését kell előkészíteniük. Ez a lépés magában foglalhatja az adatok törlését, az adatok újraformázását, vagy a további információk gépi tanuláshoz való előfeldolgozását.

   A repülőgép-hajtóművek adatai esetében az adatok előkészítése magában foglalja az adatokra vonatkozó tényleges észrevételek alapján a mintában szereplő összes adatpontra vonatkozó explicit idő-a meghibásodási idők kiszámítását. Ez az információ lehetővé teszi, hogy a gépi tanulási algoritmus megkeresse a tényleges érzékelő adatmintái és a motor várható hátralévő élettartama közötti összefüggéseket. Ez a lépés nagytartományra jellemző.

1. **Hozzon létre egy gépi tanulási modellt**. Az előkészített adatmennyiség alapján már különböző gépi tanulási algoritmusok és parameterizations használatával kísérletezünk, és összehasonlítjuk egymással az eredményeket.

   Ebben az esetben a teszteléshez hasonlítjuk össze a modell által kiszámított előre jelzett eredményt a motorok egy készletén megfigyelt valós eredmény alapján. Azure Machine Learning a modell beállításjegyzékében létrehozott modellek különböző ismétléseit kezeljük.

1. **A modell üzembe helyezése**. Ha már van olyan modellünk, amely megfelel a siker feltételeinek, átléphetünk az üzembe helyezésre. Ez magában foglalja a modell becsomagolását egy webszolgáltatási alkalmazásba, amely a REST-hívásokkal és a visszaadott elemzési eredményekkel együtt használható adatokkal. A webszolgáltatás-alkalmazás ezután egy Docker-tárolóba lesz csomagolva, amely a felhőben vagy IoT Edge modulban is üzembe helyezhető. Ebben a példában az üzembe helyezésre koncentrálunk IoT Edgera.

1. **A modell karbantartása és pontosítása**. A modell üzembe helyezése után nem végezünk munkát. Sok esetben szeretnénk folytatni az adatok gyűjtését és az adatok felhőbe való rendszeres feltöltését. Ezután ezeket az adattípusokat felhasználhatjuk a modell újratanításához és pontosításához, amelyet aztán újra üzembe helyezhetünk IoT Edge.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást el nem végezte, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag a következő szakaszokra oszlik:

1. Állítsa be a fejlesztési gépet és az Azure-szolgáltatásokat.
2. A gépi tanulási modul betanítási szolgáltatásának előállítása.
3. A Machine learning modul betanítása és üzembe helyezése.
4. Konfiguráljon egy IoT Edge eszközt úgy, hogy transzparens átjáróként működjön.
5. IoT Edge-modulok létrehozása és üzembe helyezése.
6. Az adatküldés a IoT Edge eszközre.

Folytassa a következő cikkel egy fejlesztési gép beállításához és az Azure-erőforrások kiépítéséhez.

> [!div class="nextstepaction"]
> [Környezet beállítása a gépi tanuláshoz IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)