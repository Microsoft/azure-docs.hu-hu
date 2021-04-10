---
title: 'ML Studio (klasszikus): munkaterület létrehozása – Azure'
description: Azure Machine Learning Studio (klasszikus) használatához Machine Learning Studio (klasszikus) munkaterületre van szükség. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520544"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Machine Learning Studio (klasszikus) munkaterület létrehozása és megosztása

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klasszikus) használatához Machine Learning Studio (klasszikus) munkaterületre van szükség. Ez a munkaterület tartalmazza a kísérletek létrehozásához, kezeléséhez és közzétételéhez szükséges eszközöket.

## <a name="create-a-studio-classic-workspace"></a>Studio (klasszikus) munkaterület létrehozása

Ha Machine Learning Studio (klasszikus) munkaterületet szeretne megnyitni, be kell jelentkeznie a munkaterület létrehozásához használt Microsoft-fiókba, vagy meg kell kapnia egy meghívót a tulajdonostól, hogy csatlakozzon a munkaterülethez. A Azure Portal kezelheti a munkaterületet, amely a hozzáférés konfigurálását is lehetővé teszi.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

    > [!NOTE]
    > A bejelentkezéshez és a Studio (klasszikus) munkaterület létrehozásához Azure-előfizetés rendszergazdájának kell lennie. 
    >
    > 

2. Kattintson az **+ új** elemre

3. A keresőmezőbe írja be a **Machine learning Studio (klasszikus) munkaterületet** , és válassza ki a megfelelő elemet. Ezután válassza a lap alján található **Létrehozás** elemet.

4. Adja meg a munkaterület adatait:

   - A *munkaterület neve* legfeljebb 260 karakter hosszú lehet. A név nem tartalmazhatja a következő karaktereket: `< > * % & : \ ? + /`
   - A *webszolgáltatások* ezen a munkaterületen való üzembe helyezéséhez a kiválasztott (vagy létrehozás) webszolgáltatás-csomagot, valamint a társított *díjszabási szintet* kell használni.

     ![Új Studio (klasszikus) munkaterület létrehozása](./media/create-workspace/create-new-workspace.png)

5. Kattintson a **Létrehozás** lehetőségre.

   Machine Learning jelenleg korlátozott számú régióban érhető el. Ha az előfizetése nem tartalmaz ilyen régiókat, a következő hibaüzenet jelenhet meg: "nincsenek előfizetések az engedélyezett régiókban."  Ha egy régiót szeretne felvenni az előfizetésbe, hozzon létre egy új Microsoft-támogatási kérelmet a Azure Portal, válassza a **számlázás** lehetőséget a probléma típusaként, és kövesse az utasításokat a kérelem elküldéséhez.


> [!NOTE]
> A Machine Learning Studio (klasszikus) egy Azure Storage-fiókra támaszkodik, amelyet Ön a munkafolyamatok végrehajtásakor a köztes adattároláshoz biztosít. A munkaterület létrehozása után, ha a Storage-fiókot törölték, vagy ha a hozzáférési kulcsok módosulnak, a munkaterület működése leáll, és a munkaterület összes kísérlete sikertelen lesz.
Ha véletlenül törölte a Storage-fiókot, hozza létre újra a Storage-fiókot ugyanabban a régióban, mint a törölt Storage-fiókot, és szinkronizálja újra a hozzáférési kulcsot. Ha módosította a tárfiók hozzáférési kulcsát, az Azure Portal használatával szinkronizálja újra a hozzáférési kulcsot a munkaterületen.

Miután telepítette a munkaterületet, megnyithatja azt Machine Learning Studio (klasszikus).

