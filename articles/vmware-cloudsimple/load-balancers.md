---
title: Azure VMware-megoldás a CloudSimple által – válasszon egy terheléselosztási megoldást a CloudSimple privát felhők számára
description: Ismerteti az alkalmazások privát felhőben való üzembe helyezésének terheléselosztási beállításait.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06d2305c209011e4fb1b8ee05a016d2e15a49833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898028"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Terheléselosztási megoldás kiválasztása a privát felhők CloudSimple

Ha egy CloudSimple privát felhőben helyez üzembe egy alkalmazást, a terheléselosztáshoz több beállítást is választhat.

A CloudSimple saját felhőben is kiválaszthatja a virtuális vagy szoftveres terheléselosztó szolgáltatást, vagy akár az Azure-előfizetésében futó Azure L7 Load balancert is használhatja, hogy a webes rétegbeli virtuális gépek a CloudSimple privát felhőben fussanak. Itt felsorolunk néhány lehetőséget:

## <a name="virtual-load-balancers"></a>Virtuális Load Balancer

A vCenter felületen keresztül telepítheti a virtuális Load Balancer-berendezéseket a VMware-környezetbe, és beállíthatja őket az alkalmazás forgalmának befejezéséhez.

Néhány népszerű gyártó: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 Load Balancer

Ha az Azure Application Gatewayt L7 terheléselosztóként használja a privát felhőben futó alkalmazáshoz, nem kell kezelnie a terheléselosztó szoftverét. A terheléselosztó szoftverét az Azure felügyeli. A magánhálózati felhőben lévő összes webes virtuális gép magánhálózati IP-címeket használ, és nincs szükség további NAT-szabályokra vagy nyilvános IP-címekre a nevek feloldásához. A webes rétegbeli virtuális gépek privát, kis késleltetésű, nagy sávszélességű kapcsolaton keresztül kommunikálnak az Azure Application Gateway.

Ha többet szeretne megtudni a megoldás konfigurálásáról, tekintse meg a megoldási útmutatót az Azure Application Gateway L7 Load Balancer használatával történő használatáról.

## <a name="azure-internal-load-balancer"></a>Azure belső terheléselosztó

Ha úgy dönt, hogy az alkalmazást egy hibrid telepítésben futtatja, ahol a webes előtér-réteg az Azure-előfizetésben található Azure-vNet belül fut, és az alkalmazás adatbázis-rétege VMware virtuális gépeken fut a CloudSimple Private Cloud szolgáltatásban, az Azure belső terheléselosztó (L4 Load Balancer) az adatbázis-rétegbeli virtuális gépekhez, az adatforgalom kezeléséhez is használható.

További információt az Azure [belső Load Balancer](../load-balancer/components.md#frontend-ip-configurations) dokumentációjában talál.

## <a name="global-server-load-balancer"></a>Globális kiszolgáló Load Balancer

Ha DNS-alapú Load balancert keres, akkor használhatja az Azure Marketplace-en elérhető, harmadik féltől származó megoldásokat vagy a natív Azure-megoldást is.

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a forgalom optimális elosztását a globális Azure-régiók és a helyszíni szolgáltatások között, miközben magas rendelkezésre állást és válaszadást biztosít. További információt az Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) dokumentációjában talál.
