---
title: Kényszerített bújtatás Azure Firewall
description: A kényszerített bújtatást beállíthatja úgy, hogy az internetes forgalmat egy további tűzfalra vagy hálózati virtuális készülékre irányítsa további feldolgozás céljából.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88612595"
---
# <a name="azure-firewall-forced-tunneling"></a>Kényszerített bújtatás Azure Firewall

Amikor új Azure Firewallt konfigurál, az összes internetes forgalmat átirányíthatja egy kijelölt következő ugrásra a közvetlen internetkapcsolat helyett. Előfordulhat például, hogy egy helyszíni peremhálózati tűzfallal vagy más hálózati virtuális berendezéssel (NVA) szeretné feldolgozni a hálózati forgalmat az internetre való továbbítás előtt. A kényszerített bújtatáshoz azonban nem konfigurálhat meglévő tűzfalat.

Alapértelmezés szerint a kényszerített bújtatás nem engedélyezett a Azure Firewallon annak biztosítása érdekében, hogy az összes kimenő Azure-függősége teljesül. A felhasználó által megadott route-(UDR-) konfigurációk a *AzureFirewallSubnet* , amelyek alapértelmezett útvonala nem közvetlenül az internetre van letiltva.

## <a name="forced-tunneling-configuration"></a>Kényszerített bújtatás konfigurálása

A kényszerített bújtatás támogatásához a szolgáltatás-felügyeleti forgalom el van különítve az ügyfelek forgalmával. Egy *AzureFirewallManagementSubnet* nevű további dedikált alhálózat (minimális alhálózat mérete/26) szükséges a saját társított nyilvános IP-címéhez. Az ezen az alhálózaton engedélyezett egyetlen útvonal az Internet alapértelmezett útvonala, a BGP-útvonalak propagálását pedig le kell tiltani.

Ha a BGP-n keresztül meghirdetett alapértelmezett útvonalon keresztül kényszeríti a forgalmat a helyszíni rendszerbe, létre kell hoznia a *AzureFirewallSubnet* és a *AzureFirewallManagementSubnet* a tűzfal üzembe helyezése előtt, és rendelkeznie kell egy, az internetre irányuló alapértelmezett útvonallal, és az **átjáró-útvonalak propagálása** le kell UDR.

Ebben a konfigurációban a *AzureFirewallSubnet* mostantól a helyszíni tűzfalakhoz vagy NVA útvonalakat is tartalmazhatnak a forgalom feldolgozásához, mielőtt a rendszer átadja az internetre. Ezeket az útvonalakat a BGP-n keresztül is közzéteheti a *AzureFirewallSubnet* , ha az **átjárók terjesztési útvonalai** engedélyezve vannak ezen az alhálózaton.

Létrehozhat például egy alapértelmezett útvonalat a *AzureFirewallSubnet* a VPN-átjáróval a következő ugrásként a helyszíni eszköz beszerzéséhez. Vagy engedélyezheti az **átjáró-útvonalak propagálását** , hogy megkapják a megfelelő útvonalakat a helyszíni hálózat számára.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Virtuális hálózati átjáró útvonalának propagálása":::

Ha engedélyezi a kényszerített bújtatást, az internethez kötött forgalom a AzureFirewallSubnet egyik címfordítást, a forrást pedig a helyszíni tűzfalból elrejti.

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall SNATs a forgalmat a AzureFirewallSubnet egyik tűzfal magánhálózati IP-címére. Azonban úgy is konfigurálhatja a Azure Firewallt, hogy **ne** SNAT a nyilvános IP-címtartományt. További információ: [Azure Firewall SNAT magánhálózati IP-címtartományok](snat-private-range.md).

Miután konfigurálta Azure Firewall a kényszerített bújtatás támogatásához, nem vonhatja vissza a konfigurációt. Ha eltávolítja az összes többi IP-konfigurációt a tűzfalon, a rendszer eltávolítja a felügyeleti IP-konfigurációt is, és a tűzfal fel van foglalva. A felügyeleti IP-konfigurációhoz rendelt nyilvános IP-cím nem távolítható el, de másik nyilvános IP-címet is hozzárendelhet.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)
