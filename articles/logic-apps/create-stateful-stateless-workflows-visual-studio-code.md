---
title: Előzetes Logic Apps munkafolyamatok létrehozása a Visual Studio Code-ban
description: Automatizálási és integrációs forgatókönyvekhez használható munkafolyamatokat építhet ki és futtathat a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítmény segítségével.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 4010f7e2d0d20216107a45109056478694c940ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772504"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Állapot- és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban a Azure Logic Apps (előzetes verzió) bővítvekkel

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az [Azure Logic Apps Preview](logic-apps-overview-preview.md)kiadással automatizálási és [  integrációs  ](logic-apps-overview-preview.md#stateful-stateless) megoldásokat építhet ki az alkalmazások, adatok, felhőszolgáltatások és rendszerek számára, ha az Visual Studio Code-ban állapotalapú és állapot nélküli munkafolyamatokat is magában foglaló logikai alkalmazásokat hoz létre és futtat az Azure Logic Apps (előzetes verzió) bővítmény használatával. Ezzel az új logikaialkalmazás-típussal több, az újratervezett Azure Logic Apps Preview futtatókörnyezet által működtetett munkafolyamatot is felépíthet, amely hordozhatóságot, jobb teljesítményt és rugalmasságot biztosít a különböző üzemeltetési környezetekben, nem csak az Azure-ban, hanem a Docker-tárolókban is. További információ az új logikai alkalmazástípusról: Áttekintés a Azure Logic Apps [előzetes verzióról.](logic-apps-overview-preview.md)

![Képernyőkép a Visual Studio, logikaialkalmazás-projektről és munkafolyamatról.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

A Visual Studio Code-ban először hozzon létre egy  projektet, amelyben helyileg buildelheti és futtathatja a logikai alkalmazás munkafolyamatait a fejlesztési környezetben a Azure Logic Apps (előzetes verzió) bővítmény használatával. Először is hozzon létre egy új logic [ **app (előzetes verzió)**](create-stateful-stateless-workflows-azure-portal.md)erőforrást a Azure Portal-ban, de mindkét módszer lehetővé teszi a logikai alkalmazás azonos típusú üzemeltetési környezetekben való üzembe helyezését és futtatását.

Addig is létrehozhatja az eredeti logikaialkalmazás-típust. Bár a kód Visual Studio eltérőek az eredeti és az új logikaialkalmazás-típusok között, az Azure-előfizetés mindkét típust tartalmazhatja. Megtekintheti és elérheti az Összes üzembe helyezett logikai alkalmazást az Azure-előfizetésében, de az alkalmazások a saját kategóriáikba és szakaszaikba vannak rendezve.

Ez a cikk bemutatja, hogyan hozhatja létre a logikai alkalmazást és egy munkafolyamatot a Visual Studio Code-ban az Azure Logic Apps (előzetes verzió) bővítmény használatával és az alábbi magas szintű feladatok elvégzésével:

* Hozzon létre egy projektet a logikai alkalmazáshoz és a munkafolyamathoz.

* Eseményindító és művelet hozzáadása.

* Futtatás, tesztelés, hibakeresés és futtatás előzményeinek helyi áttekintése.

* Keresse meg a tűzfal-hozzáférés tartománynevének részleteit.

* Üzembe helyezés az Azure-ban, amely opcionálisan lehetővé teszi a Application Insights.

* Az üzembe helyezett logikai alkalmazás kezelése a Visual Studio Code-ban és a Azure Portal.

* Futtatás előzményeinek engedélyezése állapot nélküli munkafolyamatokhoz.

* Engedélyezze vagy nyissa meg a Application Insights után.

* Üzembe helyezés egy Olyan Docker-tárolóban, amely bárhol futtatható.

> [!NOTE]
> Az aktuális ismert problémákkal kapcsolatos információkért tekintse meg a Logic Apps nyilvános előzetes verzió ismert problémáit ismertető oldalt [a GitHubon.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

## <a name="prerequisites"></a>Előfeltételek

### <a name="access-and-connectivity"></a>Hozzáférés és kapcsolat

* Az internethez való hozzáféréssel letöltheti a követelményeket, csatlakozhat az Visual Studio Code-ból az Azure-fiókjához, és közzéteheti az Visual Studio Code-ból az Azure-ba, egy Docker-tárolóba vagy más környezetbe.

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A cikkben használt példa logikai alkalmazás felépítéséhez egy munkahelyi vagy iskolai Microsoft-fiókot használó Office 365 Outlook e-mail-fiókra lesz szüksége a bejelentkezéshez.

  Ha egy másik, [az Azure Logic Apps](/connectors/)által támogatott e-mail-összekötőt (például Outlook.com vagy [Gmail)](../connectors/connectors-google-data-security-privacy-policy.md)használ, akkor is követheti a példát, és az általános általános lépések ugyanazok, de a felhasználói felület és a beállítások bizonyos módokon eltérhetnek. Ha például a Outlook.com összekötőt használja, a személyes Microsoft-fiók használhatja a bejelentkezéshez.

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>Tárolási követelmények

#### <a name="windows"></a>Windows

Logikaialkalmazás-projekt helyi felépítéséhez és futtatásához a Visual Studio Code-ban Windows használata esetén kövesse az alábbi lépéseket az Azure Storage Emulator beállításhoz:

1. Töltse le és telepítse az [Azure Storage Emulator 5.10-et.](https://go.microsoft.com/fwlink/p/?linkid=717179)

1. Ha még nem rendelkezik ilyen alkalmazással, telepítenie kell egy helyi SQL DB-t, például az [ingyenes SQL Server 2019 Express Editiont,](https://go.microsoft.com/fwlink/p/?linkid=866658)hogy az emulátor futtatható legyen.

   További információ: [Az Azure Storage Emulator használata fejlesztéshez és teszteléshez.](../storage/common/storage-use-emulator.md)

1. A projekt futtatása előtt indítsa el az emulátort.

   ![Képernyőkép az Azure Storage Emulator futtatásáról.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS és Linux

A logikaialkalmazás-projekt helyi létrehozásához és futtatásához a Visual Studio Code-ban macOS vagy Linux használata esetén kövesse az alábbi lépéseket egy Azure Storage-fiók létrehozásához és beállításhoz.

> [!NOTE]
> A Visual Studio Code tervezője jelenleg nem működik Linux operációs rendszeren, de továbbra is futtathat buildet, futtathat és üzembe helyezhet olyan logikai alkalmazásokat, amelyek az Logic Apps Preview-alapú futásidejű alkalmazást használják Linux-alapú virtuális gépeken. Logikai alkalmazásait jelenleg windowsos vagy macOS rendszeren a Visual Studio Code-ban építheti ki, majd linuxos virtuális gépen helyezheti üzembe.

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és hozzon létre egy [Azure Storage-fiókot,](../storage/common/storage-account-create.md?tabs=azure-portal)amely a Azure Functions. [](../azure-functions/storage-considerations.md)

1. A tárfiók menüjében a Beállítások **alatt válassza** a Hozzáférési **kulcsok lehetőséget.**

1. A Hozzáférési **kulcsok panelen** keresse meg és másolja ki a tárfiók kapcsolati sztringet, amely az alábbi példához hasonlóan néz ki:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Képernyőkép az Azure Portal tárfiók hozzáférési kulcsait és a kapcsolati sztringet átmásolva.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   További információ: [Manage storage account keys (Tárfiókkulcsok kezelése).](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Mentse a kapcsolati sztringet egy biztonságos helyre. Miután létrehozott egy logikaialkalmazás-projektet a Visual Studio Code-ban, hozzá kell adni a sztringet a **local.settings.js** fájl gyökérszintű mappájában található fájlhoz.

   > [!IMPORTANT]
   > Ha Docker-tárolóban tervezi az üzembe helyezést, ezt a kapcsolati sztringet is használnia kell az üzembe helyezéshez használt Docker-fájllal. Éles forgatókönyvek esetén ügyeljen arra, hogy az ilyen titkos kulcsokat és bizalmas információkat védje és védje, például egy kulcstartót használva.
  
### <a name="tools"></a>Eszközök

* [Visual Studio Code 1.30.1 (2019. január)](https://code.visualstudio.com/)vagy újabb verzió, amely ingyenes. Továbbá töltse le és telepítse ezeket az eszközöket a Visual Studio Code-hoz, ha még nincsenek telepítve:

  * [Azure-fiókbővítmény,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)amely egy közös Azure-bejelentkezést és előfizetés-szűrést biztosít a Code összes többi Azure-Visual Studio számára.

  * [C# a Visual Studio kódbővítményhez,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)amely lehetővé teszi az F5 funkcióval a logikai alkalmazás futtatását.

  * [Azure Functions Core Tools 3.0.3245-ös](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) vagy újabb verziót használja a Microsoft Installer (MSI) verziójával, amely a `func-cli-3.0.3245-x*.msi` következő: .

    Ezek az eszközök tartalmazzák ugyanannak a futtatásnak a verzióját, amely a Azure Functions futtatás, amelyet az előzetes verziójú bővítmény a Visual Studio használ.

    > [!IMPORTANT]
    > Ha a telepítés korábbi, mint ezek a verziók, először távolítsa el ezt a verziót, vagy győződjön meg arról, hogy a PATH környezeti változó a letöltött és telepített verzióra mutat.

  * [Azure Logic Apps (előzetes verzió) bővítmény a Visual Studio Code-hez.](https://go.microsoft.com/fwlink/p/?linkid=2143167) Ez a bővítmény lehetővé teszi logikai alkalmazások létrehozására, ahol állapot-alapú és állapot nélküli munkafolyamatokat hozhat létre, amelyek helyileg futnak az Visual Studio Code-ban, majd ezeket a logikai alkalmazásokat közvetlenül az Azure-ban vagy Docker-tárolókban helyezheti üzembe.

    Jelenleg az eredeti és a nyilvános Azure Logic Apps is telepítve lehet a Visual Studio Code-ban. Bár a fejlesztési élmény bizonyos módokon eltér a bővítmények között, az Azure-előfizetés a bővítményekkel létrehozott logikaialkalmazás-típusokat is tartalmazhatja. Visual Studio Code megjeleníti az Azure-előfizetésben üzembe helyezett logikai alkalmazásokat, de különböző  szakaszokba rendezi őket bővítménynevek, Logic Apps és Azure Logic Apps **(előzetes verzió) alapján.**

    > [!IMPORTANT]
    > Ha a korábbi privát előzetes verziójú bővítménysel hozott létre logikaialkalmazás-projekteket, ezek a projektek nem fognak működni a nyilvános előzetes verziójú bővítővel. Ezeket a projekteket azonban át is telepítheti a privát előzetes verziójú bővítmény eltávolítása, a társított fájlok törlése és a nyilvános előzetes verziójú bővítmény telepítése után. Ezután létrehoz egy új projektet a Visual Studio Code-ban, és átmásolja a korábban létrehozott logikai alkalmazás **workflow.definition** fájlját az új projektbe. További információ: Áttelepítés a privát előzetes [verziójú bővítményből.](#migrate-private-preview)
    > 
    > Ha a korábbi nyilvános előzetes verziójú bővítménysel hozott létre logikaialkalmazás-projekteket, a migrálási lépések nélkül folytathatja a projektek használatát.

    **A Azure Logic Apps **(előzetes verzió)** bővítmény telepítéséhez kövesse az alábbi lépéseket:**

    1. A Visual Studio Code bal oldali eszköztárán válassza a **Bővítmények lehetőséget.**

    1. A bővítmények keresőmezőbe írja be a következőt: `azure logic apps preview` . Az eredmények listájából válassza a Azure Logic Apps **(előzetes verzió)** **>** **Telepítése lehetőséget.**

       A telepítés befejezése után az Előnézet bővítmény megjelenik a **Bővítmények: Telepítve listában.**

       ![Képernyőkép a Visual Studio Code telepített bővítménylistáról, aláhúzva a "Azure Logic Apps (előzetes verzió)" kiterjesztéssel.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > Ha a bővítmény nem jelenik meg a telepített listában, próbálja meg újraindítani a Visual Studio Code-ban.

* A JavaScriptet használó Beágyazott kódműveleti művelethez telepítseNode.js [10.x.x, 11.x.x vagy 12.x.x](https://nodejs.org/en/download/releases/)verziókat. [](../logic-apps/logic-apps-add-run-inline-code.md)

  > [!TIP] 
  > Windows esetén töltse le az MSI-verziót. Ha ehelyett a ZIP-verziót használja, manuálisan kell elérhetővé Node.js az operációs rendszer PATH környezeti változóját használva.

* Webhook-alapú eseményindítók és műveletek helyi futtatásához( például a beépített [HTTP-webhook-eseményindítóhoz)](../connectors/connectors-native-webhook.md)a Visual Studio Code-ban be kell állítania a visszahívási [URL-cím továbbítását.](#webhook-setup)

* A cikkben létrehozott példa logikai alkalmazás teszteléséhez olyan eszközre van szükség, amely hívásokat küldhet a Kérés eseményindítónak, amely a példaként használt logikai alkalmazás első lépése. Ha még nincs ilyen eszköze, letöltheti, telepítheti és használhatja a [Postman eszközt.](https://www.postman.com/downloads/)

* Ha a logikai alkalmazást a használatával támogató beállításokkal hozza létre és [Application Insights,](../azure-monitor/app/app-insights-overview.md)engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor is meg lehet tenni, ha a logikai alkalmazást az Visual Studio kódból vagy az üzembe helyezés után telepíti. Szüksége lesz egy Application Insights-példányra, de ezt az [](../azure-monitor/app/create-workspace-resource.md)erőforrást előre is létrehozhatja a logikai alkalmazás üzembe helyezésekor vagy az üzembe helyezés után.

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>Áttelepítés privát előzetes verziójú bővítményről

A Azure Logic Apps (privát előzetes **verzió)** bővítővel létrehozott logikaialkalmazás-projektek nem fognak működni a Nyilvános előzetes verzió bővítménysel. Ezeket a projekteket azonban új projektekbe is átemelheti az alábbi lépésekkel:

1. Távolítsa el a privát előzetes verziójú bővítményt.

1. Törölje az összes társított bővítménycsomagot és NuGet-csomagmappát a következő helyeken:

   * A **Microsoft.Azure.Functions.ExtensionBundle.Workflows** mappa, amely a korábbi bővítménycsomagokat tartalmazza, és a következő két elérési úton található:

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * A **microsoft.azure.workflows.webjobs.extension** mappa, amely a privát előzetes verziójú bővítmény [NuGet-gyorsítótára,](/nuget/what-is-nuget) és a következő elérési úton található:

     `C:\Users\{userName}\.nuget\packages`

1. Telepítse a **Azure Logic Apps (előzetes verzió)** bővítményt.

1. Hozzon létre egy új projektet a Visual Studio Code-ban.

1. Másolja a korábban létrehozott logikai alkalmazás **workflow.definition** fájlját az új projektbe.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>A Visual Studio Code telepítése

1. Annak érdekében, hogy minden bővítmény megfelelően legyen telepítve, töltse be újra vagy indítsa újra a Visual Studio Code-hoz.

1. Győződjön meg arról, Visual Studio Code automatikusan megkeresi és telepíti a bővítményfrissítéseket, hogy az előzetes verziójú bővítmény megkapja a legújabb frissítéseket. Ellenkező esetben manuálisan kell eltávolítania az elavult verziót, és telepítenie kell a legújabb verziót.

   1. A Fájl **menüben** válassza a Beállítások  **>** **lehetőséget.**

   1. A User **(Felhasználó) lapon** válassza a Features  **>** **Extensions (Funkciók bővítményei) lapot.**

   1. Győződjön meg arról, **hogy az Automatikus frissítések és** az Automatikus frissítés jelölőnégyzet **be** van jelölve.

Emellett alapértelmezés szerint a következő beállítások vannak engedélyezve és beállítva a Logic Apps előzetes verziójú bővítményhez:

* **Azure Logic Apps V2: Project Runtime**, amely **a ~3-as verzióra van beállítva**

  > [!NOTE]
  > Ez a verzió szükséges a beágyazott [kódműveletekkel kapcsolatos műveletekhez.](../logic-apps/logic-apps-add-run-inline-code.md)

* **Azure Logic Apps V2: Kísérleti nézetkezelő**, amely lehetővé teszi a legújabb tervezőt a Visual Studio Code-ban. Ha problémákat tapasztal a tervezőben, például elemeket húz vagy húz át, kapcsolja ki ezt a beállítást.

A beállítások kereséséhez és megerősítéséhez kövesse az alábbi lépéseket:

1. A Fájl **menüben** válassza a Beállítások  **>** **lehetőséget.**

1. A Felhasználó **lapon** válassza a Bővítmények Azure Logic Apps **>**  **>** **(előzetes verzió) lapot.**

   Itt találhatja meg például a **Azure Logic Apps V2: Project Runtime** beállítást, vagy használhatja a keresőmezőt más beállítások kereséséhez:

   ![A "Visual Studio Azure Logic Apps (előzetes verzió)" bővítmény kódbeállításait bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

1. A kód Visual Studio sávon válassza az Azure ikont.

   ![Képernyőkép a Visual Studio tevékenységsávról és a kiválasztott Azure ikonról.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Az Azure panelen az **Azure: Logic Apps (előzetes verzió)** alatt válassza **a Bejelentkezés az Azure-ba lehetőséget.** Amikor megjelenik Visual Studio Kódhitelesítés lap, jelentkezzen be Azure-fiókjával.

   ![Képernyőkép az Azure panelről és az Azure-bejelentkezéshez kiválasztott hivatkozásról.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Miután bejelentkezik, az Azure panelen megjelenik az Azure-fiókjában az előfizetések. Ha a nyilvánosan kiadott bővítmény is elérhető, a **bővítménysel** létrehozott logikai alkalmazásokat a Logic Apps szakaszban találja, nem pedig a **Logic Apps (előzetes verzió)** szakaszban.
   
   Ha a várt előfizetések nem jelennek meg, vagy azt szeretné, hogy a panel csak bizonyos előfizetéseket mutasson, kövesse az alábbi lépéseket:

   1. Az előfizetések listájában helyezze a mutatót az első előfizetés mellé, amíg meg nem jelenik az **Előfizetések** kiválasztása gomb (szűrő ikon). Válassza a szűrő ikont.

      ![Képernyőkép az Azure panelről és a kiválasztott szűrőikonról.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Vagy a kód Visual Studio válassza ki Azure-fiókját. 

   1. Amikor megjelenik egy másik előfizetési lista, válassza ki a kívánt előfizetéseket, majd válassza az **OK gombot.**

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Helyi projekt létrehozása

A logikai alkalmazás létrehozása előtt hozzon létre egy helyi projektet, hogy kezelni, futtatni és üzembe tudja helyezni a logikai alkalmazást a Visual Studio Code-ból. A mögöttes projekt hasonló egy Azure Functions projekthez, más néven függvényalkalmazás-projekthez. Ezek a projekttípusok azonban elkülönülnek egymástól, így a logikai alkalmazások és a függvényalkalmazások nem létezhetnek ugyanabban a projektben.

1. A számítógépen hozzon létre *egy* üres helyi mappát, amely a Code-ban később létrehoz majd Visual Studio projekthez.

1. A Visual Studio Code-ban zárja be az any és az összes megnyitott mappát.

1. Az Azure panelen az **Azure: Logic Apps (előzetes verzió)** mellett válassza a **Create New Project** (Új projekt létrehozása) lehetőséget (egy mappát és egy villám ikont megjelenítő ikon).

   ![Képernyőkép az Azure panel eszköztárának "Új projekt létrehozása" elemének beállításával.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Ha Windows Defender tűzfal a hálózati hozzáférés megadását kéri, ami az Visual Studio Code, a esetében pedig a Azure Functions Core Tools, válassza a Magánhálózatok, például a saját otthoni vagy munkahelyi hálózatOm Hozzáférés engedélyezése `Code.exe` `func.exe`  **>** lehetőséget.

1. Keresse meg azt a helyet, ahol létrehozta a projektmappát, válassza ki a mappát, és folytassa.

   ![Képernyőkép a "Mappa kiválasztása" párbeszédpanelről, az újonnan létrehozott projektmappával és a kijelölt "Kiválasztás" gombbal.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. A megjelenő sablonok listájában válassza az **Állapot-** szerinti munkafolyamat vagy az **Állapot nélküli munkafolyamat lehetőséget.** Ebben a példában az **Állapot- szerinti munkafolyamat lehetőséget választjuk.**

   ![A munkafolyamat-sablonok listáját a "Stateful Workflow" (Állapot- szerinti munkafolyamat) beállítással bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Adja meg a munkafolyamat nevét, majd nyomja le az Enter billentyűt. Ez a példa `Fabrikam-Stateful-Workflow` a nevet használja névként.

   ![Képernyőkép az "Új állapot- szerinti munkafolyamat létrehozása (3/4)" mezőről és a "Fabrikam-Stateful-Workflow" munkafolyamat nevéről.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code befejezi a projekt létrehozását, és megnyitja **a** workflow.jsfájlban található fájlt a kódszerkesztőben.

   > [!NOTE]
   > Ha a rendszer arra kéri, hogy válassza  ki a projekt megnyitásának mikéntjét, válassza a Megnyitás az aktuális ablakban lehetőséget, ha a projektet az aktuális Visual Studio code ablakban szeretné megnyitni. Új kódpéldány megnyitásához Visual Studio Megnyitás **új ablakban lehetőséget.**

1. A Visual Studio nyissa meg az Explorer panelt, ha még nincs megnyitva.

   Az Explorer panelen megjelenik a projekt, amely már automatikusan létrehozott projektfájlokat is tartalmaz. A projekt például rendelkezik egy mappával, amely a munkafolyamat nevét jeleníti meg. Ebben a mappában a **workflow.jsfájlban** található fájl tartalmazza a munkafolyamat mögöttes JSON-definícióját.

   ![Képernyőkép az Explorer panelről a projektmappával, a munkafolyamat mappával és a "workflow.js" fájllal.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. MacOS vagy Linux használata esetén a következő lépésekkel állíthatja be a tárfiókhoz való hozzáférést, amelyek a projekt helyi futtatásához szükségesek:

   1. A projekt gyökérmappában nyissa meg alocal.settings.js **fájlban.**

      ![Képernyőkép az Explorer panelről és local.settings.jsfájlról a projektben.](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. Cserélje le a tulajdonság értékét a tárfiók korábban mentett kapcsolati sztringjára, `AzureWebJobsStorage` például:

      Előtte:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      Utána:

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

      > [!IMPORTANT]
      > Éles forgatókönyvek esetén ügyeljen arra, hogy az ilyen titkos kulcsokat és bizalmas információkat védje és védje, például egy kulcstartót használva.

   1. Amikor végzett, mentse a módosításokat.

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>Beépített összekötők írásának engedélyezése

Az előzetes kiadás extenzitási keretrendszerével bármilyen szolgáltatáshoz létrehozhat saját beépített [összekötőket.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) A beépített összekötőkhöz ,például az Azure Service Bus-hoz és a SQL Server-hoz hasonlóan ezek az összekötők nagyobb átviteli sebességet, alacsony késést és helyi kapcsolatot biztosítanak, és natív módon futnak ugyanabban a folyamatban, mint az előzetes verziójú futtatás.

A szerzői funkció jelenleg csak a Visual Studio Code-ban érhető el, de alapértelmezés szerint nincs engedélyezve. Ezen összekötők létrehozásához először konvertálja a projektet csomagalapú bővítményről (Node.js) NuGet-csomagalapúra (.NET).

> [!IMPORTANT]
> Ez egy egy egyértes művelet, amely nem vonható vissza.

1. Az Explorer panelen, a projekt gyökerénél helyezze az egérmutatót az összes többi fájl és mappa alatti üres területre, nyissa meg a helyi menüt, és válassza a **Konvertálás Nuget-alapú logikaialkalmazás-projektre lehetőséget.**

   ![Képernyőkép az Explorer panelről, ahol a projekt helyi menüje üres területről nyílik meg a projektablakban.](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. Amikor megjelenik a kérdés, erősítse meg a projekt konvertálását.

1. A folytatáshoz tekintse át és kövesse a Running [Anywhere - Built-in connector extensibility (Bárhol](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)futó Azure Logic Apps – beépített összekötők extenzibilitása) cikkben található lépéseket.

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>A munkafolyamat-definíciós fájl megnyitása a tervezőben

1. A következő parancs futtatásával ellenőrizze a számítógépre telepített verziókat:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Ha 5.x .NET Core SDK van, ez a verzió megakadályozhatja a logikai alkalmazás mögöttes munkafolyamat-definíciójának megnyitását a tervezőben. A verzió eltávolítása helyett a projekt gyökérmappában hozzon létre egy **global.js-t** a fájlon, amely a .NET Core runtime 3.x 3.x verziójára hivatkozik, amely a 3.1.201-esnél későbbi, például:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Győződjön meg arról,  hogy explicit módonglobal.jsa projekt gyökérmappa fájljában található fájlt a Code Visual Studio belül. Ellenkező esetben a tervező nem nyílik meg.

1. Bontsa ki a munkafolyamat projektmappát. Nyissa meg **workflow.jsfájlt** a helyi menüben, és válassza a Megnyitás **a tervezőben lehetőséget.**

   ![Képernyőkép az Explorer panelről és a fájlban workflow.jsablakról, a "Megnyitás a tervezőben" lehetőség kijelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. Az **Összekötők engedélyezése** az Azure-ban listában válassza az **Összekötők** használata az Azure-ból lehetőséget, amely az Azure-ban elérhető és üzembe helyezett összes felügyelt összekötőre vonatkozik, nem csak az Azure-szolgáltatások összekötőire.

   ![Képernyőkép az Explorer panelről, megnyitott "Összekötők engedélyezése az Azure-ban" listával és az "Azure-beli összekötők használata" lehetőség kijelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > Az állapot nélküli munkafolyamatok [](../connectors/managed.md)jelenleg csak az Azure-ban üzembe helyezett felügyelt összekötőkhöz támogatják a műveleteket, az eseményindítókhoz nem.  Bár lehetősége van engedélyezni az összekötőket az Azure-ban az állapot nélküli munkafolyamathoz, a tervező nem mutatja a kiválasztott felügyelt összekötő eseményindítóit.

1. Az Előfizetés **kiválasztása listából** válassza ki a logikaialkalmazás-projekthez használni kívánt Azure-előfizetést.

   ![Képernyőkép az Explorer panelről, az "Előfizetés kiválasztása" mezővel és az előfizetés kijelölésével.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. Az erőforráscsoportok listájában válassza az **Új erőforráscsoport létrehozása lehetőséget.**

   ![Képernyőkép az Explorer panelről, az erőforráscsoportok listájával és az "Új erőforráscsoport létrehozása" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Adja meg az erőforráscsoport nevét, majd nyomja le az Enter billentyűt. Ez a példa a következőt használja: `Fabrikam-Workflows-RG`.

   ![Képernyőkép az Explorer panelről és az erőforráscsoport neve mezőről.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. A helyek listájában keresse meg és válassza ki az erőforráscsoport és az erőforrások létrehozásakor használni kívánt Azure-régiót. Ez a példa az USA **nyugati középső régióját használja.**

   ![Képernyőkép az Explorer panelről a helyek listájával és az "USA nyugati középső régiója" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   A lépés végrehajtása után a Visual Studio Code megnyitja a munkafolyamat-tervezőt.

   > [!NOTE]
   > Amikor Visual Studio Code elindítja a munkafolyamat tervezési API-ját, előfordulhat, hogy megjelenik egy üzenet, amely szerint az indítás néhány másodpercig is eltelhet. Ezt az üzenetet figyelmen kívül hagyhatja, vagy kattintson az **OK gombra.**
   >
   > Ha a tervező nem nyílik meg, tekintse át a hibaelhárítási szakaszt, és [a Designer nem tudja megnyitni a következőt:](#designer-fails-to-open).

   Miután megjelenik a tervező, megjelenik **a** Művelet kiválasztása üzenet a tervezőben, és alapértelmezés szerint ki van választva, amely a Művelet **hozzáadása panelt jeleníti** meg.

   ![A munkafolyamat-tervezőt bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Ezután adjon [hozzá egy eseményindítót és műveleteket](#add-trigger-actions) a munkafolyamathoz.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Eseményindító és műveletek hozzáadása

A tervező megnyitása után megjelenik **a Művelet** kiválasztása üzenet, amely alapértelmezés szerint ki van választva. Most már elkezdheti létrehozni a munkafolyamatot egy eseményindító és műveletek hozzáadásával.

A példában használt munkafolyamat ezt az eseményindítót és a következő műveleteket használja:

* A beépített [](../connectors/connectors-native-reqres.md)Kérés eseményindító, a **HTTP-kérés** fogadása esetén, amely fogadja a bejövő hívásokat vagy kéréseket, és létrehoz egy végpontot, amelyet más szolgáltatások vagy logikai alkalmazások hívhatnak meg.

* Az [Office 365 Outlook művelete,](../connectors/connectors-create-api-office365-outlook.md) **e-mail küldése.**

* A beépített [Response művelet,](../connectors/connectors-native-reqres.md)amellyel választ küldhet, és adatokat küldhet vissza a hívónak.

### <a name="add-the-request-trigger"></a>A Kérés eseményindító hozzáadása

1. A tervező mellett, az Eseményindító hozzáadása  panelen, a Művelet kiválasztása  keresőmező alatt győződjön meg arról, hogy a Beépített lehetőség van kiválasztva, így kiválaszthat egy natívan futó eseményindítót. 

1. A Művelet **kiválasztása keresőmezőbe** írja be a következőt: , majd válassza ki a HTTP-kérések esetén nevű beépített Kérés `when a http request` **eseményindítót.**

   ![Képernyőkép a munkafolyamat-tervezőről és az **Eseményindító hozzáadása** panelről a "HTTP-kérés érkezik" eseményindítóval.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Amikor az eseményindító megjelenik a tervezőben, megnyílik az eseményindító részletek panelje, amely megjeleníti az eseményindító tulajdonságait, beállításait és egyéb műveleteket.

   ![Képernyőkép a munkafolyamat-tervezőről, a "HTTP-kérés érkezik" eseményindítóval, és az eseményindító részletei panel meg van nyitva.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ha a Részletek panel nem jelenik meg, győződjön meg arról, hogy az eseményindító ki van választva a tervezőben.

1. Ha törölnie kell egy elemet a tervezőből, kövesse az alábbi lépéseket a [tervező elemeinek törléséhez.](#delete-from-designer)

### <a name="add-the-office-365-outlook-action"></a>Az Office 365 Outlook művelet hozzáadása

1. A tervezőben a hozzáadott eseményindító alatt válassza az Új **lépés lehetőséget.**

   A **tervezőben** megjelenik a Művelet kiválasztása kérdés, majd megnyitható **a** Művelet hozzáadása panel, hogy kiválasztja a következő műveletet.

1. A Művelet **hozzáadása panel** Művelet  kiválasztása keresőmezője alatt válassza az **Azure** lehetőséget, hogy megkeressen és kiválasztson egy műveletet az Azure-ban üzembe helyezett felügyelt összekötőhöz.

   Ez a példa kiválasztja és használja az Office 365 Outlook **E-mail küldése (V2) műveletét.**

   ![Képernyőkép a munkafolyamat-tervezőről és az **Művelet hozzáadása** panelről, az Office 365 Outlook "E-mail küldése" műveletének bejelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. A művelet részleteit tartalmazó panelen válassza a **Bejelentkezés** lehetőséget, hogy kapcsolatot hozhasson létre az e-mail-fiókjával.

   ![Képernyőkép a munkafolyamat-tervezőről és az **E-mail küldése (V2)** panelről a "Bejelentkezés" beállítással.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Amikor Visual Studio Code jóváhagyást kér az e-mail-fiókjához való hozzáféréshez, válassza a **Megnyitás lehetőséget.**

   ![A hozzáférést engedélyező Visual Studio kódablakot bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > A jövőbeli kérések elkerülése érdekében válassza a Megbízható tartományok **konfigurálása** lehetőséget, hogy a hitelesítési lapot megbízható tartományként adja hozzá.

1. Kövesse az alábbi utasításokat a bejelentkezéshez, a hozzáférésük engedélyezése és a kódhoz való visszatérés Visual Studio érdekében.

   > [!NOTE]
   > Ha túl sok idő telik el a kérések befejezése előtt, a hitelesítési folyamat időkorrelkalmaz, és meghiúsul. Ebben az esetben térjen vissza a tervezőhöz, és próbálja meg újra bejelentkezni a kapcsolat létrehozásához.

1. Amikor a Azure Logic Apps (előzetes verzió) bővítmény jóváhagyást kér az e-mail-fiókjához való hozzáféréshez, válassza a **Megnyitás lehetőséget.** A következő parancssorban engedélyezze a hozzáférést.

   ![Képernyőkép az előzetes verziójú bővítmény hozzáférési kérésének megtekintéséről.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > A jövőbeli kérések elkerülése érdekében válassza a Ne kérdezzen rá újra erre **a bővítményre lehetőséget.**

   Miután Visual Studio Code létrehozza a kapcsolatot, egyes összekötők a következő üzenetet mutatják: `The connection will be valid for {n} days only` . Ez az időkorlát csak arra az időtartamra vonatkozik, amíg a logikai alkalmazást a Visual Studio Code-ban. Az üzembe helyezés után ez a korlát már nem érvényes, mert a logikai alkalmazás futásidőben is képes hitelesítést végezni az automatikusan engedélyezett, rendszer által hozzárendelt [felügyelt identitásával.](../logic-apps/create-managed-service-identity.md) Ez a felügyelt identitás eltér a kapcsolat létrehozásakor használt hitelesítő adatoktól vagy kapcsolati sztringtől. Ha letiltja ezt a rendszer által hozzárendelt felügyelt identitást, a kapcsolatok futásidőben nem fognak működni.

1. Ha a tervezőben nem **jelenik** meg az E-mail küldése művelet, válassza ki ezt a műveletet.

1. A művelet részleteket tartalmazó panelének Paraméterek **lapján** adja meg a művelethez szükséges adatokat, például:

   ![Képernyőkép a munkafolyamat-tervezőről az Office 365 Outlook "E-mail küldése" műveletének részleteivel.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Ide:** | Yes | <*saját e-mail-címe*> | Az e-mail címzettje, amely lehet az Ön e-mail-címe tesztelési célból. Ebben a példában a fiktív e-mailt `sophiaowen@fabrikam.com` használjuk. |
   | **Tárgy** | Yes | `An email from your example workflow` | Az e-mail tárgya |
   | **Törzs** | Yes | `Hello from your example workflow!` | Az e-mail törzsének tartalma |
   ||||

   > [!NOTE]
   > Ha módosításokat szeretne eszközül módosítani a **Beállítások,** Statikus  eredmény **vagy** Futtatás utána  lapon található részletek panelen, a módosítások véglegesítéséhez válassza a Kész gombot, mielőtt lapfüleket vált, vagy fókuszt módosít a tervezőre. Ellenkező esetben Visual Studio Code nem fogja megtartani a módosításokat. További információért tekintse át a Logic Apps előzetes verzió ismert problémáit ismertető oldalt [a GitHubon.](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

1. A tervezőben válassza a **Mentés lehetőséget.**

> [!IMPORTANT]
> Webhook-alapú eseményindítót vagy műveleteket (például a beépített [HTTP-webhook-eseményindítót](../connectors/connectors-native-webhook.md)vagy műveletet) használó munkafolyamat helyi futtatásához engedélyeznie kell ezt a képességet a webhook visszahívási URL-címéhez való továbbítás [beállításával.](#webhook-setup)

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>Helyileg futó webhookok engedélyezése

Ha webhookalapú eseményindítót vagy műveletet (például **HTTP-webhookot)** használ egy Azure-ban futó logikai alkalmazással, az Logic Apps-futtatókörnyezet előfizet a szolgáltatásvégpontra egy visszahívási URL-cím a végponttal való generálása és regisztrálása által. Az eseményindító vagy művelet ezután megvárja, amíg a szolgáltatásvégpont meg nem hívja az URL-címet. Ha azonban az Visual Studio Kódban dolgozik, a létrehozott visszahívási URL-cím a következővel kezdődik: `http://localhost:7071/...` . Ez az URL-cím a localhost kiszolgálóra mutat, amely privát, így a szolgáltatásvégpont nem tudja ezt az URL-címet hívni.

A Visual Studio Code-ban a webhook-alapú eseményindítók és műveletek helyi futtatásához be kell állítania egy nyilvános URL-címet, amely elérhetővé teszi a localhost kiszolgálót, és biztonságosan továbbítja a hívásokat a szolgáltatásvégpontról a webhook visszahívási URL-címére. Használhat olyan továbbító szolgáltatást és eszközt, mint az [**ngrok,**](https://ngrok.com/)amely megnyit egy HTTP-alagutat a localhost porthoz, vagy használhatja a saját eszközét.

#### <a name="set-up-call-forwarding-using-ngrok"></a>Hívás-továbbítás beállítása **ngrok használatával**

1. [Ha még nem fiókja **van, regisztráljon egy ngrok-fiókra.**](https://dashboard.ngrok.com/signup) Ellenkező esetben [jelentkezzen be a fiókjába.](https://dashboard.ngrok.com/login)

1. Szerezze be személyes hitelesítési jogkivonatát, amelyre **az ngrok-ügyfélnek** szüksége van a fiókhoz való csatlakozáshoz és a hozzáférés hitelesítéséhez.

   1. A hitelesítési [jogkivonat oldalának megkeresése](https://dashboard.ngrok.com/auth/your-authtoken)a fiók irányítópultjának menüjében bontsa ki a Hitelesítés elemet, majd válassza a Saját hitelesítési **jogkivonat lehetőséget.**

   1. A **Saját hitelesítési jogkivonat mezőből** másolja a jogkivonatot egy biztonságos helyre.

1. Az [ **ngrok letöltési** oldalról](https://ngrok.com/download) vagy a fiók [irányítópultjáról](https://dashboard.ngrok.com/get-started/setup)töltse le a kívánt **ngrok-verziót,** és bontsa ki a .zip fájlt. További információ: [1. lépés: A telepítéséhez csomagolja ki.](https://ngrok.com/download)

1. Nyissa meg a parancssori eszközt a számítógépén. Keresse meg azt a helyet, ahol angrok.exe **található.**

1. Csatlakoztassa az **ngrok-ügyfelet** az **ngrok-fiókjához** a következő parancs futtatásával. További információ: [2. lépés: A fiók csatlakoztatása.](https://ngrok.com/download)

   `ngrok authtoken <your_auth_token>`

1. Nyissa meg a HTTP-alagutat a localhost 7071-es portjához a következő parancs futtatásával. További információ: [Step 3: Fire it up](https://ngrok.com/download).

   `ngrok http 7071`

1. A kimenetben keresse meg a következő sort:

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. Másolja és mentse a következő formátumú URL-címet: `http://<domain>.ngrok.io`

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>A továbbítási URL-cím beállítása az alkalmazásbeállításokban

1. A Visual Studio Code-ban a tervezőben adja hozzá a **HTTP + Webhook** eseményindítót vagy műveletet.

1. Amikor megjelenik a gazdagép végpontjának helyére vonatkozó kérdés, adja meg a korábban létrehozott továbbítási (átirányítási) URL-címet.

   > [!NOTE]
   > Ha figyelmen kívül hagyja a parancssort, megjelenik egy figyelmeztetés, amely szerint meg kell adnia a továbbítási URL-címet, ezért válassza a **Konfigurálás** lehetőséget, és adja meg az URL-címet. Miután végzett ezzel a lépéssel, a kérés nem fog újra megjelenik a további webhook-triggerek vagy -műveletek után, amelyek hozzáadhatóak.
   >
   > Ahhoz, hogy a parancssor ismét megjelenik, a projekt  gyökérszinten nyissa meg alocal.settings.jsa fájl helyi menüjében, és válassza a **Configure Webhook Redirect Endpoint (Webhook** átirányítási végpontjának konfigurálása) lehetőséget. Megjelenik a kérés, hogy meg tudja adni a továbbítási URL-címet.

   Visual Studio Code hozzáadja a továbbító URL-címet **local.settings.jsprojekt** gyökérmappa fájljában található fájlhoz. A objektumban megjelenik a nevű tulajdonság, és a továbbítási URL-címre van `Values` `Workflows.WebhookRedirectHostUri` beállítva, például:
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

Amikor először indít helyi hibakeresési munkamenetet, vagy hibakeresés nélkül futtatja a munkafolyamatot, a Logic Apps-runtime regisztrálja a munkafolyamatot a szolgáltatásvégponttal, és feliratkozik erre a végpontra a webhook-műveletek értesítése érdekében. A munkafolyamat következő futtatásakor a futásidő nem regisztrál vagy ír elő újra, mert az előfizetés-regisztráció már létezik a helyi tárolóban.

Ha leállítja egy helyileg futtatott webhook-alapú eseményindítókat vagy műveleteket használó munkafolyamat-futtatás hibakeresési munkamenetét, a meglévő előfizetés-regisztrációk nem törlődnek. A regisztráció törléséhez manuálisan kell eltávolítania vagy törölnie az előfizetés-regisztrációkat.

> [!NOTE]
> A munkafolyamat futásának elkezdését követően a terminálablakban az alábbi példához hasonló hibákat fog mutatni:
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> Ebben az esetben nyissa meg **local.settings.jsfájl** gyökérmappában található fájlt, és győződjön meg arról, hogy a tulajdonság beállítása `true` :
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>Hibakeresési töréspontok kezelése

Mielőtt futtatja és teszteli a logikai alkalmazás munkafolyamatát egy [](https://code.visualstudio.com/docs/editor/debugging#_breakpoints) hibakeresési  munkamenettel, töréspontokat állíthat be aworkflow.jsmunkafolyamat fájljában. Nincs szükség más beállításra. 

Jelenleg a töréspontok csak műveletekhez támogatottak, eseményindítókhoz nem. Minden műveletdefiníció a következő törésponthelyekkel rendelkezik:

* Állítsa be a kezdő töréspontot a művelet nevét bemutató sorban. Amikor ez a töréspont eléri a hibakeresési munkamenetet, a kiértékelés előtt áttekintheti a művelet bemenetét.

* Állítsa be a záró töréspontot a művelet záró kapcsos zárójelét **(} )** bemutató sorban. Amikor ez a töréspont eléri a hibakeresési munkamenetet, a művelet futtatása előtt áttekintheti a művelet eredményeit.

Töréspont hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg **workflow.jsa hibakeresési** munkafolyamat fájljában.

1. A töréspont beállításának sorában, a bal oldali oszlopban válassza az oszlopon belül lehetőséget. A töréspont eltávolításához válassza ki a töréspontot.

   Amikor elindítja a hibakeresési munkamenetet, a kódablak bal oldalán megjelenik a Futtatás nézet, felül pedig a Hibakeresés eszköztár.

   > [!NOTE]
   > Ha a Futtatás nézet nem jelenik meg automatikusan, nyomja le a Ctrl+Shift+D billentyűkombinációt.

1. A törésponttal kapcsolatos elérhető információk megtekintéséhez a Futtatás nézetben vizsgálja meg a **Változók panelt.**

1. A munkafolyamat végrehajtásának folytatásához a Hibakeresés eszköztáron válassza a **Folytatás** (lejátszás gomb) lehetőséget.

A munkafolyamat futtatása során bármikor felvehet és eltávolíthat töréspontokat. Ha azonban a futtatás **workflow.js** után frissíti a fájlban található adatokat, a töréspontok nem frissülnek automatikusan. A töréspontok frissítéséhez indítsa újra a logikai alkalmazást.

Általános információkért lásd: Töréspontok – [Visual Studio kód.](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>Helyi futtatás, tesztelés és hibakeresés

A logikai alkalmazás teszteléséhez kövesse az alábbi lépéseket a hibakeresési munkamenetek elkezdéséhez, és keresse meg a Kérés eseményindító által létrehozott végpont URL-címét. Erre az URL-címre szüksége lesz, hogy később kérést küldenének erre a végpontra.

1. Az állapot nélküli munkafolyamatok hibakeresése érdekében engedélyezheti a munkafolyamat [futtatáselőzményét.](#enable-run-history-stateless)

1. A Visual Studio tevékenységsávján nyissa meg a Futtatás **menüt,** és válassza a **Hibakeresés** elkezdése (F5) lehetőséget.

   Megnyílik a **Terminál** ablak, hogy áttekintheti a hibakeresési munkamenetet.

   > [!NOTE]
   > Ha a "Hiba létezik a **preLaunchTask 'generateDebugSymbols'** futtatása után létezik" hibaüzenet jelenik meg, tekintse meg a hibaelhárítási szakaszt: A hibakeresési munkamenet nem indul [el.](#debugging-fails-to-start)

1. Most keresse meg a végpont visszahívási URL-címét a Kérés eseményindítóban.

   1. Nyissa meg újra az Explorer panelt a projekt megtekintéséhez.

   1. Aworkflow.js **helyi** menüjében válassza az Áttekintés **lehetőséget.**

      ![Képernyőkép az Explorer panelről és a fájlban workflow.jsablakról, az "Áttekintés" lehetőség kijelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Keresse meg **a Visszahívási URL-cím** értékét, amely a request eseményindítóhoz tartozó példa URL-címéhez hasonlóan néz ki:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Képernyőkép a munkafolyamat áttekintő oldalával és a visszahívási URL-címről](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. A visszahívás URL-címének a logikai alkalmazás munkafolyamatának aktiválásával való teszteléséhez nyissa meg a [Postmant](https://www.postman.com/downloads/) vagy az Ön által választott eszközt a kérések létrehozásához és elküldéhez.

   Ez a példa a Postman használatával folytatódik. További információ: [Postman – Első lépések.](https://learning.postman.com/docs/getting-started/introduction/)

   1. A Postman eszköztárán válassza az Új **lehetőséget.**

      ![A Postman képernyőképe a kijelölt Új gombbal](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Az Új **létrehozása panel** Építőelemek alatt **válassza** a Kérelem **lehetőséget.**

   1. A **Save Request (Kérés mentése)** ablakban, a **Request name (Kérelem neve)** alatt adjon nevet a kérésnek, például: `Test workflow trigger` .

   1. A **Select a collection or folder to save to**(Gyűjtemény vagy mappa kiválasztása a alkalmazásba) alatt válassza a Create Collection **(Gyűjtemény létrehozása) lehetőséget.**

   1. A **Minden gyűjtemény alatt** adja meg a kérések rendszerezéséhez létrehozni kívánt gyűjtemény nevét, nyomja le az Enter billentyűt, és válassza a Mentés <a gyűjtemény ***nevének kiválasztásához.* >** Ez a példa `Logic Apps requests` a gyűjtemény nevét használja.

      Megnyílik a Postman kéréspanelje, hogy elküldhető kérés a Kérés eseményindító visszahívási URL-címére.

      ![Képernyőkép a Postmanről a megnyitott kérelempanelen](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Térjen vissza Visual Studio Code-hoz. a munkafolyamat áttekintési oldalán másolja ki a **Visszahívási URL-cím** tulajdonság értékét.

   1. Térjen vissza a Postmanbe. A kérelempanelen kattintson a metódusok listájára, amely jelenleg **a GET** alapértelmezett kérési metódusként jelenik meg, illessze be a címmezőbe korábban kimásott visszahívási URL-címet, majd válassza a **Küldés lehetőséget.**

      ![Képernyőkép a Postman és a visszahívási URL-cím címmezőről, a Küldés gomb kijelölve](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      A példa logikaialkalmazás-munkafolyamat az alábbi példához hasonló e-mailt küld:

      ![Képernyőkép az Outlook e-mail-címről a példában leírtak szerint](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. A Visual Studio Code-ban térjen vissza a munkafolyamat áttekintő oldalára.

   Ha létrehozott egy állapot-igénylést, az elküldött kérés után az áttekintő oldal megjeleníti a munkafolyamat futtatásának állapotát és előzményeit.

   > [!TIP]
   > Ha a futtatás állapota nem jelenik meg, próbálja meg frissíteni az áttekintő oldalt a Frissítés **lehetőség kiválasztásával.** Nem történik futtatás olyan eseményindítókban, amelyek kihagyása a nem megfelelő feltételek miatt vagy adatkeresés miatt nem történik meg.

   ![Képernyőkép a munkafolyamat áttekintési oldaláról a futtatás állapotával és előzményeivel](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | Futtatás állapota | Description |
   |------------|-------------|
   | **Megszakítva** | A futtatás leállt vagy külső problémák, például rendszerkimaradás vagy elfogyott Azure-előfizetés miatt nem fejezték be. |
   | **Törölt** | A futtatás aktiválódott és elindult, de megszakítási kérelmet kapott. |
   | **Sikertelen** | A futtatás legalább egy művelete sikertelen volt. A munkafolyamatban nem voltak beállítva további műveletek a hiba kezelésére. |
   | **Futó** | A futtatás aktiválva lett, és folyamatban van, de ez az állapot a [](logic-apps-limits-and-config.md) műveletkorlátok vagy az aktuális díjszabási csomag miatt lekorlátozott futtatáskor is [megjelenhet.](https://azure.microsoft.com/pricing/details/logic-apps/) <p><p>**Tipp:** Ha beállította a [diagnosztikai naplózást,](monitor-logic-apps-log-analytics.md)az esetlegesen bekövetkező beállításokkal kapcsolatos információkat kaphat. |
   | **Sikeres** | A futtatás sikeres volt. Ha bármilyen művelet meghiúsult, a munkafolyamat egy későbbi művelete kezelte ezt a hibát. |
   | **Időkorrekta** | A futtatás túllépte az időkorlátot, mert az aktuális időtartam túllépte a futtatás időtartamának korlátját, amelyet a Futtatás előzményeinek megőrzése napokban beállítás [  vezérel.](logic-apps-limits-and-config.md#run-duration-retention-limits) A futtatás időtartamát a rendszer a futtatás kezdési és futási időtartamának a kezdési időpontra vonatkozó korlátja alapján számítja ki. <p><p>**Megjegyzés:** Ha a futtatás időtartama is meghaladja az aktuális futtatáselőzmény-megőrzési korlátot *,* amelyet a Futtatás előzményeinek megőrzése napokban beállítás is [  vezérel,](logic-apps-limits-and-config.md#run-duration-retention-limits)a futtatás törlődik a futtatás előzményeiből egy napi tisztítási feladat által. A futtatás időkorlátja vagy befejezése esetén a megőrzési idő mindig a futtatás kezdési ideje és az aktuális megőrzési korlát *alapján* lesz kiszámítva. Így ha csökkenti egy utas futtatás időtartamának korlátját, a futtatás időkorlátja túllépi az időkorlátot. A futtatás azonban vagy marad, vagy törlődik a futtatás előzményeiből az alapján, hogy a futtatás időtartama túllépte-e a megőrzési korlátot. |
   | **Várakozó** | A futtatás nem indult el vagy fel van függesztve, például egy még futó korábbi munkafolyamat-példány miatt. |
   |||

1. Egy adott futtatás egyes lépései állapotának, valamint a lépés bemenetének és kimenetének áttekintéséhez válassza a futtatáshoz szükséges három pont **(...**) gombot, majd a Futtatás megjelenítése **lehetőséget.**

   ![Képernyőkép a munkafolyamat futtatáselőzmény-sorról, a három pont gombbal és a kijelölt "Futtatás megjelenítése" gombbal](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code megnyitja a figyelési nézetet, és megjeleníti a futtatás egyes lépésének állapotát.

   ![Képernyőkép a munkafolyamat egyes lépésiről és állapotáról](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > Ha egy futtatás sikertelen, és a figyelési nézetben egy lépés a hibát jeleníti meg, a probléma egy hosszabb eseményindító vagy műveletnév miatt léphet fel, amely miatt a mögöttes Uniform Resource Identifier (URI) túllépi az alapértelmezett `400 Bad Request` karakterkorlátot. További információ: ["400 Hibás kérés".](#400-bad-request)

   A munkafolyamat egyes lépésének lehetséges állapotai a következőek:

   | Művelet állapota | Ikon | Description |
   |---------------|------|-------------|
   | **Megszakítva** | ![A megszakított művelet állapotának ikonja][aborted-icon] | A művelet leállt vagy nem fejeztük be külső problémák, például rendszerkimaradás vagy az Azure-előfizetés hiánya miatt. |
   | **Törölt** | ![A "Megszakítva" művelet állapotának ikonja][cancelled-icon] | A művelet futott, de kérést kapott a megszakításra. |
   | **Sikertelen** | ![Sikertelen műveletállapot ikonja][failed-icon] | A művelet sikertelen volt. |
   | **Futó** | ![A "Futó" művelet állapotának ikonja][running-icon] | A művelet jelenleg fut. |
   | **Kihagyva** | ![A "Kihagyva" művelet állapotának ikonja][skipped-icon] | A műveletet a rendszer kihagyta, mert a közvetlenül megelőző művelet sikertelen volt. A művelet olyan feltétellel rendelkezik, amely megköveteli, hogy az előző művelet sikeresen befejeződik `runAfter` az aktuális művelet futtatása előtt. |
   | **Sikeres** | ![A "Sikeres" művelet állapotának ikonja][succeeded-icon] | A művelet sikeres volt. |
   | **Sikeres újraküldetve** | ![A "Sikeres újraküldve" művelet állapotának ikonja][succeeded-with-retries-icon] | A művelet sikeres volt, de csak egy vagy több újraesetet után. Az újrapróbálkozási előzmények megtekintéséhez a futtatási előzmények részletei nézetben válassza ki ezt a műveletet, hogy megtekint tudja a bemeneteket és kimeneteket. |
   | **Időkorrekta** | ![Az "Időkoronált" művelet állapotának ikonja][timed-out-icon] | A művelet a művelet beállításai által megadott időtúllépési korlát miatt leállt. |
   | **Várakozó** | ![A "Várakozó" művelet állapotának ikonja][waiting-icon] | A hívótól bejövő kérésre várakozó webhook-műveletre vonatkozik. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. Az egyes lépés bemenetek és kimenetek áttekintéséhez válassza ki a vizsgálni kívánt lépést.

   ![Képernyőkép a munkafolyamat egyes lépései állapotáról, valamint a bővített "E-mail küldése" művelet bemeneteiről és kimeneteiről](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Az erre a lépésre vonatkozó nyers bemenetek és kimenetek további áttekintéséhez válassza a **Nyers** bemenetek megjelenítése vagy a Nyers kimenetek **megjelenítése lehetőséget.**

1. A hibakeresési munkamenet leállításhoz válassza a Futtatás menü **Hibakeresés** befejezése (Shift + F5) parancsát. 

<a name="return-response"></a>

## <a name="return-a-response"></a>Válasz visszaadva

Ha választ ad vissza a logikai alkalmazásnak kérelmet indító hívónak, használhatja a beépített [Response](../connectors/connectors-native-reqres.md) műveletet a Kérés eseményindítóval elinduló munkafolyamathoz.

1. A munkafolyamat-tervező e-mail küldése **művelete** alatt válassza az Új **lépés lehetőséget.**

   A **tervezőben** megjelenik a Művelet kiválasztása kérdés, majd megnyitható **a** Művelet hozzáadása panel, hogy kiválasztja a következő műveletet.

1. Győződjön meg **arról, hogy a** Művelet hozzáadása panel **Művelet** kiválasztása keresőmezője alatt a Beépített lehetőség van **kiválasztva.** A keresőmezőbe írja be a következőt: `response` , majd válassza ki a **Response** műveletet.

   ![Képernyőkép a munkafolyamat-tervezőről a kiválasztott Válasz művelet használatával.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Amikor **megjelenik** a Válasz művelet a tervezőben, a művelet részletek panelje automatikusan megnyílik.

   ![Képernyőkép a munkafolyamat-tervezőről, amely megnyitja a "Válasz" művelet részletező paneljét, és a "Törzs" tulajdonság az "E-mail küldése" művelet "Törzs" tulajdonságára van beállítva.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. A **Paraméterek lapon** adja meg a meghívni kívánt függvényhez szükséges adatokat.

   Ez a példa **az** E-mail küldése művelet kimenetének Törzs **tulajdonságértékét adja** vissza.

   1. Kattintson a **Törzs tulajdonságmezőbe,** hogy megjelenjen a dinamikus tartalmak listája, és megjelenjenek az előző eseményindító és a munkafolyamatban lévő műveletek elérhető kimeneti értékei.

      ![Képernyőkép a "Válasz" művelet részletező panelről, a "Törzs" tulajdonságon belüli egérmutatóval, hogy megjelenjen a dinamikus tartalomlista.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. A dinamikus tartalmak listájában, az **E-mail küldése alatt** válassza a **Törzs lehetőséget.**

      ![Képernyőkép a megnyitott dinamikus tartalmak listájáról. A listában az "E-mail küldése" fejléc alatt a "Törzs" kimeneti érték van kiválasztva.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Ha végzett, a Válasz művelet **Törzs** tulajdonsága az  E-mail küldése művelet Törzs kimeneti **értékére** van beállítva.

      ![Képernyőkép a munkafolyamat egyes lépései, valamint a kibontott "Válasz" művelet bemenetei és kimenetei állapotáról.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. A tervezőben válassza a Mentés **lehetőséget.**

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>A logikai alkalmazás újratesztel

Miután frissítette a logikai alkalmazást, futtathat egy másik tesztet úgy, hogy újrafuttatja a hibakeresőt a Visual Studio-ban, és egy másik kérést küld a frissített logikai alkalmazás aktiválásához, hasonlóan a Helyi futtatás, tesztelés és hibakeresés [lépéseihez.](#run-test-debug-locally)

1. A Visual Studio tevékenységsávján nyissa meg a Futtatás **menüt,** és válassza a **Hibakeresés** elkezdése (F5) lehetőséget.

1. A Postmanben vagy a kérések létrehozására és küldésére használható eszközben küldjön egy másik kérést a munkafolyamat aktiválásához.

1. Ha létrehozott egy állapot- szerinti munkafolyamatot, a munkafolyamat áttekintő oldalán ellenőrizze a legutóbbi futtatás állapotát. A futtatás egyes lépései állapotának, bemenetének és kimenetének megtekintéséhez válassza a futtatáshoz szükséges három pont (**...**) gombot, majd a **Futtatás megjelenítése lehetőséget.**

   Itt például a következő lépésenként adhatja meg egy futtatás állapotát, miután a minta-munkafolyamat frissítve lett a Válasz műveletre.

   ![Képernyőkép a frissített munkafolyamat egyes lépései állapotáról, valamint a kibontott "Válasz" művelet bemeneteiről és kimeneteiről.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. A hibakeresési munkamenet leállításhoz válassza a Futtatás menü **Hibakeresés** befejezése (Shift + F5) parancsát. 

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>Tartománynevek megkerese a tűzfal-hozzáféréshez

Mielőtt üzembe helyez és futtat egy logikaialkalmazás-munkafolyamatot a Azure Portal-ban, ha a környezet szigorú hálózati követelményekkel vagy a forgalmat korlátozó tűzfallal rendelkezik, engedélyeket kell beállítania a munkafolyamatban meglévő eseményindító- vagy műveletkapcsolatokhoz.

A kapcsolatok teljes tartománynevének (FQDN) megkeresése érdekében kövesse az alábbi lépéseket:

1. A logikaialkalmazás-projektben nyissa meg a **connections.jsfájlban,** amely azután jön létre, hogy hozzáadta az első kapcsolatalapú eseményindítót vagy műveletet a munkafolyamathoz, és megkeresi az `managedApiConnections` objektumot.

1. Minden létrehozott kapcsolathoz keresse meg, másolja és mentse a tulajdonság értékét egy biztonságos helyre, hogy be tudja állítani a tűzfalat ezzel az `connectionRuntimeUrl` információval.

   Ez a **connections.jsfájlban** található példa két kapcsolatot tartalmaz, egy AS2-kapcsolatot és egy Office 365-kapcsolatot az alábbi `connectionRuntimeUrl` értékekkel:

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

A Visual Studio Code-ból közvetlenül közzéteheti a projektet az Azure-ban, amely az új **Logic App (előzetes verzió)** erőforrástípussal üzembe helyezheti a logikai alkalmazást. A Azure Functions függvényalkalmazás-erőforrásához hasonlóan ehhez az új erőforrástípushoz is [](../app-service/overview-hosting-plans.md)ki kell választania egy szolgáltatási tervet és tarifacsomagot, amelyet az üzembe helyezés során állíthat be. Az üzemeltetési csomagokkal és díjszabásokkal kapcsolatos további információkért tekintse át az alábbi témaköröket:

* [Horizontális felskálás a Azure App Service](../app-service/manage-scale-up.md)
* [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)

A logikai alkalmazást közzéteheti új erőforrásként, amely automatikusan létrehozza az összes szükséges erőforrást, például egy [Azure Storage-fiókot, a függvényalkalmazás követelményeihez hasonlóan.](../azure-functions/storage-considerations.md) Vagy közzéteheti a logikai alkalmazást egy korábban üzembe helyezett **logic app (előzetes verzió)** erőforrásban, amely felülírja ezt a logikai alkalmazást.

### <a name="publish-to-a-new-logic-app-preview-resource"></a>Közzététel új Logic App-erőforrásban (előzetes verzió)

1. A Visual Studio-tevékenységsávon válassza az Azure ikont.

1. Az **Azure: Logic Apps (előzetes verzió)** panel eszköztárán válassza az Üzembe **helyezés logikai alkalmazásban lehetőséget.**

   ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről és a panel eszköztárával, az "Üzembe helyezés a logikai alkalmazásban" beállítás kijelölve.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Ha a rendszer kéri, válassza ki a logikai alkalmazás üzembe helyezéséhez használni kívánt Azure-előfizetést.

1. A Code Visual Studio listában válasszon az alábbi lehetőségek közül:

   * **Új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban** (gyors)
   * **Új logikai alkalmazás létrehozása (előzetes verzió) az Azure Advanced szolgáltatásban**
   * Korábban üzembe helyezett **logic app (előzetes verzió)** erőforrás, ha van ilyen

   Ez a példa az Új logikai alkalmazás **létrehozása (előzetes verzió) az Azure Advanced kiadásban folytatja.**

   ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről, az "Új logikai alkalmazás létrehozása (előzetes verzió) az Azure-ban" listában.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Az új **Logic App-erőforrás (előzetes verzió) létrehozásához** kövesse az alábbi lépéseket:

   1. Adjon globálisan egyedi nevet az új logikai alkalmazásnak, amely a Logikai alkalmazás **(előzetes verzió)** erőforráshoz használni fog. Ez a példa a következőt használja: `Fabrikam-Workflows-App`.

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről, valamint egy kérésről, hogy adja meg az új logikai alkalmazás létrehozható nevét.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Válasszon ki [egy üzemeltetési tervet](../app-service/overview-hosting-plans.md) az új logikai alkalmazáshoz, amely lehet App Service csomag [ **(dedikált)**](../azure-functions/dedicated-plan.md) vagy a [**Prémium.**](../azure-functions/functions-premium-plan.md)

      > [!IMPORTANT]
      > A használatban álló csomagok nem támogatottak és nem érhetők el ehhez az erőforrástípushoz. A kiválasztott csomag befolyásolja a később elérhető képességeket és tarifacsomagokat. További információért tekintse át az alábbi témaköröket: 
      >
      > * [Az Azure Functions méretezése és üzemeltetése](../azure-functions/functions-scale.md)
      > * [App Service díjszabás részletei](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > A prémium szintű csomag például hozzáférést biztosít a hálózati képességekhez, például a csatlakozáshoz és az Azure-beli virtuális hálózatokkal való privát integrációhoz, hasonlóan ahhoz, Azure Functions a logikai alkalmazások létrehozásakor és üzembe helyezésekor. 
      > További információért tekintse át az alábbi témaköröket:
      > 
      > * [Az Azure Functions hálózatkezelési lehetőségei](../azure-functions/functions-networking-options.md)
      > * [Azure Logic Apps Running Anywhere – Hálózati lehetőségek a Azure Logic Apps előzetes verzióval](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      Ez a példa a **App Service tervet használja.**

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről, valamint a "App Service csomag" vagy a "Prémium" kiválasztására vonatkozó kérésről.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Hozzon létre egy App Service vagy válasszon ki egy meglévőt. Ebben a példában a **Create new App Service Plan (Új App Service létrehozása) lehetőséget választjuk.**

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről, valamint az "Új App Service-csomag létrehozása" üzenettel, vagy egy meglévő App Service kiválasztásával.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Adja meg a App Service nevét, majd válasszon [](../app-service/overview-hosting-plans.md) tarifacsomagot a csomaghoz. Ebben a példában az **F1 ingyenes csomag van kiválasztva.**

      ![Az "Azure: Logic Apps (előzetes verzió)" panelt és a tarifacsomag kiválasztására vonatkozó kérést megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Az optimális teljesítmény érdekében keresse meg és válassza ki ugyanazt az erőforráscsoportot, mint a projekt az üzembe helyezéshez.

      > [!NOTE]
      > Bár létrehozhat vagy használhat egy másik erőforráscsoportot, ez hatással lehet a teljesítményre. Ha egy másik erőforráscsoportot hoz létre vagy választ, de a megerősítési kérés megjelenése után megszakítja a műveletet, az üzemelő példány is megszakad.

   1. Állapot- szerinti munkafolyamatok számára válassza az **Új tárfiók létrehozása vagy** egy meglévő tárfiók lehetőséget.

      ![Képernyőkép az "Azure: Logic Apps (előzetes verzió)" panelről, valamint a tárfiók létrehozására vagy kiválasztására vonatkozó kérésről.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Ha a logikai alkalmazás létrehozási és üzembe helyezési beállításai támogatják a [Application Insights,](../azure-monitor/app/app-insights-overview.md)engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt a logikai alkalmazás kódból vagy az üzembe helyezést követően Visual Studio meg. Szüksége lesz egy Application Insights-példányra, de ezt az [](../azure-monitor/app/create-workspace-resource.md)erőforrást előre is létrehozhatja a logikai alkalmazás üzembe helyezésekor vagy az üzembe helyezés után.

      A naplózás és a nyomkövetés engedélyezéséhez kövesse az alábbi lépéseket:

      1. Válasszon ki egy meglévő Application Insights erőforrást, vagy **hozzon létre Application Insights erőforrást.**

      1. A [Azure Portal](https://portal.azure.com)az erőforráshoz Application Insights.

      1. Az erőforrás menüben válassza az Áttekintés **lehetőséget.** Keresse meg és másolja ki a **eszközkulcs** értékét.

      1. A Visual Studio Code-ban, a projekt gyökérmappában nyissa meg alocal.settings.js **fájlban.**

      1. A objektumban adja hozzá a tulajdonságot, és állítsa az értékét a `Values` `APPINSIGHTS_INSTRUMENTATIONKEY` eszközkulcsra, például:

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > Ellenőrizheti, hogy az eseményindítók és a műveletnevek helyesen jelennek-e meg a Application Insights példányban.
         >
         > 1. A Azure Portal a saját erőforrás Application Insights meg.
         >
         > 2. Az erőforrásmenü Vizsgálat menüjében **válassza** az **Alkalmazástérkép lehetőséget.**
         >
         > 3. Tekintse át a térképen megjelenő műveletneveket.
         >
         > A beépített triggerek egyes bejövő kérései ismétlődésekként megjelenhetnek az alkalmazástérképen. 
         > A formátum használata helyett ezek az ismétlődések a munkafolyamat nevét használják a művelet neveként, és a Azure Functions `WorkflowName.ActionName` származnak.

      1. A következő lépésként beállíthatja a logikai alkalmazás által gyűjtött és a saját Application Insights küldött nyomkövetési adatok súlyossági szintjét.

         Minden munkafolyamattal kapcsolatos esemény bekövetkeztekor, például egy munkafolyamat aktivált vagy egy művelet futtatásakor a futásidő különböző nyomkövetéseket bocsát ki. Ezek a nyomkövetések lefedik a munkafolyamat élettartamát, és többek között a következő eseménytípusokat tartalmazzák:

         * Szolgáltatási tevékenység, például indítás, leállítás és hibák.
         * Feladatok és diszpécsertevékenység.
         * Munkafolyamat-tevékenység, például eseményindító, művelet és futtatás.
         * Tárterület-kérési tevékenység, például sikeres vagy sikertelen.
         * HTTP-kérési tevékenység, például bejövő, kimenő, sikeres és sikertelen.
         * Minden fejlesztési nyomkövetés, például hibakeresési üzenetek.

         Minden eseménytípus súlyossági szinthez van rendelve. A szint például a legrészletesebb üzeneteket rögzíti, míg a szint a munkafolyamat általános tevékenységét rögzíti, például azt, hogy mikor indul el és áll le a logikai alkalmazás, a munkafolyamat, az eseményindító és a `Trace` `Information` műveletek. Ez a táblázat a súlyossági szinteket és azok nyomkövetési típusait ismerteti:

         | Súlyossági szint | Nyomkövetési típus |
         |----------------|------------|
         | Kritikus | Naplók, amelyek a logikai alkalmazás nem állítható vissza meghibásodását írják le. |
         | Hibakeresés | A fejlesztés során vizsgálathoz használható naplók, például bejövő és kimenő HTTP-hívások. |
         | Hiba | Naplók, amelyek a munkafolyamat végrehajtásának sikertelenségére utalnak, de a logikai alkalmazás általános meghibásodására nem. |
         | Tájékoztatás | Naplók, amelyek a logikai alkalmazásban vagy munkafolyamatban követik nyomon az általános tevékenységeket, például: <p><p>– Eseményindító, művelet vagy futtatás kezdete és vége. <br>– A logikai alkalmazás elindul vagy befejeződik. |
         | Nyomkövetés | Naplók, amelyek a legrészletesebb üzeneteket tartalmazzák, például a tárolási kérelmeket vagy a diszpécsertevékenységet, valamint a munkafolyamat-végrehajtási tevékenységgel kapcsolatos összes üzenetet. |
         | Figyelmeztetés | Naplók, amelyek a logikai alkalmazás rendellenes állapotát emelik ki, de nem akadályozzák a futását. |
         |||

         A súlyossági szint beállításához a projekt gyökérszinten nyissa meg ahost.js **fájlban,** és keresse meg az `logging` objektumot. Ez az objektum a logikai alkalmazás összes munkafolyamatának naplószűrését szabályozza, és a ASP.NET alapvető elrendezését követi a [naplótípus-szűréshez.](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         Ha az objektum nem tartalmaz a tulajdonságot tartalmazó objektumot, adja `logging` hozzá ezeket az `logLevel` `Host.Triggers.Workflow` elemeket. Állítsa a tulajdonságot a kívánt nyomkövetési típus súlyossági szintjére, például:

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   Ha végzett az üzembe helyezési lépésekkel, a Visual Studio Code elkezdi létrehozni és üzembe helyezni a logikai alkalmazás közzétételéhez szükséges erőforrásokat.

1. Az üzembe helyezési folyamat áttekintéséhez és figyeléhez válassza a Nézet **menü** Kimenet **parancsát.** A Kimenet ablak eszköztárának listájában válassza **a** Azure Logic Apps.

   ![Képernyőkép a Kimenet ablakról, amely az eszköztár Azure Logic Apps az "alkalmazás" listában, valamint az üzembe helyezési folyamattal és állapotokkal.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Amikor Visual Studio Code befejezi a logikai alkalmazás üzembe helyezését az Azure-ban, a következő üzenet jelenik meg:

   ![Képernyőkép az Azure-ban való üzembe helyezés sikeres befejezéséről.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gratulálunk, a logikai alkalmazás mostantól elérhető az Azure-ban, és alapértelmezés szerint engedélyezve van.

Ezután megtanulhatja, hogyan hajthatja végre ezeket a feladatokat:

* [Adjon hozzá egy üres munkafolyamatot a projekthez.](#add-workflow-existing-project)

* [Az üzembe helyezett logikai alkalmazásokat a Visual Studio Code-ban](#manage-deployed-apps-vs-code) vagy a [Azure Portal.](#manage-deployed-apps-portal)

* [Futtatás előzményeinek engedélyezése állapot nélküli munkafolyamatokban.](#enable-run-history-stateless)

* [Engedélyezze a figyelési nézetet a Azure Portal telepített logikai alkalmazáshoz.](#enable-monitoring)

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>Üres munkafolyamat hozzáadása a projekthez

A logikaialkalmazás-projektben több munkafolyamat is lehet. Ha üres munkafolyamatot szeretne hozzáadni a projekthez, kövesse az alábbi lépéseket:

1. A kód Visual Studio sávon válassza az Azure ikont.

1. Az Azure panelen az **Azure: Logic Apps (előzetes verzió)** elem mellett válassza a **Munkafolyamat** létrehozása lehetőséget (a Azure Logic Apps).

1. Válassza ki a hozzáadni kívánt munkafolyamat-típust: **Állapot-** vagy **Állapot nélküli**

1. Adjon nevet a munkafolyamatnak.

Ha végzett, megjelenik egy új munkafolyamat-mappa **a** projektben, valamint egyworkflow.jsa munkafolyamat-definíció fájljában.

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>Üzembe helyezett logikai alkalmazások kezelése a Visual Studio Code-ban

Az Visual Studio Code-ban megtekintheti az Összes üzembe helyezett logikai alkalmazást az Azure-előfizetésében, függetlenül attól, hogy azok az eredeti **Logic Apps** vagy a Logikai alkalmazás **(előzetes verzió)** erőforrástípust jelentik, és kiválaszthatja azokat a feladatokat, amelyek segítségével kezelheti ezeket a logikai alkalmazásokat. Mindkét erőforrástípus eléréséhez azonban az Azure Logic Apps  és Azure Logic Apps **(előzetes verzió)** bővítményekre is szüksége lesz a Visual Studio Code-hoz.

1. A bal oldali eszköztáron válassza az Azure ikont. Az **Azure: Logic Apps (előzetes verzió)** panelen bontsa ki az előfizetést, amely megjeleníti az előfizetéshez üzembe helyezett logikai alkalmazásokat.

1. Nyissa meg a kezelni kívánt logikai alkalmazást. A logikai alkalmazás helyi menüjében válassza ki a végrehajtani kívánt feladatot.

   Kiválaszthat például olyan feladatokat, mint az üzembe helyezett logikai alkalmazás leállítása, indítása, újraindítása vagy törlése.

   ![Képernyőkép a Visual Studio Code-ot a megnyitott "Azure Logic Apps (előzetes verzió)" bővítménypanelről és az üzembe helyezett munkafolyamatról.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. A logikai alkalmazás összes munkafolyamatának megtekintéséhez bontsa ki a logikai alkalmazást, majd a **Munkafolyamatok csomópontot.**

1. Egy adott munkafolyamat megtekintéséhez nyissa meg a munkafolyamat helyi menüjét, és válassza a Megnyitás tervezőben lehetőséget, amely csak olvasható módban nyitja meg a munkafolyamatot.

   A munkafolyamat szerkesztéséhez a következő lehetőségek állnak rendelkezésre:

   * A Visual Studio Code-ban nyissa meg a projekt **workflow.jsa** munkafolyamat-tervezőben található fájlban, szerkessze a módosításokat, majd üzembe tegye a logikai alkalmazást az Azure-ban.

   * A Azure Portal keresse meg és [nyissa meg a logikai alkalmazást.](#manage-deployed-apps-portal) Keresse meg, szerkessze és mentse a munkafolyamatot.

1. Az üzembe helyezett logikai alkalmazás megnyitásához a Azure Portal nyissa meg a logikai alkalmazás helyi menüjét, és válassza a Megnyitás a **portálon lehetőséget.**

   A Azure Portal megnyílik a böngészőben, automatikusan bejelentkezik a portálra, ha bejelentkezett a Visual Studio Code-ban, és megjeleníti a logikai alkalmazást.

   ![A logikai alkalmazás Azure Portal oldalának képernyőképe az Visual Studio Code-ban.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Külön is bejelentkezhet a Azure Portal, a portál keresőmezőjével megkeresheti a logikai alkalmazást, majd kiválaszthatja a logikai alkalmazást az eredmények listájából.

   ![Képernyőkép a Azure Portal és a keresési sávról a kiválasztott telepített logikai alkalmazás keresési eredményeivel.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>Üzembe helyezett logikai alkalmazások kezelése a portálon

A Azure Portal megtekintheti az Összes üzembe helyezett logikai alkalmazást, amely az Azure-előfizetésében van, függetlenül attól, hogy **azok** az eredeti Logic Apps-erőforrástípus vagy a logikai alkalmazás **(előzetes verzió)** erőforrástípusa. Jelenleg minden erőforrástípus külön kategóriákba van rendezve és kezelhető az Azure-ban. Logikai alkalmazás (előzetes **verzió)** típusú logikai alkalmazások kereséséhez kövesse az alábbi lépéseket:

1. A Azure Portal keresőmezőbe írja be a `logic app preview` következőt: . Amikor megjelenik az eredmények listája, a **Szolgáltatások alatt válassza** a Logikai alkalmazás **(előzetes verzió) lehetőséget.**

   ![Képernyőkép a Azure Portal keresőmezőről a "logikai alkalmazás előnézete" keresési szöveggel.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. A Logikai **alkalmazás (előzetes verzió)** panelen keresse meg és válassza ki a Visual Studio logikai alkalmazást.

   ![Az Azure-ban Azure Portal logikai alkalmazás (előzetes verzió) erőforrásait bemutató képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   A Azure Portal megnyitja a kiválasztott logikai alkalmazás egyéni erőforráslapját.

   ![Képernyőkép a logikai alkalmazás munkafolyamatának erőforrásoldalról a Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. A logikai alkalmazásban a munkafolyamatok megtekintéséhez válassza a logikai alkalmazás menüjének **Munkafolyamatok elemét.**

   A **Munkafolyamatok panel** az aktuális logikai alkalmazás összes munkafolyamatát megjeleníti. Ez a példa a Code-ban létrehozott Visual Studio be.

   ![Képernyőkép egy "Logikai alkalmazás (előzetes verzió)" erőforrásoldalról, megnyitott "Munkafolyamatok" panelel és az üzembe helyezett munkafolyamattal](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Egy munkafolyamat megtekintéséhez a **Munkafolyamatok panelen** válassza ki azt a munkafolyamatot.

   Megnyílik a munkafolyamat panelje, és további információkat és feladatokat tartalmaz, amelyek a munkafolyamaton hajthat végre.

   Ha például a munkafolyamat lépéseit meg kell tekintenie, válassza a **Tervező lehetőséget.**

   ![Képernyőkép a kiválasztott munkafolyamat "Áttekintés" panelről, a munkafolyamat menüjében pedig a kijelölt "Tervező" parancs látható.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Megnyílik a munkafolyamat-tervező, és megjeleníti a Kód Visual Studio munkafolyamatot. Most már módosíthatja ezt a munkafolyamatot a Azure Portal.

   ![A munkafolyamat-tervezőt és a Code-ból üzembe helyezett munkafolyamatot Visual Studio képernyőkép.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>Másik munkafolyamat hozzáadása a portálon

A Azure Portal üres munkafolyamatokat adhat hozzá az Visual Studio Code-ból üzembe helyezett Logic **App- (előzetes verziójú)** erőforrásokhoz, és ezeket a munkafolyamatokat a Azure Portal.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az üzembe helyezett **Logic App (előzetes verzió) erőforrást.**

1. A logikai alkalmazás menüjében válassza a **Munkafolyamatok lehetőséget.** A **Munkafolyamatok panelen** válassza a Hozzáadás **lehetőséget.**

   ![Képernyőkép a kiválasztott logikai alkalmazás "Munkafolyamatok" panelről és eszköztárról a kijelölt "Hozzáadás" paranccsal.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Az Új **munkafolyamat panelen** adja meg a munkafolyamat nevét. Válassza az **Állapot vagy** az Állapot **nélküli létrehozás** **>** **lehetőséget.**

   Miután az Azure üzembe helyezett egy új  munkafolyamatot, amely megjelenik a Munkafolyamatok panelen, válassza ki ezt a munkafolyamatot, hogy más feladatokat kezelhet és hajthat végre, például megnyithatja a tervezőt vagy a kódnézetet.

   ![Képernyőkép a kiválasztott munkafolyamatról felügyeleti és felülvizsgálati beállításokkal.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Egy új munkafolyamat tervezője például üres vásznat mutat. Most már felépítheti ezt a munkafolyamatot a Azure Portal.

   ![Képernyőkép a munkafolyamat-tervezőről és egy üres munkafolyamatról.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Futtatás előzményeinek engedélyezése állapot nélküli munkafolyamatokhoz

Az állapot nélküli munkafolyamatok hibakeresése érdekében engedélyezheti a munkafolyamat futtatáselőzményét, majd letilthatja a futtatás előzményeit, amikor végzett. Kövesse ezeket a lépéseket a Visual Studio Code-hoz, vagy ha a Azure Portal-ban dolgozik, tekintse meg az állapot- szerinti és állapot nélküli munkafolyamatok a [Azure Portal.](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)

1. A Visual Studio Code projektben bontsa ki a **workflow-designtime** mappát, és nyissa meg **local.settings.jsfájlt.**

1. Adja hozzá `Workflows.{yourWorkflowName}.operationOptions` a tulajdonságot, és állítsa a értékét `WithStatelessRunHistory` értékre, például:

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS vagy Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Ha szeretné letiltani a futtatás előzményeit, ha végzett, állítsa a tulajdonságot értékre, vagy törölje a tulajdonságot `Workflows.{yourWorkflowName}.OperationOptions` `None` és annak értékét.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Monitorozási nézet engedélyezése a Azure Portal

Miután üzembe helyezett egy **Logic App- (előzetes verziójú)** erőforrást az Visual Studio Code-ból az Azure-ba, áttekintheti az  erőforrásban található munkafolyamatok elérhető futtatáselőzményét és részleteit az Azure Portal és a munkafolyamat Figyelése funkciójának használatával. Először azonban engedélyeznie kell a **Figyelési** nézet képességet a logikai alkalmazás erőforrásán.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az üzembe helyezett **Logic App (előzetes verzió) erőforrást.**

1. Az erőforrás menüjében az API alatt **válassza** a **CORS lehetőséget.**

1. A **CORS panelen** az **Engedélyezett eredetek** alatt adja hozzá a helyettesítő karaktert (*).

1. Ha végzett, a **CORS** eszköztáron válassza a Mentés **lehetőséget.**

   ![Képernyőkép az üzembe Azure Portal Logikai alkalmazás (előzetes verzió) erőforrással való telepítésről. Az erőforrásmenüben a "CORS" van kiválasztva, és az "Engedélyezett eredet" új bejegyzése a "*" helyettesítő karakterre van állítva.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>A központi telepítés Application Insights engedélyezése vagy megnyitása

A munkafolyamat végrehajtása során a logikai alkalmazás más eseményekkel együtt telemetriát bocsát ki. Ezzel a telemetriával jobban átlátást kaphat a munkafolyamat futásának, valamint a Logic Apps futásidejű különböző módokon való működését. A munkafolyamatot a Application Insights [használatával](../azure-monitor/app/app-insights-overview.md)figyelheti, amely közel valós idejű telemetriát (élő metrikákat) biztosít. Ezzel a képességgel könnyebben kivizsgálhatja a hibákat és a teljesítményproblémákat, ha ezeket az adatokat problémák diagnosztizálására, riasztások beállítására és diagramok felépítésére használja.

Ha a logikai alkalmazás létrehozási és üzembe helyezési beállításai támogatják a [Application Insights,](../azure-monitor/app/app-insights-overview.md)engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor is meg lehet tenni, ha a logikai alkalmazást az Visual Studio kódból vagy az üzembe helyezés után telepíti. Szüksége lesz egy Application Insights példányra, de ezt az [](../azure-monitor/app/create-workspace-resource.md)erőforrást előre is létrehozhatja a logikai alkalmazás üzembe helyezésekor vagy az üzembe helyezés után.

Ha engedélyezni Application Insights egy üzembe helyezett logikai alkalmazásban, vagy szeretné áttekintni a Application Insights adatokat, ha már engedélyezve van, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg az üzembe helyezett logikai alkalmazást.

1. A logikai alkalmazás menüjében a Beállítások **alatt válassza** a Application Insights **lehetőséget.**

1. Ha Application Insights nincs engedélyezve, a Application Insights **panelen** válassza **a Kapcsolat Application Insights.** A panel frissítése után alul válassza az Alkalmaz **lehetőséget.**

   Ha Application Insights engedélyezve van, a Application Insights **panelen** válassza az **Adatok megtekintése Application Insights lehetőséget.**

A Application Insights után áttekintheti a logikai alkalmazás különböző metrikákat. További információért tekintse át az alábbi témaköröket:

* [Azure Logic Apps Running Anywhere – Monitor Application Insights – 1. rész](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps Running Anywhere – Monitor Application Insights – 2. rész](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Üzembe helyezés a Dockerben

A logikai alkalmazást a .NET CLI használatával helyezheti üzembe üzemeltetési környezetként egy [Docker-tárolóban.](/dotnet/core/tools/) [](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) Ezekkel a parancsokkal felépítheti és közzéteheti a logikai alkalmazás projektjét. Ezután felépítheti és futtathatja a Docker-tárolót a logikai alkalmazás üzembe helyezésének célhelyeként.

Ha még nem ismeri a Docker-t, tekintse át az alábbi témaköröket:

* [Mi a Docker?](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [A tárolók és a Docker bemutatása](/dotnet/architecture/microservices/container-docker-introduction/)
* [A .NET és a Docker bemutatása](/dotnet/core/docker/introduction)
* [Docker-tárolók, rendszerképek és beállításregisztrálók](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [Oktatóanyag: A Docker első lépések (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>Követelmények

* A logikai alkalmazás által az üzembe helyezéshez használt Azure Storage-fiók

* Docker-fájl a Docker-tároló létrehozása során használt munkafolyamathoz

  Ez a Docker-mintafájl például üzembe helyez egy logikai alkalmazást, és megadja azt a kapcsolati sztringet, amely tartalmazza a logikai alkalmazásnak a virtuális gépre való közzétételéhez használt Azure Storage-fiók Azure Portal. A sztring keresésével kapcsolatban lásd: [Tárfiók kapcsolati sztringének lekérte.](#find-storage-account-connection-string) További információkért tekintse át a [Docker-fájlok írásának ajánlott eljárásait.](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
  
  > [!IMPORTANT]
  > Éles forgatókönyvek esetén ügyeljen arra, hogy az ilyen titkos adatokat és bizalmas adatokat védje és védje, például egy kulcstartót használva. A Docker-fájlokról a [BuildKittel](https://docs.docker.com/develop/develop-images/build_enhancements/) való rendszerképek összeállítása és a Bizalmas [adatok kezelése Docker-titkos kulcsok segítségével( ) ismertetőt tekintse át.](https://docs.docker.com/engine/swarm/secrets/)

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>Tárfiók kapcsolati sztringének lekérte

Mielőtt felépítheti és futtathatja a Docker-tároló rendszerképét, le kell szereznie a tárfiók hozzáférési kulcsát tartalmazó kapcsolati sztringet. Korábban már létrehozta ezt a tárfiókot macOS vagy Linux rendszeren a bővítmény használatára, vagy amikor üzembe helyezett egy logikai alkalmazást a Azure Portal.

A kapcsolati sztring megkeresi és kimásolódhatja az alábbi lépéseket:

1. A Azure Portal a tárfiók menüjének Beállítások **menüjében válassza** a **Hozzáférési kulcsok lehetőséget.** 

1. A Hozzáférési **kulcsok panelen** keresse meg és másolja ki a tárfiók kapcsolati sztringet, amely az alábbi példához hasonlóan néz ki:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Képernyőkép az Azure Portal tárfiók hozzáférési kulcsait és a kapcsolati sztringet átmásolva.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   További információ: [Manage storage account keys (Tárfiókkulcsok kezelése).](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)

1. Mentse a kapcsolati sztringet egy biztonságos helyre, hogy hozzáadja ezt a sztringet az üzembe helyezéshez használt Docker-fájlhoz. 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>Tárfiók főkulcsának megkerese

Ha a munkafolyamat kérelem-eseményindítót tartalmaz, le kell kérnie az eseményindító visszahívási [URL-címét](#get-callback-url-request-trigger) a Docker-tároló rendszerképének létrehozása és futtatása után. Ehhez a feladathoz meg kell adnia a központi telepítéshez használt tárfiók mesterkulcsának értékét is.

1. A mesterkulcs megkereséhez a projektben nyissa meg az **azure-webjobs-secrets/{üzemelő példány neve}/host.jsfájlt.**

1. Keresse meg `AzureWebJobsStorage` a tulajdonságot, és másolja ki a kulcs értékét ebből a szakaszból:

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. Mentse ezt a kulcsértéket egy biztonságos helyre, ahol később is használhatja.

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Docker-tároló rendszerképének összeállítása és futtatása

1. Készítse el a Docker-tároló rendszerképét a Docker-fájllal és az alábbi parancs futtatásával:

   `docker build --tag local/workflowcontainer .`

   További információ: [docker build](https://docs.docker.com/engine/reference/commandline/build/).

1. Futtassa helyileg a tárolót a következő paranccsal:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   További információ: [docker run](https://docs.docker.com/engine/reference/commandline/run/).

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Visszahívási URL-cím lekérése a Kérés eseményindítóhoz

A Kérés eseményindítót használó munkafolyamatok esetén kérje le az eseményindító visszahívási URL-címét a következő kérés elküldével:

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

Az érték a munkafolyamat JSON-definíciójában megjelenő `{trigger-name}` Kérés eseményindító neve. Az érték az Azure Storage-fiókban van definiálva, amely az `{master-key}` `AzureWebJobsStorage` **azure-webjobs-secrets/{deployment-name}/host.jstulajdonsághoz van beállítva.** További információ: [Find storage account master key (Tárfiók főkulcsának megkerese).](#find-storage-account-master-key)

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Elemek törlése a tervezőből

Ha törölni szeretné a munkafolyamat egy elemét a tervezőből, kövesse az alábbi lépéseket:

* Jelölje ki az elemet, nyissa meg az elem helyi menüjét (Shift+F10), majd válassza a **Törlés lehetőséget.** A megerősítéshez válassza az **OK** lehetőséget.

* Jelölje ki az elemet, és nyomja le a delete billentyűt. A megerősítéshez válassza az **OK** lehetőséget.

* Válassza ki az elemet, hogy az elemhez megnyíljon a Részletek panel. A panel jobb felső sarkában nyissa meg a három pont **(...**) menüt, és válassza a **Törlés lehetőséget.** A megerősítéshez válassza az **OK** lehetőséget.

  ![Képernyőkép egy kijelölt elemről a tervezőben a megnyitott részletek panelen, valamint a kijelölt három pont gombbal és a "Törlés" paranccsal.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > Ha a három pont menü nem látható, bontsa ki a Visual Studio Code ablakot elég szélesre, hogy a részletek ablaktáblán a jobb felső sarokban lévő három pont **(...)** gomb megjelenik.

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>Hibák és problémák elhárítása

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>A tervező nem nyílik meg

Amikor megpróbálja megnyitni a tervezőt, a következő hibaüzenet jelenik meg: "A munkafolyamat tervezési **ideje nem indult el"**. Ha korábban megpróbálta megnyitni a tervezőt, majd megszüntette vagy törölte a projektet, előfordulhat, hogy a bővítménycsomag nem tölt le megfelelően. Annak ellenőrzéshez, hogy ez a probléma oka-e, kövesse az alábbi lépéseket:

  1. A Visual Studio nyissa meg a Kimenet ablakot. A Nézet **menüben** válassza a Kimenet **lehetőséget.**

  1. A Kimenet ablak címsorában található listában válassza a Azure Logic Apps **(előzetes verzió)** lehetőséget, hogy áttekintse a bővítmény kimenetét, például:

     ![Képernyőkép a Kimenet ablakról, a kijelölt "Azure Logic Apps" lehetőségekkel.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. Tekintse át a kimenetet, és ellenőrizze, hogy megjelenik-e ez a hibaüzenet:

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   A hiba megoldásához törölje az **ExtensionBundles** mappát ezen a helyen **...\Users \{ your-username}\AppData\Local\Temp\Functions\ExtensionBundles**, majd próbálja meg újra megnyitni a **workflow.js** fájlt a tervezőben.

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Új eseményindítók és műveletek hiányoznak a tervezőválasztóból a korábban létrehozott munkafolyamatokhoz

Azure Logic Apps Előzetes verzió az Azure-függvényműveletek, a Liquid-műveletek és az  XML-műveletek beépített műveleteit támogatja, például az XML-érvényesítést és az **XML-átalakítást.** A korábban létrehozott logikai alkalmazások esetében azonban előfordulhat, hogy ezek a műveletek nem jelennek meg a tervezőválasztóban, és kiválaszthatja, hogy az Visual Studio Code a bővítménycsomag elavult verzióját használja-e. `Microsoft.Azure.Functions.ExtensionBundle.Workflows`

Emellett az **Azure-függvényműveletek** összekötője és műveletei csak akkor jelennek meg a tervezőválasztóban, ha engedélyezte vagy be nem választotta az **Azure-beli** összekötők használatát a logikai alkalmazás létrehozásakor. Ha az alkalmazás létrehozásakor nem engedélyezi az Azure-ban üzembe helyezett összekötőket, a projektből a Code-ban Visual Studio őket. Nyissa meg **workflow.jshelyi menüben,** és válassza az Összekötők használata az **Azure-ból lehetőséget.**

Az elavult csomag kijavítás érdekében kövesse az alábbi lépéseket az elavult csomag törléséhez, így a Visual Studio Code automatikusan a legújabb verzióra frissíti a bővítménycsomagot.

> [!NOTE]
> Ez a megoldás csak azokra a logikai alkalmazásokra vonatkozik, amelyek az Azure Logic Apps (előzetes verzió) bővítő Visual Studio Code használatával hozhatók létre és helyezhetők üzembe, a Azure Portal. Lásd: A tervező által hiányzó támogatott eseményindítók és műveletek a [Azure Portal.](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)

1. Mentsen minden olyan munkát, amely nem szeretne elveszni, és zárja be Visual Studio.

1. A számítógépen keresse meg a következő mappát, amely a meglévő csomag verziószámos mappáit tartalmazza:

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Törölje a korábbi csomag verziómappát, például ha az 1.1.3-as verzióhoz van mappája, törölje azt.

1. Most keresse meg a következő mappát, amely a szükséges NuGet-csomag verziószámos mappáit tartalmazza:

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. Törölje a korábbi csomag verziómappát, például ha van egy mappája az 1.0.0.8-preview verzióhoz, törölje azt a mappát.

1. Nyissa Visual Studio Code-ban, a projektben és aworkflow.js **a tervezőben** található fájlban.

A hiányzó eseményindítók és műveletek most már megjelennek a tervezőben.

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 Hibás kérés" üzenet jelenik meg egy eseményindítón vagy műveleten

Ha egy futtatás sikertelen, és figyelési nézetben vizsgálja meg a futtatásokat, ez a hiba olyan eseményindítón vagy műveleten jelenhet meg, amelynek a neve hosszabb, ezért a mögöttes Uniform Resource Identifier (URI) túllépi az alapértelmezett karakterkorlátot.

A probléma megoldásához és a hosszabb URI-hoz való beállításhoz szerkessze a és beállításkulcsokat a számítógépen `UrlSegmentMaxCount` `UrlSegmentMaxLength` az alábbi lépések szerint. A kulcs alapértelmezett értékeit ez a témakör ismerteti, amely a Windows [Http.sys beállításjegyzék-beállításait ismerteti.](/troubleshoot/iis/httpsys-registry-windows)

> [!IMPORTANT]
> Mielőtt elkezdené, mentse a munkáját. Ehhez a megoldáshoz újra kell indítania a számítógépet, miután végzett, hogy a módosítások érvénybe lépnek.

1. A számítógépen nyissa meg a **Futtatás ablakot,** és futtassa a `regedit` parancsot, amely megnyitja a beállításszerkesztőt.

1. A Felhasználói **fiókok felügyeletének mezőben** válassza az **Igen lehetőséget** a módosítások a számítógépen való engedélyéhez.

1. A bal oldali panel Számítógép **csomópontja alatt** bontsa ki a csomópontokat az elérési út mentén, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters** a Paraméterek **lehetőséget.**

1. A jobb oldali panelen keresse meg a és a `UrlSegmentMaxCount` `UrlSegmentMaxLength` beállításkulcsot.

1. Növelje elégre ezeket a kulcsértékeket, hogy az URI-k elférnek a használni kívánt neveken. Ha ezek a kulcsok nem léteznek, adja hozzá őket a **Parameters** mappához az alábbi lépéseket követve:

   1. A Paraméterek **helyi** menüben válassza az **Új**  >  **DWORD (32 bites) érték lehetőséget.**

   1. A megjelenő szerkesztési mezőbe írja be a `UrlSegmentMaxCount` következőt az új kulcsnévként: .

   1. Nyissa meg az új kulcs helyi menüjét, és válassza a **Módosítás lehetőséget.**

   1. A megjelenő **Sztring** szerkesztése mezőben  adja meg az Érték adatkulcs hexadecimális vagy decimális formátumban kívánt értékét. Hexadecimális értékben például a `400` tizedes `1024` törtnek felel meg.

   1. A kulcs értékének `UrlSegmentMaxLength` hozzáadásához ismételje meg ezeket a lépéseket.

   A kulcsértékek növelése vagy hozzáadása után a beállításszerkesztő az alábbi példához hasonlít:

   ![Képernyőkép a beállításszerkesztőről.](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. Ha elkészült, indítsa újra a számítógépet, hogy a módosítások érvénybe lépjenek.

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>A hibakeresési munkamenet nem indul el

Amikor hibakeresési munkamenetet próbál elindítani, a következő hibaüzenet jelenik meg: "Hiba történt a **preLaunchTask generateDebugSymbols"** futtatása után. A probléma megoldásához szerkessze **atasks.jsfájlban** található fájlt a szimbólum generálás kihagyása érdekében.

1. A projektben bontsa ki a **.vscode** mappát, és nyissa meg **tasks.jsfájlt.**

1. A következő feladatban törölje a sort az előző sort lezáró vesszővel `"dependsOn: "generateDebugSymbols"` együtt, például:

   Előtte:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   Utána:

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>Következő lépések

Szívesen meghallgatjuk a tapasztalatait a Azure Logic Apps (előzetes verzió) bővítményben!

* Hibák vagy problémák esetén hozza [létre a problémákat a GitHubon.](https://github.com/Azure/logicapps/issues)
* Ha kérdése, kérése, megjegyzése vagy egyéb visszajelzése van, használja [ezt a visszajelzési űrlapot.](https://aka.ms/lafeedback)
