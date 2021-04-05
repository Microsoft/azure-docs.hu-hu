---
title: Node.js alkalmazások konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy Node.js alkalmazást a natív Windows-példányokban, vagy egy előre elkészített Linux-tárolóban, Azure App Service. A cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095202"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Node.js alkalmazás konfigurálása Azure App Servicehoz

Node.js alkalmazásokat az összes szükséges NPM-függőséggel telepíteni kell. A App Service üzembe helyezési motor automatikusan elindul, `npm install --production` Amikor git- [tárházat](deploy-local-git.md)telepít, vagy egy olyan [ZIP-csomagot](deploy-zip.md) , amelyen engedélyezve van az Build Automation. Ha az [FTP/S](deploy-ftp.md)használatával helyezi üzembe a fájlokat, akkor manuálisan kell feltöltenie a szükséges csomagokat.

Ez az útmutató a App Service üzembe helyezéséhez Node.js fejlesztőknek szóló főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta a Azure App Servicet, először kövesse a [Node.js](quickstart-nodejs.md) rövid útmutató és a [Node.js a MongoDB oktatóanyagot](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Node.js verzió megjelenítése

::: zone pivot="platform-windows"  

Az aktuális Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Az összes támogatott Node.js-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Az aktuális Node.js verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Az összes támogatott Node.js-verzió megjelenítéséhez futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Node.js verziójának beállítása

::: zone pivot="platform-windows"  

Ha az alkalmazást egy [támogatott Node.js verzióra](#show-nodejs-version)szeretné beállítani, futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com) `WEBSITE_NODE_DEFAULT_VERSION` egy támogatott verzióra való beállításához:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Ezzel a beállítással adható meg a használni kívánt Node.js-verzió, és az automatikus csomagok visszaállításakor App Service Build Automation során.

> [!NOTE]
> Állítsa be a Node.js verziót a projektben `package.json` . Az üzembe helyezési motor egy külön folyamatban fut, amely az összes támogatott Node.js verziót tartalmazza.

::: zone-end

::: zone pivot="platform-linux"

Ha az alkalmazást egy [támogatott Node.js verzióra](#show-nodejs-version)szeretné beállítani, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Ezzel a beállítással adható meg a használni kívánt Node.js-verzió, a kudu-ben pedig az automatizált csomagok visszaállítása során.

> [!NOTE]
> Állítsa be a Node.js verziót a projektben `package.json` . Az üzembe helyezési motor egy különálló tárolóban fut, amely az összes támogatott Node.js verziót tartalmazza.

::: zone-end

## <a name="get-port-number"></a>Portszám beolvasása

Node.js alkalmazásnak meg kell hallgatni a megfelelő portot a bejövő kérések fogadásához.

::: zone pivot="platform-windows"  

A Windows App Serviceban Node.js alkalmazások a [IISNode](https://github.com/Azure/iisnode)szolgáltatásban futnak, és a Node.js alkalmazásnak a változóban megadott portot kell figyelnie `process.env.PORT` . Az alábbi példa bemutatja, hogyan végezheti el egy egyszerű expressz alkalmazásban:

::: zone-end

::: zone pivot="platform-linux"  

App Service beállítja a környezeti változót `PORT` a Node.js tárolóban, és továbbítja a beérkező kéréseket a tárolóba az adott portszámon. A kérések fogadásához az alkalmazásnak a használatával kell figyelnie a portot `process.env.PORT` . Az alábbi példa bemutatja, hogyan végezheti el egy egyszerű expressz alkalmazásban:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>A Build Automation testreszabása

Ha a Build Automation használatával git vagy zip csomagok segítségével helyezi üzembe az alkalmazást, akkor a App Service az alábbi lépésekkel hozhat létre automatizálási lépéseket:

1. Futtassa az egyéni parancsfájlt, ha a meg van adva `PRE_BUILD_SCRIPT_PATH` .
1. Futtasson `npm install` jelzők nélkül, amely tartalmazza a NPM `preinstall` és a `postinstall` parancsfájlokat, valamint telepíti is `devDependencies` .
1. Futtatás `npm run build` , ha a *package.jsa* létrehozási parancsfájl van megadva.
1. Futtatás `npm run build:azure` , ha Build: Azure-szkript van megadva a *package.json*.
1. Futtassa az egyéni parancsfájlt, ha a meg van adva `POST_BUILD_SCRIPT_PATH` .

> [!NOTE]
> A NPM- [dokumentumok](https://docs.npmjs.com/misc/scripts), a (z) és a (z `prebuild` ) és után futtatott szkriptek, ha meg vannak `postbuild` `build` adva. `preinstall` és `postinstall` Futtatás előtt és után is `install` .

`PRE_BUILD_COMMAND``POST_BUILD_COMMAND`a és a környezeti változók, amelyek alapértelmezés szerint üresek. Az előkészítő parancsok futtatásához adja meg a következőt: `PRE_BUILD_COMMAND` . A létrehozás utáni parancsok futtatásához adja meg a következőt: `POST_BUILD_COMMAND` .

A következő példa a két változót adja meg egy több parancshoz, vesszővel elválasztva.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

További környezeti változók a Build Automation testreszabásához: [Oryx-konfiguráció](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

A Node.js alkalmazások Linux rendszeren való futtatásáról és App Serviceáról további információt a [Oryx dokumentációjában talál: Hogyan történik a Node.js alkalmazások észlelése és felépítése](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Node.js kiszolgáló konfigurálása

A Node.js tárolók a [PM2](https://pm2.keymetrics.io/), a Production Process Manager szolgáltatással származnak. Beállíthatja, hogy az alkalmazás a PM2 vagy a NPM, vagy egy egyéni paranccsal induljon el.

- [Egyéni parancs futtatása](#run-custom-command)
- [NPM-indítás futtatása](#run-npm-start)
- [Futtatás a PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Egyéni parancs futtatása

App Service elindíthatja az alkalmazást egy egyéni parancs használatával, például egy végrehajtható fájlt (például *Run.sh*). A futtatásához például `npm run start:prod` futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>NPM-indítás futtatása

Az alkalmazás használatának megkezdéséhez `npm start` csak ellenőrizze, hogy `start` van-e parancsfájl a fájl *package.js* . Például:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Ha egyéni *package.js* szeretne használni a projektben, futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Futtatás a PM2

A tároló automatikusan elindítja az alkalmazást a PM-ben, ha a projektben megtalálható a közös Node.js fájlok egyike:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- A következő PM2- [fájlok](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file)egyike: *process.js* és *ecosystem.config.js*

Egyéni indítási fájlt is beállíthat a következő kiterjesztésekkel:

- Egy *. js* -fájl
- Egy *. JSON* kiterjesztésű [PM2-fájl](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) , *.config.js*, *. YAML* vagy *. YML*

Egyéni indítási fájl hozzáadásához futtassa a következő parancsot a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Távoli hibakeresés

> [!NOTE]
> A távoli hibakeresés jelenleg előzetes verzióban érhető el.

A [Visual Studio Code](https://code.visualstudio.com/) -ban távolról is hibakeresést végezhet a Node.js alkalmazásban, ha úgy konfigurálja, hogy a PM2-mel [fusson](#run-with-pm2), kivéve ha * .config.js, *. YML vagy *. YAML* használatával futtatja.

A legtöbb esetben nincs szükség további konfigurációra az alkalmazáshoz. Ha az alkalmazás egy *process.js* fájllal (alapértelmezett vagy egyéni) fut, akkor `script` a JSON-gyökérben tulajdonsággal kell rendelkeznie. Például:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Ha a Visual Studio Code-ot szeretné beállítani a távoli hibakereséshez, telepítse az [app Service-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Kövesse a bővítmény lapon megjelenő utasításokat, és jelentkezzen be az Azure-ba a Visual Studio Code-ban.

Az Azure Explorerben keresse meg a hibakereséshez használni kívánt alkalmazást, kattintson rá a jobb gombbal, és válassza a **távoli hibakeresés indítása** lehetőséget. Az alkalmazás engedélyezéséhez kattintson az **Igen** gombra. App Service elindít egy bújtatási proxyt, és csatolja a hibakeresőt. Ezután megteheti a kérelmeket az alkalmazásnak, és megtekintheti a hibakereső szüneteltetését a megszakítási pontokon.

Miután végzett a hibakereséssel, állítsa le a hibakeresőt a **kapcsolat bontása** lehetőség kiválasztásával. Ha a rendszer kéri, kattintson az **Igen** gombra a távoli hibakeresés letiltásához. Ha később szeretné letiltani, kattintson a jobb gombbal az alkalmazásra az Azure Explorerben, és válassza a **távoli hibakeresés letiltása** lehetőséget.

::: zone-end

## <a name="access-environment-variables"></a>Hozzáférés a környezeti változókhoz

Az App Service-szel az alkalmazás kódján kívül is [megadhatja az alkalmazások beállításait](configure-common.md). Ezután a standard Node.js mintával érheti el őket. Például egy `NODE_ENV` nevű alkalmazásbeállítás hozzáféréséhez használja a következő kódot:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Morog/Bower/Nyelő futtatása

Alapértelmezés szerint a App Service Build Automation akkor fut, `npm install --production` Amikor felismeri, hogy a Node.js alkalmazást a git vagy a zip üzembe helyezése lehetővé teszi a Build Automation használatával. Ha az alkalmazáshoz a népszerű Automation-eszközök, például a morog, a Bower vagy a Nyelő szükséges, egy [egyéni telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) kell megadnia a futtatásához.

Ha engedélyezni szeretné, hogy a tárház futtassa ezeket az eszközöket, hozzá kell adnia azokat a függőségekhez *package.json.* Például:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Egy helyi terminál ablakban váltson át a tárház gyökerére, és futtassa a következő parancsokat:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A tárház gyökerének most két további fájlja van: *. Deployment* és *Deploy.sh*.

Nyissa meg a *Deploy.sh* , és keresse meg az `Deployment` alábbihoz hasonló szakaszt:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ez a szakasz a futtatásával végződik `npm install --production` . Adja hozzá a Code *(kód)* szakaszt, amelyen futtatnia kell a szükséges eszközt a `Deployment` szakasz végén:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Tekintse [meg a MEAN.js minta példáját](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ahol az üzembe helyezési parancsfájl egy egyéni parancsot is futtat `npm install` .

### <a name="bower"></a>Bower

Ez a kódrészlet fut `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ez a kódrészlet fut `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ez a kódrészlet fut `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS-munkamenet észlelése

App Service az [SSL-megszakítás](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztó esetében történik, így minden HTTPS-kérelem titkosítatlan http-kérésként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy a felhasználói kérések titkosítva vannak-e, vagy sem, vizsgálja meg a `X-Forwarded-Proto` fejlécet.

A népszerű webes keretrendszerek lehetővé teszik a `X-Forwarded-*` szabványos alkalmazási mintában lévő információk elérését. Az [Express](https://expressjs.com/)-ben [megbízhatósági proxykat](https://expressjs.com/guide/behind-proxies.html)használhat. Például:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Monitorozás az Application Insights segítségével

Application Insights lehetővé teszi az alkalmazás teljesítményének, kivételének és használatának figyelését a kód módosítása nélkül. Az alkalmazás-betekintő ügynök csatlakoztatásához nyissa meg a webalkalmazást a portálon, és válassza a **Application Insights** lehetőséget a **Beállítások** területen, majd válassza **a Application Insights bekapcsolása** elemet. Ezután válasszon ki egy meglévő alkalmazás-keresési erőforrást, vagy hozzon létre egy újat. Végül válassza az **alkalmaz** elemet az alján. Ha a webalkalmazást a PowerShell használatával szeretné kiépíteni, tekintse meg [ezeket az utasításokat](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell)

Ez az ügynök figyeli a kiszolgálóoldali Node.js alkalmazást. Az ügyféloldali JavaScript figyeléséhez [adja hozzá a JavaScript SDK-t a projekthez](../azure-monitor/app/javascript.md). 

További információ: [Application Insights Extension kibocsátási megjegyzései](../azure-monitor/app/web-app-extension-release-notes.md).

::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

Ha egy működő Node.js alkalmazás másképp viselkedik App Service vagy hibákat tartalmaz, próbálkozzon a következőkkel:

- [A log stream elérése](#access-diagnostic-logs).
- Az alkalmazás helyi tesztelése éles módban. A App Service éles módban futtatja Node.js-alkalmazásait, ezért gondoskodnia kell arról, hogy a projekt a várt módon működjön a helyi üzemi módban. Például:
    - A *package.jstól* függően különböző csomagok is telepíthetők üzemi módba (vagy `dependencies` `devDependencies` ).
    - Bizonyos webes keretrendszerek eltérő üzemi módban telepíthetnek statikus fájlokat.
    - Bizonyos webes keretrendszerek éles módban történő futtatáskor egyéni indítási parancsfájlokat is használhatnak.
- Az alkalmazást App Service fejlesztési módban futtathatja. [MEAN.js](https://meanjs.org/)például beállíthatja, hogy az alkalmazás a futtatókörnyezet fejlesztési módjára állítsa be [az `NODE_ENV` alkalmazás beállításait](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Node.js alkalmazás a MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service a Linuxon – gyakori kérdések](faq-app-service-linux.md)

::: zone-end
