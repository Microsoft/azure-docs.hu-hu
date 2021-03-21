---
title: Ismerkedés az Azure kommunikációs szolgáltatás felhasználói felületi keretrendszerének alapösszetevőivel
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a felhasználói felületi keretrendszer alapösszetevőit
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488176"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>Rövid útmutató: a felhasználói felületi keretrendszer alapösszetevőinek használatának első lépései

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a felhasználói felületi keretrendszer segítségével, amellyel gyorsan integrálhatja az alkalmazásaiba a kommunikációs tapasztalatokat. Ebből a rövid útmutatóból megtudhatja, hogyan integrálhatja a felhasználói felületi keretrendszer alapösszetevőit az alkalmazásba kommunikációs élmények létrehozásához. 

A felhasználói felületi keretrendszer összetevői két változatban érhetők el: alap és összetett.

- Az **Alapösszetevők** különálló kommunikációs képességeket jelentenek; Ezek az alapszintű építőelemek, amelyek segítségével összetett kommunikációs élmények hozhatók létre. 
- Az **összetett összetevők** olyan gyakori kommunikációs forgatókönyvek, amelyek **alapösszetevőkkel** és beépített építőelemek használatával lettek létrehozva, és könnyen integrálhatók az alkalmazásokba.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Az aktív LTS és a karbantartási LTS-verziók (a 12. csomópont ajánlott).
- Aktív kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](./../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívási ügyfél létrehozásához. Megtudhatja, hogyan [hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](./../access-tokens.md).

## <a name="setting-up"></a>Beállítás

A felhasználói felületi keretrendszernek egy reagáló környezetet kell beállítania. Ezt követően ezt tesszük. Ha már rendelkezik egy reagáló alkalmazással, kihagyhatja ezt a szakaszt.

### <a name="set-up-react-app"></a>Az alkalmazás reagálásának beállítása

Ehhez a rövid útmutatóhoz a Create-rereagáljanak-app sablont fogjuk használni. További információ: [Ismerkedés a reakcióval](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

A folyamat végén a mappán belül teljes alkalmazásnak kell lennie `my-app` . Ebben a rövid útmutatóban a mappában lévő fájlokat módosítjuk `src` .

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat a JavaScript-hez készült ügyféloldali kódtár meghívásával. Helyezze át a megadott Fez-t (privát előzetes verzió) a My-app könyvtárba.

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

### <a name="run-create-react-app"></a>A Create rereagáló alkalmazás futtatása

Tesztelje a Create rereagáljanak app telepítését a következők futtatásával:

```console

npm run start   

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások felhasználói felületének függvénytárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Szolgáltató| Fluent felhasználói felületi szolgáltató, amely lehetővé teszi a fejlesztők számára az alapul szolgáló Fluent felhasználói felületi összetevők módosítását|
| CallingProvider| A szolgáltató meghívása hívás létrehozásához. További összetevők hozzáadásához szükséges|
| ChatProvider | Csevegési szolgáltató egy csevegési szál létrehozásához. További összetevők hozzáadásához szükséges|
| MediaGallery | A meghívó résztvevőit és a távoli video streameket bemutató alapösszetevő |
| MediaControls | Az alapösszetevő a hívás vezérléséhez, beleértve a némítást, a videót, a megosztási képernyőt |
| ChatThread | Olyan alapösszetevő, amely egy csevegési szálat jelenít meg, és begépelési jeleket, olvasási visszaigazolásokat stb. |
| SendBox | Az alapösszetevő, amely lehetővé teszi a felhasználó számára az összekapcsolt szálba küldendő üzenetek bevitelét|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Hívó és csevegési szolgáltatók inicializálása az Azure kommunikációs szolgáltatások hitelesítő adataival

Nyissa `src` meg a mappát a belsejében, `my-app` és keresse meg a fájlt `app.js` . Itt a következő kódot fogjuk eldobni a hívási és csevegési szolgáltatók inicializálásához. Ezek a szolgáltatók felelősek a hívási és csevegési élmények kontextusának fenntartásáért. Kiválaszthatja, hogy melyiket használja az Ön által létrehozott kommunikációs élmény típusától függően. Ha szükséges, mindkettőt használhatja egyszerre. Az összetevők inicializálásához az Azure kommunikációs szolgáltatásokból beolvasott hozzáférési tokenre lesz szükség. A hozzáférési jogkivonatok beszerzésével kapcsolatos részletekért lásd: [hozzáférési tokenek létrehozása és kezelése](./../access-tokens.md).

> [!NOTE]
> Az összetevők nem hoznak hozzáférési jogkivonatokat, csoport-azonosítókat vagy szál-azonosítókat. Ezek az elemek olyan szolgáltatásokból származnak, amelyek végigvezetik az azonosítók létrehozásához szükséges lépéseken, és továbbítják azokat az ügyfélalkalmazás számára. További információkért lásd: [ügyféloldali kiszolgáló architektúrája](./../../concepts/client-and-server-architecture.md).
> 
> Például: a csevegési szolgáltató azt várja, hogy az `userId` `token` inicializáláshoz használt alkalmazás már csatlakoztatva van a megadott helyhez `threadId` . Ha a jogkivonat nincs csatlakoztatva a szál-AZONOSÍTÓhoz, akkor a csevegési szolgáltató sikertelen lesz. További információ a csevegéssel kapcsolatban: [első lépések csevegéssel](./../chat/get-started.md)

Az alkalmazás megjelenésének és működésének fejlesztéséhez egy Fluent felhasználói felületi témát fogunk használni:

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

Az inicializálást követően ez a szolgáltató lehetővé teszi saját elrendezésének összeállítását a felhasználói felületi keretrendszer alapösszetevői és a további elrendezési logikák használatával. A szolgáltató gondoskodik az összes mögöttes logika inicializálásáról és a különböző összetevők egymáshoz való csatlakoztatásáról. Ezután a felhasználói felületi keretrendszer által biztosított különböző alapösszetevőket fogjuk használni kommunikációs tapasztalatok létrehozásához. Testreszabhatja ezen összetevők elrendezését, és hozzáadhatja azokat az egyéni összetevőket, amelyeket meg szeretne jeleníteni. 

## <a name="build-ui-framework-calling-component-experiences"></a>Felhasználói felületi keretrendszer meghívása az összetevőkből

A híváshoz a és az összetevőket fogjuk használni `MediaGallery` `MediaControls` . További információt ezekről a [felhasználói felületi keretrendszer képességei](./../../concepts/ui-framework/ui-sdk-features.md)című témakörben talál. A kezdéshez a `src` mappában hozzon létre egy nevű új fájlt `CallingComponents.js` . Itt inicializálunk egy függvény-összetevőt, amely megtartja az alap összetevőket, majd importálja azokat `app.js` . Az összetevők körén kívül további elrendezést és formázást is hozzáadhat. 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

A fájl alján a hívási összetevőket a  `connectFuncsToContext` felhasználói felületi keretrendszer metódusának használatával exportálta a hívó felhasználói felület összetevőinek az alapul szolgáló állapothoz való csatlakoztatásához a leképezési függvény használatával `MapToCallingSetupProps` . Ezzel a módszerrel a rendszer kitölti a hozzá tartozó kellékeket, és ezt követően az állapotot is bejelölheti, és csatlakozhat a híváshoz. A tulajdonság használatával győződjön meg arról, hogy `isCallInitialized` a `CallAgent` készen áll-e, majd a `joinCall` módszer használatával csatlakozzon a következőhöz:. A felhasználói felületi keretrendszer támogatja az egyéni leképezési funkciókat, amelyek segítségével a fejlesztők szabályozhatják, hogy az adatok hogyan legyenek leküldve az összetevőkbe.

## <a name="build-ui-framework-chat-component-experiences"></a>Felhasználói felületi keretrendszer csevegési összetevőjének létrehozása

Csevegés esetén a és az összetevőket fogjuk használni `ChatThread` `SendBox` . További információ ezekről az összetevőkről: a [felhasználói felületi keretrendszer képességei](./../../concepts/ui-framework/ui-sdk-features.md). A kezdéshez a `src` mappában hozzon létre egy nevű új fájlt `ChatComponents.js` . Itt inicializálunk egy függvény-összetevőt, amely megtartja az alap összetevőket, majd importálja azokat `app.js` .

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>Hívó és csevegési összetevők hozzáadása a fő alkalmazáshoz

A `app.js` fájlba visszatérve most hozzáadjuk az összetevőket a és az `CallingProvider` `ChatProvider` alább láthatóhoz.

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>Gyors útmutató futtatása

A fenti kód futtatásához használja a következő parancsot:

```console

npm run start   

```

A képességek teljes körű teszteléséhez szüksége lesz egy második ügyfélre, amelyben hívási és csevegési funkciókkal csatlakozhat a híváshoz és a csevegési szálhoz. Tekintse meg a [Calling Hero minta](./../../samples/calling-hero-sample.md) és a [csevegő Hero minta](./../../samples/chat-hero-sample.md) lehetőséget a lehetséges lehetőségek közül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Felhasználói felületi keretrendszer összetett összetevőinek kipróbálása](./get-started-with-composites.md)

További információkat találhat az alábbi forrásokban:
- [A felhasználói felületi keretrendszer áttekintése](../../concepts/ui-framework/ui-sdk-overview.md)
- [Felhasználói felületi keretrendszer képességei](./../../concepts/ui-framework/ui-sdk-features.md)
