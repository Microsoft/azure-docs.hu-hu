---
title: Rugalmasabb hibrid hitelesítés kiépítése Azure Active Directory
description: Útmutató az építészek és a rendszergazdák számára a rugalmas hibrid infrastruktúra kiépítéséhez.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de01788c01d4d6dedd9563faaaac07bff30bbd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724759"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>Hozzon létre rugalmasságot a hibrid architektúrában

A hibrid hitelesítés lehetővé teszi a felhasználóknak a felhőalapú erőforrások elérését a helyszínen elsajátított identitásokkal. A hibrid infrastruktúra magában foglalja a Felhőbeli és a helyszíni összetevőket is.

* A felhőalapú összetevők közé tartoznak az Azure AD, az Azure-erőforrások és-szolgáltatások, a szervezet felhőalapú alkalmazásai és az SaaS-alkalmazások.

* A helyszíni összetevők közé tartoznak a helyszíni alkalmazások, az erőforrások, például az SQL-adatbázisok, valamint egy olyan identitás-szolgáltató, mint a Windows Server Active Directory. 

> [!IMPORTANT]
> A hibrid infrastruktúra rugalmasságának tervezése során kulcsfontosságú a függőségek és a meghibásodási pontok csökkentése. 

A Microsoft három módszert kínál a hibrid hitelesítéshez. A beállítások a rugalmassági sorrendben vannak felsorolva. Javasoljuk, hogy ha lehetséges, alkalmazza a jelszó-kivonat szinkronizálását.

* A [jelszó-kivonat szinkronizálása](../hybrid/whatis-phs.md) (PHS) Azure ad Connect használatával szinkronizálja az identitást és a jelszó kivonatát az Azure ad-be, lehetővé téve a felhasználók számára a felhőalapú erőforrásokhoz való bejelentkezést a helyi jelszavával. A PHS csak a szinkronizáláshoz, és nem a hitelesítéshez rendelkezik helyszíni függőségekkel.

* Az [átmenő hitelesítés](../hybrid/how-to-connect-pta.md) (PTA) átirányítja a felhasználókat az Azure ad-be a bejelentkezéshez. Ezt követően a rendszer ellenőrzi a felhasználónevet és a jelszót a helyszíni Active Directory a vállalati hálózaton üzembe helyezett ügynökön keresztül. A PTA Azure AD PTA-ügynökök helyszíni lábnyoma a helyszíni kiszolgálókon található.

* Az [összevonási](../hybrid/whatis-fed.md) ügyfelek olyan összevonási szolgáltatást telepítenek, mint például a AD FS, majd az Azure ad érvényesíti az összevonási szolgáltatás által létrehozott SAML-jogcímet. Az összevonás a legmagasabb függőséggel rendelkezik a helyszíni infrastruktúrával, így több meghibásodási ponttal rendelkezik. 

   
Előfordulhat, hogy egy vagy több módszert használ a szervezetben. További információ: [válassza ki a megfelelő hitelesítési módszert az Azure ad Hybrid Identity megoldáshoz](../hybrid/choose-ad-authn.md). Ez a cikk egy döntési fát tartalmaz, amely segítséget nyújt a módszertan eldöntésében.

## <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása

Az Azure AD legegyszerűbb és legrugalmasabb hibrid hitelesítési lehetősége a [jelszó-kivonatolási szinkronizálás](../hybrid/whatis-phs.md) , amely nem rendelkezik helyszíni identitás-infrastruktúra függőséggel a hitelesítési kérések feldolgozásakor. Miután a jelszó-kivonatokkal rendelkező identitások szinkronizálva lesznek az Azure AD-vel, a felhasználók a helyszíni identitás-összetevőktől való függőség nélkül hitelesíthetők a Felhőbeli erőforrásokhoz. 

