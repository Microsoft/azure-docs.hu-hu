---
title: 'Oktatóanyag: hitelesítés Azure Functionssal – Azure-jelző'
description: Ebből az oktatóanyagból megtudhatja, hogyan hitelesítheti az Azure Signaler szolgáltatás ügyfelét Azure Functions kötéshez
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 6df47d3fd62083a5d0940a1d6da50ac5d7d955f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150905"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Oktatóanyag: Azure SignalR Service-hitelesítés az Azure Functions segítségével

Részletes oktatóanyag arról, hogyan kell egy hitelesítési és privát üzenetküldési funkciókkal rendelkező csevegőszobát felépíteni az Azure Functions, az AppService-hitelesítés és a SignalR Service segítségével.

## <a name="introduction"></a>Bevezetés

### <a name="technologies-used"></a>A használt technológiák

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Felhasználók hitelesítésére és csevegőüzenetek küldésére szolgáló háttérrendszeri API
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) – Új üzenetek küldése a csatlakoztatott csevegőügyfeleknek
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) – A csevegőügyfél felhasználói felületeként szolgáló statikus webhely üzemeltetése

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő szoftverekre van szüksége.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (10.x verzió)
* [.NET SDK](https://www.microsoft.com/net/download) (2.x verzió, a Functions-bővítmények használatához szükséges)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (2-es verzió)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code), a következő bővítményekkel:
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) – az Azure Functions használatához a VS Code-ban
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) – weblapok helyi kiszolgálása tesztelés céljából

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Az [Azure Portalon](https://portal.azure.com/) jelentkezzen be a hitelesítő adataival.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="create-an-azure-signalr-service-instance"></a>Azure SignalR Service-példány létrehozása

Azure Functions-alkalmazását helyileg fogja összeállítani és tesztelni. Az alkalmazás hozzá fog férni egy SignalR Service-példányhoz az Azure-ban, amelyet előre létre kell hozni.

1. Az új Azure-erőforrások létrehozásához kattintson az **erőforrás létrehozása** ( **+** ) gombra.

1. Keresse meg és válassza ki a **SignalR Service** elemet. Kattintson a **Létrehozás** lehetőségre.

    ![Új SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Adja meg a következő információkat:

    | Name | Érték |
    |---|---|
    | Erőforrás neve | A SignalR Service-példány egyedi neve |
    | Erőforráscsoport | Új erőforráscsoport létrehozása egyedi névvel |
    | Hely | Válasszon ki egy Önhöz közel eső helyet |
    | Tarifacsomag | Ingyenes |

1. Kattintson a **Létrehozás** lehetőségre.

1. A példány üzembe helyezése után nyissa meg a portálon, és keresse meg a beállítások lapot. Módosítsa a szolgáltatás mód beállítását *kiszolgáló* nélkülire.

    ![Jelző szolgáltatás üzemmódja](media/signalr-concept-azure-functions/signalr-service-mode.png)
    
[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="initialize-the-function-app"></a>A függvényalkalmazás inicializálása

### <a name="create-a-new-azure-functions-project"></a>Új Azure Functions-projekt létrehozása

1. Egy új VS Code ablak `File > Open Folder` menüpontjában hozzon létre és nyisson meg egy üres mappát egy alkalmas helyen. Ez lesz a létrehozni kívánt alkalmazás fő projektmappája.

1. A VS Code-ban az Azure Functions bővítmény segítségével inicializáljon egy függvényalkalmazást a fő projektmappában.
   1. A VS Code parancskatalógusának megnyitásához válassza a **View (Nézet) > Command Palette (Parancskatalógus)** elemet (billentyűparancs: `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. Keresse meg és válassza ki az **Azure Functions: Create New Project** (Azure Functions: Új projekt létrehozása) parancsot.
   1. Megjelenik a projekt főmappája. Válassza ki (vagy keresse meg tallózással).
   1. Amikor a szoftver megkérdezi, hogy melyik nyelvet kívánja használni, válassza a **JavaScript** lehetőséget.

      ![Függvényalkalmazás létrehozása](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>A függvényalkalmazás-bővítmények telepítése

Ebben az oktatóanyagban Azure Functions-kötésekkel lépünk interakcióba az Azure SignalR Service-szel. A legtöbb más kötéshez hasonlóan a SignalR Service kötései is bővítményként érhetők el, amelyeket használat előtt az Azure Functions Core Tools CLI-vel telepíteni kell.

1. Nyisson meg egy terminált a VS Code-ban. Ehhez válassza a menü **> terminál nézetét** (CTRL- \` ).

1. Gondoskodjon róla, hogy a projekt főmappája legyen az aktuális mappa.

1. Telepítse a SignalR Service függvényalkalmazás-bővítményét.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Az Azure Functions futtatókörnyezetének helyi futtatása és hibakeresése során használt alkalmazásbeállítások a **local.settings.json** fájlban találhatóak. Frissítse ezt a fájlt az előzőekben létrehozott SignalR Service-példány kapcsolati sztringjével.

1. A VS Code Explorerben válassza ki a **local.settings.json** fájlt, és nyissa meg.

1. A fájl tartalmát cserélje le a következőre:

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Adja meg az Azure SignalR Service kapcsolati sztringjét az `AzureSignalRConnectionString` beállítás értékeként. Ezt az értéket az Azure Portalon, az Azure SignalR Service erőforrás **Kulcsok** lapján találja meg. Az elsődleges és a másodlagos kapcsolati sztringek közül bármelyiket használhatja.
   * A `WEBSITE_NODE_DEFAULT_VERSION` beállítást a rendszer a helyi futtatás során nem használja. Akkor van rá szükség, amikor az Azure-ban végez üzembe helyezést.
   * A `Host` szakasz a helyi Functions-gazdagép port- és CORS-beállításait konfigurálja (Azure-beli futtatáskor nincs hatása).

       > [!NOTE]
       > Az élő kiszolgáló általában a tartalom kiszolgálására van konfigurálva `http://127.0.0.1:5500` . Ha úgy találja, hogy más URL-címet használ, vagy más HTTP-kiszolgálót használ, módosítsa a `CORS` beállítást úgy, hogy az tükrözze a megfelelő forrást.

     ![SignalR Service-kulcs megkeresése](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Mentse a fájlt.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Függvény létrehozása a felhasználók SignalR Service-ben történő hitelesítéséhez

Amikor a csevegőalkalmazás először nyílik meg a böngészőben, szüksége lesz a kapcsolat hitelesítő adataira ahhoz, hogy kapcsolódhasson az Azure SignalR Service-hez. A kapcsolódási adatok visszaküldéséhez hozzon létre egy *egyeztető* nevű http által aktivált függvényt a Function alkalmazásban.

> [!NOTE]
> Ennek a függvénynek a neve *egyeztetésnek* kell lennie, mivel a signaler-ügyfélnek olyan végpontra van szüksége, amely befejeződik `/negotiate` .

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Create Function** (parancs létrehozása) parancsot.

1. Ha a rendszer kéri, adja meg az alábbi információkat.

    | Név | Érték |
    |---|---|
    | Function app folder (Függvényalkalmazás mappája) | Válassza ki a projekt főmappáját |
    | Sablon | HTTP-eseményindító |
    | Név | negotiate |
    | Engedélyszint | Névtelen |

    Létrejön egy **egyeztető** nevű mappa, amely tartalmazza az új függvényt.

1. A függvényhez tartozó kötések konfigurálásához nyissa meg az **egyeztetés/function.json** lehetőséget. Módosítsa a fájl tartalmát az alábbiak szerint. Ez létrehoz egy bemeneti kötést, amely képes lesz olyan érvényes hitelesítő adatokat létrehozni, amelyekkel az ügyfél csatlakozhat a `chat` nevű Azure SignalR Service-központhoz.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    A rendszer a `signalRConnectionInfo` kötés `userId` tulajdonságának felhasználásával hoz létre hitelesített SignalR Service-kapcsolatot. Mivel helyi fejlesztésről van szó, a tulajdonság értékét hagyja üresen. Akkor lesz rá szükség, amikor a függvényalkalmazást az Azure-ban telepíti.

1. Nyissa meg az **egyeztetés/index.js** lehetőséget a függvény törzsének megtekintéséhez. Módosítsa a fájl tartalmát az alábbiak szerint.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    A függvény a bemeneti kötés SignalR kapcsolati adatait adja vissza az ügyfélnek a HTTP-válasz törzsében. A Signaler-ügyfél ezeket az információkat fogja használni a Signal Service-példányhoz való kapcsolódáshoz.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="create-a-function-to-send-chat-messages"></a>Függvény létrehozása csevegőüzenetek küldéséhez

A webalkalmazásnak egy HTTP API-ra is szüksége lesz, hogy csevegőüzeneteket tudjon küldeni. Létre fog hozni egy *SendMessage* nevű, HTTP által kiváltott függvényt, amely képes lesz üzeneteket küldeni a SignalR Service-hez csatlakoztatott összes ügyfélnek.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Create Function** (parancs létrehozása) parancsot.

1. Ha a rendszer kéri, adja meg az alábbi információkat.

    | Név | Érték |
    |---|---|
    | Function app folder (Függvényalkalmazás mappája) | válassza ki a projekt főmappáját |
    | Sablon | HTTP-eseményindító |
    | Név | SendMessage |
    | Engedélyszint | Névtelen |

    Létrejön egy, az új függvényt tartalmazó, **SendMessage** nevű mappa.

1. A függvény kötéseinek konfigurálásához nyissa meg a **SendMessage/function.json** fájlt. Módosítsa a fájl tartalmát az alábbiak szerint.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Az eredeti függvény két helyen változik meg:
    * Az útvonal `messages` értékre változik, és a HTTP-trigger a **POST** HTTP-metódusra korlátozódik.
    * Hozzáadja a Signaler szolgáltatás kimeneti kötését, amely a függvény által visszaadott üzenetet küld az összes olyan ügyfélnek, amely egy nevű Signal Service hub-hubhoz csatlakozik `chat` .

1. Mentse a fájlt.

1. Nyissa meg a **SendMessage/index.js** fájlt a függvénytörzs megtekintéséhez. Módosítsa a fájl tartalmát az alábbiak szerint.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Ez a függvény továbbküldi a HTTP-kérelem törzsét a SignalR Service-hez csatlakoztatott ügyfeleknek azzal, hogy minden egyes ügyfélen meghívja a `newMessage` nevű függvényt.

    A függvény képes beolvasni a küldő identitását és elfogadni az üzenet törzsében található *recipient* (címzett) nevű értéket, hogy az üzenetet privát módon, csak egy adott felhasználónak lehessen elküldeni. Ezekre a funkciókra az oktatóanyag későbbi részében lesz majd szükség.

1. Mentse a fájlt.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="create-and-run-the-chat-client-web-user-interface"></a>A csevegőügyfél webes felhasználói felületének létrehozása és futtatása

A csevegőalkalmazás felhasználói felülete egy egyszerű, a VUE JavaScript-keretrendszerrel létrehozott egyoldalas alkalmazás (single page application, SPA). Üzemeltetése a függvényalkalmazástól elválasztva történik. Helyileg a webes felületet a VS Code Live Server-bővítményével fogja futtatni.

1. A VS Code-ban hozzon létre egy új mappát **content** néven a fő projektmappa gyökerében.

1. A **content** mappában hozzon létre egy új fájlt **index.html** néven.

1. Másolja ki és illessze be az **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)** fájl tartalmát.

1. Mentse a fájlt.

1. A függvényalkalmazás helyi futtatásához és hibakereső csatlakoztatásához nyomja le az **F5** billentyűt.

1. Nyissa meg az **index.html** fájlt a VS Code-ban, majd a parancskatalógusból (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) válassza ki a **Live Server: Open with Live Server** (Live Server: Indítás Live Serverrel) menüpontot. Elindul a Live Server. A Live Server megnyitja az alkalmazást egy böngészőben.

1. Elindul az alkalmazás. A csevegőmezőbe írja be a kívánt üzenetet, majd nyomja le az ENTER billentyűt. Frissítse az alkalmazást, ha látni szeretné az új üzeneteket. Mivel hitelesítés nem lett konfigurálva, az összes üzenet feladója névtelen lesz.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="deploy-to-azure-and-enable-authentication"></a>Üzembe helyezés az Azure-ban és a hitelesítés engedélyezése

Eddig mind a függvényalkalmazást, mind a csevegőalkalmazást helyileg futtatta. Most viszont az Azure-ban fogja őket üzembe helyezni, és be fogja kapcsolni a hitelesítési és a privát üzenetküldési funkciókat.

### <a name="log-into-azure-with-vs-code"></a>Bejelentkezés az Azure-ba a VS Code-dal

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure: Sign in** (Azure: Bejelentkezés) parancsot.

1. A bejelentkezési folyamat végrehajtásához kövesse a böngészőben megjelenő utasításokat.

### <a name="create-a-storage-account"></a>Storage-fiók létrehozása

Az Azure-ban futó Function alkalmazásnak Azure Storage-fiókra van szüksége. A csevegés felhasználói felületének weblapját az Azure Storage statikus webhelyek funkciójának használatával is üzemeltetheti.

1. A Azure Portal kattintson az **erőforrás létrehozása** ( **+** ) gombra egy új Azure-erőforrás létrehozásához.

1. Válassza ki a **tárolási** kategóriát, majd válassza a **Storage-fiók** lehetőséget.

1. Adja meg a következő információkat:

    | Name | Érték |
    |---|---|
    | Előfizetés | Válassza ki a Signaler Service-példányt tartalmazó előfizetést. |
    | Erőforráscsoport | Azonos erőforráscsoport kiválasztása |
    | Erőforrás neve | A Storage-fiók egyedi neve |
    | Hely | Válassza ki ugyanazt a helyet, mint a többi erőforrást |
    | Teljesítmény | Standard |
    | Fiók altípusa | StorageV2 (általános célú v2) |
    | Replikáció | Helyileg redundáns tárolás (LRS) |
    | Hozzáférési szintek | Gyakori |

1. Kattintson a **felülvizsgálat + létrehozás**, majd a **Létrehozás** elemre.

### <a name="configure-static-websites"></a>Statikus webhelyek konfigurálása

1. A Storage-fiók létrehozása után nyissa meg a Azure Portal.

1. Válassza a **statikus webhely** lehetőséget.

1. Válassza az **engedélyezve** lehetőséget a statikus webhely funkció engedélyezéséhez.

1. Az **index dokumentum neve** mezőbe írja be *index.html* értéket.

1. Kattintson a **Mentés** gombra.

1. Megjelenik egy **elsődleges végpont** . Jegyezze fel ezt az értéket. A Function alkalmazás konfigurálására lesz szükség.

### <a name="configure-function-app-for-authentication"></a>A függvényalkalmazás konfigurálása hitelesítés használatához

Eddig a csevegőalkalmazás névtelenül működött. Az Azure-ban az [App Service-hitelesítéssel](../app-service/overview-authentication-authorization.md) fogja a felhasználókat hitelesíteni. A hitelesített felhasználó felhasználóazonosítója vagy felhasználóneve átadható a *SignalRConnectionInfo* kötésnek, amellyel a felhasználóhoz kapcsolt, hitelesítéshez használható kapcsolati adatokat lehet létrehozni.

Az alkalmazás eldöntheti, hogy az egyes üzeneteket minden csatlakoztatott ügyfélnek, vagy csak az adott felhasználó esetében hitelesített ügyfeleknek küldje-e el.

1. A VS Code-ban nyissa meg az **egyeztetés/function.jsa** következőn:.

1. A *SignalRConnectionInfo* kötés *userId* tulajdonságában adjon meg egy [kötési kifejezést](../azure-functions/functions-triggers-bindings.md): `{headers.x-ms-client-principal-name}`. Ez az értéket a hitelesített felhasználó felhasználónevére állítja be. Az attribútumnak most így kell kinéznie:

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Mentse a fájlt.


### <a name="deploy-function-app-to-azure"></a>Function alkalmazás üzembe helyezése az Azure-ban

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`) majd válassza ki az **Azure Functions: Deploy to Function App** (Azure Functions: Üzembe helyezés a függvényalkalmazásban) parancsot.

1. Ha a rendszer kéri, adja meg az alábbi információkat.

    | Név | Érték |
    |---|---|
    | Folder to deploy (Üzembe helyezni kívánt mappa) | Válassza ki a projekt főmappáját |
    | Előfizetés | Az előfizetés kiválasztása |
    | Függvényalkalmazás | Válassza ki a **Create New Function App** (Új függvényalkalmazás létrehozása) elemet |
    | Függvényalkalmazás neve | Adjon meg egy egyedi nevet |
    | Erőforráscsoport | Azt az erőforráscsoportot válassza ki, amelyikbe a SignalR Service-példány is tartozik. |
    | A(z) | Válassza ki a korábban létrehozott Storage-fiókot |

    Az Azure-ban létrejön egy új függvényalkalmazás, és megindul az üzembehelyezési folyamat. Várjon, amíg az üzembe helyezés befejeződik.

### <a name="upload-function-app-local-settings"></a>A függvényalkalmazás helyi beállításainak feltöltése

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Upload local settings** (Azure Functions: Helyi beállítások feltöltése) parancsot.

1. Ha a rendszer kéri, adja meg az alábbi információkat.

    | Név | Érték |
    |---|---|
    | Local settings file (Helyi beállításfájl) | local.settings.json |
    | Előfizetés | Az előfizetés kiválasztása |
    | Függvényalkalmazás | Válassza ki az előbb telepített függvényalkalmazást |

A rendszer feltölti a helyi beállításokat az Azure-beli függvényalkalmazásba. A **Yes to all** (Igen, mindet) lehetőséggel írhatja felül a létező beállításokat, ha szükség van rá.


### <a name="enable-app-service-authentication"></a>Az App Service-hitelesítés engedélyezése

Az App Service-hitelesítés a Microsoft-fiókkal végzett, illetve az Azure Active Directory-, Facebook-, Twitter- és Google-hitelesítést támogatja.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Functions: Open in portal** (Azure Functions: Megnyitás a portálon) parancsot.

1. Válassza ki az előfizetés és a függvényalkalmazás nevét az alkalmazás megnyitásához az Azure Portalon.

1. A portálon megnyitott Function alkalmazásban keresse meg a **platform szolgáltatásai** lapot, majd válassza a **hitelesítés/engedélyezés** lehetőséget.

1. Kapcsolja **Be** az App Service-hitelesítést.

1. Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a „Bejelentkezés a következővel: {előzőleg kiválasztott hitelesítési szolgáltató}” lehetőséget.

1. Az **Engedélyezett külső átirányítási URL-címek** mezőben adja meg az előző lépésben a tárfiók létrehozásakor feljegyzett elsődleges webes végpontot.

1. A konfigurációs folyamat befejezéséhez kövesse a kiválasztott hitelesítési szolgáltató dokumentációját.

    - [Azure Active Directory](../app-service/configure-authentication-provider-aad.md)
    - [Facebook](../app-service/configure-authentication-provider-facebook.md)
    - [Twitter](../app-service/configure-authentication-provider-twitter.md)
    - [Microsoft-fiók](../app-service/configure-authentication-provider-microsoft.md)
    - [Google](../app-service/configure-authentication-provider-google.md)

### <a name="update-the-web-app"></a>A webalkalmazás frissítése

1. Nyissa meg a függvényalkalmazás áttekintő lapját az Azure Portalon.

1. Másolja ki a függvényalkalmazás URL-címét.

    ![URL-cím helye](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. A VS Code-ban nyissa meg az **index.html** fájlt, és cserélje le az `apiBaseUrl` értéket a függvényalkalmazás URL-címére.

1. Az alkalmazás Microsoft-fiókkal végzett, illetve Azure Active Directory-, Facebook-, Twitter- vagy Google-hitelesítések használatára konfigurálható be. Az `authProvider` érték beállításával válassza ki a használni kívánt hitelesítési szolgáltatót.

1. Mentse a fájlt.

### <a name="deploy-the-web-application-to-blob-storage"></a>A webalkalmazás üzembe helyezése blobtárolón

A webalkalmazást az Azure Blob Storage statikuswebhely-szolgáltatásával fogjuk üzemeltetni.

1. Nyissa meg a VS Code parancskatalógusát (`Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).

1. Keresse meg és válassza ki az **Azure Storage: Üzembe helyezés statikus webhelyre** parancsot.

1. Írja be a következő értékeket:

    | Name | Érték |
    |---|---|
    | Előfizetés | Az előfizetés kiválasztása |
    | A(z) | Válassza ki a korábban létrehozott Storage-fiókot |
    | Folder to deploy (Üzembe helyezni kívánt mappa) | Válassza a **Tallózás** lehetőséget, és válassza ki a *tartalom* mappát. |

A *tartalom* mappában lévő fájlokat most telepíteni kell a statikus webhelyre.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Eltérő eredetű erőforrások megosztásának (CORS) engedélyezése a függvényalkalmazásokban

Bár a **local.settings.json** fájlban lehetőség van a CORS beállítására, ez nem érvényesül az Azure-ban futó függvényalkalmazásban. A CORS-t külön kell beállítania.

1. Nyissa meg a függvényalkalmazást az Azure Portalon.

1. A **platform szolgáltatásai** lapon válassza a **CORS** lehetőséget.

    ![A CORS helye](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. Az *engedélyezett eredetek* szakaszban adjon hozzá egy bejegyzést a statikus webhely *elsődleges végpontja* értékként (a záró eltávolítása */* ).

1. Ahhoz, hogy a Signaler JavaScript SDK meghívja a Function alkalmazást egy böngészőben, engedélyezni kell a hitelesítő adatok támogatását a CORS. Jelölje be a "hozzáférés-vezérlés engedélyezése – hitelesítő adatok engedélyezése" jelölőnégyzetet.

    ![Hozzáférés-vezérlés – engedélyezés – hitelesítő adatok engedélyezése](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Kattintson a **Mentés** gombra a CORS-beállítások mentéséhez.

### <a name="try-the-application"></a>Az alkalmazás kipróbálása

1. A böngészőben nyissa meg a tárfiók elsődleges webes végpontját.

1. Válassza a **Bejelentkezést**, hogy hitelesíthesse magát a kiválasztott hitelesítési szolgáltatóval.

1. Küldjön nyilvános üzeneteket a fő csevegőmezőből.

1. Privát üzenetek küldéséhez kattintson a csevegési előzményekben egy felhasználónévre. Ezeket az üzeneteket csak a kijelölt címzett fogja megkapni.

Gratulálunk! Üzembe helyezett egy valós idejű, kiszolgáló nélküli csevegőalkalmazást!

![Bemutató](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag elvégzése során létrehozott erőforrásokat, törölje az erőforráscsoportot az Azure Portalon.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból elsajátította, hogyan használható az Azure Functions az Azure SignalR Service szolgáltatással. További információkat is találhat arról, hogyan lehet valós idejű, kiszolgáló nélküli alkalmazásokat létrehozni az Azure Functions SignalR Service-kötéseivel.

> [!div class="nextstepaction"]
> [Valós idejű alkalmazások létrehozása Azure Functions](signalr-concept-azure-functions.md)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/asrs/qsauth)