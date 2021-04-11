---
title: Végpontok közötti Transport Layer Security engedélyezése
titleSuffix: Azure Spring Cloud
description: Az alkalmazások végpontok közötti Transport Layer Securityának engedélyezése.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227806"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Végpontok közötti TLS engedélyezése alkalmazásokhoz

Ebből a témakörből megtudhatja, hogyan engedélyezheti a végpontok közötti SSL/TLS-t a bejövő vezérlőről a HTTPS-t támogató alkalmazások felé irányuló forgalom biztonságossá tételéhez. Miután engedélyezte a végpontok közötti TLS-t, és betölt egy tanúsítványt a kulcstartóból, a rendszer az Azure Spring Cloud-on belüli összes kommunikációt TLS-védelemmel látja el.

   ![A TLS által védett kommunikációs diagramok.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Előfeltételek 

- Egy üzembe helyezett Azure Spring Cloud-példány. Az első lépésekhez kövesse [Az Azure CLI-n keresztül történő üzembe helyezést ismertető](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) útmutatót.
- Ha nem ismeri a végpontok közötti TLS-t, tekintse meg a [végpontok közötti TLS-mintát](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- A szükséges tanúsítványok a Spring boot-alkalmazásokban való biztonságos betöltéséhez használhatja a kulcstartó [Spring boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates)-t.


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Végpontok közötti TLS engedélyezése meglévő alkalmazáson

A paranccsal `az spring-cloud app update --enable-end-to-end-tls` engedélyezheti vagy letilthatja az alkalmazások végpontok közötti TLS-használatát.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Végpontok közötti TLS engedélyezése egyéni tartomány kötése esetén

A paranccsal `az spring-cloud app custom-domain update --enable-end-to-end-tls` `az spring-cloud app custom-domain bind --enable-end-to-end-tls` engedélyezheti vagy letilthatja az alkalmazások végpontok közötti TLS-használatát.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Végpontok közötti TLS-állapot ellenőrzése

A parancs használatával `az spring-cloud app show` Keresse meg a értékét `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Következő lépések
* [A konfigurációs kiszolgáló és a szolgáltatás beállításjegyzékének elérése](how-to-access-data-plane-azure-ad-rbac.md)
