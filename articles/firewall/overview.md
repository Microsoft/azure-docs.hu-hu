---
title: Mi az Azure Firewall?
description: Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 04/20/2021
ms.author: victorh
ms.openlocfilehash: 9ed46af7e4c62b2b7213b9e7a3d71c1b4776c806
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835343"
---
# <a name="what-is-azure-firewall"></a>Mi az Azure Firewall?

![ICSA-tanúsítvány](media/overview/icsa-cert-firewall-small.png)

Az Azure Firewall egy felügyelt, felhőalapú hálózatbiztonsági szolgáltatás, amely Azure Virtual Network-erőforrásait védi. Egy teljes mértékben állapotalapú tűzfalszolgáltatás, beépített magas rendelkezésre állással és korlátlan felhőméretezhetőséggel.

![Tűzfal áttekintése](media/overview/firewall-threat.png)

Központilag hozhatja létre, érvényesítheti és naplózhatja az alkalmazás- és hálózatelérési szabályzatokat az előfizetésekre és a virtuális hálózatokra vonatkozóan. Az Azure Firewall statikus nyilvános IP-címet használ a virtuális hálózat erőforrásaihoz, így a külső tűzfalak azonosíthatják a virtuális hálózatból érkező forgalmat.  A szolgáltatás teljesen integrálva van az Azure Monitorral a naplózás és az elemzés érdekében.

További információ a Azure Firewall szolgáltatásokról: Azure Firewall [funkciók.](features.md)

## <a name="azure-firewall-premium-preview"></a>Azure Firewall Premium Előzetes verzió

Azure Firewall Premium Preview egy következő generációs tűzfal, amely a szigorúan bizalmas és szabályozott környezetekhez szükséges képességekkel rendelkezik. Ilyen képesség például a TLS-vizsgálat, az IDPS, az URL-szűrés és a webes kategóriák.

A Prémium Azure Firewall előzetes verziójú funkciókkal kapcsolatos további információkért lásd a [prémium Azure Firewall funkciókat.](premium-features.md)


A Prémium szintű tűzfal előzetes verziója a Azure Portal előzetes verzió Azure Firewall a [Azure Portal.](premium-portal.md)


## <a name="pricing-and-sla"></a>Díjszabás és SLA

