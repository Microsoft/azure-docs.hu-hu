---
title: Az eszköz konverziós REST API
description: Ismerteti, hogyan lehet átalakítani egy eszközt a REST API használatával
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e3db4b9c9b4a05142f1327f681b067748cb1a2f9
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951640"
---
# <a name="use-the-model-conversion-rest-api"></a>A modellátalakító REST API használata

A [modell-átalakítási](model-conversion.md) szolgáltatást egy [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer)vezérli. Ez az API a konverziók létrehozásához, az átalakítási tulajdonságok beszerzéséhez és a meglévő konverziók listázásához használható.

## <a name="rest-api-reference"></a>REST API-referencia

A távoli renderelés REST API dokumentációja [itt](/rest/api/mixedreality/2021-01-01/remoterendering)található, és a hencegő definíciók [itt](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)találhatók.

Biztosítunk egy PowerShell-szkriptet az [ARR Samples adattárában](https://github.com/Azure/azure-remote-rendering) a *Scripts* mappában, amelyet a *Conversion.ps1* nevezünk, amely a szolgáltatás használatát mutatja be. A szkriptet és annak konfigurációját itt találja: [példa PowerShell-parancsfájlok](../../samples/powershell-example-scripts.md). A [.net](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/remoterendering/Azure.MixedReality.RemoteRendering/README.md) és a [Java]( https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/remoterendering/azure-mixedreality-remoterendering/README.md)SDK-kat is biztosítunk.

## <a name="next-steps"></a>Következő lépések

- [Modellek átalakítása az Azure Blob Storage használatával](blob-storage.md)
- [Modell átalakítása](model-conversion.md)