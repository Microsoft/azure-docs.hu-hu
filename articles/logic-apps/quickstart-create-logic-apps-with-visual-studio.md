---
title: Feladatok és munkafolyamatok automatizálása Visual Studio
description: Automatizált munkafolyamatok létrehozása, ütemezése és futtatása vállalati integrációhoz Azure Logic Apps és Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 5ae67e5708a7298385a4e27d612566008884b972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790058"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rövid útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása Azure Logic Apps – Visual Studio

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és Visual Studio segítségével munkafolyamatokat hozhat létre olyan feladatok és folyamatok automatizálásához, amelyek alkalmazásokat, adatokat, rendszereket és szolgáltatásokat integrálnak a vállalatok és szervezetek között. Ez a rövid útmutató bemutatja, hogyan tervezheti meg és építheti fel ezeket a munkafolyamatokat logikai alkalmazások létrehozásával a Visual Studióban, és hogyan helyezheti üzembe a létrehozott alkalmazásokat az Azure-ban. Bár ezeket a feladatokat elvégezheti a Azure Portal Visual Studio lehetővé teszi, hogy logikai alkalmazásait hozzáadja a verzióvezérléshez, különböző verziókat tegyen közzé, és Azure Resource Manager sablonokat hozzon létre a különböző telepítési környezetekhez.