További Azure Firewall a díjszabást Azure Firewall [oldalon.](https://azure.microsoft.com/pricing/details/azure-firewall/)

További Azure Firewall SLA-információkért [lásd: Azure Firewall SLA.](https://azure.microsoft.com/support/legal/sla/azure-firewall/)

## <a name="whats-new"></a>Újdonságok

A szolgáltatás újdonságát az Azure-Azure Firewall [azure-frissítésekkel ismerkedheti meg.](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall)


## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall az alábbi ismert hibákkal rendelkezik:

|Probléma  |Description  |Kockázatcsökkentés  |
|---------|---------|---------|
|A nem TCP/UDP-protokollokra (például ICMP) vonatkozó hálózati szűrési szabályok nem működnek az internetre irányuló forgalom esetében|A nem TCP/UDP protokollok hálózati szűrési szabályai nem működnek az SNAT-val a nyilvános IP-címhez. A nem TCP/UDP-protokollok a küllők alhálózatai és a virtuális hálózatok között támogatottak.|Az Azure Firewall a Standard Load Balancert használja, [amely jelenleg nem támogatja a forráshálózati címfordítást az IP-protokollokon](../load-balancer/load-balancer-overview.md). A forgatókönyv egy későbbi kiadásban való támogatásához kínálunk lehetőségeket.|
|A PowerShell és a CLI nem támogatja az ICMP-t|Azure PowerShell és a CLI nem támogatja az ICMP-t érvényes protokollként a hálózati szabályokban.|Az ICMP protokollként továbbra is használható a portálon és a REST API. Dolgozunk azon, hogy hamarosan hozzáadjuk az ICMP-t a PowerShellhez és a parancssori felülethez.|
|Az FQDN-címkék protokoll: port megadását igénylik|Az FQDN-címkékkel rendelkezik alkalmazásszabályokhoz port: protokolldefiníció szükséges.|A port:protokoll értékként használhat **https**-t. Dolgozunk azon, hogy ez a mező FQDN-címkék használata esetén is választható legyen.|
|A tűzfal másik erőforráscsoportba vagy előfizetésbe való áthelyezése nem támogatott|A tűzfalak másik erőforráscsoportba vagy előfizetésbe való áthelyezése nem támogatott.|Ennek a funkciónak a támogatása már az úton van. Ahhoz, hogy egy tűzfalat áthelyezzen másik erőforráscsoportba vagy előfizetésbe, először törölnie kell az aktuális példányt, és újra létre kell hoznia az új erőforráscsoportban vagy előfizetésben.|
|A fenyegetésintelligencia-riasztások maszkolása|A 80/443-as célhelyet a kimenő szűrésre vonatkozó hálózati szabályok elfedik a fenyegetésintelligencia-riasztásokat, ha csak riasztási módra vannak konfigurálva.|Hozzon létre kimenő szűrést a 80/443-as porthoz alkalmazásszabályok használatával. Vagy módosítsa a fenyegetésintelligencia-módot Riasztás **és Megtagadás módra.**|
|Azure Firewall DNAT nem működik magánhálózati IP-címeken|Azure Firewall DNAT támogatása az internetes forgalomra/bejövő forgalomra korlátozódik. A DNAT jelenleg nem működik magánhálózati IP-címeken. Például küllőről küllőre.|Ez egy aktuális korlátozás.|
|Az első nyilvános IP-konfiguráció nem távolítható el|Minden Azure Firewall nyilvános IP-cím egy *IP-konfigurációhoz van rendelve.*  Az első IP-konfiguráció a tűzfal üzembe helyezése során van hozzárendelve, és általában a tűzfal alhálózatának hivatkozását is tartalmazza (kivéve, ha sablontelepítéssel kifejezetten másként van konfigurálva). Ez az IP-konfiguráció nem törölhető, mert az fel kellene osztania a tűzfalat. Akkor is módosíthatja vagy eltávolíthatja az IP-konfigurációhoz társított nyilvános IP-címet, ha a tűzfalon van legalább egy másik használható nyilvános IP-cím.|Ez az elvárt működés.|
|A rendelkezésre állási zónák csak az üzembe helyezés során konfigurálhatóak.|A rendelkezésre állási zónák csak az üzembe helyezés során konfigurálhatóak. A tűzfal üzembe helyezése Availability Zones után nem konfigurálhat tűzfalat.|Ez az elvárt működés.|
|SNAT bejövő kapcsolatokon|A DNAT mellett a tűzfal nyilvános IP-címén (bejövő) keresztüli kapcsolatok a tűzfal egyik magánhálózati IP-címére vannak SNAT-n keresztül. Ez a követelmény jelenleg (aktív/aktív NVA-k esetében is) a szimmetrikus útválasztás biztosítása érdekében.|A HTTP/S eredeti forrásának megőrzése érdekében érdemes [lehet XFF-fejléceket](https://en.wikipedia.org/wiki/X-Forwarded-For) használni. Például használjon olyan szolgáltatást, mint Azure Front Door [vagy](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) a tűzfal előtt. A WAF-et a tűzfalhoz Azure Front Door lánc részeként is hozzáadhatja.
|Az SQL FQDN-szűrés csak proxy módban támogatott (1433-as port)|A Azure SQL Database, Azure Synapse Analytics és a Azure SQL Managed Instance:<br><br>Az SQL FQDN-szűrés csak proxy módban támogatott (1433-as port).<br><br>IaaS-Azure SQL:<br><br>Ha nem szabványos portokat használ, ezeket a portokat az alkalmazásszabályokban adhatja meg.|Átirányítási módú SQL esetén (alapértelmezés szerint, ha az Azure-ból csatlakozik), ehelyett szűrheti a hozzáférést az SQL-szolgáltatáscímkével a hálózati Azure Firewall részeként.
|A 25-ös TCP-port kimenő forgalma nem engedélyezett| A 25-ös TCP-portot használó kimenő SMTP-kapcsolatok le vannak tiltva. A 25-ös portot elsősorban nem hitelesített e-mailek kézbesítésére használják. Ez a virtuális gépek alapértelmezett platformviselkedése. További információ: [Kimenő SMTP-kapcsolati problémák elhárítása az Azure-ban.](../virtual-network/troubleshoot-outbound-smtp-connectivity.md) A virtuális gépekkel ellentétben azonban ez a funkció jelenleg nem engedélyezhető a Azure Firewall. Megjegyzés: a hitelesített SMTP (587-es port) vagy SMTP 25-től különböző porton való engedélyezése érdekében győződjön meg arról, hogy hálózati szabályt konfigurált, és nem alkalmazásszabályt, mivel az SMTP-vizsgálat jelenleg nem támogatott.|Kövesse az ajánlott módszert az e-mailek küldése érdekében, az SMTP hibaelhárítási cikkben dokumentált módon. Vagy zárja ki azt a virtuális gépet, amely kimenő SMTP-hozzáférést szeretne az alapértelmezett tűzfal-útvonalról. Ehelyett konfigurálja a kimenő hozzáférést közvetlenül az internethez.
|SNAT-port kimerítése|Azure Firewall jelenleg 1024 portot támogat nyilvános IP-címenként háttér virtuálisgép-méretezésikészlet-példányonként. Alapértelmezés szerint két VMSS-példány van.|Ez egy SLB-korlátozás, és folyamatosan keressük a korlátok növelésének lehetőségeit. Addig is ajánlott legalább Azure Firewall nyilvános IP-címmel konfigurálni az SNATfogyásnak kimerülésre érzékeny üzemelő példányokat. Ez ötször növeli az elérhető SNAT-portokat. Foglaljon le egy IP-címelőtagból az lefelé irányuló engedélyek egyszerűsítése érdekében.|
|A DNAT nem támogatott, ha a kényszerített bújtatás engedélyezve van|A kényszerített bújtatással üzembe helyezett tűzfalak aszimmetrikus útválasztás miatt nem támogatják az internetről bejövő hozzáférést.|Ez az aszimmetrikus útválasztás miatt van így. A bejövő kapcsolatok visszatérési útvonala a helyszíni tűzfalon keresztül történik, amely még nem látta a kapcsolatot.
|Az FTP-kiszolgáló konfigurációjának függvényében előfordulhat, hogy a kimenő passzív FTP nem működik több nyilvános IP-címmel rendelkező tűzfalak esetén.|A passzív FTP különböző kapcsolatokat létesít a vezérlési és adatcsatornák számára. Amikor egy több nyilvános IP-címmel rendelkező tűzfal kimenő adatokat küld, véletlenszerűen kiválasztja az egyik nyilvános IP-címét a forrás IP-címéhez. Az FTP sikertelen lehet, ha az adat- és vezérlőcsatornák különböző forrás IP-címeket használnak az FTP-kiszolgáló konfigurációjának megfelelően.|Tervben van egy explicit SNAT-konfiguráció. Addig is konfigurálhatja az FTP-kiszolgálót úgy, hogy fogadja a különböző forrás IP-címekről származó adatokat és vezérlő csatornákat (lásd egy példát az [IIS-re).](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity) Ebben az esetben érdemes lehet egyetlen IP-címet használni.|
|Előfordulhat, hogy a bejövő passzív FTP nem működik az FTP-kiszolgáló konfigurációja alapján |A passzív FTP különböző kapcsolatokat létesít a vezérlési és adatcsatornák számára. A hálózati Azure Firewall a tűzfal egyik magánhálózati IP-címére SNAT-ként vannak becslve a szimmetrikus útválasztás biztosítása érdekében. Az FTP sikertelen lehet, ha az adat- és vezérlőcsatornák eltérő forrás IP-címeket használnak az FTP-kiszolgáló konfigurációjának megfelelően.|A rendszer az eredeti forrás IP-cím megőrzését vizsgálja. Addig is konfigurálhatja az FTP-kiszolgálót úgy, hogy fogadja a különböző forrás IP-címekről származó adatokat és vezérlő csatornákat.|
|A NetworkRuleHit metrika hiányzik egy protokolldimenzióból|Az ApplicationRuleEmet metrika lehetővé teszi a szűrésen alapuló protokollt, de ez a képesség hiányzik a megfelelő NetworkRuleEmet metrikában.|A javítás kivizsgálása folyamatban van.|
|A 64000 és 65535 közötti portokkal való NAT-szabályok nem támogatottak|Azure Firewall 1–65535 tartomány bármely portját engedélyezi a hálózati és alkalmazásszabályokban, a NAT-szabályok azonban csak az 1–63999 tartományba esnek portokat.|Ez egy aktuális korlátozás.
|A konfigurációfrissítések átlagosan öt percet is igénybe vehetnek|Egy Azure Firewall konfigurációfrissítés átlagosan három-öt percet is igénybe vehet, és a párhuzamos frissítések nem támogatottak.|A javítás kivizsgálása folyamatban van.|
|Azure Firewall SNI TLS-fejlécek használatával szűri a HTTPS- és MSSQL-forgalmat|Ha a böngésző- vagy kiszolgálószoftver nem támogatja a Kiszolgálónév-jelző (SNI) bővítményt, nem csatlakozhat a Azure Firewall.|Ha a böngésző- vagy kiszolgálószoftver nem támogatja az SNI-t, akkor előfordulhat, hogy alkalmazásszabály helyett hálózati szoftverfrissítéssel tudja vezérelni a kapcsolatot. Lásd [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) SNI-t támogató szoftverekről.|
|Az egyéni DNS nem működik kényszerített bújtatással|Ha a kényszerített bújtatás engedélyezve van, az egyéni DNS nem működik.|A javítás kivizsgálása folyamatban van.|
|A Start/Stop nem működik kényszerített bújtatású módban konfigurált tűzfallal|A Start/Stop nem működik kényszerített bújtatású módban konfigurált Azure-tűzfallal. Ha kényszerített bújtatással Azure Firewall kényszerített bújtatással próbálja meg elindítani a hitelesítést, a következő hibaüzenet jelenik meg:<br><br>*Set-AzFirewall: Az AzureFirewall FW-xx felügyeleti IP-konfigurációt nem lehet hozzáadni egy meglévő tűzfalhoz. Ha kényszerített bújtatás támogatását szeretné használni, akkor a felügyeleti IP-konfigurációval újratelepítést kell használnia. <br> StatusCode: 400 <br> ReasonPhrase: Hibás kérés*|Vizsgálat alatt áll.<br><br>Áthidaló megoldásként törölheti a meglévő tűzfalat, és létrehozhat egy újat ugyanazokkal a paraméterekkel.|
|Nem lehet tűzfal-szabályzatcímkéket hozzáadni a portálon|Azure Firewall szabályzat rendelkezik egy javítástámogatási korlátozással, amely megakadályozza a címke hozzáadását a Azure Portal. A következő hiba keletkezik: Nem sikerült menteni az *erőforrás címkéit.*|A javítás kivizsgálása folyamatban van. A címkék frissítéséhez használhatja Azure PowerShell `Set-AzFirewallPolicy` parancsmagot is.|
|Az IPv6 még nem támogatott|Ha IPv6-címet ad hozzá egy szabályhoz, a tűzfal sikertelen lesz.|Csak IPv4-címeket használjon. Az IPv6-támogatás vizsgálat alatt áll.|
|Több IP-csoport frissítése ütközési hibával meghiúsul.|Ha két vagy több, ugyanabba a tűzfalhoz csatolt IPGroupot frissít, az egyik erőforrás hibás állapotba kerül.|Ez egy ismert probléma/korlátozás. <br><br>Amikor frissít egy IPGroupot, az elindít egy frissítést az összes tűzfalon, amelyekhez az IPGroup csatolva van. Ha egy második IP-csoport frissítését indította el, miközben a tűzfal még Frissítés állapotban *van,* az IPGroup frissítése sikertelen lesz.<br><br>A hiba elkerülése érdekében az azonos tűzfalhoz csatolt IP-csoportokat egyszerre csak egyszer kell frissíteni. Hagyjon elegendő időt a frissítések között ahhoz, hogy a tűzfal ne legyen frissítési *állapotban.*| 


## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: Azure Firewall és tűzfal-szabályzat létrehozása – ARM-sablon](../firewall-manager/quick-firewall-policy.md)
- [Rövid útmutató: Azure Firewall üzembe helyezése Availability Zones – ARM-sablon](deploy-template.md)
- [Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](tutorial-firewall-deploy-portal.md)