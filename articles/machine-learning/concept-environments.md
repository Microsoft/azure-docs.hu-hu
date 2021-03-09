---
title: Tudnivalók Azure Machine Learning környezetekről
titleSuffix: Azure Machine Learning
description: Ismerje meg a gépi tanulási környezeteket, amelyek lehetővé teszik a reprodukálható, auditálható, & hordozható gépi tanulási függőségi definíciókat a különböző számítási célokhoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508469"
---
# <a name="what-are-azure-machine-learning-environments"></a>Mik azok a Azure Machine Learning környezetek?

Azure Machine Learning környezetek a gépi tanulási képzést végző környezet beágyazását jelentik. Megadják a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. A futtatási időpontokat (Python, Spark vagy Docker) is megadják. A környezetek a Machine Learning munkaterületen belül felügyelt és verzióval rendelkező entitások, amelyek lehetővé teszik a reprodukálható, auditálható és hordozható gépi tanulási munkafolyamatok különböző számítási célokból való átirányítását.

A helyi számítási feladatokhoz a `Environment` következőket használhatja:
* Fejlessze a betanítási szkriptet.
* Használja ugyanazt a környezetet Azure Machine Learning számítási feladatokhoz a Modelles képzések esetében.
* Telepítse a modellt ugyanazzal a környezettel.
* Tekintse át azt a környezetet, amelyben a meglévő modellt betanítták.

Az alábbi ábra azt szemlélteti, hogyan használható egyetlen objektum a `Environment` futtatási konfigurációban (a betanításhoz) és a következtetési és telepítési konfigurációban (a webszolgáltatás központi telepítése esetén).

![A Machine learning-munkafolyamatban található környezet ábrája](./media/concept-environments/ml-environment.png)

A környezet, a számítási cél és a betanítási parancsfájl együttesen a futtatási konfigurációt alkotják: egy képzési Futtatás teljes specifikációja.

## <a name="types-of-environments"></a>A környezetek típusai

A környezetek széles körben három kategóriára oszthatók: a *kurátor*, a *felhasználó által felügyelt* és a *rendszer által felügyelt*.

A Azure Machine Learning által biztosított, és a munkaterületen alapértelmezés szerint elérhetővé tett környezetek. A szolgáltatásként való használatra szánt Python-csomagokat és-beállításokat tartalmazó gyűjteményeket tartalmaz, amelyek segítséget nyújtanak a különböző gépi tanulási keretrendszerek megkezdéséhez. Ezek az előre létrehozott környezetek is lehetővé teszik a gyorsabb üzembe helyezési időt. A teljes listát a [kurátori környezetek című cikkben](resource-curated-environments.md)találja.

A felhasználó által felügyelt környezetekben Ön felelős a környezet beállításához és minden olyan csomag telepítéséhez, amelyet a képzési parancsfájlnak szüksége van a számítási célra. A Conda nem vizsgálja a környezetet, vagy semmit sem telepít Önnek. Ha saját környezetét határozza meg, akkor a `azureml-defaults` verziót pip-függőségként kell listáznia `>= 1.0.45` . Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

