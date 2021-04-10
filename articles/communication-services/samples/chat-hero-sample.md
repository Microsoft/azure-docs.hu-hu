---
title: Csoportos csevegési minta
titleSuffix: An Azure Communication Services sample overview
description: Az Azure kommunikációs szolgáltatásokat használó csevegési Hero-minta áttekintése, amely lehetővé teszi a fejlesztők számára, hogy többet tudjanak megtudni a minta belső működéséről, és megtudja, hogyan módosíthatja azt.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 59bfac3df1a1ba8a466b9cdc5197ede7332ed758
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728555"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Ismerkedés a Group chat Hero-mintával

> [!IMPORTANT]
> [Ez a minta a GitHubon érhető el.](https://github.com/Azure-Samples/communication-services-web-chat-hero)


Az Azure kommunikációs szolgáltatások **csoport csevegési hőse minta** azt mutatja be, hogyan használható a kommunikációs szolgáltatások csevegési web SDK egy csoportos hívási élmény létrehozásához.

Ebből a rövid útmutatóból megtudhatja, hogyan működik a minta a minta helyi gépen való futtatása előtt. Ezután üzembe helyezzük a mintát az Azure-ban a saját Azure kommunikációs szolgáltatások erőforrásaival.


## <a name="overview"></a>Áttekintés

A mintában ügyféloldali alkalmazás és kiszolgálóoldali alkalmazás is található. Az **ügyféloldali alkalmazás** egy reagáló/Redux webalkalmazás, amely a Microsoft Fluent felhasználói felületének keretrendszerét használja. Ez az alkalmazás olyan ASP.NET Core **kiszolgálóoldali alkalmazásnak** küld kérelmeket, amely segítséget nyújt az ügyféloldali alkalmazásnak az Azure-hoz való kapcsolódáshoz.

A minta így néz ki:

:::image type="content" source="./media/chat/landing-page.png" alt-text="A minta alkalmazás kezdőlapját ábrázoló képernyőfelvétel":::

A "csevegés indítása" gomb megnyomásakor a webalkalmazás a kiszolgálóoldali alkalmazásból lekéri a felhasználói hozzáférési tokent. Ezt a tokent ezután az ügyfélalkalmazás Azure kommunikációs szolgáltatásokhoz való összekapcsolására használják. A jogkivonat lekérése után meg kell adnia a nevét és a Emoji, amely a csevegésben fog megjelenni.

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Az alkalmazás csevegés előtti képernyőjét ábrázoló képernyőkép.":::

A megjelenítendő név és a Emoji beállítása után csatlakozhat a csevegési munkamenethez. Most megjelenik a fő csevegési vászon, ahol az alapvető csevegési élmény él.

:::image type="content" source="./media/chat/main-app.png" alt-text="A minta alkalmazás fő képernyőjét bemutató képernyőkép.":::

A fő csevegési képernyő összetevői:

- **Fő csevegési felület**: ez az alapvető csevegési élmény, amellyel a felhasználók küldhetnek és kaphatnak üzeneteket. Üzenetek küldéséhez használhatja a beviteli körzetet, és nyomja le az ENTER billentyűt (vagy használja a Küldés gombot). A fogadott csevegési üzeneteket a küldő a helyes névvel és Emoji kategorizálja. A csevegési terület két típusú értesítést fog látni: 1) értesítéseket írhat be, amikor a felhasználó begépeli és 2) az üzenetek küldésére és olvasására vonatkozó értesítéseket.
- **Fejléc**: ez az a hely, ahol a felhasználó látni fogja a csevegési szál címét, valamint a résztvevő és a beállítások oldalsó sávjának a vezérlését, valamint a csevegési munkamenetből való kilépéshez szükséges vezérlőket.
- **Oldalsó sáv**: ebben az esetben a rendszer a fejlécben szereplő vezérlők használatával jeleníti meg a résztvevőket és a beállítási információkat. A résztvevők oldalsó sáv tartalmazza a csevegés résztvevőinek listáját, valamint egy hivatkozást, amely meghívja a résztvevőket a csevegési munkamenetbe. A beállítások oldal sáv lehetővé teszi a csevegési szál címének konfigurálását.

