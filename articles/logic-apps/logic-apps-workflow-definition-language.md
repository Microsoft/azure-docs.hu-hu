---
title: Munkafolyamat-definíció nyelvi sémájának referenciája
description: Útmutató a JSON-sémához és a munkafolyamat-definíciós nyelv szintaxisához, amely leírja a munkafolyamatokat Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 71929cd449f4a00b91cc6c8620b33b0e0c6d506c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87078147"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Séma-útmutató a munkafolyamat-definíciós nyelvhez Azure Logic Apps

Amikor [Azure Logic Appsban](../logic-apps/logic-apps-overview.md)hoz létre logikai alkalmazást, a logikai alkalmazásnak van egy mögöttes munkafolyamat-definíciója, amely leírja a logikai alkalmazásban futó tényleges logikát. A munkafolyamat-definíció [JSON](https://www.json.org/) -t használ, és a munkafolyamat-definíció nyelvi sémája által érvényesített struktúrát követi. Ez a hivatkozás áttekintést nyújt erről a szerkezetről, valamint arról, hogy a séma hogyan határozza meg az attribútumokat a munkafolyamat-definícióban.

## <a name="workflow-definition-structure"></a>Munkafolyamat-definíciós struktúra

A munkafolyamat-definíció mindig tartalmaz egy eseményindítót a logikai alkalmazás létrehozásához, valamint egy vagy több olyan műveletet, amely az eseményindítót követően fut.

A munkafolyamat-definíciók magas szintű szerkezete:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attribútum | Kötelező | Leírás |
|-----------|----------|-------------|
| `definition` | Igen | A munkafolyamat-definíció kezdő eleme |
| `$schema` | Csak akkor, ha külsőleg hivatkozik egy munkafolyamat-definícióra | A munkafolyamat-definíció nyelvi verzióját leíró JSON-sémafájl helye, amelyet itt talál: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | A munkafolyamat-futtatókörnyezetben végrehajtandó egy vagy több művelet definíciói. További információ: [triggerek és műveletek](#triggers-actions). <p><p>Maximális műveletek: 250 |
| `contentVersion` | No | A munkafolyamat-definíció verziószáma, amely alapértelmezés szerint "1.0.0.0". A munkafolyamat telepítésekor a megfelelő definíció azonosításához és megerősítéséhez adja meg a használni kívánt értéket. |
| `outputs` | No | Egy munkafolyamat-futtatásból visszaadott kimenetek definíciói. További információt a [kimenetek](#outputs)című témakörben talál. <p><p>Maximális kimenetek: 10 |
| `parameters` | No | Egy vagy több olyan paraméter definíciója, amely átadja a logikai alkalmazás futtatókörnyezetében használni kívánt értékeket. További információ: [Paraméterek](#parameters). <p><p>Maximális paraméterek: 50 |
| `staticResults` | No | Egy vagy több művelet által visszaadott egy vagy több statikus eredmény definíciói, ha a statikus eredmények engedélyezve vannak ezeken a műveleteken. Az egyes műveletek definíciójában az `runtimeConfiguration.staticResult.name` attribútum a megfelelő definícióra hivatkozik belül `staticResults` . További információ: [statikus eredmények](#static-results). |
| `triggers` | No | Egy vagy több, a munkafolyamatot létrehozó eseményindító definíciói. Több trigger is megadható, de csak a munkafolyamat-definíciós nyelvvel, nem pedig vizuálisan a Logic Apps Designer használatával. További információ: [triggerek és műveletek](#triggers-actions). <p><p>Eseményindítók maximális száma: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Eseményindítók és műveletek

A munkafolyamatok definíciójában a `triggers` és a `actions` szakaszban a munkafolyamat végrehajtása során megjelenő hívásokat határozzák meg. Az ezekkel a részekkel kapcsolatos szintaxissal és további információkkal kapcsolatban lásd: [munkafolyamat-eseményindítók és-műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Paraméterek

Az üzembe helyezési életciklus általában különböző környezetekkel rendelkezik fejlesztési, tesztelési, előkészítési és éles környezetben. Ha különböző környezetekben helyez üzembe logikai alkalmazásokat, valószínűleg különböző értékeket kíván használni, például a kapcsolódási karakterláncokat a telepítési igények alapján. Vagy előfordulhat, hogy olyan értékeket is használhat, amelyeket a logikai alkalmazásban rögzítjük nélkül vagy a módosítást követően is használni szeretne. A munkafolyamat-definíció szakaszban megadhatja `parameters` vagy szerkesztheti azokat az értékeket, amelyeket a logikai alkalmazás futásidőben használ. Először ezeket a paramétereket kell megadnia, mielőtt a munkafolyamat-definícióban máshol is hivatkozhat ezekre a paraméterekre.

A paraméterek definíciójának általános szerkezete:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Attribútum | Kötelező | Típus | Description |
|-----------|----------|------|-------------|
| <*paraméter – név*> | Igen | Sztring | A definiálni kívánt paraméter neve |
| <*paraméter típusa*> | Yes | int, float, string, bool, Array, Object, SecureString, secureobject <p><p>**Megjegyzés**: az összes jelszóhoz, kulcshoz és titokhoz használja a `securestring` vagy a `secureobject` típust, mert a `GET` művelet nem ad vissza ilyen típusokat. A paraméterek biztonságossá tételével kapcsolatos további információkért lásd: [biztonsági javaslatok a műveletekhez és a bemeneti paraméterekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | A paraméter típusa |
| <*default-paraméter-érték*> | Yes | Ugyanaz, mint `type` | Az alapértelmezett paraméterérték, amelyet akkor kell használni, ha nincs megadva érték a munkafolyamat példányainak létrehozásakor. Az `defaultValue` attribútum megadása kötelező, hogy a Logic app Designer helyesen tudja megjeleníteni a paramétert, de megadhat egy üres értéket. |
| <*tömb – engedélyezett – paraméter-értékek*> | No | Tömb | Egy tömb, amely a paraméter által elfogadható értékekkel rendelkezik |
| <*paraméter – Leírás*> | No | JSON-objektum | Egyéb paraméterek részletei, például a paraméter leírása |
||||

Ezután hozzon létre egy [Azure Resource Manager sablont](../azure-resource-manager/templates/overview.md) a munkafolyamat-definícióhoz, definiálja a központi telepítésben használni kívánt értékeket elfogadó sablon paramétereit, cserélje le a hardcoded értékeket a sablonra vagy a munkafolyamat-definíció paramétereinek megfelelő értékekre, és tárolja a telepítéskor használandó értékeket egy külön [paraméter fájljában](../azure-resource-manager/templates/parameter-files.md). Így a logikai alkalmazás frissítése és újbóli üzembe helyezése nélkül is könnyebben módosíthatja ezeket az értékeket a paraméter fájlján keresztül. A bizalmas vagy biztonságossá tett információk, például a felhasználónevek, a jelszavak és a titkos kulcsok esetében Azure Key Vault tárolhatja ezeket az értékeket, és a paraméter-fájllal lekérheti ezeket az értékeket a kulcstartóból. További információk és példák a paraméterek definiálására a sablon és a munkafolyamat-definíció szintjein: [Áttekintés: az üzembe helyezés automatizálása a Logic apps alkalmazásokhoz Azure Resource Manager-sablonokkal](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

<a name="static-results"></a>

## <a name="static-results"></a>Statikus eredmények

Az `staticResults` attribútumban Definiáljon egy művelet modelljét, `outputs` és `status` azt, hogy a művelet akkor adja vissza, ha a művelet statikus eredményhalmaz beállítása be van kapcsolva. A művelet definíciójában az `runtimeConfiguration.staticResult.name` attribútum a statikus eredmény definíciójának nevére hivatkozik `staticResults` . Megtudhatja, hogyan [tesztelheti a Logic apps-alkalmazásokat a statikus eredmények beállításával](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attribútum | Kötelező | Típus | Description |
|-----------|----------|------|-------------|
| <*statikus – eredmény-definíció – név*> | Igen | Sztring | A statikus eredmény definíciójának neve, amelyet a műveleti definíció hivatkozhat egy `runtimeConfiguration.staticResult` objektumon keresztül. További információ: [futásidejű konfigurációs beállítások](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Bármilyen egyedi nevet használhat. Alapértelmezés szerint ez az egyedi név egy számmal van hozzáfűzve, amely szükség szerint növekszik. |
| <*output-attributes-and-Values – visszaadott*> | Yes | Változó | Az attribútumokra vonatkozó követelmények eltérő feltételek alapján változnak. Ha például a `status` értéke `Succeeded` , az attribútum a `outputs` művelet által az ál-kimenetként visszaadott attribútumokat és értékeket tartalmazza. Ha a `status` értéke `Failed` , az `outputs` attribútum tartalmazza az `errors` attribútumot, amely egy vagy több hibás információt tartalmazó tömb `message` . |
| <*fejléc – értékek*> | Nem | JSON | A művelet által visszaadott fejléc-értékek |
| <*állapot-kód – visszaadott*> | Igen | Sztring | A művelet által visszaadott állapotkód |
| <*művelet – állapot*> | Igen | Sztring | A művelet állapota, például `Succeeded` vagy `Failed` |
|||||

Ebben a HTTP-művelet definíciójában például az `runtimeConfiguration.staticResult.name` attribútum azon `HTTP0` attribútumon belül hivatkozik, ahol a `staticResults` művelethez tartozó modell-kimenetek definiálva vannak. Az `runtimeConfiguration.staticResult.staticResultOptions` attribútum azt adja meg, hogy a statikus eredmény beállítás a `Enabled` http-műveleten van.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

A HTTP-művelet a definíción belüli kimeneteket adja vissza `HTTP0` `staticResults` . Ebben a példában az állapotkód esetében a modell kimenete a következő: `OK` . A fejléc értékeinél a modell kimenete a következő: `"Content-Type": "application/JSON"` . A művelet állapotához a modell kimenete a következő: `Succeeded` .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Kifejezések

A JSON használatával olyan literál értékekkel rendelkezhet, amelyek a tervezési időszakban léteznek, például:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Olyan értékekkel is rendelkezhet, amelyek nem léteznek futási időben. Az értékek megjelenítéséhez használhat *kifejezéseket*, amelyeket a rendszer futási időben értékel ki. A kifejezés olyan sorozatot mutat be, amely egy vagy több [függvényt](#functions), [operátort](#operators), [változót](./logic-apps-create-variables-store-values.md), explicit értéket vagy állandót tartalmazhat. A munkafolyamat-definícióban egy JSON-karakterlánc értékében bárhol használhatja a kifejezést a (z) jellel ( \@ ). Egy JSON-értéket jelölő kifejezés kiértékelése során a kifejezés törzsét Kinyeri a karakter eltávolításával \@ , és mindig egy másik JSON-értéket ad eredményként.

Például a korábban definiált tulajdonság esetében a `customerName` tulajdonság értékét egy kifejezés [Parameters ()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) függvényének használatával szerezheti be, és hozzárendelheti az értéket a `accountName` tulajdonsághoz:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

A *karakterlánc-interpolációval* több kifejezés is használható a karakterláncokban, amelyek a \@ karakter-és kapcsos zárójelek () szerint vannak becsomagolva {} . A szintaxis a következő:

```json
@{ "<expression1>", "<expression2>" }
```

Az eredmény mindig karakterlánc, így a funkció hasonló a `concat()` függvényhez, például: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Ha olyan literál sztringet tartalmaz, amely a \@ karakterrel kezdődik, a \@ karaktert egy másik \@ karakterrel együtt Escape-karakterként kell előtagként megjelölni: \@\@

Ezek a példák a kifejezések kiértékelésének módját mutatják be:

| JSON-érték | Eredmény |
|------------|--------|
| "Sophia Owen" | A következő karakterek visszaadása: "Sophia Owen" |
| "tömb [1]" | A következő karakterek visszaadása: "Array [1]" |
| "\@\@" | A karaktereket a következő karakterként kell visszaadni: " \@ " |
| " \@" | A karaktereket a következő két karakterből álló karakterláncként kell visszaadni: " \@ " |
|||

Ezen példák esetében tegyük fel, hogy a "myBirthMonth" érték "január" és "myAge" értékkel egyenlő, mint a 42-as szám:

```json
"myBirthMonth": "January",
"myAge": 42
```

Ezek a példák a következő kifejezések kiértékelésének módját mutatják be:

| JSON-kifejezés | Eredmény |
|-----------------|--------|
| " \@ Parameters (' myBirthMonth ')" | A következő sztring visszaadása: "január" |
| " \@ {Parameters (' myBirthMonth ')}" | A következő sztring visszaadása: "január" |
| " \@ Parameters (' myAge ')" | A következő szám visszaadása: 42 |
| " \@ {Parameters (' myAge ')}" | A szám visszaadása karakterláncként: "42" |
| "Az életkorom \@ {Parameters (' myAge ')}) | A következő sztring visszaadása: "My Age is 42" |
| " \@ concat (' My Age is ', string (parameters (' myAge '))) | A következő sztring visszaadása: "My Age is 42" |
| "Az életkorom \@ \@ {Parameters (' myAge ')}) | Adja vissza ezt a karakterláncot, amely tartalmazza a következő kifejezést: "a My Age értéke \@ {Parameters (' myAge ')} ' |
|||

Amikor vizuálisan dolgozik a Logic Apps Designerben, létrehozhat kifejezéseket a Kifejezésszerkesztő használatával, például:

![Logic Apps Designer > Expression Builder](./media/logic-apps-workflow-definition-language/expression-builder.png)

Ha elkészült, a kifejezés megjelenik a munkafolyamat-definícióban található megfelelő tulajdonsághoz, például a `searchQuery` tulajdonság itt:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>Kimenetek

A `outputs` szakaszban adja meg a munkafolyamat által visszaadott adatait, amikor a rendszer készen áll. Ha például egy adott állapotot vagy értéket szeretne nyomon követni az egyes futtatásokból, adja meg, hogy a munkafolyamat kimenete az adatokat adja vissza.

> [!NOTE]
> Ha a szolgáltatás REST API érkező kérésekre válaszol, ne használja `outputs` . Ehelyett használja a `Response` művelet típusát. További információ: [munkafolyamat-eseményindítók és-műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md).

A kimenet definíciójának általános szerkezete:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attribútum | Kötelező | Típus | Description |
|-----------|----------|------|-------------|
| <*kulcs neve*> | Igen | Sztring | A kimenet visszatérési értékének kulcsa |
| <*kulcs típusa*> | Yes | int, float, string, SecureString, bool, Array, JSON objektum | A kimenet visszatérési értékének típusa |
| <*kulcs-érték*> | Yes | Ugyanaz, mint <*Key-Type*> | A kimenet visszatérési értéke |
|||||

Egy munkafolyamat-Futtatás kimenetének lekéréséhez tekintse át a logikai alkalmazás futtatási előzményeit és részleteit a Azure Portal, vagy használja a [munkafolyamat-REST API](/rest/api/logic/workflows). Külső rendszereknek is átadhatja a kimenetet, például Power BI, hogy irányítópultokat lehessen létrehozni.

<a name="operators"></a>

## <a name="operators"></a>Operátorok

A [kifejezésekben](#expressions) és [függvényekben](#functions)a kezelők adott feladatokat hajtanak végre, például egy tulajdonságra vagy egy tömbben lévő értékre hivatkoznak.

| Operátor | Feladat |
|----------|------|
| ' | Ha karakterláncot vagy kifejezéseket és függvényeket szeretne használni, akkor a sztringet csak egyszeres idézőjelek közé kell becsomagolni, például: `'<myString>'` . Ne használjon idézőjeleket (""), amelyek ütköznek a teljes kifejezés JSON-formázásával. Például: <p>**Igen**: hossz ("Hello") </br>**Nem**: hossz ("helló") <p>Tömbök vagy számok átadásakor nincs szükség a tördelési írásjelekre. Például: <p>**Igen**: hossz ([1, 2, 3]) </br>**Nem**: hossz ("[1, 2, 3]") |
| [] | Ha egy tömbben egy adott pozíció (index) értékére szeretne hivatkozni, szögletes zárójeleket kell használnia. Például egy tömb második elemének lekéréséhez: <p>`myArray[1]` |
| . | Egy objektum egy tulajdonságára való hivatkozáshoz használja a dot operátort. Például `name` egy JSON-objektum tulajdonságának beszerzéséhez `customer` : <p>`"@parameters('customer').name"` |
| ? | Ha futásidejű hibát nem tartalmazó objektumban szeretne null tulajdonságokat hivatkozni, használja a kérdőjel operátort. Ha például egy triggerből null kimenetet szeretne kezelni, ezt a kifejezést használhatja: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

Egyes kifejezések olyan futtatókörnyezeti műveletek értékeit kapják meg, amelyek esetleg még nem léteznek, amikor a munkafolyamat-definíció elindul. A kifejezésekben szereplő értékek hivatkozásához és használatához használhatja a munkafolyamat-definíciós nyelv által biztosított [*függvényeket*](../logic-apps/workflow-definition-language-functions-reference.md) .

## <a name="next-steps"></a>Következő lépések

* A [munkafolyamat-definíció nyelvi műveleteinek és eseményindítóinak](../logic-apps/logic-apps-workflow-actions-triggers.md) megismerése
* Ismerje meg, hogyan hozhat létre és kezelhet Logic apps-alkalmazásokat a [Munkafolyamattal REST API](/rest/api/logic/workflows)
