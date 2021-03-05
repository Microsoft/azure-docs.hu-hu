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
ms.openlocfilehash: fe6fa5c9b05afd5479818fe9bb25e471665cb867
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171466"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Jupyter-jegyzetfüzetek futtatása a munkaterületen

Megtudhatja, hogyan futtathat Jupyter-jegyzetfüzeteket közvetlenül a munkaterületen a Azure Machine Learning Studióban. A [Jupyter](https://jupyter.org/) vagy a [JupyterLab](https://jupyterlab.readthedocs.io)elindításához lehetősége van a jegyzetfüzetek szerkesztésére és futtatására is a munkaterület elhagyása nélkül.

A fájlok, például a jegyzetfüzetek létrehozásával és kezelésével kapcsolatos információkért lásd: [fájlok létrehozása és kezelése a munkaterületen](how-to-manage-files.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Jegyzetfüzet szerkesztése

A jegyzetfüzetek szerkesztéséhez nyissa meg a munkaterület **felhasználói fájlok** szakaszában található bármelyik jegyzetfüzetet. Kattintson a szerkeszteni kívánt cellára.  Ha nincsenek jegyzetfüzetek ebben a szakaszban, tekintse [meg a fájlok létrehozása és kezelése a munkaterületen](how-to-manage-files.md)című szakaszt.

A jegyzetfüzetet a számítási példányhoz való csatlakozás nélkül is szerkesztheti.  Ha a jegyzetfüzetben szeretné futtatni a cellákat, válasszon ki vagy hozzon létre egy számítási példányt.  Ha leállított számítási példányt választ, a rendszer automatikusan elindítja az első cella futtatásakor.

Ha egy számítási példány fut, akkor a kód befejezését is használhatja az [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)használatával, bármely Python-jegyzetfüzetben.

A Jupyter vagy a JupyterLab a jegyzetfüzet eszköztáráról is elindíthatja.  A Azure Machine Learning nem biztosít frissítéseket, és nem javítja ki a Jupyter vagy a JupyterLab hibáit, mivel a Microsoft ügyfélszolgálata határán kívül nyílt forráskódú termékek.

## <a name="focus-mode"></a>Fókusz mód

Az aktuális nézet kibontásához használja a fókusz módot, így az aktív lapfülekre koncentrálhat. A fókusz mód elrejti a jegyzetfüzetek fájlkezelőjét.

1. A terminál ablak eszköztárán válassza a **fókusz mód** lehetőséget a fókusz üzemmód bekapcsolásához. Az ablak szélessége függően ez az eszköztár. **..** menüpontjában lehet.
1. Fókusz módban a normál **nézet** lehetőségre kattintva térjen vissza a normál nézetbe.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Fókusz mód/normál nézet váltása":::

## <a name="use-intellisense"></a>IntelliSense használata

Az [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) egy kód-kiegészítési támogatás, amely számos funkciót tartalmaz: tagok listázása, a paraméterek adatai, a gyors információ és a teljes szó. Ezek a funkciók segítenek többet megtudni a használt kódokról, nyomon követhetik a beírt paramétereket, és hívásokat adhatnak hozzá a tulajdonságokhoz és metódusokhoz, csak néhány billentyűleütéssel.  

A kód beírásakor használja a CTRL + SZÓKÖZ billentyűkombinációt az IntelliSense aktiválásához.

## <a name="clean-your-notebook-preview"></a>A jegyzetfüzet tisztítása (előzetes verzió)

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

## <a name="save-and-checkpoint-a-notebook"></a>Jegyzetfüzet mentése és ellenőrzőpont

A Azure Machine Learning egy *ipynb* -fájl létrehozásakor létrehoz egy ellenőrzőpont-fájlt.

A jegyzetfüzet eszköztárán válassza ki a menüt, majd a **fájl &gt; mentése és ellenőrzőpont** elemet a jegyzetfüzet manuális mentéséhez, és a rendszer hozzáadja a jegyzetfüzethez tartozó ellenőrzőpont-fájlt.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Képernyőkép a Mentés eszközről a jegyzetfüzet eszköztárán":::

Minden jegyzetfüzetet 30 másodpercenként kell megtakarítani. Az automatikus mentés szolgáltatás csak a kezdeti *ipynb* -fájlt frissíti, nem az ellenőrzőpont-fájlt.
 
Válassza az **ellenőrzőpontok** lehetőséget a notebook menüben egy elnevezett ellenőrzőpont létrehozásához és a jegyzetfüzet egy mentett ellenőrzőpontra való visszavonásához.

## <a name="export-a-notebook"></a>Jegyzetfüzet exportálása

A jegyzetfüzet eszköztárán válassza ki a menüt, majd **exportálja** a (z) parancsot a következő támogatott típusok bármelyikének használatával:

* Jegyzetfüzet
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Jegyzetfüzet exportálása a számítógépre":::

A rendszer menti az exportált fájlt a számítógépen.

## <a name="run-a-notebook-or-python-script"></a>Jegyzetfüzet vagy Python-szkript futtatása

Jegyzetfüzet vagy Python-szkript futtatásához először egy futó [számítási példányhoz](concept-compute-instance.md)kell csatlakoznia.

* Ha nem rendelkezik számítási példánnyal, az alábbi lépésekkel hozhat létre egyet:

    1. A jegyzetfüzet vagy parancsfájl eszköztáron a számítási legördülő menüben kattintson az **+ új számítás** lehetőségre. A képernyő méretétől függően előfordulhat, hogy az egy **..** . menüben található.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Új számítási számítás létrehozása":::
    1. Nevezze el a számítást, és válassza ki a **virtuális gép méretét**. 
    1. Válassza a **Létrehozás** lehetőséget.
    1. A számítási példány automatikusan csatlakozik a fájlhoz.  Most már futtathatja a jegyzetfüzet-cellákat vagy a Python-szkriptet a számítási példány bal oldalán található eszköz használatával.

* Ha leállította a számítási példányt, válassza a számítási legördülő lista jobb oldalán lévő  **számítás indítása** lehetőséget. A képernyő méretétől függően előfordulhat, hogy az egy **..** . menüben található.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Számítási példány indítása":::

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

## <a name="add-new-kernels"></a>Új kernelek hozzáadása

[A terminál használatával ](how-to-access-terminal.md#add-new-kernels) létrehozhat és hozzáadhat új kerneleket a számítási példányhoz. A jegyzetfüzet automatikusan megkeresi a csatlakoztatott számítási példányra telepített összes Jupyter-kernelt.

A jobb oldali kernel legördülő menüben váltson a telepített kernelek bármelyikére.  


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

## <a name="useful-keyboard-shortcuts"></a>Hasznos billentyűparancsok
A Jupyter-jegyzetfüzetekhez hasonlóan a Azure Machine Learning Studio notebookok modális felhasználói felülettel rendelkeznek. A billentyűzet különböző műveleteket végez, attól függően, hogy a jegyzetfüzet-cella milyen módban van. Azure Machine Learning Studio jegyzetfüzetek az alábbi két módot támogatják egy adott kódú cellához: parancs mód és szerkesztési mód.

### <a name="command-mode-shortcuts"></a>Parancs mód parancsikonjai

A cella parancs módban van, ha nincs beírni kívánt szöveg. Ha egy cella parancs módban van, a jegyzetfüzetet teljes egészében szerkesztheti, de nem lehet egyéni cellákba írni. A parancs mód megadásához nyomja le `ESC` vagy az egér használatával válassza ki a cella szerkesztő területén kívüli lehetőséget.  Az aktív cella bal oldali szegélye kék és tömör, a **Futtatás** gombja pedig kék.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Jegyzetfüzet-cella parancs módban ":::

| Helyi                      | Leírás                          |
| ----------------------------- | ------------------------------------|
| Enter                         | A szerkesztési mód megnyitása             |        
| Shift + Enter                 | Cella futtatása, válassza az alábbi lehetőséget         |     
| Vezérlő/parancs + ENTER       | Cella futtatása                            |
| ALT + ENTER                   | Cella futtatása, kód cellájának beszúrása lent    |
| Vezérlő/parancs + ALT + ENTER | Cella futtatása, Markdown-cella beszúrása alul|
| ALT + R                       | Az összes futtatása      |                       
| Y                             | Cella konvertálása kódra    |                         
| M                             | Cella konvertálása Markdown  |                       
| Fel/K                          | Cella kijelölése    |               
| Lefelé/J                        | Válassza ki az alábbi cellát    |               
| A                             | A fenti kód cellájának beszúrása  |            
| B                             | Az alábbi kód cellájának beszúrása   |           
| Control/Command + Shift + A   | A fenti Markdown cella beszúrása    |      
| Control/Command + Shift + B   | Az alábbi Markdown-cella beszúrása   |       
| X                             | Kijelölt cella kivágása    |               
| C                             | Kijelölt cella másolása   |               
| Shift + V                     | A fent kiválasztott cella beillesztése           |
| V                             | Lenti kijelölt cella beillesztése    |       
| D D                           | Kijelölt cella törlése|                
| O                             | Kimenet váltása         |              
| SHIFT + O                     | Kimenet görgetésének váltása   |          
| ÉN                           | Kernel megszakítása |                   
| 0 0                           | Kernel újraindítása |                     
| Shift + szóköz                 | Görgetés felfelé  |                         
| Space (Szóköz)                         | Görgetés lefelé|
| Tab                           | Fókusz módosítása a következő fókuszálható elemre (ha a TAB trap le van tiltva)|
| Vezérlő/parancs + S           | Jegyzetfüzet mentése |                      
| 1                             | Módosítás H1-re|                       
| 2                             | Váltás a H2-re|                        
| 3                             | Váltás H3-re|                        
| 4                             | Váltás H4-re |                       
| 5                             | Váltás a H5-re |                       
| 6                             | Váltás a H6 |                       

### <a name="edit-mode-shortcuts"></a>Szerkesztési mód parancsikonjai

A szerkesztési módot egy szöveges kurzor jelzi, amely arra kéri, hogy írja be a szerkesztőt. Ha egy cella szerkesztési módban van, beírhatja a cellába. Adja meg a szerkesztési módot úgy, hogy lenyomja `Enter` vagy felhasználja az egeret a cella szerkesztői területein való kiválasztáshoz. Az aktív cella bal oldali szegélyének zöld színűnek kell lennie, és a Futtatás gombja zöld színnel van **elindítva** . A kurzor rákérdez a cellában szerkesztési módban is látható.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Jegyzetfüzet-cella szerkesztési módban":::

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat Azure Machine Learning jegyzetfüzetekben lévő kódokat szerkesztési módban.

| Helyi                      | Leírás|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Adja meg a parancs módját|  
| Vezérlő/parancs + szóköz       | IntelliSense aktiválása |
| Shift + Enter                 | Cella futtatása, válassza az alábbi lehetőséget |                         
| Vezérlő/parancs + ENTER       | Cella futtatása  |                                      
| ALT + ENTER                   | Cella futtatása, kód cellájának beszúrása lent  |              
| Vezérlő/parancs + ALT + ENTER | Cella futtatása, Markdown-cella beszúrása alul  |          
| ALT + R                       | Az összes cella futtatása     |                              
| Fel                            | Kurzor feljebb vagy az előző cellában    |             
| Le                          | Kurzor mozgatása lefelé vagy a következő cellába |                  
| Vezérlő/parancs + S           | Jegyzetfüzet mentése   |                                
| Vezérlés/Command + fel          | Ugrás a cella elejére   |                             
| Vezérlő/parancs + lefelé        | Ugrás a cella végére |                                 
| Tab                           | Kód befejezése vagy behúzása (ha engedélyezve van a TAB trap) |
| Vezérlő/parancs + M           | Tab-trap engedélyezése/letiltása  |                       
| Control/Command +]           | Behúzás |                                         
| Control/Command + [           | Kihúzás  |                                        
| Control/Command + A           | Az összes kijelölése|                                      
| Vezérlő/parancs + Z           | Visszavonás |                                           
| Control/Command + Shift + Z   | Ismétlés |                                           
| Control/Command + Y           | Ismétlés |                                           
| Control/Command + Home        | Ugrás a cella elejére|                                
| Vezérlő/parancs + vége         | Ugrás a cella végére   |                               
| Vezérlő/parancs + balra        | Ugrás egy szóval balra |                               
| Control/Command + Right       | Egy szó jobbra |                              
| Control/Command + Backspace   | A Word törlése előtt |                             
| Vezérlő/parancs + törlés      | Szó törlése a következő után |                              
| Control/Command +/           | Megjegyzés bekapcsolása a cellában

## <a name="troubleshooting"></a>Hibaelhárítás

* Ha nem tud jegyzetfüzethez csatlakozni, győződjön meg arról, hogy a webes szoftvercsatorna- **kommunikáció nincs letiltva** . A számítási példányok Jupyter működéséhez engedélyezni kell a webes szoftvercsatorna-kommunikációt. Győződjön meg arról, hogy a hálózat engedélyezi a WebSocket-kapcsolatokat a *. instances.azureml.net és a *. instances.azureml.ms. 

* Ha a számítási példány egy privát kapcsolati munkaterületen van üzembe helyezve, akkor csak a [virtuális hálózaton belülről lehet hozzáférni](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance). Ha egyéni DNS-vagy gazdagép-fájlt használ, vegyen fel egy bejegyzést a <példány neve>. <region> . instances.azureml.ms a munkaterület privát végpontjának magánhálózati IP-címével. További információ az [Egyéni DNS-](./how-to-custom-dns.md?tabs=azure-cli) cikkben található.
    
## <a name="next-steps"></a>Következő lépések

* [Az első kísérlet futtatása](tutorial-1st-experiment-sdk-train.md)
* [A file Storage biztonsági mentése pillanatképekkel](../storage/files/storage-snapshots-files.md)
* [Biztonságos környezetek használata](./how-to-secure-training-vnet.md#compute-instance)
