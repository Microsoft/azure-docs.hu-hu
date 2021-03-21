---
title: 'Rövid útmutató: az első JavaScript-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépéseket követve engedélyezheti az erőforrás-gráf függvénytárát a JavaScripthez, és futtathatja az első lekérdezést.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 97cbd9ddf65a4135f55304f6dd67c704c6fcac5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98917521"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása JavaScript használatával

Ez a rövid útmutató végigvezeti a kódtárak JavaScript-telepítéshez való hozzáadásának folyamatán. Az Azure Resource Graph használatának első lépése egy JavaScript-alkalmazás inicializálása a szükséges könyvtárakkal.

A folyamat végén hozzáadta a kódtárakat a JavaScript-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- **Node.js**: a 12-es vagy újabb verziójú [Node.js](https://nodejs.org/) megadása kötelező.

## <a name="application-initialization"></a>Alkalmazásinicializálás

Annak engedélyezéséhez, hogy a JavaScript lekérdezze az Azure Resource Graphot, konfigurálni kell a környezetet. Ez a beállítás bárhol használható a JavaScript használatával, beleértve [a bash használatát a Windows 10 rendszeren](/windows/wsl/install-win10).

1. Inicializáljon egy új Node.js projektet a következő parancs futtatásával.

   ```bash
   npm init -y
   ```

1. Adjon hozzá egy hivatkozást a yargs modulhoz.

   ```bash
   npm install yargs
   ```

1. Adjon hozzá egy hivatkozást az Azure Resource Graph-modulhoz.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Adjon hozzá egy hivatkozást az Azure Authentication Library-hez.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > A _package.json_ `@azure/arm-resourcegraph` verziójának ellenőrzése **2.0.0** vagy újabb verziójú, és `@azure/ms-rest-nodeauth` a verziószáma **3.0.3** vagy magasabb.

## <a name="query-the-resource-graph"></a>Az erőforrás-gráf lekérdezése

1. Hozzon létre egy _index.js_ nevű új fájlt, és adja meg a következő kódot.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Adja meg a következő parancsot a terminálon:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Ügyeljen rá, hogy a `<YOUR_SUBSCRIPTION_ID_LIST>` helyőrzőt cserélje le az Azure-előfizetési azonosítók vesszővel tagolt listájára.

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem tartalmaz olyan rendezési módosítót, mint a `order by` , a lekérdezés többszöri futtatása valószínűleg egy másik erőforrás-készletet eredményez.

1. Módosítsa az első paramétert, `index.js` és módosítsa a lekérdezést `order by` a **Name (név** ) tulajdonságra. A helyére írja `<YOUR_SUBSCRIPTION_ID_LIST>` be az előfizetés-azonosítóját:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Ahogy a parancsfájl megkísérli a hitelesítést, az alábbi üzenethez hasonló üzenet jelenik meg a terminálon:

   > A bejelentkezéshez egy webböngésző segítségével nyissa meg a lapot, https://microsoft.com/devicelogin és adja meg a hitelesíteni kívánt FGB56WJUGK.

   Miután végzett a hitelesítéssel a böngészőben, a szkript továbbra is futni fog.

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Módosítsa az első paramétert, `index.js` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra, majd az első `limit` öt találatra. A helyére írja `<YOUR_SUBSCRIPTION_ID_LIST>` be az előfizetés-azonosítóját:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat az alkalmazásból, futtassa a következő parancsot.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban felvette az erőforrás-gráf kódtárait a JavaScript-környezetbe, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)