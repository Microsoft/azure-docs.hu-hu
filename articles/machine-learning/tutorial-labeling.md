---
title: 'Oktatóanyag: címkézési projekt létrehozása képbesoroláshoz'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kezelheti a képek címkézésének folyamatát, hogy azok felhasználhatók legyenek a többosztályos lemezkép besorolási modelljeiben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 3a86f0eb88ba0a56f0887d71f649cf9b9d5ec7a3
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227262"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Oktatóanyag: címkézési projekt létrehozása a többosztályos képek besorolásához 


Ebből az oktatóanyagból megtudhatja, hogyan kezelheti a gépi tanulási modellek készítéséhez használt, a címkézési (más néven címkézési) lemezképeket. Az adatfelirat Azure Machine Learning nyilvános előzetes verzióban érhető el.

Ha egy gépi tanulási modellt szeretne betanítani a képek besorolásához, több száz vagy akár több ezer, megfelelően címkézett lemezképre van szüksége.  A Azure Machine Learning segítségével kezelheti a tartományi szakértők privát csapatának előrehaladását az adataik címkézése során.
 
Ebben az oktatóanyagban a macskák és kutyák képeit fogja használni.  Mivel az egyes képek Cat vagy Dog, ez egy *többosztályos* címkézési projekt. A következőket fogja megtanulni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Storage-fiókot, és töltsön fel képeket a fiókba.
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Hozzon létre egy többosztályos Képcímkéző projektet.
> * Címkézze fel adatait.  Ezt a feladatot Ön vagy a címkéje is elvégezheti.
> * A projekt végrehajtásához tekintse át és exportálja az adatelemzést.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban.

[A munkaterület többféleképpen is létrehozható](how-to-manage-workspace.md). Ebben az oktatóanyagban egy munkaterületet hoz létre az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal használatával.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Címkézési projekt indítása

Ezután a Azure Machine Learning Studióban felügyeli az adatcímkéző projektet, amely egy összevont felület, amely tartalmazza a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek végezhetők el az összes képzettségi szint adatelemző szakemberek számára. A Studio nem támogatott az Internet Explorer böngészőben.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Adattár létrehozása

Azure Machine Learning adattárolók a kapcsolódási adatok tárolására szolgálnak, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel. Itt egy adattárral csatlakozhat ahhoz a Storage-fiókhoz, amely az oktatóanyaghoz tartozó lemezképeket tartalmazza.

1. A munkaterület bal **oldalán válassza az** adattárolók lehetőséget.

1. Válassza az **+ új adattár** lehetőséget.

1. Töltse ki az űrlapot a következő beállításokkal:

    Mező|Leírás 
    ---|---
    Adattár neve | Adja meg az adattár nevét.  Itt a **labeling_tutorial** használjuk.
    Adattár típusa | Válassza ki a tároló típusát.  Itt az **Azure Blob Storaget** használjuk, a lemezképek előnyben részesített tárhelyét.
    Fiók kiválasztása módszer | Válassza az **ENTER manuálisan** lehetőséget.
    URL-cím | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Hitelesítéstípus | Válasszon **sas-tokent**.
    Fiókkulcs | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Válassza a **Létrehozás** lehetőséget az adattár létrehozásához.

### <a name="create-a-labeling-project"></a>Címkézési projekt létrehozása

Most, hogy hozzáfér a címkével ellátni kívánt adataihoz, hozza létre a címkézési projektet.

1. A lap tetején válassza a **projektek** lehetőséget.

1. Válassza a **+ projekt hozzáadása** elemet.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Projekt létrehozása":::

### <a name="project-details"></a>Projekt részletei

1. Használja a következő inputot a **Project details** űrlaphoz:

    Mező|Leírás 
    ---|---
    Projektnév | Adjon nevet a projektnek.  Itt az **oktatóanyag-macskák-n-Dogs**-ket fogjuk használni.
    Feladattípus címkézése | Válassza a **Képbesorolás többosztályos** lehetőséget.  
    
    Kattintson a **tovább** gombra a projekt létrehozásának folytatásához.

