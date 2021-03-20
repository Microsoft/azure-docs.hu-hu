---
title: Privát kapcsolati szolgáltatás megosztása a virtuális WAN-on keresztül
titleSuffix: Azure Virtual WAN
description: A privát kapcsolat konfigurálásának lépései a virtuális WAN-ban
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913755"
---
# <a name="use-private-link-in-virtual-wan"></a>Privát hivatkozás használata a virtuális WAN-ban

Az [Azure Private link](../private-link/private-link-overview.md) egy olyan technológia, amely lehetővé teszi, hogy [privát végpontok](../private-link/private-endpoint-overview.md)kiválasztásával összekapcsolja a privát IP-cím kapcsolattal rendelkező Azure platform-szolgáltatásbeli ajánlatokat. Az Azure Virtual WAN használatával egy virtuális hubhoz csatlakoztatott virtuális hálózat egyikében telepítheti a privát végpontokat. Ez kapcsolatot biztosít bármely más virtuális hálózattal vagy fiókteleppel, amely ugyanahhoz a virtuális WAN-hoz csatlakozik.

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések azt feltételezik, hogy már üzembe helyezett egy virtuális WAN-t egy vagy több hubhoz, valamint legalább két, a virtuális WAN-hoz csatlakoztatott virtuális hálózatot.

Új virtuális WAN és új központ létrehozásához kövesse az alábbi cikkekben ismertetett lépéseket:

* [Virtuális WAN létrehozása](virtual-wan-site-to-site-portal.md#openvwan)
* [Elosztó létrehozása](virtual-wan-site-to-site-portal.md#hub)
* [Virtuális hálózat csatlakoztatása elosztóhoz](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Privát hivatkozás végpontjának létrehozása

Több különböző szolgáltatáshoz is létrehozhat egy privát kapcsolati végpontot. Ebben a példában a Azure SQL Database fogjuk használni. A következő témakörben talál további információt arról, hogyan hozhat létre privát végpontot egy Azure SQL Databasehoz a gyors útmutatóban [: privát végpont létrehozása a Azure Portal használatával](../private-link/create-private-endpoint-portal.md). Az alábbi képen a Azure SQL Database hálózati konfigurációja látható:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="privát hivatkozás létrehozása" lightbox="./media/howto-private-link/create-private-link.png":::

A Azure SQL Database létrehozása után ellenőrizheti a magánhálózati végpont IP-címét, és megkeresheti a privát végpontokat:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="privát végpontok" lightbox="./media/howto-private-link/endpoints.png":::

Ha a létrehozott privát végpontra kattint, megjelenik a saját magánhálózati IP-címe, valamint a teljes tartományneve (FQDN). Vegye figyelembe, hogy a magánhálózati végpont IP-címmel rendelkezik azon VNet (10.1.3.0/24) tartományában:

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL-végpont" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Azonos VNet létesített kapcsolat ellenőrzése

Ebben a példában ellenőrizni fogjuk, hogy a Azure SQL Database egy Ubuntu virtuális gépről, amelyen telepítve van az MS SQL Tools. Első lépésként ellenőrizze, hogy a DNS-feloldás működik-e, és a Azure SQL Database teljes tartománynevet egy magánhálózati IP-címhez oldja-e fel, ugyanabban a VNet, ahol a magánhálózati végpont üzembe lett helyezve (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Ahogy az előző kimenetben is látható, a teljes tartománynév le `wantest.database.windows.net` lesz képezve `wantest.privatelink.database.windows.net` , hogy a privát végponton létrehozott magánhálózati DNS-zóna feloldja a magánhálózati IP-címet `10.1.3.228` . A magánhálózati DNS-zónába való betekintéssel meggyőződhet arról, hogy a magánhálózati IP-címhez hozzárendelt privát végponthoz tartozó rekord van:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS-zóna" lightbox="./media/howto-private-link/dns-zone.png":::

A helyes DNS-feloldás ellenőrzése után megkísérelhető a Kapcsolódás az adatbázishoz:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Amint láthatja, egy speciális SQL-lekérdezést használunk, amely megadja a forrás IP-címet, amelyet az SQL Server az ügyféltől lát. Ebben az esetben a kiszolgáló a saját magánhálózati IP-címével () látja az ügyfelet `10.1.3.75` , ami azt jelenti, hogy a forgalom a VNet egyenesen a privát végpontba kerül.

Vegye figyelembe, hogy az útmutatóban szereplő példáknak megfelelően be kell állítania a változókat `username` , és meg kell `password` egyeznie a Azure SQL Databaseban definiált hitelesítő adatokkal.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Kapcsolat egy másik VNet

Most, hogy az Azure Virtual WAN egyik VNet kapcsolódik a privát végponthoz, a virtuális WAN-hoz csatlakozó összes többi virtuális hálózatok és ág is hozzáférhet a szolgáltatáshoz. Meg kell adnia a kapcsolatot az Azure Virtual WAN által támogatott modellek bármelyikén keresztül, például a [bármilyen](scenario-any-to-any.md) módon vagy a [megosztott szolgáltatások VNet forgatókönyvben](scenario-shared-services-vnet.md), hogy két példát említsünk.

Miután kapcsolódott a VNet vagy az ág azon VNet, ahol a magánhálózati végpont üzembe lett állítva, konfigurálnia kell a DNS-feloldást:

* Ha egy VNet csatlakozik a privát végponthoz, használhatja ugyanazt a saját zónát, amelyet a Azure SQL Database hozott létre.
* Ha egy ág (helyek közötti VPN, pont – hely VPN vagy ExpressRoute) használatával csatlakozik a magánhálózati végponthoz, a helyszíni DNS-feloldást kell használnia.

Ebben a példában egy másik VNet fogunk csatlakozni, ezért először csatoljuk a magánhálózati DNS-zónát az új VNet, hogy a számítási feladatok feloldják az Azure SQL Database teljes tartománynevet a magánhálózati IP-címekre. Ez a magánhálózati DNS-zóna új VNet való összekapcsolásával történik:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS-hivatkozás" lightbox="./media/howto-private-link/dns-link.png":::

A csatolt VNet lévő bármelyik virtuális gépnek megfelelően fel kell oldania az Azure SQL Database FQDN-t a privát kapcsolat magánhálózati IP-címére:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Annak ellenőrzéséhez, hogy ez a VNet (10.1.1.0/24) kapcsolódik-e az eredeti VNet, ahol a magánhálózati végpont konfigurálva volt (10.1.3.0/24), a VNet bármely virtuális gépén ellenőrizheti a hatályos útválasztási táblázatot:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="érvényes útvonalak" lightbox="./media/howto-private-link/effective-routes.png":::

Amint láthatja, az Azure Virtual WAN Virtual Network-átjárói által befecskendezett VNet 10.1.3.0/24 útvonal látható. Most már végre is tesztelheti a kapcsolatot az adatbázissal:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

Ebben a példában láttuk, hogy a virtuális WAN-hoz csatlakoztatott egyik virtuális hálózatok hogyan hozhat létre egy privát végpontot a virtuális WAN-ban található többi virtuális hálózatok és ág kapcsolatával.

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
