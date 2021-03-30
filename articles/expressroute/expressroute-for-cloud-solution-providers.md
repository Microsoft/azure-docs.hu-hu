---
title: ExpressRoute felhőalapú megoldás-szolgáltatók számára – Azure | Microsoft Docs
description: Ez a cikk olyan felhőalapú megoldás-szolgáltatókról nyújt tájékoztatást, amelyek az Azure-szolgáltatásokat és a ExpressRoute az ajánlatokban foglalják magukban.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 09fee610ccc15874481ecfd4693e4b89379caa7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330036"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>ExpressRoute felhőszolgáltatók (CSP) számára
A Microsoft hatalmas léptékű szolgáltatásokat kínál a hagyományos viszonteladók és forgalmazók (CSP) számára, hogy gyorsan láthassák el ügyfeleiket új szolgáltatásokat és megoldásokat anélkül, hogy be kellene fektetniük az új szolgáltatások fejlesztésébe. Ahhoz, hogy lehetővé tegye a felhőszolgáltatók (CSP) számára az új szolgáltatások közvetlen felügyeletét, a Microsoft olyan programokat és API-kat biztosít, amelyek segítségével a CSP-k ügyfeleik nevében felügyelhetik a Microsoft Azure-erőforrásokat. Ezeknek az erőforrásoknak az egyike az ExpressRoute. Az ExpressRoute lehetővé teszi, hogy a CSP meglévő ügyfélerőforrásait csatlakoztassa az Azure-szolgáltatásokhoz. A ExpressRoute egy nagy sebességű privát kommunikációs hivatkozás az Azure szolgáltatásaihoz. 

A ExpressRoute a magas rendelkezésre állású áramkörökből áll, amelyek egyetlen ügyfél előfizetéséhez vannak csatolva, és nem oszthatók meg több ügyfél között. A két kapcsolatcsoport végét külön útválasztókra kell csatlakoztatni a magas rendelkezésre állás biztosítása érdekében.

> [!NOTE]
> Az egyes ExpressRoute-körökben a sávszélesség és a kapcsolatok száma korlátozva van. Ha egy ügyfél igénye meghaladja ezeket a korlátokat, több ExpressRoute-áramkörre van szükségük a hibrid hálózati megvalósításhoz.
> 
> 

A Microsoft Azure egyre nagyobb számban kínál szolgáltatásokat, amelyeket Ön biztosíthat az ügyfeleinek. A ExpressRoute segít Önnek és ügyfeleinek kihasználni ezeket a szolgáltatásokat azáltal, hogy nagy sebességű, kis késésű hozzáférést biztosítanak a Microsoft Azure-környezethez.

## <a name="microsoft-azure-management"></a>Microsoft Azure-kezelés
A Microsoft az API-k segítségével felügyeli az Azure Customer-előfizetéseket, mivel lehetővé teszi a programozott integrációt a saját szolgáltatás-felügyeleti rendszereivel. A támogatott kezelési képességek [itt](/previous-versions/windows/mt844538(v=win.10)) találhatók.

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure erőforrás-kezelés
Az ügyfelével kötött szerződése határozza meg az előfizetés kezelésének módját. A CSP közvetlenül kezelheti az erőforrások létrehozását és karbantartását, vagy az ügyfél megtarthatja a Microsoft Azure-előfizetés feletti felügyeletet, és maga hozhat létre igény szerinti Azure-erőforrásokat. Ha az ügyfél felügyeli az erőforrások létrehozását a Microsoft Azure-előfizetésében, a következő két modell egyikét fogja használni: "*Kapcsolódás*" modell vagy "*Direct-to*" modell. A modellek leírását a következő szakaszok tartalmazzák.  

