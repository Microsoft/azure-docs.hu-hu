---
title: Jegyzetfüzetek közzététele a Azure Cosmos DB notebook-galériában
description: Megtudhatja, hogyan tölthet le jegyzetfüzeteket a nyilvános galériából, szerkesztheti őket, és közzéteheti saját jegyzetfüzeteit a katalógusban.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: c7c910c7162bde2209b07eb4f1d533328bae08c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693210"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Jegyzetfüzetek közzététele a Azure Cosmos DB notebook-galériában
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB beépített Jupyter jegyzetfüzetek közvetlenül integrálva vannak a Azure Portal Azure Cosmos DB fiókjaiba. Ezeket a jegyzetfüzeteket használva elemezheti és megjelenítheti az adatait a Azure Portalból. A Azure Cosmos DB beépített jegyzetfüzetei jelenleg [29 régióban](#supported-regions)érhetők el. Jegyzetfüzetek használatához [hozzon létre egy új Cosmos-fiókot](#create-a-new-cosmos-account) , vagy [engedélyezze a jegyzetfüzeteket egy meglévő fiókban](#enable-notebooks-in-an-existing-cosmos-account) ezen régiók egyikében.

A Azure Portalban található notebook-környezetnek van néhány mintája, amelyet a Azure Cosmos DB csapat tesz közzé. Emellett rendelkezik egy nyilvános katalógussal is, ahol közzéteheti és megoszthatja saját jegyzetfüzeteit. Miután közzétett egy jegyzetfüzetet a katalógusban, elérhetővé válik az összes Azure Cosmos DB felhasználó számára a megtekintéshez és a használathoz. Ebből a cikkből megtudhatja, hogyan használhatók a jegyzetfüzetek a nyilvános galériában, és hogyan tehetők közzé a katalógusban.

## <a name="download-a-notebook-from-the-gallery"></a>Jegyzetfüzet letöltése a katalógusból

A következő lépésekkel tekintheti meg és töltheti le a katalógusból a saját jegyzetfüzetek munkaterületére a jegyzetfüzeteket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon a notebook-környezettel engedélyezett Azure Cosmos db-fiókhoz.

1. Navigáljon a **adatkezelő**  >  **notebookok** katalógus nyilvános katalógusa  >    >   lapra.

1. Fogadja el a [viselkedési szabályzatot](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  , mielőtt megtekinti vagy közzéteszi a gyűjteményben lévő jegyzetfüzeteket. A viselkedési szabályzatot felhasználónként kell naplózni, az előfizetés szintjén. Ha másik előfizetésre vált, újra el kell fogadnia a katalógushoz való hozzáférés előtt. A viselkedési szabályzat elfogadásához jelölje be a jelölőnégyzetet, és **folytassa** a következőket:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Navigáljon a jegyzetfüzetek nyilvános galériához, és fogadja el a viselkedési szabályzatot.":::

1. Ezután hozzáadhat egy adott jegyzetfüzetet a Kedvencek laphoz, vagy letöltheti azt. Amikor letölt egy jegyzetfüzetet, a rendszer átmásolja a jegyzetfüzetek munkaterületére, ahol futtathatja vagy szerkesztheti.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Töltsön le egy jegyzetfüzetet a galériából a munkaterületre.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Jegyzetfüzet közzététele a katalógusban

Ha saját jegyzetfüzeteket hoz létre, vagy a meglévő jegyzetfüzeteket egy másik forgatókönyvhöz szeretné igazítani, érdemes lehet közzétenni őket a katalógusban. Miután közzétett egy jegyzetfüzetet a katalógusban, más felhasználók is hozzáférhetnek. A jelenleg elérhető jegyzetfüzetek megtekintéséhez tekintse meg a [Jegyzetfüzet-minták gyűjteményét](https://cosmos.azure.com/gallery.html) .

Jegyzetfüzet közzétételéhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és navigáljon a notebook-környezettel engedélyezett Azure Cosmos db-fiókhoz.

1. Navigáljon a jegyzetfüzetek **adatkezelő**  >  **notebookok**  >   lapfülre.

1. Nyissa meg a közzétenni kívánt jegyzetfüzetet. Kattintson a parancssáv **Mentés** gombjára, majd válassza a **Közzététel a** katalógusba lehetőséget:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Válasszon ki egy jegyzetfüzetet, amelyet közzé szeretne tenni a gyűjteményben.":::

   A **Közzététel** a katalógusban lehetőséget a... lehetőségre kattintva is megtalálhatja **.** a jegyzetfüzet neve melletti gomb:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Válasszon ki egy jegyzetfüzetet, amelyet közzé szeretne tenni a gyűjteményben.":::

1. Töltse ki a **Közzététel** a katalógusba űrlapot a következő részletekkel:

   * **Név:** A jegyzetfüzet azonosítására szolgáló felhasználóbarát név.
   * **Leírás:**  A jegyzetfüzet funkcióinak rövid leírása.
   * **Címkék:** A címkék nem kötelezőek, és az eredmények szűrésére szolgálnak egy kulcsszó alapján való kereséskor.
   * **Takarás képe:** A címlapon a jegyzetfüzet közzétételekor használt rendszerkép. A következő lehetőségek közül választhat:

     * **Egyéni rendszerkép** – feltölthet egy rendszerképet a számítógépről. Válasszon egy képfájlt méretarányos 256x144.
     * **URL-cím** – adjon meg egy nyilvánosan elérhető URL-címet, ahol a rendszerkép található.
     * **Képernyőkép készítése** – a megnyitott jegyzetfüzetből származó képernyőkép automatikusan megjelenik, és az előzetes verzióra lesz feltöltve.
     * A megjelenítési kimenettel rendelkező első cella **első kimeneti kimenetének használata** . Azok a cellák, amelyek csak Markdown vagy szöveget jelenítenek meg, nem jelenítik meg a megjelenítési kimenetet.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Töltse ki a közzététel a galériában űrlapot.":::

   > [!NOTE]
   > Ha a **Közzététel** a katalógusban lehetőséget használja a.. **.** a jegyzetfüzet neve melletti gomb nem jelenik meg az összes **képképpel** kapcsolatos beállításban. Ennek oka, hogy előfordulhat, hogy a jegyzetfüzet nem nyílik meg, és Azure Cosmos DB nem fér hozzá képernyőfelvételhez, vagy nem fér hozzá az első megjelenített kimenethez.

1. Győződjön meg arról, hogy az előnézet jól látható, majd válassza a **Közzététel** lehetőséget. Közzétételekor ez a jegyzetfüzet a Azure Cosmos DB jegyzetfüzetek nyilvános galériájában fog megjelenni. A közzététel előtt győződjön meg róla, hogy eltávolította a bizalmas adatokat vagy a kimenetet. A rendszer a jegyzetfüzet tartalmát a Microsoft-szabályzatok közzététel előtti megsértésével vizsgálja. Ez a folyamat általában eltarthat néhány másodpercig. Ha bármilyen megsértést talál, megjelenik egy üzenet az értesítés lapon. A jegyzetfüzet nem kerül közzétételre, ha bármilyen megsértést talál, eltávolítja a megsértett szöveget, és újból közzéteszi azt.

   > [!NOTE]
   > Miután közzétette a jegyzetfüzeteket a nyilvános galériában, a Azure Cosmos DB összes felhasználója megtekintheti őket. A hozzáférés nem korlátozódik előfizetésre, vagy szervezeti szintenként.

1. Miután a jegyzetfüzet közzé lett téve a katalógusban, a **saját közzétett munka** lapon láthatja. A nyilvános galériából közzétett jegyzetfüzeteket is eltávolíthat vagy törölhet.

1. Olyan jegyzetfüzetet is jelenthet, amely sérti a viselkedési szabályzatot. Ha a rendszer megsértést talál, Cosmos DB automatikusan eltávolítja a jegyzetfüzetet a katalógusból. Ha eltávolít egy jegyzetfüzetet, a felhasználók a **saját közzétett munka** lapon láthatják el az eltávolított megjegyzéssel. Ha egy jegyzetfüzetet szeretne bejelenteni, lépjen a **adatkezelő**  >  **jegyzetfüzetek** katalógus nyilvános katalógusa  >    >   lapra. Nyissa meg a jelentést tartalmazó jegyzetfüzetet, vigye a kurzort a jobb oldali sarokban található **Súgó** gombra, és válassza a **Visszaélés jelentése** lehetőséget.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Olyan jegyzetfüzetet jelent, amely sérti a viselkedési szabályzatot.":::

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [Azure Cosmos db Jupyter notebookok](cosmosdb-jupyter-notebooks.md) előnyeit
* [A Python notebook funkcióinak és parancsainak használata](use-python-notebook-features-and-commands.md)
* [C# Notebook-funkciók és-parancsok használata](use-csharp-notebook-features-and-commands.md)
* [Jegyzetfüzetek importálása GitHub-tárházból](import-github-notebooks.md)
