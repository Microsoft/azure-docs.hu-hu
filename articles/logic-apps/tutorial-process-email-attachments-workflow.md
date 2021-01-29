---
title: Több Azure-szolgáltatással rendelkező feladatok automatizálása
description: Oktatóanyag – automatizált munkafolyamatok létrehozása az e-mailek feldolgozásához Azure Logic Apps, Azure Storage és Azure Functions használatával
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 02/27/2020
ms.openlocfilehash: 95cc13a79f39888a5be10e423bda4c7cd7c84cb3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054788"
---
# <a name="tutorial-automate-tasks-to-process-emails-by-using-azure-logic-apps-azure-functions-and-azure-storage"></a>Oktatóanyag: feladatok automatizálása az e-mailek feldolgozásához Azure Logic Apps, Azure Functions és Azure Storage használatával

Az Azure Logic Apps segítségével automatizálhatja a munkafolyamatait, és integrálhatja az adatokat különböző Azure- és Microsoft-szolgáltatások, más szolgáltatott szoftveralkalmazások (SaaS), valamint a helyszíni rendszerek között. Ez az oktatóanyag bemutatja, hogyan hozhat létre bejövő e-maileket és mellékleteket kezelő [logikai alkalmazásokat](../logic-apps/logic-apps-overview.md). Ez a logikai alkalmazás elemzi az e-mail-tartalmakat, menti a tartalmat az Azure Storage-ba, és értesítéseket küld a tartalom áttekintéséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az [Azure storage](../storage/common/storage-introduction.md) és a Storage Explorer konfigurálása a mentett e-mailek és mellékletek ellenőrzésére.
> * [Azure-függvény](../azure-functions/functions-overview.md) létrehozása az e-mailekben található HTML-formázás eltávolítására. Ebben az oktatóanyagban megtalálja az ehhez a függvényhez használható kódot.
> * Üres logikai alkalmazás létrehozása.
> * Eseményindító hozzáadása az e-mailek mellékleteinek figyelésére.
> * Feltétel hozzáadása annak ellenőrzéséhez, hogy az e-maileknek van-e melléklete.
> * Művelet hozzáadása az Azure-függvény meghívásához, ha egy e-mail melléklettel rendelkezik.
> * Művelet hozzáadása tárolóblobok létrehozására az e-mailek és a mellékletek számára.
> * Művelet hozzáadása e-mail-értesítések küldésére.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű befejezett logikai alkalmazás](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Logic Apps által támogatott e-mail-szolgáltató (például Office 365 Outlook, Outlook.com vagy Gmail) által üzemeltetett e-mail-fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/).

  Ez a logikai alkalmazás munkahelyi vagy iskolai fiókot használ. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Az [ingyenes Microsoft Azure Storage Explorer](https://storageexplorer.com/) letöltése és telepítése. Az eszköz segítségével ellenőrizheti, hogy a Storage-tároló megfelelően van-e beállítva.

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

## <a name="set-up-storage-to-save-attachments"></a>Tároló beállítása a mellékletek mentésére

A bejövő e-mailek és mellékletek blobként menthetőek egy [Azure Storage-tárolóba](../storage/common/storage-introduction.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Storage-tároló létrehozása előtt [hozzon létre egy Storage-fiókot](../storage/common/storage-account-create.md) ezekkel a beállításokkal a Azure Portal **alapok** lapján:

   | Beállítás | Érték | Leírás |
   |---------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve |  
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport*> | A kapcsolódó erőforrások rendezéséhez és felügyeletéhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) neve. Ez a példa a "LA-tutorial-RG" protokollt használja. <p>**Megjegyzés:** Az erőforráscsoportok adott régiókon belül léteznek. Bár az ebben az oktatóanyagban bemutatott elemek nem feltétlenül érhetőek el minden régióban, igyekezzen ugyanazt a régiót használni, amikor csak lehetséges. |
   | **Tárfiók neve** | <*Azure-Storage-fiók-név*> | A Storage-fiók neve, amelynek 3-24 karakterből kell állnia, és csak kisbetűket és számokat tartalmazhat. Ez a példa a "attachmentstorageacct" kifejezést használja. |
   | **Hely** | <*Azure-régió*> | Az a régió, ahol a Storage-fiókkal kapcsolatos információkat tárolhatja. Ez a példa a "West US"-t használja. |
   | **Teljesítmény** | Standard | Ez a beállítás adja meg a támogatott adattípusokat és az adathordozót az adatok tárolásához. Lásd: [A tárfiókok típusai](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Fiók altípusa** | Általános célú | A [tárfiók típusa](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikáció** | Helyileg redundáns tárolás (LRS) | Ez a beállítás határozza meg az adatok másolásának, tárolásának, felügyeletének és szinkronizálásának módját. Lásd [a helyileg redundáns tárolást (LRS): alacsony költséghatékonyságú adatredundancia az Azure Storage szolgáltatáshoz](../storage/common/storage-redundancy.md). |
   | **Hozzáférési szint (alapértelmezett)** | Tartsa meg az aktuális beállítást. |
   ||||

   A **speciális** lapon válassza ki ezt a beállítást:

   | Beállítás | Érték | Leírás |
   |---------|-------|-------------|
   | **Biztonságos átvitelre van szükség** | Disabled (Letiltva) | Ez a beállítás adja meg a kapcsolatokról érkező kérésekre vonatkozó biztonsági követelményeket. Lásd: [Biztonságos átvitel megkövetelése](../storage/common/storage-require-secure-transfer.md). |
   ||||

   A tárfiók létrehozásához az [Azure PowerShellt](../storage/common/storage-account-create.md?tabs=powershell) vagy az [Azure CLI-t](../storage/common/storage-account-create.md?tabs=azure-cli) is használhatja.

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

1. Miután az Azure üzembe helyezte a Storage-fiókját, keresse meg a Storage-fiókját, és szerezze be a Storage-fiók hozzáférési kulcsát:

   1. A tárfiók menüjében a **Beállítások** alatt válassza a **Hozzáférési kulcsok** lehetőséget.

   1. Másolja ki a Storage-fiók nevét és a **key1**, és mentse el ezeket az értékeket valahol biztonságos helyen.

      ![A tárfiók nevének és kulcsának másolása és mentése](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   A tárfiók hozzáférési kulcsát az [Azure PowerShell](/powershell/module/az.storage/get-azstorageaccountkey) vagy az [Azure CLI](/cli/azure/storage/account/keys.md#az-storage-account-keys-list) használatával is lekérheti.

1. Hozzon létre egy Blob Storage-tárolót az e-mail-mellékletek számára.

   1. A tárfiók menüjében válassza az **Áttekintés** lehetőséget. Az Áttekintés panelen válassza a **tárolók** lehetőséget.

      ![Blob Storage-tároló hozzáadása](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   1. Ha megnyílik a **Tárolók** lap, az eszköztáron válassza a **Tároló** elemet.

   1. Az **új tároló** területen adja meg `attachments` a tároló nevét. A **nyilvános hozzáférés szintje** területen válassza a **tároló (névtelen olvasási hozzáférés tárolók és Blobok számára)** elemet  >  .

      Amikor elkészült, a Storage-tárolót a tárfiókjában találja itt, az Azure Portalon:

      ![Befejezett Storage-tároló](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Tároló létrehozásához használhatja a [Azure PowerShell](/powershell/module/az.storage/new-azstoragecontainer) vagy az [Azure CLI](/cli/azure/storage/container#az-storage-container-create)-t is.

Ezután csatlakoztassa a Storage Explorert a tárfiókhoz.

## <a name="set-up-storage-explorer"></a>A Storage Explorer beállítása

Most csatlakoztassa a Storage Explorert a tárfiókjához, így ellenőrizheti, hogy a logikai alkalmazás megfelelően menti-e blobokként a mellékleteket a Storage-tárolóba.

1. Microsoft Azure Storage Explorer elindítása.

   A Storage Explorer kérni fogja a kapcsolatot a tárfiókhoz.

1. A **Kapcsolódás az Azure Storage-hoz** panelen válassza **a Storage-fiók nevének és kulcsának használata** lehetőséget  >  .

   ![Storage Explorer – Csatlakozás tárfiókhoz](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Ha nem jelenik meg üzenet, a Storage Explorer eszköztáron válassza a **fiók hozzáadása** lehetőséget.

1. A **megjelenítendő név** mezőben adjon meg egy rövid nevet a kapcsolatok számára. A **Fióknév** alatt adja meg a tárfiók nevét. A **fiók kulcsa** területen adja meg a korábban mentett hozzáférési kulcsot, majd kattintson a **Tovább gombra**.

1. Erősítse meg a kapcsolati adatokat, majd válassza a **Csatlakoztatás** lehetőséget.

   Storage Explorer létrehozza a kapcsolatokat, és megjeleníti a Storage-fiókot az Explorer ablakban a **helyi & csatolt**  >  **Storage-fiókok** területen.

1. A blob Storage-tároló megkereséséhez a **Storage-fiókok** területen bontsa ki a Storage-fiókot, amely itt **attachmentstorageacct** , és bontsa ki a **blob-tárolókat** , ahol a **mellékletek** tároló található, például:

   ![Storage Explorer – Storage-tároló megkeresése](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Ezután hozzon létre egy [Azure-függvényt](../azure-functions/functions-overview.md) a bejövő e-mailekben lévő a HTML-formázás eltávolításához.

## <a name="create-function-to-clean-html"></a>Függvény létrehozása a HTML-formázás tisztításához

Most az ezekben a lépésekben megadott kódrészlet használatával hozzon létre egy Azure-függvényt az egyes bejövő e-mailekben található HTML-formázás eltávolítására. Így az e-mailek tartalma tisztább és könnyebben feldolgozható lesz. Ez a függvény azután a logikai alkalmazásból hívható majd meg.

1. A függvény létrehozása előtt [hozzon létre egy függvényalkalmazást](../azure-functions/functions-create-function-app-portal.md) az alábbi beállításokkal:

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Alkalmazás neve** | <*függvény – alkalmazás-név*> | A Function alkalmazás neve, amelynek az Azure-ban globálisan egyedinek kell lennie. Ez a példa már használja a "CleanTextFunctionApp" nevet, ezért adjon meg egy másik nevet, például: "MyCleanTextFunctionApp-<*Your-name*>" |
   | **Előfizetés** | <*saját-Azure-előfizetés-név*> | A korábban is használt Azure-előfizetés |
   | **Erőforráscsoport** | LA-Tutorial-RG | A korábban is használt Azure-erőforráscsoport |
   | **OS** | <*az operációs rendszer*> | Válassza ki azt az operációs rendszert, amely támogatja kedvenc funkciójának programozási nyelvét. Ebben a példában válassza a **Windows** lehetőséget. |
   | **Szolgáltatási csomag** | Használatalapú csomag | Ez a beállítás határozza meg az erőforrások, például a számítási teljesítmény lefoglalásának és méretezésének módját a függvényalkalmazás futtatásához. Lásd a [szolgáltatási csomagok összehasonlítását](../azure-functions/functions-scale.md). |
   | **Hely** | USA nyugati régiója | A korábban is használt régió |
   | **Futtatókörnyezeti verem** | Elsődleges nyelv | Válasszon olyan futtatókörnyezetet, amely támogatja kedvenc funkciójának programozási nyelvét. Válassza a **.net** lehetőséget a C# és az F # függvények számára. |
   | **Storage** | cleantextfunctionstorageacct | Hozzon létre egy tárfiókot a függvényalkalmazás számára. Csak kisbetűket és számokat használjon. <p>**Megjegyzés:** Ez a Storage-fiók tartalmazza a Function-alkalmazásokat, és eltér a korábban létrehozott Storage-fióktól az e-mail-mellékletek számára. |
   | **Application Insights** | Letiltás | Az alkalmazás figyelésének bekapcsolása a [Application Insightssal](../azure-monitor/app/app-insights-overview.md), de ebben az oktatóanyagban válassza az alkalmazás **letiltása** lehetőséget  >  . |
   ||||

   Ha a Function alkalmazás nem nyílik meg automatikusan az üzembe helyezés után, a [Azure Portal](https://portal.azure.com) keresőmezőbe keresse meg és válassza a **függvényalkalmazás** lehetőséget. A **függvényalkalmazás** alatt válassza ki a Function alkalmazást.

   ![Függvényalkalmazás kiválasztása](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Ellenkező esetben az Azure automatikusan megnyitja a függvényalkalmazást, ahogy alább látható:

   ![Létrehozott függvényalkalmazás](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Function-alkalmazás létrehozásához használhatja az [Azure CLI](../azure-functions/create-first-function-cli-csharp.md)-t, illetve a [PowerShell-és Resource Manager-sablonokat](../azure-resource-manager/templates/deploy-powershell.md)is.

1. A **Function apps** listában bontsa ki a Function alkalmazást, ha még nincs kibontva. A Function alkalmazás alatt válassza a **függvények** lehetőséget. A függvények eszköztárán válassza az **Új függvény** lehetőséget.

   ![Új függvény létrehozása](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

1. A **válasszon egy sablont az alábbi listából, vagy lépjen** a gyors üzembe helyezési pontra, és válassza a **http-trigger** sablont.

   ![HTTP-trigger sablon kiválasztása](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Az Azure egy olyan függvényt hoz létre, amely egy nyelvspecifikus sablont használ egy HTTP-triggert használó függvényhez.

1. Az **Új függvény** panelen, a **Név** területen adja meg ezt: `RemoveHTMLFunction`. Tartsa meg az **engedélyezési szint** beállítását **, majd** válassza a **Létrehozás** lehetőséget.

   ![A függvény neve](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

1. Miután megnyílik a szerkesztő, a sablonban lévő kód helyére illessze be ezt a mintakódot, amely eltávolítja a HTML-formázást és visszaadja az eredményeket a hívónak:

   ```csharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log) {

      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

1. Amikor elkészült, válassza a **Mentés** lehetőséget. A függvény teszteléséhez a szerkesztő jobb szélén, a nyíl ( **<** ) ikon alatt válassza a **teszt** elemet.

   ![A „Tesztelés” panel bezárása](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

1. A **teszt** ablaktábla **kérelem törzse** területén adja meg ezt a sort, majd válassza a **Futtatás** lehetőséget.

   `{"name": "<p><p>Testing my function</br></p></p>"}`

   ![A függvény tesztelése](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   A **Kimenet** ablakban látható a függvény eredménye:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Miután ellenőrizte, hogy működik-e a függvény, készítse el a logikai alkalmazást. Bár ez az oktatóanyag bemutatja, hogyan hozhat létre olyan függvényt, amely eltávolítja az e-mailek HTML-formázását, a Logic Apps biztosít egy **HTML–szöveg** összekötőt is.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure legfelső szintű keresési mezőjébe írja be a kifejezést `logic apps` , majd válassza a **Logic apps** lehetőséget.

   ![A "Logic Apps" megkeresése és kiválasztása](./media/tutorial-process-email-attachments-workflow/find-select-logic-apps.png)

1. A **Logic apps** ablaktáblán válassza a **Hozzáadás** lehetőséget.

   ![Új logikai alkalmazás hozzáadása](./media/tutorial-process-email-attachments-workflow/add-new-logic-app.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a logikai alkalmazás részleteit az itt látható módon. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

   ![Logikai alkalmazás adatainak megadása](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Előfizetés** | <*saját-Azure-előfizetés-név*> | A korábban is használt Azure-előfizetés |
   | **Erőforráscsoport** | LA-Tutorial-RG | A korábban is használt Azure-erőforráscsoport |
   | **Logikai alkalmazás neve** | LA-ProcessAttachment | A logikai alkalmazás neve |
   | **Hely kiválasztása** | USA nyugati régiója | A korábban is használt régió |
   | **Naplóelemzés** | Kikapcsolva | Ebben az oktatóanyagban válassza a **kikapcsolás** beállítást. |
   ||||

1. Miután az Azure üzembe helyezte az alkalmazást, az Azure eszköztáron válassza az értesítések ikont, és válassza az **Ugrás az erőforráshoz** lehetőséget.

   ![Az Azure-értesítések listából válassza az "Ugrás erőforráshoz" lehetőséget.](./media/tutorial-process-email-attachments-workflow/go-to-new-logic-app-resource.png)

1. Miután megnyitotta a Logic Apps designert, és megjelenít egy bemutató videót és sablonokat a Common Logic app-mintákhoz. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Üres logikai alkalmazás sablonjának kiválasztása](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Ezután adjon hozzá egy [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely a melléklettel rendelkező beérkező e-maileket figyeli. Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>A bejövő e-mailek monitorozása

1. A keresőmezőbe írja be a szűrőt a Keresés mezőbe `when new email arrives` . Válassza ki ezt az eseményindítót a saját levelezési szolgáltatójához: **When a new email arrives - <*saját-levelezési-szolgáltató*>**

   Például:

   ![Válassza ezt az eseményindítót az e-mail-szolgáltatónál: „Új e-mail érkezésekor”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.

   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a rendszer kéri a hitelesítő adatokat, jelentkezzen be az e-mail-fiókjába, hogy a Logic Apps kapcsolatot létesíthessen vele.

1. Most adja meg az eseményindító által az új e-mailek szűréséhez alkalmazott feltételeket.

   1. Itt adhatja meg az e-mailek ellenőrzéséhez alább ismertetett beállításokat.

      ![A mappa, az időtartam és a gyakoriság megadása az e-mailek ellenőrzéséhez](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Mappa** | Beérkezett üzenetek | Az ellenőrizni kívánt e-mail-mappa |
      | **Melléklettel rendelkezik** | Yes | Csak a melléklettel rendelkező e-mailek beolvasása. <p>**Megjegyzés:** Az eseményindító nem törli az e-maileket a fiókból, csak ellenőrzi az új üzeneteket, és feldolgozza azokat, amelyek megfelelnek a tárgyszűrőnek. |
      | **Mellékletek is** | Yes | A mellékletek egyszerű ellenőrzése helyett azok lekérése bemenetként a munkafolyamathoz. |
      | **Intervallum** | 1 | Az ellenőrzések között kivárt intervallumok száma |
      | **Gyakoriság** | Minute | Az ellenőrzések közötti intervallumok időegysége |
      ||||

   1. Az **új paraméter hozzáadása** listából válassza a **tulajdonos szűrő** elemet.

   1. Miután a **tulajdonos szűrő** mező megjelenik a műveletben, a tárgyat az itt felsorolt módon adhatja meg:

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Tárgyszűrő** | `Business Analyst 2 #423501` | Az e-mail tárgyában keresendő szöveg |
      ||||

1. Ha egyelőre el szeretné rejteni az eseményindító részleteit, kattintson az eseményindító címsorába.

   ![Alakzat összecsukása a részletek elrejtéséhez](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

   A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy ellenőrzi az e-maileket. Ezután adja hozzá egy feltételt, amely meghatározza a munkafolyamat folytatására vonatkozó kritériumokat.

## <a name="check-for-attachments"></a>Mellékletek ellenőrzése

Most adjon meg olyan feltételt, amely csak a csatolmánnyal rendelkező e-maileket választja ki.

1. Az trigger alatt válassza az **új lépés** lehetőséget.

   !["Új lépés"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

1. A **válasszon műveletet** területen a keresőmezőbe írja be a kifejezést `condition` . Válassza ki ezt a műveletet: **feltétel**

   ![Válassza a "feltétel" lehetőséget.](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Adjon egy leíróbb nevet a feltételnek. A feltétel címsorán kattintson a három pontra (**..**.) > **Átnevezés** gombra.

      ![Feltétel átnevezése](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Nevezze át a feltételt a következő leírással: `If email has attachments and key subject phrase`

1. Hozzon létre egy olyan feltételt, amely ellenőrzi, hogy mely e-mailnek van csatolmánya.

   1. Az **Ls** feltétel alatti első sorban kattintson a bal oldali mezőbe. A megjelenő dinamikus tartalomlistában válassza a **Has attachment** (Rendelkezik melléklettel) tulajdonságot.

      ![A feltételhez tartozó "and" tulajdonságot és a "mellékletek" tulajdonság kijelölését bemutató képernyőkép.](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   1. A középső mezőben tartsa meg az **is equal to** (egyenlő) operátort.

   1. A jobb oldali mezőben adja meg az **igaz** értéket, hogy a rendszer összehasonlítsa a **melléklet** tulajdonság értékével az triggerből.

      ![Feltétel létrehozása](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Ha a két érték egyezik, az e-mail legalább egy melléklettel rendelkezik, a feltétel teljesül, és a munkafolyamat folytatódik.

   A kódszerkesztő ablakban megtekinthető mögöttes logikaialkalmazás-definícióban ez a feltétel az alábbi példához hasonlóan néz ki:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="test-your-condition"></a>A feltétel tesztelése

Most tesztelje le, hogy a feltétel megfelelően működik-e:

1. Ha a logikai alkalmazás már nem fut, válassza a **Futtatás** lehetőséget a tervező eszköztáron.

   Ezzel a lépéssel manuálisan indíthatja a logikai alkalmazást, és nem kell megvárnia, amíg letelik a megadott időtartam. Addig azonban semmi nem történik, amíg a teszt e-mail meg nem érkezik a postaládájába.

1. Küldjön magának egy e-mailt, amely megfelel az alábbi feltételeknek:

   * Az e-mail-cím tárgya az trigger **tulajdonos szűrőben** megadott szövege: `Business Analyst 2 #423501`

   * Az e-mail rendelkezik egy melléklettel. Most csak hozzon létre egy üres szövegfájlt, és csatolja a fájlt az e-mailhez.

   Amint az e-mailek megérkezik, a logikai alkalmazás ellenőrzi a mellékleteket és a megadott szöveget a tárgyban. Ha a feltétel teljesül, az eseményindító aktiválódik, a Logic Apps-motorral létrehozat egy logikai alkalmazáspéldányt, és elindíttatja a munkafolyamatot.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget, és győződjön meg arról, hogy a trigger és a logikai alkalmazás sikeresen futott.

   ![Az eseményindító és a futtatási előzmények ellenőrzése](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Ha az eseményindító nem aktiválódott, vagy a logikai alkalmazás a sikeres aktiválás ellenére nem futott le, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Ezt követően adja meg a **Ha igaz** ágban végrehajtandó műveleteket. Az e-mailek és esetleges mellékleteik mentéséhez törölni kell az e-mail törzsének HTML-formázását, majd blobokat létrehozni a Storage-tárolóban az e-mail és a mellékletek számára.

> [!NOTE]
> A logikai alkalmazásnak semmit nem kell tennie a **Ha hamis** ágon, azaz akkor, ha egy e-mail nem rendelkezik melléklettel.
> Soron kívüli feladatként az oktatóanyag befejezése után a **Ha hamis** ágban is hozzáadhat valamilyen megfelelő műveletet, amelyet végre szeretne hajtatni.

## <a name="call-removehtmlfunction"></a>A RemoveHTMLFunction meghívása

Ez a lépés hozzáadja az előzőleg létrehozott Azure-függvényt a logikai alkalmazáshoz, és az e-mail eseményindítójától érkező e-mail szövegtörzsét átadja a függvénynek.

1. A logikai alkalmazás menüjében válassza a **Logic App tervező** lehetőséget. A **Ha igaz** ág területen válassza a **művelet hozzáadása** lehetőséget.

   ![Művelet hozzáadása az „If true”-n (Ha igaz) belül](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

1. A keresőmezőbe keresse meg az "Azure functions" kifejezést, és válassza ki ezt a műveletet: **válasszon ki egy Azure-függvényt – Azure functions**

   ![Művelet kiválasztása a „Válasszon Azure-függvényt” lehetőséghez](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

1. Válassza ki a korábban létrehozott Function alkalmazást, amely `CleanTextFunctionApp` ebben a példában látható:

   ![Az Azure-függvényalkalmazás kiválasztása](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

1. Most válassza ki a függvényt: **RemoveHTMLFunction**

   ![Az Azure-függvény kiválasztása](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

1. Nevezze át a függvényalakzatot a következő leírással: `Call RemoveHTMLFunction to clean email body`

1. Most adja meg a függvény által feldolgozandó bemenetet.

   1. A **Kérelem törzse** mezőben adja meg a következő szöveget záró szóközzel:

      `{ "emailBody":`

      Miközben a következő lépésekben ezzel a bemenettel dolgozik, érvénytelen JSON-ra figyelmeztető hibaüzenet jelenik egészen addig, amíg a bemenet helyes JSON-formázást nem kap. A függvény előző tesztelésekor a megadott bemenet a JavaScript Object Notation (JSON) formátumot használta. Ezért a kérelem törzsének is ezt a formátumot kell követnie.

      Amíg a kurzor a **Kérelem törzse** mezőben van, megjelenik egy dinamikus tartalomlista is, amelyből kiválaszthatja a korábban már megadott tulajdonságértékeket is.

   1. A dinamikus listából a **When a new mail arrives** (Új e-mail érkezésekor) alatt válassza a **Body** (Törzs) tulajdonságot. A tulajdonság után ne felejtse el beírni a záró kapcsos zárójelet: `}`

      ![A függvénybe beadandó kérelemtörzs megadása](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Ha elkészült, a függvény bemenete a következő példához hasonlít:

   ![Elkészült a függvénynek átadandó kérelemtörzs](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy műveletet, amely egy blobot hoz létre a tárolóban az e-mail törzsének mentéséhez.

## <a name="create-blob-for-email-body"></a>Blob létrehozása az e-mail törzséhez

1. A **Ha igaz** blokkban és az Azure-függvényben válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a kifejezést, `create blob` majd válassza a következő műveletet: **blob létrehozása**

   ![Művelet hozzáadása blob létrehozására az e-mail törzse számára](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

1. Létesítsen kapcsolatot a tárfiókkal az itt bemutatott beállításokkal. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   ![Tárfiókkapcsolat létrehozása](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Kapcsolat neve** | AttachmentStorageConnection | A kapcsolat leíró neve |
   | **Tárfiók** | attachmentstorageacct | A mellékletek mentéséhez korábban létrehozott tárfiók neve |
   ||||

1. Nevezze át a **Blob létrehozása** műveletet a következő leírással: `Create blob for email body`

1. A **Blob létrehozása** műveletnél adja meg ezeket az adatokat, és válassza ki ezeket a mezőket a blob létrehozásához az itt ismertetett módon:

   ![Blob adatainak megadása az e-mail-törzs számára](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Mappa elérési útja** | /attachments | A korábban létrehozott tároló elérési útja és neve. Ehhez a példához kattintson a mappa ikonra, majd válassza az „/attachments” tárolót. |
   | **Blob neve** | **Feladó** mező | Ehhez a példához a blob neveként használja a feladó nevét. Kattintson a mezőn belülre, hogy megjelenjen a dinamikus tartalomlista, majd a **When a new mail arrives** (Új e-mail érkezésekor) alatt válassza a **Feladó** mezőt. |
   | **Blob tartalma** | **Tartalom** mező | Ebben a példában blobtartalomként használjon HTML-mentes e-mail-törzset. Kattintson a mezőn belülre, hogy megjelenhessen a dinamikus tartalomlista, majd a **Call RemoveHTMLFunction to clean email body** (a RemoveHTMLFunction meghívása az e-mail-törzs megtisztításához) alatt válassza a **Body** elemet. |
   ||||

   Ha elkészült, a művelet a következő példához hasonlít:

   ![A "blob létrehozása" művelet egy példáját bemutató képernyőkép.](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

1. Mentse a logikai alkalmazást.

### <a name="check-attachment-handling"></a>A mellékletek kezelésének ellenőrzése

A következő lépés annak tesztelése, hogy a logikai alkalmazás a megadott módon kezeli-e az e-maileket:

1. Ha a logikai alkalmazás már nem fut, válassza a **Futtatás** lehetőséget a tervező eszköztáron.

1. Küldjön magának egy e-mailt, amely megfelel az alábbi feltételeknek:

   * Az e-mail-cím tárgya az trigger **tulajdonos szűrőben** megadott szövege: `Business Analyst 2 #423501`

   * Az e-mail legalább egy melléklettel rendelkezik. Most hozzon létre egy üres szövegfájlt, és csatolja a fájlt az e-mailhez.

   * Az e-mail-címe tartalmaz valamilyen tesztelési tartalmat a törzsben, például: `Testing my logic app`

   Ha az eseményindító nem aktiválódott, vagy a logikai alkalmazás a sikeres aktiválás ellenére nem futott le, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

1. Ellenőrizze, hogy a logikai alkalmazás mentette-e az e-mailt a megfelelő tárolóba.

   1. Storage Explorer bontsa ki a **helyi & csatolt**  >  **Storage**  >  **-fiókok attachmentstorageacct (kulcs)**  >  **blob-tárolók**  >  **mellékleteit**.

   1. Ellenőrizze az **attachments** tárolóban az e-mailt.

      Ekkor még csak az e-mail jelenik meg a tárolóban, mivel a logikai alkalmazás egyelőre nem dolgozza fel a mellékleteket.

      ![A mentett e-mailek ellenőrzése a Storage Explorerben](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   1. Amikor végzett, törölje az e-mailt a Storage Explorerben.

1. Ha szeretné, az egyelőre tétlen **Ha hamis** ág teszteléséhez küldhet egy olyan e-mailt, amely nem felel meg a feltételeknek.

Ezután adjon hozzá egy iterációt az összes e-mail-melléklet feldolgozására.

## <a name="process-attachments"></a>Mellékletek feldolgozása

Az e-mail összes melléklet feldolgozásához a logikai alkalmazás munkafolyamatához adjon hozzá egy **For each** iterációt.

1. A **blob létrehozása az e-mail törzse** alakzathoz területen válassza a **művelet hozzáadása** lehetőséget.

   ![„for each” iteráció hozzáadása](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. A **válasszon műveletet** területen a keresőmezőbe írja be szűrőként a kifejezést, `for each` majd válassza a **következő** műveletet:

   ![Válassza az "összes" lehetőséget.](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Nevezze át az iterációt a következő leírással: `For each email attachment`

1. Most adja meg az iteráció által feldolgozandó adatokat. Kattintson a **Kimenet választása az előző lépésekből** mezőn belülre, hogy megjelenhessen a dinamikus tartalomlista, majd válassza a **Mellékletek** elemet.

   ![A „Mellékletek” elem kiválasztása](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   A **Mellékletek** mező egy tömböt továbbít, amely az e-mailben szereplő összes mellékletet tartalmazza. A **For each** iteráció a tömbben beadott mindegyik elemre vonatkozóan megismétli a műveleteket.

1. Mentse a logikai alkalmazást.

Ezután adja hozzá a műveletet, amely az egyes mellékleteket blobként menti az **attachments** Storage-tárolóba.

## <a name="create-blob-for-each-attachment"></a>Blob létrehozása minden melléklethez

1. Az **egyes e-mail-mellékletek esetében** válassza a **művelet hozzáadása** lehetőséget, így megadhatja az egyes talált mellékleteken végrehajtandó feladatot.

   ![Művelet hozzáadása az iterációhoz](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

1. A keresőmezőbe írja be szűrőként a kifejezést, `create blob` majd válassza a következő műveletet: **blob létrehozása**

   ![Művelet hozzáadása blob létrehozásához](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

1. Nevezze át a **Blob létrehozása 2** műveletet a következő leírással: `Create blob for each email attachment`

1. A **Blob létrehozása az e-mail-mellékletek számára** műveletnél adja meg ezeket az adatokat, és válassza ki a paramétereket az egyes blobok létrehozásához az itt ismertetett módon:

   ![Blob adatainak megadása](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Mappa elérési útja** | /attachments | A korábban létrehozott tároló elérési útja és neve. Ehhez a példához kattintson a mappa ikonra, majd válassza az „/attachments” tárolót. |
   | **Blob neve** | **Név** mező | Ehhez a példához a blob neveként használja a melléklet nevét. Kattintson a mezőn belülre, hogy megjelenjen a dinamikus tartalomlista, majd az **Új e-mail érkezésekor** alatt válassza a **Név** mezőt. |
   | **Blob tartalma** | **Tartalom** mező | Ebben a példában blobtartalomként használja a **Tartalom** mezőt. Kattintson a mezőn belülre, hogy megjelenjen a dinamikus tartalomlista, majd az **Új e-mail érkezésekor** alatt válassza a **Tartalom** mezőt. |
   ||||

   Ha elkészült, a művelet a következő példához hasonlít:

   ![Elkészült a „Blob létrehozása” művelet](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

1. Mentse a logikai alkalmazást.

### <a name="check-attachment-handling"></a>A mellékletek kezelésének ellenőrzése

A következő lépés annak tesztelése, hogy a logikai alkalmazás a megadott módon kezeli-e a mellékleteket:

1. Ha a logikai alkalmazás már nem fut, válassza a **Futtatás** lehetőséget a tervező eszköztáron.

1. Küldjön magának egy e-mailt, amely megfelel az alábbi feltételeknek:

   * Az e-mail tárgya az trigger **tulajdonos szűrő** tulajdonságában megadott szöveg: `Business Analyst 2 #423501`

   * Az e-mail legalább két melléklettel rendelkezik. Most csak hozzon létre két üres szövegfájlt, és csatolja a fájlokat az e-mailhez.

   Ha az eseményindító nem aktiválódott, vagy a logikai alkalmazás a sikeres aktiválás ellenére nem futott le, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

1. Ellenőrizze, hogy a logikai alkalmazás elmentette-e az e-mailt és a mellékleteket a megfelelő tárolóba.

   1. Storage Explorer bontsa ki a **helyi & csatolt**  >  **Storage**  >  **-fiókok attachmentstorageacct (kulcs)**  >  **blob-tárolók**  >  **mellékleteit**.

   1. Ellenőrizze az **attachments** tárolóban az e-mailt és a mellékleteket egyaránt.

      ![A mentett e-mailek és mellékletek ellenőrzése](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   1. Amikor végzett, törölje az e-mailt és a mellékleteket a Storage Explorerben.

Ezután adjon meg egy műveletet, hogy a logikai alkalmazás egy e-mail-üzenetet küldjön a mellékletek áttekintésére vonatkozóan.

## <a name="send-email-notifications"></a>E-mail-értesítések küldése

1. A **Ha igaz** ág alatt, az **egyes e-mail-mellékletek** ciklusa alatt válassza a **művelet hozzáadása** lehetőséget.

   ![Művelet hozzáadása a „for each” iteráció alatt](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

1. A keresőmezőbe írja be szűrőként a kifejezést, `send email` majd válassza az e-mail-szolgáltató "e-mail küldése" műveletét.

   Ha a műveletek listáját adott szolgáltatásra szeretné szűrni, először kiválaszthatja az összekötőt.

   ![Az „e-mail küldése” művelet kiválasztása az e-mail-szolgáltatóhoz](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az Office 365 Outlookot.

   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

1. Ha a rendszer kéri a hitelesítő adatokat, jelentkezzen be az e-mail-fiókjába, hogy a Logic Apps kapcsolatot létesíthessen vele.

1. Nevezze át az **E-mail küldése** műveletet a következő leírással: `Send email for review`

1. Adja meg a művelet adatait, és válassza ki az e-mailben szerepeltetni kívánt mezőket az itt ismertetett módon. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

   ![E-mail-értesítés küldése](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Ha nem talál egy várt mezőt a dinamikus tartalmak listájában, válassza a **továbbiak** tovább lehetőséget, **Amikor új e-mail érkezik**.

   | Beállítás | Érték | Jegyzetek |
   | ------- | ----- | ----- |
   | **Ide:** | <*címzett-e-mail-cím*> | Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy**  | ```ASAP - Review applicant for position:```**Tárgy** | Az e-mail tárgya, amelyet használni kíván. Kattintson a mezőn belülre, írja be a példa szöveget, majd a dinamikus tartalomlistából az **Új e-mail érkezésekor** mellett válassza a **Tárgy** mezőt. |
   | **Törzs** | ```Please review new applicant:``` <p>```Applicant name:```**Innen** : <p>```Application file location:``` **Elérési út** <p>```Application email content:``` **Törzs** | Az e-mail szövegtörzsének tartalma. Kattintson a mezőn belülre, írja be a példa szöveget, majd a dinamikus tartalomlistából válassza az alábbi mezőket: <p>- A **Feladó** mezőt az **Új e-mail érkezésekor** alatt </br>- Az **Elérési út** mezőt a **Blob létrehozása az e-mail törzséhez** alatt </br>- A **Törzs** mezőt a **Call RemoveHTMLFunction to clean email body** (A RemoveHTMLFunction meghívása az e-mail-törzs megtisztításához) alatt |
   ||||

   > [!NOTE]
   > Ha olyan mezőt választ ki, amely egy tömböt tartalmaz, például a **Tartalom** elemet, amely egy mellékleteket tartalmazó tömb, a tervező automatikusan hozzáad egy „For each” iterációt a mezőre hivatkozó művelet köré.
   > Így a logikai alkalmazás a tömb mindegyik elemén végrehajthatja az adott műveletet.
   > A hurok eltávolításához távolítsa el a tömb mezőjét, helyezze át a hivatkozó műveletet a hurokon kívülre, válassza a hurok címsorán lévő három pontot (**..**.), majd válassza a **Törlés** lehetőséget.

1. Mentse a logikai alkalmazást.

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Befejezett logikai alkalmazás](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailt, amely megfelel az alábbi feltételeknek:

   * Az e-mail tárgya az trigger **tulajdonos szűrő** tulajdonságában megadott szöveg: `Business Analyst 2 #423501`

   * Az e-mail egy vagy több melléklettel rendelkezik. Megint felhasználhatja az üres szövegfájlokat az előző tesztből. Ha valószerűbb forgatókönyvet szeretne, csatoljon egy önéletrajzfájlt.

   * Az e-mail törzse tartalmazza a következő szöveget (másolható és beilleszthető):

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

1. Futtassa a logikai alkalmazást. Ha sikeresen fut, a logikai alkalmazás egy, a következőhöz hasonló e-mailt küld:

   ![Logikai alkalmazás által küldött e-mail-értesítés](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely feladatokat automatizál különböző Azure-szolgáltatásokban, és egyéni kódokat hív meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség erre a mintára, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. A legfelső szintű Azure Search mezőbe írja be a kifejezést `resources groups` , majd válassza az **erőforráscsoportok** lehetőséget.

   ![Erőforráscsoportok megkeresése és kiválasztása](./media/tutorial-process-email-attachments-workflow/find-azure-resource-groups.png)

1. Az **erőforráscsoportok** listából válassza ki az oktatóanyaghoz tartozó erőforráscsoportot. 

   ![Az oktatóanyaghoz tartozó erőforráscsoport megkeresése](./media/tutorial-process-email-attachments-workflow/find-select-tutorial-resource-group.png)

1. Az **Áttekintés** panelen válassza az **erőforráscsoport törlése** elemet.

   ![Logikai alkalmazás erőforráscsoportjának törlése](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítő ablaktábla, adja meg az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban létrehoztunk egy logikai alkalmazást e-mail mellékletek, feldolgozására és tárolására Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával. Most megismerkedhet a logikai alkalmazások létrehozásához használható egyéb összekötőkkel.

> [!div class="nextstepaction"]
> [További tudnivalók a Logic Apps összekötőiről](../connectors/apis-list.md)