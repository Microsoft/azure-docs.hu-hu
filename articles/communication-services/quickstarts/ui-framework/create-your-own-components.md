---
title: Saját FELHASZNÁLÓIFELÜLET-keretrendszer összetevő létrehozása
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan egyéni összetevőt, amely kompatibilis a felhasználói felületi keretrendszerrel
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 798b898f6e2367e3096bcf985715fd48de3d394f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105930217"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>Rövid útmutató: saját FELHASZNÁLÓIFELÜLET-keretrendszer összetevő létrehozása

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a felhasználói felületi keretrendszer segítségével, amellyel gyorsan integrálhatja az alkalmazásaiba a kommunikációs tapasztalatokat.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre saját összetevőket a felhasználói felületi keretrendszer által kínált előre meghatározott állapot felület használatával. Ez a megközelítés ideális azoknak a fejlesztőknek, akiknek több testreszabásra van szükségük, és a saját tervezési eszközeiket szeretnék használni a felhasználói élményhez. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Az aktív LTS és a karbantartási LTS-verziók (a 12. csomópont ajánlott).
- Aktív kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](./../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívási ügyfél létrehozásához. Megtudhatja, hogyan [hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](./../access-tokens.md).

A felhasználói felületi keretrendszerhez szükség van egy reagáló környezet beállítására. Ezt követően ezt tesszük. Ha már rendelkezik egy reagáló alkalmazással, kihagyhatja ezt a szakaszt.

### <a name="set-up-react-app"></a>Az alkalmazás reagálásának beállítása

Ehhez a rövid útmutatóhoz a Create-rereagáljanak-app sablont fogjuk használni. További információ: [Ismerkedés a reakcióval](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

A folyamat végén egy teljes alkalmazásnak kell lennie a mappán belül `my-app` . Ebben a rövid útmutatóban a mappában lévő fájlokat módosítjuk `src` .

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat HÍVÓ SDK-t a javascripthez. Helyezze át a megadott Fez-t (privát előzetes verzió) a My-app könyvtárba.

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

A következő osztályok és felületek az Azure kommunikációs szolgáltatások felhasználói felületi SDK főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| Szolgáltató| Fluent felhasználói felületi szolgáltató, amely lehetővé teszi a fejlesztők számára az alapul szolgáló Fluent felhasználói felületi összetevők módosítását|
| CallingProvider| A szolgáltató meghívása hívás létrehozásához. Alapvető összetevők hozzáadásához szükséges|
| ChatProvider | Csevegési szolgáltató egy csevegési szál létrehozásához. Alapvető összetevők hozzáadásához szükséges|
| connectFuncsToContext | Felhasználói felületi keretrendszer összetevőinek összekapcsolására szolgáló metódus a leképezéseket használó mögöttes szolgáltatók használatával |
| MapToChatMessageProps | Csevegési üzenetek adatrétegének leképezése, amely a csevegő üzenetekkel rendelkező összetevőket biztosít |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Csevegési szolgáltatók inicializálása az Azure kommunikációs szolgáltatások hitelesítő adataival

Ebben a rövid útmutatóban a csevegés példáját fogjuk használni a hívással kapcsolatban: az [Alapösszetevők](./get-started-with-components.md) gyors üzembe helyezése és az [összetett összetevők](./get-started-with-composites.md)gyors üzembe helyezése.

Nyissa `src` meg a mappát a belsejében, `my-app` és keresse meg a fájlt `app.js` . Itt a csevegési szolgáltató inicializálásához a következő kódot fogjuk eldobni. Ez a szolgáltató a hívási és csevegési élmények kontextusának fenntartásáért felelős. Az összetevők inicializálásához az Azure kommunikációs szolgáltatásokból beolvasott hozzáférési tokenre lesz szükség. A hozzáférési token beszerzésével kapcsolatos részletekért lásd: [hozzáférési tokenek létrehozása és kezelése](./../access-tokens.md).

A felhasználói felületi keretrendszer összetevői ugyanazt az általános architektúrát követik, mint a szolgáltatás többi része. Az összetevők nem hoznak hozzáférési jogkivonatokat, csoport-azonosítókat vagy szál-azonosítókat. Ezek az elemek olyan szolgáltatásokból származnak, amelyek végigvezetik az azonosítók létrehozásához szükséges lépéseken, és továbbítják azokat az ügyfélalkalmazás számára. További információ: [ügyféloldali kiszolgáló architektúrája](./../../concepts/client-and-server-architecture.md).

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

Az inicializálást követően ez a szolgáltató lehetővé teszi saját elrendezésének összeállítását a felhasználói felületi keretrendszer összetevővel és bármely további elrendezési logikával. A szolgáltató gondoskodik az összes mögöttes logika inicializálásáról és a különböző összetevők egymáshoz való csatlakoztatásáról. A következő lépésben létrehozunk egy egyéni összetevőt a felhasználói felületi keretrendszer mappers használatával a csevegési szolgáltatóhoz való kapcsolódáshoz.


## <a name="create-a-custom-component-using-mappers"></a>Egyéni összetevő létrehozása a mappers használatával

Először létre kell hozni egy új fájlt, `SimpleChatThread.js` ahol létrehozjuk az összetevőt. Először importáljuk a felhasználói felületi keretrendszer összetevőit, amelyekre szükségünk lesz. Itt kihasználjuk a HTML Box-t, és reagálunk arra, hogy egy egyszerű csevegési szálhoz hozzon létre egy teljesen egyéni összetevőt. A metódus használatával a leképezést a `connectFuncsToContext` `MapToChatMessageProps` kellékek egyéni összetevőkre való leképezéséhez fogjuk használni  `SimpleChatThread` . Ezek a kellékek hozzáférést biztosítanak a küldött és fogadott csevegési üzenetekhez az egyszerű szálon való feltöltéshez.

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>Egyéni összetevő hozzáadása az alkalmazáshoz

Most, hogy elkészült az egyéni összetevőnk, importáljuk és felvesszük az elrendezésbe.

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>Gyors útmutató futtatása

A fenti kód futtatásához használja a következő parancsot:

```console

npm run start   

```

A képességek teljes körű teszteléséhez egy csevegési funkcióval rendelkező második ügyfélre lesz szüksége az egyszerű csevegési szál által fogadott üzenetek küldéséhez. Tekintse meg a [Calling Hero minta](./../../samples/calling-hero-sample.md) és a [csevegő Hero minta](./../../samples/chat-hero-sample.md) lehetőséget a lehetséges lehetőségek közül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Felhasználói felületi keretrendszer összetett összetevőinek kipróbálása](./get-started-with-composites.md)

További információkat találhat az alábbi forrásokban:
- [A felhasználói felületi keretrendszer áttekintése](../../concepts/ui-framework/ui-sdk-overview.md)
- [Felhasználói felületi keretrendszer képességei](./../../concepts/ui-framework/ui-sdk-features.md)
- [Felhasználói felületi keretrendszer alapösszetevői – rövid útmutató](./get-started-with-components.md)
