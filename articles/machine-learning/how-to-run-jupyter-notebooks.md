---
title: Jupyter-jegyzetfüzetek futtatása a munkaterületen
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan futtathat Jupyter notebookot a munkaterület Azure Machine Learning Studióban való elhagyása nélkül.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 46e0687056d697afc2d4355bdf900af138273eaf
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061834"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Jupyter-jegyzetfüzetek futtatása a munkaterületen

Megtudhatja, hogyan futtathat Jupyter-jegyzetfüzeteket közvetlenül a munkaterületen a Azure Machine Learning Studióban. A [Jupyter](https://jupyter.org/) vagy a [JupyterLab](https://jupyterlab.readthedocs.io)elindításához lehetősége van a jegyzetfüzetek szerkesztésére és futtatására is a munkaterület elhagyása nélkül.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Jegyzetfüzetek létrehozása

A Azure Machine Learning munkaterületen hozzon létre egy új Jupyter-jegyzetfüzetet, és kezdjen el dolgozni. Az újonnan létrehozott jegyzetfüzetet az alapértelmezett munkaterület-tárolóban tárolja a rendszer. Ez a jegyzetfüzet a munkaterülethez hozzáféréssel rendelkező bárkivel megosztható. 

Új jegyzetfüzet létrehozása: 

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek** lehetőséget. 
1. Válassza az **új fájl létrehozása** ikont a **saját fájlok** szakaszban lévő **felhasználói fájlok** listájának megadásához.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Új fájl létrehozása":::

1. Nevezze el a fájlt. 
1. Jupyter notebook-fájlok esetén válassza a **Jegyzetfüzet** lehetőséget fájltípusként.
1. Válassza ki a kívánt könyvtárat.
1. Válassza a **Létrehozás** lehetőséget.

Szövegfájlokat is létrehozhat.  Válassza ki a kívánt **szöveget** , és adja hozzá a kiterjesztést a névhez (például myfile.py vagy myfile.txt)  

A notebookok oldal tetején található eszközökkel mappákat és fájlokat is feltölthet, beleértve a jegyzetfüzeteket is.  A jegyzetfüzetek és a legtöbb szöveges fájltípus az előnézet szakaszban látható.  A legtöbb fájltípushoz nem érhető el előzetes verzió.

> [!IMPORTANT]
> A jegyzetfüzetekben és parancsfájlokban lévő tartalmak beolvashatják a munkamenetek adatait, és az Azure-beli szervezet nélkül férhetnek hozzá az adatokhoz.  Csak megbízható forrásból származó fájlok betöltése. További információ: [biztonságos kód – ajánlott eljárások](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Minták klónozása

A munkaterület tartalmaz egy jegyzetfüzeteket tartalmazó **minta** mappát, amely segít az SDK megismerésében, és példaként szolgál a saját gépi tanulási projektjeihez.  Ezeket a jegyzetfüzeteket a munkaterület Storage-tárolójában lévő saját mappájába is klónozott.  

Példaként tekintse [meg az első ml-kísérlet létrehozását ismertető oktatóanyagot](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Fájlok használata a git és a saját fájlok verziójában

Az összes git-műveletet egy terminál-ablak használatával érheti el. A rendszer az összes git-fájlt és-mappát a munkaterület fájlrendszerében fogja tárolni.

> [!NOTE]
> A **~/cloudfiles/Code/Users** mappában bárhol hozzáadhatja fájljait és mappáit, így azok az összes Jupyter-környezetben láthatók lesznek.

A terminál elérése:

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek** lehetőséget.
1. Válassza ki a bal oldalon a **felhasználói fájlok** szakaszban található bármelyik jegyzetfüzetet.  Ha nincs jegyzetfüzete, először [hozzon létre egy jegyzetfüzetet](#create)
1. Válasszon ki egy **számítási** célt, vagy hozzon létre egy újat, és várjon, amíg fut.
1. Válassza a **nyitott terminál** ikont.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Terminál megnyitása":::

1. Ha nem látja az ikont, válassza a **...** lehetőséget a számítási cél jobb oldalán, majd válassza a **terminál megnyitása** lehetőséget.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Terminál megnyitása a következőből:...":::


További információ a [git-adattáraknak a munkaterület-fájlrendszerbe való klónozásáról](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Másolás és beillesztés a terminálon

> * Windows: `Ctrl-Insert` Másolás és használat `Ctrl-Shift-v` vagy `Shift-Insert` Beillesztés.
> * Mac OS: `Cmd-c` a másoláshoz és `Cmd-v` a beillesztéshez.
> * A FireFox/IE nem támogatja megfelelően a vágólap-engedélyeket.

### <a name="share-notebooks-and-other-files"></a>Jegyzetfüzetek és egyéb fájlok megosztása

Másolja és illessze be az URL-címet egy jegyzetfüzet vagy fájl megosztásához.  Ehhez az URL-címhez csak a munkaterület más felhasználói férhetnek hozzá.  További információ [a munkaterület hozzáférésének biztosításáról](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Jegyzetfüzet szerkesztése

A jegyzetfüzetek szerkesztéséhez nyissa meg a munkaterület **felhasználói fájlok** szakaszában található bármelyik jegyzetfüzetet. Kattintson a szerkeszteni kívánt cellára. 

A jegyzetfüzetet a számítási példányhoz való csatlakozás nélkül is szerkesztheti.  Ha a jegyzetfüzetben szeretné futtatni a cellákat, válasszon ki vagy hozzon létre egy számítási példányt.  Ha leállított számítási példányt választ, a rendszer automatikusan elindítja az első cella futtatásakor.

Ha egy számítási példány fut, akkor a kód befejezését is használhatja az [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)használatával, bármely Python-jegyzetfüzetben.

A Jupyter vagy a JupyterLab a jegyzetfüzet eszköztáráról is elindíthatja.  A Azure Machine Learning nem biztosít frissítéseket, és nem javítja ki a Jupyter vagy a JupyterLab hibáit, mivel a Microsoft ügyfélszolgálata határán kívül nyílt forráskódú termékek.

### <a name="focus-mode"></a>Fókusz mód

Az aktuális nézet kibontásához használja a fókusz módot, így az aktív lapfülekre koncentrálhat. A fókusz mód elrejti a jegyzetfüzetek fájlkezelőjét.

1. A terminál ablak eszköztárán válassza a **fókusz mód** lehetőséget a fókusz üzemmód bekapcsolásához. Az ablak szélessége függően ez az eszköztár. **..** menüpontjában lehet.
1. Fókusz módban a normál **nézet** lehetőségre kattintva térjen vissza a normál nézetbe.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Fókusz mód/normál nézet váltása":::


### <a name="use-intellisense"></a>IntelliSense használata

Az [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) egy kód-kiegészítési támogatás, amely számos funkciót tartalmaz: tagok listázása, a paraméterek adatai, a gyors információ és a teljes szó. Ezek a funkciók segítenek többet megtudni a használt kódokról, nyomon követhetik a beírt paramétereket, és hívásokat adhatnak hozzá a tulajdonságokhoz és metódusokhoz, csak néhány billentyűleütéssel.  

A kód beírásakor használja a CTRL + SZÓKÖZ billentyűkombinációt az IntelliSense aktiválásához.

### <a name="clean-your-notebook-preview"></a>A jegyzetfüzet tisztítása (előzetes verzió)

> [!IMPORTANT]
> A Gathering szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A jegyzetfüzetek létrehozása során általában az adatfeltáráshoz vagy a hibakereséshez használt cellákkal végződik. Az *adatgyűjtés* funkció segítségével tiszta jegyzetfüzetet hozhat létre a külső cellák nélkül.

1. Futtassa az összes jegyzetfüzet-cellát.
1. Válassza ki azt a cellát, amely tartalmazza az új jegyzetfüzet futtatásához használni kívánt kódot. Például egy kísérletet elküldő kód, vagy esetleg a modellt regisztráló kód.
1. Válassza a cella eszköztárán megjelenő **összegyűjtési** ikont.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Képernyőfelvétel: a gyűjtés ikon kiválasztása":::
1. Adja meg az új "összegyűjtött" jegyzetfüzet nevét.  

Az új jegyzetfüzet csak programkódot tartalmaz, és az összes cellának ugyanazokat az eredményeket kell megadnia, mint a begyűjtéshez kiválasztott cella.

### <a name="save-and-checkpoint-a-notebook"></a>Jegyzetfüzet mentése és ellenőrzőpont

A Azure Machine Learning egy *ipynb* -fájl létrehozásakor létrehoz egy ellenőrzőpont-fájlt.

A jegyzetfüzet eszköztárán válassza ki a menüt, majd a **fájl &gt; mentése és ellenőrzőpont** elemet a jegyzetfüzet manuális mentéséhez, és a rendszer hozzáadja a jegyzetfüzethez tartozó ellenőrzőpont-fájlt.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Képernyőkép a Mentés eszközről a jegyzetfüzet eszköztárán":::

Minden jegyzetfüzetet 30 másodpercenként kell megtakarítani. Az automatikus mentés szolgáltatás csak a kezdeti *ipynb* -fájlt frissíti, nem az ellenőrzőpont-fájlt.
 
Válassza az **ellenőrzőpontok** lehetőséget a notebook menüben egy elnevezett ellenőrzőpont létrehozásához és a jegyzetfüzet egy mentett ellenőrzőpontra való visszavonásához.

## <a name="delete-a-notebook"></a>Jegyzetfüzet törlése

A **mintákat** tartalmazó jegyzetfüzetek *nem* törölhetők.  Ezek a jegyzetfüzetek a stúdió részét képezik, és minden alkalommal frissülnek, amikor új SDK kerül közzétételre.  

A következő módokon *törölheti* a **felhasználói fájlok** jegyzetfüzeteit:

* A Studióban válassza a **...** elemet a mappa vagy fájl végén.  Győződjön meg arról, hogy támogatott böngészőt használ (Microsoft Edge, Chrome vagy Firefox).
* Bármely jegyzetfüzet eszköztárból válassza a [**terminál megnyitása**](#terminal)  lehetőséget a számítási példányhoz tartozó terminál ablak eléréséhez.
* A Jupyter vagy a JupyterLab eszközzel.

## <a name="run-a-notebook-or-python-script"></a>Jegyzetfüzet vagy Python-szkript futtatása

Jegyzetfüzet vagy Python-szkript futtatásához először egy futó [számítási példányhoz](concept-compute-instance.md)kell csatlakoznia. Ha nem rendelkezik számítási példánnyal, az alábbi lépésekkel hozhat létre egyet: 

1. Válassza **+** a jegyzetfüzet vagy a parancsfájl eszköztárát. 
2. Nevezze el a számítást, és válassza ki a **virtuális gép méretét**. 
3. Válassza a **Létrehozás** lehetőséget.
4. A számítási példány automatikusan csatlakozik a fájlhoz.  Most már futtathatja a jegyzetfüzet-cellákat vagy a Python-szkriptet a számítási példány bal oldalán található eszköz használatával.

Csak az Ön által létrehozott számítási példányok láthatók és használhatók.  A **felhasználói fájlokat** a rendszer külön tárolja a virtuális gépen, és a munkaterület összes számítási példánya között meg van osztva.

### <a name="view-logs-and-output"></a>Naplók és kimenet megtekintése

A Futtatás és a naplók előrehaladásának megtekintéséhez [Jegyzetfüzet-minialkalmazások](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) használhatók. A widgetek aszinkron módon frissülnek, és frissítéseket biztosítanak, amíg befejeződik a képzés. A Azure Machine Learning widgeteket a Jupyter és a JupterLab is támogatja.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Képernyőkép: Jupyter notebook widget ":::

## <a name="explore-variables-in-the-notebook"></a>A jegyzetfüzetben szereplő változók megismerése

A jegyzetfüzet eszköztárán a **változó Explorer** eszköz használatával jelenítse meg a jegyzetfüzetben létrehozott összes változó nevét, típusát, hosszát és mintájának értékét.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Képernyőfelvétel: változó Explorer eszköz":::

Válassza ki az eszközt a változó Explorer ablak megjelenítéséhez.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Képernyőfelvétel: változó Explorer ablak":::

## <a name="navigate-with-a-toc"></a>Navigáljon a TARTALOMJEGYZÉKbe

A jegyzetfüzet eszköztárán a tartalomjegyzék eszköz használatával jelenítheti meg vagy elrejtheti  **a tartalomjegyzéket** .  Kezdjen el egy fejlécet tartalmazó Markdown cellát a tartalomjegyzékbe való felvételhez. Kattintson egy bejegyzésre a táblázatban, és görgessen a cellához a jegyzetfüzetben.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Képernyőfelvétel: tartalomjegyzék a jegyzetfüzetben":::

## <a name="change-the-notebook-environment"></a>A notebook-környezet módosítása

A notebook eszköztár lehetővé teszi, hogy megváltoztassa a környezetet, amelyen a jegyzetfüzet fut.  

Ezek a műveletek nem változtatják meg a jegyzetfüzet állapotát vagy a jegyzetfüzetben lévő változók értékeit:

|Művelet  |Eredmény  |
|---------|---------| --------|
|A kernel leállítása     |  Leállítja a futó cellákat. A cella futtatása automatikusan újraindítja a rendszermagot. |
|Navigáljon egy másik munkaterület szakaszhoz     |     A futó cellák le vannak állítva. |

Ezek a műveletek alaphelyzetbe állítják a jegyzetfüzet állapotát, és a jegyzetfüzetben lévő összes változót visszaállítják.

|Művelet  |Eredmény  |
|---------|---------| --------|
| A kernel módosítása | A jegyzetfüzet új kernelt használ |
| Számítás váltása    |     A jegyzetfüzet automatikusan az új számítást használja. |
| Számítás visszaállítása | Újraindul, amikor egy cellát próbál futtatni |
| Számítás leállítása     |    Egyetlen cella sem fog futni  |
| Jegyzetfüzet megnyitása a Jupyter vagy a JupyterLab     |    A jegyzetfüzet új lapon lett megnyitva.  |

### <a name="add-new-kernels"></a>Új kernelek hozzáadása

A jegyzetfüzet automatikusan megkeresi a csatlakoztatott számítási példányra telepített összes Jupyter-kernelt.  Kernel hozzáadása a számítási példányhoz:

1. A jegyzetfüzet eszköztárán válassza a [**terminál megnyitása**](#terminal) lehetőséget.
1. Hozzon létre egy új környezetet a terminálablak használatával.  Az alábbi kód például a következőt hozza létre `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Aktiválja a környezetet.  Például a létrehozása után `newenv` :

    ```shell
    conda activate newenv
    ```
1. Telepítse a pip és a ipykernel csomagot az új környezetbe, és hozzon létre egy kernelt az adott Conda env számára

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```
1. A kernel telepítése után frissítse a lapot, és nyisson meg egy jegyzetfüzetet. Ekkor megjelenik az új kernel a kernel listában.

> [!NOTE]
> Jegyzetfüzeten belüli csomagkezelő esetén a **% pip** vagy **% Conda** Magic functions használatával automatikusan telepíthet csomagokat a **jelenleg futó kernelre**, nem pedig **! pip** vagy **! Conda** , amely az összes csomagra vonatkozik (beleértve a jelenleg futó kernelen kívüli csomagokat is)

A [rendelkezésre álló Jupyter-kernelek](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) bármelyike telepíthető.

### <a name="status-indicators"></a>Állapotjelzők

A **számítási** legördülő lista melletti kijelző megjeleníti az állapotát.  Az állapot a legördülő menüben is látható.  

|Szín |Számítási állapot |
|---------|---------| 
| Zöld | A számítás fut |
| Piros |A számítás nem sikerült | 
| Fekete | Számítás leállítva |
|  Világoskék |Számítás létrehozása, indítása, újraindítása, beállítás |
|  Szürke |Számítási törlés, Leállítás |

A **kernel** legördülő lista melletti kijelző megjeleníti az állapotát.

|Szín |Kernel állapota |
|---------|---------|
|  Zöld |Kernel csatlakoztatva, tétlen, foglalt|
|  Szürke |A kernel nincs csatlakoztatva |

## <a name="find-compute-details"></a>Számítási részletek keresése

A számítási példányok részleteit a [Studióban](https://ml.azure.com), a **számítási** oldalon találhatja meg.

## <a name="troubleshooting"></a>Hibaelhárítás

* Ha nem tud jegyzetfüzethez csatlakozni, győződjön meg arról, hogy a webes szoftvercsatorna- **kommunikáció nincs letiltva** . A számítási példányok Jupyter működéséhez engedélyezni kell a webes szoftvercsatorna-kommunikációt. Győződjön meg arról, hogy a hálózat engedélyezi a WebSocket-kapcsolatokat a *. instances.azureml.net és a *. instances.azureml.ms. 

* Ha a számítási példány egy privát kapcsolati munkaterületen van üzembe helyezve, akkor csak a virtuális hálózaton belülről lehet hozzáférni. Ha egyéni DNS-vagy gazdagép-fájlt használ, vegyen fel egy bejegyzést a <példány neve>. <region> . instances.azureml.ms a munkaterület privát végpontjának magánhálózati IP-címével. További információ az [Egyéni DNS-](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli) cikkben található.
    
## <a name="next-steps"></a>Következő lépések

* [Az első kísérlet futtatása](tutorial-1st-experiment-sdk-train.md)
* [A file Storage biztonsági mentése pillanatképekkel](../storage/files/storage-snapshots-files.md)
* [Biztonságos környezetek használata](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