### <a name="connect-through-model"></a>Szolgáltatón keresztüli csatlakozás modell
![A "összekapcsolás" modellt bemutató diagram.](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

A szolgáltatón keresztüli csatlakozás modellben a CSP közvetlen kapcsolatot hoz létre az Ön adatközpontja és az ügyfél Azure-előfizetése között. A közvetlen kapcsolat ExpressRoute használatával jön létre, és hálózatát összekapcsolja az Azure-ral. Az ügyfél ezután az Ön hálózatához csatlakozik. Ebben a forgatókönyvben az ügyfélnek a CSP hálózatán keresztül kell kapcsolódnia az Azure-szolgáltatásokhoz. 

Ha az ügyfele más Azure-előfizetésekkel is rendelkezik, amelyeket nem az Ön kezel, akkor a nyilvános interneten vagy saját privát kapcsolaton keresztül csatlakoznak a nem CSP-előfizetés keretében létesített szolgáltatásokhoz. 

Az Azure-szolgáltatásokat kezelő CSP esetében feltételezhető, hogy a CSP-nek korábban létrehozott ügyfél-identitási tárolója van, amelyet a rendszer a CSP-előfizetésük felügyeletére Azure Active Directory replikálhat a (z) (AOBO) használatával. A forgatókönyvhöz tartozó fő illesztőprogramok közé tartozik például, hogy egy adott partner vagy szolgáltató kapcsolatban álljon az ügyféllel, az ügyfél jelenleg szolgáltatói szolgáltatásokat használ, vagy ha a partnernek a szolgáltató által üzemeltetett és az Azure által üzemeltetett megoldások kombinációját kívánja biztosítani, hogy rugalmasságot és megoldást nyújtson a CSP által nem teljesíthető felhasználói kihívásokra. Ez a modell az alábbi **ábrán** látható.

![A "összekapcsolás" modell részletes forgatókönyvét bemutató diagram.](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Szolgáltatóhoz való csatlakozás
![A "kapcsolódás a következőhöz" modellt bemutató diagram.](./media/expressroute-for-cloud-solution-providers/connect-to.png)

A szolgáltatóhoz való csatlakozás modellben a szolgáltató közvetlen kapcsolatot hoz létre az ügyfél adatközpontja és a CSP által létrehozott Azure-előfizetés között az ExpressRoute használatával az ügyfél hálózatán (ügyfélhálózat) keresztül.

> [!NOTE]
> Az ExpressRoute-hoz az ügyfélnek létre kell hoznia és fenn kell tartania egy ExpressRoute-kapcsolatcsoportot.  
> 
> 

Ez a csatlakozási forgatókönyv megköveteli, hogy az ügyfél közvetlenül kapcsolódjon az ügyfél-hálózaton keresztül a CSP által felügyelt Azure-előfizetés eléréséhez, közvetlen hálózati kapcsolaton keresztül, amelyet teljes egészében, vagy részben az ügyfél kezel. Ezen ügyfelek esetében feltételezhető, hogy a szolgáltató jelenleg nem rendelkezik ügyfél-identitási tárolóval, és a szolgáltató segítséget nyújt az ügyfélnek az aktuális azonosítási tár replikálásában Azure Active Directory az előfizetésük kezeléséhez az AOBO-en keresztül. A forgatókönyv fő esetei, ha egy adott partner vagy szolgáltató már jól működő kapcsolattal rendelkezik az ügyféllel, az ügyfél jelenleg is használja a szolgáltató által nyújtott szolgáltatásokat, vagy ha a partner kizárólag az Azure-on futtatott megoldásokon alapuló olyan szolgáltatásokat kíván nyújtani, amelyekhez nincs szükség meglévő szolgáltatói adatközpontra vagy infrastruktúrára.

![A "kapcsolódás a modellhez" nevű részletes forgatókönyvet bemutató diagram.](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

A két lehetőség közül választhat az ügyfél igényeinek megfelelően, és a jelenlegi igényeknek kell megadnia az Azure-szolgáltatásokat. A modellekkel és a kapcsolódó szerepköralapú hozzáférésvezérlés-, hálózat- és identitástervezési mintákkal kapcsolatos részletek az alábbi hivatkozásokon tekinthetők meg:

* **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)** – az Azure RBAC a Azure Active Directoryon alapul.  Az Azure RBAC kapcsolatos további információkért lásd [itt](../role-based-access-control/role-assignments-portal.md).
* **Hálózat** – A különféle hálózati témaköröket fedi le a Microsoft Azure-ban.
* **Azure Active Directory (Azure ad)** – az Azure ad lehetővé teszi a Microsoft Azure és a külső gyártóktól származó SaaS-alkalmazások Identitáskezelés kezelését. További információ az Azure AD-ről: [itt](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Hálózati sebességek
A ExpressRoute a 50 MB/s és 10 GB/s közötti hálózati sebességet támogatja. Az ügyfelek így az egyedi környezetük által igényelt hálózati sávszélességet vásárolhatják meg.

> [!NOTE]
> A hálózati sávszélesség igény szerint növelhető a kommunikáció megszakadása nélkül, azonban a csökkentéséhez le kell bontani a kapcsolatcsoportot, és újra kell építeni az alacsonyabb hálózati sebességen.  
> 
> 

Az ExpressRoute több vNet csatlakoztatását is támogatja egyetlen ExpressRoute-kapcsolatcsoporthoz a nagyobb sebességű kapcsolatok jobb kihasználása érdekében. Egyetlen ExpressRoute-kapcsolatcsoport egy adott ügyfél több Azure-előfizetése között is megosztható.

## <a name="configuring-expressroute"></a>Az ExpressRoute konfigurálása
Az ExpressRoute három típusú forgalom ([útválasztási tartomány](#expressroute-routing-domains)) támogatására konfigurálható egyetlen ExpressRoute-kapcsolatcsoportban. Ezt a forgalmat a rendszer elkülöníti a privát és a Microsoft-partnerek között, és a nyilvános (elavult). Egyetlen ExpressRoute-kapcsolatcsoportban egy vagy több forgalomtípust is továbbíthat, vagy használhat több ExpressRoute-kapcsolatcsoportot is, az ExpressRoute-kapcsolatcsoport méretétől és az ügyfél által igényelt elkülönítéstől függően. Előfordulhat, hogy az ügyfél biztonsági irányelvei, tiltják hogy a nyilvános forgalom és a privát forgalom ugyanazon a körön folyjanak.

### <a name="connect-through-model"></a>Szolgáltatón keresztüli csatlakozás modell
A kapcsolaton keresztüli konfigurációban az összes hálózatkezelési műveletért felelős az ügyfél adatközpont-erőforrásainak az Azure-ban üzemeltetett előfizetésekhez való összekapcsolásához. Az Azure-képességeket használni kívánó ügyfeleknek saját ExpressRoute-kapcsolatra van szükségük, amelyet Ön kezel. Ugyanazokat a módszereket fogja használni, amelyeket az ügyfél az ExpressRoute áramkör beszerzéséhez használ. Ugyanezeket a lépéseket követve a [ExpressRoute-munkafolyamatok](expressroute-workflows.md) az áramköri kiépítés és az áramköri állapotok című cikkben is szerepelnek. Ezután konfigurálja a Border Gateway Protocol (BGP) útvonalakat a helyszíni hálózat és az Azure vNet közötti forgalom szabályozásához.

### <a name="connect-to-model"></a>Szolgáltatóhoz való csatlakozás
A kapcsolódási konfigurációban az ügyfél már rendelkezik egy meglévő Azure-kapcsolattal, vagy kapcsolatot kezdeményez az internetszolgáltatóval, amely a saját adatközpontja helyett közvetlenül az Azure-ba kapcsolja össze a ExpressRoute. A kiépítési folyamat megkezdéséhez az ügyfélnek ugyanúgy a Szolgáltatón keresztüli csatlakozás modellnél fentebb ismertetett lépéseket kell végrehajtania. Az áramkör létrehozása után az ügyfélnek konfigurálnia kell a helyszíni útválasztókat, hogy hozzáférjenek a hálózathoz és az Azure-virtuális hálózatok.

Támogatást nyújthat a kapcsolat beállításában és az útvonalak konfigurálásában, hogy az adatközpont(ok)ban található erőforrások kommunikálhassanak az ügyfél erőforrásaival az Ön adatközpontjában, vagy az Azure-ban futtatott erőforrásokkal.

## <a name="expressroute-routing-domains"></a>ExpressRoute útválasztási tartományok
A ExpressRoute két útválasztási tartományt kínál az új áramkörökhöz: privát és Microsoft-partnerek. Az útválasztási tartományok mindegyike azonos útválasztókkal van konfigurálva az aktív-aktív konfigurációban a magas rendelkezésre állás érdekében. Az ExpressRoute útválasztási tartományokkal kapcsolatos további részleteket lásd [itt](expressroute-circuit-peerings.md).

Az egyéni útvonalszűrőket meghatározhatja úgy is, hogy kizárólag a kívánt vagy szükséges útvonal(ak)at engedélyezzék. A módosítások végrehajtásával kapcsolatos további információkért tekintse meg a [Create and modify routing for an ExpressRoute circuit using PowerShell](expressroute-howto-routing-classic.md) (ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása a PowerShell használatával) című cikket, amely az útválasztási szűrőkkel kapcsolatban is biztosít részletes információkat.

> [!NOTE]
> A Microsoft-társak esetében a kapcsolatnak azonban az ügyfél vagy a CSP által birtokolt nyilvános IP-címnek kell lennie, és meg kell felelnie az összes meghatározott szabálynak. További információkért lásd az [ExpressRoute-előfeltételek](expressroute-prerequisites.md) lapot.  
> 
> 

## <a name="routing"></a>Útválasztás
Az ExpressRoute az Azure Virtual Network Gatewayen keresztül csatlakozik az Azure-hálózatokhoz. A hálózati átjárók biztosítják az útvonalválasztást az Azure virtuális hálózatok számára.

Az Azure Virtual Networks létrehozásával egy alapértelmezett útválasztási tábla is létrejön a vNet számára a vNet alhálózataira irányuló, illetve onnan érkező forgalom irányításához. Ha az alapértelmezett útválasztási táblázat nem elegendő a megoldáshoz, a rendszer egyéni útvonalakat hozhat létre a kimenő forgalom egyéni készülékekre való irányításához, illetve az útvonalak adott alhálózatokra vagy külső hálózatokra való letiltásához.

### <a name="default-routing"></a>Alapértelmezett útválasztás
Az alapértelmezett útválasztási tábla az alábbi útvonalakat tartalmazza:

* Alhálózaton belüli útválasztás
* Alhálózatok közti útvonalak a virtuális hálózaton belül
* Az internet felé
* Virtuális hálózatok közti útvonalak a VPN Gateway használatával
* Virtuális hálózat és helyszíni hálózat közötti útvonal VPN- vagy ExpressRoute-átjáró használatával

![Az alapértelmezett útválasztási beállításokat megjelenítő diagram.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Felhasználó által meghatározott útválasztás (UDR)
A felhasználó által megadott útvonalak segítségével felügyelhető a hozzárendelt alhálózatról a többi alhálózatra irányuló kimenő forgalom a virtuális hálózatban vagy a többi előre definiált átjáró (ExpressRoute, internet vagy VPN) valamelyikén keresztül. A rendszer alapértelmezett útválasztási táblája lecserélhető a felhasználó által meghatározott útválasztási táblára, amely a benne lévő útvonalakat egyéni útvonalakra cseréli. A felhasználó által meghatározott útválasztás használatával az ügyfelek egyedi útvonalakat hozhatnak létre készülékekhez, például tűzfalakhoz vagy behatolás-észlelő készülékekhez, vagy blokkolhatják a hozzáférést megadott alhálózatokhoz azon alhálózatról, amelyen a felhasználó által megadott útvonal található. Az User-Defined útvonalak áttekintését [itt](../virtual-network/virtual-networks-udr-overview.md)tekintheti meg. 

## <a name="security"></a>Biztonság
A használt modelltől – Szolgáltatóhoz való csatlakozás vagy Szolgáltatón keresztüli csatlakozás – függően az ügyfél a biztonsági házirendeket a vNetben határozza meg, vagy megadja a CSP-nek a biztonsági házirendekre vonatkozó követelményeket azok meghatározásához a vNetekben. A következő kritériumok határozhatók meg:

1. **Ügyfél elkülönítése** – Az Azure platform lehetőséget biztosít az ügyfél elkülönítésére az ügyfél-azonosító és a vNet-adatok egy biztonságos adatbázisban való eltárolásával, amelyeket arra használ, hogy az egyes ügyfelek forgalmát egy GRE-alagútba bújtassa.
2. A **hálózati biztonsági csoport (NSG)** szabályai segítségével meghatározható a vNetek alhálózatainak engedélyezett be- és kimenő forgalma az Azure-ban. Alapértelmezés szerint a NSG blokkoló szabályokat tartalmaz az internetről a vNet való adatforgalom blokkolására, valamint a vNet belüli adatforgalomra vonatkozó szabályok engedélyezésére. A hálózati biztonsági csoportokkal kapcsolatos további [információkért tekintse](https://azure.microsoft.com/blog/network-security-groups/)meg a következőt:.
3. **Kényszerített bújtatás** – Ezzel a lehetőséggel az Azure-ból származó internetes forgalom átirányítható az ExpressRoute-kapcsolaton keresztül a helyszíni adatközpontba. A kényszerített bújtatással kapcsolatos további információkat [itt](expressroute-routing.md#advertising-default-routes) tekintheti meg.  
4. **Titkosítás** – Annak ellenére, hogy az ExpressRoute-kapcsolatcsoportok adott ügyfelekre vonatkoznak, fennáll a lehetősége, hogy a hálózatszolgáltató rendszerét feltörik, és így a behatoló megvizsgálhatja a csomagforgalmat. Az ilyen helyzetek kezelése érdekében az ügyfél vagy a CSP a kapcsolaton áthaladó forgalom titkosításához meghatározhat IPSec-bújtatás módú házirendeket a helyszíni erőforrások és az Azure-erőforrások között áramló teljes forgalomra vonatkozóan (lásd az 1-es ügyfélre vonatkozó választható bújtatás módú IPSec-házirendeket a fenti 5. ábrán: ExpressRoute-biztonság). A második lehetőség egy tűzfalkészülék használata az ExpressRoute-kapcsolatcsoport mindegyik végpontján. Ehhez további, harmadik féltől származó tűzfalat futtató virtuális gépeket/berendezéseket kell telepíteni mindkét végponton a ExpressRoute áramkörön keresztüli forgalom titkosításához.

![helyettesítő szöveg](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Következő lépések
A felhőszolgáltatói szolgáltatás segítségével anélkül növelheti az ügyfelei számára nyújtott értéket, hogy költséges infrastruktúra- és képességberuházásokat kellene eszközölnie, és így megtarthatja pozícióját, mint első számú szolgáltató a kiszervezések terén. A Microsoft Azure-ral való zökkenőmentes integráció a CSP API-n keresztül valósítható meg, amelynek segítségével a Microsoft Azure felügyelete integrálható meglévő felügyeleti keretrendszerekbe.  

További információt az alábbi hivatkozásokat követve tekinthet meg:

[Azure a felhőszolgáltatói programban](/azure/cloud-solution-provider).  
[Felkészülés a felhőszolgáltatóként kínált tranzakciók végrehajtására](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[A Microsoft felhőszolgáltatói erőforrásai](https://partner.microsoft.com/solutions/cloud-reseller-resources).