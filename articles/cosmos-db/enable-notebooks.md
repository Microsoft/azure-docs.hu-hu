---
title: Jegyzetfüzetek engedélyezése a Azure Cosmos DB fiókban (előzetes verzió)
description: A Azure Cosmos DB beépített jegyzetfüzetei lehetővé teszik az adatok elemzését és megjelenítését a portálon belülről. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót a Cosmos-fiókokhoz.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.author: dech
ms.custom: references_regions
ms.openlocfilehash: 02e8ad5f2b5326f947ba0bca6456ce9d9d3c27d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692776"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a>Jegyzetfüzetek engedélyezése Azure Cosmos DB-fiókokhoz (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB beépített jegyzetfüzetei jelenleg [29 régióban](#supported-regions)érhetők el. Jegyzetfüzetek használatához [hozzon létre egy új Cosmos-fiókot](#create-a-new-cosmos-account) , vagy [engedélyezze a jegyzetfüzeteket egy meglévő fiókban](#enable-notebooks-in-an-existing-cosmos-account) ezen régiók egyikében. 

A Azure Cosmos DB beépített Jupyter notebookok lehetővé teszik az adatok elemzését és megjelenítését a Azure Portal. Ez a cikk leírja, hogyan lehet engedélyezni ezt a funkciót az Azure Cosmos DB-fiókhoz.

## <a name="create-a-new-cosmos-account"></a>Új Cosmos-fiók létrehozása
2021. február 10-én a [támogatott régió](#supported-regions) egyikében létrehozott új Azure Cosmos-fiókok automatikusan engedélyezve lesznek a jegyzetfüzetek számára. A jegyzetfüzetek engedélyezéséhez nincs szükség további konfigurációra. Új fiók létrehozásához kövesse az alábbi utasításokat:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza **az erőforrás létrehozása**  >  **adatbázisok**  >  **Azure Cosmos db** elemet.
1. Adja meg a fiók alapszintű beállításait.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="Az Azure Cosmos DB új fiók lapja":::

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** lehetőséget. 
1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás** gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**.

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Az Azure Portal Értesítések panelje":::

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot.

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="A Azure Cosmos DB fiók lapja":::

1. Navigáljon a **adatkezelő** panelre. Ekkor látnia kell a jegyzetfüzetek munkaterületét.

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="Új Azure Cosmos DB notebookok munkaterülete":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>Jegyzetfüzetek engedélyezése egy meglévő Cosmos-fiókban

A meglévő fiókokon is engedélyezheti a jegyzetfüzeteket. Ezt a lépést fiókkal csak egyszer kell elvégezni.

1. Navigáljon a Cosmos-fiók **adatkezelő** ablaktáblájához.
1. Válassza a **jegyzetfüzetek engedélyezése** lehetőséget.

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="Új jegyzetfüzetek munkaterületének létrehozása Adatkezelő":::

1. Ez a művelet megkéri, hogy hozzon létre egy új jegyzetfüzet-munkaterületet. Válassza a **telepítés befejezése lehetőséget.**
1. A fiókja már engedélyezve van a jegyzetfüzetek használatához.

## <a name="create-and-run-your-first-notebook"></a>Az első jegyzetfüzet létrehozása és futtatása

Annak ellenőrzéséhez, hogy használhatók-e jegyzetfüzetek, válassza ki az egyik jegyzetfüzetet a minta jegyzetfüzetek területen. Ezzel a művelettel a jegyzetfüzet egy másolatát menti a munkaterületre, és megnyithatja.

Ebben a példában a **bemutatása GettingStarted. ipynb**-t fogjuk használni.

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="Bemutatása GettingStarted. ipynb jegyzetfüzet megtekintése":::

A jegyzetfüzet futtatása:
1. Válassza ki a Python-kódot tartalmazó első kódlapot.
1. Válassza a **Futtatás** lehetőséget a cella futtatásához. A cella futtatásához a **SHIFT + ENTER** billentyűkombinációt is használhatja.
1. Frissítse az erőforrás-ablaktáblát, és tekintse meg a létrehozott adatbázist és tárolót.

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="Az első lépések jegyzetfüzet futtatása":::

Az **új jegyzetfüzet** lehetőség kiválasztásával új jegyzetfüzetet hozhat létre, vagy feltöltheti a meglévő jegyzetfüzeteket (. ipynb) a **saját jegyzetfüzetek** menüjének **fájl feltöltése** parancsával. 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="Új jegyzetfüzet létrehozása vagy feltöltése":::

## <a name="supported-regions"></a>Támogatott régiók
Az Azure Cosmos DB beépített notebookjai jelenleg 29 Azure-régióban érhetők el. Az ezekben a régiókban létrehozott új Azure Cosmos-fiókoknál a jegyzetfüzetek automatikusan engedélyezve lesznek. A jegyzetfüzetek ingyenesek a fiókjával. 

- Ausztrália középső régiója
- Ausztrália 2. középső régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Dél-Brazília
- Közép-Kanada
- Kelet-Kanada
- Közép-India
- USA középső régiója
- USA keleti régiója
- USA 2. keleti régiója
- Közép-Franciaország
- Dél-Franciaország
- Észak-Németország
- Középnyugat-Németország
- Nyugat-Japán
- Dél-Korea déli régiója
- USA északi középső régiója
- Észak-Európa
- USA déli középső régiója
- Délkelet-Ázsia
- Észak-Svájc
- UAE középső régiója
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- USA nyugati középső régiója
- Nyugat-Európa
- Nyugat-India
- USA 2. nyugati régiója

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
* [Ismerkedés a notebook Samples-galériával](https://cosmos.azure.com/gallery.html)
* [Jegyzetfüzetek közzététele a Azure Cosmos DB notebook-galériában](publish-notebook-gallery.md)
* [A Python notebook funkcióinak és parancsainak használata](use-python-notebook-features-and-commands.md)
* [C# Notebook-funkciók és-parancsok használata](use-csharp-notebook-features-and-commands.md)
* [Jegyzetfüzetek importálása GitHub-tárházból](import-github-notebooks.md)
