---
title: Fájlok létrehozása és kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és kezelhet fájlokat a munkaterületen a Azure Machine Learning Studióban.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101345"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Fájlok létrehozása és kezelése a munkaterületen

Megtudhatja, hogyan hozhatja létre és kezelheti a fájlokat a Azure Machine Learning munkaterületen.  Ezeket a fájlokat az alapértelmezett munkaterület-tárolóban tárolja a rendszer. A fájlok és mappák megoszthatók másokkal, akik olvasási hozzáféréssel rendelkeznek a munkaterülethez, és használhatók a munkaterület bármely számítási példányán.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Fájlok létrehozása

Új fájl létrehozása az alapértelmezett mappában ( `Users > yourname` ):

1. Nyissa meg a munkaterületét [Azure Machine learning Studióban](https://ml.azure.com).
1. A bal oldalon válassza a **jegyzetfüzetek** lehetőséget.
1. Válassza ki a **+** rendszerképet.
1. Válassza az  **új fájl létrehozása** képet.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Új fájl létrehozása":::

1. Nevezze el a fájlt.
1. Válassza ki a fájltípust.
1. Válassza a **Létrehozás** lehetőséget.

A jegyzetfüzetek és a legtöbb szöveges fájltípus az előnézet szakaszban látható.  A legtöbb más fájltípusnak nincs előzetes verziója.

Új fájl létrehozása egy másik mappában:
1. Válassza a "..." lehetőséget. a mappa végén
1. Válassza az **új fájl létrehozása** lehetőséget.

> [!IMPORTANT]
> A jegyzetfüzetekben és parancsfájlokban lévő tartalmak beolvashatják a munkamenetek adatait, és az Azure-beli szervezet nélkül férhetnek hozzá az adatokhoz.  Csak megbízható forrásból származó fájlok betöltése. További információ: [biztonságos kód – ajánlott eljárások](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

## <a name="manage-files-with-git"></a>Fájlok kezelése a git-vel

A git-Tárházak klónozásához és kezeléséhez [használjon számítási példányok terminálját](how-to-access-terminal.md#git) .

## <a name="clone-samples"></a>Minták klónozása

A munkaterület tartalmaz egy jegyzetfüzeteket tartalmazó **minta jegyzetfüzeteket** , amelyek segítségével MEGISMERHETI az SDK-t, és példaként szolgálhat a saját gépi tanulási projektjeihez.   ezeket a jegyzetfüzeteket a saját mappájába helyezheti a futtatásához és szerkesztéséhez.  

Példaként tekintse [meg az első ml-kísérlet létrehozását ismertető oktatóanyagot](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Fájlok megosztása

Másolja és illessze be az URL-címet egy fájl megosztásához.  Ehhez az URL-címhez csak a munkaterület más felhasználói férhetnek hozzá.  További információ [a munkaterület hozzáférésének biztosításáról](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Fájl törlése

A **minta jegyzetfüzetek** fájljait *nem* lehet törölni.  Ezek a fájlok a stúdió részét képezik, és minden alkalommal frissülnek, amikor új SDK kerül közzétételre.  

A **fájlok** szakaszban található fájlokat a következő módokon *törölheti* :

* A Studióban válassza a **...** elemet a mappa vagy fájl végén.  Győződjön meg arról, hogy támogatott böngészőt használ (Microsoft Edge, Chrome vagy Firefox).
* [Használjon terminált](how-to-access-terminal.md) bármely számítási példányból a munkaterületen. A **~/cloudfiles** mappa a munkaterület Storage-fiókjában található tárolóra van leképezve.
* A Jupyter vagy a JupyterLab eszközzel.