### <a name="select-or-create-a-dataset"></a>Adatkészlet kiválasztása vagy létrehozása

1.   A **válasszon ki vagy hozzon létre egy adatkészletet** űrlapon válassza a második lehetőséget, **hozzon létre egy adatkészletet**, majd válassza ki a hivatkozást az **adattárból**.

1. Használja a következő inputot az **adatkészlet létrehozása adattárból** űrlapból:

    1. Az **alapszintű információ** űrlapon adjon meg egy nevet, itt a **képek-for-tutorial** használatát fogjuk használni.  Ha szeretné, adja meg a leírást.  Ezután kattintson a **Tovább** gombra.
    1. Az **adattár kiválasztása** űrlapon válassza a **korábban létrehozott adattár** lehetőséget, majd kattintson az adattár nevére, és válassza az **adattár kiválasztása** lehetőséget.
    1. A következő lapon ellenőrizze, hogy a jelenleg kijelölt adattár helyes-e. Ha nem, válassza a **korábban létrehozott adattár** lehetőséget, és ismételje meg az előző lépést.
    1. Ezután továbbra is az **adattár kiválasztása** űrlapon válassza a **Tallózás** lehetőséget, majd válassza a **többosztályos DogsCats** lehetőséget.  Válassza a **Mentés** lehetőséget a **/MultiClass-DogsCats** elérési útvonalként való használatához.
    1. A **tovább** gombra kattintva erősítse meg a részleteket, majd **hozzon** létre az adatkészlet létrehozásához.
    1. Válassza ki az adatkészlet neve melletti kört a listában, például **képek-for-tutorial**.

1. Kattintson a **tovább** gombra a projekt létrehozásának folytatásához.

### <a name="incremental-refresh"></a>Növekményes frissítés

Ha új lemezképeket szeretne hozzáadni az adatkészlethez, a Növekményes frissítés megkeresi ezeket az új képeket, és hozzáadja őket a projekthez.  Ha engedélyezi ezt a funkciót, a projekt rendszeresen ellenőrzi az új lemezképeket.  Ebben az oktatóanyagban nem fog új rendszerképeket hozzáadni az adattárhoz, ezért a funkció nincs bejelölve.

A folytatáshoz válassza a **Tovább** gombot.

### <a name="label-classes"></a>Címkézési osztályok

1. A **címkézési osztályok** űrlapon írja be a címke nevét, majd válassza a **+ címke hozzáadása** lehetőséget a következő címke beírásához.  Ebben a projektben a címkék a következők: **Cat**, **Dog** és **bizonytalan**.

1. Válassza a **tovább** lehetőséget, ha az összes címkét felvette.

### <a name="labeling-instructions"></a>Címkézési utasítások

1. A **címkézési utasítások** űrlapján megadhat egy olyan webhelyre mutató hivatkozást, amely részletes útmutatást nyújt a címkéi számára.  Ebben az oktatóanyagban üresen hagyjuk.

1. A feladat rövid leírását közvetlenül is hozzáadhatja az űrlapon.  Írja be a **címkézési oktatóanyagot – macskák & kutyák.**

1. Kattintson a **Tovább** gombra.

1. A **ml által támogatott címkézés** szakaszban ne jelölje be a jelölőnégyzetet. A ML által támogatott címkézés több olyan adattípust igényel, mint amit ebben az oktatóanyagban használ.

1. Válassza a **Create project** (Projekt létrehozása) lehetőséget.

Ez a lap nem frissül automatikusan. Egy szüneteltetés után manuálisan frissítse a lapot, amíg a projekt állapota **létre** nem változik.

## <a name="start-labeling"></a>Címkézés indítása

Most beállította az Azure-erőforrásokat, és konfigurált egy adatcímkéző projektet. Itt az ideje, hogy címkéket vegyen fel az adataiba.

### <a name="tag-the-images"></a>A képek címkézése

