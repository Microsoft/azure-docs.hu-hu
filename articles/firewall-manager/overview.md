---
title: Mi az az Azure Firewall Manager?
description: A Azure Firewall Manager funkcióinak megismerése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/16/2021
ms.author: victorh
ms.openlocfilehash: 506799f94676be007cf94320e3958bd305ce85f0
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573455"
---
# <a name="what-is-azure-firewall-manager"></a>Mi az az Azure Firewall Manager?

A Azure Firewall Manager egy biztonsági felügyeleti szolgáltatás, amely központi biztonsági házirendet és útválasztási felügyeletet biztosít a felhőalapú biztonsági körzetek számára. 

A Firewall Manager két hálózati architektúra-típushoz biztosít biztonsági felügyeletet:

- **Biztonságos virtuális központ**

   Az [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) egy Microsoft által felügyelt erőforrás, amellyel könnyedén hozhat létre sugaras és küllős architektúrákat. Ha a biztonsági és útválasztási házirendek egy ilyen hubhoz vannak társítva, akkor azt a rendszer *[biztonságos virtuális központként](secured-virtual-hub.md)* említi. 
- **Hub virtuális hálózata**

   Ez egy szabványos Azure-beli virtuális hálózat, amelyet saját maga hozhat létre és kezelhet. Ha a biztonsági szabályzatok egy ilyen hubhoz vannak társítva, akkor azt *hub virtuális hálózatnak* nevezzük. Jelenleg csak Azure Firewall házirend támogatott. A munkaterhelési kiszolgálókat és szolgáltatásokat tartalmazó társ-küllős virtuális hálózatokat is használhatja. A tűzfalakat olyan önálló virtuális hálózatokban is kezelheti, amelyek nem rendelkeznek a küllős szolgáltatással.

A *biztonságos virtuális központ* és a *hub virtuális hálózati* architektúráinak részletes összehasonlítását lásd: [Mik a Azure Firewall Manager architektúra lehetőségei?](vhubs-and-vnets.md).

