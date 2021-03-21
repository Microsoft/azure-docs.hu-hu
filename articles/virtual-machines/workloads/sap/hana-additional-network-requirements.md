---
title: Az Azure-beli SAP HANA további hálózati követelményei (nagyméretű példányok) | Microsoft Docs
description: Az Azure-beli SAP HANA további hálózati követelményei (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83a695792e78916bf79f4d4cf1394ae197c25fb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671865"
---
# <a name="additional-network-requirements-for-large-instances"></a>A nagyméretű példányok további hálózati követelményei

Előfordulhat, hogy további hálózati követelményekre van szüksége az Azure-beli SAP HANA nagyméretű példányainak központi telepítésének részeként.

## <a name="add-more-ip-addresses-or-subnets"></a>További IP-címek vagy alhálózatok hozzáadása

Ha több IP-címet vagy alhálózatot ad hozzá, használja a Azure Portal, a PowerShell vagy az Azure CLI-t.

Új összesített tartomány létrehozása helyett adja hozzá az új IP-címtartományt új tartományként a virtuális hálózati címtartomány területéhez. Ezt a módosítást küldje el a Microsoftnak. Ez lehetővé teszi, hogy az adott új IP-címtartomány és az ügyfélben lévő HANA nagyméretű példány egységei között csatlakozhasson. Nyisson meg egy Azure-támogatási kérést az új virtuális hálózati címtartomány hozzáadásához. Miután megkapta a jóváhagyást, hajtsa végre a következő lépéseket.

Ha további alhálózatot szeretne létrehozni a Azure Portalból, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)című témakört. A PowerShellből való létrehozással kapcsolatban lásd: [virtuális hálózat létrehozása a PowerShell használatával](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Virtuális hálózatok hozzáadása

Miután először csatlakoztatta egy vagy több Azure-beli virtuális hálózatot, érdemes lehet további, az Azure-ban SAP HANA (nagyméretű példányok) elérését is csatlakoztatni. Először küldje el az Azure-támogatási kérelmet. Ebben a kérelemben adja meg az adott Azure-telepítést azonosító adatokat. Adja meg az IP-címtartomány tartományát vagy az Azure-beli virtuális hálózati címtartomány tartományait is. A Microsoft Service Management SAP HANA a további virtuális hálózatok és az Azure-ExpressRoute összekapcsolásához szükséges információkat biztosítja. Minden virtuális hálózat esetében egyedi engedélyezési kulcsra van szükség, hogy a ExpressRoute-áramkörhöz a nagyméretű HANA-példányokhoz kapcsolódjon.

## <a name="increase-expressroute-circuit-bandwidth"></a>ExpressRoute-áramköri sávszélesség növelésének fokozása

Forduljon SAP HANA a Microsoft Service Management szolgáltatáshoz. Ha azt javasoljuk, hogy növelje az Azure-beli (nagyméretű példányok) ExpressRoute áramköri SAP HANA sávszélességét, hozzon létre egy Azure-támogatási kérelmet. (Legfeljebb 10 GB/s sebességű egyetlen áramköri sávszélességet igényelhet.) Ezután értesítést kap a művelet befejezését követően. nem kell mást tennie, hogy engedélyezze ezt a nagyobb sebességet az Azure-ban.

## <a name="add-an-additional-expressroute-circuit"></a>További ExpressRoute áramkör hozzáadása

Forduljon SAP HANA a Microsoft Service Management szolgáltatáshoz. Ha további ExpressRoute-áramkör hozzáadására tesznek javaslatot, hozzon létre egy Azure-támogatási kérést (beleértve az új áramkörhöz való kapcsolódáshoz szükséges hitelesítési adatok beszerzésére vonatkozó kérést is). A kérelem elkészítése előtt meg kell határoznia a virtuális hálózatokon használt címtartomány méretét. Az SAP HANA a Microsoft Service Management szolgáltatásban megadhatja az engedélyt.

Amikor létrejön az új áramkör, és a Microsoft Service Management konfigurációjának SAP HANAa befejeződött, értesítést kap a folytatáshoz szükséges információkkal. Az Azure-beli virtuális hálózatok nem csatlakoztathatók ehhez a további áramkörhöz, ha már csatlakoznak egy másik SAP HANAhoz az Azure-beli (nagyméretű példány) ExpressRoute áramkörben ugyanabban az Azure-régióban.

## <a name="delete-a-subnet"></a>Alhálózat törlése

A virtuális hálózati alhálózat eltávolításához használhatja a Azure Portal, a PowerShellt vagy az Azure CLI-t. Ha az Azure-beli virtuális hálózati IP-címtartomány vagy a Címterület összesített tartománya volt, a Microsoft nem végez nyomon követést. (Ne feledje azonban, hogy a virtuális hálózat továbbra is propagálja a BGP-útvonal címterület-területét, amely tartalmazza a törölt alhálózatot.) Lehetséges, hogy az Azure-beli virtuális hálózati címtartomány vagy a címtartomány több IP-címtartomány formájában lett definiálva, amelyek közül egyet a törölt alhálózathoz rendeltek. Ügyeljen arra, hogy a virtuális hálózati címtartomány alapján törölje azt. Ezután tájékoztassa SAP HANA a Microsoft Service Management szolgáltatásról, hogy eltávolítsa azt az Azure-on SAP HANA tartományokból (nagyméretű példányok).

További információ: [alhálózat törlése](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

További információ: [Virtual Network (virtuális hálózat törlése](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)).

A Microsoft Service Management SAP HANA eltávolítja a meglévő engedélyeket az Azure-beli (nagyméretű példányok) ExpressRoute áramkörön található SAP HANA. Emellett eltávolítja az Azure Virtual Network IP-címtartomány vagy a címtartomány a HANA nagyméretű példányokkal folytatott kommunikációhoz.

A virtuális hálózat eltávolítása után nyisson meg egy Azure-támogatási kérést az IP-címtartomány vagy az eltávolítandó tartományok megadásához.

Annak érdekében, hogy eltávolítsa a mindent, törölje a ExpressRoute, a virtuális hálózati átjárót, a virtuális hálózati átjáró nyilvános IP-címét és a virtuális hálózatot.

## <a name="delete-an-expressroute-circuit"></a>ExpressRoute kör törlése

Ha el szeretne távolítani egy további SAP HANAt az Azure-beli (nagyméretű példányok) ExpressRoute áramkörön, nyisson meg egy Azure-támogatási kérelmet a SAP HANA a Microsoft Service Management szolgáltatásban. Az áramkör törlésére vonatkozó kérés. Az Azure-előfizetésen belül szükség esetén törölheti vagy megtarthatja a virtuális hálózatot. Azonban a HANA Large instances ExpressRoute áramkör és a társított virtuális hálózati átjáró közötti kapcsolatot törölnie kell.

## <a name="next-steps"></a>Következő lépések

- [Az SAP HANA (nagy méretű példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md)
