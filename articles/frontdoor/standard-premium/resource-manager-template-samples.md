---
title: Resource Manager-sablonok – minták – Azure bejárati ajtó
description: Információ az Azure-beli bejárati ajtóhoz megadott minta Azure Resource Manager-sablonokról.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/05/2021
ms.openlocfilehash: 393c15761c16dbf04d2af48fe13c0bc41b2e15b1
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509573"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager Azure-beli előtérben lévő sablonok

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az alábbi táblázat az Azure bejárati ajtókhoz Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz, beleértve az egyéb Azure-szolgáltatásokat is.

| App Service | Leírás |
|-|-|
| [APP SERVICE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Létrehoz egy App Service alkalmazást egy nyilvános végponttal, és egy bejárati ajtó profilt.  |
| [App Service privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Létrehoz egy App Service alkalmazást egy privát végponttal és egy bejárati ajtót.  |
| [App Service környezet privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Létrehoz egy App Service környezetet, egy privát végponttal rendelkező alkalmazást és egy bejárati profilt.  |
|**Azure Functions**| **Leírás** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Létrehoz egy Azure Functions alkalmazást egy nyilvános végponttal, és egy bejárati ajtó profilt.  |
| [Azure Functions privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Létrehoz egy Azure Functions alkalmazást egy privát végponttal és egy bejárati ajtót.  |
|**API Management**| **Leírás** |
| [API Management (külső)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Létrehoz egy API Management példányt külső VNet-integrációval és egy bejárati ajtót.  |
|**Storage**| **Leírás** |
| [Tárolási statikus webhely](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Létrehoz egy Azure Storage-fiókot és egy statikus webhelyet nyilvános végponttal, valamint egy bejárati profilt.  |
| [Storage-Blobok privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Létrehoz egy Azure Storage-fiókot és egy BLOB-tárolót egy privát végponttal, valamint egy bejárati ajtót.  |
| | |
