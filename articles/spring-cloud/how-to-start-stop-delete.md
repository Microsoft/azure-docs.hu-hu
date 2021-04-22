---
title: Indítsa el, állítsa le és törölje Azure Spring Cloud-alkalmazás | Microsoft Docs
description: A Azure Spring Cloud elindítani, leállítani és törölni
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 914325aba3d123fb1b700f0eff8ddb17119c5d12
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863208"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Az alkalmazás Azure Spring Cloud, leállítása és törlése

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez az útmutató azt ismerteti, hogyan módosíthatja egy alkalmazás állapotát a Azure Spring Cloud a Azure Portal vagy az Azure CLI használatával.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Az alkalmazás üzembe helyezése után elindíthatja, leállíthatja és törölheti azt a Azure Portal.

1. A Azure Spring Cloud a saját Azure Portal.
1. Válassza az **Alkalmazás-irányítópult** lapot.
1. Válassza ki azt az alkalmazást, amelynek az állapotát módosítani szeretné.
1. Az alkalmazás **Áttekintés** lapján válassza a **Start/Stop**, **Újraindítás** vagy **Törlés lehetőséget.**

## <a name="using-the-azure-cli"></a>Az Azure CLI-vel

> [!NOTE]
> Az Azure CLI-hez választható paramétereket is használhat, és konfigurálhatja az alapértelmezett beállításokat. Az Azure CLI-ről további információt a [referenciadokumentációnkban talál.](/cli/azure/spring-cloud)  

Először telepítse a Azure Spring Cloud bővítményt az Azure CLI-hez a következőképpen:

```azurecli
az extension add --name spring-cloud
```

Ezután válassza ki az alábbi Azure CLI-műveletek bármelyikét:

* Az alkalmazás elindítani:

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
