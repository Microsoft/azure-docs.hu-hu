---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83597232"
---
#### <a name="local-proxy"></a>Helyi proxy

Beállíthat egy proxyt az élő kiszolgáló Visual Studio Code-bővítményéhez, amely az összes kérelmet a `/api` futó API-végpontra irányítja a következő helyen: `http://127.0.0.1:7071/api` .

1. Nyissa _meg a. vscode/settings.js_ fájlt.

1. Adja hozzá a következő beállításokat az élő kiszolgáló proxyjának megadásához.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Ezt a konfigurációt a Project Settings (projekt beállításai) fájlban a rendszer a felhasználói beállítások fájljában a legjobban menti.

   A Project Settings szolgáltatás használata biztosítja, hogy a proxy ne legyen alkalmazva a Visual Studio Code-ban megnyitott összes többi projektre.
