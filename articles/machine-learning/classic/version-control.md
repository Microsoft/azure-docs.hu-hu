---
title: 'ML Studio (klasszikus): alkalmazások életciklusának kezelése – Azure'
description: Alkalmazás-életciklus-kezelési ajánlott eljárások alkalmazása Azure Machine Learning Studio (klasszikus)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518402"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Az alkalmazások életciklusának kezelése Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A Azure Machine Learning Studio (klasszikus) az Azure Cloud platformon üzemelő gépi tanulási kísérletek fejlesztésére szolgáló eszköz. Olyan, mint a Visual Studio IDE és a skálázható felhőalapú szolgáltatás egyetlen platformba egyesítve. A standard szintű alkalmazás-életciklus-kezelési (ALM) eljárásokat beépítheti a különböző eszközökről az automatikus végrehajtásra és üzembe helyezésre Azure Machine Learning Studio (klasszikus). Ez a cikk néhány lehetőséget és megközelítést ismertet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Verziószámozási kísérlet
A kísérletek verziójának két ajánlott módja van. Használhatja a beépített futtatási előzményeket, vagy a kísérletet JSON formátumban exportálhatja, hogy külsőleg kezelhesse. Minden megközelítés előnyeit és hátrányait tartalmazza.

### <a name="experiment-snapshots-using-run-history"></a>Pillanatképek kipróbálása a futtatási előzmények használatával
A Azure Machine Learning Studio (klasszikus) tanulási kísérlet végrehajtási modelljében a kísérletet egy nem módosítható pillanatkép küldi el a feladatütemezőnek, amikor a **Futtatás** gombra kattint a kísérlet szerkesztőjében. A pillanatképek listájának megtekintéséhez kattintson a parancssáv **Előzmények** elemére a kísérlet szerkesztő nézetben.

![Futtatási előzmények gomb](./media/version-control/runhistory.png)

A pillanatképet zárolt módban is megnyithatja, ha a kísérlet elküldésének időpontjában a kísérletre kattint, és elkészítette a pillanatképet. Figyelje meg, hogy a lista első eleme, amely az aktuális kísérletet jelöli, szerkeszthető állapotban van. Azt is figyelje meg, hogy az egyes Pillanatképek különböző állapotokban is lehetnek, beleértve a befejezett (részleges Futtatás), a sikertelen, a sikertelen (részleges Futtatás) vagy a piszkozatot is.

![Futtatási előzmények listája](./media/version-control/runhistorylist.png)

Miután megnyitotta, mentheti a pillanatkép-kísérletet új kísérletként, majd módosíthatja azt. Ha a kísérlet pillanatképe olyan eszközöket tartalmaz, mint például a betanított modellek, átalakítások vagy a frissített verziókkal rendelkező adatkészletek, a pillanatkép a pillanatkép készítésekor megőrzi az eredeti verzióra mutató hivatkozásokat. Ha a zárolt pillanatképet új kísérletként menti, Azure Machine Learning Studio (klasszikus) észleli az eszközök újabb verziójának létezését, és automatikusan frissíti őket az új kísérlet során.

Ha törli a kísérletet, a kísérlet összes pillanatképét törli a rendszer.

