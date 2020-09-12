---
title: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése | Microsoft Docs
description: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 17dd83ba7df469e12e0569e670d71b9068bb2a9f
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297518"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése

Ez az útmutató azt ismerteti, hogyan lehet módosítani az alkalmazások állapotát az Azure Spring Cloud-ban a Azure Portal vagy az Azure CLI használatával.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Az alkalmazások központi telepítése után elindíthatja, leállíthatja és törölheti a Azure Portal használatával.

1. Nyissa meg az Azure Spring Cloud Service-példányt a Azure Portal.
1. Válassza az **alkalmazás-irányítópult** fület.
1. Válassza ki azt az alkalmazást, amelynek az állapotát módosítani szeretné.
1. Az alkalmazás **Áttekintés** lapján válassza a **Start/leállítás**, **Újraindítás**vagy **Törlés**lehetőséget.

## <a name="using-the-azure-cli"></a>Az Azure CLI-vel

> [!NOTE]
> Az Azure CLI-vel választható paramétereket és az alapértelmezett értékeket is konfigurálhatja. További információt az Azure CLI-ről a [dokumentációban talál](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest).  

Először telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi módon:

```azurecli
az extension add --name spring-cloud
```

Ezután válassza ki az alábbi Azure CLI-műveletek bármelyikét:

* Az alkalmazás elindítása:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás leállítása:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás újraindítása:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás törlése:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
