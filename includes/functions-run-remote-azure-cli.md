---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425097"
---
## <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy olyan eszközzel, mint például a curl. 

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions` . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **Meghívási URL-címmel**, és illessze be a paramétert `&name=Functions` . A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
