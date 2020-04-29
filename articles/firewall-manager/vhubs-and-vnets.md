---
title: Mik a Azure Firewall Manager architektúrájának lehetőségei?
description: A hub Virtual Network vagy a biztonságos virtuális központ architektúráinak összehasonlítása a Azure Firewall Manager használatával.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444575"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Mik a Azure Firewall Manager architektúrájának lehetőségei?

A Azure Firewall Manager kétféle hálózati architektúra esetén biztosít biztonsági felügyeletet:

- **biztonságos virtuális központ**

   Az [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) egy Microsoft által felügyelt erőforrás, amellyel könnyedén hozhat létre sugaras és küllős architektúrákat. Ha a biztonsági és útválasztási házirendek egy ilyen hubhoz vannak társítva, akkor azt a rendszer *[biztonságos virtuális központként](secured-virtual-hub.md)* említi. 
- **hub virtuális hálózata**

   Ez egy szabványos Azure-beli virtuális hálózat, amelyet saját maga hozhat létre és kezelhet. Ha a biztonsági szabályzatok egy ilyen hubhoz vannak társítva, akkor azt *hub virtuális hálózatnak*nevezzük. Jelenleg csak Azure Firewall házirend támogatott. A munkaterhelési kiszolgálókat és szolgáltatásokat tartalmazó társ-küllős virtuális hálózatokat is használhatja. Az önálló virtuális hálózatokban olyan tűzfalak is kezelhetők, amelyek nem kapcsolódnak egyetlen küllőhöz sem.

## <a name="comparison"></a>Összehasonlítás

A következő táblázat összehasonlítja ezt a két architektúrát, és segíthet eldönteni, hogy melyik a legmegfelelőbb a szervezet biztonsági követelményeinek:


|  |**Hub virtuális hálózata**|**Biztonságos virtuális központ**  |
|---------|---------|---------|
|**Alapul szolgáló erőforrás**     |Virtuális hálózat|Virtuális WAN-központ|
|**A hub & küllő**     |Virtuális hálózati társat használ|Automatikus hub virtuális hálózati kapcsolatok használatával|
|**Helyszíni kapcsolat**     |Akár 10 GB/s S2S-kapcsolatot VPN Gateway; ExpressRoute|Több skálázható VPN Gateway 20 GB/s és 1000 S2S-kapcsolattal; Expressz útvonal|
|**Automatikus ág-kapcsolat a SDWAN használatával**      |Nem támogatott|Támogatott|
|**Hubok régiónként**     |Régiónként több virtuális hálózat|Régiónként egyetlen virtuális központ. Több hub is lehetséges több virtuális WAN-vel|
|**Azure Firewall – több nyilvános IP-cím**      |Ügyfél által megadott|Automatikusan generált. A GA elérhetővé tételéhez.|
|**Azure Firewall Availability Zones**     |Támogatott|Előzetes verzióban nem érhető el. A GA elérhetővé tétele|
|**Fejlett Internet-biztonság külső felektől származó biztonsági szolgáltatásként partnerként**     |Ügyfél által létesített és felügyelt VPN-kapcsolat a választott partneri szolgáltatással|Automatizált megbízható biztonsági partneri folyamattal és partner-felügyeleti felülettel|
|**Központosított irányítás a forgalom irányításához a központba**     |Ügyfél által felügyelt felhasználó által megadott útvonal|A BGP használatával támogatott|
|**Web Application Firewall az Application Gatewayen** |Virtual Network támogatott|Jelenleg támogatott a küllős hálózaton|
|**Hálózati virtuális berendezés**|Virtual Network támogatott|Jelenleg támogatott a küllős hálózaton|

## <a name="next-steps"></a>További lépések

- Tekintse át a [Azure Firewall Manager előzetes telepítésének áttekintését](deployment-overview.md)
- A [biztonságos virtuális hubok](secured-virtual-hub.md)megismerése.