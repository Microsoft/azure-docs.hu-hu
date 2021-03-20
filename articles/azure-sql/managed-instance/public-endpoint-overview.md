---
title: Azure SQL felügyelt példányok nyilvános végpontjának védelme
description: Nyilvános végpontok biztonságos használata az Azure SQL felügyelt példányain
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: d9c1828732b9a4e0e85c3af2263f097edd54437d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332848"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Az Azure SQL felügyelt példányának biztonságos használata nyilvános végpontokkal
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példánya felhasználói kapcsolatot biztosíthat a [nyilvános végpontokon](../../virtual-network/virtual-network-service-endpoints-overview.md)keresztül. Ez a cikk azt ismerteti, hogyan lehet ezt a konfigurációt biztonságosabbá tenni.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure SQL felügyelt példánya egy privát végpontot biztosít, amely lehetővé teszi a kapcsolat használatát a virtuális hálózatán belülről. Az alapértelmezett beállítás a maximális elkülönítés biztosítása. Vannak azonban olyan helyzetek, amikor nyilvános végponti kapcsolatokat kell megadnia:

- A felügyelt példánynak integrálva kell lennie a több-bérlős platform-szolgáltatásként elérhető (Pásti) ajánlatokkal.
- Az adatcsere nagyobb átviteli sebességre van szüksége, mint amennyire VPN használata esetén van szükség.
- A céges szabályzatok nem tiltják a vállalati hálózatokon belüli Pásti használatát.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Felügyelt példány üzembe helyezése nyilvános végponti hozzáféréshez

Bár nem kötelező, a nyilvános végponti hozzáféréssel rendelkező felügyelt példányok közös üzembe helyezési modellje a példányt egy dedikált elkülönített virtuális hálózatban hozza létre. Ebben a konfigurációban a virtuális hálózat csak a virtuális fürtök elkülönítésére szolgál. Nem számít, hogy a felügyelt példány IP-címének mérete átfedésben van-e a vállalati hálózat IP-címével.

## <a name="secure-data-in-motion"></a>Mozgásban lévő adatvédelmek

Az SQL felügyelt példány adatforgalma mindig titkosítva van, ha az ügyfél-illesztőprogram támogatja a titkosítást. A felügyelt példány és más Azure-beli virtuális gépek vagy Azure-szolgáltatások között elküldett adatszolgáltatások soha nem hagyják el az Azure gerincét. Ha van kapcsolat a felügyelt példány és egy helyszíni hálózat között, javasoljuk, hogy használja az Azure ExpressRoute-t. A ExpressRoute segítségével elkerülheti az adatáthelyezést a nyilvános interneten keresztül. A felügyelt példányok magánhálózati kapcsolata esetében csak a privát társak használhatók.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bejövő és kimenő kapcsolat zárolása

A következő ábrán az ajánlott biztonsági konfigurációk láthatók:

![A bejövő és a kimenő kapcsolat zárolására szolgáló biztonsági konfigurációk](./media/public-endpoint-overview/managed-instance-vnet.png)

A felügyelt példány [dedikált nyilvános végponti címe](management-endpoint-find-ip-address.md). Az ügyféloldali kimenő tűzfalon és a hálózati biztonsági csoport szabályaiban állítsa be ezt a nyilvános végponti IP-címet a kimenő kapcsolat korlátozására.

Ha biztosítani szeretné, hogy a felügyelt példány forgalma megbízható forrásból származik, javasoljuk, hogy a jól ismert IP-címmel rendelkező forrásokhoz kapcsolódjon. Használjon hálózati biztonsági csoportot a felügyelt példány nyilvános végpontjának a 3342-es porton való hozzáférésének korlátozásához.

Ha az ügyfeleknek csatlakozniuk kell egy helyszíni hálózatról, győződjön meg arról, hogy a rendszer az eredeti címet egy jól ismert IP-címekre fordítja le. Ha ezt nem teszi meg (például a mobil munkaerő tipikus forgatókönyv), javasoljuk, hogy [pont – hely típusú VPN-kapcsolatokat és privát végpontot](point-to-site-p2s-configure.md)használjon.

Ha a kapcsolatok az Azure-ból indulnak, javasoljuk, hogy a forgalom egy jól ismert, hozzárendelt [virtuális IP-címről](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (például egy virtuális gépről) származik. A virtuális IP-címek (VIP) egyszerűbb kezelése érdekében érdemes lehet [nyilvános IP-cím előtagokat](../../virtual-network/public-ip-address-prefix.md)használni.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan konfigurálhat nyilvános végpontot a következő példányok kezeléséhez: [nyilvános végpont konfigurálása](public-endpoint-configure.md)
