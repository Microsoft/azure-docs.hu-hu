---
title: Webhookok a beállításjegyzékbeli műveletekre való válaszadáshoz
description: Megtudhatja, hogyan aktiválhat eseményeket webhookokkal, amikor leküldéses vagy leküldési műveletek történnek a beállításjegyzék-adattárakban.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4f6fb719f8d9d51429a19616aa5548b32a2687e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773399"
---
# <a name="using-azure-container-registry-webhooks"></a>A Azure Container Registry használata

Az Azure-beli tároló-beállításjegyzékek privát Docker-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a Docker Hub nyilvános Docker-rendszerképeket tárol. Emellett Helm-diagramok [(előzetes](container-registry-helm-repos.md) verzió) adattárait is képes üzembe helyezni, amely egy csomagolási formátum az alkalmazások Kubernetesben való üzembe helyezéséhez. Webhookokkal eseményeket aktiválhat, ha bizonyos műveletekre sor kerül az egyik beállításjegyzék-adattárban. A webhookok reagálnak a beállításjegyzék szintjén található eseményekre, vagy egy adott adattárcímkére is kiterjednek. Egy  [georeplikált beállításjegyzékben](container-registry-geo-replication.md) minden webhookot úgy konfigurál, hogy reagáljon egy adott regionális replika eseményeire.

A webhook végpontjának nyilvánosan elérhetőnek kell lennie a beállításjegyzékből. A beállításjegyzék webhookkérései konfigurálhatóak a biztonságos végponton való hitelesítéshez.

A webhookkérésekkel kapcsolatos részletekért lásd: Azure Container Registry [webhook sémareferenciája.](container-registry-webhook-reference.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure Container Registry – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használhatja például a [Azure Portal](container-registry-get-started-portal.md) vagy az Azure [CLI-t.](container-registry-get-started-azure-cli.md) A [Azure Container Registry szolgáltatási szintek](container-registry-skus.md) különböző webhookkvótával vannak.
* A Docker parancssori felülete – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Webhook létrehozása – Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen arra a tárolójegyzékre, amelyben létre szeretne hozni egy webhookot.
1. A **Szolgáltatások alatt** válassza a **Webhookok lehetőséget.**
1. A **webhook** eszköztárán válassza a Hozzáadás lehetőséget.
1. Töltse ki *a Webhook létrehozása űrlapot* a következő információkkal:

| Érték | Leírás |
|---|---|
| Webhook neve | A webhooknak adni kívánt név. Csak betűket és számokat tartalmazhat, és 5–50 karakter hosszúságúnak kell lennie. |
| Hely | [Georeplikált beállításjegyzékhez](container-registry-geo-replication.md) adja meg a beállításjegyzék-replika Azure-régióját. 
| Szolgáltatás URI-ja | Az URI, ahová a webhook POST-értesítéseket küld. |
| Egyéni fejlécek | A POST-kéréssel együtt átadni kívánt fejlécek. A "kulcs: érték" formátumban kell lennie. |
| Eseményindító-műveletek | A webhookot aktiváló műveletek. A műveletek közé tartozik a rendszerkép leküldése, a kép törlése, a Helm-diagramok leküldése, a Helm-diagram törlése és a kép karanténba kerül. Egy vagy több műveletet is kiválaszthat a webhook aktiválása érdekében. |
| Állapot | A webhook létrehozása utáni állapot. Alapértelmezés szerint engedélyezve van. |
| Hatókör | A webhook működése. Ha nincs megadva, a hatókör a beállításjegyzék összes eseményére kiterjed. Egy adattárhoz vagy címkéhez a "repository:tag" vagy "repository:*" formátumban lehet megadni az adattárban található összes címkéhez. |

Példa webhook űrlapra:

![Képernyőkép az ACR-webhook létrehozási U I-ről a Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook létrehozása – Azure CLI

Webhook Azure CLI használatával való létrehozásához használja az [az acr webhook create](/cli/azure/acr/webhook#az_acr_webhook_create) parancsot. A következő parancs létrehoz egy webhookot a *mycontainerregistry* beállításjegyzékben található összes rendszerkép-törlési eseményhez:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure Portal

A webhook használata előtt tesztelheti a **Ping** gombbal. A ping egy általános POST kérést küld a megadott végpontnak, és naplózza a választ. A ping funkcióval ellenőrizheti, hogy megfelelően konfigurálta-e a webhookot.

1. Válassza ki a tesztelni kívánt webhookot.
2. A felső eszköztáron válassza a **Ping lehetőséget.**
3. Ellenőrizze a végpont válaszát a **HTTP STATUS oszlopban.**

![Az ACR-webhook létrehozási felhasználói felülete a Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Egy ACR-webhook Azure CLI-val való teszteléshez használja az [az acr webhook ping parancsot.](/cli/azure/acr/webhook#az_acr_webhook_ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények eléréséhez használja az [az acr webhook list-events](/cli/azure/acr/webhook) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Az egyes webhookok úgy törölhetők, hogy  kiválasztják a webhookot, majd a törlés gombot a Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Következő lépések

### <a name="webhook-schema-reference"></a>Webhooksémák referenciája

A JSON-esemény hasznos adatainak formátumával és tulajdonságaival kapcsolatos részletekért tekintse meg a Azure Container Registry sémareferenciáját:

[Azure Container Registry webhook sémareferenciája](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid események

A natív beállításjegyzékben a cikkben tárgyalt webhookesemények mellett a Azure Container Registry is kibocsáthat eseményeket a Event Grid:

[Rövid útmutató: Tároló-beállításjegyzék-események küldése a Event Grid](container-registry-event-grid-quickstart.md)
