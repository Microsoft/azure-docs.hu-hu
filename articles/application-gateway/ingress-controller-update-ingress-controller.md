---
title: A bejövő adatkezelő vezérlésének frissítése a Helm
description: Ez a cikk tájékoztatást nyújt arról, hogyan frissíthet egy Application Gateway beérkező adatokat a Helm használatával.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84806783"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Az Application Gateway bejövőforgalom-vezérlőjének frissítése a Helm használatával 

A Kubernetes (AGIC) Azure Application Gateway beáramlási vezérlőjét az Azure Storage-ban üzemeltetett Helm-adattár használatával lehet frissíteni.

A frissítési folyamat megkezdése előtt győződjön meg arról, hogy a szükséges tárházat adta hozzá:

- Tekintse meg a jelenleg hozzáadott Helm-adattárakat az alábbiakkal:

    ```bash
    helm repo list
    ```

- Adja hozzá a AGIC-tárházat az alábbiakkal:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Frissítés

1. Frissítse a AGIC Helm-tárházat a legújabb kiadás beszerzéséhez:

    ```bash
    helm repo update
    ```

1. A diagram elérhető verzióinak megtekintése `application-gateway-kubernetes-ingress` :

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Példa a válaszra:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    A fenti lista legújabb elérhető verziója a következő: `0.7.0-rc1`

1. A jelenleg telepített Helm-diagramok megtekintése:

    ```bash
    helm list
    ```

    Példa a válaszra:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    A fenti minta válaszból származó Helm diagram a neve `odd-billygoat` . Ezt a nevet fogjuk használni a többi parancshoz. A tényleges központi telepítési név valószínűleg eltérő lesz.

1. A Helm üzembe helyezésének frissítése egy új verzióra:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Visszaállítás

Ha a Helm üzembe helyezése meghiúsul, visszaállíthatja az előző kiadást.

1. Az utolsó ismert kifogástalan kiadás számának beolvasása:

    ```bash
    helm history odd-billygoat
    ```

    Példa a kimenetre:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    A parancs mintájának kimenetében `helm history` úgy tűnik, hogy a verzió utolsó sikeres üzembe helyezése `odd-billygoat``1`

1. Visszaállítás az utolsó sikeres változatra:

    ```bash
    helm rollback odd-billygoat 1
    ```