![PHS architektúra ábrája](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

Ha ezt a hitelesítési lehetőséget választja, akkor nem tapasztal fennakadást, ha a helyszíni identitás összetevői elérhetetlenné válnak. A helyszíni megszakadást számos ok okozhatja, többek között a hardverhiba, az áramkimaradások, a természeti katasztrófák és a kártevők elleni támadások. 

### <a name="how-do-i-implement-phs"></a>Hogyan implementálja a PHS?

A PHS megvalósításához tekintse meg a következő forrásokat:

* [Jelszó-kivonatolási szinkronizálás implementálása Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md)

* [Jelszókivonat szinkronizálásának engedélyezése](../hybrid/how-to-connect-password-hash-synchronization.md)

Ha a követelmények olyanok, amelyek nem használhatják a PHS, használja az átmenő hitelesítést.

## <a name="pass-through-authentication"></a>Átmenő hitelesítés

Az átmenő hitelesítés a kiszolgálókon található helyszíni hitelesítési ügynököktől függ. Az Azure AD és a helyszíni PTA-ügynökök között állandó kapcsolat vagy Service Bus szerepel. A tűzfal, a hitelesítési ügynököt üzemeltető kiszolgálók és a helyszíni Windows Server Active Directory (vagy más identitás-szolgáltató) minden lehetséges meghibásodási pont. 

![PTA architektúra ábrája](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>Hogyan a PTA-t?

Az átmenő hitelesítés megvalósításához tekintse meg a következő forrásokat.

* [Az átmenő hitelesítés működése](../hybrid/how-to-connect-pta-how-it-works.md)

* [Az átmenő hitelesítés részletes biztonsági ismertetése](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [Az Azure AD átmenő hitelesítésének telepítése](../hybrid/how-to-connect-pta-quick-start.md)

* Ha PTA-t használ, adjon meg egy [magasan elérhető topológiát](../hybrid/how-to-connect-pta-quick-start.md).

 ## <a name="federation"></a>Összevonás

Az összevonás magában foglalja az Azure AD és az összevonási szolgáltatás közötti megbízhatósági kapcsolat létrehozását, amely magában foglalja a végpontok, a jogkivonat-aláíró tanúsítványok és más metaadatok cseréjét. Amikor egy kérelem érkezik az Azure AD szolgáltatásba, beolvassa a konfigurációt, és átirányítja a felhasználót a konfigurált végpontokra. Ezen a ponton a felhasználó kommunikál az összevonási szolgáltatással, ami az Azure AD által érvényesített SAML-jogcímet bocsát ki. 

A következő ábra egy vállalati Active Directory összevonási szolgáltatások (AD FS) (AD FS) topológiáját mutatja be, amely a redundáns összevonási és webalkalmazás-proxykiszolgálók több helyszíni adatközpontban való üzembe helyezését is tartalmazza. Ez a konfiguráció a vállalati hálózati infrastruktúra összetevőire támaszkodik, például a DNS-t, a hálózati terheléselosztást a Geo-affinitási képességekkel, tűzfalakkal stb. Az összes helyszíni összetevő és kapcsolat nem alkalmas a meghibásodásra. További információért látogasson el a [AD FS Capacity tervezési dokumentációba](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) .

> [!NOTE]
>  Az összevonás a legnagyobb számú helyszíni függőséggel rendelkezik, ezért a legtöbb lehetséges meghibásodási pont. Az ábrán látható AD FS a többi helyszíni Identitáskezelő hasonló tervezési szempontokat mutat be a magas rendelkezésre állás, a méretezhetőség és a feladatátvétel érdekében.

![Az összevonás architektúrájának ábrája](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>Hogyan az összevonás megvalósítása?

Ha összevont hitelesítési stratégiát hajt végre, vagy rugalmasabban szeretné kipróbálni, tekintse meg a következő forrásokat.

* [Mi az összevont hitelesítés?](../hybrid/whatis-fed.md)

* [Az összevonás működése](../hybrid/how-to-connect-fed-whatis.md)

* [Az Azure AD összevonás kompatibilitási listája](../hybrid/how-to-connect-fed-compatibility.md)

* Kövesse a [AD FS Capacity tervezési dokumentációját](/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [AD FS üzembe helyezése az Azure IaaS](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [PHS engedélyezése](../hybrid/tutorial-phs-backup.md) az összevonással együtt

## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)