---
title: Feladatok és munkafolyamatok automatizálása a Visual Studióval
description: Automatizált munkafolyamatok létrehozása, betartása és futtatása a vállalati integrációhoz a Azure Logic Apps és a Visual Studio használatával
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/27/2020
ms.openlocfilehash: 8181097425045e5b1ed838c5fcc08b0069185908
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051957"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rövid útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps használatával – Visual Studio

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a Visual Studióval olyan munkafolyamatokat hozhat létre, amelyekkel automatizálható a vállalatok és a szervezetek alkalmazásait, adatait, rendszereit és szolgáltatásait integráló feladatok és folyamatok automatizálása. Ez a rövid útmutató bemutatja, hogyan tervezheti meg és építheti fel ezeket a munkafolyamatokat logikai alkalmazások létrehozásával a Visual Studióban, és hogyan helyezheti üzembe a létrehozott alkalmazásokat az Azure-ban. Bár ezeket a feladatokat a Azure Portal is elvégezheti, a Visual Studio lehetővé teszi a logikai alkalmazások hozzáadását a verziókövetés számára, különböző verziók közzétételét, valamint Azure Resource Manager sablonok létrehozását különböző központi telepítési környezetekhez.

Ha még nem ismeri a Azure Logic Appst, és csak az alapvető fogalmakat szeretné megtekinteni, próbálkozzon a [logikai alkalmazás létrehozásával a Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Logic app Designer hasonlóan működik a Azure Portal és a Visual Studióban is.

Ebben a rövid útmutatóban ugyanazt a logikai alkalmazást hozza létre a Visual Studióval Azure Portal rövid útmutatóként. Azt is megtudhatja, hogyan [hozhat létre egy példát a Visual Studio Code-ban](quickstart-create-logic-apps-visual-studio-code.md), és hogyan [hozhat létre és kezelhet logikai alkalmazásokat az Azure Command-Line felületen (Azure CLI)](quickstart-logic-apps-azure-cli.md). Ez a logikai alkalmazás figyeli a webhely RSS-hírcsatornáját, és e-mailt küld az adott hírcsatorna minden új eleménél. A kész logikai alkalmazás a következő magas szintű munkafolyamathoz hasonlít:

![A kész logikai alkalmazás magas szintű munkafolyamatát bemutató képernyőkép.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Ha Azure Government-előfizetéssel rendelkezik, kövesse ezeket a további lépéseket a [Visual Studio Azure Government-felhőhöz való beállításához](#azure-government).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – Community Edition vagy újabb](https://aka.ms/download-visual-studio). Ez a rövid útmutató a Visual Studio Community 2017-et használja.

    > [!IMPORTANT]
    > A Visual Studio 2019-es vagy 2017-es vagy-es telepítésekor ügyeljen arra, hogy kiválassza az **Azure-fejlesztési** számítási feladatot.

  * [Microsoft Azure SDK for .net (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/). További információ az [Azure SDK for .NET](/dotnet/azure/intro) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * A Visual Studio bővítmény legújabb Azure Logic Apps eszközei a kívánt verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](/visualstudio/ide/finding-and-using-visual-studio-extensions). Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Internet-hozzáférés a beágyazott Logikaialkalmazás-tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége az erőforrások létrehozásához az Azure-ban, illetve a logikai alkalmazásban található összekötők tulajdonságainak és adatainak olvasásához.

* Logic Apps által támogatott e-mail-fiók, például az Outlook for Microsoft 365, a Outlook.com vagy a gmail. Más szolgáltatók esetén itt tekintse át az [Összekötők listáját](/connectors/). Ez a példa az Office 365 Outlookot használja. Ha más szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Visual Studio beállítása az Azure Governmenthez

### <a name="visual-studio-2017"></a>Visual Studio 2017

Használhatja az [Azure Environment választó Visual Studio bővítményt](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), amelyet a [Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector)tölthet le és telepíthet.

### <a name="visual-studio-2019"></a>Visual Studio 2019

A Azure Logic Apps Azure Government-előfizetésekkel való együttműködéshez [hozzá kell adnia egy felderítési végpontot a Azure Government Cloud a Visual studióhoz](../azure-government/documentation-government-connect-vs.md). Mielőtt azonban *bejelentkezne a Visual studióba a Azure Government-fiókjával*, át kell neveznie a felderítési végpont hozzáadása után generált JSON-fájlt a következő lépések végrehajtásával:

1. Zárja be a Visual Studiót.

1. Keresse meg a létrehozott JSON-fájlt `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` ezen a helyen:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Nevezze át a JSON-fájlt a következőre: `AadProvider.Configuration.json` .

1. Indítsa újra a Visual Studiót.

1. Folytassa a Azure Government-fiókkal való bejelentkezés lépéseivel.

A telepítés visszavonásához törölje a JSON-fájlt a következő helyen, majd indítsa újra a Visual studiót:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/management/overview.md).

1. Indítsa el a Visual Studiót. Jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet. (Billentyűzet: Ctrl + Shift + N)

   ![Az Új > Projekt menüpont kiválasztása a Fájl menüben](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza ki a **Cloud**  >  **Azure-erőforráscsoportot**. Adjon nevet a projektnek, például a következőt:

   ![Azure erőforráscsoport-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Az erőforráscsoportok nevei csak betűket, számokat, pontokat ( `.` ), aláhúzást ( `_` ), kötőjelet ( `-` ) és zárójeleket ( `(` ,) tartalmazhatnak `)` , de nem *végződhet* pontokkal ( `.` ).
   >
   > Ha a **felhő** vagy az **Azure-erőforráscsoport** nem jelenik meg, győződjön meg róla, hogy telepíti a Visual studióhoz készült Azure SDK-t.

   Ha a Visual Studio 2019-et használja, kövesse az alábbi lépéseket:

   1. Az **új projekt létrehozása** mezőben válassza ki a Visual C# vagy a Visual Basic **Azure erőforráscsoport** -projektjét. Kattintson a **Tovább** gombra.

   1. Adja meg a használni kívánt Azure-erőforráscsoport nevét és a projekt egyéb információit. Válassza a **Létrehozás** lehetőséget.

1. A sablon listából válassza ki a **logikai alkalmazás** sablonját. Válassza az **OK** lehetőséget.

   ![Logikaialkalmazás-sablon kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Miután a Visual Studio létrehozta a projektet, megnyílik a Megoldáskezelő, és megjeleníti a megoldást. A megoldásban a **LogicApp.js** fájl nem csak a Logic app-definíciót tárolja, hanem egy Azure Resource Manager sablon is, amelyet az üzembe helyezéshez használhat.

   ![A Megoldáskezelő az új logikai alkalmazással és az üzembe helyezési fájllal](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

Ha rendelkezik az Azure erőforráscsoport-projekttel, hozzon létre egy logikai alkalmazást az **üres logikai alkalmazás** sablonnal.

1. A Megoldáskezelőban nyissa meg a **LogicApp.jsa** fájl helyi menüjében. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**. (Billentyűzet: CTRL + L)

   ![A logikai alkalmazás .json-fájljának megnyitása a Logikaialkalmazás-tervezővel](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

   A Visual Studio az Azure-előfizetését és egy Azure-erőforráscsoportot kér a logikai alkalmazás és a kapcsolatok erőforrásainak létrehozásához és üzembe helyezéséhez.

1. Az **előfizetés** mezőben válassza ki az Azure-előfizetését. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget egy másik Azure-erőforráscsoport létrehozásához.

   ![Azure-előfizetés, erőforráscsoport és erőforrás helyének kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Beállítás | Példaérték | Description |
   | ------- | ------------- | ----------- |
   | Felhasználói fiók | Fabrikam <br> sophia-owen@fabrikam.com | A fiók, amelyet a Visual studióba való bejelentkezéskor használt |
   | **Előfizetés** | Utólagos, használatalapú fizetés <br> (sophia-owen@fabrikam.com) | Az Azure-előfizetés és a társított fiók neve |
   | **Erőforráscsoport** | MyLogicApp-RG <br> (USA nyugati régiója) | A logikai alkalmazás erőforrásainak tárolásához és üzembe helyezéséhez használt Azure-erőforráscsoport és-hely |
   | **Hely** | **Ugyanaz, mint az erőforráscsoport** | A logikai alkalmazás üzembe helyezéséhez használt hely típusa és adott hely. A hely típusa egy Azure-régió vagy egy meglévő [integrációs szolgáltatási környezet (ISE)](connect-virtual-network-vnet-isolated-environment.md). <p>Ebben a rövid útmutatóban tartsa meg a hely típusát **régióra** , és a helyet állítsa az **erőforráscsoport** értékre. <p>**Megjegyzés**: az erőforráscsoport-projekt létrehozása után [módosíthatja a hely típusát és helyét](manage-logic-apps-with-visual-studio.md#change-location), de a különböző típusú helyek különböző módokon érintik a logikai alkalmazást. |
   ||||

1. A Logic Apps Designer egy olyan oldalt nyit meg, amely bemutatja a bemutató videót és a gyakran használt eseményindítókat. Görgessen le a videó **és az eseményindítók között, és** válassza az **üres logikai alkalmazás** lehetőséget.

   ![„Üres logikai alkalmazás” kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának felépítése

Ezután adjon hozzá egy RSS- [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely akkor aktiválódik, amikor megjelenik egy új hírcsatorna elem. Minden logikai alkalmazás egy eseményindítóval kezdődik, amely az adott feltételek teljesülése esetén következik be. A Logic Apps-motor a trigger minden aktiválásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

1. A Logic app Designerben a keresőmező alatt válassza az **összes** lehetőséget. A keresőmezőbe írja be az "RSS" kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót: a **hírcsatorna-elemek közzétételekor**

   ![Logikai alkalmazás felépítése trigger és műveletek hozzáadásával](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Miután az trigger megjelenik a tervezőben, fejezze be a logikai alkalmazás kiépítése a [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)rövid útmutató munkafolyamat lépéseit követve, majd térjen vissza ehhez a cikkhez. Az elkészült logikai alkalmazás az alábbihoz hasonlít:

   ![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Mentse a Visual Studio-megoldást. (Billentyűparancs: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logikai alkalmazás üzembe helyezése az Azure-ban

A logikai alkalmazás futtatása és tesztelése előtt telepítse az alkalmazást az Azure-ba a Visual studióból.

1. Megoldáskezelő a projekt helyi menüjében válassza az   >  **új** telepítése lehetőséget. Ha szükséges, jelentkezzen be Azure-fiókjával.

   ![A logikai alkalmazás üzemelő példányának létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Ehhez a központi telepítéshez tartsa meg az alapértelmezett Azure-előfizetést, erőforráscsoportot és egyéb beállításokat. Válassza az **Üzembe helyezés** lehetőséget.

   ![Logikai alkalmazás üzembe helyezése Azure-erőforráscsoportra](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adjon meg egy erőforrás-nevet a logikai alkalmazás számára. Mentse a beállításokat.

   ![Adja meg a logikai alkalmazás üzemelő példányának nevét](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot.

   ![Üzembe helyezés állapotának kimenete](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Ha a kiválasztott összekötőknek bemenetre van szüksége, a háttérben megnyílik egy PowerShell-ablak, és megkérdezi a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![PowerShell-ablak](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Az üzembe helyezés befejeződése után a logikai alkalmazás a Azure Portalban fut, és a megadott ütemterven (percenként) fut. Ha az eseményindító új hírcsatorna-elemeket talál, az eseményindító tüzek, amely létrehoz egy munkafolyamat-példányt, amely futtatja a logikai alkalmazás műveleteit. A logikai alkalmazás minden új elemmel elküld e-mailt. Ellenkező esetben, ha az trigger nem talál új elemeket, az trigger nem gyullad ki, és "kihagyja" a munkafolyamat példányát. Az ellenőrzés előtt a logikai alkalmazás a következő intervallumig vár.

   Itt láthatja a logikai alkalmazás által küldött e-maileket. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

   ![Az Outlook minden új RSS-elemről e-mailt küld](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulálunk, sikeresen felépítette és üzembe helyezte a logikai alkalmazást a Visual Studióban. A logikai alkalmazás kezeléséhez és az előzményeinek megtekintéséhez lásd a [logikai alkalmazások Visual Studióval történő kezelésével](../logic-apps/manage-logic-apps-with-visual-studio.md) foglalkozó cikket.

## <a name="add-new-logic-app"></a>Új logikai alkalmazás hozzáadása

Ha rendelkezik egy meglévő Azure-erőforráscsoport-projekttel, a JSON-vázlat ablak használatával hozzáadhat egy új üres logikai alkalmazást a projekthez.

1. A Megoldáskezelőban nyissa meg a `<logic-app-name>.json` fájlt.

1. A **nézet** menüben válassza a **más Windows**  >  **JSON-vázlat** lehetőséget.

1. Ha hozzá szeretne adni egy erőforrást a sablonfájl számára, válassza a JSON-vázlat ablak tetején található **erőforrás hozzáadása** lehetőséget. Vagy a JSON-vázlat ablakban nyissa meg az **erőforrások** helyi menüt, és válassza az **új erőforrás hozzáadása** lehetőséget.

   ![JSON-vázlat ablak](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Az **erőforrás hozzáadása** párbeszédpanelen a keresőmezőbe írja be a kifejezést `logic app` , majd válassza a **logikai alkalmazás** lehetőséget. Nevezze el a logikai alkalmazást, és válassza a **Hozzáadás** lehetőséget.

   ![Erőforrás hozzáadása](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a logikai alkalmazással, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazás létrehozásához használt fiókkal.

1. A Azure Portal menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az **erőforráscsoportot** bármelyik oldalon. Válassza ki a logikai alkalmazáshoz tartozó erőforráscsoportot.

1. Az **Áttekintés** lapon válassza az **erőforráscsoport törlése** elemet. Adja meg az erőforráscsoport nevét megerősítésként, majd válassza a **Törlés** lehetőséget.

   ![„Erőforráscsoportok” > „Áttekintés” > „Erőforráscsoport törlése”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Törölje a Visual Studio-megoldást a helyi számítógépről.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy logikai alkalmazást állított össze, helyezett üzembe és futtatott a Visual Studióval. A Logic apps és a Visual Studio használatával végzett speciális üzembe helyezés kezelésével és végrehajtásával kapcsolatos információkért tekintse meg a következő cikkeket:

> [!div class="nextstepaction"]
> [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)
