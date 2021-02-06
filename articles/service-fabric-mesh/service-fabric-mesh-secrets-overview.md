---
title: Az Azure Service Fabric Mesh alkalmazás titkainak tárolása és használata
description: Service Fabric Mesh a titkokat Azure-erőforrásokként támogatja. A titkok tárolása és kezelése a Service Fabric Mesh-alkalmazásokkal.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625532"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh-alkalmazás titkai

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh a titkokat Azure-erőforrásokként támogatja. A Service Fabric Mesh titkos kulcsa lehet bármilyen bizalmas szöveges információ, például a tárolási kapcsolatok sztringje, jelszava vagy más olyan érték, amelyet biztonságosan kell tárolni és továbbítani.

![A Mesh titkainak áttekintése][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh titkok erőforrásai
A háló alkalmazás titkos kulcsa a következőkből áll:
* A **titkok** erőforrása, amely egy olyan tároló, amely a szöveges titkokat tárolja. A **titkok** erőforrásban található titkos kódok tárolása és továbbítása biztonságos.
* Egy vagy több **titok/érték** erőforrás, amely a **titkok** erőforrás-tárolóban van tárolva. Az egyes **titkok/értékek** erőforrásait a verziószáma különbözteti meg.

## <a name="next-steps"></a>Következő lépések 
A Service Fabric Mesh-titkokkal kapcsolatos további tudnivalókért tekintse meg a következőt:
- [Service Fabric Mesh-alkalmazás titkainak kezelése](service-fabric-mesh-howto-manage-secrets.md)
- [A Service Fabric Resource Model bemutatása](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
