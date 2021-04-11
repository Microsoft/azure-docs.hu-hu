---
title: Webhookok a beállításjegyzékbeli műveletekre való reagáláshoz
description: Ismerje meg, hogyan indíthat el eseményeket a webhookok használatával, amikor leküldéses vagy lekéréses műveletek történnek a beállításjegyzékbeli adattárakban.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722245"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhookok használata

Az Azure-beli tároló-beállításjegyzékek privát Docker-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a Docker Hub nyilvános Docker-rendszerképeket tárol. Emellett a [Helm-diagramok](container-registry-helm-repos.md) (előzetes verzió) adattárházait is üzemeltetheti, amelyekkel alkalmazásokat telepíthet a Kubernetes. A webhookok használatával aktiválhatja az eseményeket, amikor bizonyos műveletek az egyik beállításjegyzékbeli tárházban lépnek életbe. A webhookok a beállításjegyzék szintjén válaszolnak az eseményekre, vagy hatókörük egy adott adattár címkéjére is felhasználhatók. [Földrajzilag replikált](container-registry-geo-replication.md) beállításjegyzék esetén az egyes webhookokat úgy konfigurálja, hogy válaszoljon az adott regionális replika eseményeire.

A webhook végpontjának nyilvánosan elérhetőnek kell lennie a beállításjegyzékből. Konfigurálhatja a beállításjegyzék webhook-kérelmeit a biztonságos végpontra történő hitelesítéshez.

A webhook-kérelmekkel kapcsolatos részletekért lásd: [Azure Container Registry webhook-séma referenciája](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure Container Registry – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t. A [Azure Container Registry szolgáltatási szintjei](container-registry-skus.md) eltérő webhook-kvótával rendelkeznek.
* A Docker parancssori felülete – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Webhook létrehozása – Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon ahhoz a tároló-beállításjegyzékhez, amelyben létre kíván hozni egy webhookot.
1. A **szolgáltatások** területen válassza a **webhookok** lehetőséget.
1. A webhook eszköztárban válassza a **Hozzáadás** lehetőséget.
1. Fejezze be a *webhook létrehozása* űrlapot a következő információkkal:

| Érték | Leírás |
|---|---|
| Webhook neve | A webhookhoz adni kívánt név. Csak betűket és számokat tartalmazhat, és 5-50 karakter hosszúnak kell lennie. |
| Hely | [Földrajzilag replikált](container-registry-geo-replication.md) beállításjegyzék esetén a beállításjegyzék-replika Azure-régióját kell megadnia. 
| Szolgáltatás URI-ja | Az az URI, amelyben a webhooknak POST-értesítéseket kell küldenie. |
| Egyéni fejlécek | A POST kérelemmel együtt átadni kívánt fejlécek. A "Key: Value" formátumúnak kell lenniük. |
| Trigger műveletek | A webhookot kiváltó műveletek. A műveletek közé tartozik a képek leküldése, a rendszerkép törlése, a Helm chart push, a Helm diagram törlése és a képek karanténba helyezése. Kiválaszthat egy vagy több műveletet a webhook elindításához. |
| Állapot | A webhook állapota a létrehozásuk után. Alapértelmezés szerint engedélyezve van. |
| Hatókör | A webhook működésének hatóköre. Ha nincs megadva, a hatókör a beállításjegyzékben található összes eseményre kiterjed. Megadható egy adattárhoz vagy egy címkéhez "adattár: tag" vagy "adattár: *" formátumban a tárházban található összes címkéhez. |

Példa webhook-űrlapra:

![Képernyőkép, amely az ACR webhook létrehozását mutatja be a Azure Portalban.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook létrehozása – Azure CLI

Webhook az Azure CLI használatával történő létrehozásához használja az az [ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) parancsot. A következő parancs létrehoz egy webhookot az összes rendszerkép-törlési eseményhez a beállításjegyzék *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure Portal

A webhook használatának megkezdése előtt tesztelheti a **ping** gomb segítségével. A ping általános POST-kérést küld a megadott végpontnak, és naplózza a választ. A ping funkció használatával ellenőrizheti, hogy helyesen konfigurálta-e a webhookot.

1. Válassza ki a tesztelni kívánt webhookot.
2. A felső eszköztáron válassza a **pingelés** lehetőséget.
3. A **http-állapot** oszlopban keresse meg a végpont válaszát.

![ACR webhook-létrehozási felhasználói felület a Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az ACR webhook Azure CLI-vel való teszteléséhez használja az az [ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja az az [ACR webhook List-Events](/cli/azure/acr/webhook) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Minden webhook törölhető úgy, hogy kiválasztja a webhookot, majd a Azure Portal **Törlés** gombját.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Következő lépések

### <a name="webhook-schema-reference"></a>Webhook-séma – dokumentáció

A Azure Container Registry által kibocsátott JSON-események tartalmának formátumával és tulajdonságaival kapcsolatos részletekért tekintse meg a webhook sémájának dokumentációját:

[Azure Container Registry webhook-séma referenciája](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Események Event Grid

A cikkben tárgyalt natív beállításjegyzékbeli webhook-eseményeken kívül a Azure Container Registry eseményeket is kibocsáthat a Event Gridba:

[Gyors útmutató: tároló-beállításjegyzékbeli események küldése Event Grid](container-registry-event-grid-quickstart.md)
