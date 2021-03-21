---
title: Azure Signaler szolgáltatás kiszolgáló nélküli gyors üzembe helyezése – Python
description: Egy rövid útmutató az Azure Signaler szolgáltatás és a Azure Functions használatával csevegési helyiség létrehozásához a Python használatával.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: aaaf9011d38e7ec02e83db63757c434329b835e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960291"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Gyors útmutató: csevegési helyiség létrehozása a Azure Functions és a Signaler szolgáltatással a Python használatával

Az Azure SignalR szolgáltatás használatával egyszerűen adhat hozzá valós idejű funkciókat az alkalmazásához. Az Azure Functions egy kiszolgáló nélküli platform, amellyel infrastruktúra kezelése nélkül futtathat kódokat. Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan készíthet kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és a Functions használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

Ellenőrizze, hogy van-e telepítve valamilyen kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/).

Telepítse a [Azure functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (2.7.1505 vagy újabb verziót) a Python Azure Function apps helyi futtatásához.

A Azure Functions [Python 3,6 vagy 3,7](https://www.python.org/downloads/)szükséges.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A Kódszerkesztő alkalmazásban nyissa meg a *src/chat/Python* mappát a klónozott tárházban.

1. A Python-függvények helyi fejlesztéséhez és teszteléséhez Python 3,6 vagy 3,7 környezetben kell dolgoznia. Futtassa a következő parancsokat a nevű virtuális környezet létrehozásához és aktiválásához `.venv` .

    **Linux vagy macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A Python-függvények mappákba vannak rendezve. Minden mappában két fájl található: *function.jsa* függvényben használt kötéseket definiálja, és az *\_ \_ init \_ \_ .* a függvény törzse. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **messages** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. Az aktivált virtuális környezettel rendelkező terminálon győződjön meg arról, hogy a *src/chat/Python* mappában található. Telepítse a szükséges Python-csomagokat a PIP használatával.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Futtassa a függvényalkalmazást.

    ```bash
    func start
    ```

    ![Function alkalmazás futtatása](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problémák léptek fel? Próbálja ki a [hibaelhárítási útmutatót](signalr-howto-troubleshoot-guide.md) , vagy [tudassa velünk](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást készített és futtatott a VS Code-ban. A következőkben még többet tudhat meg az Azure Functions VS Code-ból történő üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése VS Code-dal](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

