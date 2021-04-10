---
title: Logic Apps előnézeti munkafolyamatok létrehozása a Azure Portal
description: Munkafolyamatokat hozhat létre és futtathat automatizálási és integrációs forgatókönyvekhez Azure Logic Apps előzetes verzióval a Azure Portalban.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff938d29d998b6fcf0b2cfae72a9a9e685a10dc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563952"
---
# <a name="create-stateful-and-stateless-workflows-in-the-azure-portal-with-azure-logic-apps-preview"></a>Állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Azure Portalban Azure Logic Apps előzetes verzióban

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A [Azure Logic apps előzetes](logic-apps-overview-preview.md)verzióban automatizálási és integrációs megoldásokat építhet ki az alkalmazásokban, az adattárakban, a Cloud Servicesben és a rendszerekben olyan logikai alkalmazások létrehozásával és futtatásával, amelyek az új **logikai alkalmazás (előzetes verzió)** erőforrástípus alapján a Azure Portal [ *állapot* -és *állapot nélküli* munkafolyamatokat](logic-apps-overview-preview.md#stateful-stateless) tartalmaznak. Ezzel az új logikai alkalmazás típussal több munkafolyamatot is létrehozhat, amelyek az újratervezett Azure Logic Apps előzetes verziós futtatókörnyezettel rendelkeznek, amely hordozhatóságot, jobb teljesítményt és rugalmasságot biztosít különböző üzemeltetési környezetekben történő üzembe helyezéshez és működtetéshez, nem csak az Azure-ban, hanem a Docker-tárolókban is. Az új logikai alkalmazás típusával kapcsolatos további tudnivalókért tekintse meg a [Azure Logic apps előzetes verziójának áttekintését](logic-apps-overview-preview.md).

![A "Logic app (előzetes verzió)" erőforráshoz tartozó Munkafolyamat-tervezővel Azure Portal bemutató képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/azure-portal-logic-apps-overview.png)

A Azure Portal elindíthat egy új **logikai alkalmazás (előzetes verzió)** erőforrás létrehozásával. Noha [a Visual Studio Code-ban létrehoz egy projektet a Azure Logic apps (előzetes verzió) bővítménnyel](create-stateful-stateless-workflows-visual-studio-code.md), mindkét módszer lehetővé teszi, hogy a logikai alkalmazást ugyanolyan típusú üzemeltetési környezetekben lehessen üzembe helyezni és futtatni.

Addig is létrehozhatja az eredeti logikai alkalmazás típusát. Bár a portál fejlesztői tapasztalatai eltérnek az eredeti és az új logikai alkalmazás típusától, az Azure-előfizetés mindkét típust magában foglalhatja. Az Azure-előfizetésben megtekintheti és elérheti az összes telepített Logic apps-alkalmazást, de az alkalmazások a saját kategóriákba és csoportokba vannak rendezve.

Ez a cikk bemutatja, hogyan hozhatja létre a logikai alkalmazást és a munkafolyamatot a Azure Portal a **Logic app (előzetes verzió)** erőforrástípus használatával, és hajtsa végre a következő magas szintű feladatokat:

* Hozza létre az új logikai alkalmazás erőforrását, és adjon hozzá egy üres munkafolyamatot.

* Adjon hozzá egy triggert és egy műveletet.

* Munkafolyamat-Futtatás elindítása.

* A munkafolyamat futtatási és kiváltó előzményeinek megtekintése.

* Az üzembe helyezést követően engedélyezze vagy nyissa meg a Application Insights.

* Állapot nélküli munkafolyamatok futtatási előzményeinek engedélyezése.

> [!NOTE]
> Az aktuális ismert problémákkal kapcsolatos információkért tekintse át a [Logic apps nyilvános előzetes verzió ismert problémáit ismertető oldalt a githubon](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy [Azure Storage-fiók](../storage/common/storage-account-overview.md) , mivel a **logikai alkalmazás (előzetes verzió)** erőforrása Azure functions, és [tárolási követelményekkel rendelkezik, mint a Function apps](../azure-functions/storage-considerations.md). Használhat meglévő Storage-fiókot, vagy létrehozhat egy Storage-fiókot előre vagy a logikai alkalmazás létrehozása során.

  > [!NOTE]
  > Az [állapot-nyilvántartó Logic apps](logic-apps-overview-preview.md#stateful-stateless) tárolási tranzakciókat hajt végre, például várólistákat használ a táblázatok és Blobok munkafolyamat-állapotának ütemezéséhez és tárolásához. Ezek a tranzakciók az [Azure Storage-díjakat terhelik](https://azure.microsoft.com/pricing/details/storage/). További információ arról, hogy az állapot-nyilvántartó Logic apps hogyan tárolja az adatokat a külső tárolóban, lásd: állapot-nyilvántartó és [állapot nélküli](logic-apps-overview-preview.md#stateful-stateless).

* Docker-tárolóba való üzembe helyezéshez szüksége van egy meglévő Docker-tároló lemezképére. Létrehozhatja például ezt a rendszerképet [Azure Container Registry](../container-registry/container-registry-intro.md), [app Service](../app-service/overview.md)vagy az [Azure Container instance](../container-instances/container-instances-overview.md)használatával. 

* Ha ugyanezt a logikai alkalmazást szeretné felépíteni ebben a cikkben, szüksége van egy Office 365 Outlook e-mail fiókra, amely Microsoft munkahelyi vagy iskolai fiókot használ a bejelentkezéshez.

  Ha úgy dönt, hogy más, [Azure Logic apps által támogatott e-mail-összekötőt](/connectors/)használ, például a Outlook.com vagy a [Gmail szolgáltatást](../connectors/connectors-google-data-security-privacy-policy.md), akkor továbbra is követheti a példát, és az általános átfogó lépések ugyanazok, de a felhasználói felület és a beállítások bizonyos módokon eltérőek lehetnek. Ha például az Outlook.com-összekötőt használja, használja a személyes Microsoft-fiók a bejelentkezéshez.

* A cikkben létrehozott példa logikai alkalmazás teszteléséhez olyan eszközre van szükség, amely képes hívásokat küldeni a kérelem-triggernek, amely a példa logikai alkalmazás első lépése. Ha nem rendelkezik ilyen eszközzel, a [Poster](https://www.postman.com/downloads/)letöltése, telepítése és használata is használható.

* Ha olyan beállításokkal hozza létre a logikai alkalmazást, amelyek támogatják a [Application Insights](../azure-monitor/app/app-insights-overview.md)használatát, akkor engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt megteheti a logikai alkalmazás létrehozásakor vagy az üzembe helyezés után is. Rendelkeznie kell egy Application Insights példánnyal, de ezt az erőforrást [előre](../azure-monitor/app/create-workspace-resource.md)is létrehozhatja a logikai alkalmazás létrehozásakor vagy az üzembe helyezés után.

## <a name="create-the-logic-app-resource"></a>A logikai alkalmazás erőforrásának létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Azure Portal keresőmezőbe írja be a kifejezést `logic app preview` , majd válassza a **Logic app (előzetes verzió)** elemet.

   ![Képernyőfelvétel: a "Logic app Preview" keresési kifejezés és a "Logic app (előzetes verzió)" erőforrás kiválasztására szolgáló Azure Portal keresési mező.](./media/create-stateful-stateless-workflows-azure-portal/find-logic-app-resource-template.png)

1. A **logikai alkalmazás (előzetes verzió)** lapon válassza a **Hozzáadás** lehetőséget.

1. A **logikai alkalmazás létrehozása (előzetes verzió)** lapon az **alapok** lapon adja meg a logikai alkalmazással kapcsolatos információkat.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Előfizetés** | Yes | <*Azure-előfizetés – név*> | A logikai alkalmazáshoz használandó Azure-előfizetés. |
   | **Erőforráscsoport** | Yes | <*Azure-Erőforrás-csoport-név*> | Az Azure-erőforráscsoport, amelyben létrehozza a logikai alkalmazást és a kapcsolódó erőforrásokat. Az erőforrás nevének egyedinek kell lennie a régiók között, és csak betűket, számokat, kötőjeleket ( **-** ), aláhúzást (**_**), zárójeleket (**()**) és pontokat (**.**) tartalmazhat. <p><p>Ez a példa létrehoz egy nevű erőforráscsoportot `Fabrikam-Workflows-RG` . |
   | **Logikai alkalmazás neve** | Yes | <*logikai alkalmazás neve*> | A logikai alkalmazáshoz használandó név. Az erőforrás nevének egyedinek kell lennie a régiók között, és csak betűket, számokat, kötőjeleket ( **-** ), aláhúzást (**_**), zárójeleket (**()**) és pontokat (**.**) tartalmazhat. <p><p>Ez a példa egy nevű logikai alkalmazást hoz létre `Fabrikam-Workflows` . <p><p>**Megjegyzés**: a logikai alkalmazás neve automatikusan beolvassa az utótagot, `.azurewebsites.net` mivel a **logikai alkalmazás (előzetes verzió)** erőforrása Azure functions, amely ugyanazt az alkalmazás-elnevezési konvenciót használja. |
   | **Közzététel** | Yes | <*üzembe helyezés – környezet*> | A logikai alkalmazás üzembe helyezési célhelye. A **munkafolyamat** vagy a **Docker-tároló** kiválasztásával üzembe helyezheti az Azure-ban. <p><p>Ez a példa **munkafolyamatot** használ, amely a **logikai alkalmazás (előzetes verzió)** erőforrását telepíti a Azure Portal. <p><p>**Megjegyzés**: a **Docker**-tároló kiválasztása előtt győződjön meg róla, hogy létrehozta a Docker-tároló rendszerképét. Létrehozhatja például ezt a rendszerképet [Azure Container Registry](../container-registry/container-registry-intro.md), [app Service](../app-service/overview.md)vagy az [Azure Container instance](../container-instances/container-instances-overview.md)használatával. Így a **Docker-tároló** kiválasztása után [megadhatja a logikai alkalmazás beállításaiban használni kívánt tárolót](#set-docker-container). |
   | **Régió** | Yes | <*Azure-régió*> | Az erőforráscsoport és az erőforrások létrehozásakor használandó Azure-régió. <p><p>Ez a példa az **USA nyugati** régióját használja. |
   |||||

   Bemutatunk egy példát:

   ![Képernyőfelvétel: a Azure Portal és a "logikai alkalmazás létrehozása (előzetes verzió)" oldal.](./media/create-stateful-stateless-workflows-azure-portal/create-logic-app-resource-portal.png)

1. Ezután a gazdagép lapon adja meg ezt az **információt a logikai** alkalmazáshoz használni kívánt tárolási megoldásról és üzemeltetési tervről.

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Storage-fiók** | Yes | <*Azure-Storage-fiók-név*> | A tárolási tranzakciókhoz használandó [Azure Storage-fiók](../storage/common/storage-account-overview.md) . Az erőforrás nevének egyedinek kell lennie a régiók között, és 3-24 karakterből kell állnia, és csak számokat és kisbetűket tartalmazhat. Válasszon ki egy meglévő fiókot, vagy hozzon létre egy új fiókot. <p><p>Ez a példa egy nevű Storage-fiókot hoz létre `fabrikamstorageacct` . |
   | **Csomag típusa** | Yes | <*Azure-üzemeltetési csomag*> | A logikai alkalmazás üzembe helyezésére szolgáló [üzemeltetési terv](../app-service/overview-hosting-plans.md) , amely vagy [**functions Premium**](../azure-functions/functions-premium-plan.md) vagy [ **app Service-csomag** (dedikált)](../azure-functions/dedicated-plan.md). Az Ön választása befolyásolja a később elérhető képességeket és árképzési szinteket. <p><p>Ez a példa az **app Service-csomagot** használja. <p><p>**Megjegyzés**: a Azure Functionshöz hasonlóan a **logikai alkalmazás (előzetes verzió)** erőforrástípus üzemeltetési tervet és díjszabási szintet igényel. A felhasználási csomagok nem támogatottak, és nem érhetők el ehhez az erőforrás-típushoz. További információkért tekintse át a következő témaköröket: <p><p>- [Méretezés és üzemeltetés Azure Functions](../azure-functions/functions-scale.md) <br>- [A App Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/) <p><p>A functions Premium csomag például hozzáférést biztosít a hálózati képességekhez, például az Azure-beli virtuális hálózatokhoz való csatlakozáshoz és az azokhoz való hozzáféréshez, hasonlóan a Azure Functionshoz, mint a logikai alkalmazások létrehozásakor és telepítésekor. További információkért tekintse át a következő témaköröket: <p><p>- [Azure Functions hálózati beállítások](../azure-functions/functions-networking-options.md) <br>- [Azure Logic Apps a bárhonnan hálózati lehetőségeket a Azure Logic Apps előzetes verziójával](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047) |
   | **Windows-csomag** | Yes | <*csomag neve*> | A használandó csomag neve. Válasszon ki egy meglévő csomagot, vagy adja meg egy új csomag nevét. <p><p>Ez a példa a nevet használja `Fabrikam-Service-Plan` . |
   | **SKU és size** | Yes | <*díjszabás – réteg*> | A logikai alkalmazás üzemeltetéséhez használt [díjszabási](../app-service/overview-hosting-plans.md) csomag. A beállításokat a korábban kiválasztott csomag típusa érinti. Ha módosítani szeretné az alapértelmezett szintet, válassza a **méret módosítása** lehetőséget. Ezután kiválaszthatja az egyéb díjszabási szinteket a szükséges munkaterhelés alapján. <p><p>Ez a példa az ingyenes **F1 árképzési szintet** használja a **fejlesztési és tesztelési** feladatokhoz. További információkért tekintse át [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/). |
   |||||

1. Ezt követően, ha a létrehozási és telepítési beállítások támogatják a [Application Insights](../azure-monitor/app/app-insights-overview.md)-t, lehetősége van engedélyezni a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz.

   1. Ha még nincs kiválasztva a **figyelés** lap **Application Insights** területén, állítsa az **Engedélyezés Application Insights** **Igen értéket** .

   1. A **Application Insights** beállításnál válasszon ki egy meglévő Application Insights példányt, vagy ha új példányt szeretne létrehozni, válassza az **új létrehozása** lehetőséget, és adja meg a használni kívánt nevet.

1. Miután az Azure érvényesíti a logikai alkalmazás beállításait, a **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

   Például:

   ![A Azure Portal és az új logikai alkalmazás erőforrás-beállításait bemutató képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > Ha a **Létrehozás** gombra kattintva érvényesítési hibaüzenetet kap, nyissa meg és tekintse át a hiba részleteit. Ha például a kiválasztott régió eléri a létrehozni kívánt erőforrások kvótáját, lehetséges, hogy egy másik régiót kell kipróbálnia.

   Miután az Azure befejezte az üzembe helyezést, a logikai alkalmazás automatikusan működik és fut, de még nem csinál semmit, mert nem létezik munkafolyamat.

1. Az üzembe helyezés befejezése lapon válassza az **erőforráshoz való ugrás** lehetőséget, így elkezdheti felépíteni a munkafolyamatot. Ha a logikai alkalmazás üzembe helyezéséhez a **Docker-tárolót** választotta, folytassa a [Docker-tárolóval kapcsolatos információk megadásához szükséges lépésekkel](#set-docker-container).

   ![A Azure Portalt és a befejezett telepítést bemutató képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="set-docker-container"></a>

## <a name="specify-docker-container-for-deployment"></a>Docker-tároló megadása a központi telepítéshez

Mielőtt elkezdené ezeket a lépéseket, szüksége van egy Docker-tároló rendszerképére. Létrehozhatja például ezt a rendszerképet [Azure Container Registry](../container-registry/container-registry-intro.md), [app Service](../app-service/overview.md)vagy az [Azure Container instance](../container-instances/container-instances-overview.md)használatával. Ezután a logikai alkalmazás létrehozása után a Docker-tárolóval kapcsolatos információkat adhat meg.

1. A Azure Portal nyissa meg a logikai alkalmazás erőforrását.

1. A logikai alkalmazás menü **Beállítások** területén válassza a **központi telepítési központ** elemet.

1. A **központi telepítési központ** ablaktáblán kövesse a Docker-tároló részleteinek biztosításához és kezeléséhez szükséges utasításokat.

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>Üres Munkafolyamat hozzáadása

1. Miután az Azure megnyitta az erőforrást, a logikai alkalmazás menüjében válassza a **munkafolyamatok** lehetőséget. A **munkafolyamatok** eszköztáron válassza a **Hozzáadás** lehetőséget.

   ![Képernyőfelvétel: a logikai alkalmazás erőforrás menüjének kijelölése a "munkafolyamatok" beállítással, majd a "Hozzáadás" lehetőség be van jelölve az eszköztáron.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. Az **Új munkafolyamat** ablaktábla megnyitása után adja meg a munkafolyamat nevét, és válassza ki az [ **állapot** vagy **állapot nélküli**](logic-apps-overview-preview.md#stateful-stateless) munkafolyamat típusát. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   Ebben a példában egy nevű üres állapot-nyilvántartó munkafolyamatot ad meg `Fabrikam-Stateful-Workflow` . Alapértelmezés szerint a munkafolyamat engedélyezve van, de nem végez semmit, amíg nem ad hozzá egy triggert és műveletet.

   ![Képernyőkép, amely az újonnan hozzáadott üres, állapot-nyilvántartó munkafolyamatot mutatja: "Fabrikam-állapot-munkafolyamat".](./media/create-stateful-stateless-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. Ezután nyissa meg az üres munkafolyamatot a tervezőben, hogy hozzá lehessen adni egy triggert és egy műveletet.

   1. A munkafolyamat listából válassza ki az üres munkafolyamatot.

   1. A munkafolyamat menü **fejlesztő** területén válassza a **tervező** lehetőséget.

      A tervező felületen a **művelet kiválasztása** parancssor már megjelenik, és alapértelmezés szerint ki van választva, így az **trigger hozzáadása** ablaktábla is megnyílik.

      ![Képernyőfelvétel: a megnyitott Munkafolyamat-tervező a tervező felületen kiválasztott "válasszon műveletet".](./media/create-stateful-stateless-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>Trigger és művelet hozzáadása

Ez a példa egy olyan munkafolyamatot hoz létre, amely a következő lépésekkel rendelkezik:

* A beépített [kérelem triggere](../connectors/connectors-native-reqres.md), **Amikor HTTP-kérés érkezik**, amely bejövő hívásokat fogad, és olyan végpontot hoz létre, amelyet más szolgáltatások vagy logikai alkalmazások hívhatnak meg.

* Az [Office 365 Outlook művelet](../connectors/connectors-create-api-office365-outlook.md), **e-mail küldése**.

* A beépített [Válasz művelet](../connectors/connectors-native-reqres.md), amellyel a rendszer visszaküldi a választ, és visszaküldi azokat a hívónak.

### <a name="add-the-request-trigger"></a>A kérelem-trigger hozzáadása

Mielőtt hozzáadhat egy triggert egy üres munkafolyamathoz, győződjön meg arról, hogy a Munkafolyamat-tervező meg van nyitva, és hogy a **művelet kiválasztása kérdés ki** van választva a tervező felületen.

1. A tervező felület mellett, az **trigger hozzáadása** ablaktáblán a művelet keresésének **kiválasztása** mezőben győződjön meg arról, hogy a **beépített** lap van kiválasztva. Ezen a lapon a Azure Logic Apps natív módon futó eseményindítók láthatók.

1. A **válasszon műveletet** keresési mezőben adja meg a kifejezést `when a http request` , majd válassza ki a **http-kérelem fogadásakor** megnevezett beépített kérelem-triggert.

   ![A tervezőt bemutató képernyőkép, valamint a * * trigger * * panel hozzáadása a "HTTP-kérelem fogadásakor" trigger kiválasztásával.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger.png)

   Amikor az trigger megjelenik a tervezőben, megnyílik az trigger részletek panelje, amely megjeleníti az trigger tulajdonságait, beállításait és egyéb műveleteit.

   ![Képernyőkép, amely a "HTTP-kérés fogadásakor" triggert jeleníti meg, és megnyílik a részletek panel](./media/create-stateful-stateless-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > Ha a részleteket tartalmazó ablaktábla nem jelenik meg, ellenőrizze, hogy az trigger ki van-e választva a tervezőben.

1. Ha törölni szeretne egy elemet a tervezőből, kövesse az [alábbi lépéseket az elemek tervezőből való törléséhez](#delete-from-designer).

1. A munka mentéséhez a tervező eszköztárán válassza a **Mentés** lehetőséget.

   Amikor első alkalommal ment egy munkafolyamatot, és a munkafolyamat kérelem-triggerrel kezdődik, a Logic Apps szolgáltatás automatikusan létrehoz egy URL-címet a kérelem-trigger által létrehozott végponthoz. Később, amikor teszteli a munkafolyamatot, elküld egy kérést erre az URL-címre, amely kiváltja az eseményindítót, és elindítja a munkafolyamat futtatását.

### <a name="add-the-office-365-outlook-action"></a>Az Office 365 Outlook-művelet hozzáadása

1. A tervezőben a hozzáadott trigger alatt válassza az **új lépés** lehetőséget.

   A **válasszon műveletet** kérő üzenet jelenik meg a tervezőben, és a **művelet hozzáadása** ablaktábla újra megnyílik, így kiválaszthatja a következő műveletet.

   > [!NOTE]
   > Ha az **Add a Action (művelet hozzáadása** ) ablaktábla megjeleníti a hibaüzenetet, a "nem definiált" tulajdonság nem olvasható be, mentse a munkafolyamatot, töltse újra az oldalt, nyissa meg újra a munkafolyamatot, és próbálkozzon újra.

1. Az **Add a Action** (művelet hozzáadása) panelen, a **művelet** keresése mezőben válassza az **Azure** lehetőséget. Ezen a lapon láthatók azok a felügyelt összekötők, amelyek elérhetők és üzembe helyezhetők az Azure-ban.

   > [!NOTE]
   > Ha a **művelet hozzáadása** ablaktábla megjeleníti a hibaüzenetet, `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` mentse a munkafolyamatot, töltse újra a lapot, nyissa meg újra a munkafolyamatot, majd próbálja meg újból hozzáadni a műveletet.

   Ez a példa a **Send an email (v2)** nevű Office 365 Outlook-műveletet használja.

   ![A tervezőt bemutató képernyőkép és a * * művelet hozzáadása * * ablaktábla az Office 365 Outlook "e-mail küldése" művelet kiválasztásával.](./media/create-stateful-stateless-workflows-azure-portal/find-send-email-action.png)

1. A művelet részletek ablaktábláján, a **kapcsolat létrehozása** lapon válassza a **Bejelentkezés** lehetőséget, hogy létre tudja hozni a kapcsolatát az e-mail-fiókjával.

   ![Képernyőfelvétel: a tervező és az "e-mail küldése (v2)" részleteket tartalmazó ablaktábla, amelyen be van jelölve a "Bejelentkezés" lehetőség.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-sign-in.png)

1. Ha a rendszer megkéri, hogy adja meg az e-mail-fiókjához való hozzáférést, jelentkezzen be a fiókja hitelesítő adataival.

   > [!NOTE]
   > Ha a hibaüzenet jelenik meg, `Failed with error: 'The browser is closed.'. Please sign in again` ellenőrizze, hogy a böngésző blokkolja-e a harmadik féltől származó cookie-kat. Ha ezek a cookie-k le vannak tiltva, próbálja meg hozzáadni `https://portal.azure.com` a cookie-kat használó helyek listájához. Ha inkognitóban üzemmódot használ, ügyeljen arra, hogy a külső cookie-k ne legyenek letiltva az adott módban való munka közben.
   > 
   > Szükség esetén töltse be újra a lapot, nyissa meg a munkafolyamatot, adja hozzá újra az e-mail műveletet, és próbálja meg létrehozni a kapcsolat létrehozását.

   Miután az Azure létrehozta a kapcsolatát, az **E-mail küldése** művelet megjelenik a tervezőben, és alapértelmezés szerint ki van választva. Ha a művelet nincs kiválasztva, válassza ki a műveletet, hogy a részleteket tartalmazó ablaktábla is meg legyen nyitva.

1. A művelet részletek ablaktáblájának **Paraméterek** lapján adja meg a művelethez szükséges adatokat, például:

   ![Képernyőfelvétel: a tervező és az "e-mail küldése" részleteit tartalmazó ablaktábla, ahol a "parameters" (paraméterek) lapon be van jelölve.](./media/create-stateful-stateless-workflows-azure-portal/send-email-action-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Ide:** | Yes | <*saját e-mail cím*> | Az e-mail címzettje, amely a tesztelési célú e-mail-címe lehet. Ez a példa a fiktív e-mailt használja `sophiaowen@fabrikam.com` . |
   | **Tárgy** | Yes | `An email from your example workflow` | Az e-mail tárgya |
   | **Törzs** | Yes | `Hello from your example workflow!` | Az e-mail szövegtörzsének tartalma |
   ||||

   > [!NOTE]
   > Ha módosításokat végez a **Beállítások**, a **statikus eredmény** vagy a lapok **után** , a részletek ablaktáblán, akkor a **kész** gombra kattintva véglegesítse ezeket a módosításokat, mielőtt átváltja a lapokat, vagy módosítani szeretné a fókuszt a tervezőre. Ellenkező esetben a tervező nem fogja megőrizni a módosításokat.

1. Mentse a munkáját. A tervező eszköztárán válassza a **Mentés** lehetőséget.

1. Ha a környezete szigorú hálózati követelményekkel vagy a forgalmat korlátozó tűzfalakkal rendelkezik, be kell állítania a munkafolyamatban létező összes trigger-vagy műveleti kapcsolat engedélyeit. A teljes minősítés megkeresése 

   Ellenkező esetben a munkafolyamat teszteléséhez [futtasson manuálisan egy futtatást](#trigger-workflow).

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>A tűzfal-hozzáféréshez használt tartománynevek keresése

Mielőtt telepítené a logikai alkalmazást, és futtatja a munkafolyamatot a Azure Portalban, ha a környezete szigorú hálózati követelményekkel vagy a forgalmat korlátozó tűzfalakkal rendelkezik, a logikai alkalmazásban található munkafolyamatokban be kell állítania a hálózati vagy a tűzfal engedélyeit.

A kapcsolatok teljes tartománynevének (FQDN) megkereséséhez kövesse az alábbi lépéseket:

1. A logikai alkalmazás menüjében, a **munkafolyamatok** területen válassza a **kapcsolatok** lehetőséget. Az **API-kapcsolatok** lapon válassza ki a kapcsolat erőforrásának nevét, például:

   ![Képernyőfelvétel: a Azure Portal és a Logic app menü, amely a "connections" és a "offic365" kapcsolati erőforrás nevét tartalmazza.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connections.png)

1. Elég széles a böngésző kibontása ahhoz, hogy a böngésző jobb felső sarkában megjelenjen a **JSON-nézet** , válassza a **JSON-nézet** lehetőséget.

   ![Képernyőfelvétel: a Azure Portal és az API-kapcsolatok ablaktáblája a "JSON nézettel" van kiválasztva.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-view-json.png)

1. A tulajdonságérték biztonságos megkeresése, másolása és mentése, `connectionRuntimeUrl` így a tűzfal beállítható ezen információk használatával.

   ![A "connectionRuntimeUrl" tulajdonság kiválasztott értékét megjelenítő képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. Az egyes kapcsolatok esetében ismételje meg a megfelelő lépéseket.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>A munkafolyamat elindítása

Ebben a példában a munkafolyamat akkor fut le, amikor a kérelem-trigger bejövő kérelmet kap, amelyet az trigger által létrehozott végpont URL-címére küld a rendszer. Amikor első alkalommal mentette a munkafolyamatot, a Logic Apps szolgáltatás automatikusan létrehozta ezt az URL-címet. Így a kérelem elküldéséhez a munkafolyamat indításához meg kell keresnie ezt az URL-címet.

1. A Munkafolyamat-tervezőben válassza ki a **http-kérelem fogadásakor** megnevezett kérelem-triggert.

1. A részletek ablaktábla megnyitása után a parameters ( **Paraméterek** ) lapon keresse meg a **http post URL-cím** tulajdonságot. A generált URL-cím másolásához válassza ki a **másolási URL-** címet (fájl másolása ikon), és most mentse el az URL-címet. Az URL-cím a következő formátumot követi:

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![A "HTTP POST URL" tulajdonságban a tervező és a végpont URL-címének megadását bemutató képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/find-request-trigger-url.png)

   Ebben a példában az URL-cím a következőképpen néz ki:

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > A végpont URL-címét a logikai alkalmazás **Áttekintés** paneljén is megtalálhatja a **munkafolyamat URL-címe** tulajdonságban.
   >
   > 1. Az erőforrás menüben válassza az **Áttekintés** lehetőséget.
   > 1. Az **Áttekintés** panelen keresse meg a **munkafolyamat URL-** tulajdonságát.
   > 1. A végpont URL-címének másolásához vigye a mutatót a végpont URL-címének végére, és válassza a **Másolás a vágólapra** (fájl másolása ikon) lehetőséget.

1. Az URL-cím teszteléséhez a kérelem elküldésével nyissa meg a [Poster](https://www.postman.com/downloads/) vagy a kívánt eszközt a kérelmek létrehozásához és küldéséhez.

   Ez a példa a Poster használatával folytatódik. További információ: [poster első lépések](https://learning.postman.com/docs/getting-started/introduction/).

   1. Az utólagos eszköztáron válassza az **új** lehetőséget.

      ![Képernyőkép, amely az új gomb kiválasztásával mutatja be a Poster elemet](./media/create-stateful-stateless-workflows-azure-portal/postman-create-request.png)

   1. Az **új ablaktábla létrehozás** paneljén kattintson a **kérelem** **elemre**.

   1. A kérelem **mentése** ablakban a kérelem **neve** alatt adja meg a kérelem nevét, például: `Test workflow trigger` .

   1. A **menteni kívánt gyűjtemény vagy mappa kiválasztása** területen válassza a **gyűjtemény létrehozása** lehetőséget.

   1. A **minden gyűjtemény** területen adja meg a kérések rendszerezéséhez létrehozandó gyűjtemény nevét, nyomja le az ENTER billentyűt, majd válassza a **Mentés lehetőséget <*gyűjtemény neve* >** elemre. Ez a példa `Logic Apps requests` a gyűjtemény nevét használja.

      A Poster kérelmi panelje megnyílik, így kérést küldhet a végpont URL-címére a kérelem triggere számára.

      ![Képernyőkép, amely a megnyitott kérelem ablaktáblán jeleníti meg a Poster-t](./media/create-stateful-stateless-workflows-azure-portal/postman-request-pane.png)

   1. A kérelem ablaktáblán, a metódus lista melletti cím mezőben, amely jelenleg az alapértelmezett kérelem módszerét **mutatja,** illessze be a korábban másolt URL-címet, majd válassza a **Küldés** lehetőséget.

      ![Képernyőkép, amely a Poster és a végpont URL-címét jeleníti meg a cím mezőben, a Küldés gomb kiválasztásával](./media/create-stateful-stateless-workflows-azure-portal/postman-test-endpoint-url.png)

      Amikor az eseményindító elindul, a példa a munkafolyamat futtatására és egy, a példához hasonlóként megjelenő e-mailt küld.

      ![Az Outlook e-maileket megjelenítő képernyőkép a példában leírtak szerint](./media/create-stateful-stateless-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Állapot-nyilvántartó munkafolyamatok esetén az egyes munkafolyamatok futtatása után megtekintheti a futtatási előzményeket, beleértve a teljes Futtatás állapotát, az triggerhez, valamint az egyes műveletekhez, valamint azok bemeneteit és kimeneteit. A Azure Portal a futtatási előzmények és az triggerek előzményei a munkafolyamat szintjén jelennek meg, nem a logikai alkalmazás szintjén. Az trigger előzményeinek a futtatási előzmények kontextuson kívüli áttekintését lásd: trigger-előzmények [áttekintése](#view-trigger-histories).

1. A Azure Portal a munkafolyamat menüjében válassza a **figyelés** lehetőséget.

   A **figyelő** ablaktábla megjeleníti a munkafolyamat futtatási előzményeit.

   ![Képernyőkép a munkafolyamat "figyelő" paneljéről és futtatási előzményeiről.](./media/create-stateful-stateless-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > Ha a legutóbbi futtatási állapot nem jelenik meg, a **figyelő** ablaktábla eszköztárán válassza a **frissítés** lehetőséget. A rendszer nem teljesíti az olyan triggereket, amelyek nem teljesülő feltételek miatt kimaradnak, vagy nem találnak semmilyen adatsort.

   | Futtatás állapota | Leírás |
   |------------|-------------|
   | **Megszakítva** | A Futtatás leállt vagy nem fejeződik be külső problémák miatt, például rendszerleállás vagy elévült Azure-előfizetés. |
   | **Megszakítva** | A Futtatás elindítva és elindítva, de megszakítási kérelem érkezett. |
   | **Sikertelen** | A Futtatás során legalább egy művelet sikertelen volt. A munkafolyamatban nem történt további művelet, amely a hiba kezelésére lett beállítva. |
   | **Futó** | A Futtatás aktiválva lett, és folyamatban van, de ez az állapot a [művelet korlátai](logic-apps-limits-and-config.md) vagy a [jelenlegi díjszabási csomag](https://azure.microsoft.com/pricing/details/logic-apps/)miatt szabályozott Futtatás esetén is megjelenhet. <p><p>**Tipp**: Ha [diagnosztikai naplózást](monitor-logic-apps-log-analytics.md)állít be, a megjelenő összes szabályozási eseményről információt kaphat. |
   | **Sikeres** | A Futtatás sikerült. Ha bármilyen művelet meghiúsult, a munkafolyamat egy későbbi művelete ezt a hibát kezelte. |
   | **Időtúllépés** | A Futtatás időtúllépés miatt meghaladta az időkorlátot, mert a jelenlegi időtartam túllépte a futtatási időtartam határértékét, amelyet a [ **futtatási Előzmények megőrzése nap** beállításban](logic-apps-limits-and-config.md#run-duration-retention-limits)szabályoz. A Futtatás időtartamát a Futtatás kezdő időpontja és a futtatási időtartam korlátja alapján számítjuk ki a kezdési időpontnál. <p><p>**Megjegyzés**: Ha a Futtatás időtartama szintén meghaladja az aktuális *futtatási előzmények megőrzési korlátját* is, amelyet a futtatási [ **Előzmények megőrzésének napja** beállítás](logic-apps-limits-and-config.md#run-duration-retention-limits)is szabályoz, a futtatást a rendszer a napi törlési feladatokból törli a futtatási előzményekből. Függetlenül attól, hogy a futási idő ki vagy fejeződik be, a megőrzési időszakot a rendszer mindig a Futtatás kezdési ideje és az *aktuális* megőrzési korlát használatával számítja ki. Tehát ha csökkenti a repülés közbeni futás időtartamának korlátját, a Futtatás időtúllépést eredményez. A Futtatás azonban marad, vagy törlődik a futtatási előzmények alapján, attól függően, hogy a Futtatás időtartama túllépte-e a megőrzési korlátot. |
   | **Várakozó** | A Futtatás nem indult el vagy szüneteltetve van, például egy olyan korábbi munkafolyamat-példány miatt, amely még fut. |
   |||

1. Egy Futtatás egyes lépései állapotának áttekintéséhez válassza ki az áttekinteni kívánt futtatást.

   Megnyílik a Futtatás részletei nézet, és megjeleníti a Futtatás minden lépésének állapotát.

   ![Képernyőfelvétel: a Futtatás részleteit megjelenítő nézet a munkafolyamat egyes lépéseinek állapotával.](./media/create-stateful-stateless-workflows-azure-portal/review-run-details.png)

   A munkafolyamat egyes lépései a következő lehetséges állapotokat tartalmazhatják:

   | Művelet állapota | Ikon | Leírás |
   |---------------|------|-------------|
   | **Megszakítva** | ![A "megszakított" művelet állapotának ikonja][aborted-icon] | A művelet a külső problémák miatt leállt vagy nem zárult, például rendszerleállás vagy elévült Azure-előfizetés. |
   | **Megszakítva** | ![A "megszakított" művelet állapotának ikonja][cancelled-icon] | A művelet futott, de a rendszer visszavonási kérelmet kapott. |
   | **Sikertelen** | ![A "sikertelen" művelet állapotának ikonja][failed-icon] | A művelet sikertelen volt. |
   | **Futó** | ![A "futó" művelet állapotának ikonja][running-icon] | A művelet jelenleg fut. |
   | **Kimarad** | ![A "kihagyott" művelet állapotának ikonja][skipped-icon] | A rendszer kihagyta a műveletet, mert a közvetlenül megelőző művelet meghiúsult. Egy művelet olyan `runAfter` feltétellel rendelkezik, amely megköveteli, hogy az előző művelet sikeresen befejeződik az aktuális művelet futtatása előtt. |
   | **Sikeres** | ![A "sikeres" művelet állapotának ikonja][succeeded-icon] | A művelet sikeresen befejeződött. |
   | **Az újrapróbálkozások sikeresek voltak** | !["Sikeres újrapróbálkozások" művelet ikonja][succeeded-with-retries-icon] | A művelet sikeresen befejeződött, de csak egy vagy több újrapróbálkozás után. Az újrapróbálkozási előzmények áttekintéséhez a futtatási előzmények részletei nézetben válassza ki ezt a műveletet, így megtekintheti a bemeneteket és a kimeneteket. |
   | **Időtúllépés** | !["Időtúllépés" műveleti állapot ikonja][timed-out-icon] | A művelet a művelet beállításaiban megadott időkorlát miatt leállt. |
   | **Várakozó** | ![A "Waiting" művelet állapotának ikonja][waiting-icon] | Egy olyan webhook-műveletre vonatkozik, amely egy hívótól érkező bejövő kérésre vár. |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-azure-portal/waiting.png

1. Egy adott lépéshez tartozó bemenetek és kimenetek áttekintéséhez válassza ki ezt a lépést.

   ![A kiválasztott "e-mail küldése" művelet bemeneteit és kimeneteit bemutató képernyőkép.](./media/create-stateful-stateless-workflows-azure-portal/review-step-inputs-outputs.png)

1. Az adott lépéshez tartozó nyers bemenetek és kimenetek további áttekintéséhez válassza a **nyers bemenetek megjelenítése** vagy a **nyers kimenet megjelenítése** lehetőséget.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>Trigger-előzmények áttekintése

Állapot-nyilvántartó munkafolyamatok esetén áttekintheti az egyes futtatások trigger-előzményeit, beleértve az aktiválási állapotot a bemenetekkel és kimenetekkel együtt, a [futtatási előzmények környezettől](#view-run-history)függetlenül. A Azure Portalban az aktiválási előzmények és a futtatási előzmények a munkafolyamat szintjén jelennek meg, nem a logikai alkalmazás szintjén. A korábbi adatértékek megkereséséhez kövesse az alábbi lépéseket:

1. A Azure Portal a munkafolyamat menüjének **fejlesztői** területén válassza az **aktiválási előzmények** elemet.

   Az **trigger-előzmények** ablaktáblán láthatók a munkafolyamat futtatási előzményei.

1. Egy adott trigger előzményeinek áttekintéséhez válassza ki az azonosítót a futtatáshoz.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>Application Insights engedélyezése vagy megnyitása az üzembe helyezés után

A munkafolyamat végrehajtása során a logikai alkalmazás más eseményekkel együtt bocsát ki telemetria. Ennek a telemetria a segítségével jobban megismerheti a munkafolyamat futását, valamint azt, hogy a Logic Apps futtatókörnyezet hogyan működik különböző módokon. [Application Insights](../azure-monitor/app/app-insights-overview.md)használatával figyelheti a munkafolyamatot, amely közel valós idejű telemetria (élő metrikákat) biztosít. Ezzel a képességgel könnyebben vizsgálhatja meg a hibákat és a teljesítménnyel kapcsolatos problémákat, amikor ezeket az adatait a problémák diagnosztizálásához, a riasztások beállításához és a diagramok létrehozásához használja.

Ha a logikai alkalmazás létrehozása és üzembe helyezése a [Application Insights](../azure-monitor/app/app-insights-overview.md)használatával támogatott, akkor engedélyezheti a diagnosztikai naplózást és a nyomkövetést a logikai alkalmazáshoz. Ezt akkor teheti meg, ha a logikai alkalmazást a Azure Portal vagy a telepítés után hozza létre. Rendelkeznie kell egy Application Insights példánnyal, de ezt az erőforrást [előre](../azure-monitor/app/create-workspace-resource.md)is létrehozhatja a logikai alkalmazás létrehozásakor vagy az üzembe helyezés után.

Ha a Application Insights egy telepített logikai alkalmazásban szeretné engedélyezni, vagy ha már engedélyezve van a Application Insights-irányítópult, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg a telepített logikai alkalmazást.

1. A logikai alkalmazás menü **Beállítások** területén válassza a **Application Insights** lehetőséget.

1. Ha Application Insights nincs engedélyezve, a **Application Insights** panelen válassza a **bekapcsolás Application Insights** lehetőséget. A panel frissítéseinek alján kattintson az **alkalmaz** gombra.

   Ha a Application Insights engedélyezve van, akkor a **Application Insights** ablaktáblán válassza az **Application Insights-adatbázis megtekintése** lehetőséget.

Application Insights megnyitása után áttekintheti a logikai alkalmazás különböző mérőszámait. További információkért tekintse át a következő témaköröket:

* [Azure Logic Apps fut bárhol – figyelő Application Insights – 1. rész](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [Azure Logic Apps fut bárhol – figyelő Application Insights – 2. rész](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>Állapot nélküli munkafolyamatok futtatási előzményeinek engedélyezése

Az állapot nélküli munkafolyamatok egyszerűbb hibakereséséhez engedélyezheti a munkafolyamat futtatási előzményeit, majd letilthatja a futtatási előzményeket, ha elkészült. Kövesse az alábbi lépéseket a Azure Portalhoz, vagy ha a Visual Studio Code-ban dolgozik, tekintse meg az [állapot-nyilvántartó és állapot nélküli munkafolyamatok létrehozása a Visual Studio Code-ban](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)című témakört.

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg a **logikai alkalmazás (előzetes verzió)** erőforrását.

1. A logikai alkalmazás menüjében, a **Beállítások** területen válassza a **konfiguráció** elemet.

1. Az **Alkalmazásbeállítások** lapon válassza az **új alkalmazás beállítása** lehetőséget.

1. Az **alkalmazás hozzáadása/szerkesztése** panel **név** mezőjébe írja be a következő művelet nevét: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Az **érték** mezőbe írja be a következő értéket: `WithStatelessRunHistory`

   Például:

   ![Képernyőfelvétel: a Azure Portal és Logic app (előzetes verzió) erőforrás "Configuration" > "új Alkalmazásbeállítás" < "alkalmazás hozzáadása/szerkesztése" panel megnyitása és a "munkafolyamatok". {yourWorkflowName}. A "OperationOptions" beállítás "WithStatelessRunHistory" értékre van állítva.](./media/create-stateful-stateless-workflows-azure-portal/stateless-operation-options-run-history.png)

1. A feladat befejezéséhez kattintson **az OK gombra**. A **konfigurációs** ablaktábla eszköztárán válassza a **Mentés** lehetőséget.

1. Ha végzett a futtatási előzmények letiltásával, állítsa be a `Workflows.{yourWorkflowName}.OperationOptions` tulajdonságot a értékre `None` , vagy törölje a tulajdonságot és annak értékét.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>Elemek törlése a tervezőből

Ha törölni szeretne egy elemet a munkafolyamatból a tervezőből, kövesse az alábbi lépéseket:

* Jelölje ki az elemet, nyissa meg az elem helyi menüjét (Shift + F10), majd válassza a **Törlés** lehetőséget. A megerősítéshez válassza az **OK** lehetőséget.

* Jelölje ki az elemet, majd nyomja le a DELETE billentyűt. A megerősítéshez válassza az **OK** lehetőséget.

* Válassza ki az elemet, hogy a részleteket tartalmazó ablaktábla megnyíljon az adott elemhez. Az ablaktábla jobb felső sarkában nyissa meg az ellipszisek (**..**.) menüt, és válassza a **Törlés** lehetőséget. A megerősítéshez válassza az **OK** lehetőséget.

  ![Képernyőfelvétel: a megnyitott részletek ablaktáblával és a kijelölt ellipszisek és a "Törlés" paranccsal megjelenített kiválasztott elem a tervezőben.](./media/create-stateful-stateless-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > Ha az ellipszisek menü nem látható, bontsa ki a böngészőablakot, hogy a részleteket tartalmazó ablaktábla megjelenjen a jobb felső sarokban lévő három pont (**..**.) gomb.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>Problémák és hibák elhárítása

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>Az új eseményindítók és műveletek hiányoznak a tervező választóból a korábban létrehozott munkafolyamatokhoz

Azure Logic Apps előzetes verzió támogatja a beépített műveleteket az Azure functions műveletekhez, a folyékony műveletekhez és az XML-műveletekhez, például az **XML-hitelesítéshez** és az **XML-átalakításhoz**. A korábban létrehozott Logic apps esetében azonban előfordulhat, hogy ezek a műveletek nem jelennek meg a tervezőben, így kiválaszthatja, hogy a logikai alkalmazás a kiterjesztési csomag elavult verzióját használja-e `Microsoft.Azure.Functions.ExtensionBundle.Workflows` .

A probléma megoldásához kövesse az alábbi lépéseket az elavult verzió törléséhez, hogy a kiterjesztés kötege automatikusan frissítsen a legújabb verzióra.

> [!NOTE]
> Ez a megoldás csak a **logikai alkalmazás (előzetes verzió)** azon erőforrásaira vonatkozik, amelyeket a Azure Portal használatával hoz létre, nem pedig a Visual Studio Code és a Azure Logic apps (előzetes verzió) bővítmény használatával létrehozott és üzembe helyezett logikai alkalmazásokat. [A Visual Studio Code-ban a tervezőből hiányzik a támogatott eseményindítók és műveletek](create-stateful-stateless-workflows-visual-studio-code.md#missing-triggers-actions).

1. A Azure Portalban állítsa le a logikai alkalmazást.

   1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

   1. Az **Áttekintés** ablaktábla eszköztárán válassza a **Leállítás** lehetőséget.

1. A logikai alkalmazás menüjének **fejlesztői eszközök** területén válassza a **speciális eszközök** elemet.

1. A **speciális eszközök** panelen válassza az **Indítás** lehetőséget, amely megnyitja a logikai alkalmazás kudu-környezetét.

1. A kudu eszköztáron nyissa meg a **hibakeresési konzol** menüt, és válassza a **cmd** elemet. 

   Megnyílik egy konzolablak, ahol a parancssor használatával megkeresheti a köteg mappát. Vagy böngészhet a konzol ablakában megjelenő címtár-struktúrában is.

1. Keresse meg a következő mappát, amely a meglévő csomag verziószámmal ellátott mappáit tartalmazza:

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. Törölje a meglévő csomaghoz tartozó Version mappát. A konzol ablakban futtathatja ezt a parancsot, ahol lecseréli `{bundle-version}` a meglévő verziót:

   `rm -rf {bundle-version}`

   Például: `rm -rf 1.1.3`

   > [!TIP]
   > Ha hibaüzenet jelenik meg, például "engedély megtagadva" vagy "fájl használatban", frissítse a lapot a böngészőben, és ismételje meg az előző lépéseket, amíg el nem törli a mappát.

1. A Azure Portal térjen vissza a logikai alkalmazás **Áttekintés** lapjára, és válassza az **Újraindítás** lehetőséget.

   A portál automatikusan lekéri és a legújabb csomagot használja.

## <a name="next-steps"></a>Következő lépések

Ezt a nyilvános előzetes verziót szeretném hallani a tapasztalatairól!

* Hibák vagy problémák esetén [hozza létre a problémákat a githubon](https://github.com/Azure/logicapps/issues).
* Kérdések, kérések, megjegyzések és egyéb visszajelzések esetén [használja ezt a visszajelzési űrlapot](https://aka.ms/lafeedback).
