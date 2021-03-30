---
title: Tudásbázis megtekintése Storage Explorer
titleSuffix: Azure Cognitive Search
description: Megtekintheti és elemezheti az Azure Cognitive Search Knowledge Store-t a Azure Portal Storage Explorerával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566024"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Tudásbázis megtekintése Storage Explorer

Ebből a cikkből megtudhatja, hogyan csatlakozhat a tudásbázishoz, és hogyan derítheti fel a Storage Explorer használatával a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

+ A jelen útmutatóban használt mintaűrlapok létrehozásához kövesse a [Tudásbázis létrehozása a Azure Portalban](knowledge-store-create-portal.md) című témakör lépéseit.

+ Szüksége lesz a Knowledge Store létrehozásához használt Azure Storage-fiók nevére, valamint a Azure Portal elérési kulcsára.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Egy Tudásbázis megtekintése, szerkesztése és lekérdezése Storage Explorer

1. A Azure Portal [nyissa meg](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) azt a Storage-fiókot, amelyet a Knowledge Store létrehozásához használt.

1. A Storage-fiók bal oldali navigációs paneljén kattintson a **Storage Explorer** elemre.

1. A **Tables (táblák** ) lista kibontásával megjelenítheti az Azure Table-előrejelzések listáját, amelyeket a rendszer a mintaadatok **importálása** varázsló futtatásakor hozott létre.

Bármelyik táblát kiválasztva megtekintheti a dúsított adatelemeket, beleértve a legfontosabb kifejezéseket és a hangulati pontszámokat.

   ![Táblázatok megtekintése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Táblázatok megtekintése Storage Explorer")

Ha módosítani szeretné bármely tábla értékének adattípusát, vagy módosítani szeretné a tábla egyes értékeit, kattintson a **Szerkesztés** gombra. Ha egy tábla bármely oszlopának adattípusát megváltoztatja, az összes sorra lesz alkalmazva.

   ![Táblázat szerkesztése Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Táblázat szerkesztése Storage Explorer")

A lekérdezések futtatásához kattintson a parancssorban a **lekérdezés** elemre, és adja meg a feltételeit.  

   ![Lekérdezési táblázat Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Lekérdezési táblázat Storage Explorer")

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon.

## <a name="next-steps"></a>Következő lépések

A Knowledge Store-t összekapcsolhatjuk a mélyebb elemzés érdekében, vagy a REST API és a Poster használatával egy másik Tudásbázis létrehozásával Power BI.

> [!div class="nextstepaction"]
> [Kapcsolat Power bi](knowledge-store-connect-power-bi.md) 
>  [Hozzon létre egy Knowledge Store-t a REST-ben](knowledge-store-create-rest.md)