A rendszer által felügyelt környezeteket akkor használja, ha azt szeretné, hogy a [Conda](https://conda.io/docs/) kezelje a Python-környezetet és a parancsfájlok függőségeit. Az új Conda-környezet a Conda-függőségek objektum alapján épül fel. A Azure Machine Learning szolgáltatás alapértelmezés szerint ezt a típusú környezetet feltételezi, mert a távoli számítási célok nem konfigurálhatók manuálisan.

## <a name="create-and-manage-environments"></a>Környezetek létrehozása és kezelése

Környezeteket a használatával hozhat létre:

* Új objektumok definiálása `Environment` egy kurátori környezet használatával vagy a saját függőségeinek definiálásával.
* Meglévő `Environment` objektumok használata a munkaterületről. Ez a megközelítés lehetővé teszi az egységességet és a reprodukálhatóságot a függőségekkel.
* Importálás meglévő anaconda-környezet definícióból.
* A Azure Machine Learning parancssori felület használata
* [A VS Code bővítmény használata](how-to-manage-resources-vscode.md#create-environment)

Adott kódok esetében tekintse meg a környezetek [használata](how-to-use-environments.md#create-an-environment)című témakör "környezet létrehozása" című szakaszát. A környezeteket a munkaterületen is könnyedén kezelheti. Ezek a következő funkciókat tartalmazzák:

* A környezetek automatikusan regisztrálva lesznek a munkaterületen, amikor elküld egy kísérletet. Manuálisan is regisztrálhatók.
* A munkaterületről beolvashat környezeteket a képzéshez vagy az üzembe helyezéshez, vagy szerkesztheti a környezet definícióját.
* A verziószámozással időben megtekintheti a környezetek változásait, ami biztosítja a reprodukálhatóságot.
* Docker-rendszerképeket automatikusan építhet ki a környezetből.

A kódok használatával kapcsolatban tekintse meg a [környezetek használatáról](how-to-use-environments.md#manage-environments)szóló témakör "környezetek kezelése" című szakaszát.

## <a name="environment-building-caching-and-reuse"></a>Környezetek kiépítése, gyorsítótárazása és újrafelhasználása

A Azure Machine Learning szolgáltatás környezeti definíciókat épít be a Docker-rendszerképekbe és a Conda környezetbe. Emellett gyorsítótárazza a környezeteket, hogy újra felhasználhatók legyenek a következő képzések futtatásakor és a szolgáltatás végpontjának telepítése során. A betanítási szkriptek távoli futtatása a Docker-rendszerkép létrehozását igényli, míg egy helyi Futtatás közvetlenül is használhat Conda-környezetet. 

### <a name="submitting-a-run-using-an-environment"></a>Futtatás elküldése környezet használatával

Amikor először küld el egy távoli futtatást egy környezettel, a Azure Machine Learning szolgáltatás egy [ACR-felépítési feladatot](../container-registry/container-registry-tasks-overview.md) hív meg a munkaterülethez társított Azure Container Registry (ACR) használatával. Ezután a rendszer gyorsítótárazza a beépített Docker-rendszerképet a munkaterület ACR-ben. A kurátori környezetek a globális ACR-ben gyorsítótárazott Docker-rendszerképekkel támogatottak. A Futtatás végrehajtásának megkezdése után a rendszer a képet a megfelelő ACR-ből olvassa be a számítási cél alapján.

Helyi futtatások esetén a rendszer egy Docker-vagy Conda-környezetet hoz létre a környezeti definíció alapján. A szkripteket ezután végrehajtja a cél számításon – helyi futtatókörnyezeti környezetben vagy helyi Docker-motoron.

### <a name="building-environments-as-docker-images"></a>Környezetek kiépítése Docker-rendszerképekként

Ha a környezet definíciója még nem létezik a munkaterület ACR-ben, a rendszer egy új rendszerképet fog felépíteni. A rendszerkép létrehozása két lépésből áll:

 1. Alaprendszerkép letöltése és a Docker-lépések végrehajtása
 2. Conda-környezet létrehozása a környezeti definícióban megadott Conda-függőségek alapján.

A második lépés kimarad, ha [felhasználó által felügyelt függőségeket](/python/api/azureml-core/azureml.core.environment.pythonsection)ad meg. Ebben az esetben Ön felelős a Python-csomagok telepítéséhez, az alaprendszerképbe való belefoglalásával, vagy az első lépésben az egyéni Docker-lépések megadásával. Emellett a Python végrehajtható fájl megfelelő helyének megadására is a felelős. [Egyéni Docker-alaprendszerkép](how-to-deploy-custom-docker-image.md)is használható.

### <a name="image-caching-and-reuse"></a>Képek gyorsítótárazása és újrafelhasználása

Ha ugyanezt a környezeti definíciót használja egy másik futtatáshoz, a Azure Machine Learning szolgáltatás újrahasználja a gyorsítótárazott rendszerképet a munkaterület ACR-ből. 

A gyorsítótárazott képek részleteinek megtekintéséhez használja [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) metódust.

Annak megállapításához, hogy fel kell-e használni a gyorsítótárazott képet, vagy újat kell létrehoznia, a szolgáltatás kiszámítja a környezeti definícióból származó [kivonatot](https://en.wikipedia.org/wiki/Hash_table) , és összehasonlítja a meglévő környezetek kivonatával. A kivonat a következőn alapul:
 
 * Kiinduló rendszerkép tulajdonságának értéke
 * Egyéni Docker-lépések tulajdonságának értéke
 * Python-csomagok listája a Conda-definícióban
 * A Spark-definícióban található csomagok listája 

A kivonat nem függ a környezet nevétől vagy verziójától – Ha átnevezi a környezetet, vagy új környezetet hoz létre egy meglévő tulajdonságok és csomagok alapján, akkor a kivonatoló érték változatlan marad. A környezeti definíció módosításait, például a Python-csomagok hozzáadását vagy eltávolítását, vagy a csomag verziójának módosítását, a kivonat értékének módosítását okozhatja. Egy adott környezetben a függőségek vagy a csatornák sorrendjének módosítása új környezetet eredményez, és így új rendszerkép-buildre van szükség. Fontos megjegyezni, hogy a kurátori környezet változásai érvénytelenítik a kivonatot, és egy új "nem kiszolgált" környezetet eredményeznek.

A számított kivonatoló értéket a munkaterület és a globális ACR (vagy a helyi futtatások számítási céljának) összehasonlítja. Ha egyezés van, akkor a gyorsítótárazott képet kihúzta a rendszer, ellenkező esetben a rendszerkép kiépítése aktiválódik. A gyorsítótárazott képek lekérésének időtartama magában foglalja a letöltési időt, míg az újonnan létrehozott rendszerképek lekérésének időtartama magában foglalja a fordítási időt és a letöltési időt is. 

A következő ábra három környezeti definíciót mutat be. Közülük kettő eltérő névvel és verzióval rendelkezik, de a rendszerkép és a Python-csomagok is azonosak. Azonban ugyanazzal a kivonattal rendelkeznek, és így ugyanahhoz a gyorsítótárazott képhez tartoznak. A harmadik környezet különböző Python-csomagokat és-verziókat tartalmaz, ezért egy másik gyorsítótárazott rendszerképnek felel meg.

![A környezeti gyorsítótárazás diagramja Docker-rendszerképekként](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Ha például olyan környezetet hoz létre, amely nem rögzített csomag-függőséggel rendelkezik, például a környezet a ```numpy``` _környezet létrehozásakor_ telepített csomag verziószámát fogja használni. Emellett a megfelelő definícióval rendelkező jövőbeli környezetek továbbra is a régi verziót használják. 

A csomag frissítéséhez meg kell adnia egy verziószámot a rendszerkép újraépítésének kényszerítéséhez, például: ```numpy==1.18.1``` . A rendszer telepíti az új függőségeket, beleértve a beágyazottkat is, amelyek megszakítják a korábban működő forgatókönyvet. 

> [!WARNING]
>  A [környezet. a Build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) metódus újraépíti a gyorsítótárazott rendszerképet, amely a nem rögzített csomagok frissítésének lehetséges mellékhatása, valamint az adott gyorsítótárazott rendszerképnek megfelelő összes környezeti definíció esetén a reprodukálhatóság megszakítása.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és használhat környezeteket](how-to-use-environments.md) a Azure Machine Learningban.
* A [környezeti osztályhoz](/python/api/azureml-core/azureml.core.environment%28class%29)tartozó Python SDK dokumentációjában talál.
* Tekintse meg az R SDK dokumentációját a [környezetekhez](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).