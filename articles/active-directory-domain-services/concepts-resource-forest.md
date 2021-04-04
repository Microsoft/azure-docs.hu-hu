---
title: Erőforrás-erdővel kapcsolatos fogalmak a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogy milyen erőforrás-erdő van Azure Active Directory Domain Servicesban, és hogy miként használják a céget a korlátozott felhasználói hitelesítési lehetőségekkel vagy biztonsági kérdésekkel rendelkező hibrid környezetben.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 79cf408bcb9060c247b97e6a81204c5a5517d384
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620001"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Az erőforrás-erdőhöz kapcsolódó fogalmak és szolgáltatások Azure Active Directory Domain Services

A Azure Active Directory Domain Services (Azure AD DS) bejelentkezési élményt nyújt az örökölt, helyszíni és üzletági alkalmazásokhoz. A helyszíni és a Felhőbeli felhasználók felhasználóinak, csoportjainak és jelszavainak kivonatai szinkronizálva lesznek az Azure AD DS felügyelt tartományával. Ezek a szinkronizált jelszó-kivonatok lehetővé teszi a felhasználók számára, hogy a helyszíni AD DS, Microsoft 365 és Azure Active Directory számára egyetlen hitelesítő adatot használjanak.

Bár a biztonságos és további biztonsági előnyöket biztosít, egyes szervezetek nem tudják szinkronizálni ezeket a felhasználói jelszavakat az Azure AD-be vagy az Azure AD DSba. Előfordulhat, hogy a szervezetben lévő felhasználók nem ismerik a jelszavukat, mert csak az intelligens kártyás hitelesítést használják. Ezek a korlátozások megakadályozzák, hogy egyes szervezetek az Azure AD DS használatával felemelik és áthelyezhetik a helyi klasszikus alkalmazásokat az Azure-ba.

Ezen igények és korlátozások kezelése érdekében létrehozhat egy erőforrás-erdőt használó felügyelt tartományt. Ez a fogalmi cikk a biztonságos hitelesítési módszer biztosításához szükséges erdőket ismerteti, valamint azt, hogy miként bíznak más erőforrásokban.

## <a name="what-are-forests"></a>Mik azok az erdők?

Az *erdők* Active Directory Domain Services (AD DS) által használt logikai szerkezetek egy vagy több *tartomány* csoportosításához. A tartományok ezután objektumokat tárolnak a felhasználók vagy csoportok számára, és biztosítják a hitelesítési szolgáltatásokat.

Az Azure AD DS felügyelt tartományokban az erdő csak egy tartományt tartalmaz. A helyszíni AD DS erdők gyakran sok tartományt tartalmaznak. A nagyméretű szervezeteknél – különösen az összevonások és a beszerzések után – előfordulhat, hogy több helyszíni erdővel is rendelkezik, amelyek mindegyike több tartományt tartalmaz.

Alapértelmezés szerint a felügyelt tartomány *felhasználói* erdőként jön létre. Ez a típusú erdő az Azure AD összes objektumát szinkronizálja, beleértve a helyszíni AD DS környezetben létrehozott felhasználói fiókokat is. A felhasználói fiókok közvetlenül hitelesíthetők a felügyelt tartományon, például bejelentkezhetnek egy tartományhoz csatlakoztatott virtuális gépre. A felhasználói erdő akkor működik, ha a jelszó-kivonatok szinkronizálhatók, és a felhasználók nem használnak exkluzív bejelentkezési módszereket, például az intelligens kártyás hitelesítést.

Felügyelt tartományi *erőforrás* -erdőben a felhasználók egy egyirányú erdőszintű *megbízhatósági kapcsolaton* keresztül hitelesíthetők a helyszíni AD DS. Ezzel a módszerrel a felhasználói objektumok és jelszavak kivonatai nem szinkronizálhatók a felügyelt tartományba. A felhasználói objektumok és a hitelesítő adatok csak a helyszíni AD DSban találhatók. Ez a megközelítés lehetővé teszi, hogy a vállalatok az Azure-ban olyan erőforrásokat és alkalmazás-platformokat működtessenek, amelyek a klasszikus hitelesítéstől (például LDAPs, Kerberos vagy NTLM) függenek, de a hitelesítési problémák és a problémák el

