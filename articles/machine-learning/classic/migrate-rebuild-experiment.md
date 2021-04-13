---
title: 'ML Studio (klasszikus): Migrálás Azure Machine Learningre – Újraépítés kísérlet'
description: Építse újra a Studio (klasszikus) kísérleteket Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 35a68d1a2883d0631f90cd8009e272bca0c66278
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311838"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Studio (klasszikus) kísérlet újraépítése Azure Machine Learning

Ez a cikk azt ismerteti, hogyan lehet újraépíteni a Studio (klasszikus) kísérletet Azure Machine Learning. A studióból (klasszikus) való áttelepítéssel kapcsolatos további információkért tekintse meg [az áttelepítés áttekintése című cikket](migrate-overview.md).

A Studio (klasszikus) **kísérletek** hasonlóak a Azure Machine learning lévő **folyamatokhoz** . Azure Machine Learning folyamatokban azonban ugyanarra a háttérre épülnek, amely az SDK-t is felhasználja. Ez azt jelenti, hogy két lehetőség áll rendelkezésre a gépi tanulási fejlesztéshez: a drag-and-drop Designer vagy a Code-First SDK-k.

A folyamatok SDK-val történő létrehozásával kapcsolatos további információkért lásd: [Mik a Azure Machine learning folyamatok](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk).


## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy Azure Machine Learning-munkaterület. [Hozzon létre egy Azure Machine learning munkaterületet](../how-to-manage-workspace.md#create-a-workspace).
- Egy Studio (klasszikus) kísérlet az áttelepítésre.
- [Töltse fel az adatkészletet](migrate-register-dataset.md) Azure Machine Learningba.

## <a name="rebuild-the-pipeline"></a>A folyamat újraépítése

[Az adatkészlet Azure Machine Learningra való áttelepítését](migrate-register-dataset.md)követően készen áll a kísérlet újbóli létrehozására.

Azure Machine Learning a vizualizációs gráfot a **folyamat vázlatának** nevezzük. Ebben a szakaszban újra létrehozza a klasszikus kísérletet folyamat piszkozatként.

1. Ugrás a Azure Machine Learning studióra ([ml.Azure.com](https://ml.azure.com))
1. A bal oldali navigációs panelen válassza a **Designer** > **könnyen használható előre elkészített modulok** ![ képernyőképét, amely bemutatja, hogyan hozhat létre egy új folyamat piszkozatot.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Manuálisan építse újra a kísérletet Designer modulokkal.
    
    A helyettesítő modulok kereséséhez tekintse meg a [modul-leképezési táblázatot](migrate-overview.md#studio-classic-and-designer-module-mapping) . A Studióban (klasszikus) a legnépszerűbb modulok azonos verziójúak a Designerben.

    > [!Important]
    > Ha a kísérlet az R szkript végrehajtása modult használja, további lépéseket kell végrehajtania a kísérlet áttelepítéséhez. További információ: R- [parancsfájl-modulok áttelepítése](migrate-execute-r-script.md).

1. Állítsa be a paramétereket.
    
    Válassza ki az egyes modulokat, és állítsa be a paramétereket a modul beállításai panelen jobbra. Használja a paramétereket a Studio (klasszikus) kísérlet funkcióinak újbóli létrehozásához. Az egyes modulokkal kapcsolatos további információkért tekintse meg a [modul referenciáját](../algorithm-module-reference/module-reference.md).

## <a name="submit-a-run-and-check-results"></a>Futtatási és ellenőrzési eredmények elküldése

A Studio (klasszikus) kísérlet újbóli létrehozása után itt az ideje, hogy elküldje egy **folyamat futtatását**.

A folyamat futtatása a munkaterülethez csatolt **számítási célra** végezhető el. Beállíthat egy alapértelmezett számítási célt a teljes folyamathoz, vagy megadhat számítási célokat egy modul alapján.

Miután elküldte a futtatást egy folyamat-tervezetből, a **folyamat futására** vált. A rendszer rögzíti a folyamat minden egyes futtatását, és bejelentkezett Azure Machine Learningba.

Alapértelmezett számítási cél beállítása a teljes folyamathoz:
1. Válassza a **fogaskerék ikon** ![ fogaskerék ikont a tervezőben ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) a folyamat neve mellett.
1. Válassza a **számítási cél kiválasztása** lehetőséget.
1. Válasszon ki egy meglévő számítást, vagy hozzon létre egy új számítást a képernyőn megjelenő utasításokat követve.

Most, hogy beállította a számítási célt, elküldheti a folyamat futtatását:

1. A vászon tetején válassza a **Küldés** lehetőséget.
1. Új kísérlet létrehozásához válassza az **új létrehozása** lehetőséget.
    
    A hasonló folyamatokat alkotó kísérletek együtt futnak. Ha többször is futtat egy folyamatot, kiválaszthatja ugyanazt a kísérletet egymást követő futtatásokhoz. Ez a naplózás és a nyomon követés esetében hasznos.
1. Adja meg a kísérlet nevét. Ezután válassza a **Küldés** lehetőséget.

    Az első futtatás akár 20 percet is igénybe vehet. Mivel az alapértelmezett számítási beállításokhoz a csomópont minimális mérete 0, a tervezőnek az üresjárat után le kell foglalnia az erőforrásokat. Az egymást követő futtatások kevesebb időt vesznek igénybe, mivel a csomópontok már le vannak foglalva. A futási idő felgyorsításához létrehozhat egy legalább 1 vagy több csomópontot tartalmazó számítási erőforrásokat.

A Futtatás befejezése után megtekintheti az egyes modulok eredményeit:

1. Kattintson a jobb gombbal arra a modulra, amelynek kimenetét látni szeretné.
1. Válassza a **Megjelenítés**, a **kimenet megtekintése** vagy a **napló megtekintése** lehetőséget.

    - **Megjelenítés**: az eredmények adatkészletének előnézete.
    - **Kimenet megtekintése**: nyisson meg egy hivatkozást a kimeneti tároló helyére. Ezzel a paranccsal megismerheti vagy letöltheti a kimenetet. 
    - **Napló megtekintése**: illesztőprogram-és rendszernaplók megtekintése. A **70_driver_log** használatával megtekintheti a felhasználó által küldött parancsfájlokkal kapcsolatos információkat, például a hibákat és a kivételeket.

> [!IMPORTANT]
> A Designer modulok a Studio (klasszikus) C# csomagjaihoz képest nyílt forráskódú Python-csomagokat használnak. Ennek eredményeképpen a modul kimenete némileg eltérő lehet a Designer és a Studio (klasszikus) között. 


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan lehet újraépíteni a Studio (klasszikus) kísérletet Azure Machine Learning. A következő lépés a [webszolgáltatások újraépítése a Azure Machine Learningban](migrate-rebuild-web-service.md).


Tekintse meg a Studio (klasszikus) áttelepítési sorozat további cikkeit:

1. [Áttelepítési áttekintés](migrate-overview.md).
1. [Adatkészlet Átmigrálása](migrate-register-dataset.md).
1. **Építse újra a Studio (klasszikus) betanítási folyamatát**.
1. [Építse újra a Studio (klasszikus) webszolgáltatást](migrate-rebuild-web-service.md).
1. [Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával](migrate-rebuild-integrate-with-client-app.md).
1. [Telepítse át az R-szkriptet](migrate-execute-r-script.md).
