---
title: Azure API Management-háttérrendszer | Microsoft Docs
description: Ismerkedjen meg az egyéni háttérrendszer API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500616"
---
# <a name="backends-in-api-management"></a>A API Management hátterei

API Management egy *háttérrendszer* (vagy *API-háttér*) egy olyan http-szolgáltatás, amely megvalósítja az előtér-API-t és annak műveleteit.

Bizonyos API-k importálásakor a API Management automatikusan konfigurálja az API-hátteret. A API Management például a [OpenAPI-specifikáció](import-api-from-oas.md), a [SOAP API](import-soap-api.md)vagy az Azure-erőforrások, például egy http-triggeres [Azure-függvényalkalmazás](import-function-app-as-api.md) vagy [logikai alkalmazás](import-logic-app-as-api.md)importálásakor konfigurálja a hátteret.

A API Management más Azure-erőforrások, például egy [Service Fabric-fürt](how-to-configure-service-fabric-backend.md) vagy egy egyéni szolgáltatás használatát is támogatja API-háttérként. Az egyéni háttérrendszer használata további konfigurálást igényel, például hogy hitelesítő adatokat engedélyezzen a háttérrendszer számára, és hogy definiálja az API-műveleteket. Ezeket a háttereket a Azure Portal, illetve az Azure API-k vagy eszközök használatával konfigurálhatja és kezelheti.

A háttérrendszer létrehozása után hivatkozhat a háttér URL-címére az API-kon. A [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) házirend segítségével átirányíthat egy bejövő API-kérést az egyéni háttérrendszer helyett az adott API alapértelmezett háttérbe való átirányításához.

## <a name="benefits-of-backends"></a>A háttérrendszer előnyei

Az egyéni háttérrendszer számos előnnyel jár, többek között:

* Absztrakt információk a háttérrendszer-szolgáltatásról, az API-k újrahasználhatóságának elősegítése és a jobb irányítás  
* Egyszerű használat egy átalakítási szabályzat konfigurálásával egy meglévő API-ra
* Kihasználja API Management funkcióinak előnyeit, hogy megőrizze a titkokat Azure Key Vault ha [nevesített értékek](api-management-howto-properties.md) vannak konfigurálva a fejléc vagy a lekérdezési paraméter hitelesítéséhez

## <a name="next-steps"></a>Következő lépések

* [Service Fabric háttér](how-to-configure-service-fabric-backend.md) beállítása a Azure Portal használatával.
* A háttérrendszer a API Management [REST API](/rest/api/apimanagement), a [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)vagy a [Azure Resource Manager sablonok](../service-fabric/service-fabric-tutorial-deploy-api-management.md)használatával is konfigurálható.

