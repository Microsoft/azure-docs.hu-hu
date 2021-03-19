---
title: Azure Firewall kezelő szűrése hálózati szabályokban (előzetes verzió)
description: FQDN-szűrés használata a hálózati szabályokban
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460150"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>FQDN-szűrés a hálózati szabályokban (előzetes verzió)

> [!IMPORTANT]
> A hálózati szabályok FQDN-szűrése jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A teljes tartománynév (FQDN) a gazdagép vagy az IP-cím (ek) tartománynevét jelöli. A hálózati szabályokban a Azure Firewall és a tűzfalszabályok DNS-feloldása alapján használhat teljes tartományneveket. Ez a funkció lehetővé teszi a kimenő forgalom szűrését bármely TCP/UDP protokollal (beleértve az NTP, az SSH, az RDP és egyebek). Engedélyeznie kell a DNS-proxyt a teljes tartománynevek használatára a hálózati szabályokban. További információ: [Azure Firewall házirend DNS-beállításai (előzetes verzió)](dns-settings.md).

## <a name="how-it-works"></a>Működés

Miután meghatározta, hogy a szervezet melyik DNS-kiszolgálóval rendelkezik (Azure DNS vagy a saját egyéni DNS), a Azure Firewall a kiválasztott DNS-kiszolgáló alapján lefordítja a teljes tartománynevet egy IP-cím (ek) ra. Ez a fordítás az alkalmazás-és a hálózati szabályok feldolgozására is vonatkozik.

Mi a különbség a tartománynevek alkalmazási szabályokban való használata között a hálózati szabályokhoz képest? 

- Az alkalmazási szabályokban a HTTP/S és az MSSQL esetében a teljes tartománynév-szűrés az alkalmazás szintű transzparens proxyn és a SNI fejlécen alapul. Így a két, azonos IP-címhez feloldott FQDN között képes érzékelni. Nem ez a helyzet a teljes tartománynév-szűréssel a hálózati szabályokban. Mindig használja az alkalmazás szabályait, ha lehetséges.
- Az alkalmazási szabályokban HTTP/S és MSSQL protokollt használhat a kiválasztott protokollokként. A hálózati szabályok területen bármilyen TCP/UDP protokollt használhat a célként megadott teljes tartománynévvel.

## <a name="next-steps"></a>Következő lépések

[Azure Firewall DNS-beállítások](dns-settings.md)