Az erőforrás-erdők azt is lehetővé teszik, hogy egyszerre több összetevőt lehessen feloldani az alkalmazások számára. Számos örökölt helyszíni alkalmazás többrétegű, gyakran webkiszolgálót, előtér-adatbázist és sok adatbázissal kapcsolatos összetevőt használ. Ezek a szintek megnehezítik a teljes alkalmazás a felhőbe való átváltását egy lépésben. A Resource Forests használatával a felhőbe felemelhető az alkalmazás fokozatos megközelítése, amely megkönnyíti az alkalmazások áthelyezését az Azure-ba.

## <a name="what-are-trusts"></a>Mi a megbízhatóság?

Az egynél több tartománnyal rendelkező szervezeteknek gyakran kell a felhasználókhoz hozzáférni egy másik tartományban található megosztott erőforrásokhoz. A megosztott erőforrásokhoz való hozzáféréshez az szükséges, hogy az egyik tartományban lévő felhasználók egy másik tartományban legyenek hitelesítve. A különböző tartományokban lévő ügyfelek és kiszolgálók közötti hitelesítési és engedélyezési képességek biztosítása érdekében a két tartomány között *megbízhatósági kapcsolatnak* kell lennie.

A tartományi megbízhatósági kapcsolatok esetében az egyes tartományok hitelesítési mechanizmusai megbíznak a másik tartománytól érkező hitelesítésekkel. A megbízhatósági kapcsolatok segítségével szabályozható hozzáférést biztosíthat az erőforrás-tartományokban lévő megosztott erőforrásokhoz (a *megbízó* tartományhoz), ha ellenőrzi, hogy a bejövő hitelesítési kérelmek megbízható hitelesítésszolgáltatótól ( *megbízható* tartományból) származnak-e. A megbízhatósági kapcsolatok olyan hidakként működnek, amelyek csak az érvényesített hitelesítési kérelmeket engedélyezik a tartományok közötti utazáshoz.

A hitelesítési kérelmek megbízhatóságának módja a konfigurálásának módjától függ. A megbízhatósági kapcsolatok az alábbi módszerek egyikével konfigurálhatók:

* **Egyirányú** – hozzáférést biztosít a megbízható tartományból a megbízó tartomány erőforrásaihoz.
* **Kétirányú** – hozzáférést biztosít az egyes tartományokból a másik tartomány erőforrásaihoz.

A megbízhatósági kapcsolatok úgy is konfigurálhatók, hogy a további megbízhatósági kapcsolatokat a következő módszerek egyikével kezeljék:

* Nem **tranzitív** – a megbízhatóság csak a két megbízhatósági partner tartománya között létezik.
* A **tranzitív** megbízhatósági kapcsolat automatikusan kiterjeszthető minden olyan tartományra, amelyet a partnerek bármelyike megbízhatónak tart.

Bizonyos esetekben a rendszer automatikusan létrehozza a megbízhatósági kapcsolatokat a tartományok létrehozásakor. Máskor ki kell választania egy megbízhatósági típust, és explicit módon meg kell határoznia a megfelelő kapcsolatokat. A használt megbízhatósági kapcsolatok és a megbízhatósági kapcsolatok szerkezete attól függ, hogy miként történik a AD DS könyvtár rendszerezése, és hogy a Windows különböző verziói léteznek-e a hálózaton.

## <a name="trusts-between-two-forests"></a>Két erdő közötti megbízhatóság

A tartományi megbízhatósági kapcsolatok kiterjeszthetők egyetlen erdőn belül egy másik erdőre, ha manuálisan létrehoz egy egyirányú vagy kétirányú erdőszintű megbízhatóságot. Az erdőszintű megbízhatóság olyan tranzitív megbízhatóság, amely csak az erdő gyökértartományának és egy második erdőszintű gyökértartomány között létezik.

