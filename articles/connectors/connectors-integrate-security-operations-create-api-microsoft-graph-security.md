---
title: Biztonsági műveletek integrálása és kezelése & Microsoft Graph biztonsággal
description: Javítsa az alkalmazás veszélyforrások elleni védelmét, észlelését és válaszát Microsoft Graph biztonsági & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888538"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>A biztonsági műveletek Microsoft Graph biztonsági & való integrálásával növelheti a veszélyforrások elleni védelmet Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Microsoft Graph biztonsági](/graph/security-concept-overview) összekötővel javíthatja az alkalmazás által észlelt, védett és a fenyegetésekre való reagálást a Microsoft biztonsági termékeinek, szolgáltatásainak és partnereinek integrálására szolgáló automatizált munkafolyamatok létrehozásával. Létrehozhat például olyan [Azure Security Center](../security-center/workflow-automation.md) forgatókönyveket, amelyek Microsoft Graph biztonsági entitások, például riasztások figyelésére és kezelésére szolgálnak. Íme néhány forgatókönyv, amelyet a Microsoft Graph biztonsági összekötő támogat:

* Riasztások lekérése lekérdezés vagy riasztási azonosító alapján. Lekérhet például egy olyan listát, amely nagy súlyosságú riasztásokat tartalmaz.

* Riasztások frissítése. Például frissítheti a riasztási hozzárendeléseket, megjegyzéseket fűzhet a riasztásokhoz, vagy címkézheti a riasztásokat.

* A riasztási [előfizetések (webhookok)](/graph/api/resources/webhooks)létrehozásával figyelheti a riasztások létrehozását vagy módosítását.

* A riasztási előfizetések kezelése. Lekérheti például az aktív előfizetéseket, kiterjesztheti az előfizetés lejárati idejét, vagy törölhet előfizetéseket.

A logikai alkalmazás munkafolyamata olyan műveleteket is használhat, amelyek válaszokat kapnak a Microsoft Graph biztonsági összekötőtől, és ezt a kimenetet a munkafolyamat más műveletei számára is elérhetővé teszik. A munkafolyamatban más műveletek is megadhatók a Microsoft Graph biztonsági összekötő műveleteiből származó kimenet használatával. Ha például magas súlyosságú riasztásokat kap a Microsoft Graph biztonsági összekötőn keresztül, az Outlook Connector használatával e-mailben elküldheti ezeket a riasztásokat egy e-mail-üzenetben. 

