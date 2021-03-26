---
title: Resource Manager-sablonok – minták – Azure bejárati ajtó
description: Információ az Azure-beli bejárati ajtóhoz megadott minta Azure Resource Manager-sablonokról.
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/24/2021
ms.openlocfilehash: 929adb0be948339af033d85b0dabd7e1cedf353e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561746"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Resource Manager Azure-beli előtérben lévő sablonok

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az alábbi táblázat az Azure bejárati ajtókhoz Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz, beleértve az egyéb Azure-szolgáltatásokat is.

| Sample | Leírás |
|-|-|
| [Szabálykészlet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Létrehoz egy előtérben lévő profilt és egy szabálykészlet-készletet.  |
|**App Service eredete**| **Leírás** |
| [APP SERVICE](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | Létrehoz egy App Service alkalmazást egy nyilvános végponttal, és egy bejárati ajtó profilt.  |
| [App Service privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | Létrehoz egy App Service alkalmazást egy privát végponttal és egy bejárati ajtót.  |
| [App Service környezet privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | Létrehoz egy App Service környezetet, egy privát végponttal rendelkező alkalmazást és egy bejárati profilt.  |
|**Azure Functions eredete**| **Leírás** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | Létrehoz egy Azure Functions alkalmazást egy nyilvános végponttal, és egy bejárati ajtó profilt.  |
| [Azure Functions privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | Létrehoz egy Azure Functions alkalmazást egy privát végponttal és egy bejárati ajtót.  |
|**API Management eredete**| **Leírás** |
| [API Management (külső)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | Létrehoz egy API Management példányt külső VNet-integrációval és egy bejárati ajtót.  |
|**Tárolás eredete**| **Leírás** |
| [Tárolási statikus webhely](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Létrehoz egy Azure Storage-fiókot és egy statikus webhelyet nyilvános végponttal, valamint egy bejárati profilt.  |
| [Storage-Blobok privát kapcsolattal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Létrehoz egy Azure Storage-fiókot és egy BLOB-tárolót egy privát végponttal, valamint egy bejárati ajtót.  |
|**Application Gateway eredete**| **Leírás** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Létrehoz egy Application Gateway és egy bejárati ajtó profilt. |
|**Virtuális gépek eredete**| **Leírás** |
| [Virtuális gép privát kapcsolati szolgáltatással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | Létrehoz egy virtuális gépet és egy magánhálózati kapcsolati szolgáltatást, valamint egy bejárati ajtót. |
| | |
