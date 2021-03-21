---
title: A Container-ajánlatok közzétételi útmutatója az Azure Marketplace-en
description: Ez a cikk a tároló-ajánlatok Azure Marketplace-en való közzétételének követelményeit ismerteti.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741661"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Az Azure Container-ajánlatok közzétételi útmutatója

Az Azure Container segítséget nyújt a tárolók rendszerképének közzétételéhez az Azure Marketplace-en. Ebben az útmutatóban megismerheti az ajánlat típusának követelményeit.

Az Azure Container ajánlatok az Azure Marketplace-en üzembe helyezett és számlázható tranzakciós ajánlatok. A felhasználó által megjelenő listázási lehetőség a "Letöltés most".

Ha a megoldás egy olyan Docker-tároló-rendszerkép, amely Kubernetes-alapú Azure Container-példányként van beállítva, használja az Azure Container ajánlat típusát.

> [!NOTE]
> Az Azure Container instance egy futásidejű Docker-példány, amely a tárolók Azure-ban való futtatásának leggyorsabb és legegyszerűbb módját biztosítja anélkül, hogy virtuális gépeket kellene kezelnie, és nem kell magasabb szintű szolgáltatást alkalmaznia. A Container instances közvetlenül az Azure-ba helyezhető üzembe, vagy az Azure Kubernetes Services vagy az Azure Kubernetes Service Engine segítségével.  

A Microsoft jelenleg támogatja az ingyenes és a saját licencű (BYOL) licencelési modelleket.

## <a name="container-offer-requirements"></a>A Container-ajánlat követelményei

| Követelmény | Részletek |  
|:--- |:--- |  
| Számlázás és mérés | Az ingyenes vagy a BYOL számlázási modellt is támogatja.<br><br> |  
| Docker létrehozott rendszerkép | A tároló lemezképeit a Docker-rendszerkép specifikációja alapján kell létrehozni, és egy Docker kell létrehozni.<br> <br>A Docker-rendszerképek létrehozásával kapcsolatos további információkért tekintse meg a [Docker dokumentációjának](https://docs.docker.com/engine/reference/builder/#usage)"használat" szakaszát.<br><br> |  
| Üzemeltetés Azure Container Registry adattárban | A tároló lemezképeit egy Azure Container Registry adattárban kell tárolni.<br> <br>A Azure Container Registry használatáról további információt a rövid útmutató [: Private Container Registry létrehozása a Azure Portal használatával](../container-registry/container-registry-get-started-portal.md)című témakörben talál.<br><br> |  
| Képcímkézés | A tároló lemezképének legalább egy címkét tartalmaznia kell (címkék maximális száma: 16).<br><br>A képek címkézésével kapcsolatos további információkért tekintse `docker tag` meg a lapot a [Docker dokumentációs](https://docs.docker.com/engine/reference/commandline/tag) webhelyén.<br><br> |  

## <a name="next-steps"></a>Következő lépések

- Ha technikai eszközöket szeretne felkészíteni egy tárolóra, tekintse meg az [Azure Container technikai eszközeinek létrehozása](create-azure-container-technical-assets.md)című témakört.

- Az Azure Container-ajánlat létrehozásával kapcsolatos további információkért lásd: [Azure Container-ajánlat létrehozása az Azure piactéren](create-azure-container-offer.md) .