Az alábbiakban további információkat talál a minta beállításához szükséges előfeltételekről és lépésekről.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- [Node.js (8.11.2 és újabb)](https://nodejs.org/en/download/)
- [Visual Studio (2017 vagy újabb)](https://visualstudio.microsoft.com/vs/)
- [.Net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Ügyeljen arra, hogy a Visual Studio-példánynak megfelelő verziót telepítse, 32 vs 64 bit)
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../quickstarts/create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell jegyeznie az erőforrás- **kapcsolódási karakterláncot** .

## <a name="locally-deploying-the-service--client-app"></a>A szolgáltatás helyileg történő üzembe helyezése & ügyfélalkalmazás

Az egyszálas csevegési minta lényegében két "alkalmazás" ügyfél-és kiszolgálóalkalmazás.

Nyissa meg a Visual studiót a chat. csproj és futtassa hibakeresési módban, Ezzel elindítja a csevegési előtér-szolgáltatást. Amikor a Server alkalmazást meglátogatják a böngészőből, a rendszer átirányítja a forgalmat a helyileg telepített csevegési előtér-szolgáltatás felé.

A mintát helyileg is tesztelheti úgy, hogy több böngészős munkamenetet nyit meg a csevegés URL-címével, hogy szimulálja a többfelhasználós csevegést.

## <a name="before-running-the-sample-for-the-first-time"></a>A minta első futtatása előtt

1. Nyisson meg egy PowerShell-példányt, a Windows Terminalt, a parancssort vagy a megfelelőt, és navigáljon ahhoz a könyvtárhoz, amelyet a minta klónozására szeretne használni.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Szerezze be a `Connection String` Azure Portal. A kapcsolati karakterláncokkal kapcsolatos további információkért lásd: [Azure kommunikációs erőforrások létrehozása](../quickstarts/create-communication-resource.md)
4. A `Connection String` hozzáadását követően adja hozzá a kapcsolódási karakterláncot a csevegés mappájában található **appsettings.js** fájlhoz. Adja meg a kapcsolatok sztringjét a (z) változóban: `ResourceConnectionString` .

### <a name="local-run"></a>Helyi futtatás

1. Lépjen a csevegés mappára, és nyissa meg a `Chat.csproj` megoldást a Visual Studióban
2. Futtassa a projektet. A böngésző a localhost: 5000 címen nyílik meg.

#### <a name="troubleshooting"></a>Hibaelhárítás

- A megoldás nem épül fel, hibákba ütközik a NPM telepítése/készítése során

   A C# megoldás tisztítása/újraépítése

## <a name="publish-the-sample-to-azure"></a>A minta közzététele az Azure-ban

1. Kattintson a jobb gombbal a `Chat` projektre, és válassza a közzététel lehetőséget.
2. Hozzon létre egy új közzétételi profilt, és válassza ki az Azure-előfizetését.
3. A közzététel előtt adja hozzá a (z `Edit App Service Settings` ) és a (z), és adja meg a `ResourceConnectionString` (z) kulcsot, és adja meg a (appsettings.json) értéket a (z) értékként.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

>[!div class="nextstepaction"]
>[A minta letöltése a GitHubról](https://github.com/Azure-Samples/communication-services-web-chat-hero)

További információért tekintse át a következő cikkeket:

- Tudnivalók a [csevegési fogalmakról](../concepts/chat/concepts.md)
- Ismerkedjen meg a [csevegő SDK](../concepts/chat/sdk-features.md) -val
- A [contoso Med-alkalmazás](https://github.com/Azure-Samples/communication-services-contoso-med-app) mintájának áttekintése

## <a name="additional-reading"></a>További információ

- [Minták](./overview.md) – további mintákat és példákat talál a minták áttekintése oldalon.
- [Redux](https://redux.js.org/) – ügyféloldali állapot kezelése
- [FluentUI](https://aka.ms/fluent-ui) – Microsoft powered UI Library
- [Reagálás](https://reactjs.org/) – függvénytár a felhasználói felületek létrehozásához
- [ASP.net Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) -keretrendszer webalkalmazások létrehozásához
