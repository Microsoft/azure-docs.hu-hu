---
title: Azure Virtual Network – fogalmak és ajánlott eljárások
description: Ismerkedjen meg az Azure Virtual Network-fogalmakkal és az ajánlott eljárásokkal.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223618"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Az Azure Virtual Network fogalmak és ajánlott eljárások

Ez a cikk az Azure Virtual Network (VNet) főbb fogalmait és ajánlott eljárásait ismerteti.

## <a name="vnet-concepts"></a>VNet fogalmak

- **Címterület:** VNet létrehozásakor egyéni magánhálózati IP-címtartományt kell megadnia nyilvános és magánhálózati (RFC 1918) címek használatával. Az Azure magánhálózati IP-címet rendel hozzá a virtuális hálózatokon belüli erőforrásokhoz a hozzárendelt címtérből. Ha például egy virtuális gépet központilag telepít egy VNet, a 10.0.0.0/16, a virtuális gép egy magánhálózati IP-címet (például 10.0.0.4) fog rendelni.
- **Alhálózatok:** Az alhálózatok lehetővé teszik a virtuális hálózat egy vagy több alhálózatra való felosztását, és a virtuális hálózat címterület egy részének lefoglalását az egyes alhálózatokra. Ezután üzembe helyezheti az Azure-erőforrásokat egy adott alhálózaton. A hagyományos hálózatokhoz hasonlóan az alhálózatok is lehetővé teszik, hogy a VNet-címtartomány a szervezet belső hálózatának megfelelő szegmensekre legyen felosztva. Ez javítja a címek kiosztásának hatékonyságát is. Az alhálózatokon belüli erőforrásokat hálózati biztonsági csoportokkal is biztonságossá teheti. További információ: [hálózati biztonsági csoportok](./network-security-groups-overview.md).
- **Régiók**: a VNet egyetlen régióra/helyre vonatkozik; azonban a különböző régiókban található több virtuális hálózat összekapcsolható Virtual Network-társítással.
- **Előfizetés:** A VNet hatóköre egy előfizetés. Több virtuális hálózatot is megvalósíthat az egyes Azure-[előfizetéseken](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és Azure-[régiókon](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) belül.

## <a name="best-practices"></a>Ajánlott eljárások

A hálózat Azure-ban való létrehozásakor fontos szem előtt tartani a következő általános tervezési alapelveket:

- Győződjön meg arról, hogy nem átfedésben lévő címterület. Győződjön meg arról, hogy a VNet címtartomány (CIDR-blokk) nem fedi át a szervezet más hálózati tartományait.
- Az alhálózatok nem fedik le a VNet teljes címterületját. Tervezze meg előre, és foglaljon le némi címtartományt a jövő számára.
- Azt javasoljuk, hogy több kis virtuális hálózatok helyett kevesebb nagy virtuális hálózatok legyen. Ez megakadályozza a felügyelet terhelését.
- A VNet biztonságossá tételéhez rendeljen hozzá hálózati biztonsági csoportokat (NSG) az alá tartozó alhálózatokhoz.

## <a name="next-steps"></a>Következő lépések

 A virtuális hálózatok használatának megkezdéséhez hozzon létre egyet, helyezzen üzembe rajta néhány virtuális gépet, majd létesítsen kommunikációt a virtuális gépek között. Ennek ismertetéséhez tekintse meg a [Virtuális hálózat létrehozása](quick-create-portal.md) című rövid útmutatót.