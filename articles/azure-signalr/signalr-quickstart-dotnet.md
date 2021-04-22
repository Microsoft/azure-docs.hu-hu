---
title: Fejlesztés ASP.NET – Azure SignalR Service
description: Rövid útmutató a Azure SignalR Service egy csevegőszoba létrehozásához ASP.NET keretrendszer használatával.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 0ac17de3f73424994fc39ef0044d17ef83b501b7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866142"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Rövid útmutató: Csevegőszoba létrehozása ASP.NET és SignalR Service

Azure SignalR Service a SignalR a [ASP.NET Core 2.1-hez,](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)amely **nem** 100%-ban kompatibilis az ASP.NET SignalR-sel. Azure SignalR Service a SignalR ASP.NET a legújabb core ASP.NET alapján. A Azure SignalR Service SignalR ASP.NET egyes ASP.NET SignalR-funkciók már nem támogatottak, például az Azure SignalR nem lejátszja az üzeneteket, amikor az ügyfél újra csatlakozik. Emellett a Forever Frame-hez és a JSONP-hez való szállítás nem támogatott. Bizonyos kódváltozások és a függő kódtárak megfelelő verziója szükséges ahhoz, hogy ASP.NET SignalR-alkalmazás működjön SignalR Service.

A SignalR [és](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) a Core SignalR ASP.NET szolgáltatások teljes összehasonlítását a verziók közötti különbségeket ASP.NET dokumentumban.

Ebből a rövid útmutatóból megtudhatja, hogyan ismerkedhet meg a hasonló ASP.NET Azure SignalR Service hasonló [csevegőszoba-alkalmazással.](./signalr-quickstart-dotnet-core.md)


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET-keretrendszer 4.6.1](https://dotnet.microsoft.com/download/dotnet-framework/net461)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

*A kiszolgáló nélküli* mód nem támogatott ASP.NET SignalR-alkalmazásokhoz. Mindig az *Alapértelmezett vagy* a *Klasszikus értéket* használja a Azure SignalR Service példányhoz.

Az ebben a rövid útmutatóban használt Azure-erőforrásokat a Create a SignalR Service script (Új szkript [létrehozása) SignalR Service is létrehozhatja.](scripts/signalr-cli-create-service.md)

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudajuk meg velünk a következőt:](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Amíg a szolgáltatás üzembe helyezése folyamatban van, térjünk át a kóddal folytatott munkavégzésre. Klónozza a [mintaalkalmazást a GitHubról](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), állítsa be a SignalR Service kapcsolati sztringjét, és futtassa helyileg az alkalmazást.

1. Nyisson meg egy git terminálablakot. Váltson át arra a mappára, ahová a mintaprojektet klónozni szeretné.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudajuk meg velünk a következőt:](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Csevegőszoba-webalkalmazás konfigurálása és futtatása

1. Indítsa Visual Studio, és nyissa meg a megoldást a klónozott adattár *aspnet-samples/ChatRoom/* mappájában.

1. A böngészőben, ahol a Azure Portal meg van nyitva, keresse meg és válassza ki a létrehozott példányt.

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

1. Most állítsa be a kapcsolati sztringet *aweb.config* fájlban.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. A *Startup.cs fájlban* a hívása helyett meg kell hívnia és át kell adnia a kapcsolati sztringet, hogy az alkalmazás a SignalR üzemeltetése helyett a szolgáltatáshoz `MapSignalR()` `MapAzureSignalR({YourApplicationName})` kapcsolódjon. Cserélje `{YourApplicationName}` le a helyére az alkalmazás nevét. Ez a név egy egyedi név, amely megkülönbözteti az alkalmazást a többi alkalmazástól. Ezt `this.GetType().FullName` használhatja értékként.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Az API-k használata előtt hivatkozni kell a szolgáltatási SDK-ra is. Nyissa meg **a Tools | NuGet-Csomagkezelő | Csomagkezelő konzolt,** és futtassa a következő parancsot:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    A módosításokon kívül minden más változatlan marad, továbbra is használhatja a már ismert központi felületet az üzleti logika írásához.

    > [!NOTE]
    > Az implementációban a végpontot az SDK Azure SignalR Service `/signalr/negotiate` teszi elérhetővé. Ez egy speciális egyeztetési választ ad vissza, amikor az ügyfelek megpróbálnak csatlakozni és átirányítani az ügyfeleket a kapcsolati sztringben meghatározott szolgáltatásvégpontra.

1. Nyomja <kbd>le az F5</kbd> billentyűt a projekt hibakeresési módban való futtatásához. Láthatja, hogy az alkalmazás helyileg fut. Ahelyett, hogy a SignalR-runtime-t önmagában az alkalmazáson keresztül futtatja, most már csatlakozik a Azure SignalR Service.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. Ebben a rövid útmutatóban a *SignalRTestResources* nevű erőforráscsoportot használtuk. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új Azure SignalR Service, és egy új webalkalmazással ASP.NET azt. Ezután megtudhatja, hogyan fejleszthet valós idejű alkalmazásokat a Azure SignalR Service core ASP.NET használatával.

> [!div class="nextstepaction"]
> [Azure SignalR Service a ASP.NET Core-sal](./signalr-quickstart-dotnet-core.md)
