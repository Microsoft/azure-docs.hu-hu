---
title: Egy GitHub-tárházból származó jegyzetfüzetek importálása és futtatása Azure Cosmos DB
description: Megtudhatja, hogyan csatlakozhat a GitHubhoz, és hogyan importálhat egy GitHub-tárházból származó jegyzetfüzeteket az Azure Cosmos-fiókjába. Az importálás után futtathatja, szerkesztheti, és mentheti a módosításokat a GitHubra.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: f7002b3968e91447a26315f31347ad469aa5daca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690651"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Jegyzetfüzetek importálása egy GitHub-tárházból a Azure Cosmos DBba
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Miután engedélyezte az Azure Cosmos-fiókok [notebook-támogatását](enable-notebooks.md) , új jegyzetfüzeteket hozhat létre, új jegyzetfüzeteket tölthet fel a helyi számítógépről, vagy importálhatja a meglévő jegyzetfüzeteket a GitHub-fiókjaiból. Ez a cikk bemutatja, hogyan csatlakoztatható a jegyzetfüzetek munkaterülete a GitHubhoz, és hogyan importálhatja a jegyzetfüzeteket egy GitHub-tárházból az Azure Cosmos-fiókjába. Az importálás után futtathatja őket, módosításokat végezhet, és mentheti a módosításokat a GitHubra.

## <a name="get-notebooks-from-github"></a>Jegyzetfüzetek beszerzése a GitHubról

A saját GitHub-adattárakhoz vagy más nyilvános GitHub-adattárakhoz is csatlakozhat, amelyekkel elolvashatja, elkészítheti és megoszthatja a jegyzetfüzeteket Azure Cosmos DB. Egy GitHub-fiókhoz való kapcsolódáshoz kövesse az alábbi lépéseket:

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) , és navigáljon az Azure Cosmos-fiókjához.

1. Nyissa meg a **adatkezelő** lapot. Ezen a lapon láthatók a meglévő adatbázisok, tárolók és jegyzetfüzetek.

1. Válassza a **Kapcsolódás a githubhoz** menüpontot.

1. Megnyílik egy lap, ahol dönthet úgy, hogy csak a **nyilvános** vagy **nyilvános és a privát** adattárakhoz tud csatlakozni.  A kötelező beállítás kiválasztása után válassza a **hozzáférés engedélyezése** lehetőséget. A GitHub-fiókban lévő adattárak eléréséhez Azure Cosmos DB szükséges a hitelesítés.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="A GitHub-adattárakhoz való hozzáférés engedélyezése Azure Cosmos DB":::

1. A rendszer átirányítja a "github.com" weblapra, ahol megerősítheti az engedélyt. Kattintson a **AzureCosmosDBNotebooks engedélyezése** gombra, és adja meg a GitHub-fiók jelszavát a parancssorban.

1. Az engedélyezés sikerességét követően visszakerül az Azure Cosmos-fiókjába. Ezután megtekintheti a GitHub-fiókjából származó összes nyilvános/privát repót. Kiválaszthat egy tárházat a listából, vagy közvetlenül is hozzáadhat egy tárházat az URL-címével.

1. Miután kiválasztotta a szükséges tárházat, a tárház bejegyzése a nem **rögzített repók** szakaszból a **rögzített repók** szakaszba kerül. Ha szükséges, az adott tárház adott ágát is kiválaszthatja, hogy importálja a jegyzetfüzeteket a rendszerből.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Tárház és ág kiválasztása":::

1. Az importálási művelet befejezéséhez kattintson **az OK gombra** . A tárház kiválasztott ágában elérhető összes jegyzetfüzetet az Azure Cosmos-fiókjába importálja a rendszer.

A GitHub-fiókkal való integráció után csak az Azure Cosmos-fiókban található Tárházak és jegyzetfüzetek listáját láthatja. Ez az utasítás akkor is igaz, ha több felhasználó is bejelentkezik a Azure Cosmos DB-fiókba, és hozzáadja a saját fiókjait. Ez azt jelenti, hogy több felhasználó is használhatja ugyanazt az Azure Cosmos-fiókot a notebook munkaterület GitHubhoz való összekapcsolásához. Az egyes felhasználók azonban csak az importált Tárházak és jegyzetfüzetek listáját látják. A mások által importált jegyzetfüzetek nem láthatók Önnek.

A GitHub-fiók jegyzetfüzetek munkaterületről való leválasztásához nyissa meg a **adatkezelő** lapot, válassza a `…` GitHub- **repók** melletti kijelölés lehetőséget, majd válassza a **Leválasztás a githubról**

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Jegyzetfüzet-és leküldéses módosítások módosítása a GitHubon

Szerkeszthet egy meglévő jegyzetfüzetet, vagy hozzáadhat egy új jegyzetfüzetet a tárházhoz, és visszamentheti a módosításokat a GitHubra.

A meglévő jegyzetfüzetek szerkesztését követően válassza a **Mentés** lehetőséget. Megnyílik egy párbeszédpanel, ahol megadhatja a végrehajtott módosítások véglegesítő üzenetét. Válassza a **commit (véglegesítés** ) lehetőséget, és a rendszer frissíti a a githubon A frissítések érvényesítéséhez jelentkezzen be a GitHub-fiókjába, és ellenőrizze a véglegesítő előzményeket.

A módosítások véglegesítése után a szokásos GitHub-folyamat során általában leküldi a módosításokat egy távoli rendszerbe. Ebben az esetben azonban a véglegesítési lehetőség a frissítések a GitHubra történő "előkészítését, véglegesítését és kitolását" szolgálja.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Jegyzetfüzetek szerkesztése és módosítások elvégzése a GitHubon":::

A jegyzetfüzet szerkesztése után [közzéteheti azt a jegyzetfüzet-galériában](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit.
* [Ismerkedés a notebook Samples-galériával](https://cosmos.azure.com/gallery.html)
* [Jegyzetfüzetek közzététele a Azure Cosmos DB notebook-galériában](publish-notebook-gallery.md)
* [A Python notebook funkcióinak és parancsainak használata](use-python-notebook-features-and-commands.md)
* [C# Notebook-funkciók és-parancsok használata](use-csharp-notebook-features-and-commands.md)
