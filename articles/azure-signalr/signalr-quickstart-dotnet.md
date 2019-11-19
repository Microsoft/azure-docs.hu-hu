---
title: Fejlesztés a ASP.NET – Azure Signaler szolgáltatással
description: Egy rövid útmutató, amely az Azure Signaler szolgáltatást használja egy ASP.NET-keretrendszerrel rendelkező chat-hely létrehozásához.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/20/2019
ms.author: zhshang
ms.openlocfilehash: ec5b7a75bced4b7cd81a120925558b8c1be57818
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158173"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Rövid útmutató: csevegési helyiség létrehozása a ASP.NET és a Signaler szolgáltatással

Az Azure Signaler szolgáltatás a [ASP.NET Core 2,0-es jelzőn](https://docs.microsoft.com/aspnet/core/signalr/introduction)alapul, amely **nem** 100%-kompatibilis a ASP.net-jelzővel. Az Azure Signaler szolgáltatás a legújabb ASP.NET Core technológiák alapján újra implementálta a ASP.NET Signaler adatprotokollját. Ha az Azure Signaler szolgáltatást a ASP.NET-jelzőhöz használja, akkor egyes ASP.NET-jelző funkciók már nem támogatottak, például az Azure-szignáló nem küldi újra az üzeneteket, amikor az ügyfél újrakapcsolódik. Emellett a Forever frame Transport és a JSNOP támogatással nem támogatott. A ASP.NET-szignáló alkalmazás a Signaler szolgáltatással való működéséhez szükség van a Code Changes és a függő könyvtárak megfelelő verziójára. 

A ASP.NET-jelző és a ASP.NET Core-jelző közötti szolgáltatások összehasonlításának teljes listájáért tekintse meg a [verzió-különbségek dokumentációját](https://docs.microsoft.com/aspnet/core/signalr/version-differences?view=aspnetcore-2.2) .

Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el a ASP.NET és az Azure Signaler szolgáltatást egy hasonló [Chat Room-alkalmazáshoz](./signalr-quickstart-dotnet-core.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET-jelző 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

A ASP.NET-jelző alkalmazások nem támogatják a *kiszolgáló* nélküli üzemmódot. Mindig használja az alapértelmezett vagy a *klasszikus* *értéket* az Azure signaler szolgáltatás példányához.

Az ebben a rövid útmutatóban használt Azure-erőforrásokat is létrehozhatja [a signaler Service-parancsfájl létrehozásával](scripts/signalr-cli-create-service.md).

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kóddal folytatott munkavégzésre. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="configure-and-run-chat-room-web-app"></a>A chat room Web App konfigurálása és futtatása

1. Indítsa el a Visual studiót, és nyissa meg a megoldást a klónozott adattár *ASPNET-Samples/chat/* mappában.

1. Keresse meg és válassza ki a létrehozott példányt a böngészőben, ahol a Azure Portal meg van nyitva.

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

1. Most állítsa be a kapcsolati karakterláncot a web. config fájlban.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. A *Startup.cs*-ben a `MapSignalR()`hívása helyett meg kell hívnia `MapAzureSignalR({your_applicationName})` és továbbítania kell a kapcsolati karakterláncot, hogy az alkalmazás kapcsolódjon a szolgáltatáshoz ahelyett, hogy saját maga is felhasználja a jelet. Cserélje le a `{YourApplicationName}`t az alkalmazás nevére. Ez a név egy egyedi név, amely megkülönbözteti ezt az alkalmazást a többi alkalmazástól. A `this.GetType().FullName`t értékként használhatja.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Ezen API-k használata előtt a Service SDK-ra is hivatkoznia kell. Az **eszközök megnyitása | NuGet csomagkezelő | Package Manager konzol** és futtatási parancs:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    A változásokon kívül minden más változatlan marad, továbbra is használhatja a hub felületét, amellyel az üzleti logikát írhat.

    > [!NOTE]
    > Az implementációban a végpontok `/signalr/negotiate` az Azure Signaler Service SDK általi egyeztetésre van kitéve. Speciális egyeztetési választ ad vissza, amikor az ügyfelek megpróbálnak csatlakozni a kapcsolati sztringben meghatározott szolgáltatási végponthoz, és átirányítják az ügyfeleket.

1. A projekt hibakeresési módban való futtatásához nyomja le az **F5** billentyűt. Láthatja, hogy az alkalmazás helyileg fut. A szignáló futtatókörnyezetet az alkalmazás nem üzemelteti, hanem az Azure Signaler szolgáltatáshoz csatlakozik.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]



> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
> 
> 

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** lehetőségre.

   
![Törlés](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új Azure Signal Service-erőforrást, és felhasználta azt egy ASP.NET-webalkalmazással. Következő lépésként megtudhatja, hogyan fejleszthet valós idejű alkalmazásokat az Azure Signaler szolgáltatással a ASP.NET Core használatával.

> [!div class="nextstepaction"]
> [Azure Signaler szolgáltatás és ASP.NET Core](./signalr-quickstart-dotnet-core.md)