Ha még csak most használja Azure Logic Apps, és csak az alapfogalmakat szeretné, próbálja ki a logikai alkalmazások létrehozásának rövid útmutatóját [a Azure Portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A Logikaialkalmazás-tervező hasonlóan működik a Azure Portal és Visual Studio.

Ebben a rövid útmutatóban ugyanazt a logikai alkalmazást hozza létre, Visual Studio a Azure Portal is. Azt is megtanulhatja, hogyan hozhat létre egy példaalkalmazást [a Visual Studio Code-ban,](quickstart-create-logic-apps-visual-studio-code.md)és hogyan hozhat létre és kezelhet logikai alkalmazásokat az Azure Command-Line Interface [(Azure CLI) használatával.](quickstart-logic-apps-azure-cli.md) Ez a logikai alkalmazás figyeli egy webhely RSS-hírcsatornáját, és e-mailt küld a hírcsatorna minden új eleméről. A kész logikai alkalmazás a következő magas szintű munkafolyamathoz hasonlít:

![A kész logikai alkalmazás magas szintű munkafolyamatát bemutató képernyőkép.](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Ha előfizetéssel rendelkezik Azure Government, kövesse az alábbi lépéseket a felhőbeli Visual Studio [Azure Government beállításához.](#azure-government)

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – Community edition vagy nagyobb.](https://aka.ms/download-visual-studio) Ez a rövid útmutató a 2017 Visual Studio Community t használja.

    > [!IMPORTANT]
    > A 2019 Visual Studio 2017-es vagy 2017-es telepítésekor válassza ki az **Azure-fejlesztési számítási feladatot.**

  * [Microsoft Azure SDK for .NET (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/). További információ az [Azure SDK for .NET](/dotnet/azure/intro) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * A Azure Logic Apps tools for the Visual Studio legújabb Visual Studio a kívánt verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](/visualstudio/ide/finding-and-using-visual-studio-extensions). Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Internet-hozzáférés a beágyazott Logikaialkalmazás-tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége, hogy erőforrásokat hoz létre az Azure-ban, és beolvassa a tulajdonságokat és adatokat a logikai alkalmazás összekötőiből.

* A felhasználók által támogatott e-mail-Logic Apps, például Outlook for Microsoft 365, Outlook.com Gmail. Más szolgáltatók esetében tekintse át az [összekötők listáját itt.](/connectors/) Ez a példa az Office 365 Outlookot használja. Ha más szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, csak a G-Suite üzleti fiókok használhatja ezt az összekötőt korlátozás nélkül a logikai alkalmazásokban. Ha rendelkezik Gmail-felhasználói fiókkal, használhatja ezt az összekötőt csak bizonyos Google által jóváhagyott szolgáltatásokkal, vagy létrehozhat egy Google-ügyfélalkalmazást a Gmail-összekötővel való [hitelesítéshez.](/connectors/gmail/#authentication-and-bring-your-own-application) További információ: Adatbiztonsági és adatvédelmi szabályzatok [Google-összekötőkhöz a Azure Logic Apps.](../connectors/connectors-google-data-security-privacy-policy.md)

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott  IP-címekre [](logic-apps-limits-and-config.md#outbound) korlátozza a forgalmat, a tűzfalnak engedélyeznie kell a hozzáférést az Logic Apps szolgáltatás vagy a futásidő által használt bejövő és kimenő IP-címek számára is abban az Azure-régióban, ahol a logikai alkalmazás létezik. [](logic-apps-limits-and-config.md#inbound) Ha a logikai alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak hozzáférést kell engedélyeznie a felügyelt összekötő összes kimenő [IP-címéhez](logic-apps-limits-and-config.md#outbound) a logikai alkalmazás Azure-régiójában. [](../connectors/managed.md) [](/connectors/custom-connectors/)

<a name="azure-government"></a>

## <a name="set-up-visual-studio-for-azure-government"></a>Visual Studio beállítása az Azure Governmenthez

### <a name="visual-studio-2017"></a>Visual Studio 2017

Használhatja az [Azure Environment Selector](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/)Visual Studio bővítményt, amelyet letölthet és telepíthet a Visual Studio [Marketplace-ről.](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector)

### <a name="visual-studio-2019"></a>Visual Studio 2019

Az Azure Government előfizetések Azure Logic Apps való munkához hozzá kell adni egy felderítési végpontot [a Azure Government Cloudhoz](../azure-government/documentation-government-connect-vs.md)a Visual Studio. Mielőtt azonban bejelentkezik a Visual Studio Azure Government-fiókkal, át kell neveznie a felderítési végpont hozzáadása után létrehozott *JSON-fájlt* az alábbi lépésekkel:

1. Zárja be a Visual Studiót.

1. Keresse meg a létrehozott nevű `Azure U.S. Government-A3EC617673C6C70CC6B9472656832A26.Configuration` JSON-fájlt ezen a helyen:

   `%localappdata%\.IdentityService\AadConfigurations`
 
1. Nevezze át a JSON-fájlt a `AadProvider.Configuration.json` következőre: .

1. Indítsa újra a Visual Studiót.

1. Folytassa a bejelentkezési lépésekkel a Azure Government fiókjával.

A beállítás visszaállításához törölje a JSON-fájlt a következő helyen, és indítsa újra a Visual Studio:

`%localappdata%\.IdentityService\AadConfigurations\AadProvider.Configuration.json`

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/management/overview.md).

1. Indítsa el a Visual Studiót. Jelentkezzen be Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet. (Billentyűzet: Ctrl + Shift + N)

   ![Az Új > Projekt menüpont kiválasztása a Fájl menüben](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza **a Cloud** Azure Resource Group  >  **(Felhőbeli Azure-erőforráscsoport) lehetőséget.** Adjon nevet a projektnek, például a következőt:

   ![Azure erőforráscsoport-projekt létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Az erőforráscsoportok neve csak betűket, számokat, pontokat ( ), aláhúzásjeleket `.` ( `_` ), kötőjeleket ( ), és zárójeleket `-` ( , `(` `)` )  `.` tartalmazhat, de nem végződhet pontokkal ( ).
   >
   > Ha **a Felhő** vagy az **Azure-erőforráscsoport** nem jelenik meg, telepítse az Azure SDK for Visual Studio.

   Ha a 2019-es Visual Studio használja, kövesse az alábbi lépéseket:

   1. Az Új **projekt létrehozása mezőben** válassza ki az **Azure Resource Group** projektet a Visual C# vagy a Visual Basic. Kattintson a **Tovább** gombra.

   1. Adja meg a használni kívánt Azure-erőforráscsoport nevét és egyéb projektinformációt. Válassza a **Létrehozás** lehetőséget.

1. A sablonlistából válassza ki a **Logikai alkalmazás sablont.** Válassza az **OK** lehetőséget.

   ![Logikaialkalmazás-sablon kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Miután a Visual Studio létrehozta a projektet, megnyílik a Megoldáskezelő, és megjeleníti a megoldást. A megoldásban a **LogicApp.js** fájlban található fájl nem csak a logikaialkalmazás-definíciót tárolja, hanem Azure Resource Manager is, amely üzembe helyezéshez használható.

   ![A Megoldáskezelő az új logikai alkalmazással és az üzembe helyezési fájllal](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

Ha már megvan az Azure-erőforráscsoport-projekt, hozza létre a logikai alkalmazást az **Üres logikai alkalmazás sablonnal.**

1. A Megoldáskezelő nyissa megLogicApp.js **fájl helyi** menüjében található irányítópultot. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**. (Billentyűzet: Ctrl + L)

   ![A logikai alkalmazás .json-fájljának megnyitása a Logikaialkalmazás-tervezővel](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha a 2019-es Visual Studio nem ezt a parancsot Visual Studio, ellenőrizze, hogy a legújabb frissítésekkel Visual Studio.

   Visual Studio kéri az Azure-előfizetését és egy Azure-erőforráscsoportot a logikai alkalmazás és a kapcsolatok erőforrásainak létrehozásához és üzembe helyezéséhez.

1. Az **Előfizetés mezőben** válassza ki az Azure-előfizetését. Az **Erőforráscsoport mezőben** válassza az **Új létrehozása lehetőséget** egy másik Azure-erőforráscsoport létrehozásához.

   ![Azure-előfizetés, erőforráscsoport és erőforrás helyének kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Beállítás | Példaérték | Description |
   | ------- | ------------- | ----------- |
   | Felhasználói fiók | Fabrikam <br> sophia-owen@fabrikam.com | A fiók, amely a fiókba való bejelentkezve Visual Studio |
   | **Előfizetés** | Utólagos, használatalapú fizetés <br> (sophia-owen@fabrikam.com) | Az Azure-előfizetés és a társított fiók neve |
   | **Erőforráscsoport** | MyLogicApp-RG <br> (USA nyugati régiója) | A logikai alkalmazás erőforrásainak tárolására és üzembe helyezésére szolgáló Azure-erőforráscsoport és hely |
   | **Hely** | **Ugyanaz, mint az erőforráscsoport** | A logikai alkalmazás üzembe helyezésének helye és helye. A hely típusa lehet Egy Azure-régió vagy egy meglévő integrációs szolgáltatási [környezet (ISE).](connect-virtual-network-vnet-isolated-environment.md) <p>Ebben a rövid útmutatóban a helytípust a **Region** (Régió) és a location (Hely) beállításnál tartsa meg a Same as Resource Group (Ugyanaz, mint az **erőforráscsoport) beállításnál.** <p>**Megjegyzés:** Az erőforráscsoport-projekt létrehozása [](manage-logic-apps-with-visual-studio.md#change-location)után módosíthatja a hely típusát és helyét, de a különböző helytípus különböző módokon befolyásolja a logikai alkalmazást. |
   ||||

1. A Logic Apps Designer megnyit egy oldalt, amely egy bevezető videót és a gyakran használt eseményindítókat jeleníti meg. Görgessen le a videó mellett, és az eseményindítókat **a Sablonok részbe,** és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![„Üres logikai alkalmazás” kiválasztása](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának felépítése

Ezután adjon hozzá egy [RSS-eseményindítót,](../logic-apps/logic-apps-overview.md#logic-app-concepts) amely új hírcsatornaelem megjelenésekor aktiválódik. Minden logikai alkalmazás egy eseményindítóval indul, amely adott feltételek teljesültével aktiválódik. A Logic Apps-motor a trigger minden aktiválásakor létrehoz egy logikaialkalmazás-példányt, amely futtatja a munkafolyamatot.

1. A Logic App Designer keresőmezője alatt válassza az Összes **lehetőséget.** A keresőmezőbe írja be az "rss" (rss) adatokat. Az eseményindítók listájából válassza ki a következő eseményindítót: **Hírcsatornaelem közzétételekor**

   ![Logikai alkalmazás felépítése trigger és műveletek hozzáadásával](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Miután az eseményindító megjelent a tervezőben, fejezze be a logikai alkalmazás építését a rövid útmutatóban Azure Portal lépéseit követve, majd térjen vissza ehhez [a](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)cikkhez. Az elkészült logikai alkalmazás az alábbihoz hasonlít:

   ![Befejezett logikai alkalmazás](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Mentse a Visual Studio megoldását. (Billentyűparancs: Ctrl + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Logikai alkalmazás üzembe helyezése az Azure-ban

A logikai alkalmazás futtatása és tesztelése előtt telepítse az alkalmazást az Azure-ban a Visual Studio.

1. A Megoldáskezelő a projekt helyi menüjében válassza az Új **üzembe helyezése**  >  **lehetőséget.** Ha szükséges, jelentkezzen be Azure-fiókjával.

   ![A logikai alkalmazás üzemelő példányának létrehozása](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Ebben az üzembe helyezésben tartsa meg az alapértelmezett Azure-előfizetést, erőforráscsoportot és egyéb beállításokat. Válassza az **Üzembe helyezés** lehetőséget.

   ![Logikai alkalmazás üzembe helyezése Azure-erőforráscsoportra](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Ha **megjelenik** a Paraméterek szerkesztése mező, adja meg a logikai alkalmazás erőforrásnevét. Mentse a beállításokat.

   ![Adja meg a logikai alkalmazás üzemelő példányának nevét](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot.

   ![Üzembe helyezés állapotának kimenete](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Ha a kiválasztott összekötőknek bemenetre van szükségük, megnyílik egy PowerShell-ablak a háttérben, és kéri a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![PowerShell-ablak](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Az üzembe helyezés befejezése után a logikai alkalmazás a Azure Portal a megadott ütemezés szerint fut (percenként). Ha az eseményindító új hírcsatornaelemeket talál, aktiválódik az eseményindító, amely létrehoz egy munkafolyamat-példányt, amely futtatja a logikai alkalmazás műveletét. A logikai alkalmazás minden új elemről e-mailt küld. Ellenkező esetben, ha az eseményindító nem talál új elemeket, az eseményindító nem aktiválódik, és "kihagyja" a munkafolyamat példányosozását. A logikai alkalmazás az ellenőrzés előtt megvárja a következő időközt.

   Íme a logikai alkalmazás által küldött e-mailek mintája. Ha nem kap e-mailt, ellenőrizze a levélszemét mappát.

   ![Az Outlook minden új RSS-elemről e-mailt küld](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Gratulálunk, sikeresen felépítette és üzembe helyezett logikai alkalmazását a Visual Studio. A logikai alkalmazás kezeléséhez és az előzményeinek megtekintéséhez lásd a [logikai alkalmazások Visual Studióval történő kezelésével](../logic-apps/manage-logic-apps-with-visual-studio.md) foglalkozó cikket.

## <a name="add-new-logic-app"></a>Új logikai alkalmazás hozzáadása

Ha már van Azure-erőforráscsoport-projektje, a JSON-vázlat ablakban hozzáadhat egy új üres logikai alkalmazást a projekthez.

1. A Megoldáskezelő nyissa meg a `<logic-app-name>.json` fájlt.

1. A Nézet **menüben** válassza az **Egyéb Windows**  >  **JSON-vázlat lehetőséget.**

1. Ha erőforrást szeretne hozzáadni a sablonfájlhoz, válassza az **Erőforrás** hozzáadása lehetőséget a JSON-vázlat ablak tetején. Vagy a JSON-vázlat ablakban  nyissa meg az erőforrások helyi menüjét, és válassza az **Új erőforrás hozzáadása lehetőséget.**

   ![JSON-vázlat ablak](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. Az Erőforrás **hozzáadása párbeszédpanel** keresőmezőben keresse meg a et, majd válassza a Logikai `logic app` alkalmazás **lehetőséget.** Nevezze el a logikai alkalmazást, majd válassza a **Hozzáadás lehetőséget.**

   ![Erőforrás hozzáadása](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a logikai alkalmazással, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a logikai alkalmazás létrehozásához használt fiókkal.

1. A Azure Portal válassza az **Erőforráscsoportok** lehetőséget, vagy keresse meg és válassza **ki** az Erőforráscsoportok elemet bármelyik oldalon. Válassza ki a logikai alkalmazás erőforráscsoportját.

1. Az Áttekintés **lapon** válassza az **Erőforráscsoport törlése lehetőséget.** Megerősítésként adja meg az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

   ![„Erőforráscsoportok” > „Áttekintés” > „Erőforráscsoport törlése”](./media/quickstart-create-logic-apps-with-visual-studio/clean-up-resources.png)

1. Törölje a Visual Studio-megoldást a helyi számítógépről.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy logikai alkalmazást állított össze, helyezett üzembe és futtatott a Visual Studióval. Az alábbi cikkekből megtudhatja, hogyan lehet a logikai alkalmazások speciális üzembe helyezését Visual Studio kezelésével és elvégzésével kapcsolatban:

> [!div class="nextstepaction"]
> [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)
