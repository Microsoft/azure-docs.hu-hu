---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648277"
---
A Azure Functions támogatja a több eredetű erőforrás-megosztást (CORS). A CORS a [portálon](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) és az [Azure CLI](/cli/azure/functionapp/cors)-n keresztül van konfigurálva. A CORS engedélyezett Origins listája a függvény alkalmazás szintjén érvényes. Ha a CORS engedélyezve van, a válaszok tartalmazzák a `Access-Control-Allow-Origin` fejlécet. További információ: [Eltérő eredetű erőforrás-megosztás](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) 