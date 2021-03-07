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
ms.openlocfilehash: 08fe9e56298218936e700518cdd150f54d7f01de
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425721"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Oktatóanyag: webalkalmazás előkészítése az Azure kommunikációs szolgáltatásokhoz (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Az Azure kommunikációs szolgáltatások segítségével valós idejű kommunikációt adhat hozzá alkalmazásaihoz. Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy olyan webalkalmazást, amely támogatja az Azure kommunikációs szolgáltatásokat. Ez egy bevezető oktatóanyag olyan új fejlesztőknek, akik szeretnének megkezdeni a valós idejű kommunikációt.

Az oktatóanyag végére egy, az Azure kommunikációs szolgáltatások ügyféloldali kódtárakkal konfigurált alapterv-webalkalmazással fog rendelkezni. Ezt követően az alkalmazás használatával megkezdheti a valós idejű kommunikációs megoldás felépítését.

Ha visszajelzést szeretne kapni, látogasson el az [Azure Communication Services GitHub oldalára](https://github.com/Azure/communication) .

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Konfigurálja a fejlesztési környezetet.
> * Hozzon létre egy helyi webkiszolgálót.
> * Adja hozzá az Azure kommunikációs szolgáltatások csomagjait a webhelyéhez.
> * Tegye közzé webhelyét az Azure statikus webhelyein.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört. Az ingyenes fiók $200 az Azure-kreditekben, hogy kipróbálja a szolgáltatások bármely kombinációját.
- [Visual Studio Code](https://code.visualstudio.com/) a kód szerkesztéséhez a helyi fejlesztési környezetben.
- a [webpack](https://webpack.js.org/) a kód megadásához és helyi üzemeltetéséhez szükséges.
- [Node.js](https://nodejs.org/en/) olyan függőségek telepítéséhez és kezeléséhez, mint az Azure kommunikációs szolgáltatások ügyféloldali kódtárai és a webpack.
- [NVM és NPM](/windows/nodejs/setup-on-windows) kezelése.
- A Visual Studio Code-hoz készült [Azure Storage-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) . Erre a bővítményre szüksége lesz az alkalmazás Azure Storage-ban való közzétételéhez. [További információ a statikus webhelyek Azure Storage-beli üzemeltetéséről](../../storage/blobs/storage-blob-static-website.md).
- A [Azure app Service bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A bővítmény lehetővé teszi a webhelyek üzembe helyezését a teljes körűen felügyelt folyamatos integráció és folyamatos teljesítés (CI/CD) beállításával.
- A [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) saját kiszolgáló nélküli alkalmazások létrehozásához. A hitelesítési alkalmazást például a Azure Functions futtathatja.
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Útmutató a kommunikációs szolgáltatások erőforrásának létrehozásához](../quickstarts/create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat. Útmutatásért tekintse meg a [hozzáférési tokenek létrehozásával és kezelésével](../quickstarts/access-tokens.md?pivots=programming-language-javascript) , illetve a [megbízható hitelesítési szolgáltatás kialakítására vonatkozó oktatóanyaggal](./trusted-service-tutorial.md)kapcsolatos útmutatót.


## <a name="configure-your-development-environment"></a>A fejlesztési környezet konfigurálása

A helyi fejlesztési környezet a következőképpen lesz konfigurálva:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="A fejlesztési környezet architektúráját bemutató diagram.":::


### <a name="install-nodejs-nvm-and-npm"></a>A Node.js, a NVM és a NPM telepítése

Az ügyféloldali alkalmazáshoz szükséges különböző függőségek letöltéséhez és telepítéséhez Node.js fogunk használni. Az Azure-ban üzemeltetett statikus fájlok létrehozásához ezt fogjuk használni, így nem kell aggódnia a kiszolgálón történő konfigurálásával kapcsolatban.

A Windows-fejlesztők [ezt a Node.js oktatóanyagot](/windows/nodejs/setup-on-windows) követve konfigurálhatják a csomópontot, a NVM és a NPM. 

Ez az oktatóanyag az LTS 12.20.0-verzióján alapul. A NVM telepítése után a következő PowerShell-paranccsal telepítheti a használni kívánt verziót:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="A csomópont-verzió üzembe helyezésére vonatkozó parancsokat bemutató képernyőkép.":::

### <a name="configure-visual-studio-code"></a>A Visual Studio Code konfigurálása

A [Visual Studio Code](https://code.visualstudio.com/) -ot az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms)is letöltheti.

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Munkaterület létrehozása az Azure kommunikációs szolgáltatások projektjeihez

Hozzon létre egy mappát a projektfájlok tárolásához, például: `C:\Users\Documents\ACS\CallingApp` . A Visual Studio Code-ban válassza a **fájl**  >  **mappa hozzáadása a munkaterülethez** lehetőséget, és adja hozzá a mappát a munkaterülethez.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="A fájl munkaterülethez való hozzáadására szolgáló beállításokat bemutató képernyőkép.":::

A bal oldali ablaktáblán nyissa meg a **Explorert** , és `CallingApp` a mappa a **Névtelen** munkaterületen jelenik meg.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Képernyőkép, amely a tallózót és a névtelen munkaterületet mutatja.":::

Nyugodtan frissítheti a munkaterület nevét. A Node.js verziójának érvényesítéséhez kattintson a jobb gombbal a `CallingApp` mappára, és válassza a **Megnyitás az integrált terminálban** lehetőséget.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="A mappa egy integrált terminálban való megnyitásának kijelölését bemutató képernyőkép.":::

A terminálban adja meg a következő parancsot az előző lépésben telepített Node.js-verzió ellenőrzéséhez:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="A csomópont verziójának érvényesítését bemutató képernyőkép.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Azure-bővítmények telepítése a Visual Studio Code-hoz

Telepítse az [Azure Storage-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) a Visual Studio Marketplace-en vagy a Visual Studiocode-on keresztül (az  >    >  **Azure Storage** bővítmények megtekintése).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Képernyőkép, amely az Azure Storage bővítmény telepítésére szolgáló gombot mutatja.":::

Kövesse ugyanezen lépéseket a [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) és a [Azure app Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) bővítmények esetében.


## <a name="set-up-a-local-web-server"></a>Helyi webkiszolgáló beállítása

### <a name="create-an-npm-package"></a>NPM-csomag létrehozása

A terminálban a munkaterület mappájának elérési útjából írja be a következőt:

``` console
npm init -y
```

Ez a parancs inicializál egy új NPM-csomagot, és hozzáadja `package.json` a projekt gyökérkönyvtárához.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Képernyőkép, amely megjeleníti a J S O N csomagot.":::

A szolgáltatással kapcsolatos további dokumentációért `npm init` tekintse meg az [adott parancshoz tartozó NPM-dokumentumok lapot](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>A webpack telepítése

A [webpack](https://webpack.js.org/) használatával az Azure-ban üzembe helyezhető statikus fájlokra is becsomagolhatja a kódot. Emellett egy fejlesztői kiszolgáló is van, amelyet a hívó mintával való használatra konfigurálhat.

A (z) terminálban adja meg a következő parancsot a webpack telepítéséhez:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Ez az oktatóanyag az előző parancsban megadott verziókkal lett tesztelve. Ha megadja, `-dev` hogy a Package Manager ezt a függőséget fejlesztési célokra használja-e, nem kell szerepelnie az Azure-ban üzembe helyezett kódban.

Két új csomagot fog látni a `package.json` fájlhoz `devDependencies` . A csomagok a könyvtárba lesznek telepítve `./CallingApp/node_modules/` .

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="A webpack konfigurációját bemutató képernyőkép.":::

### <a name="configure-the-development-server"></a>A fejlesztői kiszolgáló konfigurálása

Ha statikus alkalmazást (például a `index.html` fájlt) futtat a böngészőjében, a `file://` protokollt használja. Ahhoz, hogy a NPM modulok megfelelően működjenek, szüksége lesz a HTTP protokollra helyi fejlesztési kiszolgálóként a webpack használatával.

Két konfigurációt fog létrehozni: egyet a fejlesztéshez és a másikat az éles környezethez. Az éles környezethez előkészített fájlok a minified, ami azt jelenti, hogy el fogja távolítani a fel nem használt szóközöket és karaktereket. Ez a konfiguráció olyan éles környezetekben alkalmazható, ahol a késést a lehető legkisebbre kell állítani, vagy ha a kódot el kell torzítani.

Az eszköz használatával a `webpack-merge` [webpack különböző konfigurációs fájljaival](https://webpack.js.org/guides/production/)dolgozhat.

Kezdjük a fejlesztési környezettel. Először telepítenie kell a következőt: `webpack merge` . A terminálban futtassa a következő parancsot:

```Console
npm install --save-dev webpack-merge
```

A fájlban láthatja `package.json` , hogy még egy függőség van hozzáadva a következőhöz: `devDependencies` .

Ezután hozzon létre egy nevű fájlt, `webpack.common.js` és adja hozzá a következő kódot:

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

Ezután adjon hozzá még két fájlt, egyet az egyes konfigurációkhoz:

* `webpack.dev.js`
* `webpack.prod.js`

Most módosítsa a `webpack.dev.js` fájlt úgy, hogy hozzáadja a következő kódot:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Ebben a konfigurációban általános paramétereket importál a alkalmazásból `webpack.common.js` , egyesítse a két fájlt, állítsa be a módot `development` , és konfigurálja a forrás-hozzárendelést a következőként: `inline-source-map` .

A fejlesztői mód azt jelzi, hogy a webpack nem hozza létre a fájlokat, és nem készít optimalizált éles fájlokat. A webpack- [üzemmódokról a webpack mód weboldalán](https://webpack.js.org/configuration/mode/)talál részletes dokumentációt.

A forrás-hozzárendelési beállítások a [webpack DevTool weblapon](https://webpack.js.org/configuration/devtool/#root)találhatók. A forrás-hozzárendelés beállítása megkönnyíti a böngészőn keresztüli hibakeresést.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Képernyőkép, amely a webpack konfigurálásához szükséges kódot mutatja.":::

A fejlesztői kiszolgáló futtatásához lépjen `package.json` a következő kódra, és adja hozzá az alábbi kódot `scripts` :

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="A package.jsmódosítását bemutató képernyőkép.":::

### <a name="test-the-development-server"></a>A fejlesztői kiszolgáló tesztelése

 A Visual Studio Code-ban hozzon létre három fájlt a projektben:

* `index.html`
* `app.js`
* `app.css` (nem kötelező az alkalmazás stílusához)

A kód beillesztése a következőbe `index.html` :

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="A H T M L-fájlt megjelenítő képernyőkép.":::

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
Ne felejtsen el menteni! A nem mentett fájlt fehér pont jelöli, a fájlnevek mellett az Explorerben.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="A JavaScript-kódot tartalmazó App.js fájlt megjelenítő képernyőkép.":::

Amikor megnyitja ezt a lapot, megjelenik egy riasztással megjelenő üzenet a böngésző konzolján.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Az app. css fájlt megjelenítő képernyőkép.":::

A fejlesztési konfiguráció teszteléséhez használja a következő terminál-parancsot:

```Console
npm run build:dev
```

A konzolon látható, hogy a kiszolgáló hol fut. Alapértelmezés szerint ez a érték `http://localhost:8080` . A `build:dev` parancs a korábban hozzáadott parancs `package.json` .

:::image type="content" source="./media/step-one-pic-16.png" alt-text="A fejlesztői kiszolgáló indítását bemutató képernyőkép.":::
 
Lépjen a böngésző címsorába, és tekintse meg az előző lépésekben konfigurált lapot és riasztást.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Képernyőkép a H T M L oldalról.":::
  
 
Amíg a kiszolgáló fut, módosíthatja a kódot és a kiszolgálót. A HTML-oldal automatikusan újratöltődik. 

Ezután nyissa meg a `app.js` fájlt a Visual Studio Code-ban, és törölje a következőt: `alert('Hello world alert!');` . Mentse a fájlt, és ellenőrizze, hogy a riasztás eltűnik-e a böngészőből.

A kiszolgáló leállításához futtassa a parancsot `Ctrl+C` a terminálon. A kiszolgáló elindításához adja meg `npm run build:dev` a parancsot bármikor.

## <a name="add-the-azure-communication-services-packages"></a>Az Azure kommunikációs szolgáltatások csomagjainak hozzáadása

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat a JavaScript-hez készült ügyféloldali kódtár meghívásával.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Ez a művelet hozzáadja az Azure kommunikációs szolgáltatásokat közösen, és a csomagokat a csomag függőségeiként hívja meg. Két új csomagot fog látni a `package.json` fájlhoz. További információt `npm install` az [adott parancs NPM docs lapján](https://docs.npmjs.com/cli/v6/commands/npm-install)talál.

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Az Azure kommunikációs szolgáltatások csomagjainak telepítéséhez szükséges kódot bemutató képernyőkép.":::

Ezeket a csomagokat az Azure kommunikációs szolgáltatások csapata adja meg, és tartalmazza a hitelesítést és a hívó kódtárakat. A `--save` parancs jelzi, hogy az alkalmazás az éles használatú csomagoktól függ, és `devDependencies` a `package.json` fájlba kerül. Ha éles környezetben hozza létre az alkalmazást, a csomagok szerepelni fognak a termelési kódban.


## <a name="publish-your-website-to-azure-static-websites"></a>Webhelyek közzététele az Azure-beli statikus webhelyeken

### <a name="create-a-configuration-for-production-deployment"></a>Konfiguráció létrehozása éles üzembe helyezéshez

Szúrja be a következő kódot az `webpack.prod.js` fájlba:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Ez a konfiguráció a következővel lesz egyesítve: `webpack.common.js` (hol adta meg a bemeneti fájlt, és hol tárolja az eredményeket). A konfiguráció a módot is beállítja a következőre: `production` .
 
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

:::image type="content" source="./media/step-one-pic-20.png" alt-text="A konfigurált fájlokat megjelenítő képernyőkép.":::


A terminálban futtassa a következőket:

```Console
npm run build:prod
```

A parancs létrehoz egy `dist` mappát és egy éles használatra kész `app.js` statikus fájlt. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Az éles buildet bemutató képernyőkép.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Az alkalmazás üzembe helyezése az Azure Storage-ban
 
Másolja `index.html` `app.css` a mappát a `dist` mappába.

A `dist` mappában hozzon létre egy fájlt, és nevezze el `404.html` . Másolja a következő korrektúrát a fájlba:

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

Kattintson a jobb gombbal a `dist` mappára, és válassza az **üzembe helyezés statikus webhelyre az Azure Storage használatával** lehetőséget.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Képernyőkép a kijelölésekről az Azure-ba való üzembe helyezés megkezdéséhez.":::
 
Az **előfizetés kiválasztása** területen válassza az **Azure-ba való bejelentkezés** lehetőséget (vagy **hozzon létre egy ingyenes Azure-fiókot** , ha még nem hozott létre előfizetést).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Képernyőkép, amely megjeleníti az Azure-ba való bejelentkezéshez szükséges beállításokat.":::
 
Válassza az **új Storage-fiók létrehozása**  >  **speciális** lehetőséget.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Képernyőkép, amely a Storage-fiók csoportjának létrehozásához szükséges beállításokat jeleníti meg.":::
 
Adja meg a tárolási csoport nevét.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="A fiók nevének hozzáadását bemutató képernyőkép.":::
 
Szükség esetén hozzon létre egy új erőforráscsoportot.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Képernyőkép, amely egy új erőforráscsoport létrehozásának kiválasztását mutatja be.":::
  
Ha szeretné **engedélyezni a statikus webhely üzemeltetését?**, válassza az **Igen** lehetőséget.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Képernyőfelvétel: a statikus webhelyek üzemeltetésének engedélyezése lehetőség kiválasztása.":::
  
Az **adja meg az index dokumentum nevét mezőben** fogadja el az alapértelmezett fájlnevet. A fájlt már létrehozta `index.html` .

A **404-es hiba dokumentum elérési útja** mezőbe írja be a következőt: **404.html**.  
  
Válassza ki az alkalmazás helyét. A kiválasztott hely határozza meg, hogy a rendszer melyik adathordozó-processzort fogja használni a következő hívási alkalmazásban a csoportos hívásokban. 

Az Azure kommunikációs szolgáltatás az alkalmazás helye alapján kiválasztja a média processzorát.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="A helyszínek listáját bemutató képernyőkép.":::
  
Várjon, amíg az erőforrás és a webhely létre nem jön. 
 
Válassza **a Tallózás a webhelyhez** lehetőséget.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Képernyőkép, amely az üzembe helyezés befejezését jelző üzenetet jelenít meg, és a gombra kattintva böngészhet a webhelyeken.":::
 
A böngésző fejlesztői eszközeiből ellenőrizheti a forrást, és megtekintheti az éles környezetben előkészített fájlt.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Képernyőkép a webhely forrásáról a fájlból.":::

Nyissa meg a [Azure Portal](https://portal.azure.com/#home), válassza ki az erőforráscsoportot, majd válassza ki a létrehozott alkalmazást. Ezután válassza a **Beállítások**  >  **statikus webhely** lehetőséget. Láthatja, hogy a statikus webhelyek engedélyezve vannak. Jegyezze fel az elsődleges végpontot, az index dokumentum nevét és a hiba dokumentum elérési útját.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="A statikus webhely kijelölését bemutató képernyőkép.":::

A **Blob Service** szakaszban válassza a **Tárolók** lehetőséget. A listában két tároló jön létre, egy a naplókhoz (), egy pedig a `$logs` webhely tartalmához ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="A tároló konfigurációját bemutató képernyőkép.":::

Ha megnyitja a `$web` tárolót, a Visual Studióban létrehozott és az Azure-ban üzembe helyezett fájlokat fogja látni. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Képernyőkép, amely az Azure-ba telepített fájlokat jeleníti meg.":::

Bármikor újra üzembe helyezheti az alkalmazást a Visual Studio Code-ból.

Most már készen áll az első Azure Communication Services-webalkalmazás létrehozására.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hanghívás hozzáadása az alkalmazáshoz](../quickstarts/voice-video-calling/getting-started-with-calling.md)

A következőket is érdemes lehet:

- [Csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
- [A hitelesítés ismertetése](../concepts/authentication.md)