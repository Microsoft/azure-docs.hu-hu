---
title: Kapcsolódás Common Data Servicehoz (Microsoft Dataverse)
description: Common Data Service-(Microsoft-Dataverse-) rekordok létrehozása és kezelése Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 02/11/2021
tags: connectors
ms.openlocfilehash: bec3416195358121b85eb61679ab39647e664a9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382351"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Rekordok létrehozása és kezelése Common Data Serviceban (Microsoft Dataverse) a Azure Logic Apps használatával

> [!NOTE]
> November 2020-én átnevezték Common Data Service a Microsoft Dataverse.

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Common Data Service-összekötővel](/connectors/commondataservice/)olyan automatizált munkafolyamatokat hozhat létre, amelyek a [Common Data Service, a Microsoft Dataverse](/powerapps/maker/common-data-service/data-platform-intro) -adatbázisában is kezelhetik a rekordokat. Ezek a munkafolyamatok létrehozhatnak rekordokat, frissíthetik a rekordokat, és egyéb műveleteket végezhetnek el. A Dataverse-adatbázisból is kérhet információt, és a kimenet elérhetővé teheti a logikai alkalmazásban használható egyéb műveletek számára. Ha például egy rekord frissül a Dataverse-adatbázisban, az Office 365 Outlook Connector használatával küldhet e-mailt.

