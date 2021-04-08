---
title: Függvények hozzáadása és hívása Azure Logic Apps
description: Egyéni kód meghívása és futtatása az Azure-ban az automatizált feladatokból és munkafolyamatokból Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791879"
---
# <a name="call-functions-from-azure-logic-apps"></a>Függvények hívása Azure Logic Apps

Ha olyan kódot szeretne futtatni, amely egy adott feladatot hajt végre a logikai alkalmazásokban, [Azure functions](../azure-functions/functions-overview.md)használatával létrehozhatja saját függvényét. Ez a szolgáltatás segít a Node.js-, C#- és F#-függvények létrehozásában, így nem kell teljes alkalmazást vagy infrastruktúrát létrehoznia a kód futtatásához. [A Logic apps-t a functions használatával is meghívhatja](#call-logic-app). A Azure Functions kiszolgáló nélküli számítástechnikai szolgáltatásokat biztosít a felhőben, és hasznos lehet olyan feladatok elvégzéséhez, mint például az alábbi példák:

* A logikai alkalmazás viselkedését kiterjesztheti Node.js vagy C# függvényekkel.
* Számítások végrehajtása a logikai alkalmazás munkafolyamatában.
* Alkalmazzon speciális formázási vagy számítási mezőket a logikai alkalmazásokban.

A kódrészletek Azure Functions használata nélküli futtatásához tekintse meg a [beágyazott kód hozzáadását és futtatását](../logic-apps/logic-apps-add-run-inline-code.md)ismertető témakört.