Az oktatóanyagnak ebben a részében a *projekt rendszergazdájától* származó szerepköröket egy *Labeler* kell váltania.  Bárki, aki közreműködői hozzáféréssel rendelkezik a munkaterülethez, Labeler válhat.

1. A [Machine learning Studióban](https://ml.azure.com)válassza a bal oldalon található **adatfelirat** lehetőséget a projekt megkereséséhez.  

1. Válassza ki a projekthez tartozó **címke hivatkozást** .

1. Olvassa el az utasításokat, majd válassza a **feladatok** lehetőséget.

1. Válasszon ki egy miniatűr képet a jobb oldalon, és jelenítse meg a felcímkézni kívánt képek számát egy ugrásban. Az összes lemezképet fel kell címkéznie, mielőtt továbblép. Csak akkor váltson át elrendezést, ha a címkézetlen adatlapok friss lapja van. Az elrendezések váltás törli az oldal folyamatban lévő címkézési feltételeit.

1. Válasszon ki egy vagy több rendszerképet, majd válasszon ki egy címkét, amelyet alkalmazni szeretne a kijelölésre. A címke a rendszerkép alatt jelenik meg.  Folytassa az összes rendszerkép kijelölését és címkézését az oldalon.  Az összes megjelenített kép egyidejű kiválasztásához válassza **az összes kijelölése** lehetőséget. Válasszon ki legalább egy képet a címke alkalmazásához.


    > [!TIP]
    > Az első kilenc címkét a billentyűzetén található számgombok használatával választhatja ki.

1. Ha a lapon lévő összes rendszerkép címkézve van, válassza a **Submit (Küldés** ) lehetőséget a címkék elküldéséhez.

    ![Képek címkézése](media/tutorial-labeling/catsndogs.gif)

1. Miután elküldte a címkéket az adatokhoz, az Azure frissíti a lapot egy új rendszerképekkel a munkahelyi sorból.

## <a name="complete-the-project"></a>A projekt befejezése

Most újra kell váltania a szerepköröket a *projekt rendszergazdája* számára a címkézési projekthez.

Vezetőként érdemes áttekinteni a Labeler munkáját.  

### <a name="review-labeled-data"></a>Címkézett adatértékek áttekintése

1. A [Machine learning Studióban](https://ml.azure.com)válassza a bal oldalon található **adatfelirat** lehetőséget a projekt megkereséséhez.  

1. Válassza ki a projekt neve hivatkozást.

1. Az irányítópulton a projekt előrehaladása látható.

1. Az oldal **tetején válassza az adatelemet**.

1. A címkézett képek megtekintéséhez a bal oldalon válassza a **címkézett adat** lehetőséget.  

1. Ha nem ért egyet a címkével, válassza ki a képet, majd válassza az **elutasítás** lehetőséget az oldal alján.  A címkék el lesznek távolítva, és a kép vissza lesz helyezve a címkézetlen képek várólistáján.

### <a name="export-labeled-data"></a>Címkézett adatértékek exportálása

A Machine Learning kísérletezéshez bármikor exportálhatja a címkézési adattípust. A felhasználók gyakran többször is exportálnak és különböző modelleket tanítanak, és nem kell megvárniuk, hogy minden kép címkével legyen ellátva.

A képfeliratokat [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure Machine learning adatkészletként lehet exportálni. Az adatkészlet formátuma megkönnyíti a Azure Machine Learning képzésének használatát.  

1. A [Machine learning Studióban](https://ml.azure.com)válassza a bal oldalon található **adatfelirat** lehetőséget a projekt megkereséséhez.  

1. Válassza ki a projekt neve hivatkozást.

1. Válassza az **Exportálás** lehetőséget, majd válassza **az Exportálás Azure ml-adatkészletként** lehetőséget. 

    Az Exportálás állapota közvetlenül az **Exportálás** gomb alatt jelenik meg. 

1. A címkék sikeres exportálása után a bal oldalon található **adatkészletek** elemre kattintva megtekintheti az eredményeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy adatcímkéző projektet, és exportálja a címkéket](how-to-create-labeling-projects.md).