* Egy egyirányú erdőszintű megbízhatósági kapcsolat lehetővé teszi, hogy az egyik erdőben lévő összes felhasználó megbízzon a másik erdőben lévő összes tartományban.
* A kétirányú erdőszintű megbízhatóság tranzitív megbízhatósági kapcsolatot alakít ki mindkét erdő minden tartománya között.

Az erdőszintű megbízhatósági kapcsolatok tranzitivitás a két erdős partnerre korlátozódik. Az erdőszintű megbízhatóság nem terjed ki a partnerek egyike által megbízhatónak tartott további erdőkre.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű megbízhatóság diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

A szervezet AD DS struktúrájától függően különböző tartományi és erdőszintű megbízhatósági konfigurációkat hozhat létre. Az Azure AD DS csak egyirányú erdőszintű megbízhatósági kapcsolatot támogat. Ebben a konfigurációban a felügyelt tartomány erőforrásai megbíznak a helyszíni erdőben található összes tartományban.

## <a name="supporting-technology-for-trusts"></a>A megbízhatósági kapcsolatok támogató technológiája

A megbízhatósági kapcsolatok különféle szolgáltatásokat és szolgáltatásokat használnak, például a DNS-t a tartományvezérlők megkereséséhez a partneri erdőkben. A megbízhatósági kapcsolatok az NTLM-és Kerberos-hitelesítési protokolloktól, valamint a Windows-alapú engedélyezési és hozzáférés-vezérlési mechanizmusoktól függenek, így biztosítva a biztonságos kommunikációs infrastruktúra használatát AD DS tartományok és erdők között. A következő szolgáltatások és funkciók segítenek a sikeres megbízhatósági kapcsolatok támogatásában.

### <a name="dns"></a>DNS

AD DS DNS-t igényel a tartományvezérlő (DC) helyéhez és elnevezéséhez. A következő DNS-támogatás biztosítva a AD DS sikeres működéséhez:

* Névfeloldási szolgáltatás, amely lehetővé teszi a hálózati gazdagépek és szolgáltatások számára a tartományvezérlők megkeresését.
* Elnevezési struktúra, amely lehetővé teszi a vállalat számára, hogy tükrözze a szervezeti struktúráját a címtárszolgáltatás-tartományok neveiben.

A DNS-tartomány névterét általában a AD DS tartományi névtér tükrözésére használják. Ha van egy meglévő DNS-névtér a AD DS központi telepítés előtt, a DNS-névtér általában AD DSre van particionálva, és létrejön egy DNS-altartomány és delegálás az AD DS erdő gyökeréhez. Ezután további DNS-tartományneveket adnak hozzá minden AD DS gyermektartomány számára.

A DNS a AD DS tartományvezérlők helyének támogatására is használható. A DNS-zónák olyan DNS-erőforrásrekordok használatával vannak feltöltve, amelyek lehetővé teszik a hálózati gazdagépek és szolgáltatások számára AD DS tartományvezérlők megkeresését.

### <a name="applications-and-net-logon"></a>Alkalmazások és hálózati bejelentkezés

Mindkét alkalmazás és a Net Logon szolgáltatás a Windows Distributed Security Channel modell összetevői. A Windows Server rendszerbe integrált alkalmazások és a AD DS hitelesítési protokollok használatával kommunikálnak a hálózati bejelentkezési szolgáltatással, így biztonságos elérési utat hozhat létre a hitelesítéshez.

### <a name="authentication-protocols"></a>Hitelesítési protokollok

AD DS tartományvezérlők a következő protokollok egyikével hitelesítik a felhasználókat és az alkalmazásokat:

