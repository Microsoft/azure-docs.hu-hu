---
title: Oktatóanyag – webalkalmazás előkészítése az Azure kommunikációs szolgáltatásokhoz (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Ismerje meg, hogyan hozhat létre olyan alapkonfigurációs webalkalmazást, amely támogatja az Azure kommunikációs szolgáltatásokat
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: db59a9e7693190582736b9460658f629f4f1e555
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369628"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Oktatóanyag: webalkalmazás előkészítése az Azure kommunikációs szolgáltatásokhoz (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások segítségével valós idejű kommunikációt adhat hozzá alkalmazásaihoz. Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy olyan webalkalmazást, amely támogatja az Azure kommunikációs szolgáltatásokat. Ez egy bevezető oktatóanyag, amely új fejlesztők számára készült, akik a valós idejű kommunikációt szeretnék kezdeni.

Az oktatóanyag végére egy, az Azure kommunikációs szolgáltatások ügyféloldali kódtárakkal konfigurált alapterv-webalkalmazással fog rendelkezni, amellyel elkezdheti a valós idejű kommunikációs megoldás felépítését.

Ha visszajelzést szeretne kapni, látogasson el az [Azure Communication Services GitHub](https://github.com/Azure/communication) oldalára.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A fejlesztési környezet konfigurálása
> * Helyi webkiszolgáló beállítása
> * Az Azure kommunikációs szolgáltatások csomagjainak hozzáadása a webhelyhez
> * Webhelyek közzététele az Azure-beli statikus webhelyeken

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. Vegye figyelembe, hogy az ingyenes fiók az Azure-kreditek $200-as számú szolgáltatását kínálja a szolgáltatások bármilyen kombinációjának kipróbálására.
- [Visual Studio Code](https://code.visualstudio.com/): ezt a kódot fogjuk használni a helyi fejlesztési környezetben található kód szerkesztéséhez.
- [webpack](https://webpack.js.org/): ezt fogja használni a kód kötegbe és helyileg történő üzemeltetésére.
- [Node.js](https://nodejs.org/en/): ezt fogja használni a függőségek telepítéséhez és kezeléséhez, például az Azure kommunikációs szolgáltatások ügyféloldali kódtárai és webpack-szolgáltatásaihoz.
- [NVM és NPM](https://docs.microsoft.com/windows/nodejs/setup-on-windows) kezelése.
- A Visual Studio Code-hoz készült [Azure Storage-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) . Ez a bővítmény az alkalmazás Azure Storage-ban való közzétételéhez szükséges. [További információ a statikus webhelyek Azure Storage-beli üzemeltetéséről](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- A [Azure app Service bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A bővítmény lehetővé teszi a webhelyek üzembe helyezését (az előzőhöz hasonlóan), de a teljes körűen felügyelt folyamatos integráció és folyamatos teljesítés (CI/CD) konfigurálására szolgáló lehetőséggel.
- Az [Azure Function bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) saját kiszolgáló nélküli alkalmazások létrehozásához. A hitelesítési alkalmazást például az Azure functions szolgáltatásban üzemeltetheti.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../quickstarts/create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat. Útmutatásért tekintse meg a [hozzáférési tokenek](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) rövid útmutatóját vagy a [megbízható szolgáltatásról szóló oktatóanyagot](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) .


## <a name="configure-your-development-environment"></a>A fejlesztési környezet konfigurálása

A helyi fejlesztési környezet a következőképpen lesz konfigurálva:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Fejlesztői környezet architektúrája":::


### <a name="install-nodejs-nvm-and-npm"></a>A Node.js, a NVM és a NPM telepítése

Az ügyféloldali alkalmazáshoz szükséges különböző függőségek letöltéséhez és telepítéséhez Node.js fogjuk használni. Az Azure-ban üzemeltetett statikus fájlok létrehozásához ezt fogjuk használni, így nem kell aggódnia a kiszolgálón történő konfigurálásával kapcsolatban.

A Windows-fejlesztők [ezt a NodeJS-oktatóanyagot](https://docs.microsoft.com/windows/nodejs/setup-on-windows) követve konfigurálhatják a csomópontot, a NVM és a NPM. 

Ezt az oktatóanyagot az LTS 12.20.0 verziójának használatával teszteltük. A NVM telepítése után a következő PowerShell-paranccsal telepítheti a használni kívánt verziót:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="A NVM használata a Node.jsüzembe helyezéséhez ":::

### <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

A [Visual Studio Code](https://code.visualstudio.com/) -ot az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms)is letöltheti.

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Munkaterület létrehozása az Azure kommunikációs szolgáltatások projektjeihez

Hozzon létre egy új mappát a projektfájlok tárolásához, például: `C:\Users\Documents\ACS\CallingApp` . A Visual Studio Code-ban kattintson a "fájl", a "mappa hozzáadása a munkaterülethez" elemre, és adja hozzá a mappát a munkaterülethez.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Új munkahely létrehozása":::

Nyissa meg a "Explorer" kifejezést a Visual Studio Code-ban a bal oldali ablaktáblán, és a "névtelen" Munkaterület "CallingApp" mappáját fogja látni.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Explorer":::

Nyugodtan frissítheti a munkaterület nevét. A Node.js verziójának érvényesítéséhez kattintson a jobb gombbal a "CallingApp" mappára, és válassza a "Megnyitás integrált terminálban" lehetőséget.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Terminál megnyitása":::

A terminálon írja be a következő parancsot a node.js az előző lépésben telepített verziójának ellenőrzéséhez:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Node.js verziójának ellenőrzése":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Azure-bővítmények telepítése a Visual Studio Code-hoz

Telepítse az [Azure Storage-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) a Visual Studio Marketplace-en vagy a Visual Studio Code-ban (> > Azure Storage-bővítmények megtekintése).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Az Azure Storage Extension 1 telepítése":::

Kövesse ugyanezen lépéseket a [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) és a [Azure app Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) bővítmények esetében.


## <a name="set-up-a-local-webserver"></a>Helyi webkiszolgáló beállítása

### <a name="create-a-new-npm-package"></a>Új NPM-csomag létrehozása

A terminálban a munkaterület mappájának elérési útjából írja be a következőt:

``` console
npm init -y
```

Ez a parancs inicializál egy új NPM-csomagot, és hozzáadja `package.json` a projekt gyökérkönyvtárához.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="JSON-csomag":::

Az NPM init parancs további dokumentációja [itt](https://docs.npmjs.com/cli/v6/commands/npm-init) található.

### <a name="install-webpack"></a>A webpack telepítése

a webpack lehetővé teszi, hogy az Azure-ban üzembe helyezhető statikus fájlokba [csomagolja](https://webpack.js.org/) a kódot. Emellett egy fejlesztői kiszolgáló is van, amelyet a hívó mintával való használatra konfigurálunk.

A (z)-ben a következőt kell beírnia a webpack telepítéséhez:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Ez az oktatóanyag a fenti megadott verziók használatával lett tesztelve. Annak megadása, `-dev` hogy a Package Manager ezt a függőséget fejlesztési célokra használja-e, és ne szerepeljen az Azure-ban üzembe helyezett kódban.

Két új csomagot fog látni a `package.json` fájlhoz "devDependencies" néven. A csomagok a könyvtárba lesznek telepítve `./CallingApp/node_modules/` .

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="webpack-konfiguráció":::

### <a name="configure-the-development-server"></a>A fejlesztői kiszolgáló konfigurálása

Ha statikus alkalmazást (például a `index.html` fájlt) futtat a böngészőjében, a `file://` protokollt használja. Ahhoz, hogy a NPM modulok megfelelően működjenek, szükségünk lesz a HTTP protokollra a webpack helyi fejlesztési kiszolgálóként való használatával.

Két konfigurációt hozunk létre: egyet a fejlesztéshez és a másikat az éles környezethez. Az éles környezethez előkészített fájlok a minified, ami azt jelenti, hogy eltávolítjuk a fel nem használt szóközöket és karaktereket. Ez olyan éles környezetekben alkalmazható, ahol a késést a lehető legkisebbre kell állítani, vagy ha a kódot el kell torzítani.

Az eszköz használatával a `webpack-merge` [webpack különböző konfigurációs fájljaival](https://webpack.js.org/guides/production/) dolgozhat

Kezdjük a fejlesztési környezettel. Először telepíteni kell a következőt: `webpack merge` . A terminálon futtassa a következőt:

```Console
npm install --save-dev webpack-merge
```

A `package.json` fájlban egy további függőség jelenik meg a "devDependencies" elemnél.

A következő lépésben létre kell hoznia egy új fájlt, `webpack.common.js` és hozzá kell adnia a következő kódot:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

Ezután két további fájlt fogunk hozzáadni, egyet az egyes konfigurációkhoz:

* webpack.dev.js
* webpack.prod.js

A következő lépésben módosítania kell a `webpack.dev.js` fájlt. Adja hozzá a következő kódot a fájlhoz:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Ebben a konfigurációban általános paramétereket importálunk a alkalmazásból `webpack.common.js` , egyesítjük a két fájlt, állítsa a módot a "fejlesztés" értékre, és konfigurálja a SourceMap "beágyazott forrás-hozzárendelés" néven.

A fejlesztői mód azt jelzi, hogy a webpack nem hozza létre a fájlokat, és nem készít optimalizált éles fájlokat. A webpack-módok részletes dokumentációja [itt](https://webpack.js.org/configuration/mode/)található.

A forrás-hozzárendelési beállítások [itt](https://webpack.js.org/configuration/devtool/#root)láthatók. A forrás-hozzárendelés beállítása megkönnyíti a böngészőn keresztüli hibakeresést.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="A webpack konfigurálása":::

A fejlesztői kiszolgáló futtatásához nyissa meg `package.json` a következő kódot, és adja hozzá a parancsfájlok területen:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

A fájlnak most így kell kinéznie:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Hozzáadta a NPM-ből használható parancsot. 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="package.jsmódosítása":::

### <a name="testing-the-development-server"></a>A fejlesztési kiszolgáló tesztelése

 A Visual Studio Code-ban hozzon létre három fájlt a projektben:

* `index.html`
* `app.js`
* `app.css` (nem kötelező, ez lehetővé teszi az alkalmazás stílusát)

Beillesztés ebbe a következőbe `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="HTML-fájl":::

Szúrja be a következő kódot az `app.js` fájlba:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Szúrja be a következő kódot az `app.css` fájlba:

```CSS
html {
    font-family: sans-serif;
  }
```

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js fájl JS-kóddal":::

Amikor megnyitja ezt a lapot, megjelenik az üzenet riasztással és a böngésző konzolján.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="App. css fájl":::

A fejlesztési konfiguráció teszteléséhez használja a következő terminál-parancsot:

```Console
npm run build:dev
```

A konzol megjeleníti, hogy a kiszolgáló hol fut. Alapértelmezés szerint ez a érték `http://localhost:8080` . A Build: dev parancs a korábban hozzáadott parancs `package.json` .

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Fejlesztői kiszolgáló indítása":::
 
 Navigáljon a böngésző címsorába, és az előző lépésekben konfigurált lapot és riasztást kell látnia.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="HTML-lap":::
  
 
Amíg a kiszolgáló fut, módosíthatja a kódot, és a kiszolgáló és a HTML-oldal automatikusan újratöltődik. 

Ezután nyissa meg a `app.js` fájlt a Visual Studio Code-ban, és törölje a következőt: `alert('Hello world alert!');` . Mentse a fájlt, és ellenőrizze, hogy a riasztás eltűnik-e a böngészőből.

A kiszolgáló leállításához futtassa a parancsot `Ctrl+C` a terminálon. A kiszolgáló elindításához írja be `npm run build:dev` a következőt:.

## <a name="add-the-azure-communication-services-packages"></a>Az Azure kommunikációs szolgáltatások csomagjainak hozzáadása

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat a JavaScript-hez készült ügyféloldali kódtár meghívásával.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Ez a művelet felveszi az Azure kommunikációs szolgáltatásokat, és meghívja a csomagokat a csomag függőségeiként. Két új csomagot fog látni a `package.json` fájlhoz. A `npm install` parancsról további információt [itt](https://docs.npmjs.com/cli/v6/commands/npm-install)találhat.

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Az Azure kommunikációs szolgáltatások csomagjainak telepítése":::

Ezeket a csomagokat az Azure kommunikációs szolgáltatások csapata adja meg, és tartalmazza a hitelesítést és a hívó kódtárakat. A "--Save" parancs azt jelzi, hogy az alkalmazás az éles használatú csomagoktól függ, és szerepelni fog a `dependencies` `package.json` fájlban. Az alkalmazás éles környezetben való létrehozásakor a csomagok szerepelni fognak a termelési kódban.


## <a name="publish-your-website-to-azure-static-websites"></a>Webhelyek közzététele az Azure-beli statikus webhelyeken

### <a name="create-a-configuration-for-production-deployment"></a>Konfiguráció létrehozása éles üzembe helyezéshez

Adja hozzá az alábbi kódot a következőhöz `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Vegye figyelembe, hogy ez a konfiguráció a webpack.common.js (ahol a bemeneti fájlt és az eredmények tárolásának helyét) egyesíti a rendszer, és a módot "éles" értékre állítja be.
 
A alkalmazásban `package.json` adja hozzá a következő kódot:

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

A fájlnak a következőhöz kell hasonlítania:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Konfigurált fájlok":::


A terminál futtatásakor:

```Console
npm run build:prod
```

A parancs létrehoz egy `dist` mappát és egy éles környezetben felkészített `app.js` statikus fájlt. 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Éles környezet létrehozása":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Az alkalmazás üzembe helyezése az Azure Storage-ban
 
Másolja `index.html` `app.css` a mappát a `dist` mappába.

A `dist` mappában hozzon létre egy új fájlt, és nevezze el `404.html` . Másolja a következő korrektúrát a fájlba:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Mentse a fájlt (CTRL + S).

Kattintson a jobb gombbal, és válassza a üzembe helyezés statikus webhelyre az Azure Storage használatával lehetőséget.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Az Azure üzembe helyezésének megkezdése":::
 
A `Select subscription` mezőben válassza az "Bejelentkezés az Azure-ba (vagy az ingyenes Azure-fiók létrehozása" lehetőséget, ha még nem hozott létre előfizetést)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Bejelentkezés az Azure-ba":::
 
Válassza a `Create new Storage Account`  >  `Advanced` következőket:

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="A Storage-fiók csoportjának létrehozása":::
 
 Adja meg a tárolási csoport nevét:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Név hozzáadása a fiókhoz":::
 
Szükség esetén hozzon létre egy új erőforráscsoportot:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Új csoport létrehozása":::
  
  Az "igen" válasz szeretné engedélyezni a statikus webhely üzemeltetését? "
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="A statikus webhely üzemeltetését engedélyező lehetőség kiválasztása":::
  
Fogadja el az alapértelmezett fájlnevet a "adja meg az index dokumentum nevét" kifejezést a fájl létrehozásakor `index.html` .

Írja be a következőt `404.html` : "adja meg a 404-es hiba-dokumentum elérési útját".  
  
Válassza ki az alkalmazás helyét. A kiválasztott hely határozza meg, hogy a rendszer melyik adathordozó-processzort fogja használni a következő hívási alkalmazásban a csoportos hívásokban. 

Az Azure kommunikációs szolgáltatás az alkalmazás helye alapján kiválasztja a média processzorát.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Hely kiválasztása":::
  
Várjon, amíg az erőforrás és a webhely létre nem jön. 
 
Kattintson a Tallózás a webhelyen lehetőségre:

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Az üzembe helyezés befejeződött":::
 
A böngésző fejlesztői eszközeiből ellenőrizheti a forrást, és megtekintheti az éles környezetben előkészített fájlt.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Webhely":::

Nyissa meg a [Azure Portal](https://portal.azure.com/#home), válassza ki az erőforráscsoportot, válassza ki a létrehozott alkalmazást, és keresse meg a következőt: `Settings`  >  `Static website` . Láthatja, hogy a statikus webhelyek engedélyezve vannak, és jegyezze fel az elsődleges végpontot, az indexelési dokumentumot és a hiba elérési útját tartalmazó dokumentumot.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Statikus webhely kiválasztása":::

A "Blob service" területen válassza a "tárolók" lehetőséget, és két tárolót fog látni, egyet a naplókhoz ($logs) és a webhely tartalmát ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Tároló konfigurációja":::

A `$web` Visual Studióban létrehozott és az Azure-ban üzembe helyezett fájlokat megtekintheti. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Üzembe helyezés":::

Bármikor újra üzembe helyezheti az alkalmazást a Visual Studio Code-ból.

Most már készen áll az első Azure Communication Services-webalkalmazás létrehozására.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hanghívás hozzáadása az alkalmazáshoz](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következőket is érdemes elvégezheti:

- [Csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
- [A hitelesítés ismertetése](../concepts/authentication.md)
