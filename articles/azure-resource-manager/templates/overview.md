---
title: Sablonok – Áttekintés
description: A Azure Resource Manager-sablonok (ARM-sablonok) használatának előnyeit ismerteti az erőforrások üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 14bcbbd7a7ae7315dbb8e9d3e7e44ce0ffe0a4b4
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419983"
---
# <a name="what-are-arm-templates"></a>Mik azok az ARM-sablonok?

A felhőbe való áttéréssel számos csapat agilis fejlesztési módszereket fogadott el. Ezek a csapatok gyorsan megismétlik. A megoldásoknak többször is üzembe kell helyezniük a felhőben, és tudniuk kell, hogy az infrastruktúra megbízható állapotban van. Mivel az infrastruktúra az iterációs folyamat részévé vált, a műveletek és a fejlesztés közötti felosztás eltűnt. A csapatoknak egységes folyamaton keresztül kell kezelniük az infrastruktúrát és az alkalmazás kódját.

Ezeknek a kihívásoknak a kielégítéséhez automatizálhatja az üzembe helyezéseket, és használhatja az infrastruktúra mint kód gyakorlatát. A kódban definiálni kell a telepítendő infrastruktúrát. Az infrastruktúra kódja a projekt részévé válik. Az alkalmazás kódjához hasonlóan az infrastruktúra kódját is tárolhatja egy forrás adattárban, és annak verzióját is. A csapatának bármelyike futtathatja a kódot, és telepíthet hasonló környezeteket is.

Az infrastruktúra kódként való megvalósításához az Azure-megoldásokhoz használja Azure Resource Manager-sablonokat (ARM-sablonok). A sablon egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon olyan deklaratív szintaxist használ, amellyel anélkül határozhatja meg, hogy mit szeretne üzembe helyezni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A sablonban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

Új nyelvet vezettünk be az ARM-sablonok fejlesztéséhez. A nyelv neve bicep, és jelenleg előzetes verzióban érhető el. A bicep és a JSON-sablonok ugyanazokat a képességeket kínálnak. A sablont a két nyelv között alakíthatja át. A bicep olyan szintaxist biztosít, amely könnyebben használható a sablonok létrehozásához. További információ: [Mi az a bicep (előzetes verzió)?](bicep-overview.md).

Ha szeretné megtudni, hogyan kezdheti meg az ARM-sablonokat, tekintse meg az alábbi videót.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>Miért érdemes ARM-sablonokat választani?

Ha az ARM-sablonok és a Code Services egyik másik infrastruktúrája között próbál dönteni, vegye figyelembe a következő előnyöket a sablonok használatával kapcsolatban:

* **Deklaratív szintaxis**: az ARM-sablonok lehetővé teszik, hogy teljes körű Azure-infrastruktúrát hozzon létre és helyezzen üzembe. Telepítheti például nem csak a virtuális gépeket, hanem a hálózati infrastruktúrát, a tárolási rendszereket és minden más szükséges erőforrást is.

* **Ismételhető eredmények**: az infrastruktúra ismételt üzembe helyezése a fejlesztési életciklus során, és az erőforrások megbízhatóságának egységes módon történő üzembe helyezése. A sablonok idempotens, ami azt jelenti, hogy több alkalommal is telepítheti ugyanazt a sablont, és ugyanazokat az erőforrásokat ugyanaz az állapot teszi elérhetővé. Létrehozhat egy olyan sablont, amely a kívánt állapotot jelöli, nem pedig sok különálló sablon kidolgozását a frissítések megjelenítéséhez.

* Előkészítés **: nem** kell aggódnia a megrendelési műveletek bonyolultságával kapcsolatban. A Resource Manager összehangolja az egymástól függő erőforrások telepítését, hogy azok a megfelelő sorrendben jöjjenek létre. Ha lehetséges, a Resource Manager párhuzamosan helyezi üzembe az erőforrásokat, így az üzemelő példányok gyorsabban futnak, mint a soros központi telepítések. A sablont egyetlen parancs használatával helyezheti üzembe, nem pedig több, felszólító parancs használatával.

   ![Template deployment összehasonlítása](./media/overview/template-processing.png)

* **Moduláris fájlok**: a sablonokat felbonthatja kisebb, újrafelhasználható összetevőkre, és összekapcsolhatja őket az üzembe helyezés ideje alatt. Egy sablont egy másik sablonba is beágyazhat.

* **Hozzon létre egy Azure-erőforrást**: azonnal használhatja a sablonok új Azure-szolgáltatásait és szolgáltatásait. Amint egy erőforrás-szolgáltató új erőforrásokat vezet be, ezeket az erőforrásokat sablonokon keresztül is telepítheti. Az új szolgáltatások használata előtt nem kell megvárnia, hogy az eszközök és modulok frissítése megtörténjen.

