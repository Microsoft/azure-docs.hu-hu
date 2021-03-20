---
title: Bevezetés az Azure Network Watcher-kapcsolódási hibába | Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher-kapcsolatok hibaelhárítási képességéről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97653996"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Az Azure-Network Watcher kapcsolódási hibáinak bemutatása

A Network Watcher kapcsolódási hibáinak elhárítása lehetővé teszi a közvetlen TCP-kapcsolatok ellenőrzését egy virtuális gépről egy virtuális gépre (VM), a teljes tartománynévre (FQDN), az URI-ra vagy az IPv4-címekre. A hálózati forgatókönyvek összetettek, a hálózati biztonsági csoportokkal, tűzfalakkal, felhasználó által megadott útvonalakkal és az Azure által biztosított erőforrásokkal valósíthatók meg. Az összetett konfigurációk hibaelhárítást tesznek a kapcsolódási problémákkal kapcsolatban. Network Watcher segít csökkenteni a kapcsolódási problémák megtalálásának és észlelésének időtartamát. A visszaadott eredmények révén betekintést nyerhet, hogy a kapcsolódási problémát egy platform vagy egy felhasználói konfigurációs probléma okozza-e. A kapcsolat ellenőrizhető a [PowerShell](network-watcher-connectivity-powershell.md), az [Azure CLI](network-watcher-connectivity-cli.md)és a [REST API](network-watcher-connectivity-rest.md)használatával.

> [!IMPORTANT]
> A kapcsolati hibákhoz az szükséges, hogy a rendszer a virtuálisgép- `AzureNetworkWatcherExtension` bővítményt telepítse. A bővítmény Windows rendszerű virtuális gépen való telepítéséhez látogasson el az [azure Network Watcher Agent virtuálisgép-bővítmény a Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) rendszerhez és a Linux rendszerű virtuális gépekhez látogasson el az [Azure Network Watcher Agent virtuálisgép-bővítménye Linuxra](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A cél végponton nem szükséges a kiterjesztés.

## <a name="response"></a>Reagálás

A következő táblázat azokat a tulajdonságokat mutatja be, amelyeket a rendszer a kapcsolódási hibák futásának befejeződése után adott vissza.

|Tulajdonság  |Leírás  |
|---------|---------|
|ConnectionStatus     | A kapcsolat-ellenőrzési állapota. A lehetséges eredmények **elérhetők** és nem **érhetők el**.        |
|AvgLatencyInMs     | Az átlagos késés a kapcsolat ellenőrzése során ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzési állapot elérhető)        |
|MinLatencyInMs     | A kapcsolat ellenőrzésének minimális késése ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzési állapot elérhető)        |
|MaxLatencyInMs     | A kapcsolat ellenőrzése során a maximális késés ezredmásodpercben. (Csak akkor jelenik meg, ha az ellenőrzési állapot elérhető)        |
|ProbesSent     | Az ellenőrzés során eljuttatott mintavételek száma. A maximális érték 100.        |
|ProbesFailed     | Az ellenőrzés során meghiúsult mintavételek száma. A maximális érték 100.        |
|Ugrások     | Ugrás a forrás és a cél között a hop útvonalon.        |
|Ugrások []. Típusa     | Az erőforrás típusa. A lehetséges értékek a következők: **forrás**, **VirtualAppliance**, **VnetLocal** és **Internet**.        |
|Ugrások []. ID | Az Ugrás egyedi azonosítója.|
|Ugrások []. Cím | Az ugrás IP-címe.|
|Ugrások []. ResourceId | ResourceID, ha az Ugrás egy Azure-erőforrás. Ha ez egy internetes erőforrás, a ResourceID az **Internet**. |
|Ugrások []. NextHopIds | A következő ugrás egyedi azonosítója.|
|Ugrások []. Kérdések | A hop-ellenőrzés során észlelt problémák gyűjteménye. Ha nem volt probléma, az érték üres.|
|Ugrások []. Problémák []. Származási | Az aktuális ugrásban, ahol a probléma történt. Lehetséges értékek:<br/> **Bejövő** – probléma az előző ugrásról az aktuális ugrásra mutató hivatkozáson<br/>**Kimenő** – probléma az aktuális ugrásról a következő ugrásra mutató hivatkozáson<br/>**Helyi** – a probléma az aktuális ugráson van.|
|Ugrások []. Problémák []. Súlyosság | A probléma súlyossága észlelhető. A lehetséges értékek a **hiba** és a **Figyelmeztetés**. |
|Ugrások []. Problémák []. Típusa |A talált probléma típusa. Lehetséges értékek: <br/>**CPU**<br/>**Memória**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Ugrások []. Problémák []. Összefüggésben |A hibával kapcsolatos részletek.|
|Ugrások []. Problémák []. Kontextus []. Key |A kulcs érték pár visszaadott kulcsa.|
|Ugrások []. Problémák []. Context []. Value |A visszaadott kulcs érték párok értéke.|

Az alábbi példa egy hop-beli problémát mutat be.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Hibák típusai

A kapcsolatok hibaelhárítása a kapcsolatban fellépő hibák típusait adja vissza. A következő táblázat felsorolja az aktuálisan visszaadott típusú hibakódokat.

|Típus  |Leírás  |
|---------|---------|
|CPU     | Magas CPU-kihasználtság.       |
|Memória     | Nagy memória kihasználtsága.       |
|GuestFirewall     | A virtuális gép tűzfalának konfigurálása miatt a rendszer blokkolja a forgalmat.        |
|DNSResolution     | A célcím DNS-feloldása nem sikerült.        |
|NetworkSecurityRule    | Egy NSG szabály blokkolta a forgalmat (a rendszer visszaadja a szabályt)        |
|UserDefinedRoute|A rendszer egy felhasználó által definiált vagy rendszerútvonal miatt eldobott forgalmat. |

### <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan lehet a [Azure Portal](network-watcher-connectivity-portal.md), a [PowerShell](network-watcher-connectivity-powershell.md), az [Azure CLI](network-watcher-connectivity-cli.md)vagy a [REST API](network-watcher-connectivity-rest.md)használatával a kapcsolatokat elhárítani.