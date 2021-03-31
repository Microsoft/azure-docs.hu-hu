---
title: Jegyzetfüzetek használata az Azure Sentinel használatával a biztonsági vadászathoz
description: Ez a cikk bemutatja, hogyan használhatók a jegyzetfüzetek az Azure Sentinel vadászati képességeivel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092193"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Biztonsági fenyegetések keresése Jupyter-notebook segítségével

Az Azure Sentinel alapja az adattár; egyesíti a nagy teljesítményű lekérdezéseket, a dinamikus sémákat és a nagy adatmennyiségeket. A Azure Portal és az összes Azure Sentinel-eszköz közös API-t használ az adattár eléréséhez. Ugyanez az API a külső eszközök, például a [Jupyter](https://jupyter.org/) notebookok és a Python számára is elérhető. Habár számos gyakori feladat elvégezhető a portálon, a Jupyter kiterjeszti a hatókörét, hogy mit tehet az adatokkal. A teljes programozást a tárak hatalmas gyűjteményével ötvözi a gépi tanuláshoz, a vizualizációhoz és az adatelemzéshez. Ezek az attribútumok meggyőző eszközt biztosítanak a biztonsági vizsgálathoz és a vadászathoz Jupyter.

![Példa jegyzetfüzetre](./media/notebooks/sentinel-notebooks-map.png)

Integráltuk a Jupyter-élményt a Azure Portalba, így könnyedén hozhat létre és futtathat jegyzetfüzeteket az adatai elemzéséhez. A *Kqlmagic* -kódtár lehetővé teszi az Azure Sentinel-lekérdezések lekérdezését, és közvetlenül egy jegyzetfüzetben történő futtatását. A lekérdezések a [Kusto lekérdezési nyelvét](https://kusto.azurewebsites.net/docs/kusto/query/index.html)használják. Több, a Microsoft által készített biztonsági elemző által fejlesztett jegyzetfüzet is be van csomagolva az Azure Sentinel szolgáltatással. Ezek a jegyzetfüzetek egy adott forgatókönyvhöz készültek, és használhatók a-ként is. Mások mintaként szolgálnak a saját jegyzetfüzetekben való használatra másolható vagy alkalmazkodó technikák és funkciók szemléltetésére. Más jegyzetfüzetek is importálhatók az Azure Sentinel Community GitHubról.

Az integrált Jupyter-élmény a notebookok tárolásához, megosztásához és végrehajtásához [Azure Notebooks](https://notebooks.azure.com/) használ. Ezeket a jegyzetfüzeteket helyileg is futtathatja, ha Python-környezettel és Jupyter rendelkezik a számítógépen, illetve más JupterHub-környezetekben, például Azure Databricks.

A jegyzetfüzetek két összetevővel rendelkeznek:

- A böngészőalapú felület, ahol lekérdezéseket és kódokat írhat be és futtathat, valamint a végrehajtás eredményét.
- A kód elemzéséhez és végrehajtásához felelős *kernel* .

Az Azure Sentinel notebook kernele egy Azure-beli virtuális gépen (VM) fut. Több licencelési lehetőség is létezik a nagyobb teljesítményű virtuális gépek kihasználása érdekében, ha a notebookok összetett gépi tanulási modelleket tartalmaznak.

Az Azure Sentinel notebookok számos népszerű Python-kódtárat használnak, mint például a pandák, a matplotlib, a bokeh és mások. Számos más Python-csomag közül választhat, amelyek többek között a következő területekre terjednek ki:

- Vizualizációk és grafikák
- Adatfeldolgozás és-elemzés
- Statisztikák és numerikus számítástechnika
- Gépi tanulás és mély tanulás

A [msticpy](https://github.com/Microsoft/msticpy/)nevű csomagban is megjelent néhány nyílt forráskódú Jupyter biztonsági eszköz. Ezt a csomagot számos mellékelt jegyzetfüzetben használják. A Msticpy Tools kifejezetten segítséget nyújt a jegyzetfüzetek létrehozásához a vadászathoz és a nyomozáshoz, és aktívan dolgozunk az új szolgáltatásokon és újdonságokon.

Az [Azure Sentinel Community GitHub-tárház](https://github.com/Azure/Azure-Sentinel) a Microsoft által készített vagy a Közösségtől származó jövőbeli Azure Sentinel-jegyzetfüzetek helye.

A jegyzetfüzetek használatához először létre kell hoznia egy Azure Machine Learning (ML) munkaterületet.

## <a name="create-an-azure-ml-workspace"></a>Azure ML-munkaterület létrehozása

1. A Azure Portal navigáljon az **Azure Sentinel**  >  **Threat Management**  >  **notebookokhoz** , majd válassza a **Jegyzetfüzet indítása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Jegyzetfüzet elindítása az Azure ml-munkaterület elindításához](./media/notebooks/sentinel-notebooks-launch.png)

1. A **AzureML munkaterület** területen válassza az **új létrehozása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Munkaterület létrehozása](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. A **Machine learning** lapon adja meg a következő adatokat, majd válassza a **felülvizsgálat + létrehozás** elemet.

    |Mező|Leírás|
    |--|--|
    |Előfizetés|Válassza ki a használni kívánt Azure-előfizetést.|
    |Erőforráscsoport|Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **AzureMLRG**-t használjuk.|
    |Munkaterület neve|Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **testworkspace1**-t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését.|
    |Region|Válassza ki a felhasználókhoz legközelebb eső helyet, valamint az adatforrásokat a munkaterület létrehozásához.|
    |Munkaterület kiadása|Ebben a példában válassza az **alapszintű** lehetőséget a munkaterület típusaként. A munkaterület típusa (alapszintű & Enterprise) meghatározza azokat a szolgáltatásokat, amelyekhez hozzáférésre és díjszabásra van szükség.|

    > [!div class="mx-imgBorder"]
    > ![munkaterület részleteinek megadása](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Tekintse át az adatokat, ellenőrizze, hogy helyes-e, majd válassza a **Létrehozás** lehetőséget a munkaterület üzembe helyezésének elindításához.

    > [!div class="mx-imgBorder"]
    > ![munkaterület részleteinek áttekintése](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Több percet is igénybe vehet, hogy a felhőben hozza létre a munkaterületet, amikor az **Áttekintés** oldalon az aktuális telepítési állapot látható.

    > [!div class="mx-imgBorder"]
    > ![munkaterület üzembe helyezése](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

A telepítés befejezése után elindíthat jegyzetfüzeteket az új Azure ML-munkaterületen.

> [!div class="mx-imgBorder"]
> ![a munkaterület telepítése sikerült](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Jegyzetfüzet elindítása az Azure ML-munkaterületének használatával

1. A Azure Portal navigáljon az **Azure Sentinel**  >  **Threat Management**  >  **noteszgépekhez**, ahol láthatja az Azure Sentinel által biztosított jegyzetfüzeteket.

    > [!TIP]
    > Válassza az **útmutatók & visszajelzés** lehetőséget, hogy a panel megnyitásához további Súgó és útmutatást nyújtson a jegyzetfüzetekhez.
    > ![Jegyzetfüzet-útmutatók megtekintése](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Egyéni jegyzetfüzetek kiválasztásával megtekintheti a leírásokat, a szükséges adattípusokat és az adatforrásokat.

    > [!div class="mx-imgBorder"]
    > ![Jegyzetfüzet adatainak megtekintése](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Válassza ki a használni kívánt jegyzetfüzetet, majd válassza a **Jegyzetfüzet indítása** lehetőséget a notebook klónozásához és a jegyzetfüzet új Azure Notebooks projekthez való konfigurálásához, amely az Azure Sentinel-munkaterülethez csatlakozik. Ha a folyamat befejeződött, a jegyzetfüzet a futtatásához Azure Notebooks belül nyílik meg.

    > [!div class="mx-imgBorder"]
    > ![Jegyzetfüzet kiválasztása](./media/notebooks/sentinel-azure-notebooks-select.png)

1. A AzureML munkaterületen válassza ki az Azure ML-munkaterületet, majd válassza az **Indítás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Jegyzetfüzet indítása](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Válasszon ki egy számítási példányt. Ha nem rendelkezik számítási példánnyal, tegye a következőket:
    1. Az **új számítási példány** varázsló elindításához válassza a pluszjelet (+).

        > [!div class="mx-imgBorder"]
        > ![számítási példány varázsló indítása](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Az **új számítási példány** lapon adja meg a szükséges adatokat, majd válassza a **Létrehozás** lehetőséget.

        > [!div class="mx-imgBorder"]
        > ![számítási példány létrehozása](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. A jegyzetfüzet-kiszolgáló létrehozása után minden cellán belül válassza a Futtatás ikont a kód végrehajtásához a jegyzetfüzetekben.

    > [!div class="mx-imgBorder"]
    > ![Jegyzetfüzet futtatása](./media/notebooks/sentinel-azure-notebooks-run.png)

Javaslatok

- Az Azure Sentinel-beli adatlekérdezések gyors bevezetéséhez tekintse meg a [első lépések Azure ml-jegyzetfüzetekkel és Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) -útmutatóval.

- A [**minta-jegyzetfüzetek GitHub-**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) almappájában további minta-jegyzetfüzeteket talál. Ezek a minta-jegyzetfüzetek adatokat mentettek, így könnyebben megtekinthető a kívánt kimenet. Javasoljuk, hogy tekintse meg ezeket a jegyzetfüzeteket a [nbviewer](https://nbviewer.jupyter.org/)-ben.

- A [**howtos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) GitHub almappát tartalmaz, amely leírja a jegyzetfüzeteket, például: a Python alapértelmezett verziójának beállítása, DSVM konfigurálása, Azure Sentinel-könyvjelzők létrehozása jegyzetfüzetből és más témákból.

A megadott jegyzetfüzetek a saját jegyzetfüzetek fejlesztéséhez használható hasznos eszközökként, valamint illusztrációként és mintakódként is szolgálnak.

Szívesen fogadjuk a visszajelzéseket, legyen szó a javaslatokról, a funkciókról, a közreműködő jegyzetfüzetekről, a hibajelentésekről, valamint a meglévő jegyzetfüzetek fejlesztéséről és kiegészítéseiről. Lépjen az [Azure Sentinel Community githubra](https://github.com/Azure/Azure-Sentinel) , és hozzon létre egy problémát vagy elágazást, és töltsön fel egy hozzájárulást.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan kezdheti el a Jupyter Notebook használatát az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [A könyvjelzők használatával érdekes információkat menthet a vadászat során](bookmarks.md)
