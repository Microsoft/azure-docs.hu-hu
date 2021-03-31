---
title: Azure DNS használata más Azure-szolgáltatásokkal
description: Ebből a képzési tervből megtudhatja, hogyan használhatja a Azure DNSt más Azure-szolgáltatások neveinek feloldásához
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76937251"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Hogyan működik a Azure DNS más Azure-szolgáltatásokkal?

Azure DNS egy üzemeltetett DNS-felügyeleti és névfeloldási szolgáltatás. Használhatja nyilvános DNS-nevek létrehozására az Azure-ban üzembe helyezett egyéb alkalmazásokhoz és szolgáltatásokhoz. Az Azure-szolgáltatás nevének létrehozása az egyéni tartományban egyszerű. Csak a megfelelő típusú rekordot adja hozzá a szolgáltatáshoz.

* A dinamikusan lefoglalt IP-címekhez létrehozhat egy DNS-beli CNAME rekordot, amely az Azure által a szolgáltatáshoz létrehozott DNS-névhez van leképezve. A DNS-szabványok megakadályozzák, hogy a zóna csúcspontjának CNAME rekordját használják. Ehelyett használhat alias-rekordot. További információ [: oktatóanyag: alias-rekord konfigurálása, amely egy Azure nyilvános IP-címre hivatkozik](tutorial-alias-pip.md).
* A statikusan lefoglalt IP-címek esetében bármilyen névvel létrehozhat egy DNS-rekordot, amely magában foglal egy *meztelen tartománynevet* is a zóna csúcsán.

Az alábbi táblázat a különböző Azure-szolgáltatásokhoz használható támogatott bejegyzéstípusokat vázolja fel. Ahogy a táblázat mutatja, Azure DNS csak az internetre irányuló hálózati erőforrásokhoz tartozó DNS-rekordokat támogatja. Azure DNS nem használható belső, magánhálózati címek névfeloldásához.

| Azure-szolgáltatás | Hálózati adapter | Description |
| --- | --- | --- |
| Azure Application Gateway |[Előtér-nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Létrehozhat egy DNS-vagy CNAME-rekordot is. |
| Azure Load Balancer |[Előtér-nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Létrehozhat egy DNS-vagy CNAME-rekordot is. A Load Balancer a dinamikusan hozzárendelt IPv6 nyilvános IP-címmel rendelkezhet. Hozzon létre egy CNAME-rekordot egy IPv6-cím számára. |
| Azure Traffic Manager |Nyilvános név |Létrehozhat egy olyan alias-rekordot, amely leképezi a Traffic Manager-profilhoz rendelt trafficmanager.net-nevet. További információ: [oktatóanyag: alias-rekord konfigurálása a APEX-tartománynevek támogatásához a Traffic Manager használatával](tutorial-alias-tm.md). |
| Azure Cloud Services |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |Statikusan lefoglalt IP-címek esetén létrehozhat egy rekordot a DNS-ben. A dinamikusan lefoglalt IP-címekhez létre kell hoznia egy CNAME rekordot, amely leképezi a *cloudapp.net* nevét.|
| Azure App Service | [Külső IP-cím](dns-custom-domain.md#app-service-web-apps) |Külső IP-címek esetén létrehozhat egy rekordot a DNS-ben. Ellenkező esetben létre kell hoznia egy CNAME rekordot, amely leképezi a azurewebsites.net nevét. További információ: [Egyéni tartománynév hozzárendelése egy Azure-alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager virtuális gépek |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A Resource Manager-alapú virtuális gépek rendelkezhetnek nyilvános IP-címekkel. Egy nyilvános IP-címmel rendelkező virtuális gép is lehet egy terheléselosztó mögött. Létrehozhat egy DNS-, CNAME-vagy alias-rekordot a nyilvános címekhez. Ezt az egyéni nevet használhatja a virtuális IP-cím kihagyása érdekében a terheléselosztó használatával. |
| A klasszikus virtuális gépeket |[Nyilvános IP-cím](dns-custom-domain.md#public-ip-address) |A PowerShell vagy a CLI használatával létrehozott klasszikus virtuális gépeket dinamikus vagy statikus (fenntartott) virtuális címekkel lehet konfigurálni. Létrehozhat egy DNS-CNAME vagy egy rekordot is. |
