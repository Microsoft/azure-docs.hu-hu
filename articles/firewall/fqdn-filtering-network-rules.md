---
title: Azure Firewall FQDN-szűrés a hálózati szabályokban
description: A Azure Firewall FQDN-szűrés használata a hálózati szabályokban
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695945"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>FQDN-szűrés használata a hálózati szabályokban

A teljes tartománynév (FQDN) a gazdagép vagy az IP-cím (ek) tartománynevét jelöli. A hálózati szabályokban a Azure Firewall és a tűzfalszabályok DNS-feloldása alapján használhat teljes tartományneveket. Ez a funkció lehetővé teszi a kimenő forgalom szűrését bármely TCP/UDP protokollal (beleértve az NTP, az SSH, az RDP és egyebek). Engedélyeznie kell a DNS-proxyt a teljes tartománynevek használatára a hálózati szabályokban. További információ: [Azure Firewall DNS-beállítások](dns-settings.md).

> [!NOTE]
> A kiépítés alapján a teljes tartománynevek szűrése nem támogatja a helyettesítő karaktereket.

## <a name="how-it-works"></a>Működés

Miután meghatározta, hogy a szervezet melyik DNS-kiszolgálóval rendelkezik (Azure DNS vagy a saját egyéni DNS), a Azure Firewall a kiválasztott DNS-kiszolgáló alapján lefordítja a teljes tartománynevet egy IP-cím (ek) ra. Ez a fordítás az alkalmazás-és a hálózati szabályok feldolgozására is vonatkozik.

Új DNS-feloldás esetén új IP-címek lesznek hozzáadva a tűzfalszabályok számára. A DNS-kiszolgáló által már nem visszaadott régi IP-címek 15 percen belül lejárnak. Azure Firewall a szabályok 15 másodpercenként frissülnek a hálózati szabályok teljes tartománynevének DNS-feloldásakor.

### <a name="differences-in-application-rules-vs-network-rules"></a>Eltérések az alkalmazás szabályaiban és a hálózati szabályok között

- Az alkalmazási szabályokban a HTTP/S és az MSSQL esetében a teljes tartománynév-szűrés az alkalmazás szintű transzparens proxyn és a SNI fejlécen alapul. Így a két, azonos IP-címhez feloldott FQDN között képes érzékelni. Nem ez a helyzet a teljes tartománynév-szűréssel a hálózati szabályokban. 

   Mindig használja az alkalmazás szabályait, ha lehetséges:
     - Ha a protokoll HTTP/S vagy MSSQL, használja az alkalmazási szabályokat az FQDN-szűréshez.
   - A HTTP/S vagy MSSQL protokollon kívül más protokollok esetében az alkalmazás-vagy hálózati szabályok segítségével a teljes tartománynevet szűrheti.

## <a name="next-steps"></a>Következő lépések

[Azure Firewall DNS-beállítások](dns-settings.md)
