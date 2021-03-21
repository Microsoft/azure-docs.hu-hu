---
title: Csevegési helyiség létrehozása a JavaScript használatával Azure Functions és a Signaler szolgáltatással
description: Egy rövid útmutató az Azure Signaler szolgáltatás és a Azure Functions használatával csevegési helyiség létrehozásához a JavaScript használatával.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 061dce01d2437d04d371ac65c115a1d95136fb5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874695"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Gyors útmutató: csevegési helyiség létrehozása Azure Functions és a Signaler szolgáltatással a JavaScript használatával

Az Azure Signaler szolgáltatással egyszerűen adhat hozzá valós idejű funkciókat az alkalmazáshoz, és Azure Functions egy kiszolgáló nélküli platform, amely lehetővé teszi, hogy az infrastruktúra kezelése nélkül futtassa a kódot. Ebben a rövid útmutatóban a JavaScript használatával kiszolgáló nélküli, valós idejű csevegési alkalmazást hozhat létre a Signal Service és a functions használatával.

## <a name="prerequisites"></a>Előfeltételek

- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/)
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), 2-es vagy újabb verzió. Az Azure Function apps helyi futtatására szolgál.
- [Node.js](https://nodejs.org/en/download/), 10-es verzió. x

   > [!NOTE]
   > A példáknak a Node.js más verzióival is működniük kell, további információért lásd: [Azure functions Runtime Versions dokumentációja](../azure-functions/functions-versions.md#languages) .

> [!NOTE]
> Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![Képernyőkép, amely kiemeli az elsődleges kapcsolatok sztringjét.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A Kódszerkesztő alkalmazásban nyissa meg a *src/chat/JavaScript* mappát a klónozott tárházban.

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A JavaScript-függvények mappákba vannak rendezve. Minden mappában két fájl található: a *function.json* határozza meg a függvényben használt kötéseket, az *index.js* pedig a függvény törzse. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **messages** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. A terminálon győződjön meg arról, hogy a *src/chat/JavaScript* mappában található. Futtassa a függvényalkalmazást.

    ```bash
    func start
    ```

    ![SignalR szolgáltatás létrehozása](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást készített és futtatott a VS Code-ban. A következőkben még többet tudhat meg az Azure Functions VS Code-ból történő üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése VS Code-dal](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

