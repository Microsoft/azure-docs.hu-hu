---
title: Ismerkedés az Azure Communication Services felhasználói felületi keretrendszer SDK-val összetett összetevőivel
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a felhasználói felületi keretrendszer összetett összetevőit
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7d6b0d01ec3de1cb4ce91b1c81cd8a0ccd73b1d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105825798"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>Rövid útmutató: a felhasználói felületi keretrendszer összetett összetevőinek használatának első lépései

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Ismerkedjen meg az Azure kommunikációs szolgáltatásokkal a felhasználói felületi keretrendszer segítségével, amellyel gyorsan integrálhatja az alkalmazásaiba a kommunikációs tapasztalatokat. Ebből a rövid útmutatóból megtudhatja, hogyan integrálhatja a felhasználói felületi keretrendszer összetett összetevőit az alkalmazásba kommunikációs élmények létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Az aktív LTS és a karbantartási LTS-verziók (a 12. csomópont ajánlott).
- Aktív kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](./../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívás összetett példányának létrehozásához. Megtudhatja, hogyan [hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](./../access-tokens.md).

## <a name="setting-up"></a>Beállítás

A felhasználói felületi keretrendszernek egy reagáló környezetet kell beállítania. Ezt követően ezt tesszük. Ha már rendelkezik egy reagáló alkalmazással, kihagyhatja ezt a szakaszt.

### <a name="set-up-react-app"></a>Az alkalmazás reagálásának beállítása

Ehhez a rövid útmutatóhoz a Create-visszahat-app sablont fogjuk használni. További információ: [Ismerkedés a reakcióval](https://reactjs.org/docs/create-a-new-react-app.html)

```console

npx create-react-app my-app

cd my-app

```

A folyamat végén a mappán belül teljes alkalmazásnak kell lennie `my-app` . Ebben a rövid útmutatóban a mappában lévő fájlokat módosítjuk `src` .

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat HÍVÓ SDK-t a javascripthez. Helyezze át a megadott Fez-t (privát előzetes verzió) a My-app könyvtárba.

```console

//Private Preview install tarball

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
| GroupCall | Összetett összetevő, amely a résztvevői katalógussal és vezérlőkkel rendelkező csoportos telefonálási élményt jelenít meg. |
| Csoportos csevegés | Összetett összetevő, amely csevegési szálat és bemenetet megjelenítő csoportos csevegési élményt nyújt |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>Csoportos hívás és csoportos csevegés – összetett összetevők inicializálása

Nyissa `src` meg a mappát a belsejében, `my-app` és keresse meg a fájlt `app.js` . Itt a következő kódot fogjuk eldobni a csoportos csevegéshez és a híváshoz használt összetett összetevők inicializálásához. Kiválaszthatja, hogy melyiket használja az Ön által létrehozott kommunikációs élmény típusától függően. Ha szükséges, mindkettőt használhatja egyszerre. Az összetevők inicializálásához az Azure kommunikációs szolgáltatásokból beolvasott hozzáférési tokenre lesz szükség. A hozzáférési jogkivonatok beszerzésével kapcsolatos részletekért lásd: [felhasználói hozzáférési tokenek létrehozása és kezelése](./../access-tokens.md).

> [!NOTE]
> Az összetevők nem hoznak hozzáférési jogkivonatokat, csoport-azonosítókat vagy szál-azonosítókat. Ezek az elemek olyan szolgáltatásokból származnak, amelyek végigvezetik az azonosítók létrehozásához szükséges lépéseken, és továbbítják azokat az ügyfélalkalmazás számára. További információkért lásd: [ügyféloldali kiszolgáló architektúrája](./../../concepts/client-and-server-architecture.md).
> 
> Például: a csoportos csevegési összetett szolgáltatás azt várja, hogy a `userId` hozzá tartozó, az `token` inicializáláshoz használt alkalmazás már csatlakoztatva van a megadott helyhez `threadId` . Ha a jogkivonat nincs csatlakoztatva a szál-AZONOSÍTÓhoz, akkor a csoportos csevegési kompozit nem fog működni. További információ a csevegéssel kapcsolatban: [első lépések csevegéssel](./../chat/get-started.md)


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [Felhasználói felületi keretrendszer alapvető összetevőinek kipróbálása](./get-started-with-components.md)

További információkat találhat az alábbi forrásokban:
- [A felhasználói felületi keretrendszer áttekintése](../../concepts/ui-framework/ui-sdk-overview.md)
- [Felhasználói felületi keretrendszer képességei](./../../concepts/ui-framework/ui-sdk-features.md)
