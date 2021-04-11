---
title: Az állandó tárolás használata az Azure Spring Cloud-ban | Microsoft Docs
description: Az állandó tárterület használata az Azure Spring Cloud-ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8811a30d2d0d6a31ff722e7f470b7e7bc38c4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878107"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Állandó tárolók használata az Azure Spring Cloudban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud két típusú tárolót biztosít az alkalmazáshoz: állandó és ideiglenes.

Alapértelmezés szerint az Azure Spring Cloud minden egyes alkalmazás-példány esetében ideiglenes tárhelyet biztosít. Az ideiglenes tárterület az alapértelmezett csatlakozási útvonallal/tmp. 5 GB-onként van korlátozva.

> [!WARNING]
> Ha újraindít egy alkalmazás-példányt, a rendszer véglegesen törli a kapcsolódó ideiglenes tárolót.

Az állandó tárterület egy, az Azure által kezelt fájlmegosztás-tároló, amelyet egy alkalmazás foglal le. Az állandó tárolóban tárolt adatmennyiséget az alkalmazás összes példánya megosztja. Az Azure Spring Cloud-példányok legfeljebb 10 olyan alkalmazást tartalmazhatnak, amelyeken engedélyezve van az állandó tárterület. Minden alkalmazás 50 GB állandó tárterületet foglal le. Az állandó tárterület alapértelmezett csatlakoztatási útvonala a/persistent.

> [!WARNING]
> Ha letilt egy alkalmazás állandó tárterületét, az összes tárterület fel van foglalva, és az összes tárolt érték elvész.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Az állandó tárterület engedélyezése a Azure Portal használatával

1. A Azure Portal **kezdőlapján** válassza az **összes erőforrás** lehetőséget.

    >![A minden erőforrás ikon megkeresése](media/portal-all-resources.jpg)

1. Válassza ki azt az Azure Spring Cloud-erőforrást, amelynek állandó tárterületre van szüksége. Ebben a példában a kiválasztott alkalmazás neve **felrugós**.

    > ![Alkalmazás kiválasztása](media/select-service.jpg)

1. A **Beállítások** fejléc alatt válassza az **alkalmazások** lehetőséget.

1. Az Azure Spring Cloud Services egy táblában jelenik meg.  Válassza ki azt a szolgáltatást, amelyhez állandó tárolót kíván hozzáadni. Ebben a példában az **átjáró** szolgáltatás van kiválasztva.

    > ![Válassza ki a szolgáltatást](media/select-gateway.jpg)

1. A szolgáltatás konfigurációja lapon válassza a **Konfigurálás** lehetőséget.

1. Válassza ki az **állandó tárterület** fület, és válassza az **Engedélyezés** lehetőséget.

    > ![Állandó tárterület engedélyezése](media/enable-persistent-storage.jpg)

Az állandó tárterület engedélyezése után a mérete és elérési útja a konfiguráció lapon látható.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Az állandó tároló módosítása az Azure CLI használatával

Ha szükséges, telepítse az Azure CLI-hez készült Spring Cloud bővítményt:

```azurecli
az extension add --name spring-cloud
```
Egyéb műveletek:

* Állandó tárterülettel rendelkező alkalmazás létrehozása:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Állandó tárterület engedélyezése egy meglévő alkalmazáshoz:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Az állandó tárterület letiltása egy meglévő alkalmazásban:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Ha letilt egy alkalmazás állandó tárterületét, az összes tárterület fel van foglalva, és az összes tárolt érték véglegesen elvész.

## <a name="next-steps"></a>Következő lépések

* Az [alkalmazások és a szolgáltatási kvóták](spring-cloud-quotas.md)megismerése.
* Megtudhatja, hogyan [méretezheti manuálisan az alkalmazást](spring-cloud-howto-scale-manual.md).
