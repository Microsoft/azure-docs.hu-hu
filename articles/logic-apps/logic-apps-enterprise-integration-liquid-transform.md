---
title: A JSON és az XML konvertálása folyékony sablonokkal
description: A JSON és az XML átalakítása folyékony sablonok használatával Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94992709"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>A JSON és az XML átalakítása folyékony sablonokkal Azure Logic Apps

Ha alapszintű JSON-átalakításokat szeretne végrehajtani a logikai alkalmazásokban, használhat natív [adatműveleteket](../logic-apps/logic-apps-perform-data-operations.md) , például a JSON **összeállítását** vagy **elemzését**. A speciális és összetett JSON olyan JSON-átalakításokhoz, amelyek olyan elemekkel rendelkeznek, mint például az ismétlések, a vezérlési folyamatok és a változók, létrehozhatnak és használhatnak olyan sablonokat, amelyek ezeket az átalakításokat a [folyékony](https://shopify.github.io/liquid/) nyílt forráskódú sablon nyelvének használatával írják le. [Más átalakításokat is végrehajthat](#other-transformations), például a JSON-t szövegre, XML-ről JSON-ra, és XML-ről szövegre.

A logikai alkalmazásban a folyékony átalakítás elvégzéséhez először létre kell hoznia egy olyan likvid sablont, amely meghatározza a kívánt leképezést. Ezután [feltöltheti a sablont](../logic-apps/logic-apps-enterprise-integration-maps.md) az [integrációs fiókjába](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Ha hozzáadja a **JSON-t a JSON-Liquid** művelethez a logikai alkalmazáshoz, akkor a használni kívánt művelet leképezése kiválaszthatja a folyadék sablont.

Ez a cikk bemutatja, hogyan hajthatja végre ezeket a feladatokat:

* Hozzon létre egy likvid sablont.
* Adja hozzá a sablont az integrációs fiókhoz.
* Adja hozzá a folyékony átalakítás műveletet a logikai alkalmazáshoz.
* Válassza ki a sablont a használni kívánt térképként.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Alapvető ismeretek a [folyékony sablon nyelvéről](https://shopify.github.io/liquid/)

  > [!NOTE]
  > A **JSON átalakítása JSON-Liquid** művelet a [DotLiquid implementációját](https://github.com/dotliquid/dotliquid)követi a folyadék esetében, ami eltér a [Shopify megvalósításának](https://shopify.github.io/liquid)bizonyos eseteitől. További információ: [likvid sablonokkal kapcsolatos megfontolások](#liquid-template-considerations).

## <a name="create-the-template"></a>A sablon létrehozása

1. Hozza létre a JSON-átalakításhoz leképezésként használt likvid sablont. Bármilyen szerkesztési eszközt használhat.

   Ebben a példában hozza létre a minta likvid sablont a jelen szakaszban leírtak szerint:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Mentse a sablont a bővítmény használatával `.liquid` . Ez a példa a következőt használja: `SimpleJsonToJsonTemplate.liquid`.

## <a name="upload-the-template"></a>A sablon feltöltése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Azure Portal a keresőmezőbe írja be a kifejezést `integration accounts` , majd válassza az **integrációs fiókok** elemet.

   !["Integrációs fiókok" keresése](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Keresse meg és válassza ki az integrációs fiókját.

   ![Integrációs fiók kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Az **Áttekintés** ablaktábla **összetevők** területén válassza a **Maps** elemet.

    ![A "Maps" csempe kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. A **térképek** ablaktáblán válassza a **Hozzáadás** lehetőséget, és adja meg a térképen a következő adatokat:

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | `JsonToJsonTemplate` | A Térkép neve, amely ebben a példában "JsonToJsonTemplate". |
   | **Térkép típusa** | **folyékony** | A Térkép típusa A JSON-ből a JSON-átalakításhoz ki kell választania a **folyadék** elemet. |
   | **Térkép** | `SimpleJsonToJsonTemplate.liquid` | Ebben a példában a "SimpleJsonToJsonTemplate. Liquid" nevű meglévő likvid sablon vagy leképezési fájl, amely az átalakításhoz használható. A fájl megkereséséhez használhatja a fájl-választót. A Térkép méretének korlátaival kapcsolatban lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Likvid sablon hozzáadása](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>A folyékony átalakítási művelet hozzáadása

1. A Azure Portal az alábbi lépéseket követve [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A Logic app Designerben adja hozzá a [kérelem-triggert](../connectors/connectors-native-reqres.md#add-request) a logikai alkalmazáshoz.

1. Az trigger alatt válassza az **új lépés** lehetőséget. A keresőmezőbe írja be szűrőként a kifejezést, `liquid` majd válassza a következő műveletet: **JSON átalakítása JSON-Liquid**

   ![Likvid művelet keresése és kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Nyissa meg a **Térkép** listát, és válassza ki a folyékony sablont, amely ebben a példában a "JsonToJsonTemplate".

   ![Térkép kiválasztása](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Ha a térképek lista üres, valószínűleg a logikai alkalmazás nem kapcsolódik az integrációs fiókhoz. 
   Ha a logikai alkalmazást a folyékony sablonnal vagy a leképezéssel rendelkező integrációs fiókhoz szeretné kapcsolni, kövesse az alábbi lépéseket:

   1. A logikai alkalmazás menüjében válassza a **munkafolyamat-beállítások** lehetőséget.

   1. Az **integrációs fiók kiválasztása** listából válassza ki az integrációs fiókot, majd kattintson a **Mentés** gombra.

      ![Logikai alkalmazás összekapcsolása integrációs fiókkal](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Most adja hozzá a **Content** tulajdonságot ehhez a művelethez. Nyissa meg az **új paraméter hozzáadása** listát, és válassza a **tartalom** elemet.

   ![A "Content" tulajdonság hozzáadása művelethez](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. A **tartalom** tulajdonság értékének megadásához kattintson a **szövegmezőbe** , hogy megjelenjen a dinamikus tartalmak listája. Válassza ki a **törzs** jogkivonatát, amely a törzs tartalom kimenetét jelöli az triggerből.

   ![Válassza ki a "szövegtörzs" tokent a "Content" tulajdonság értékéhez](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Ha elkészült, a művelet a következő példához hasonlít:

   ![Befejeződött a "JSON átalakítása JSON-re" művelet.](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A [Poster](https://www.getpostman.com/postman) vagy egy hasonló eszköz használatával JSON-bevitelt küldhet a logikai alkalmazásba. Az átalakított JSON-kimenet a logikai alkalmazásból a következő példához hasonlóan néz ki:

![Példa kimenetre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>A likvid sablonokkal kapcsolatos megfontolások

* A likvid sablonok a Azure Logic Apps- [térképek fájlméretének korlátozásait](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) követik.

* A **JSON átalakítása JSON-Liquid** művelet a [DotLiquid implementációját követi a folyadékban](https://github.com/dotliquid/dotliquid). Ez a implementáció a .NET-keretrendszer egyik portja a [Shopify megvalósítása a folyékony](https://shopify.github.io/liquid/) környezethez, és [bizonyos esetekben](https://github.com/dotliquid/dotliquid/issues)eltér.

  Itt láthatók az ismert különbségek:

  * A **JSON átalakítása JSON-Liquid** művelet natív módon kimenetbe helyez egy karakterláncot, amely tartalmazhatja a JSON-t, az XML-t, a HTML-t és így tovább. A likvid művelet csak azt jelzi, hogy a folyadék sablonjának várt szöveges kimenete JSON-karakterlánc. A művelet arra utasítja a logikai alkalmazást, hogy a bevitelt JSON-objektumként elemezze, és alkalmazza a burkolót, hogy a folyadék értelmezze a JSON-struktúrát. Az átalakítást követően a művelet arra utasítja a logikai alkalmazást, hogy elemezze a folyadékból a szöveg kimenetét a JSON formátumba.

    A DotLiquid nem ismeri natív módon a JSON-t, ezért ügyeljen arra, hogy elkerülje a fordított perjel karaktert ( `\` ) és minden más fenntartott JSON-karaktert.

  * Ha a sablon [folyadék-szűrőket](https://shopify.github.io/liquid/basics/introduction/#filters)használ, ügyeljen arra, hogy kövesse a [DotLiquid és a C# elnevezési konvenciókat](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), amelyek a *mondatot* használják. Az összes likvid átalakító esetében ügyeljen arra, hogy a sablonban lévő szűrők nevei a mondatot is használják. Ellenkező esetben a szűrők nem működnek.

    A szűrő használatakor például a következőt `replace` használja: `Replace` `replace` . Ugyanez a szabály vonatkozik arra az esetre, ha a [DotLiquid online](http://dotliquidmarkup.org/try-online)-ban kipróbál példákat. További információ: [Shopify Liquid Filters](https://shopify.dev/docs/themes/liquid/reference/filters) and [DotLiquid Liquid Filters](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). A Shopify-specifikáció példákat tartalmaz az egyes szűrőkhöz, így összehasonlítás céljából kipróbálhatja ezeket a példákat a [DotLiquid-Try online](http://dotliquidmarkup.org/try-online)szolgáltatásban.

  * A `json` Shopify-bővítmény szűrőinek szűrője jelenleg [nincs implementálva a DotLiquid-ben](https://github.com/dotliquid/dotliquid/issues/384). Ezt a szűrőt általában a JSON-karakterláncok elemzésére szolgáló szöveg kimenetének előkészítésére használhatja, ehelyett inkább a szűrőt kell használnia `Replace` .

  * A `Replace` [DotLiquid implementációjában](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) lévő standard szűrő [reguláris kifejezés (regex) egyeztetést](/dotnet/standard/base-types/regular-expression-language-quick-reference)használ, míg a [Shopify implementáció](https://shopify.github.io/liquid/filters/replace/) [egyszerű karakterlánc-egyeztetést](https://github.com/Shopify/liquid/issues/202)használ. Úgy tűnik, hogy mindkét implementáció ugyanúgy működik, amíg nem használ egy RegEx számára fenntartott karaktert vagy Escape-karaktert a Match paraméterben.

    Ha például meg szeretné menekülni a RegEx által lefoglalt fordított perjel ( `\` ) Escape-karaktert, használja a kifejezést `| Replace: '\\', '\\'` , és ne `| Replace: '\', '\\'` . Ezek a példák azt mutatják be, hogyan viselkedik a `Replace` szűrő másképp, amikor megpróbálja elmenekülni a fordított perjel karakterből. A verzió sikeresen működik:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Ezzel az eredménnyel:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Ez a verzió nem sikerül:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Ezzel a hibával:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    További információkért lásd: a [standard Filter helyettesítése regex-mintázattal egyező..](https://github.com/dotliquid/dotliquid/issues/385).

  * A `Sort` [DotLiquid implementációban](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) lévő szűrő a tömb vagy gyűjtemény elemeire rendezi a tulajdonságot, de ezekkel a különbségekkel:<p>

    * A [Shopify sort_natural viselkedését](https://shopify.github.io/liquid/filters/sort_natural/)követi, nem a [Shopify rendezési viselkedését](https://shopify.github.io/liquid/filters/sort/).

    * A rendezés csak karakterlánc-alfanumerikus sorrendben történik. További információ: [numerikus rendezés](https://github.com/Shopify/liquid/issues/980).

    * A *kis-* és nagybetűket nem megkülönböztető sorrendet használ, nem pedig a kis-és nagybetűket. További információ: a [rendezési szűrő nem követi a Shopify specifikációjában a burkolat viselkedését]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Más átalakítások folyadék használatával

A folyadék nem korlátozódik kizárólag a JSON-átalakításokra. A folyadékot más átalakítások végrehajtására is használhatja, például:

* [JSON-szöveg](#json-text)
* [XML – JSON](#xml-json)
* [XML – szöveg](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>JSON átalakítása szöveggé

Itt látható a példaként használt likvid sablon:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Íme a minta bemenetek és kimenetek:

![Példa kimeneti JSON-szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>XML átalakítása JSON-formátumba

Itt látható a példaként használt likvid sablon:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

A `JSONArrayFor` hurok egy egyéni hurkos mechanizmus az XML-bemenetekhez, így olyan JSON-adattartalom hozható létre, amely megakadályozza a záró vesszőt. Emellett az `where` Egyéni hurkos mechanizmus feltétele az XML-elem nevét is használja az összehasonlításhoz, nem pedig az elem értékét, mint más folyadék-szűrőket. További információ: [a set-Body Policy részletes bemutatása – a dolgok gyűjteménye](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Íme a minta bemenetek és kimenetek:

![Példa kimeneti XML-ből JSON-ra](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>XML átalakítása szöveggé

Itt látható a példaként használt likvid sablon:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Íme a minta bemenetek és kimenetek:

![Példa kimeneti XML-szövegre](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Következő lépések

* [Shopify-alapú folyékony nyelv és példák](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid – online kipróbálás](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub-problémák](https://github.com/dotliquid/dotliquid/issues/)
* További információ a [mapsről](../logic-apps/logic-apps-enterprise-integration-maps.md)
