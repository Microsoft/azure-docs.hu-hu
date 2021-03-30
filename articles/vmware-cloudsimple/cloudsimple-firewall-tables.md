---
title: Azure VMware-megoldás CloudSimple-tűzfalakkal
description: Ismerje meg a CloudSimple és a tűzfalszabályok, valamint az egyes tűzfalakon létrehozott alapértelmezett szabályokat.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140870"
---
# <a name="firewall-tables-overview"></a>A tűzfal táblái – áttekintés

A tűzfalszabályok a saját Felhőbeli erőforrásokra irányuló és onnan érkező hálózati forgalom szűrésére szolgáló szabályokat sorolja fel. A tűzfalakat egy VLAN-ra/alhálózatra is alkalmazhatja. A szabályok vezérlik a hálózati forgalmat a forrásoldali hálózat vagy az IP-cím, a célként megadott hálózat vagy az IP-cím között.

## <a name="firewall-rules"></a>Tűzfalszabályok

A következő táblázat a tűzfalszabály paramétereit ismerteti.

| Tulajdonság | Részletek |
| ---------| --------|
| **Név** | A tűzfalszabály egyedi azonosítására szolgáló név. |
| **Prioritás** | 100 és 4096 közötti szám, amelynek a legmagasabb prioritása a 100. A szabályok feldolgozása prioritási sorrendben történik. Ha a forgalom egy szabály egyezését tapasztalja, a szabályok feldolgozása leáll. Ennek eredményeképpen az alacsonyabb prioritású szabályok, amelyek ugyanazokkal az attribútumokkal rendelkeznek, mint a magasabb prioritású szabályok, nem kerülnek feldolgozásra.  Ügyeljen az ütköző szabályok elkerülésére. |
| **Állapot követése** | A nyomon követés lehet állapot nélküli (privát felhő, Internet vagy VPN) vagy állapot-nyilvántartó (nyilvános IP).  |
| **Protokoll** | A lehetőségek közé tartoznak a következők: any, TCP vagy UDP. Ha az ICMP-t igényli, használja a bármelyiket. |
| **Irány** | Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik. |
| **Művelet** | A szabályban definiált forgalom típusának engedélyezése vagy letiltása. |
| **Forrás** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (10.0.0.0/24, például) vagy bármely.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre. |
| **Forrásport** | Az a port, amelyből a hálózati forgalom származik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. |
| **Cél** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (10.0.0.0/24, például) vagy bármely.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre.  |
| **Célport** | Az a port, amelyhez a hálózati forgalom áramlik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia.|

### <a name="stateless"></a>Állapot nélküli

Az állapot nélküli szabály csak az egyes csomagokat vizsgálja, és a szabály alapján szűri azokat.  
A forgalom fordított irányú továbbításához további szabályokra lehet szükség.  Állapot nélküli szabályok használata a következő pontok közötti forgalomhoz:

* Privát felhők alhálózatai
* Helyszíni alhálózat és egy privát felhőalapú alhálózat
* Internetes forgalom a privát felhőkből

### <a name="stateful"></a>Állapotalapú

 Egy állapot-nyilvántartó szabály ismeri az áthaladó kapcsolatokat. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva.  A nyilvános IP-címekre vonatkozó szabálytípus használatával szűrheti az internetről érkező forgalmat.

### <a name="default-rules"></a>Alapértelmezett szabályok

A következő alapértelmezett szabályok jönnek létre minden tűzfal-táblában.

|Prioritás|Name|Állapot követése|Irány|Forgalom típusa|Protokoll|Forrás|Forrásport|Cél|Célport|Művelet|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|a-all-to-Internet engedélyezése|Állapotalapú|Kimenő|Nyilvános IP-cím vagy internetes forgalom|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65001|internetről történő megtagadás|Állapotalapú|Bejövő|Nyilvános IP-cím vagy internetes forgalom|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Megtagadás|
|65002|összes-az-intranetes engedélyezése|Állapot nélküli|Kimenő|Privát felhő belső vagy VPN-forgalma|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65003|intranetes engedélyezése|Állapot nélküli|Bejövő|Privát felhő belső vagy VPN-forgalma|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|

## <a name="next-steps"></a>Következő lépések

* [Tűzfaltáblák és -szabályok beállítása](firewall.md)