![tűzfal – kezelő](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Azure Firewall Manager funkciói

A Azure Firewall Manager a következő funkciókat kínálja:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Központi Azure Firewall üzembe helyezése és konfigurálása

Központilag telepíthet és konfigurálhat több Azure Firewall példányt, amely különböző Azure-régiókat és-előfizetéseket foglal magába. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchikus házirendek (globális és helyi)

A Azure Firewall Manager segítségével központilag kezelheti Azure Firewall szabályzatokat több biztonságos virtuális hubhoz. A központi informatikai csapatok globális tűzfal-házirendeket hozhatnak létre a szervezeti szintű tűzfalszabályok a csapatok közötti kikényszerített szabályozásához. A helyileg létrehozott tűzfalszabályok lehetővé teszik a DevOps önkiszolgáló modell használatát a jobb mozgékonyság érdekében.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrált a fokozott biztonságú külső biztonsági szolgáltatásokkal

A Azure Firewallon kívül a külső gyártótól származó biztonsági szolgáltatásokat (SECaaS) is integrálhatja, így további hálózati védelmet biztosíthat a VNet és az ág internetes kapcsolatai számára.

Ez a szolgáltatás csak biztonságos virtuális központú üzemelő példányokkal érhető el.

- VNet az internetre (V2I) forgalom szűrése

   - A kimenő virtuális hálózati forgalom szűrése az Ön által előnyben részesített külső biztonsági szolgáltatóval.
   - Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet (B2I) forgalom szűrése

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó szűrést a fiókirodához az internetes forgatókönyvekhez.

További információ a biztonsági partner-szolgáltatókról: [Mik azok a Azure Firewall Manager biztonsági partner-szolgáltatók?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Központosított útválasztás kezelése

A biztonságos hubhoz irányíthatja át a forgalmat a szűréshez és a naplózáshoz anélkül, hogy manuálisan be kellene állítania a felhasználó által megadott útvonalakat (UDR) a küllős virtuális hálózatokon. 

Ez a szolgáltatás csak biztonságos virtuális központú üzemelő példányokkal érhető el.

Harmadik féltől származó szolgáltatókat is használhat a fiókirodák internetes (B2I) adatforgalmának szűrésére, egymás mellett Azure Firewall a ágat a VNet (B2V), a VNet VNet (V2V) és az internetre (VNET). Külső szolgáltatókat is használhat a V2I, ha a B2V és a V2V esetében nem szükséges a Azure Firewall. 

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Azure Firewall házirendek a régiók között is használhatók. Létrehozhat például egy szabályzatot az USA nyugati régiójában, és használhatja azt az USA keleti régiójában. 

## <a name="known-issues"></a>Ismert problémák

Azure Firewall Manager a következő ismert problémákkal rendelkezik:

|Probléma  |Description  |Kockázatcsökkentés  |
|---------|---------|---------|
|Forgalom felosztása|A Microsoft 365 és az Azure nyilvános Pásti forgalom felosztása jelenleg nem támogatott. Így például a V2I vagy a B2I harmadik féltől származó szolgáltatójának kiválasztásával az összes Azure nyilvános Pásti és Microsoft 365 forgalmat a partner szolgáltatáson keresztül küldi el.|A forgalom felosztásának kivizsgálása a központban.
|Egy biztonságos virtuális központ régiónként|Régiónként legfeljebb egy biztonságos virtuális központ lehet.|Több virtuális WAN-t hozhat létre egy régióban.|
|Az alapházirendeknek ugyanabban a régióban kell lenniük, mint a helyi házirendnek.|Hozza létre az összes helyi házirendet az alapházirenddel azonos régióban. Továbbra is alkalmazhat egy olyan házirendet, amely egy másik régióban található biztonságos központ egyik régiójában lett létrehozva.|Kivizsgálás|
|A csomópontok közötti forgalom szűrése a biztonságos virtuális központú üzemelő példányokban|A biztonságos virtuális központ a biztonságos virtuális hubhoz való kommunikációs szűrés még nem támogatott. A hub és a hub közötti kommunikáció azonban továbbra is működik, ha az Azure Firewallon keresztüli privát forgalom szűrése nem engedélyezett.|Kivizsgálás|
|A virtuális hubhoz eltérő régióban szólal meg|A különböző régiókban, mint a virtuális központ nem támogatott.|Kivizsgálás<br><br>Hozzon létre egy alhálózatot régiónként és egyenrangú virtuális hálózatok ugyanabban a régióban, ahol a hub található.|
|Ág és ág közötti forgalom a privát forgalom szűrésével engedélyezve|A fiókirodák közötti forgalom nem támogatott, ha a privát forgalom szűrése engedélyezve van. |Vizsgáló.<br><br>Nem biztonságos a privát forgalom, ha a fiókirodák közötti kapcsolat létfontosságú.|
|Az azonos virtuális WAN-t megosztó biztonságos virtuális huboknak ugyanabban az erőforráscsoporthoz kell tartoznia.|Ez a viselkedés a virtuális WAN-Hubokkal van összhangban ma.|Hozzon létre több virtuális WAN-t, amely lehetővé teszi, hogy a biztonságos virtuális hubok különböző erőforráscsoportok jöjjenek létre.|
|A tömeges IP-cím hozzáadása nem sikerül|Ha több nyilvános IP-címet ad hozzá, a biztonságos központ tűzfala sikertelen állapotba kerül.|Kisebb nyilvános IP-cím növekmények hozzáadása. Például adjon hozzá 10-et egyszerre.|
|A DDoS Protection standard nem támogatott a biztonságos virtuális hubok esetében|A DDoS Protection standard nincs integrálva a vWANs.|Kivizsgálás|
|A tevékenységek naplói nem teljes mértékben támogatottak|A tűzfalszabályok jelenleg nem támogatják a tevékenységek naplóit.|Kivizsgálás|
|Bizonyos tűzfalbeállítások nem települnek át, ha a tűzfalat a tűzfal-házirend használatára telepíti át.|A Availability Zones és a SNAT magánhálózati címeket a rendszer nem telepíti át Azure Firewall házirendre való Migrálás során.|Kivizsgálás| 

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure Firewall Manager üzembe helyezésének áttekintését](deployment-overview.md)
- A [biztonságos virtuális hubok](secured-virtual-hub.md)megismerése.
