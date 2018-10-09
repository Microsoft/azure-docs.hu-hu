---
title: Az Azure Container Registry webhookok
description: Ismerje meg, a beállításjegyzék-tárházak esetében bizonyos műveleteket bekövetkezésekor kiváltó események webhookok használatával.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/20/2017
ms.author: danlep
ms.openlocfilehash: 350ae16aa66276e7e64c5c35718dca74a70f499e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854099"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhookok használata

Egy Azure container registry tárolja és kezeli a privát Docker-tárolók lemezképeit, Docker Hub nyilvános Docker-rendszerképeket tárol hasonló. Amikor az egyes műveletekre kerül sor egy, a beállításjegyzék-tárházak kiváltó események webhookok is használhatja. Webhookok reagálhat az eseményekre a beállításjegyzék szintjén, vagy azok leszűkítheti egy adott adattárra címkét.

További információ a webhook-kérelem: [Azure Container Registry webhookok sémaleírás](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure container registry - tároló-beállításjegyzék létrehozása az Azure-előfizetésében. Például a [az Azure portal](container-registry-get-started-portal.md) vagy a [Azure CLI-vel](container-registry-get-started-azure-cli.md).
* Docker CLI - helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker CLI-parancsok telepítése [Docker-motor](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Az Azure portal webhook létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Lépjen a tárolóregisztrációs adatbázisba, amelyben meg szeretné hozzon létre egy webhookot.
1. A **szolgáltatások**válassza **Webhookok**.
1. Válassza ki **Hozzáadás** a webhook eszköztáron.
1. Végezze el a *webhook létrehozása* űrlapján az alábbi adatokat:

| Érték | Leírás |
|---|---|
| Name (Név) | Kíván adni a webhook nevét. Csak kisbetűket és számokat tartalmazhat, és 5 – 50 karakter hosszúságúnak kell lennie. |
| Szolgáltatás URI-ja | Az URI-t, a webhook POST értesítéseket küldjön-e. |
| Egyéni fejlécek | A POST-kérés továbbítása a kívánt fejléceket. Lehetnek a "kulcs: érték" formátumban. |
| Műveletek indítása | A webhook kiváltó műveletek. Webhookok jelenleg kép leküldéses aktiválhatja és/vagy törlési műveleteket. |
| status | A webhook létrehozása után az állapotát. Ez alapértelmezés szerint engedélyezve van. |
| Hatókör | A hatókör, amelyen a webhook működik. Alapértelmezés szerint a hatóköre az összes esemény a beállításjegyzékben. Azt adható meg a tárházban vagy a címke a "tárházat: címkét" formátumban. |

Példa webhook-űrlapon:

![Az ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Azure CLI-vel webhook létrehozása

Hozzon létre egy webhookot, az Azure CLI-vel, használja a [az acr webhook létrehozása](/cli/azure/acr/webhook#az-acr-webhook-create) parancsot.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure Portal

Való használata előtt a webhook tárolón leküldéses kép és törlési műveleteket, tesztelheti azt a **Ping** gombra. Ping egy általános POST kérést küld a megadott végponton, és a válasz naplózza. A ping szolgáltatás használatával segítségével ellenőrizze, hogy megfelelően állította be a webhook.

1. Válassza ki a vizsgálni kívánt webhook.
2. A felső eszköztáron válassza **Ping**.
3. A végpont válasz látogasson el a **HTTP-állapot** oszlop.

![Az ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-vel egy ACR webhook teszteléséhez használja a [az acr webhook pingelése](/cli/azure/acr/webhook#az-acr-webhook-ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja a [az acr webhook-lista-eseményei](/cli/azure/acr/webhook#list-events) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Egyes webhookok kiválasztásával a webhook törölhetők, majd a **törlése** gomb az Azure Portalon.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>További lépések

### <a name="webhook-schema-reference"></a>Webhook-adatbázisséma hivatkozása

További információ a formátum és a JSON esemény is észleltünk adattartalmakat. az Azure Container Registry által kibocsátott tulajdonságait tekintse meg a webhook-adatbázisséma hivatkozása:

[Az Azure Container Registry webhookok adatbázisséma hivatkozása](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid-események

A natív beállításjegyzék webhookesemények ebben a cikkben tárgyalt, mellett az Azure Container Registry kibocsátható az eseményeket az Event Grid:

[Gyors útmutató: Tároló beállításjegyzék események küldése az Event Grid](container-registry-event-grid-quickstart.md)