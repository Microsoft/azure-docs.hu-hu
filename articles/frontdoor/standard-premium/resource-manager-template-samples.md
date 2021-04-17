---
title: Resource Manager mintasablonok – Azure Front Door
description: A szolgáltatáshoz Azure Resource Manager mintasablonokkal kapcsolatos Azure Front Door.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: e939aec8f1cf5fa615904bb81bad82ea3cf22c61
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587476"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager sablonok a Azure Front Door

> [!Note]
> Ez a dokumentáció a Azure Front Door Standard/Prémium (előzetes verzió) verzióhoz érhető el. Információt keres a Azure Front Door? Tekintse [meg itt:](../front-door-overview.md).

A következő táblázat hivatkozásokat tartalmaz Azure Resource Manager-sablonokhoz, Azure Front Door referenciaarchitektúrákat tartalmaz, beleértve az egyéb Azure-szolgáltatásokat is.

| Sample | Leírás |
|-|-|
| [Front Door (gyors létrehozás)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | Létrehoz egy alapszintű Front Door profilt, beleértve a végpontot, a forráscsoportot, a forrást és az útvonalat.  |
| [Szabálykészlet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Létrehoz egy Front Door profilt és szabálykészletet.  |
| [WAF-szabályzat felügyelt szabálykészletekkel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | Létrehoz egy Front Door profilt és WAF-et felügyelt szabálykészletekkel.  |
| [WAF-szabályzat egyéni s szabályokkal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | Létrehoz egy Front Door-profilt és a WAF-et egyéni s szabályokkal.  |
|**App Service forrás**| **Leírás** |
| [APP SERVICE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Létrehoz egy App Service egy nyilvános végponttal és egy Front Door profillal.  |
| [App Service a Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Létrehoz egy App Service egy privát végponttal és egy Front Door profillal.  |
| [App Service környezet és Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Létrehoz egy App Service környezetet, egy privát végponttal és egy Front Door profillal.  |
|**Azure Functions forrás**| **Leírás** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Létrehoz egy Azure Functions egy nyilvános végponttal és egy Front Door profillal.  |
| [Azure Functions a Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Létrehoz egy Azure Functions egy privát végponttal és egy Front Door profillal.  |
|**API Management forrás**| **Leírás** |
| [API Management (külső)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Létrehoz egy API Management VNet-integrációval és egy Front Door profillal.  |
|**Tárolási forrás**| **Leírás** |
| [Statikus Storage-webhely](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Létrehoz egy Azure Storage-fiókot és egy statikus webhelyet egy nyilvános végponttal és egy Front Door profillal.  |
| [Storage-blobok Private Link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Létrehoz egy Azure Storage-fiókot és egy blobtárolót egy privát végponttal és egy Front Door profillal.  |
|**Application Gateway forrás**| **Leírás** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Létrehoz egy Application Gateway és egy Front Door profilt. |
|**A virtuális gép eredete**| **Leírás** |
| [Virtuális gép Private Link szolgáltatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Létrehoz egy virtuális gépet és Private Link szolgáltatást, valamint egy Front Door profilt. |
| | |
