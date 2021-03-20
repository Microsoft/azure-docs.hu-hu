---
title: Több eredetű erőforrás-megosztás (CORS) Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan konfigurálható az CORS-alapú erőforrás-megosztás (Azure Cosmos DB) Azure Portal és Azure Resource Manager sablonok használatával.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339897"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Az idegen eredetű erőforrások megosztásának konfigurálása (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az eltérő eredetű erőforrás-megosztás (CORS) egy olyan HTTP-szolgáltatás, amely lehetővé teszi, hogy az egyik tartományon futó webalkalmazások hozzáférjenek egy másik tartomány erőforrásaihoz. A böngészők olyan biztonsági korlátozást valósítanak meg, amelyik azonos eredetű házirend, amely megakadályozza, hogy egy weblap egy másik tartományban lévő API-kat hívjon fel. A CORS azonban biztonságos módot biztosít ahhoz, hogy a forrás tartomány egy másik tartományban lévő API-kat hívjon fel. A Azure Cosmos DB a Core (SQL) API mostantól támogatja az CORS-alapú erőforrás-megosztást a "allowedOrigins" fejléc használatával. Miután engedélyezte az Azure Cosmos-fiók CORS-támogatását, a rendszer csak a hitelesített kérelmeket értékeli ki, hogy azok a megadott szabályoknak megfelelően engedélyezettek-e.

Az CORS-beállítást a Azure Portal vagy egy Azure Resource Manager sablonból is konfigurálhatja. A Core (SQL) API-t használó Cosmos-fiókok esetében a Azure Cosmos DB támogatja a JavaScript-függvénytárat, amely Node.js és böngészőalapú környezetekben is működik. Ez a tár most kihasználhatja a CORS-támogatás előnyeit az átjáró mód használata esetén. A funkció használatához nincs szükség ügyféloldali konfigurációra. A CORS-támogatással a böngészőből származó erőforrások közvetlenül hozzáférhetnek Azure Cosmos DB a [JavaScript-könyvtárból](https://www.npmjs.com/package/@azure/cosmos) , vagy közvetlenül a [REST API](/rest/api/cosmos-db/) az egyszerű műveletekhez.

> [!NOTE]
> A CORS-támogatás csak az Azure Cosmos DB Core (SQL) API esetében alkalmazható és támogatott. Nem alkalmazható a Cassandra, a Gremlin vagy a MongoDB Azure Cosmos DB API-kra, mivel ezek a protokollok nem használják a HTTP protokollt az ügyfél-kiszolgáló kommunikációhoz.

## <a name="enable-cors-support-from-azure-portal"></a>CORS-támogatás engedélyezése Azure Portal

A következő lépések végrehajtásával engedélyezheti a több eredetű erőforrás-megosztást a Azure Portal használatával:

1. Navigáljon az Azure Cosmos DB-fiókhoz. Nyissa meg a **CORS** panelt.

2. Adja meg az eredetek vesszővel tagolt listáját, amelyek az eltérő eredetű hívásokat végezhetnek a Azure Cosmos DB-fiókban. Például:, `https://www.mydomain.com` `https://mydomain.com` , `https://api.mydomain.com` . A "" helyettesítő karaktert is használhatja \* az összes eredet engedélyezéséhez, majd válassza a **Küldés** lehetőséget. 

   > [!NOTE]
   > Jelenleg nem használhat helyettesítő karaktereket a tartománynév részeként. Például a `https://*.mydomain.net` Formátum még nem támogatott. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Több eredetű erőforrás-megosztás engedélyezése Azure Portal használatával":::

## <a name="enable-cors-support-from-resource-manager-template"></a>A CORS támogatásának engedélyezése Resource Manager-sablonból

A CORS Resource Manager-sablonnal történő engedélyezéséhez adja hozzá a "CORS" szakaszt a "allowedOrigins" tulajdonsággal bármely meglévő sablonhoz. A következő JSON egy példa egy olyan sablonra, amely létrehoz egy új Azure Cosmos-fiókot, amelyen engedélyezve van a CORS.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>A Azure Cosmos DB JavaScript-kódtár használata böngészőből

Napjainkban a Azure Cosmos DB JavaScript-függvénytár csak a csomaggal szállított könyvtár CommonJS verzióját tartalmazza. Ha ezt a kódtárat szeretné használni a böngészőből, egy böngészőkompatibilis függvénytár létrehozásához olyan eszközt kell használnia, mint a kumulatív vagy a webpack. Bizonyos Node.js könyvtáraknak rendelkezniük kell a böngészőben. A következő példa egy olyan webpack-konfigurációs fájlt mutat be, amely a szükséges modell-beállításokkal rendelkezik.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Az alábbiakban egy olyan [mintakód-minta](https://github.com/christopheranderson/cosmos-browser-sample) látható, amely a Azure Cosmos db JavaScript SDK-könyvtárral rendelkező írógéppel és webpack-t használja egy olyan Todo-alkalmazás létrehozásához, amely valós idejű frissítéseket küld, amikor új elemek jönnek létre.
Ajánlott eljárásként ne használja az elsődleges kulcsot a Azure Cosmos DB a böngészőből való kommunikációhoz. Ehelyett használjon erőforrás-jogkivonatokat a kommunikációhoz. Az erőforrás-jogkivonatokkal kapcsolatos további információkért lásd: [hozzáférés biztonságossá tétele Azure Cosmos db](secure-access-to-data.md#resource-tokens) cikkhez.

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos-fiók biztonságossá tételének egyéb módjaival kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Tűzfal konfigurálása Azure Cosmos db](how-to-configure-firewall.md) cikkhez.

* [A virtuális hálózat és az alhálózat-alapú hozzáférés konfigurálása a Azure Cosmos DB-fiókhoz](how-to-configure-vnet-service-endpoint.md)