A Microsoft Graph biztonsággal kapcsolatos további tudnivalókért tekintse meg a [Microsoft Graph biztonsági API áttekintése](/graph/security-concept-overview)című témakört. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a következőt: [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md). Ha az automatizálást vagy a PowerApps keresi, olvassa el a [Mi az a Power automatizál?](https://flow.microsoft.com/) vagy a mi az a [Power apps?](https://powerapps.microsoft.com/) című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Microsoft Graph Security-összekötő használatához *explicit* Azure Active Directorybeli bérlői rendszergazdai hozzájárulás szükséges, amely a [Microsoft Graph Security hitelesítési követelményei](/graph/security-authorization) közé is tartozik. Ehhez a megkötéshez a Microsoft Graph biztonsági összekötő alkalmazás-azonosítója és neve szükséges, amelyet a [Azure Portal](https://portal.azure.com)is talál:

  | Tulajdonság | Érték |
  |----------|-------|
  | **Alkalmazás neve** | `MicrosoftGraphSecurityConnector` |
  | **Alkalmazásazonosító** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Az összekötő engedélyezéséhez az Azure AD-bérlő rendszergazdája a következő lépéseket hajthatja végre:

  * [Adjon bérlői rendszergazdai jóváhagyást az Azure AD-alkalmazásokhoz](../active-directory/develop/v2-permissions-and-consent.md).

  * A logikai alkalmazás első futtatásakor az alkalmazás az [alkalmazás-jóváhagyási felületen](../active-directory/develop/application-consent-experience.md) kérhet jóváhagyást az Azure AD bérlői rendszergazdájától.
   
* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni Microsoft Graph biztonsági entitásait, például a riasztásokat. Microsoft Graph biztonsági trigger használatához üres logikai alkalmazásra van szükség. Microsoft Graph biztonsági művelet használatához olyan logikai alkalmazásra van szükség, amely az adott forgatókönyvhöz tartozó megfelelő triggerrel kezdődik.

## <a name="connect-to-microsoft-graph-security"></a>Kapcsolódás Microsoft Graph biztonsághoz 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Az üres logikai alkalmazások esetében adja hozzá a triggert és minden egyéb műveletet, amelyet a Microsoft Graph biztonsági művelet hozzáadása előtt szeretne használni.

   -vagy-

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez Microsoft Graph biztonsági műveletet szeretne hozzáadni, válassza az **új lépés** lehetőséget.

   -vagy-

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet (+), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be a "Microsoft Graph Security" kifejezést a szűrőként. A műveletek listából válassza ki a kívánt műveletet.

1. Jelentkezzen be Microsoft Graph biztonsági hitelesítő adataival.

1. Adja meg a kiválasztott művelethez szükséges adatokat, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="add-triggers"></a>Eseményindítók hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor az eseményindító minden egyes indításakor létrehoz egy logikai alkalmazás-példányt, és elindítja az alkalmazás munkafolyamatának futtatását.

> [!NOTE] 
> Az eseményindító indításakor az eseményindító feldolgozza az összes új riasztást. Ha nem érkezik riasztás, a rendszer kihagyja a trigger futtatását. A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időköz alapján történik.

Ebből a példából megtudhatja, hogyan indíthat el egy logikai alkalmazás-munkafolyamatot, amikor új riasztásokat küld az alkalmazásnak.

1.  A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic app designert. Ez a példa a Azure Portal használja.

1.  A tervezőben a keresőmezőbe írja be a "Microsoft Graph Security" kifejezést a szűrőként. Az eseményindítók listából válassza ki ezt az eseményindítót: **minden új riasztásnál**

1.  A triggerben adja meg a figyelni kívánt riasztásokkal kapcsolatos információkat. További tulajdonságok megjelenítéséhez nyissa meg az **új paraméter hozzáadása** listát, és válasszon egy paramétert a tulajdonság hozzáadásához az triggerhez.

   | Tulajdonság | Tulajdonság (JSON) | Kötelező | Típus | Description |
   |----------|-----------------|----------|------|-------------|
   | **Intervallum** | `interval` | Yes | Egész szám | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján. Itt láthatók a minimális és a maximális intervallumok: <p><p>-Hónap: 1-16 hónap <br>-Nap: 1-500 nap <br>-Óra: 1 – 12000 óra <br>Perc: 1 – 72000 perc <br>-Másodperc: 1 – 9999999 másodperc <p>Ha például az intervallum 6, és a gyakoriság értéke "Month", akkor az ismétlődés 6 havonta történik. |
   | **Gyakoriság** | `frequency` | Igen | Sztring | Az ismétlődés időegysége: **másodperc**, **perc**, **óra**, **nap**, **hét** vagy **hónap** |
   | **Időzóna** | `timeZone` | Nem | Sztring | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el [UTC-eltolást](https://en.wikipedia.org/wiki/UTC_offset). Válassza ki az alkalmazni kívánt időzónát. |
   | **Kezdési idő** | `startTime` | Nem | Sztring | Adja meg a kezdő dátumot és időpontot a következő formátumban: <p><p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát választ <p>-vagy- <p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem jelöl ki időzónát <p>Ha például a 2017-es szeptember 18-án, 2:00 PM-nél szeretné, adja meg a "2017-09-18T14:00:00" parancsot, és válasszon ki egy időzónát, például a csendes-óceáni téli időpontot. Vagy a "2017-09-18T14:00:00Z" érték megadásával időzóna nélkül. <p>**Megjegyzés:** Ez a kezdési időpont legfeljebb 49 évvel későbbi, és az [ISO 8601 dátum-idő specifikációt](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) kell követnie [UTC dátum és idő formátumban](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), de [UTC-eltolás](https://en.wikipedia.org/wiki/UTC_offset)nélkül. Ha nem ad meg időzónát, a végén fel kell vennie a "Z" betűt szóközök nélkül. Ez a "Z" a megfelelő [tengeri időpontra](https://en.wikipedia.org/wiki/Nautical_time)hivatkozik. <p>Az egyszerű ütemtervek esetében a kezdési időpont az első előfordulás, míg a komplex ütemtervek esetében az trigger nem a kezdési időpontnál hamarabb következik be. [*Milyen módon használhatom a kezdő dátumot és időt?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

1.  Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel.

## <a name="add-actions"></a>Műveletek hozzáadása

Az alábbiakban részletesebb információkat talál a Microsoft Graph biztonsági összekötővel elérhető különböző műveletek használatáról.

### <a name="manage-alerts"></a>Riasztások kezelése

A legutóbbi eredmények szűréséhez, rendezéséhez vagy beszerzéséhez *csak* a [Microsoft Graph által támogatott ODATA-lekérdezési paramétereket](/graph/query-parameters)adja meg. *Ne adja meg* a teljes alap URL-címet vagy a http-műveletet, például, vagy `https://graph.microsoft.com/v1.0/security/alerts` a `GET` `PATCH` műveletet. Íme egy konkrét példa, amely a **riasztások beolvasása** művelet paramétereit jeleníti meg, ha nagy súlyosságú riasztásokkal rendelkező listát szeretne:

`Filter alerts value as Severity eq 'high'`

Az összekötővel használható lekérdezésekkel kapcsolatos további információkért tekintse meg a [Microsoft Graph biztonsági riasztások dokumentációját](/graph/api/alert-list). Az összekötővel folytatott továbbfejlesztett funkciók létrehozásával kapcsolatban további információt olvashat az összekötő által támogatott [séma-tulajdonságok riasztásokról](/graph/api/resources/alert) .

| Művelet | Leírás |
|--------|-------------|
| **Riasztások beolvasása** | Egy vagy több [riasztási tulajdonság](/graph/api/resources/alert)alapján szűrt riasztások lekérése, például: `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Riasztás beolvasása azonosító alapján** | Egy adott riasztást kap a riasztás azonosítója alapján. | 
| **Riasztás frissítése** | Egy adott riasztás frissítése a riasztás azonosítója alapján. A kérelemben szereplő kötelező és szerkeszthető tulajdonságok megadásához tekintse meg a [riasztások szerkeszthető tulajdonságait](/graph/api/alert-update). Ha például riasztást szeretne hozzárendelni egy biztonsági elemzőhöz, hogy el lehessen végezni a vizsgálatot, frissítheti a riasztáshoz **rendelt** tulajdonságot. |
|||

### <a name="manage-alert-subscriptions"></a>Riasztás-előfizetések kezelése

Microsoft Graph támogatja az [*előfizetéseket*](/graph/api/resources/subscription)vagy [*webhookokat*](/graph/api/resources/webhooks). Az előfizetések lekéréséhez, frissítéséhez vagy törléséhez adja meg a [Microsoft Graph által támogatott ODATA-lekérdezési paramétereket](/graph/query-parameters) a Microsoft Graph entitás-létrehozáshoz, és adja meg a `security/alerts` ODATA-lekérdezés által követett paramétereket. *Ne adja* meg az alap URL-címet, például: `https://graph.microsoft.com/v1.0` . Ehelyett használja az ebben a példában szereplő formátumot:

`security/alerts?$filter=status eq 'NewAlert'`

| Művelet | Leírás |
|--------|-------------|
| **Előfizetések létrehozása** | [Hozzon létre egy előfizetést](/graph/api/subscription-post-subscriptions) , amely értesítést küld a változásokról. Ezt az előfizetést szűrheti a kívánt riasztási típusokra. Létrehozhat például egy olyan előfizetést, amely értesíti a nagy súlyosságú riasztásokról. |
| **Aktív előfizetések beolvasása** | [Lejárt előfizetések beolvasása](/graph/api/subscription-list). | 
| **Előfizetés frissítése** | [Előfizetés frissítése](/graph/api/subscription-update) az előfizetés-azonosító megadásával. Az előfizetés kiterjesztéséhez például frissítheti az előfizetés `expirationDateTime` tulajdonságát. | 
| **Előfizetés törlése** | [Előfizetés törlése](/graph/api/subscription-delete) az előfizetés-azonosító megadásával. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Fenyegetési intelligencia-mutatók kezelése

A legutóbbi eredmények szűréséhez, rendezéséhez vagy beszerzéséhez *csak* a [Microsoft Graph által támogatott ODATA-lekérdezési paramétereket](/graph/query-parameters)adja meg. *Ne adja meg* a teljes alap URL-címet vagy a http-műveletet, például, vagy `https://graph.microsoft.com/beta/security/tiIndicators` a `GET` `PATCH` műveletet. Az alábbi példa egy olyan példát mutat be, amely a **tiIndicators beolvasása** művelet paramétereit jeleníti meg, ha olyan listát szeretne, amely tartalmazza a `DDoS` veszélyforrás típusát:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Az ezzel az összekötővel használható lekérdezésekkel kapcsolatos további információkért tekintse meg [a "választható lekérdezési paraméterek" című részt a Microsoft Graph biztonsági fenyegetésekkel kapcsolatos intelligencia mutatójának dokumentációjában](/graph/api/tiindicators-list). Az összekötővel folytatott továbbfejlesztett funkciók létrehozásával kapcsolatban további információt a [séma tulajdonságai által támogatott veszélyforrások felderítésére szolgáló kijelzőn](/graph/api/resources/tiindicator) olvashat.

| Művelet | Leírás |
|--------|-------------|
| **Veszélyforrások elleni intelligencia indikátorok beolvasása** | Egy vagy több [tiIndicator-tulajdonság](/graph/api/resources/tiindicator)alapján szűrt tiIndicators beolvasása, például: `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Veszélyforrások elleni intelligencia-jelző beolvasása azonosító alapján** | Egy adott tiIndicator beszerzése a tiIndicator-azonosító alapján. | 
| **Fenyegetési intelligencia kijelző létrehozása** | Hozzon létre egy új tiIndicator a tiIndicators gyűjteménybe való közzétételsel. A kérelemben szereplő szükséges tulajdonságok megadásához tekintse át a [tiIndicator létrehozásához szükséges tulajdonságokat](/graph/api/tiindicators-post). |
| **Több veszélyforrást érintő intelligencia-kijelző küldése** | Hozzon létre több új tiIndicators egy tiIndicators-gyűjtemény elküldésével. A kérelemben szereplő szükséges tulajdonságok megadásához tekintse át a [több tiIndicators elküldéséhez szükséges tulajdonságokat](/graph/api/tiindicator-submittiindicators). |
| **Veszélyforrások elleni intelligencia-kijelző frissítése** | Egy adott tiIndicator frissítése a tiIndicator azonosítója alapján. A kérelemben szereplő kötelező és szerkeszthető tulajdonságok megadásához tekintse meg a [tiIndicator szerkeszthető tulajdonságait](/graph/api/tiindicator-update). Ha például frissíteni szeretné az alkalmazandó műveletet, ha a kijelző a targetProduct biztonsági eszközön belül szerepel, akkor frissítheti a tiIndicator **művelet** tulajdonságát. |
| **Több veszélyforrást érintő intelligencia-kijelző frissítése** | Több tiIndicators frissítése. A kérelemben szereplő szükséges tulajdonságok megadásához tekintse át a [több tiIndicators frissítéséhez szükséges tulajdonságokat](/graph/api/tiindicator-updatetiindicators). |
| **Veszélyforrások intelligencia-mutatójának törlése azonosító alapján** | Egy adott tiIndicator törlése a tiIndicator azonosítója alapján. |
| **Több veszélyforrást érintő intelligencia-mutató törlése azonosító alapján** | Több tiIndicators törlése az azonosítóik alapján. A kérelemben szereplő szükséges tulajdonságok megadásához tekintse meg a [szükséges tulajdonságokat a több TiIndicators azonosító alapján történő törléséhez](/graph/api/tiindicator-deletetiindicators). |
| **Több veszélyforrást érintő intelligencia-kijelző törlése külső azonosítók alapján** | Több tiIndicators törlése a külső azonosítók alapján. A kérelemben szereplő szükséges tulajdonságok megadásához tekintse meg a [szükséges tulajdonságokat a külső azonosítók által használt több tiIndicators törléséhez](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/microsoftgraphsecurity/).

## <a name="next-steps"></a>Következő lépések

További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
