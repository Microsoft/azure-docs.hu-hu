---
title: 'Oktatóanyag: Címkézési projekt létrehozása képosztályozáshoz'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kezelheti a képek címkézésének folyamatát, hogy többosztályos képbesorolási modellekben is használhatók legyen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 41e93584937ca10740e9ee0be3353d1edf5efb3e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587680"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Oktatóanyag: Címkézési projekt létrehozása többosztályos képosztályozáshoz 


Ez az oktatóanyag bemutatja, hogyan kezelheti a gépi tanulási modellek adatokatként használni képes címkézési (más néven címkézési) lemezképeket. Az adatcímkék Azure Machine Learning nyilvános előzetes verzióban érhetőek el.

Ha gépi tanulási modellt szeretne betaníteni képek osztályozására, akkor több száz vagy akár több ezer, helyesen címkével jelölt képre van szüksége.  Azure Machine Learning segítségével kezelheti a tartományszakértőkből áll privát csapat előrehaladását az adatok címkézése során.
 
Ebben az oktatóanyagban macskákat és kutyákat bemutató képeket fog használni.  Mivel minden kép egy macska vagy egy kutya, ez egy *többosztályos* címkézési projekt. A következőket fogja megtanulni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Storage-fiókot, és töltsön fel képeket a fiókba.
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Többosztályos képcímkéző projekt létrehozása.
> * Címkézze fel az adatokat.  Ezt a feladatot Ön vagy a címkézői hajthatja végre.
> * A projekt befejezéséhez tekintse át és exportálja az adatokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

A Azure Machine Learning-munkaterület egy alaperőforrás a felhőben, amely gépi tanulási modellek kísérletezésre, betanítra és üzembe helyezésére használható. Az Azure-előfizetést és -erőforráscsoportot a szolgáltatás egy könnyen felhasznált objektumához használja.

A [munkaterületek számos módon létrehozhatóak.](how-to-manage-workspace.md) Ebben az oktatóanyagban létrehoz egy munkaterületet a Azure Portal használatával, amely egy webalapú konzol az Azure-erőforrások kezeléséhez.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Címkézési projekt létrehozása

A következő lépés az adatcímkéző projekt kezelése a Azure Machine Learning stúdió-ban, amely egy olyan összevont felület, amely gépi tanulási eszközöket tartalmaz adattudományi forgatókönyvek végrehajtásához az adattudományi szakemberek számára minden készségszinten. A studio nem támogatott a Internet Explorer böngészőkben.