> [!NOTE]
> A Logic Apps és Azure Functions közötti integráció jelenleg nem működik az engedélyezett tárolóhelyekkel.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Function-alkalmazás, amely egy Azure Functionsban létrehozott függvény, valamint a létrehozott függvény tárolója. Ha nem rendelkezik Function alkalmazással, [először hozza létre a Function alkalmazást](../azure-functions/functions-get-started.md). Ezután a logikai alkalmazáson kívül is létrehozhatja a függvényt a Azure Portal, vagy a Logic app Designerben a logikai alkalmazáson [belülről](#create-function-designer) .

* A Logic apps használata esetén ugyanazok a követelmények vonatkoznak a Function apps és a functions szolgáltatásra, függetlenül attól, hogy azok már meglévőek vagy újak:

  * A Function alkalmazásnak és a logikai alkalmazásnak ugyanazt az Azure-előfizetést kell használnia.

  * Az új Function apps szolgáltatásnak a .NET-et vagy a JavaScriptet kell használnia futtatókörnyezeti veremként. Új függvény meglévő függvények alkalmazásaihoz való hozzáadásakor a C# vagy a JavaScript lehetőséget választhatja.

  * A függvény a **http-trigger** sablont használja.

    A HTTP-trigger sablonja elfogadhat olyan tartalmakat, amelyek `application/json` típusa a logikai alkalmazásból származik. Ha hozzáad egy függvényt a logikai alkalmazáshoz, a Logic app Designer megjeleníti a sablonból az Azure-előfizetésében létrehozott egyéni függvényeket.

  * A függvény nem használ egyéni útvonalakat, hacsak nem adott meg [OpenAPI-definíciót](../azure-functions/functions-openapi-definition.md) (korábbi nevén egy [hencegő fájlt](https://swagger.io/)).

  * Ha OpenAPI-definíciója van a függvényhez, a Logic Apps Designer gazdagabb élményt nyújt a függvények paramétereinek használata során. Ahhoz, hogy a logikai alkalmazás megtalálja és hozzáférhessen a OpenAPI-definíciókkal rendelkező függvényekhez, [állítsa be a Function alkalmazást a következő lépésekkel](#function-swagger).

* Az a logikai alkalmazás, amelyhez hozzá kívánja adni a függvényt, beleértve a logikai alkalmazás első lépéseként [elindított triggert](../logic-apps/logic-apps-overview.md#logic-app-concepts) is

  A functions-t futtató műveletek hozzáadása előtt a logikai alkalmazásnak triggerrel kell kezdődnie. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI-leírásokkal rendelkező függvények keresése

Ha a Logic Apps Designerben a Function paraméterekkel dolgozik, érdemes megismernie egy [OpenAPI-definíciót](../azure-functions/functions-openapi-definition.md)(korábban egy [hencegő fájlként](https://swagger.io/)) a függvényhez. A Function alkalmazás beállításához a logikai alkalmazás megkeresheti és használhatja a hencegés leírásával rendelkező függvényeket, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a Function alkalmazás aktívan fut.

1. A Function alkalmazásban a következő lépések végrehajtásával állítson be [több eredetű erőforrás-megosztást (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) , hogy minden eredet engedélyezve legyen:

   1. A **Function apps** listából válassza ki a Function alkalmazást. A jobb oldali ablaktáblán válassza a **platform szolgáltatások**  >  **CORS** elemet.

      ![Válassza ki a Function alkalmazást > "platform-funkciók" > "CORS" elemet.](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. A **CORS** területen adja hozzá a csillag ( **`*`** ) helyettesítő karaktert, de távolítsa el a listában szereplő összes többi eredetet, majd kattintson a **Mentés** gombra.

      ![A "*" helyettesítő karakter CORS beállítása](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Hozzáférési tulajdonságértékek HTTP-kérelmeken belül

A webhook functions képes a HTTP-kérelmek bemenetként való elfogadására és a kérelmek más függvényeknek való továbbítására. Például bár a Logic Apps olyan [függvényeket tartalmaz, amelyek datetime értékeket konvertálnak](../logic-apps/workflow-definition-language-functions-reference.md), ez az alapszintű minta JavaScript-függvény azt mutatja be, hogyan férhet hozzá egy tulajdonsághoz a függvénynek átadott kérelem-objektumon belül, és műveleteket hajtson végre a tulajdonság értékén. Az objektumokon belüli tulajdonságok eléréséhez ez a példa a [dot (.) operátort](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)használja:

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

A következő lépések történnek a függvényen belül:

1. A függvény létrehoz egy `data` változót, és hozzárendeli az objektumot `body` az `request` objektumon belül erre a változóra. A függvény a pont (.) operátor használatával hivatkozik az objektumra `body` az `request` objektumon belül:

   ```javascript
   var data = request.body;
   ```

1. A függvény most már elérheti a `date` tulajdonságot a `data` változón keresztül, és az adott tulajdonság értékét a DateTime típusról DateString típusra konvertálhatja a függvény meghívásával `ToDateString()` . A függvény az eredményt a `body` függvény válaszában szereplő tulajdonságon keresztül is visszaadja:

   ```javascript
   body: data.date.ToDateString();
   ```

Most, hogy létrehozta a függvényt az Azure-ban, kövesse a [függvények hozzáadása a Logic apps](#add-function-logic-app)szolgáltatáshoz című témakör lépéseit.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Függvények létrehozása logikai alkalmazásokon belül

A Logic app Designer beépített Azure Functions műveletének használatával közvetlenül a logikai alkalmazás munkafolyamataiból hozhat létre függvényeket, de ezt a módszert csak a JavaScriptben írt függvények esetében használhatja. Más nyelvek esetében a Azure Portal Azure Functions felületén keresztül hozhat létre függvényeket. További információ: az [első függvény létrehozása a Azure Portalban](../azure-functions/functions-get-started.md).

Azonban ahhoz, hogy létre tudja hozni a függvényt az Azure-ban, már rendelkeznie kell egy Function alkalmazással, amely a függvények tárolója. Ha nem rendelkezik Function alkalmazással, előbb hozza létre a Function alkalmazást. Lásd: az [első függvény létrehozása a Azure Portalban](../azure-functions/functions-get-started.md).

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A függvény létrehozásához és hozzáadásához kövesse az adott forgatókönyvhöz tartozó lépést:

   * A logikai alkalmazás munkafolyamatának utolsó lépése alatt válassza az **új lépés** lehetőséget.

   * A logikai alkalmazás munkafolyamatának meglévő lépései között vigye az egérmutatót a nyílra, válassza ki a plusz (+) jelet, majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként az "Azure functions" kifejezést. A műveletek listából válassza az Azure- **függvény kiválasztása** műveletet, például:

   ![Függvények keresése a Azure Portalban.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A Function apps listából válassza ki a Function alkalmazást. A műveletek lista megnyitása után válassza a következő műveletet: **új függvény létrehozása**.

   ![Válassza ki a Function alkalmazást](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. A Function definition Editorban adja meg a függvényt:

   1. A **függvény neve** mezőben adja meg a függvény nevét.

   1. A **Code (kód** ) mezőben adja hozzá a kódot a függvény sablonhoz, beleértve a logikai alkalmazásnak a működés befejezése után visszaadott válaszokat és hasznos adatokat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   Például:

   ![A függvény megadása](./media/logic-apps-azure-functions/add-code-function-definition.png)

   A sablon kódjában az *`context` objektum* arra az üzenetre hivatkozik, amelyet a logikai alkalmazás egy későbbi lépésben a **kérés törzse** mezőben küld. Az `context` objektum tulajdonságainak a függvényen belüli eléréséhez használja a következő szintaxist:

   `context.body.<property-name>`

   Ha például az `content` objektumon belüli tulajdonságra szeretne hivatkozni `context` , használja a következő szintaxist:

   `context.body.content`

   A sablon kódja tartalmaz egy `input` változót is, amely a paraméter értékét tárolja, `data` így a függvény végrehajthat műveleteket ezen az értéken. JavaScript-függvényeken belül a `data` változó a következőhöz is: `context.body` .

   > [!NOTE]
   > Az `body` itt látható tulajdonság az `context` objektumra vonatkozik, és nem ugyanaz, mint a művelet kimenetében található **törzs** token, amelyet a függvénynek is át lehet adni.

1. A **kérelem törzse** mezőben adja meg a függvény bemenetét, amelyet JavaScript Object Notation (JSON) objektumként kell formázni.

   Ez a bemenet a logikai alkalmazás által a függvénynek küldött *környezeti objektum* vagy üzenet. Ha a **kérelem törzse** mezőre kattint, megjelenik a dinamikus tartalom lista, így kiválaszthatja az előző lépésekből származó kimenetekhez tartozó jogkivonatokat. Ez a példa azt adja meg, hogy a környezeti adattartalom tartalmaz egy nevű tulajdonságot `content` , amely **az** e-mail-triggerből származó jogkivonat-értékkel rendelkezik.

   !["Kérelem törzse" – példa – környezeti objektum hasznos adatai](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a környezeti objektum nem karakterláncként van feldolgozva, így az objektum tartalma közvetlenül a JSON-adattartalomhoz lesz hozzáadva. Ha azonban a környezeti objektum nem JSON-jogkivonat, amely egy karakterláncot, egy JSON-objektumot vagy egy JSON-tömböt továbbít, hibaüzenet jelenik meg. Tehát ha ez a példa a **kapott** időtokent használta, akkor a környezeti objektumot karakterláncként is elvégezheti idézőjelek hozzáadásával.

   ![Objektum karakterláncként való vetítése](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. További részletek, például a használandó módszer, a fejlécek vagy a lekérdezési paraméterek vagy a hitelesítés megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt beállításokat. A hitelesítéshez a beállítások a kiválasztott függvénytől függően eltérőek lehetnek. Lásd [a függvények hitelesítésének engedélyezése](#enable-authentication-functions)című témakört.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Meglévő függvények hozzáadása a Logic apps szolgáltatáshoz

A logikai alkalmazásokból származó meglévő függvények meghívásához olyan függvényeket adhat hozzá, mint bármely más művelet a Logic app Designerben.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A függvény hozzáadásának lépéséhez válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen a keresőmezőbe írja be szűrőként az "Azure functions" kifejezést. A műveletek listából válassza az Azure- **függvény kiválasztása** műveletet.

   ![Keressen egy függvényt az Azure-ban.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. A Function apps listából válassza ki a Function alkalmazást. A függvények lista megjelenése után válassza ki a függvényt.

   ![Válassza ki a Function alkalmazást és a függvényt](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Az API-definíciókkal (hencegés leírásával) rendelkező függvények esetén a [logikai alkalmazás megkeresheti és elérheti ezeket a funkciókat](#function-swagger), de kiválaszthatja a feltört **műveleteket**.

   ![Válassza ki a Function alkalmazást, a "hencegő műveletek" és a függvényt](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. A **kérelem törzse** mezőben adja meg a függvény bemenetét, amelyet JavaScript Object Notation (JSON) objektumként kell formázni.

   Ez a bemenet a logikai alkalmazás által a függvénynek küldött *környezeti objektum* vagy üzenet. Ha a **kérelem törzse** mezőre kattint, megjelenik a dinamikus tartalom lista, ahol kiválaszthatja az előző lépésekből származó kimenetekhez tartozó jogkivonatokat. Ez a példa azt adja meg, hogy a környezeti adattartalom tartalmaz egy nevű tulajdonságot `content` , amely **az** e-mail-triggerből származó jogkivonat-értékkel rendelkezik.

   !["Kérelem törzse" – példa – környezeti objektum hasznos adatai](./media/logic-apps-azure-functions/function-request-body-example.png)

   Itt a környezeti objektum nem karakterláncként van feldolgozva, így az objektum tartalma közvetlenül a JSON-adattartalomhoz lesz hozzáadva. Ha azonban a környezeti objektum nem JSON-jogkivonat, amely egy karakterláncot, egy JSON-objektumot vagy egy JSON-tömböt továbbít, hibaüzenet jelenik meg. Tehát ha ez a példa a **kapott** időtokent használta, akkor a környezeti objektumot karakterláncként is elvégezheti idézőjelek hozzáadásával:

   ![Objektum karakterláncként való vetítése](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. További részletek, például a használandó módszer, a fejlécek, a lekérdezési paraméterek vagy a hitelesítés megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt beállításokat. A hitelesítéshez a beállítások a kiválasztott függvénytől függően eltérőek lehetnek. Lásd: [hitelesítés engedélyezése a functions](#enable-authentication-functions)szolgáltatásban.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Logikai alkalmazások hívása függvényekből

Ha a logikai alkalmazást egy függvényen belül szeretné elindítani, a logikai alkalmazásnak egy olyan triggerrel kell kezdődnie, amely meghívásos végpontot biztosít. Elindíthatja például a logikai alkalmazást a **http**, a **kérelem**, az **Azure Queues** vagy a **Event Grid** trigger használatával. A függvényen belül küldjön egy HTTP POST-kérelmet az trigger URL-címére, és adja meg a logikai alkalmazás feldolgozására használni kívánt adattartalmat. További információ: a [logikai alkalmazások hívása, triggere vagy beágyazása](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>A függvények hitelesítésének engedélyezése

Ha egyszerűen hitelesíteni szeretné a Azure Active Directory (Azure AD) által védett más erőforrásokhoz való hozzáférést anélkül, hogy be kellene jelentkeznie, és meg kell adnia a hitelesítő adatokat vagy a titkos kulcsokat, a logikai alkalmazás [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén Managed Service Identity vagy msi) is használhat. Az Azure kezeli Ön helyett ezt az identitást, és segít biztonságban tartani az Ön hitelesítő adatait, mivel így nincs szükség titkos kulcsok megadására és rendszeres módosítására. További információ az Azure [AD-hitelesítés felügyelt identitásait támogató Azure-szolgáltatásokról](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Ha úgy állítja be a logikai alkalmazást, hogy a rendszer által hozzárendelt identitást vagy manuálisan létrehozott, felhasználó által hozzárendelt identitást használja, akkor a logikai alkalmazás függvénye ugyanezt az identitást is használhatja a hitelesítéshez. A Logic apps függvények hitelesítési támogatásával kapcsolatos további információkért lásd: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Az alábbi lépéseket követve állíthatja be és használhatja a felügyelt identitást a függvénnyel:

1. Engedélyezze a felügyelt identitást a logikai alkalmazáson, és állítsa be az identitás hozzáférését a célként megadott erőforráshoz. Lásd: [Az Azure-erőforrásokhoz való hozzáférés hitelesítése a Azure Logic apps felügyelt identitások használatával](../logic-apps/create-managed-service-identity.md).

1. Engedélyezze a hitelesítést a függvényben és a függvény alkalmazásban az alábbi lépéseket követve:

   * [Névtelen hitelesítés beállítása a függvényben](#set-authentication-function-app)
   * [Azure AD-hitelesítés beállítása a Function alkalmazásban](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Névtelen hitelesítés beállítása a függvényben

Ha a logikai alkalmazás felügyelt identitását szeretné használni a függvényben, a függvény hitelesítési szintjét névtelen értékre kell állítania. Ellenkező esetben a logikai alkalmazás "BadRequest" hibát jelez.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a Function alkalmazást. Ezek a lépések az "FabrikamFunctionApp" kifejezést használják példaként szolgáló Function alkalmazásként.

1. A Function alkalmazás ablaktáblán válassza a **platform szolgáltatások** elemet. A **fejlesztői eszközök** területen válassza a **speciális eszközök (kudu)** lehetőséget.

   ![A kudu speciális eszközeinek megnyitása](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. A kudu webhely címsorában, a **hibakeresési konzol** menüjében válassza a **cmd** elemet.

   ![A hibakeresési konzol menüjében válassza a "CMD" lehetőséget.](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. A következő oldal megjelenése után a mappalistában válassza a **site**  >  **wwwroot**  >  *a-Function* elemet. Ezek a lépések az "FabrikamAzureFunction" függvényt használják példaként.

   ![Válassza ki a "site" > "wwwroot" > a függvényt](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Nyissa meg a `function.json` fájlt szerkesztésre.

   ![Kattintson a Szerkesztés elemre a "function.json" fájlhoz](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. Az `bindings` objektumban győződjön meg arról, hogy a `authLevel` tulajdonság létezik. Ha a tulajdonság létezik, állítsa a tulajdonság értékét értékre `anonymous` . Ellenkező esetben adja hozzá ezt a tulajdonságot, és állítsa be az értéket.

   ![Adja hozzá a "authLevel" tulajdonságot, és állítsa a "névtelen" értékre.](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Ha elkészült, mentse a beállításokat, majd folytassa a következő szakasszal.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Azure AD-hitelesítés beállítása a Function alkalmazáshoz

A feladat elindítása előtt keresse meg és helyezze el ezeket az értékeket a későbbi használat érdekében:

* A logikai alkalmazást jelölő, rendszerhez rendelt identitáshoz generált objektumazonosító

  * Az objektumazonosító létrehozásához [engedélyezze a logikai alkalmazás rendszer által hozzárendelt identitását](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Ellenkező esetben az objektumazonosító megkereséséhez nyissa meg a logikai alkalmazást a Logic app Designerben. A logikai alkalmazás menüjének **Beállítások** területén válassza az **Identity**  >  **System Assigned** elemet.

* Azure Active Directory (Azure AD) bérlői címtárának azonosítója

  A bérlő címtár-AZONOSÍTÓjának lekéréséhez futtathatja a [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) PowerShell-parancsot. Vagy a Azure Portal hajtsa végre az alábbi lépéseket:

  1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a Function alkalmazást.

  1. Keresse meg és válassza ki az Azure AD-bérlőt. Ezek a lépések a "Fabrikam"-t használják példaként a bérlőként.

  1. A bérlő menü **kezelés** területén válassza a **Tulajdonságok** elemet.

  1. Másolja például a bérlő címtár-AZONOSÍTÓját, és mentse az azonosítót későbbi használatra.

     ![Az Azure AD-bérlő címtár-AZONOSÍTÓjának megkeresése és másolása](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Az elérni kívánt cél erőforráshoz tartozó erőforrás-azonosító

  * Az erőforrás-azonosítók megkereséséhez tekintse át az [Azure ad-t támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Ennek az erőforrás-AZONOSÍTÓnak pontosan egyeznie kell az Azure AD által várt értékkel, beleértve a szükséges záró perjeleket is.

  Ez az erőforrás-azonosító szintén ugyanaz az érték, amelyet később a **célközönség** tulajdonságban használ, amikor [Beállítja a függvény műveletét a rendszer által hozzárendelt identitás használatára](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Most már készen áll az Azure AD-hitelesítés beállítására a Function alkalmazáshoz.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a Function alkalmazást.

1. A Function alkalmazás ablaktáblán válassza a **platform szolgáltatások** elemet. A **hálózat** területen válassza a **hitelesítés/engedélyezés** lehetőséget.

   ![Hitelesítési és engedélyezési beállítások megtekintése](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Módosítsa a **app Service hitelesítési** beállítást **a be** értékre. Ha a **kérelem nem hitelesítve van, akkor az elvégzendő műveletből** válassza a **Bejelentkezés a következővel Azure Active Directory** elemet. A **hitelesítésszolgáltatók** területen válassza a **Azure Active Directory** lehetőséget.

   ![Hitelesítés bekapcsolása az Azure AD-vel](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. A **Azure Active Directory beállítások** ablaktáblán hajtsa végre az alábbi lépéseket:

   1. Állítsa be a **felügyeleti módot** a **speciális** értékre.

   1. Az **ügyfél-azonosító** tulajdonságban adja meg a logikai alkalmazás rendszer által hozzárendelt identitásához tartozó objektumazonosítót.

   1. A **kiállító URL-címe** tulajdonságban adja meg az `https://sts.windows.net/` URL-címet, és fűzze hozzá az Azure ad-bérlő címtár-azonosítóját.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Az **engedélyezett jogkivonat-célközönségek** tulajdonságnál adja meg az elérni kívánt cél erőforrás erőforrás-azonosítóját.

      Ez az erőforrás-azonosító ugyanaz az érték, amelyet később a **célközönség** tulajdonságban használ, amikor [Beállítja a függvény műveletét a rendszer által hozzárendelt identitás használatára](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   Ezen a ponton a verziója ehhez a példához hasonlóan néz ki:

   ![Azure Active Directory hitelesítési beállítások](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Amikor elkészült, válassza az **OK** lehetőséget.

1. Térjen vissza a Logic app Designer szolgáltatáshoz, és kövesse a [lépéseket a felügyelt identitással való hozzáférés hitelesítéséhez](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Logic apps-összekötőről](../connectors/apis-list.md)