* **Kerberos 5-ös verziójú hitelesítési protokoll**
    * A Kerberos 5-ös verziója a Windows rendszerű és a külső gyártótól származó operációs rendszereket támogató helyi számítógépek által használt alapértelmezett hitelesítési protokoll. Ez a protokoll az RFC 1510-ben van meghatározva, és teljes mértékben integrálva van a AD DS, a Server Message Block (SMB), a HTTP és a távoli eljáráshívás (RPC) szolgáltatással, valamint az ezeket a protokollokat használó ügyfél-és kiszolgálói alkalmazásokkal.
    * A Kerberos protokoll használata esetén a kiszolgálónak nem kell kapcsolódnia a TARTOMÁNYVEZÉRLŐhöz. Ehelyett az ügyfél a kiszolgáló fiók tartományában lévő egyik TARTOMÁNYVEZÉRLŐtől kapja meg a jegyet. A kiszolgáló ezt követően érvényesíti a jegyet anélkül, hogy bármilyen más hatósággal kellene konzultálnia.
    * Ha a tranzakcióban részt vevő számítógépek nem támogatják a Kerberos 5-ös verzióját, akkor a rendszer az NTLM protokollt használja.

* **NTLM hitelesítési protokoll**
    * Az NTLM protokoll a régebbi operációs rendszerek által használt klasszikus hálózati hitelesítési protokoll. Kompatibilitási okokból AD DS tartományok használják a korábbi Windows-alapú ügyfelekhez és kiszolgálókhoz, illetve harmadik féltől származó operációs rendszerekhez készült alkalmazásokból érkező hálózati hitelesítési kérelmek feldolgozását.
    * Ha az NTLM protokollt az ügyfél és a kiszolgáló között használja, a kiszolgálónak csatlakoznia kell egy tartományi hitelesítési szolgáltatáshoz a tartományvezérlőn, hogy ellenőrizze az ügyfél hitelesítő adatait. A kiszolgáló úgy hitelesíti az ügyfelet, hogy az ügyfél hitelesítő adatait továbbítja az ügyfél fiókjának tartományában lévő tartományvezérlőnek.
    * Ha két AD DS tartomány vagy erdő megbízhatósági kapcsolattal rendelkezik, a protokollok használatával küldött hitelesítési kérések átirányíthatók úgy, hogy mindkét erdőben elérhetők legyenek az erőforrások.

## <a name="authorization-and-access-control"></a>Engedélyezési és hozzáférés-vezérlés

Az engedélyezési és megbízhatósági technológiák együttműködve biztosítják a biztonságos kommunikációs infrastruktúrát AD DS tartományok vagy erdők között. Az engedélyezés meghatározza, hogy a felhasználók milyen szintű hozzáférési jogosultságokkal rendelkeznek a tartomány erőforrásaihoz. A megbízhatósági kapcsolatok megkönnyítik a felhasználók tartományok közötti engedélyezését azáltal, hogy a más tartományokban lévő felhasználókat hitelesítő utat biztosítanak, így a tartományokban lévő megosztott erőforrásokra irányuló kérések is engedélyezhetők.

Ha a megbízható tartomány ellenőrzi egy megbízható tartomány hitelesítési kérelmét, azt a rendszer átadja a célként megadott erőforrásnak. A cél erőforrás ezután meghatározza, hogy a rendszer engedélyezi-e a megbízható tartományban lévő felhasználó, szolgáltatás vagy számítógép által a hozzáférés-vezérlési konfiguráció alapján végzett kérést.

A megbízhatósági kapcsolatok biztosítják ezt a mechanizmust a megbízó tartománynak átadott hitelesítési kérelmek érvényesítéséhez. Az erőforrás-számítógép hozzáférés-vezérlési mechanizmusai határozzák meg a megbízható tartományban a kérelmezőnek biztosított hozzáférés végső szintjét.

## <a name="next-steps"></a>Következő lépések

További információ a megbízhatóságokról: [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

A felügyelt tartomány erőforrás-erdővel való létrehozásának első lépéseiért tekintse meg [Az Azure AD DS felügyelt tartomány létrehozása és konfigurálása][tutorial-create-advanced]című témakört. Ezután [létrehozhat egy kimenő erdőszintű megbízhatósági kapcsolatot a helyszíni tartományba][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