Ez a cikk bemutatja, hogyan hozhat létre egy olyan logikai alkalmazást, amely egy új érdeklődői rekord létrehozásakor feladatsort hoz létre.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [Common Data Service-környezet](/power-platform/admin/environments-overview), amely a szervezet által az üzleti és a Common Data Service adatbázis tárolására, kezelésére és megosztására szolgáló terület. További információkért tekintse meg a következő forrásokat:<p>

  * [További információ: a Common Data Service első lépései](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power platform – környezetek – áttekintés](/power-platform/admin/environments-overview)

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md) és a logikai alkalmazásról, amelyről a Dataverse-adatbázisban lévő rekordokat szeretné elérni. Ha Common Data Service triggerrel szeretné elindítani a logikai alkalmazást, üres logikai alkalmazásra van szüksége. Ha most ismerkedik a Azure Logic Appsval, tekintse át a gyors útmutató [: az első munkafolyamat létrehozása a Azure Logic Apps használatával](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Common Data Service trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Ebben a példában adja hozzá a Common Data Service eseményindítót, amely új rekord létrehozásakor következik be.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. A keresőmezőbe írja be a `common data service` kifejezést. Ebben a példában az eseményindítók listában válassza ki ezt az eseményindítót: **rekord létrehozásakor**

   !["A rekord létrehozásakor" trigger kiválasztása](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Ha a rendszer kéri, jelentkezzen be a Common Data Serviceba.

1. A triggerben adja meg a környezetet, ahol figyelni szeretné az új "leads" rekordokat, például:

   ![A figyeléshez használt környezet információinak kiváltása](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Environment** | Yes | A figyelni kívánt környezet, például "Fabrikam Sales Production". További információ: [Power platform – környezetek – áttekintés](/power-platform/admin/environments-overview). |
   | **Entitás neve** | Yes | A figyelni kívánt entitás, például "leads" |
   | **Hatókör** | Yes | Az új rekordot létrehozó forrás, például egy felhasználó a céges egységben vagy a szervezet bármely felhasználója. Ez a példa a "Business Unit" szolgáltatást használja. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service művelet hozzáadása

Most adjon hozzá egy Common Data Service műveletet, amely létrehoz egy új "leads" rekordhoz tartozó feladat rekordot.

1. A **rekord létrehozásakor** trigger alatt válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be a `common data service` kifejezést. A műveletek listából válassza ki ezt a műveletet: **új rekord létrehozása**

   ![Válassza az "új rekord létrehozása" műveletet.](./media/connect-common-data-service/select-create-new-record-action.png)

1. A műveletben adja meg azt a környezetet, amelyben létre szeretné hozni az új feladathoz tartozó rekordot. Ha elérhető, más tulajdonságok is megjelennek a művelethez kiválasztott entitás alapján, például:

   ![A rekord létrehozására szolgáló környezetre vonatkozó műveleti információk](./media/connect-common-data-service/create-new-record-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Yes | Az a környezet, amelyben létre kívánja hozni a rekordot, és nem feltétlenül ugyanazt a környezetet kell megadnia az triggerben, de ebben a példában a "Fabrikam értékesítési termelés" szerepel. |
   | **Entitás neve** | Yes | Az entitás, amelyben létre szeretné hozni a rekordot, például "feladatok" |
   | **Tárgy** | Igen, az ebben a példában kiválasztott entitás alapján | A feladat céljával kapcsolatos rövid leírás |
   ||||

   1. A **Tárgy** tulajdonságnál adja meg a következő szöveget egy záró szóközzel:

      `Follow up with new lead:`

   1. Tartsa a mutatót a **Tárgy** mezőben, hogy a dinamikus tartalmak listája látható maradjon.
   
   1. A listában a **rekord létrehozásakor** szakaszban válassza ki a feladat rekordba felvenni kívánt trigger kimeneteket, például:

      ![Válassza ki a feladat rekordban használni kívánt trigger-kimeneteket](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Trigger kimenete | Leírás |
      |----------------|-------------|
      | **Keresztnév** | Az első név a rekordban, amelyet elsődleges partnerként kíván használni a feladatban. |
      | **Vezetéknév** | A tevékenység rekordjában az elsődleges partnerként használandó utolsó név a vezető rekordban |
      | **Leírás** | A feladatsorba foglalandó egyéb kimenetek, például e-mail-cím és munkahelyi telefonszám |
      |||

   Ha elkészült, a művelet a következő példához hasonló lehet:

   ![Az "új rekord létrehozása" művelet befejeződött](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** lehetőséget. A logikai alkalmazás teszteléséhez hozzon létre egy új "leads" rekordot.

## <a name="trigger-only-on-updated-attributes"></a>Csak a frissített attribútumok esetében aktiválódik

A rekordok frissítésekor futó eseményindítók esetén, például **Ha a rekord frissítésekor művelet történik** , használhat szűrési attribútumokat úgy, hogy a logikai alkalmazás csak akkor fusson, amikor a megadott attribútumok frissülnek. Ez a funkció segít megelőzni a szükségtelen logikai alkalmazások futtatását.

1. Az triggerben az **új paraméter hozzáadása** listából válassza az **attribútumok szűrők** elemet.

   ![Képernyőfelvétel: "a rekord frissítésekor" művelet és a megnyitott "új paraméter hozzáadása" lista a "Attribute Filters" tulajdonsággal kiválasztva.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Minden **attribútum szűrő elemnél** válassza ki a frissítések figyeléséhez használni kívánt attribútumot, például:

   !["Attribute Filters" tulajdonság hozzáadása](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Rekordok listázása szűrő alapján

A rekordokat visszaadó műveletek, például a **rekordok listázása** művelettel olyan ODATA-lekérdezést használhat, amely a megadott szűrő alapján visszaadja a rekordokat. A művelet például csak az aktív fiókok rekordjait sorolja fel.

1. A műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **szűrő lekérdezése** tulajdonságot.

   !["Lekérdezés szűrése" tulajdonság hozzáadása](./media/connect-common-data-service/list-records-action-filter-query.png)

1. A **szűrő lekérdezési** tulajdonságában, amely most megjelenik a műveletben, írja be ezt a ODATA-szűrő lekérdezést: `statuscode eq 1`

   ![Adja meg a rekordok szűrésére szolgáló ODATA-szűrési lekérdezést](./media/connect-common-data-service/list-records-action-filter-query-value.png)

További információ a `$filter` rendszerlekérdezési lehetőségekről: [Common Data Service-Filter Results](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Rekordok listázása rendelés alapján

A rekordokat visszaadó műveletek, például a **rekordok listázása** művelettel olyan ODATA-lekérdezést használhat, amely egy megadott sorrendben adja vissza a rekordokat, amelyek a művelet által visszaadott rekordoktól függően változnak. Megadhatja például, hogy a művelet a fiók neve alapján sorolja fel a rekordokat.

1. A műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza ki az **Order by** tulajdonságot.

   !["Order by" tulajdonság hozzáadása](./media/connect-common-data-service/list-records-action-order-by.png)

1. A műveletben most megjelenő **Order by** tulajdonsággal írja be ezt a ODATA-szűrési lekérdezést: `name`

   ![Adja meg a ODATA szűrő lekérdezését a rekordok rendezéséhez](./media/connect-common-data-service/list-records-action-order-by-value.png)

További információ a `$orderby` rendszerlekérdezési lehetőségekről: [Common Data Service Order Results](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Mező adattípusai

Függetlenül attól, hogy manuálisan adott-e meg egy értéket, vagy kijelöl egy értéket a dinamikus tartalmak listájából egy trigger vagy művelet mezőjében, az érték adattípusának meg kell egyeznie a mező kötelező adattípusával.

Ez a táblázat néhány mezőtípus és azon adattípusok leírását írja le, amelyekre ezek a mezők az értékekhez szükségesek.

| Mező | Adattípus | Leírás |
|-------|-----------|-------------|
| Szövegmező | Egysoros szöveg | Egy olyan szöveges vagy dinamikus tartalmat igényel, amely a szöveg adattípussal rendelkezik, például a következő tulajdonságokkal: <p><p>- **Leírás** <br>- **Kategória** |
| Egész szám mező | Egész szám | Az egész szám adattípusú egész vagy dinamikus tartalmat igényel, például ezek a tulajdonságok: <p><p>- **Készültségi százalék** <br>- **Időtartama** |
| Dátum mező | Dátum és idő | A (z) hh/nn/YYY formátumú vagy a dátum adattípusú dinamikus tartalomra vonatkozó dátumot igényel, például ezek a tulajdonságok: <p><p>- **Létrehozva:** <br>- **Kezdési dátum** <br>- **Tényleges indítás** <br>- **Tényleges Befejezés** <br>- **Esedékesség dátuma** |
| Egy másik entitási rekordra hivatkozó mező | Elsődleges kulcs | A rekord AZONOSÍTÓját (például GUID) és egy keresési típust is igényli, ami azt jelenti, hogy a dinamikus tartalom lista értékei nem működnek, például ezek a tulajdonságok: <p><p>- **Tulajdonos**: érvényes felhasználói azonosítónak vagy Team Record azonosítónak kell lennie. <br>- **Tulajdonos típusa**: olyan keresési típusnak kell lennie `systemusers` , mint a vagy a `teams` . <p><p>- **Kapcsolatban**: érvényes rekordazonosítónek kell lennie, például egy fiók azonosítójának vagy egy kapcsolattartói rekord azonosítójának. <br>- A **típushoz kapcsolódóan a következő** keresési típusnak kell lennie: `accounts` `contacts` , vagy, illetve. <p><p>- **Ügyfél**: érvényes rekordazonosító, például fiókazonosító vagy kapcsolattartói rekord azonosítója. <br>- **Ügyfél típusa**: a keresési típusnak (például vagy) kell lennie `accounts` `contacts` . |
||||

Ez a példa azt szemlélteti, hogy az **új rekord létrehozása** művelet hogyan hoz létre egy új "Tasks" rekordot, amely más entitás-rekordokhoz van társítva, konkrétan egy felhasználói rekorddal és egy fiók rekorddal. A művelet az adott entitás rekordjaihoz tartozó azonosítókat és keresési típusokat adja meg a releváns tulajdonságok várt adattípusának megfelelő értékek használatával.

* A **tulajdonos** tulajdonság alapján, amely megadja a felhasználói azonosítót és a **tulajdonos Type** tulajdonságát, amely megadja a `systemusers` keresési típust, a művelet az új "feladatok" rekordot társítja egy adott felhasználóval.

* A **kapcsolódó** tulajdonság alapján, amely meghatározza a rekord azonosítóját, és a keresési típust meghatározó **Type** tulajdonságot, `accounts` a művelet társítja az új "feladatok" rekordot egy adott fiókkal.

![Azonosítókkal és keresési típusokkal társított "feladatok" rekord létrehozása](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötők kivágási leírásán alapuló technikai információk, például eseményindítók, műveletek, korlátok és egyéb részletek: az [összekötő hivatkozási lapja](/connectors/commondataservice/).

## <a name="troubleshooting-problems"></a>Hibaelhárítási problémák

### <a name="calls-from-multiple-environments"></a>Több környezetből indított hívások

Mindkét összekötő, Common Data Service és Common Data Service (aktuális környezet) tárolja a logikai alkalmazások munkafolyamataival kapcsolatos információkat, amelyekre szüksége van, és értesítést kap az entitások változásairól a `callbackregistrations` Microsoft-Dataverse található entitás használatával. Dataverse-szervezet másolásakor a rendszer minden webhookot is másol. Ha a szervezetre leképezett munkafolyamatok letiltását megelőzően másolja a munkahelyét, a másolt webhookok ugyanazon logikai alkalmazásokon is mutatnak, amelyek ezután több szervezettől kapnak értesítéseket.

A nemkívánatos értesítések leállításához törölje a visszahívási regisztrációt a szervezettől, amely az alábbi lépéseket követve küldi el ezeket az értesítéseket:

1. Azonosítsa azt a Dataverse-szervezetet, amelyről el szeretné távolítani az értesítéseket, és jelentkezzen be az adott szervezetbe.

1. A Chrome böngészőben keresse meg a törölni kívánt visszahívási regisztrációt a következő lépésekkel:

   1. Tekintse át az összes visszahívási regisztráció általános listáját a következő OData URI-n, hogy az entitáson belül megtekinthető legyen az adatai `callbackregistrations` :

      `https://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations`:

      > [!NOTE]
      > Ha nem ad vissza értéket, lehet, hogy nincs engedélye az entitás típusának megtekintésére, vagy előfordulhat, hogy nem jelentkezik be a megfelelő szervezetbe.

   1. Szűrje az eseményindító entitás logikai nevét `entityname` és a logikai alkalmazás munkafolyamatának megfelelő értesítési eseményt (üzenet). Minden eseménytípus a következőképpen van leképezve az üzenet egészére:

      | Eseménytípus | Üzenet egésze |
      |------------|-----------------|
      | Létrehozás | 1 |
      | Törlés | 2 |
      | Frissítés | 3 |
      | CreateOrUpdate | 4 |
      | CreateOrDelete | 5 |
      | UpdateOrDelete | 6 |
      | CreateOrUpdateOrDelete | 7 |
      |||

      Ebből a példából megtudhatja, hogyan szűrheti az `Create` értesítéseket egy nevű entitásra `nov_validation` a következő OData URI használatával egy minta szervezet számára:

      `https://fabrikam-preprod.crm1.dynamics.com/api/data/v9.0/callbackregistrations?$filter=entityname eq 'nov_validation' and message eq 1`

      ![Képernyőkép, amely a címsorban a böngészőablakot és a OData URI-t jeleníti meg.](./media/connect-common-data-service/find-callback-registrations.png)

      > [!TIP]
      > Ha ugyanahhoz az entitáshoz vagy eseményhez több eseményindító is létezik, a listát további szűrők, például a `createdon` és attribútumok használatával szűrheti `_owninguser_value` . A tulajdonos felhasználójának neve a alatt jelenik meg `/api/data/v9.0/systemusers({id})` .

   1. Miután megtalálta a törölni kívánt visszahívási regisztráció AZONOSÍTÓját, kövesse az alábbi lépéseket:
   
      1. A Chrome böngészőben nyissa meg a Chrome Fejlesztői eszközök (billentyűzet: F12).

      1. A felső ablakban válassza a **konzol** fület.

      1. A parancssorba írja be ezt a parancsot, amely a megadott visszahívási regisztráció törlésére vonatkozó kérést küld:

         `fetch('http://{organization-name}.crm{instance-number}.dynamics.com/api/data/v9.0/callbackregistrations({ID-to-delete})', { method: 'DELETE'})`

         > [!IMPORTANT]
         > Győződjön meg arról, hogy a kérést egy nem egységesített ügyfél-illesztőfelület (UCI) lapról hajtja végre, például a OData vagy az API-válasz oldalról. Ellenkező esetben a app.js fájlban lévő logika zavarhatja a műveletet.

   1. Győződjön meg arról, hogy a visszahívás regisztrációja már nem létezik, ellenőrizze a visszahívási regisztrációk listáját.

## <a name="next-steps"></a>Következő lépések

* További tudnivalók [a Azure Logic apps-összekötők](../connectors/apis-list.md) használatáról
