---
title: Ismerkedés a Node.js Azure CDN SDK-val | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy egyszerű Node.js Console-alkalmazást, amely bemutatja, hogyan automatizálható Azure CDN profilok és végpontok létrehozása és kezelése.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993377"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

A [Node.jsAzure CDN SDK ](https://www.npmjs.com/package/azure-arm-cdn) -val a CDN-profilok és-végpontok létrehozásának és kezelésének automatizálására használható.  Ez az oktatóanyag végigvezeti egy egyszerű Node.js Console-alkalmazás létrehozásán, amely számos elérhető műveletet mutat be.  Ez az oktatóanyag nem ismerteti részletesen a Azure CDN SDK Node.js összes aspektusát.

Az oktatóanyag elvégzéséhez már [Node.js](https://www.nodejs.org) **4. x. x** vagy újabb verzió van telepítve és konfigurálva.  Használhat bármely szövegszerkesztőt, amelyben létre kívánja hozni a Node.js alkalmazást.  Az oktatóanyag írásához a [Visual Studio Code](https://code.visualstudio.com)-ot használtuk.  

> [!TIP]
> Az [oktatóanyagból származó befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) letölthető az MSDN webhelyen.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>A projekt létrehozása és NPM-függőségek hozzáadása
Most, hogy létrehozott egy erőforráscsoportot a CDN-profilokhoz, és az Azure AD-alkalmazás engedélyt kapott a CDN-profilok és-végpontok kezelésére a csoporton belül, elkezdheti az alkalmazás létrehozását.

Hozzon létre egy mappát az alkalmazás tárolásához.  A konzolon az aktuális elérési úton lévő Node.js eszközökkel állítsa be az aktuális helyet az új mappára, és a következő végrehajtásával inicializálja a projektet:

```console
npm init
```

Ekkor a projekt inicializálására vonatkozó kérdések sora jelenik meg.  A **belépési pont** esetében ez az oktatóanyag *app.jst* használ.  Az alábbi példában megtekintheti az egyéb döntéseket.

![NPM init kimenet](./media/cdn-app-dev-node/cdn-npm-init.png)

A projekt most már inicializálva van egy *packages.jsval a* fájlon.  A projektünk NPM-csomagokban található Azure-kódtárakat fog használni.  Az Azure-ügyfél futtatókörnyezetét az Node.js (MS-Rest-Azure) és a Azure CDN ügyféloldali Node.js kódtár (Azure-ARM-CD) használatával fogjuk használni.  Vegyük fel a projektbe a függőségeket.

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
```

A csomagok telepítésének befejezése után a fájl *package.js* a következőhöz hasonlóan kell kinéznie (a verziószámok eltérőek lehetnek):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Végül a szövegszerkesztővel hozzon létre egy üres szövegfájlt, és mentse azt a projekt mappájának gyökerébe *app.jsként*.  Most már készen áll a kód írásának megkezdésére.

## <a name="requires-constants-authentication-and-structure"></a>Szükséges, állandók, hitelesítés és struktúra
A szerkesztőben megnyitott *app.js* megnyithatja a programunk alapszintű szerkezetét.

1. Adja hozzá a "requires" utasítást a NPM-csomagokhoz a következővel:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Meg kell határozni néhány állandót, amelyeket a metódusok használni fognak.  Adja hozzá a következőt.  A helyőrzőket cserélje le úgy, hogy a **&lt; zárójeleket &gt;** is beleértve, szükség esetén a saját értékeivel.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Ezután létrehozza a CDN felügyeleti ügyfelet, és megadja a hitelesítő adatait.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ha egyéni felhasználói hitelesítést használ, akkor ez a két sor némileg eltér.
   
   > [!IMPORTANT]
   > Csak akkor használja ezt a kódot, ha egy egyszerű szolgáltatásnév helyett egyéni felhasználói hitelesítést szeretne használni.  Ügyeljen arra, hogy az egyéni felhasználói hitelesítő adatokat megvédje, és titokban tartsa őket.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Ügyeljen rá, hogy a megfelelő információkkal helyettesítse az elemeket a **&lt; szögletes zárójelben &gt;** .  A esetében `<redirect URI>` használja az alkalmazás Azure ad-ben való regisztrálásakor megadott átirányítási URI-t.
4. Node.js Console-alkalmazásunk bizonyos parancssori paramétereket fog igénybe venni.  Győződjön meg arról, hogy legalább egy paraméter át lett adva.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Ez a program legfontosabb részét képezi, amely a paraméterek átadása alapján más függvényeknek is ki van választva.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. A programban több helyen is meg kell győződnie arról, hogy a megfelelő számú paramétert adtak át a rendszer, és megjelenítünk némi segítséget, ha nem jelennek meg megfelelően.  Ehhez hozzon létre függvényeket.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Végül pedig a CDN felügyeleti ügyfélen használt függvények aszinkron módon működnek, ezért szükségük van egy olyan metódusra, amely visszahívást végez, amikor elkészült.  Tegyük fel, hogy megjelenítheti a CDN felügyeleti ügyfelének kimenetét (ha van ilyen), és szabályosan kilép a programból.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Most, hogy a program alapszintű szerkezete meg van írva, a paraméterek alapján létre kell hoznia a hívott függvényeket.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok listázása
Kezdjük a kóddal a meglévő profilok és végpontok listázásához.  A kód megjegyzései megadják a várt szintaxist, így tudjuk, hogy az egyes paraméterek hol vannak.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok létrehozása
Ezután a függvényeket a profilok és végpontok létrehozásához írjuk.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Végpont kiürítése
Feltételezve, hogy a végpont létrejött, az egyik gyakori feladat, amelyet a programban végre szeretnénk hajtani, a végpontban található tartalom megtisztítása.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok törlése
Az utolsó függvény tartalmazza a végpontok és a profilok törlését is.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>A program futtatása
Most már a kedvenc hibakeresőnk vagy a konzolon is végrehajtjuk Node.js programot.

> [!TIP]
> Ha hibakeresőként használja a Visual Studio Code-ot, be kell állítania a környezetét, hogy az átadja a parancssori paramétereket.  A Visual Studio Code ezt a fájl **launch.js** .  Keresse meg az **argumentumok** nevű tulajdonságot, és adjon hozzá karakterlánc-értékeket a paraméterekhez, hogy az a következőhöz hasonlóan néz ki:  `"args": ["list", "profiles"]` .
> 
> 

Kezdjük a profilok listázásával.

![Profilok listázása](./media/cdn-app-dev-node/cdn-list-profiles.png)

Egy üres tömböt kaptunk vissza.  Mivel az erőforráscsoport nem tartalmaz profilokat, ez a várt érték.  Hozzon létre most egy profilt.

![Profil létrehozása](./media/cdn-app-dev-node/cdn-create-profile.png)

Most vegyünk fel egy végpontot.

![Végpont létrehozása](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Végül töröljük a profilt.

![Profil törlése](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Következő lépések
Ha meg szeretné tekinteni a kész projektet ebből az útmutatóból, [töltse le a mintát](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

A Node.js Azure CDN SDK-ra mutató hivatkozás megtekintéséhez tekintse meg a [hivatkozást](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Ha további dokumentációt szeretne a Node.js készült Azure SDK-hoz, tekintse meg a [teljes referenciát](https://azure.github.io/azure-sdk-for-node/).

A CDN-erőforrások kezelése a [PowerShell](cdn-manage-powershell.md)-lel.

