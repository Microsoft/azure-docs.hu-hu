---
title: Azure API Management háttérbeli | Microsoft Docs
description: Tudnivalók az egyéni háttér API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a0ef3a2c1f2f1fc5cdf00737d1984f6cb13c40d0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813019"
---
# <a name="backends-in-api-management"></a>Háttér a API Management

A *háttér* (vagy *API-háttér)* a API Management egy HTTP-szolgáltatás, amely megvalósítja az előtere API-t és annak műveleteit.

Bizonyos API-k importálása API Management konfigurálja az API-háttérprogramot. Például a API Management konfigurálja a háttéralkalmazást [openAPI-specifikáció,](import-api-from-oas.md) [SOAP API](import-soap-api.md)vagy Azure-erőforrások, például HTTP-eseményindítóval aktivált Azure-függvényalkalmazások vagy [logikai alkalmazások importálásakor.](import-logic-app-as-api.md) [](import-function-app-as-api.md)

API Management más Azure-erőforrások, például egy Service Fabric fürt vagy egy egyéni szolgáltatás [API-háttérszolgáltatásként](how-to-configure-service-fabric-backend.md) való használatát is támogatja. Ezeknek az egyéni háttérkiszolgálóknak a használatához további konfigurációra van szükség, például a háttérszolgáltatáshoz való kérések hitelesítő adatainak hitelesítéséhez és az API-műveletek meghatározásához. Ezeket a háttéreszközöket a Azure Portal Azure API-k vagy eszközök használatával konfigurálhatja és kezelheti.

A háttér létrehozása után hivatkozhat a háttérbeli URL-címre az API-kban. A szabályzat használatával átirányíthatja a bejövő API-kéréseket az egyéni háttérhez az API [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) alapértelmezett háttérbeállítása helyett.

## <a name="benefits-of-backends"></a>A háttérbeli háttérbeliek előnyei

Az egyéni háttérnek számos előnye van, például:

* A háttérszolgáltatással kapcsolatos információk absztrakciója, az API-k újrahasználhatóságának elősegítése és a továbbfejlesztett irányítás  
* Könnyen használható egy meglévő API átalakítási szabályzatának konfigurálásával
* Kihasználja a API Management funkcióit a titkos kulcsok Azure Key Vault ha [nevestűs](api-management-howto-properties.md) értékek vannak konfigurálva a fejléc- vagy lekérdezésparaméter-hitelesítéshez

## <a name="next-steps"></a>Következő lépések

* Állítson be [Service Fabric háttérkészletet](how-to-configure-service-fabric-backend.md) a Azure Portal.
* A háttér is konfigurálható a következő sablonokkal: , API Management [REST API,](/rest/api/apimanagement) [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)vagy [Azure Resource Manager .](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