* **Bővíthetőség**: az [üzembe helyezési szkriptekkel](deployment-script-template.md)PowerShell-vagy bash-parancsfájlokat adhat hozzá a sablonokhoz. Az üzembe helyezési parancsfájlok kibővítik az erőforrások beállításának lehetőségét az üzembe helyezés során. A sablon tartalmazhatja a sablont, vagy külső forrásban tárolhatja, és hivatkozhat a sablonban. Az üzembe helyezési parancsfájlok lehetővé teszi a végpontok közötti környezet beállításának befejezését egyetlen ARM-sablonban.

* **Tesztelés**: gondoskodjon arról, hogy a sablon az ajánlott irányelveket követi az ARM-sablon eszközzel (ARM-TTK) való teszteléssel. Ez a teszt-készlet egy PowerShell-szkript, amelyet letöltheti a [githubról](https://github.com/Azure/arm-ttk). Az eszközkészlet megkönnyíti a szaktudás fejlesztését a sablon nyelvének használatával.

* **Előnézeti változások**: a [mi-if művelettel](template-deploy-what-if.md) beolvashatja a módosításokat a sablon telepítése előtt. Mi a teendő, hogy milyen erőforrásokat fog létrehozni, frissíteni vagy törölni, valamint minden olyan erőforrás-tulajdonságot, amely módosítva lesz. A mi-if művelet ellenőrzi a környezet aktuális állapotát, és kiküszöböli az állapot felügyeletének szükségességét.

* **Beépített ellenőrzés**: a rendszer csak az érvényesítést követően telepíti a sablont. A Resource Manager a telepítés megkezdése előtt ellenőrzi a sablont, hogy a telepítés sikeres legyen-e. Az üzembe helyezés kevésbé valószínű, hogy egy félig kész állapotban leáll.

* **Követett központi telepítések**: a Azure Portal áttekintheti az üzembe helyezési előzményeket, és információkat kaphat a sablon telepítéséről. Megtekintheti a telepített sablont, az átadott paramétereket és a kimeneti értékeket. Más infrastruktúrát, mint a Code Services a portálon keresztül nem követik nyomon.

   ![Üzembe helyezési előzmények](./media/overview/deployment-history.png)

* Szabályzat **mint kód**: [Azure Policy](../../governance/policy/overview.md) a szabályozás automatizálására szolgáló szabályzat. Ha Azure-szabályzatokat használ, a szabályzat szervizelése a nem megfelelő erőforrásokon történik a sablonokkal való üzembe helyezéskor.

* **Üzembe helyezési tervezetek**: kihasználhatja a Microsoft által a szabályozási és megfelelőségi követelmények teljesítéséhez biztosított [tervrajzok](../../governance/blueprints/overview.md) előnyeit. Ezek a tervrajzok előre elkészített sablonokat is tartalmaznak a különböző architektúrák esetében.

* **CI/CD-integráció**: a sablonokat integrálhatja a folyamatos integrációs és folyamatos üzembe helyezési (CI/CD) eszközökbe, amelyekkel automatizálhatja a kiadási folyamatokat a gyors és megbízható alkalmazás-és infrastruktúra-frissítésekhez. Az Azure DevOps és a Resource Manager-sablon feladatának használatával az Azure-folyamatok folyamatosan építhetnek ki és helyezhetnek üzembe ARM-sablonok projektjeit. További információért lásd: [a vs projekt folyamatokkal](add-template-to-azure-pipelines.md) és [oktatóanyaggal: Azure Resource Manager sablonok folyamatos integrálása az Azure-folyamatokkal](./deployment-tutorial-pipeline.md).

* **Exportálható kód**: egy meglévő erőforráscsoport sablonjának lekéréséhez exportálja az erőforráscsoport aktuális állapotát, vagy tekintse meg az adott központi telepítéshez használt sablont. Az [exportált sablon](export-template-portal.md) megtekintése hasznos módszer a sablon szintaxisának megismerésére.

* Szerzői **eszközök**: a sablonok a [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) és a Template Tool bővítménnyel hozhatók létre. Az IntelliSense, a szintaxis kiemelése, a beágyazott Súgó és számos más nyelvi funkció is elérhető. A Visual Studio Code mellett a [Visual studiót](create-visual-studio-deployment-project.md)is használhatja.

## <a name="template-file"></a>Sablonfájl

A sablonon belül olyan sablon- [kifejezéseket](template-expressions.md) írhat, amelyek kibővítik a JSON képességeit. Ezek a kifejezések a Resource Manager által biztosított [függvények](template-functions.md) használatát teszik elérhetővé.

A sablon a következő résszel rendelkezik:

* [Paraméterek](template-parameters.md) – az üzembe helyezés során értékeket adhat meg, amelyek lehetővé teszik, hogy ugyanaz a sablon más környezetekben is használható legyen.

* [Változók](template-variables.md) – a sablonokban újra felhasználható értékek definiálása. Ezek a paraméterek értékeiből állíthatók össze.

* [Felhasználó által definiált függvények](template-user-defined-functions.md) – a sablont leegyszerűsítő testreszabott függvények létrehozása.

* [Erőforrások](resource-declaration.md) – a telepítendő erőforrások meghatározása.

* [Kimenetek](template-outputs.md) – az üzembe helyezett erőforrások értékeit adja vissza.

## <a name="template-deployment-process"></a>Template deployment folyamat

Sablon központi telepítésekor a Resource Manager átalakítja a sablont REST API műveletekre. Például amikor a Resource Manager megkap egy sablont, amely a következő erőforrás-definíciót tartalmazza:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Átalakítja a definíciót a következő REST API-műveletté, amelyet a rendszer elküld a Microsoft.Storage erőforrás-szolgáltatónak:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Figyelje meg, hogy az erőforrás sablonjában beállított **apiVersion** a REST-művelet API-verziójaként szolgál. A sablon többször is üzembe helyezhető, és biztos lehet abban, hogy továbbra is működni fog. Ha ugyanazt az API-verziót használja, nem kell aggódnia a későbbi verziókban esetlegesen bevezetett változtatások miatt.

Sablon üzembe helyezéséhez használja a következő lehetőségek bármelyikét:

* [Azure Portal](deploy-portal.md)
* [Azure CLI](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST API](deploy-rest.md)
* [Gomb a GitHub-tárházban](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>Sablon kialakítása

A sablonok és erőforráscsoportok meghatározási módja teljes mértékben Öntől függ, illetve attól, hogyan szeretné kezelni a megoldást. Például egyetlen sablon segítségével üzembe helyezheti a háromszintű alkalmazását egyetlen erőforráscsoportra.

![háromrétegű sablon](./media/overview/3-tier-template.png)

Azonban nem kell meghatároznia a teljes infrastruktúrát egyetlen sablonban. Gyakran érdemes felosztani a telepítési követelményeket konkrét, célspecifikus sablonokra. Ezeket a sablonokat egyszerűen újból felhasználhatja különböző megoldásokhoz. Egy adott megoldás üzembe helyezéséhez létre kell hoznia egy fő sablont, amely összekapcsolja az összes szükséges sablont. Az alábbi kép bemutatja, hogyan telepíthető egy háromszintű megoldás egy olyan fölérendelt sablon segítségével, amely három beágyazott sablont tartalmaz.

![beágyazott rétegsablon](./media/overview/nested-tiers-template.png)

Ha elképzeli, hogy a rétegek külön életciklusokkal rendelkeznek, akkor a három szintet különálló erőforráscsoportokra telepítheti. Ügyeljen rá, hogy az erőforrások továbbra is kapcsolódhatnak egyéb erőforráscsoportok erőforrásaihoz.

![rétegsablon](./media/overview/tier-templates.png)

A beágyazott sablonokkal kapcsolatos további információkért lásd: [Kapcsolt sablonok használata az Azure Resource Manager eszközben](linked-templates.md).

## <a name="share-templates"></a>Sablonok megosztása

A sablon létrehozása után előfordulhat, hogy meg szeretné osztani a szervezet más felhasználóival. A [sablon specifikációi](template-specs.md) lehetővé teszik a sablon erőforrás-típusként való tárolását. Szerepköralapú hozzáférés-vezérlés használatával kezelheti a sablonhoz való hozzáférést. Azok a felhasználók, akik olvasási hozzáféréssel rendelkeznek a sablonhoz, a specifikációt telepíthetik, de nem változtathatják meg a sablont.

Ez a módszer azt jelenti, hogy biztonságosan megoszthatja a szervezet szabványainak megfelelő sablonokat.

## <a name="next-steps"></a>Következő lépések

* A sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd [: oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md).
* Ha szeretne többet megtudni az ARM-sablonokról a Microsoft Learn modulok irányított készletén keresztül, tekintse meg az [erőforrások üzembe helyezése és kezelése az Azure-ban ARM-sablonok használatával című részt](/learn/paths/deploy-manage-resource-manager-templates/).
* A sablonfájlok tulajdonságairól további információt az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakörben talál.
* A sablonok exportálásával kapcsolatos további tudnivalókért tekintse meg a rövid útmutató [: ARM-sablonok létrehozása és telepítése a Azure Portal használatával](quickstart-create-templates-use-the-portal.md)című témakört.
* A gyakori kérdésekre adott válaszokért tekintse meg az [ARM-sablonokkal kapcsolatos gyakori kérdések](frequently-asked-questions.md)című témakört.