### <a name="exportimport-experiment-in-json-format"></a>Kísérlet exportálása/importálása JSON formátumban
A futtatási előzmények pillanatképei megőrzik a kísérlet megváltoztathatatlan verzióját Azure Machine Learning Studio (klasszikus), amikor a rendszer minden egyes futtatáskor elküldte. Mentheti a kísérlet helyi példányát is, és megtekintheti a kedvenc forrásoldali vezérlő rendszerét, például Team Foundation Servert, és később újból létrehozhatja a kísérletet a helyi fájlból. A [Azure Machine learning PowerShell](https://aka.ms/amlps) -parancsmagok az [*export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) és az [*import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) használatával végezheti el.

A JSON-fájl a kísérleti gráf szöveges ábrázolása, amely tartalmazhatja a munkaterületen lévő eszközökre, például egy adatkészletre vagy egy betanított modellre mutató hivatkozást is. Nem tartalmazza az eszköz szerializált verzióját. Ha a JSON-dokumentumot visszahelyezi a munkaterületre, a hivatkozott objektumoknak már léteznie kell ugyanazzal az azonosítóval, amely a kísérletben hivatkozik. Ellenkező esetben nem férhet hozzá az importált kísérlethez.

## <a name="versioning-trained-model"></a>A betanított modell verziószámozása
Azure Machine Learning Studio (klasszikus) betanított modell szerializálható egy iLearner-fájlként () ismert formátumba `.iLearner` , és a munkaterülethez társított Azure Blob Storage-fiókban tárolódik. A iLearner-fájl másolatának beszerzésének egyik módja az átképzési API-n keresztül történik. [Ez a cikk](./retrain-machine-learning-model.md) bemutatja, hogyan működik az átképzési API. A magas szintű lépések:

1. Állítsa be a betanítási kísérletet.
2. Adjon hozzá egy webszolgáltatás kimeneti portját a Train Model modulhoz vagy a betanított modellt létrehozó modulhoz, például a modell Hiperparaméter vagy az R-modell létrehozásához.
3. Futtassa a betanítási kísérletet, majd telepítse a modell-betanítási webszolgáltatásként.
4. Hívja meg a betanítási webszolgáltatás BES végpontját, és adja meg a kívánt iLearner-fájl nevét és blob Storage-fiókjának helyét.
5. A létrehozott iLearner-fájl begyűjtése a BES hívás befejeződése után.

A iLearner-fájl lekérdezésének másik módja a PowerShell parancsmagot [*letöltése – AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Ez akkor lehet egyszerűbb, ha a modell programozott átképzése nélkül szeretné lekérni a iLearner fájl másolatát.

Miután megtörtént a betanított modellt tartalmazó iLearner-fájl, saját verziószámozási stratégiát alkalmazhat. A stratégiának olyan egyszerűnek kell lennie, mint a pre/Postfix alkalmazása elnevezési konvencióként, és csak a blob Storage-ban lévő iLearner-fájlból, vagy a verziókövetés rendszerbe történő másolásával/importálásával.

A mentett iLearner-fájl ezután felhasználható az üzembe helyezett webszolgáltatásokon keresztüli pontozáshoz.

## <a name="versioning-web-service"></a>Verziószámozási webszolgáltatás
Kétféle webszolgáltatást helyezhet üzembe egy Azure Machine Learning Studio (klasszikus) kísérletből. A klasszikus webszolgáltatás szorosan párosul a kísérlettel és a munkaterülettel. Az új webszolgáltatás a Azure Resource Manager keretrendszert használja, és már nem párosul az eredeti kísérlettel vagy a munkaterülettel.

### <a name="classic-web-service"></a>Klasszikus webszolgáltatás
A klasszikus webszolgáltatás verziójának kihasználásához használhatja a webszolgáltatás-végpont szerkezetét. Íme egy tipikus folyamat:

1. A prediktív kísérletből új klasszikus webszolgáltatást kell üzembe helyeznie, amely egy alapértelmezett végpontot tartalmaz.
2. Létrehoz egy EP2 nevű új végpontot, amely a kísérlet/betanított modell aktuális verzióját teszi elérhetővé.
3. Lépjen vissza, és frissítse a prediktív kísérletet és a betanított modellt.
4. Újra kell telepítenie a prediktív kísérletet, amely ezután frissíti az alapértelmezett végpontot. Ez azonban nem változtatja meg a EP2.
5. Hozzon létre egy ep3 nevű további végpontot, amely elérhetővé teszi a kísérlet és a betanított modell új verzióját.
6. Ha szükséges, térjen vissza a 3. lépéshez.

Idővel több végpont is létrehozható ugyanabban a webszolgáltatásban. Mindegyik végpont a betanított modell időponthoz tartozó verzióját tartalmazó kísérlet időponthoz tartozó másolatát jelöli. A külső logikával meghatározhatja, hogy melyik végpontot hívja meg, ami gyakorlatilag azt jelenti, hogy kijelöli a betanított modell egy verzióját a pontozási futtatáshoz.

Emellett számos azonos webszolgáltatás-végpontot is létrehozhat, majd a iLearner-fájl különböző verzióit a végpontra is kijavíthatja hasonló hatás eléréséhez. [Ez a cikk](create-models-and-endpoints-with-powershell.md) részletesebben ismerteti ezt a feladatot.

### <a name="new-web-service"></a>Új webszolgáltatás
Ha új Azure Resource Manager-alapú webszolgáltatást hoz létre, a végpont-összeállítás már nem érhető el. A webszolgáltatás-definíciós (WSD) fájlokat JSON formátumban is létrehozhatja a prediktív kísérletből az [export-AmlWebServiceDefinitionFromExperiment PowerShell-](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) parancsmagot, vagy egy telepített Resource Manager-alapú webszolgáltatásból származó [*export-AzMlWebservice PowerShell-*](/powershell/module/az.machinelearning/export-azmlwebservice) parancsmagot használatával.

Az exportált WSD-fájl és-verzió vezérlése után a WSD-t új webszolgáltatásként is telepítheti egy másik Azure-régióban található webszolgáltatási csomagba. Csak győződjön meg arról, hogy a megfelelő tárolási fiók konfigurációját és az új webszolgáltatás-csomag AZONOSÍTÓját adja meg. A különböző iLearner-fájlok javításához módosíthatja a WSD-fájlt, és frissítheti a betanított modell hivatkozási helyét, és telepítheti azt új webszolgáltatásként.

## <a name="automate-experiment-execution-and-deployment"></a>A kísérlet végrehajtásának és üzembe helyezésének automatizálása
A ALM fontos aspektusa, hogy automatizálja az alkalmazás végrehajtási és üzembe helyezési folyamatát. Azure Machine Learning Studio (klasszikus) esetén ezt a [PowerShell-modul](https://aka.ms/amlps)használatával végezheti el. Íme egy példa arra a végpontok közötti lépésekre, amelyek a standard ALM automatikus végrehajtási/telepítési folyamatra vonatkoznak a [Azure Machine learning Studio (klasszikus) PowerShell-modul](https://aka.ms/amlps)használatával. Az egyes lépések egy vagy több PowerShell-parancsmagok vannak társítva, amelyeket az adott lépés végrehajtásához használhat.

1. [Adatkészlet feltöltése](https://github.com/hning86/azuremlps#upload-amldataset).
2. Egy betanítási kísérletet a [munkaterületre vagy](https://github.com/hning86/azuremlps#copy-amlexperiment) a [katalógusból, vagy](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)egy [exportált](https://github.com/hning86/azuremlps#export-amlexperimentgraph) kísérlet helyi lemezről történő [importálásával](https://github.com/hning86/azuremlps#import-amlexperimentgraph) másolhat a munkaterületre.
3. [Frissítse az adatkészletet](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) a betanítási kísérletben.
4. [Futtassa a betanítási kísérletet](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Népszerűsítse a betanított modellt](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Prediktív kísérlet másolása](https://github.com/hning86/azuremlps#copy-amlexperiment) a munkaterületre.
7. [Frissítse a betanított modellt](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) a prediktív kísérletben.
8. [Futtassa a prediktív kísérletet](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Webszolgáltatás üzembe helyezése](https://github.com/hning86/azuremlps#new-amlwebservice) a prediktív kísérletből.
10. Tesztelje a webszolgáltatás [erőforrásrekordok](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) vagy [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) végpontját.

## <a name="next-steps"></a>Következő lépések
* Töltse le a [Azure Machine learning Studio (klasszikus) PowerShell](https://aka.ms/amlps) -modult, és kezdje meg a Alm-feladatok automatizálását.
* Megtudhatja, hogyan [hozhat létre és kezelhet nagy mennyiségű ml-modellt egyetlen kísérlettel](create-models-and-endpoints-with-powershell.md) a PowerShell és az API átképzése segítségével.
* További információ a [Azure Machine learning webszolgáltatások üzembe helyezéséről](deploy-a-machine-learning-web-service.md).