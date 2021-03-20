---
title: 'Példa: egyéni képesség létrehozása és üzembe helyezése Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Ez a példa azt mutatja be, hogyan használható a Azure Machine Learning az Azure Cognitive Search AI-bővítési folyamatának egyéni képességeinek kiépítéséhez és üzembe helyezéséhez.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745634"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Példa: egyéni képesség létrehozása és üzembe helyezése Azure Machine Learning 

Ebben a példában a [Hotel Reviews adatkészletet](https://www.kaggle.com/datafiniti/hotel-reviews) fogja használni (amelyet a Creative Commons Licence [CC-NC-SA 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) használ, hogy [Egyéni képességeket](./cognitive-search-aml-skill.md) hozzon létre a Azure Machine learning használatával, hogy kinyerje a véleményekből származó Aspect-alapú véleményét. Ez lehetővé teszi a pozitív és negatív érzelmek hozzárendelését ugyanabban a felülvizsgálatban, hogy helyesen legyenek megjelölve az azonosított entitásokhoz, például a személyzethez, a helyiséghez, a lobbyhoz vagy a készlethez.

A Azure Machine Learning aspektus-alapú hangulati modell betanításához az [NLP receptek tárházat](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa)fogja használni. Ezután a modell egy Azure Kubernetes-fürtön végpontként lesz üzembe helyezve. Az üzembe helyezést követően a végpontot a Cognitive Search szolgáltatás általi használatra vonatkozó pénzmosás-képességként adja hozzá a dúsítási folyamathoz.

Két adatkészlet van megadva. Ha saját maga szeretné betanítani a modellt, a hotel_reviews_1000.csv fájlra van szükség. Szeretné kihagyni a betanítási lépést? Töltse le a hotel_reviews_100.csv.

> [!div class="checklist"]
> * Azure Cognitive Search-példány létrehozása
> * Azure Machine Learning munkaterület létrehozása (a keresési szolgáltatásnak és a munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia)
> * Modell betanítása és üzembe helyezése Azure Kubernetes-fürtön
> * AI-dúsítási folyamat összekapcsolása az üzembe helyezett modellel
> * Az üzembe helyezett modell kimenetének beolvasása egyéni képességként

> [!IMPORTANT] 
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jelenleg nincs .NET SDK-támogatás.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes előfizetés](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)beszerzése.
* [Cognitive Search szolgáltatás](./search-get-started-arm.md)
* [Erőforrás Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure Storage-fiók](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning-munkaterület](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Beállítás

* A [minta tárház](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)tartalmának klónozása vagy letöltése.
* Tartalom kinyerése, ha a letöltés zip-fájl. Győződjön meg arról, hogy a fájlok írhatók és írhatók.
* Az Azure-fiókok és-szolgáltatások beállítása közben másolja a neveket és a kulcsokat egy könnyen elérhető szövegfájlba. A rendszer hozzáadja a neveket és a kulcsokat a jegyzetfüzet első cellájához, ahol a változók az Azure-szolgáltatások eléréséhez vannak meghatározva.
* Ha nem ismeri a Azure Machine Learning és a hozzá tartozó követelményeket, érdemes áttekintenie ezeket a dokumentumokat az első lépések előtt:
 * [Fejlesztési környezet konfigurálása Azure Machine Learninghoz](../machine-learning/how-to-configure-environment.md)
 * [Azure Machine Learning munkaterületek létrehozása és kezelése a Azure Portal](../machine-learning/how-to-manage-workspace.md)
 * A Azure Machine Learning fejlesztési környezetének konfigurálásakor érdemes lehet a [felhőalapú számítási példányt](../machine-learning/how-to-configure-environment.md#compute-instance) használni a gyors és egyszerű használat érdekében.
* Töltse fel az adatkészlet-fájlt egy tárolóba a Storage-fiókban. A nagyobb fájlra akkor van szükség, ha szeretné végrehajtani a betanítási lépést a jegyzetfüzetben. Ha szeretné kihagyni a betanítási lépést, a kisebb fájl használata javasolt.

## <a name="open-notebook-and-connect-to-azure-services"></a>Jegyzetfüzet megnyitása és kapcsolódás az Azure-szolgáltatásokhoz

1. Helyezze el az összes szükséges információt azokhoz a változókhoz, amelyek engedélyezik az Azure-szolgáltatásokhoz való hozzáférést az első cellán belül, és futtatják a cellát.
1. A második cella futtatása megerősíti, hogy csatlakozott a keresési szolgáltatáshoz az előfizetéséhez.
1. A 1,1 – 1,5. szakaszban a Search szolgáltatás adattárát, készségkészlet, indexét és indexét fogja létrehozni.

Ezen a ponton kihagyhatja a betanítási adatkészlet létrehozásához szükséges lépéseket, és kísérletezhet a Azure Machine Learningban, és közvetlenül is kihagyhatja a GitHub-tárház models (modellek) mappájában megadott két modell regisztrálását. Ha kihagyja ezeket a lépéseket, a jegyzetfüzetben a 3,5 szakaszra ugorjon, és írjon pontozási szkriptet. Ekkor időt takaríthat meg; az adatok letöltése és a feltöltés lépései akár 30 percet is igénybe vehetnek.

## <a name="creating-and-training-the-models"></a>Modellek létrehozása és betanítása

A 2. szakasz hat cellát tartalmaz, amelyek letöltik a kesztyű beágyazási fájlját az NLP receptek tárházból. A letöltés után a rendszer feltölti a fájlt a Azure Machine Learning adattárba. A. zip fájl körülbelül 2G, és eltarthat egy ideig a feladatok végrehajtásához. A feltöltés után a rendszer kinyeri az betanítási adatgyűjtést, és most már készen áll a 3. szakaszra való áttérésre.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>A aspektuson alapuló hangulati modell betanítása és a végpont üzembe helyezése

A jegyzetfüzet 3. szakasza a 2. szakaszban létrehozott modelleket fogja betanítani, regisztrálja ezeket a modelleket, és az Azure Kubernetes-fürtben végpontként telepíti őket. Ha nem ismeri az Azure Kubernetes, javasoljuk, hogy tekintse át a következő cikkeket, mielőtt megpróbál létrehozni egy következtetési fürtöt:

* [Az Azure Kubernetes szolgáltatás áttekintése](../aks/intro-kubernetes.md)
* [Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai](../aks/concepts-clusters-workloads.md)
* [Kvóták, virtuális gépek méretére vonatkozó korlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (ak)](../aks/quotas-skus-regions.md)

A következtetési fürt létrehozása és üzembe helyezése akár 30 percet is igénybe vehet. A webszolgáltatás tesztelése a végső lépésekre való áttérés előtt, a készségkészlet frissítése és az indexelő futtatása előtt ajánlott.

## <a name="update-the-skillset"></a>A készségkészlet frissítése

A jegyzetfüzet 4. szakasza négy olyan cellával rendelkezik, amelyek frissítik a készségkészlet és az indexelő. Azt is megteheti, hogy a portál használatával kijelöli és alkalmazza az új képességet a készségkészlet, majd a keresési szolgáltatás frissítéséhez futtatja az indexelő.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

A portálon lépjen a Készségkészlet elemre, és válassza ki a Készségkészlet-definíció (JSON) hivatkozást. A portál megjeleníti a készségkészlet tartozó JSON-t, amely a jegyzetfüzet első cellájában lett létrehozva. A megjelenítéstől jobbra legördülő menü látható, ahol kiválaszthatja a szakértelem-definíciós sablont. Válassza ki a Azure Machine Learning (pénzmosás) sablont. adja meg az Azure ML-munkaterület nevét és a végpontot, amelyet a rendszer a következtetési fürtön üzembe helyezett modellhez használt. A sablon a végpont URI-ja és kulcsa alapján lesz frissítve.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Készségkészlet-definíciós sablon":::

Másolja a készségkészlet sablont az ablakból, és illessze be a készségkészlet-definícióba a bal oldalon. Szerkessze a sablont, hogy a hiányzó értékeket adja meg a következőhöz:

* Név
* Leírás
* Környezet
* "bemenetek" neve és forrása
* "outputs" neve és targetName

Mentse a készségkészlet.

A készségkészlet mentése után lépjen az indexelő elemre, és válassza ki az indexelő definíció (JSON) hivatkozást. A portál megjeleníti a jegyzetfüzet első cellájában létrehozott indexelő JSON-t. A kimeneti mezők leképezéseit további mező-hozzárendelésekkel kell frissíteni, hogy az indexelő képes legyen megfelelően kezelni és továbbítani azokat. Mentse a módosításokat, majd válassza a Futtatás lehetőséget. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az egyéni ügyességi webes API áttekintése](./cognitive-search-custom-skill-web-api.md) 
>  [További információ az egyéni szaktudás hozzáadásáról a dúsítási folyamathoz](./cognitive-search-custom-skill-interface.md)