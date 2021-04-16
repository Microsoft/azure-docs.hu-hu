---
title: 'Rövid útmutató: Az első JavaScript-lekérdezés'
description: Ebben a rövid útmutatóban a lépéseket követve engedélyezheti a JavaScripthez Resource Graph kódtárat, és futtatja az első lekérdezését.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: ef93378acaf2c92cf4f7761345cea326af63d300
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533051"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Rövid útmutató: Az első lekérdezés Resource Graph JavaScript használatával

Ez a rövid útmutató végigvezeti a kódtárak JavaScript-telepítéshez való hozzáadásának folyamatán. A kódtár használatának első Azure Resource Graph JavaScript-alkalmazás inicializálása a szükséges kódtárakkal.

A folyamat végén hozzáadja a kódtárakat a JavaScript-telepítéshez, és futtatja az első Resource Graph lekérdezését.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, [a](https://azure.microsoft.com/free/) kezdés előtt hozzon létre egy ingyenes fiókot.

- **Node.js:** [Node.js](https://nodejs.org/) 12-es vagy újabb verzió szükséges.

## <a name="application-initialization"></a>Alkalmazásinicializálás

Ahhoz, hogy a JavaScript Azure Resource Graph lekérdezni, a környezetet konfigurálni kell. Ez a beállítás bárhol működik, ahol a JavaScript használható, beleértve a [Bash on Windows 10.](/windows/wsl/install-win10)

1. Inicializáljon egy Node.js projektben a következő parancs futtatásával.

   ```bash
   npm init -y
   ```

1. Adjon hozzá egy hivatkozást a yargs modulhoz.

   ```bash
   npm install yargs
   ```

1. Adjon hozzá egy hivatkozást a Azure Resource Graph modulhoz.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Adjon hozzá egy, az Azure-beli hitelesítési kódtárra vonatkozó hivatkozást.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Ellenőrizze, _hogy apackage.js_ `@azure/arm-resourcegraph` **2.0.0-s** vagy újabb, és `@azure/ms-rest-nodeauth` **3.0.3-as** vagy újabb verziójú-e.

## <a name="query-the-resource-graph"></a>Lekérdezés a Resource Graph

1. Hozzon létre egy új, _index.js_ nevű fájlt, és írja be a következő kódot.

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

1. Írja be a következő parancsot a terminálba:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   A helyőrzőt cserélje le az Azure-előfizetések vesszővel elválasztott `<YOUR_SUBSCRIPTION_ID_LIST>` listára.

   > [!NOTE]
   > Mivel ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint például, a lekérdezés többszöri futtatása valószínűleg kérésenként eltérő `order by` erőforráskészletet fog eredményezni.

1. Módosítsa az első paramétert a következőre: `index.js` , és módosítsa a lekérdezést a Name `order by` **(Név) tulajdonságra.** Cserélje `<YOUR_SUBSCRIPTION_ID_LIST>` le a helyére az előfizetés azonosítóját:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   Amikor a szkript megkísérli a hitelesítést, a terminálon az alábbihoz hasonló üzenet jelenik meg:

   > A bejelentkezéshez egy webböngészővel nyissa meg az oldalt, és írja be az https://microsoft.com/devicelogin FGB56WJUGK kódot a hitelesítéshez.

   A böngészőben való hitelesítés után a szkript továbbra is futni fog.

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancsrend először korlátozza a lekérdezési eredményeket, majd megrendeli azokat.

1. Módosítsa az első paramétert a következőre: , és módosítsa a lekérdezést a Name (Név) tulajdonságra, majd az `index.js` `order by` első öt  `limit` találatra. Cserélje `<YOUR_SUBSCRIPTION_ID_LIST>` le a helyére az előfizetés azonosítóját:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

Ha az utolsó lekérdezés többször is lefut, feltéve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** (Név) tulajdonság szerint vannak megrendelve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a telepített kódtárakat az alkalmazásból, futtassa a következő parancsot.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph kódtárakat a JavaScript-környezethez, és futtatta az első lekérdezését. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
