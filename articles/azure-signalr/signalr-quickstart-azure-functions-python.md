---
title: Azure SignalR Service gyorsútmutató – Python
description: Rövid útmutató a Azure SignalR Service és Azure Functions létrehozásához a Python használatával.
author: anthonychu
ms.author: antchu
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: a279c1ed16cac69932001146d4108cec19203e22
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536634"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Rövid útmutató: Csevegőszoba létrehozása Azure Functions és SignalR Service Python használatával

Az Azure SignalR szolgáltatás használatával egyszerűen adhat hozzá valós idejű funkciókat az alkalmazásához. Az Azure Functions egy kiszolgáló nélküli platform, amellyel infrastruktúra kezelése nélkül futtathat kódokat. Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan készíthet kiszolgáló nélküli, valós idejű csevegőalkalmazást a SignalR szolgáltatás és a Functions használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató macOS, Windows vagy Linux rendszeren is futtatható.

Ellenőrizze, hogy van-e telepítve valamilyen kódszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/).

Telepítse [a Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (2.7.1505-ös vagy újabb verzió) a Python Azure-függvényalkalmazások helyi futtatásához.

Azure Functions [Python 3.6+](https://www.python.org/downloads/)szükséges. [(Lásd: Támogatott Python-verziók](/azure/azure-functions/functions-reference-python#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com/> webhelyen az Azure-fiókjával.

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Az Azure-függvényalkalmazás konfigurálása és futtatása

1. A böngészőben, amelyben meg van nyitva az Azure Portal, a portál tetején levő keresőmezőben a példány nevére való kereséssel ellenőrizze, hogy a korábban üzembe helyezett SignalR-szolgáltatáspéldány sikeresen létrejött-e. A megnyitáshoz válassza ki a példányt.

    ![A SignalR-szolgáltatáspéldány megkeresése](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Válassza a **Kulcsok** elemet a SignalR-szolgáltatáspéldány kapcsolati sztringjeinek megtekintéséhez.

1. Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.

    ![Válassza ki és másolja a vágólapra az elsődleges kapcsolati sztring értékét.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. A kódszerkesztőben nyissa meg az *src/chat/python* mappát a klónozott adattárban.

1. Python-függvények helyi fejlesztéséhez és teszteléséhez Python 3.6-os vagy 3.7-es környezetben kell dolgoznia. Futtassa az alábbi parancsokat egy nevű virtuális környezet létrehozásához és `.venv` aktiválásához.

    **Linux vagy macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Nevezze át a *local.settings.sample.json* fájlt *local.settings.json* névre.

1. A **local.settings.json** fájlban illessze be a kapcsolati sztringet az **AzureSignalRConnectionString** beállítás értékéhez. Mentse a fájlt.

1. A Python-függvények mappákba vannak rendezve. Minden mappában két fájl található: *afunction.jsa* függvényben használt kötéseket határozza meg, az *\_ \_ init \_ \_ .py* pedig a függvény törzse. Ebben a függvényalkalmazásban két HTTP által indított függvény található:

    - **negotiate** – A *SignalRConnectionInfo* bemeneti kötést használja érvényes kapcsolatadatok létrehozásához és visszaküldéséhez.
    - **messages** – A kéréstörzsben fogadja a csevegés üzenetét, és a *SignalR* kimeneti kötés használatával továbbítja azt az összes csatlakoztatott ügyfélalkalmazás számára.

1. Győződjön meg arról, hogy a terminálban aktiválva van a virtuális környezet, és hogy az *src/chat/python mappában* van. Telepítse a szükséges Python-csomagokat a PIP használatával.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Futtassa a függvényalkalmazást.

    ```bash
    func start
    ```

    ![Függvényalkalmazás futtatása](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problémákat? Próbálja ki [a hibaelhárítási útmutatót,](signalr-howto-troubleshoot-guide.md) [vagy tudtossa velünk a következőt:](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy valós idejű kiszolgáló nélküli alkalmazást épített és futtatott a VS Code-ban. A következőkben még többet tudhat meg az Azure Functions VS Code-ból történő üzembe helyezéséről.

> [!div class="nextstepaction"]
> [Az Azure Functions üzembe helyezése VS Code-dal](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
