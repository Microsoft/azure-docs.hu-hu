---
title: Érvénytelenek a sablon hibái
description: Ismerteti, Hogyan oldhatók fel a sablon érvénytelen hibái Azure Resource Manager sablonok telepítésekor.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185793"
---
# <a name="resolve-errors-for-invalid-template"></a>Érvénytelen sablonnal kapcsolatos hibák elhárítása

Ez a cikk azt ismerteti, Hogyan oldhatók fel a sablon érvénytelen hibái.

## <a name="symptom"></a>Hibajelenség

A sablonok telepítésekor a következő hibaüzenet jelenik meg:

```
Code=InvalidTemplate
Message=<varies>
```

A hibaüzenet a hiba típusától függ.

## <a name="cause"></a>Ok

Ez a hiba számos különböző típusú hibát eredményezhet. Általában a sablonban valamilyen szintaxissal vagy szerkezeti hibával járnak.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>1. megoldás – szintaktikai hiba

Ha hibaüzenet jelenik meg, amely jelzi, hogy a sablon sikertelen volt, szintaktikai probléma lehet a sablonban.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ez a hiba könnyen elvégezhető, mert a sablon kifejezései bonyolultak lehetnek. A Storage-fiókhoz tartozó következő név-hozzárendelés például egy szögletes zárójelet, három függvényt, három zárójelet, egy szimpla idézőjelek készletét és egy tulajdonságot tartalmaz:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Ha nem adja meg a megfelelő szintaxist, a sablon olyan értéket hoz létre, amely eltér a szándéktól.

Ha ilyen típusú hibát kap, körültekintően tekintse át a kifejezés szintaxisát. Érdemes lehet egy JSON-szerkesztőt használni, például a [Visual studiót](create-visual-studio-deployment-project.md) vagy a [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ot, ami figyelmeztetheti a szintaktikai hibákra.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>2. megoldás – a szegmensek helytelen hossza

Egy másik érvénytelen sablon-hiba történik, ha az erőforrás neve nem megfelelő formátumú.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

A legfelső szintű erőforrásnak a névben egy kisebb szegmensnek kell lennie, mint az erőforrás típusának. Az egyes szegmenseket perjelek különböztetik meg. A következő példában a típus két szegmensből áll, és a névnek egyetlen szegmense van, ezért **érvényes név**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

A következő példa azonban **nem érvényes név** , mert a típussal azonos számú szegmens található.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

A gyermek erőforrások esetében a típusnak és a névnek azonos számú szegmensnek kell lennie. A szegmensek száma logikus, mert a gyermek teljes neve és típusa tartalmazza a szülő nevét és típusát. Ezért a teljes névnek még egy kevesebb szegmense van, mint a teljes típus.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

A szegmensek beolvasása jobb lehet az erőforrás-szolgáltatókon keresztül alkalmazott Resource Manager-típusokkal. Ha például egy erőforrás-zárolást szeretne egy webhelyre alkalmazni, négy szegmensből álló típusra van szükség. Ezért a név három szegmens:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>3. megoldás – a paraméter érvénytelen.

Ha olyan paramétert ad meg, amely nem a megengedett értékek egyike, a következőhöz hasonló hibaüzenet jelenik meg:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Ellenőrizze az engedélyezett értékeket a sablonban, és adjon meg egyet az üzembe helyezés során. További információ az engedélyezett paraméterek értékeiről: [Azure Resource Manager sablonok parameters (paraméterek) szakasza](template-syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>4. megoldás – túl sok cél erőforráscsoport

Előfordulhat, hogy ez a hiba a korábbi központi telepítések során fordul elő, mert egyetlen központi telepítésben legfeljebb öt cél erőforráscsoport van korlátozva. 2020 májusában a korlátot 800-erőforráscsoportok értékre emelték. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoport-re](./deploy-to-resource-group.md).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>5. megoldás – körkörös függőség észlelhető

Ez a hibaüzenet akkor jelenik meg, ha az erőforrások egymástól függenek, így megakadályozza, hogy a telepítés elinduljon. A függőségek kombinációja két vagy több erőforrás-várakozást tesz elérhetővé más erőforrások esetében is. Például a resource1 a resource3 függ, a resource2 a resource1 függ, a resource3 pedig a resource2 függ. Ezt a problémát általában a szükségtelen függőségek eltávolításával lehet megoldani.

Körkörös függőség megoldása:

1. A sablonban keresse meg a körkörös függőségben azonosított erőforrást.
2. Az adott erőforrás esetében ellenőrizze a **dependsOn** tulajdonságot és a **Reference** függvény bármely használatát, hogy megtekintse, mely erőforrások függenek.
3. Ellenőrizze ezeket az erőforrásokat, és tekintse meg, hogy mely erőforrások függenek. Kövesse a függőségeket, amíg olyan erőforrást észlel, amely az eredeti erőforrástól függ.
5. A körkörös függőségben érintett erőforrások esetében alaposan vizsgálja meg a **dependsOn** tulajdonság összes használatát a nem szükséges függőségek azonosításához. Távolítsa el ezeket a függőségeket. Ha nem biztos benne, hogy függőségre van szüksége, próbálja meg eltávolítani.
6. Telepítse újra a sablont.

Ha eltávolítja az értékeket a **dependsOn** tulajdonságból, a sablon telepítésekor hibákat okozhat. Ha hibaüzenetet kap, adja hozzá a függőséget a sablonhoz.

Ha ez a módszer nem oldja meg a körkörös függőséget, érdemes lehet áthelyezni a telepítési logika egy részét alárendelt erőforrásokra (például bővítményekre vagy konfigurációs beállításokra). Konfigurálja ezeket a alárendelt erőforrásokat úgy, hogy az a körkörös függőségben érintett erőforrások után legyen üzembe helyezhető. Tegyük fel például, hogy két virtuális gépet telepít, de tulajdonságokat kell megadnia, amelyek a másikra hivatkoznak. Ezeket a következő sorrendben helyezheti üzembe:

1. vm1
2. VM2
3. A VM1 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM1, amelyeket a VM2-ből kap.
4. A VM2 bővítmény a VM1 és a VM2 függvénytől függ. A bővítmény beállítja azokat az értékeket a VM2, amelyeket a VM1-ből kap.

Ugyanez a módszer App Service alkalmazások esetében is működik. Érdemes áthelyezni a konfigurációs értékeket az alkalmazás-erőforrás alárendelt erőforrásaiba. A következő sorrendben telepíthet két webalkalmazást:

1. webapp1
2. webapp2
3. a webapp1 konfigurációja a webapp1 és a webapp2 függvénytől függ. A webapp2 származó értékekkel rendelkező alkalmazás-beállításokat tartalmaz.
4. a webapp2 konfigurációja a webapp1 és a webapp2 függvénytől függ. A webapp1 származó értékekkel rendelkező alkalmazás-beállításokat tartalmaz.