1. Jelentkezzen be a [Azure Machine Learning stúdió.](https://ml.azure.com)

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Adattár létrehozása

Azure Machine Learning adattárolók a kapcsolati adatok, például az előfizetés-azonosító és a jogkivonat-engedélyezés tárolására használhatók. Itt egy adattárat használ az oktatóanyag lemezképét tartalmazó tárfiókhoz való csatlakozáshoz.

1. A munkaterület bal oldalán válassza az **Adattárak lehetőséget.**

1. Válassza **az + Új adattár lehetőséget.**

1. Töltse ki az űrlapot az alábbi beállításokkal:

    Mező|Leírás 
    ---|---
    Adattár neve | Nevezze el az adattárat.  Itt a **labeling_tutorial.**
    Adattár típusa | Válassza ki a tároló típusát.  Itt a **Azure Blob Storage** előnyben részesített tárolót használjuk.
    Fiókválasztási módszer | Válassza az **Enter manuálisan lehetőséget.**
    URL-cím | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Hitelesítéstípus | Válassza az **SAS-jogkivonat lehetőséget.**
    Fiókkulcs | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Az **adattár létrehozásához** válassza a Létrehozás lehetőséget.

### <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

Most, hogy hozzáfér a címkézni kívánt adatokhoz, hozza létre a címkézési projektet.

1. Az oldal tetején válassza a Projektek **lehetőséget.**

1. Válassza **a + Projekt hozzáadása lehetőséget.**

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Projekt létrehozása":::

### <a name="project-details"></a>Projekt részletei

1. A Project details (Projekt részletei) **űrlapon használja a következő** bemenetet:

    Mező|Leírás 
    ---|---
    Projektnév | Nevezze el a projektet.  A következőt **használjuk: tutorial-cats-n-dog.**
    Címkézési feladat típusa | Válassza **a Képbesorolás többosztályos lehetőséget.**  
    
    A **projekt létrehozásának** folytatásához válassza a Tovább lehetőséget.

### <a name="select-or-create-a-dataset"></a>Adatkészlet kiválasztása vagy létrehozása

1.   Az **Adatkészlet kiválasztása vagy** létrehozása űrlapon válassza ki **a** második, Adatkészlet létrehozása lehetőséget, majd válassza az **Adattárból hivatkozást.**

1. Használja a következő bemenetet az Adatkészlet létrehozása **az adattárból űrlaphoz:**

    1. Az **Alapszintű információ űrlapon** adjon meg egy nevet. Itt az **images-for-tutorial** nevet fogjuk használni.  Ha szeretné, adjon meg egy leírást.  Ezután kattintson a **Tovább** gombra.
    1. Az **Adattár kiválasztási űrlapon** válassza a **Korábban** létrehozott adattár lehetőséget, majd kattintson az adattár nevére, és válassza az **Adattár kiválasztása lehetőséget.**
    1. A következő lapon ellenőrizze, hogy az aktuálisan kiválasztott adattár helyes-e. Ha nem, válassza a **Korábban létrehozott adattár lehetőséget,** és ismételje meg az előző lépést.
    1. Ezután továbbra is az Adattár kiválasztási **űrlapon** válassza a **Tallózás,** majd a **MultiClass - DogCats lehetőséget.**  Válassza **a Mentés lehetőséget** a **/MultiClass - DogCats elérési** útként való használathoz.
    1. Kattintson **a Tovább gombra** a részletek megerősítéséhez, majd a **Létrehozás** gombra az adatkészlet létrehozásához.
    1. Válassza ki az adatkészlet neve melletti kört a listában, például **images-for-tutorial.**

1. A **projekt létrehozásának** folytatásához válassza a Tovább lehetőséget.

### <a name="incremental-refresh"></a>Növekményes frissítés

Ha új képeket szeretne hozzáadni az adatkészlethez, a növekményes frissítés megkeresi ezeket az új képeket, és hozzáadja őket a projekthez.  Ha engedélyezi ezt a funkciót, a projekt rendszeresen ellenőrzi az új rendszerképeket.  Ebben az oktatóanyagban nem fog új rendszerképeket hozzáadni az adattárhoz, ezért hagyja bejelölve ezt a funkciót.

A folytatáshoz válassza a **Tovább** gombot.

### <a name="label-classes"></a>Címkeosztályok

1. A **Label classes (Címkeosztályok) űrlapon** írjon be egy címkenevet, majd válassza a **+Add label (+Címke** hozzáadása) lehetőséget a következő címke begépelésekor.  Ebben a projektben a címkék **a Cat**, **a Dog** és a **Nem biztos.**

1. Ha **az összes** címkét hozzáadta, válassza a Tovább lehetőséget.

### <a name="labeling-instructions"></a>Címkézési utasítások

1. A **Címkézési utasítások űrlapon** meg lehet adni egy webhelyre mutató hivatkozást, amely részletes útmutatást nyújt a címkézők számára.  Ebben az oktatóanyagban ezt üresen hagyjuk.

1. A feladat rövid leírását közvetlenül az űrlapon is meg lehet adni.  Type **Labeling tutorial - Cats & Dog.**

1. Kattintson a **Tovább** gombra.

1. A **Gépi tanulási támogatás címkézése szakaszban** hagyja bejelölve a jelölőnégyzetet. A gépi tanulási személyes címkézéshez több adatra van szükség, mint amennyit ebben az oktatóanyagban használni fog.

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Ez az oldal nem frissül automatikusan. Szüneteltetés után frissítse manuálisan az oldalt, amíg a projekt állapota Created (Létrehozva) **nem lesz.**

## <a name="start-labeling"></a>Címkézés kezdése

Most már beállította az Azure-erőforrásokat, és konfigurált egy adatcímkéző projektet. Itt az ideje, hogy címkéket adjon az adatokhoz.

### <a name="tag-the-images"></a>Képek címkézése

Az oktatóanyagnak ebben a részében a  projekt rendszergazdájától egy címkéző szerepköreit fogja *átkapcsolni.*  Bárki, aki közreműködői hozzáféréssel rendelkezik a munkaterülethez, címkézővé válhat.

1. A [Machine Learning a](https://ml.azure.com)bal oldalon az **Adatcímkék** lehetőséget választva keresse meg a projektet.  

1. A **projekthez válassza a** Címke hivatkozást.

1. Olvassa el az utasításokat, majd válassza a **Feladatok lehetőséget.**

1. Válasszon ki egy miniatűrt a jobb oldalon, hogy megjelenítse az egy ugrás alatt megcímkézni kívánt képek számát. A továbblépni előtt fel kell címkéznie ezeket a rendszerképeket. Csak akkor váltson elrendezést, ha a címkézetlen adatok friss oldala van. Az elrendezésváltás törli az oldal folyamatban lévő címkézési munkáját.

1. Jelöljön ki egy vagy több képet, majd válasszon ki egy címkét, amely a kijelölésre vonatkozik. A címke a kép alatt jelenik meg.  Folytassa az oldalon található összes kép kiválasztását és címkézését.  Az összes megjelenített kép egyidejű kiválasztásához válassza az **Összes kijelölése lehetőséget.** Jelöljön ki legalább egy képet a címke alkalmazáshoz.


    > [!TIP]
    > Az első kilenc címkét a billentyűzet számbillentyűivel választhatja ki.

1. Miután az oldalon található összes kép fel van címkézve, válassza a **Küldés** gombot a címkék elküld küld.

    ![Képek címkézése](media/tutorial-labeling/catsndogs.gif)

1. Miután beküldi a címkéket az adott adatokhoz, az Azure frissíti az oldalt a munkasorból származó új képekkel.

## <a name="complete-the-project"></a>A projekt befejezése

Most vissza fogja váltani a szerepköröket a projekt projekt-rendszergazdájához *a* címkézési projekthez.

Vezetőként előfordulhat, hogy át szeretné vizsgálni a címkéző munkáját.  

### <a name="review-labeled-data"></a>Címkével jelölt adatok áttekintése

1. A [Machine Learning a bal](https://ml.azure.com)oldalon található **Adatcímkék** lehetőséget választva keresse meg a projektet.  

1. Válassza a projektnév hivatkozást.

1. Az irányítópult megjeleníti a projekt előrehaladását.

1. Az oldal tetején válassza az Adatok **lehetőséget.**

1. A címkézett képeknek a bal oldalon válassza a **Címkézett** adatok lehetőséget.  

1. Ha nem ért egyet egy címkével,  válassza ki a képet, majd válassza az Elutasítás lehetőséget az oldal alján.  A címkék el lesznek távolítva, és a kép vissza kerül a címkézetlen képek várólistájára.

### <a name="export-labeled-data"></a>Címkével jelölt adatok exportálása

A címkeadatokat bármikor exportálhatja Machine Learning kísérletezéshez. A felhasználók gyakran többször exportálnak és betanítják a különböző modelleket ahelyett, hogy megvárják az összes kép címkézését.

A képcímkék [COCO](http://cocodataset.org/#format-data) formátumban vagy Azure Machine Learning exportálhatók. Az adatkészlet formátuma megkönnyíti a betanítás használatát a Azure Machine Learning.  

1. A [Machine Learning a bal](https://ml.azure.com)oldalon található **Adatcímkék** lehetőséget választva keresse meg a projektet.  

1. Válassza a projektnév hivatkozást.

1. Válassza **az Exportálás,** majd az **Exportálás Azure ML-adatkészletként lehetőséget.** 

    Az exportálás állapota az Exportálás gomb alatt **jelenik** meg. 

1. A címkék sikeres exportálása után  a bal oldalon válassza az Adatkészletek lehetőséget az eredmények megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gépi tanulási képfelismerési modell betanítása.](/azure/machine-learning/how-to-use-labeled-dataset)

