---
title: Azure-szolgáltatásnév létrehozása
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947101"
---
## <a name="create-an-azure-service-principal"></a>Azure egyszerű szolgáltatás létrehozása

Ahhoz, hogy az alkalmazás működjön az Azure-fiókkal, szüksége lesz egy Azure-szolgáltatásra az engedélyek kezeléséhez. Kövesse az [Azure egyszerű szolgáltatás létrehozása](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0)című témakör utasításait.

Egyszerű szolgáltatás létrehozásakor látni fogja, hogy rendelkezik egy titkos értékkel, egy AZONOSÍTÓval és egy alkalmazás-AZONOSÍTÓval. Mentse az alkalmazás AZONOSÍTÓját és a titkos kulcsot egy ideiglenes helyre a későbbi lépések végrehajtásához.