1. Tallózással keresse meg Machine Learning Studio (klasszikus) a következő címen: [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Válassza ki munkaterületét a jobb felső sarokban.

    ![Munkaterület kiválasztása](./media/create-workspace/open-workspace.png)

3. Kattintson **a saját kísérletek** elemre.

    ![Kísérletek megnyitása](./media/create-workspace/my-experiments.png)

A Studio (klasszikus) munkaterület kezelésével kapcsolatos információkért lásd: [Azure Machine learning Studio (klasszikus) munkaterületek kezelése](manage-workspace.md).
Ha probléma merül fel a munkaterület létrehozásakor, olvassa el a [hibaelhárítási útmutató: Machine learning Studio (klasszikus) munkaterület létrehozása és kapcsolódása](index.yml)című témakört.


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Azure Machine Learning Studio (klasszikus) munkaterület megosztása
Miután létrehozta a Machine Learning Studio (klasszikus) munkaterületet, meghívhatja a felhasználókat a munkaterületre, hogy megosszák a hozzáférést a munkaterülethez és annak összes kísérletéhez, adatkészletéhez stb. A felhasználókat a két szerepkör egyikében adhatja hozzá:

* **Felhasználó** – a munkaterület felhasználója létrehozhat, megnyithat, módosíthat és törölhet kísérleteket, adatkészleteket stb. a munkaterületen.
* **Tulajdonos** – a felhasználók meghívhatják és eltávolíthatják a munkaterületen lévő felhasználókat, a felhasználó által elvégezhető műveletek mellett.

> [!NOTE]
> A munkaterületet létrehozó rendszergazdai fiók automatikusan hozzá lesz adva a munkaterülethez a munkaterület tulajdonosaként. Azonban az adott előfizetéshez tartozó más rendszergazdák vagy felhasználók nem kapnak automatikusan hozzáférést a munkaterülethez – ezeket explicit módon meg kell hívnia.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Studio (klasszikus) munkaterület megosztása

1. Jelentkezzen be Machine Learning Studio (klasszikus) [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. A bal oldali panelen kattintson a **Beállítások** elemre.

3. Kattintson a **felhasználók** lapra

4. Kattintson a lap alján található **további felhasználók meghívása** elemre.

    ![Studio-beállítások](./media/create-workspace/settings.png)

5. Írjon be egy vagy több e-mail-címet. A felhasználóknak érvényes Microsoft-fiók vagy szervezeti fiókra van szükségük (Azure Active Directory).

6. Válassza ki, hogy tulajdonosként vagy felhasználóként kívánja-e hozzáadni a felhasználókat.

7. Kattintson az **OK** pipa gombra.

Minden hozzáadott felhasználó kap egy e-mailt, amely útmutatást ad a megosztott munkaterületre való bejelentkezéshez.

> [!NOTE]
> Ahhoz, hogy a felhasználók képesek legyenek a webszolgáltatások üzembe helyezésére vagy kezelésére ezen a munkaterületen, az Azure-előfizetésben közreműködőnek vagy rendszergazdának kell lennie. 

## <a name="troubleshoot-storage-accounts"></a>Storage-fiókok hibáinak megoldása


A Machine Learning szolgáltatásnak szüksége van egy Storage-fiókra az adattároláshoz. Használhat meglévő Storage-fiókot, vagy létrehozhat egy új Storage-fiókot az új Machine Learning Studio (klasszikus) munkaterület létrehozásakor (ha van kvótája új Storage-fiók létrehozásához).

Miután létrehozta az új Machine Learning Studio (klasszikus) munkaterületet, bejelentkezhet a Machine Learning Studio (klasszikus) webhelyre a munkaterület létrehozásához használt Microsoft-fiók használatával. Ha a "munkaterület nem található" hibaüzenet jelenik meg (az alábbi képernyőképhez hasonlóan), akkor a következő lépésekkel törölheti a böngésző cookie-jait.

![A munkaterület nem található](media/troubleshooting-creating-ml-workspace/screen3.png)

**Böngésző cookie-k törlése**

1. Ha az Internet Explorert használja, kattintson a jobb felső sarokban található **eszközök** gombra, és válassza az **Internetbeállítások** lehetőséget.  

   ![Internetbeállítások](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Az **általános** lapon kattintson a **Törlés...** elemre.

   ![Általános lap](media/troubleshooting-creating-ml-workspace/screen5.png)

3. A **böngészési előzmények törlése** párbeszédpanelen ellenőrizze, hogy ki van-e jelölve a **cookie-k és a webhely-adatértékek** , majd kattintson a **Törlés** gombra.

   ![Cookie-k törlése](media/troubleshooting-creating-ml-workspace/screen6.png)

A cookie-k törlése után indítsa újra a böngészőt, majd lépjen a [Microsoft Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) lapra. Ha a rendszer a Felhasználónév és a jelszó megadását kéri, adja meg ugyanazt a Microsoft-fiók, amelyet a munkaterület létrehozásához használt.


## <a name="next-steps"></a>Következő lépések

A munkaterületek kezelésével kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) munkaterületek kezelése](manage-workspace.md).