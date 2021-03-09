---
title: 'MLOps: ML-modell kezelése'
titleSuffix: Azure Machine Learning
description: 'További információ a Azure Machine Learning (MLOps) modellel történő kezeléséről. A modellek üzembe helyezése, kezelése és monitorozása folyamatosan fejleszthető. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 32777d9f8313457036b103777f251329687b3997
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508010"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: a modell kezelése, üzembe helyezése és figyelése Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning a modellek életciklusának kezeléséhez. A Azure Machine Learning a Machine Learning Operations (MLOps) módszert használja. A MLOps javítja a gépi tanulási megoldások minőségét és egységességét. 

## <a name="what-is-mlops"></a>Mi az az MLOps?

A Machine Learning Operations (MLOps) a munkafolyamatok hatékonyságát növelő [DevOps](https://azure.microsoft.com/overview/what-is-devops/) alapelvek és eljárások alapján történik. Például: folyamatos integráció, kézbesítés és üzembe helyezés. A MLOps ezeket az alapelveket a gépi tanulási folyamatra alkalmazza, a következő céllal:

* Gyorsabb kísérletezés és a modellek fejlesztése
* Modellek gyorsabb üzembe helyezése éles környezetben
* Minőségbiztosítás

A Azure Machine Learning a következő MLOps képességeket biztosítja:

- **Reprodukálható ml-folyamatokat hozhat létre**. Machine Learning folyamatok lehetővé teszik az adatelőkészítési,-betanítási és-pontozási eljárások ismételhető és újrafelhasználható lépéseinek megadását.
- **Újrafelhasználható szoftvereket hozhat létre** a modellek betanításához és üzembe helyezéséhez.
- **Modellek regisztrálása, becsomagolása és üzembe helyezése bárhonnan**. A modell használatához szükséges metaadatokat is nyomon követheti.
- **Rögzítse a végpontok közötti ml-életciklus irányítási szolgáltatásait**. A naplózott információk közé tartozhatnak a modellek közzétételi modelljei, a változtatások, valamint a modellek üzembe helyezése vagy használata az éles környezetben.
- **Értesítés és riasztás a ml-életciklus eseményeiről**. Például a kísérlet befejezését, a modell regisztrációját, a modell üzembe helyezését és az adateltolódás észlelését.
- **Ml-alkalmazások figyelése operatív és ml-vel kapcsolatos problémák esetén**. Hasonlítsa össze a modell bemeneteit a képzés és a következtetések között, Fedezze fel a modellre jellemző mérőszámokat, és figyelje a ML-infrastruktúráját.
- **A végpontok közötti ml életciklus automatizálása Azure Machine learning és Azure-folyamatokkal**. A folyamatok használata lehetővé teszi a modellek gyakori frissítését, az új modellek tesztelését, valamint az új ML-modellek folyamatos kiépítését más alkalmazásokkal és szolgáltatásokkal együtt.

## <a name="create-reproducible-ml-pipelines"></a>Reprodukálható ML-folyamatok létrehozása

A modell betanítási folyamatában szereplő összes lépés összefűzéséhez használjon Azure Machine Learning ML-es folyamatokat.

A ML-folyamatok az adatok előkészítésének lépéseit is tartalmazhatják, hogy hiperparaméter a hangolást a modell kiértékeléséhez. További információ: [ml folyamatok](concept-ml-pipelines.md).

Ha a [Designer](concept-designer.md) használatával hozza létre az ml-folyamatokat, akkor a tervező oldal jobb felső sarkában található **"..."** gombra kattintva bármikor kiválaszthatja a **klónozás** elemet. A folyamat klónozása lehetővé teszi, hogy a régi verziók elvesztése nélkül megismételje a folyamat kialakítását.  

## <a name="create-reusable-software-environments"></a>Újrafelhasználható szoftveres környezetek létrehozása

Azure Machine Learning környezetek lehetővé teszik a projektek szoftveres függőségeinek nyomon követését és újbóli előállítását. A környezetek lehetővé teszik annak biztosítását, hogy a buildek reprodukálása manuálisan történjen a szoftveres konfigurációk nélkül.

A környezetek leírják a projektek pip-és Conda-függőségeit, és használhatók a modellek betanításához és üzembe helyezéséhez is. További információ: [Mik a Azure Machine learning környezetek](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Modellek regisztrálása, becsomagolása és üzembe helyezése bárhonnan

### <a name="register-and-track-ml-models"></a>ML-modellek regisztrálása és nyomon követése

A modell regisztrációja lehetővé teszi, hogy a saját munkaterületén tárolja és megtekintse a modelljeit az Azure-felhőben. A modell beállításjegyzéke megkönnyíti a betanított modellek rendszerezését és nyomon követését.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

A regisztrált modelleket a név és a verziószám azonosítja. Ha egy modellt egy már létező néven regisztrál, a beállításjegyzék megnöveli annak verziószámát. A regisztráció során további metaadat-címkéket is megadhat. Ezeket a címkéket a rendszer a modell keresésekor használja. A Azure Machine Learning a Python 3.5.2-es vagy újabb verziójával tölthető modelleket támogat.

> [!TIP]
> A Azure Machine Learningon kívül betanított modelleket is regisztrálhat.

Aktív központi telepítésben használt regisztrált modell nem törölhető.
További információkért lásd a [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel)című szakaszt a modell regisztrálása szakaszban.

> [!IMPORTANT]
> Ha `Tags` Azure Machine learning Studio modellek lapján a szűrés lehetőséggel használja, az ügyfelek használata helyett `TagName : TagValue` `TagName=TagValue` (szóköz nélkül)

### <a name="profile-models"></a>Profilmodellek

A Azure Machine Learning segítségével megismerheti a szolgáltatás CPU-és memória-követelményeit, amelyeket a rendszer a modell telepítésekor hoz létre. A profilkészítés a modellt futtató szolgáltatást teszteli, és olyan információkat ad vissza, mint például a CPU-használat, a memóriahasználat és a válasz késése. Emellett egy CPU-és memória-javaslatot is biztosít az erőforrás-használat alapján.
További információért lásd a [modellek üzembe helyezése](how-to-deploy-profile-model.md)című szakaszt.

### <a name="package-and-debug-models"></a>Csomagok és hibakeresési modellek

A modell éles környezetben történő üzembe helyezése előtt egy Docker-rendszerképbe van csomagolva. A legtöbb esetben a rendszerkép létrehozása a háttérben automatikusan megtörténik a telepítés során. Manuálisan is megadhatja a rendszerképet.

Ha a telepítés során problémákba ütközne, a hibaelhárításhoz és a hibakereséshez telepítheti a helyi fejlesztési környezetét.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md#registermodel) és [hibaelhárítása](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Modellek konvertálása és optimalizálása

A modell átalakítása a [neurális hálózati Exchange](https://onnx.ai) (ONNX) megnyitásával növelheti a teljesítményt. Átlagosan a ONNX-re való konvertálás a kétszeri teljesítmény növelését eredményezheti.

A Azure Machine Learningrel való ONNX kapcsolatos további információkért tekintse meg a [ml-modellek létrehozása és felgyorsítása](concept-onnx.md) című cikket.

### <a name="use-models"></a>Modellek használata

A betanított gépi tanulási modellek a felhőben vagy helyileg webszolgáltatásként vannak üzembe helyezve. A modelleket Azure IoT Edge-eszközökre is üzembe helyezheti. A központi telepítések CPU-, GPU-vagy mező-programozható Gate-tömböket (FPGA) használnak a következtetésekhez. Power BI modelleket is használhat.

Ha webszolgáltatásként vagy IoT Edge-eszközként használ egy modellt, akkor meg kell adnia a következő elemeket:

* A szolgáltatásnak/eszköznek beküldött adatértékek kiértékeléséhez használt modell (ek).
* Egy bejegyzésparancsfájl. Ez a szkript fogadja a kérelmeket, a modell (ek) et használja az adatpontszámok kiértékelésére és a válasz visszaküldésére.
* Egy Azure Machine Learning környezet, amely leírja a modell (ek) és a bejegyzési parancsfájl által igényelt pip-és Conda-függőségeket.
* A modell (ek) és a bejegyzési parancsfájl által igényelt további eszközök, például szöveg, adatok stb.

Meg kell adnia az üzembe helyezés célplatformjának konfigurációját is. Például a virtuálisgép-család típusa, a rendelkezésre álló memória és a magok száma az Azure Kubernetes szolgáltatásban való üzembe helyezéskor.

A rendszerkép létrehozását követően a rendszer hozzáadja az Azure Machine Learning által igényelt összetevőket is. Ilyenek például a webszolgáltatás futtatásához és az IoT Edge-dzsel való interakciókhoz szükséges eszközök.

#### <a name="batch-scoring"></a>Batch-pontozás
A Batch pontozása ML-folyamatok esetében támogatott. További információ: batch- [előrejelzések Big Data](./tutorial-pipeline-batch-scoring-classification.md).

#### <a name="real-time-web-services"></a>Valós idejű webszolgáltatások

A **webszolgáltatások** modelljei a következő számítási célokkal használhatók:

* Azure Container Instance
* Azure Kubernetes Service
* Helyi fejlesztési környezet

A modell webszolgáltatásként való üzembe helyezéséhez a következő elemeket kell megadnia:

* A modellek modellje vagy együttese.
* A modell használatához szükséges függőségek. Például egy olyan parancsfájl, amely fogadja a kérelmeket, és meghívja a modellt, a Conda függőségeit stb.
* Telepítési konfiguráció, amely leírja, hogyan és hol kell telepíteni a modellt.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Vezérelt bevezetés

Az Azure Kubernetes Service-ben való üzembe helyezéskor a következő helyzetekben engedélyezheti a szabályozott bevezetést:

* Végpont több verziójának létrehozása egy központi telepítéshez
* Végezzen A/B tesztelést úgy, hogy a végpont különböző verzióira irányítja át a forgalmat.
* Váltás a végpontok verziói között úgy, hogy a végpont-konfigurációban frissíti a forgalom százalékos arányát.

További információ: ML- [modellek vezérelt bevezetése](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Eszközök IoT Edge

A modelleket IoT eszközökkel **Azure IoT Edge modulokon** keresztül is használhatja. IoT Edge modulok üzembe helyezése egy hardvereszközön történik, amely lehetővé teszi a következtetések kiértékelését vagy a modellek pontozását az eszközön.

További információ: [modellek üzembe helyezése](how-to-deploy-and-where.md).

### <a name="analytics"></a>Elemzés

A Microsoft Power BI támogatja a gépi tanulási modellek használatát az adatelemzéshez. További információ: [Azure Machine learning Integration in Power bi (előzetes verzió)](/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Rögzítse a végpontok közötti ML életciklus rögzítéséhez szükséges irányítási adatmennyiséget

Az Azure ML lehetővé teszi az összes ML-eszköz teljes körű naplózásának nyomon követését metaadatok használatával.

- Az Azure ML a git szolgáltatással [integrálva](how-to-set-up-training-targets.md#gitintegration) követheti azokat az információkat, amelyeken a tárház/ág/a kód véglegesítve származik.
- Az [Azure ml-adatkészletek](how-to-create-register-datasets.md) segítenek a nyomon követésben, a profilban és a verziószámozásban.
- A [tolmácsolás](how-to-machine-learning-interpretability.md) lehetővé teszi a modellek magyarázatát, a szabályozásoknak való megfelelést, valamint azt, hogy a modellek hogyan érkeznek meg az adott input miatt.
- Az Azure ML-futtatási előzményei a modellek betanításához használt kód, adatok és számítások pillanatképét tárolják.
- Az Azure ML-modell beállításjegyzéke rögzíti a modellhez társított összes metaadatot (amely kitanítja azt, hogy hol van üzembe helyezve, ha a központi telepítése kifogástalan).
- Az Azure-nal való [integráció](how-to-use-event-grid.md) lehetővé teszi, hogy az ml-életcikluson belüli eseményeket cselekedjen. Például a modell regisztrációjának, üzembe helyezésének, adateltolódásának és képzésének (futtatásának) eseményei.

> [!TIP]
> Míg a modellekkel és adatkészletekkel kapcsolatos néhány információ automatikusan rögzítve van, a __címkék__ használatával további információkat is hozzáadhat. Ha regisztrált modelleket és adatkészleteket keres a munkaterületen, használhat címkéket szűrőként.
>
> Az adatkészlet regisztrált modellel való társítása egy választható lépés. Az adatkészletek modelljének regisztrálásakor való hivatkozással kapcsolatos információkért tekintse meg a [modell](/python/api/azureml-core/azureml.core.model%28class%29) osztályának referenciáját.


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>A ML-életciklus eseményeinek értesítése, automatizálása és riasztása
Az Azure ML kulcsfontosságú eseményeket tesz közzé az Azure EventGrid, amely a ML-életciklus eseményeinek értesítésére és automatizálására használható. További információkért tekintse meg [ezt a dokumentumot](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Az Operational & ML-vel kapcsolatos problémák monitorozása

A figyelés lehetővé teszi, hogy megtudja, milyen adatok kerülnek a modellbe, valamint a visszaadott előrejelzések.

Ez az információ segít megérteni a modell felhasználásának módját. A begyűjtött bemeneti adatok is hasznosak lehetnek a modell jövőbeli verzióinak betanításához.

További információ: [a Model-adatok gyűjtésének engedélyezése](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>A modell újratanítása új adatértékekre

Gyakran érdemes ellenőrizni a modellt, frissíteni vagy akár teljesen újra betanítani az előzményekből, mivel új információkhoz jut. Időnként az új adatfogadás a tartomány várt részét képezi. Más időpontokban, ahogy azt az [adathalmazok észlelése (előzetes verzió) az adatkészletek](how-to-monitor-datasets.md)esetében is tárgyalja, a modell teljesítménye csökkentheti az adott érzékelő, a természetes adatváltozások, például az idényjellegű hatások vagy a más funkciókhoz képest változó funkciók változásait. 

Nincs univerzális válasz arra, hogy "Hogyan tudni, ha újra kell tanítani?" Az Azure ML-es esemény-és figyelési eszközei azonban az automatizáláshoz megfelelő kiindulási pontokat mutatnak. Ha úgy döntött, hogy újratanítást végez, a következőket kell tennie: 

- Az adatai előfeldolgozása ismételhető, automatizált folyamat használatával
- Az új modell betanítása
- Hasonlítsa össze az új modell kimeneteit a régi modellel
- Előre definiált feltételek használata annak kiválasztásához, hogy a régi modellt kell-e cserélni 

A fenti lépések témája, hogy az átképzésnek automatizáltnak kell lennie, nem ad hoc. [Azure Machine learning folyamatok](concept-ml-pipelines.md) jó választ adnak az adatelőkészítéssel, a képzéssel, az ellenőrzéssel és az üzembe helyezéssel kapcsolatos munkafolyamatok létrehozásához. Olvassa el a [modellek újratanítása Azure Machine learning Designer](how-to-retrain-designer.md) segítségével című témakört, amelyből megtudhatja, hogyan illeszkedik a folyamatok és a Azure Machine learning Designer egy átképzési forgatókönyvbe 

## <a name="automate-the-ml-lifecycle"></a>A ML életciklus automatizálása 

A GitHub és az Azure-folyamatok segítségével folyamatos integrációs folyamatot hozhat létre, amely egy modellt hajt. Egy tipikus forgatókönyv esetén, amikor egy adattudós ellenőrzi a projekt git-tárházában bekövetkező változást, az Azure-folyamat elkezdi a képzés futtatását. A Futtatás eredményei megtekinthetők a betanított modell teljesítmény-jellemzőinek megtekintéséhez. Létrehozhat egy olyan folyamatot is, amely webszolgáltatásként telepíti a modellt.

A [Azure Machine learning bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) megkönnyíti az Azure-folyamatokkal való munkát. A következő fejlesztéseket biztosítja az Azure-folyamatok számára:

* Engedélyezi a munkaterület kijelölését a szolgáltatás kapcsolatának meghatározásakor.
* Lehetővé teszi, hogy a kiadási folyamatokat a betanítási folyamat során létrehozott betanított modellek aktiválja.

Az Azure-folyamatok Azure Machine Learning használatával történő használatáról az alábbi hivatkozásokon talál további információt:

* [ML modellek folyamatos integrációja és üzembe helyezése az Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine learning MLOps](https://aka.ms/mlops) adattár.
* [Azure Machine learning MLOpsPython](https://github.com/Microsoft/MLOpspython) adattár.

A Azure Data Factory használatával olyan adatfeldolgozási folyamat is létrehozható, amely előkészíti az adatgyűjtést az oktatással való használatra. További információ: [adatfeldolgozási folyamat](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Következő lépések

További információ: a következő erőforrások olvasása és feltárása:

+ [Hogyan & a modellek üzembe helyezését](how-to-deploy-and-where.md) Azure Machine learning

+ [Oktatóanyag: lemezkép-besorolási modell üzembe helyezése az ACI-ban](tutorial-deploy-models-with-aml.md).

+ [A végpontok közötti MLOps-példák tárháza](https://github.com/microsoft/MLOps)

+ [CI/CD/ML modellek Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning)

+ [Üzembe helyezett modellt](how-to-consume-web-service.md) használó ügyfelek létrehozása

+ [Gépi tanulás nagy léptékben](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Azure AI-referenciák architektúrái & ajánlott eljárások rep](https://github.com/microsoft/AI)