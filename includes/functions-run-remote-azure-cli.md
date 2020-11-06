---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425097"
---
## <a name="invoke-the-function-on-azure"></a>A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy olyan eszközzel, mint például a curl. 

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a publish (közzététel) parancs kimenetében megjelenő teljes **Meghívási URL-** címet egy böngésző címsorába, és illessze be a lekérdezési paramétert `&name=Functions` . A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[Curl](#tab/curl)

Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **Meghívási URL-címmel** , és illessze be a paramétert `&name=Functions` . A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
