---
title: Hogyan működik a megbízhatóság a Azure AD Domain Servicesban? | Microsoft Docs
description: További információ arról, hogyan működik az erdőszintű megbízhatóság a Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620205"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Hogyan működik a megbízhatósági kapcsolatok a Azure Active Directory Domain Services erőforrás-erdőkön

A Active Directory Domain Services (AD DS) a tartományi és erdőszintű megbízhatósági kapcsolatokon keresztül több tartományra vagy erdőre kiterjedő biztonságot nyújt. A megbízhatósági kapcsolaton keresztüli hitelesítés előtt a Windowsnak először ellenőriznie kell, hogy a felhasználó, számítógép vagy szolgáltatás által kért tartomány megbízhatósági kapcsolatban áll-e a kérelmező fiók tartományával.

A megbízhatósági kapcsolat kereséséhez a Windows biztonsági rendszer a kérelmet fogadó kiszolgáló tartományvezérlője és a kérést kérő fiók tartományában lévő tartományvezérlő közötti megbízhatósági útvonalat számítja ki.

A AD DS és a Windows elosztott biztonsági modellje által biztosított hozzáférés-vezérlési mechanizmusok biztosítják a tartomány-és erdőszintű megbízhatósági kapcsolatok működtetésének környezetét. Ahhoz, hogy ezek a megbízhatósági kapcsolatok megfelelően működjenek, minden erőforrásnak vagy számítógépnek közvetlen megbízhatósági úttal kell rendelkeznie a tartományvezérlőhöz abban a tartományban, amelyben található.

A megbízhatósági útvonalat a Net Logon szolgáltatás valósítja meg a megbízható tartományi szolgáltatónak hitelesített távoli eljáráshívás (RPC) kapcsolaton keresztül. A biztonságos csatornák a tartományok közötti megbízhatósági kapcsolatokon keresztül is kiterjeszthetők más AD DS tartományokra. Ez a biztonságos csatorna a biztonsági információk, például a felhasználók és csoportok biztonsági azonosítóinak (SID-ek) beszerzésére és ellenőrzésére szolgál.

Az Azure AD DS-beli megbízhatósági kapcsolatok alkalmazásáról az erőforrás- [erdővel kapcsolatos fogalmak és szolgáltatások][create-forest-trust]című cikk nyújt tájékoztatást.

A megbízhatósági kapcsolatok Azure AD DSban való használatának megkezdéséhez [hozzon létre egy olyan felügyelt tartományt, amely erdőszintű megbízhatósági kapcsolatot használ][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Megbízhatósági kapcsolatok folyamatai

A bizalmi kapcsolaton keresztüli biztonságos kommunikáció folyamata meghatározza a megbízhatóság rugalmasságát. A megbízhatóság létrehozásának és konfigurálásának módja határozza meg, hogy a kommunikáció meddig terjed ki az erdőkön belül vagy azok között.

A megbízhatósági kapcsolatok áramlását a megbízhatóság iránya határozza meg. A bizalmi kapcsolatok lehetnek egyirányú vagy kétirányúak, és tranzitívak vagy nem tranzitívak is lehetnek.

Az alábbi ábra azt mutatja, hogy az *1* . és *2* . fa összes tartománya alapértelmezés szerint tranzitív megbízhatósági kapcsolattal rendelkezik. Ennek eredményeképpen az *1. fában* lévő felhasználók hozzáférhetnek a *2* . fában lévő tartományok erőforrásaihoz, és az *1* . fában lévő felhasználók hozzáférhetnek az erőforrásokhoz a *2. fában*, ha a megfelelő engedélyek hozzá vannak rendelve az erőforráshoz.

![Két erdő közötti megbízhatósági kapcsolatok diagramja](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Egyirányú és kétirányú megbízhatósági kapcsolatok

A megbízhatósági kapcsolatok lehetővé teszik az erőforrásokhoz való hozzáférést, akár egyirányú, akár kétirányú is lehet.

Az egyirányú megbízhatósági kapcsolat egy egyirányú hitelesítési útvonal, amely két tartomány között jön létre. Az *a tartomány és a* *b* tartomány közötti egyirányú megbízhatósági kapcsolaton keresztül az *a* tartományba tartozó felhasználók hozzáférhetnek a *b tartomány* erőforrásaihoz. A *B tartományban* lévő felhasználók azonban nem férhetnek hozzá az *a tartomány* erőforrásaihoz.

Néhány egyirányú megbízhatósági kapcsolat nem tranzitív vagy tranzitív lehet a létrehozandó megbízhatóság típusától függően.

Kétirányú megbízhatósági kapcsolat esetén a *tartomány a* *b tartományba* , a *b tartomány* pedig az *a* tartományt bízik meg. Ez a konfiguráció azt jelenti, hogy a két tartomány közötti hitelesítési kérések mindkét irányban átadhatók. Néhány kétirányú kapcsolat nem tranzitív vagy tranzitív lehet a létrehozandó megbízhatóság típusától függően.

Egy AD DS erdőben lévő összes tartományi megbízhatóság kétirányú, tranzitív megbízhatósági kapcsolat. Új gyermektartomány létrehozásakor a rendszer automatikusan létrehoz egy kétirányú, tranzitív megbízhatósági kapcsolatot az új gyermektartomány és a szülő tartomány között.

### <a name="transitive-and-non-transitive-trusts"></a>Tranzitív és nem tranzitív megbízhatósági kapcsolatok

A tranzitivitás meghatározza, hogy a megbízhatóság kiterjeszthető-e azon két tartományon kívül, amelyekkel létrehozták.

* Tranzitív megbízhatósági kapcsolat lehet a más tartományokkal való megbízhatósági kapcsolatok kiterjesztésére.
* Nem tranzitív megbízhatósági kapcsolat használható a más tartományokkal való megbízhatósági kapcsolatok megtagadására.

Minden alkalommal, amikor új tartományt hoz létre egy erdőben, a rendszer automatikusan létrehoz egy kétirányú, tranzitív megbízhatósági kapcsolatot az új tartomány és a szülő tartománya között. Ha a gyermektartomány hozzá lett adva az új tartományhoz, a megbízhatósági útvonal felfelé halad a tartományi hierarchián keresztül, és kiterjeszti az új tartomány és a szülőtartomány tartománya között létrejött kezdeti megbízhatósági útvonalat. A tranzitív megbízhatósági kapcsolatok a tartomány fáján keresztül haladnak át, így tranzitív megbízhatósági kapcsolatot hoznak létre a tartományfa összes tartománya között.

A hitelesítési kérelmek követik ezeket a megbízhatósági útvonalakat, így az erdő bármely tartományának fiókjait az erdő bármely másik tartománya hitelesítheti. Egyszeri bejelentkezési folyamat esetén a megfelelő engedélyekkel rendelkező fiókok hozzáférhetnek az erdőben található bármely tartomány erőforrásaihoz.

## <a name="forest-trusts"></a>Erdőszintű megbízhatóságok

Az erdőszintű megbízhatósági kapcsolatok segítségével felügyelheti a szegmentált AD DS infrastruktúrákat, és támogathatja az erőforrásokhoz és más objektumokhoz való hozzáférést több erdőn keresztül. Az erdőszintű megbízhatósági kapcsolatok a szolgáltatók számára hasznosak, az egyesüléseket vagy beszerzéseket végző vállalatokat, az együttműködésen alapuló üzleti extraneteket és az adminisztratív önállóságot igénylő vállalatokat.

Az erdőszintű megbízhatósági kapcsolatok használatával két különböző erdőt kapcsolhat össze egy egyirányú vagy kétirányú tranzitív megbízhatósági kapcsolat formájában. Az erdőszintű megbízhatósági kapcsolat lehetővé teszi, hogy a rendszergazdák két AD DS erdőhöz csatlakozzanak egyetlen megbízhatósági kapcsolatban, hogy zökkenőmentes hitelesítési és engedélyezési élményt nyújtsanak az erdőkben.

Erdőszintű megbízhatósági kapcsolat csak az egyik erdőben lévő erdő gyökértartományában és egy másik erdőben lévő erdő gyökértartományában hozható létre. Az erdőszintű megbízhatósági kapcsolatok csak két erdő között hozhatók létre, és nem terjeszthetők ki implicit módon harmadik erdőre. Ez azt jelenti, hogy ha erdőszintű megbízhatóság jön létre az *1* . erdő és a *2*. erdő között, és egy másik erdőszintű megbízhatósági kapcsolat jön létre a *2* . erdő és a *3*. erdő között, az *erdő 1* nem rendelkezik implicit megbízhatósággal a *3. erdővel*.

A következő ábra két különálló erdőszintű megbízhatósági kapcsolatot mutat be egyetlen szervezet három AD DS erdője között.

![Az erdőszintű megbízhatósági kapcsolatok diagramja egyetlen szervezeten belül](./media/concepts-forest-trust/forest-trusts-diagram.png)

A példában szereplő konfiguráció a következő hozzáférést biztosítja:

* A *2. erdőben* lévő felhasználók bármelyik tartományban hozzáférhetnek az erőforrásokhoz az *1* . vagy *erdő 3* . erdőben
* A *3. erdő* felhasználói hozzáférhetnek a *2. erdőben* található bármely tartomány erőforrásaihoz
* Az *1. erdőben* lévő felhasználók hozzáférhetnek a 2. *erdőben* található bármely tartomány erőforrásaihoz

Ez a konfiguráció nem engedélyezi, hogy az *1. erdőben* lévő felhasználók hozzáférhessenek az erőforrásokhoz a *3. erdőben* , vagy fordítva. Ahhoz, hogy a felhasználók mind az *1* . erdő, mind a *3* . erdőben lehetővé tegyék az erőforrások megosztását, kétirányú tranzitív megbízhatósági kapcsolatot kell létrehozni a két erdő között.

Ha egyirányú erdőszintű megbízhatósági kapcsolat jön létre két erdő között, a megbízható erdő tagjai a megbízó erdőben lévő erőforrásokat használhatják. A megbízhatóság azonban csak egy irányba működik.

Ha például egy egyirányú, erdőszintű megbízhatósági kapcsolat jön létre az *1. erdő* (a megbízható erdő) és a *2. erdő* (a megbízó erdő) között:

* Az *1. erdő* tagjai hozzáférhetnek a *2. erdőben* található erőforrásokhoz.
* A *2. erdő* tagjai nem férnek hozzá az *1. erdőben* található erőforrásokhoz ugyanazzal a megbízhatósággal.

> [!IMPORTANT]
> Azure AD Domain Services az erőforrás-erdő csak egyirányú erdőszintű megbízhatósági kapcsolatot támogat a helyszíni Active Directory számára.

### <a name="forest-trust-requirements"></a>Erdőszintű megbízhatósági követelmények

Erdőszintű megbízhatóság létrehozása előtt ellenőriznie kell, hogy a megfelelő tartománynévrendszer (DNS) infrastruktúra van-e érvényben. Az erdőszintű megbízhatósági kapcsolatok csak akkor hozhatók létre, ha a következő DNS-konfigurációk valamelyike elérhető:

* Egyetlen legfelső szintű DNS-kiszolgáló a legfelső szintű DNS-kiszolgáló mind az erdő DNS-névterekhez – a legfelső szintű zóna minden DNS-névtérhez és az összes DNS-kiszolgálóhoz tartozó gyökérútmutató-delegálást tartalmaz, beleértve a legfelső szintű DNS-kiszolgálót is.
* Ha nincs megosztott gyökér DNS-kiszolgáló, és az egyes erdők DNS-névterében lévő legfelső szintű DNS-kiszolgálók az egyes DNS-névterek DNS feltételes továbbítóit használják a más névtérben található nevekre irányuló lekérdezések irányítására.

    > [!IMPORTANT]
    > Azure AD Domain Services az erőforrás-erdőben ezt a DNS-konfigurációt kell használnia. Az erőforrás-erdő DNS-névterén kívül más DNS-névtér nem a Azure AD Domain Services egyik funkciója. A feltételes továbbító a megfelelő konfiguráció.

* Ha nincs megosztott gyökér DNS-kiszolgáló, és az egyes erdő DNS-névterében lévő legfelső szintű DNS-kiszolgálók a DNS másodlagos zónáit használják, a rendszer az egyes DNS-névterekben konfigurálja a lekérdezéseket a másik névtérben található nevekre.

Erdőszintű megbízhatóság létrehozásához a Tartománygazdák csoport (az erdő gyökértartományában) vagy a vállalati rendszergazdák csoport tagjának kell lennie Active Directory. Minden megbízhatósághoz hozzá van rendelve egy jelszó, amelyet mindkét erdőben a rendszergazdáknak ismerniük kell. Mindkét erdőben a vállalati rendszergazdák tagjai egyszerre is létrehozhatják a megbízhatósági kapcsolatokat mindkét erdőben, és ebben az esetben a titkosítási szempontból véletlenszerű jelszó automatikusan létrejön és íródik mindkét erdőhöz.

A Azure AD Domain Serviceshoz tartozó kimenő erdőszintű megbízhatósági kapcsolat létrejön a Azure Portal. Nem hozhatja létre manuálisan a megbízhatóságot a felügyelt tartományhoz. A bejövő erdőszintű megbízhatóságot a helyszíni Active Directory korábban feljegyzett jogosultságokkal rendelkező felhasználónak kell konfigurálnia.

## <a name="trust-processes-and-interactions"></a>Megbízhatósági folyamatok és interakciók

Számos tartományok közötti és erdők közötti tranzakció függ a tartomány-vagy erdőszintű megbízhatóságtól a különböző feladatok elvégzése érdekében. Ez a szakasz azokat a folyamatokat és interakciókat ismerteti, amelyeket az erőforrásokkal kapcsolatban a rendszer a Megbízhatóságok és a hitelesítési átirányítások kiértékelésével biztosít.

### <a name="overview-of-authentication-referral-processing"></a>A hitelesítés-átirányítási feldolgozás áttekintése

Ha a hitelesítésre vonatkozó kérelmet egy tartományhoz utalnak, akkor az adott tartományban lévő tartományvezérlőnek meg kell határoznia, hogy van-e megbízhatósági kapcsolat a kérést tartalmazó tartománnyal. Meg kell határozni a megbízhatóság irányát, valamint azt, hogy a bizalmi kapcsolat tranzitív vagy nem tranzitív-e, mielőtt hitelesíti a felhasználót a tartomány erőforrásainak eléréséhez. A megbízható tartományok között megjelenő hitelesítési folyamat a használatban lévő hitelesítési protokolltól függően változik. A Kerberos V5 és az NTLM protokollok különböző tartományokra való hitelesítésre irányuló átirányítási folyamatokat

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5-hivatkozás feldolgozása

A Kerberos V5 hitelesítési protokoll a tartományvezérlőkön lévő hálózati bejelentkezési szolgáltatástól függ az ügyfél-hitelesítéshez és az engedélyezési információkhoz. A Kerberos protokoll csatlakozik egy online kulcsszolgáltatóhoz (KDC) és a Active Directory Account Store-hoz a munkamenet-jegyek számára.

A Kerberos protokoll megbízhatósági kapcsolatot is használ a tartományok közötti TGS, valamint a jogosultsági attribútumok tanúsítványainak (PAC) érvényesítésére egy biztonságos csatornán keresztül. A Kerberos protokoll több tartományon belüli hitelesítést hajt végre a nem Windows rendszerű, Kerberos-tartományokat, például az MIT Kerberos-tartományokat, és nem kell a Net Logon szolgáltatással kommunikálnia.

Ha az ügyfél Kerberos V5 protokollt használ a hitelesítéshez, a fiók tartományának egyik tartományvezérlőjén kér egy jegyet a célkiszolgálóra. A Kerberos KDC megbízható közvetítőként működik az ügyfél és a kiszolgáló között, és olyan munkamenetkulcsot biztosít, amely lehetővé teszi a két fél számára, hogy hitelesítsék egymást. Ha a céltartomány eltér az aktuális tartománytól, a KDC egy logikai folyamattal határozza meg, hogy lehet-e hivatkozni a hitelesítési kérelemre:

1. Az aktuális tartomány közvetlenül a kért kiszolgáló tartománya alapján van-e megbízni?
    * Ha igen, küldje el az ügyfelet egy hivatkozónak a kért tartományba.
    * Ha nem, folytassa a következő lépéssel.

2. Létezik tranzitív megbízhatósági kapcsolat az aktuális tartomány és a következő tartomány között a megbízhatósági útvonalon?
    * Ha igen, küldje el az ügyfelet a megbízhatósági útvonalon a következő tartományra.
    * Ha nem, küldje el az ügyfelet a bejelentkezési üzenetet megtagadó üzenetben.

### <a name="ntlm-referral-processing"></a>NTLM-hivatkozó feldolgozása

Az NTLM hitelesítési protokoll a tartományvezérlőkön lévő hálózati bejelentkezési szolgáltatástól függ az ügyfél-hitelesítéshez és az engedélyezési információkhoz. Ez a protokoll hitelesíti a Kerberos-hitelesítést nem használó ügyfeleket. Az NTLM megbízhatósági kapcsolatok használatával továbbítja a hitelesítési kérelmeket a tartományok között.

Ha az ügyfél NTLM protokollt használ a hitelesítéshez, akkor a hitelesítés kezdeti kérelme közvetlenül az ügyfélről a céltartományban lévő erőforrás-kiszolgálóra kerül. Ez a kiszolgáló egy olyan kihívást hoz létre, amelyhez az ügyfél válaszol. A kiszolgáló ezután elküldi a felhasználónak a számítógép fiókja tartományában lévő tartományvezérlőre adott választ. Ez a tartományvezérlő ellenőrzi a felhasználói fiókot a biztonsági fiókok adatbázisán.

Ha a fiók nem létezik az adatbázisban, a tartományvezérlő meghatározza, hogy el kell-e végezni az átmenő hitelesítést, továbbítania kell a kérést, vagy el kell utasítania a kérelmet a következő logika használatával:

1. Az aktuális tartomány közvetlen megbízhatósági kapcsolatban áll a felhasználó tartományával?
    * Ha igen, a tartományvezérlő továbbítja az ügyfél hitelesítő adatait a felhasználó tartományának egyik tartományvezérlőjére az átmenő hitelesítéshez.
    * Ha nem, folytassa a következő lépéssel.

2. Az aktuális tartomány tranzitív megbízhatósági kapcsolatban áll a felhasználó tartományával?
    * Ha igen, adja át a hitelesítési kérést a következő tartományra a megbízhatósági útvonalon:. Ez a tartományvezérlő megismétli a folyamatot, ha ellenőrzi a felhasználó hitelesítő adatait a saját biztonsági fiókjainak adatbázisán.
    * Ha nem, küldjön egy bejelentkezési üzenetet az ügyfélnek.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>A hitelesítési kérések Kerberos-alapú feldolgozása erdőszintű megbízhatósági kapcsolatokon keresztül

Ha két erdőt erdőszintű megbízhatósági kapcsolat köti össze, a Kerberos V5 vagy az NTLM protokoll használatával végrehajtott hitelesítési kérések átirányíthatók az erdők között, hogy mindkét erdőben elérhetők legyenek az erőforrások.

Amikor először létrehoznak egy erdőszintű megbízhatósági kapcsolatot, az egyes erdők összegyűjtik a partner erdőben lévő összes megbízható névteret, és egy [megbízható tartomány objektumban](#trusted-domain-object)tárolják az adatokat. A megbízható névterek közé tartoznak a tartományfa neve, az egyszerű felhasználónév (UPN) utótagok, az egyszerű szolgáltatásnév (SPN) utótagok és a másik erdőben használt biztonsági azonosító (SID) névterek. A rendszer replikálja a TDO objektumokat a globális katalógusba.

Ahhoz, hogy a hitelesítési protokollok követni tudják az erdőszintű megbízhatósági kapcsolatot, az erőforrás-számítógép egyszerű szolgáltatásnév (SPN) fel kell oldani a másik erdőben található helyre. Az egyszerű szolgáltatásnév a következő nevek egyike lehet:

* Egy gazdagép DNS-neve.
* Egy tartomány DNS-neve.
* A szolgáltatási kapcsolódási pont objektumának megkülönböztető neve.

Ha az egyik erdőben lévő munkaállomás egy másik erdőben lévő erőforrás-számítógépen kísérli meg elérni az adatelérést, akkor a Kerberos-hitelesítési folyamat az erőforrás-számítógép SPN-je felé irányuló szolgáltatási jegyhez kapcsolódik a tartományvezérlőhöz. Miután a tartományvezérlő lekérdezte a globális katalógust, és megállapítja, hogy az egyszerű szolgáltatásnév nem ugyanabban az erdőben található, mint a tartományvezérlő, a tartományvezérlő a fölérendelt tartományra hivatkozó hivatkozást küld vissza a munkaállomásnak. Ezen a ponton a munkaállomás lekérdezi a szolgáltatási jegy szülőtartomány tartományát, és folytatja az átirányítási lánc követését, amíg el nem éri az erőforrást tartalmazó tartományt.

A következő ábra és lépések részletes leírást nyújtanak a Kerberos hitelesítési folyamatról, amelyet akkor használ a rendszer, ha a Windows rendszert futtató számítógépek erőforrásokat próbálnak elérni egy másik erdőben található számítógépről.

![A Kerberos-folyamat diagramja erdőszintű megbízhatósági kapcsolaton keresztül](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. A *Felhasználó1* bejelentkezik a *Munkaallomas1 nevű munkaállomásnak* -be a *Europe.tailspintoys.com* tartomány hitelesítő adatainak használatával. A felhasználó ezután megpróbál hozzáférni egy megosztott erőforráshoz a *USA.wingtiptoys.com* erdőben található *Fajlkiszolgalo1 nevű kiszolgálónak* .

2. A *Munkaallomas1 nevű munkaállomásnak* megkeresi a Kerberos KDC-t a tartományában lévő tartományvezérlőn, *GyermekTV1*, és a *Fajlkiszolgalo1 nevű kiszolgálónak* SPN-re vonatkozó szolgáltatási jegyet kér.

3. A *GyermekTV1* nem találja az egyszerű szolgáltatásnevet a tartományi adatbázisban, és lekérdezi a globális katalógust annak megállapítására, hogy a *tailspintoys.com* erdőben található bármely tartomány tartalmazza-e ezt az egyszerű szolgáltatásnevet. Mivel a globális katalógus a saját erdőre korlátozódik, az egyszerű szolgáltatásnév nem található.

    A globális katalógus ezután megkeresi az adatbázisát az erdővel létesített erdőszintű megbízhatósági kapcsolatokról. Ha a rendszer megtalálta, összehasonlítja az erdőszintű megbízhatósági kapcsolat megbízható tartomány objektumában (TDO) felsorolt névutótagok utótagját a cél SPN utótagjának megfelelően. Ha megtalálta a egyezést, a globális katalógus egy útválasztási célzást biztosít a *GyermekTV1*.

    Az útválasztási útmutatók segítik a közvetlen hitelesítési kérelmeket a cél erdő felé. A rendszer csak akkor használja a mutatókat, ha az összes hagyományos hitelesítési csatorna, például a helyi tartományvezérlő, majd a globális katalógus nem talál egyszerű szolgáltatásnevet.

4. A *GyermekTV1* visszaküldi a *Munkaallomas1 nevű munkaállomásnak* a fölérendelt tartományra mutató hivatkozást.

5. A *Munkaallomas1 nevű munkaállomásnak* a *ErdoGyokerTV1* (a szülőtartomány tartományában) lévő tartományvezérlővel csatlakozik egy, a *wingtiptoys.com* erdőben lévő erdő gyökértartományában lévő tartományvezérlőhöz (*ForestRootDC2*).

6. A kért szolgáltatáshoz tartozó *Munkaallomas1 nevű munkaállomásnak* névjegyek *ForestRootDC2* a *wingtiptoys.com* erdőben.

7. A *ForestRootDC2* megkeresi a globális katalógust az egyszerű szolgáltatásnév megtalálásához, és a globális katalógus egyezést talál az egyszerű szolgáltatásnév számára, és visszaküldi a *ForestRootDC2*.

8. A *ForestRootDC2* ezután a *USA.wingtiptoys.com* vissza küldi az átirányítást a *Munkaallomas1 nevű munkaállomásnak*.

9. A *Munkaallomas1 nevű munkaállomásnak* kapcsolatba lép a KDC-val a *ChildDC2* -on, és egyezteti a *Fajlkiszolgalo1 nevű kiszolgálónak*-hez való hozzáférést a *Felhasználó1* -jegyet.

10. Miután a *Munkaallomas1 nevű munkaállomásnak* rendelkezik, a *Fajlkiszolgalo1 nevű kiszolgálónak* küldi a szolgáltatási jegyet, amely beolvassa a *Felhasználó1* biztonsági hitelesítő adatait, és ennek megfelelően létrehozza a hozzáférési jogkivonatot.

## <a name="trusted-domain-object"></a>Megbízható tartományi objektum

A szervezeten belüli minden tartományi vagy erdőszintű megbízhatósági kapcsolatot egy, a tartományon belüli *rendszertárolóban* tárolt megbízható tartományi objektum (TDO) képvisel.

### <a name="tdo-contents"></a>TDO tartalma

A TDO található információk attól függően változnak, hogy a TDO tartományi megbízhatóság vagy erdőszintű megbízhatóság hozta-e létre.

Tartományi megbízhatóság létrehozásakor a rendszer az attribútumokat (például a DNS-tartománynevet, a tartomány biztonsági azonosítóját, a megbízhatóság típusát, a megbízhatósági tranzitivitás és a kölcsönös tartománynevet) jeleníti meg a TDO. Az erdőszintű megbízhatósági kapcsolat TDOs további attribútumokat tárol a partner erdő összes megbízható névtérének azonosításához. Ezek az attribútumok a következők: tartományfa neve, egyszerű felhasználónév (UPN) utótagok, egyszerű szolgáltatásnév (SPN) utótagok és biztonsági azonosító (SID) névterek.

Mivel a megbízhatósági kapcsolatok tárolása Active Directory TDOs történik, az erdő összes tartománya ismeri az erdőn belül érvényben lévő megbízhatósági kapcsolatokat. Hasonlóképpen, ha két vagy több erdő az erdő megbízhatósági kapcsolatain keresztül csatlakozik egymáshoz, az egyes erdőkben található erdőszintű gyökértartomány a megbízható erdők összes tartományában lévő megbízhatósági kapcsolatokkal kapcsolatos ismeretekkel rendelkezik.

### <a name="tdo-password-changes"></a>TDO

A megbízhatósági kapcsolat mindkét tartománya egy jelszót is megoszt, amely a Active Directory TDO objektumában van tárolva. A fiók-karbantartási folyamat részeként minden 30 nap a megbízó tartományvezérlő megváltoztatja a TDO tárolt jelszót. Mivel az összes kétirányú megbízhatósági kapcsolat valójában 2 1-as irányú megbízhatósági kapcsolattal rendelkezik, a folyamat kétszer zajlik a kétirányú megbízhatósági kapcsolatok esetében.

A megbízhatóság megbízható és megbízható oldalon van. A megbízható oldalon minden írható tartományvezérlő használható a folyamathoz. A megbízó oldalon az elsődleges tartományvezérlő emulátora végrehajtja a jelszó módosítását.

A jelszavak módosításához a tartományvezérlők a következő folyamatot hajtják végre:

1. A megbízó tartomány elsődleges tartományvezérlő (PDC) emulátora új jelszót hoz létre. A megbízható tartomány egyik tartományvezérlője soha nem indítja el a jelszó módosítását. Mindig a megbízó tartomány PDC-emulátora indítja el.

2. A megbízó tartomány PDC-emulátora a TDO objektum *OldPassword* mezőjét az aktuális *ÚjJelszó* mezőre állítja be.

3. A megbízó tartomány PDC-emulátora a TDO objektum *ÚjJelszó* mezőjét az új jelszóra állítja be. Az előző jelszó másolatának megőrzése lehetővé teszi a régi jelszó visszavonását, ha a megbízható tartomány tartományvezérlője nem tudja fogadni a változást, vagy ha a módosítás nem replikálódik az új megbízhatósági jelszót használó kérelem előtt.

4. A megbízó tartomány PDC-emulátora távoli hívást kezdeményez a megbízható tartomány egyik tartományvezérlője számára, amely arra kéri, hogy állítsa be a jelszót a megbízhatósági fiókban az új jelszóra.

5. A megbízható tartomány tartományvezérlője módosítja a megbízhatósági jelszót az új jelszóra.

6. A rendszer a megbízhatóság mindkét oldalán replikálja a frissítéseket a tartomány többi tartományvezérlőjére. A meghatalmazó tartományban a változás a megbízható tartomány objektum sürgős replikálását indítja el.

A jelszó mostantól mindkét tartományvezérlőn módosul. A normál replikáció a TDO objektumokat a tartomány többi tartományvezérlőjére osztja el. Azonban lehetséges, hogy a tartományvezérlő a meghatalmazó tartományban a megbízható tartományban lévő tartományvezérlő sikeres frissítése nélkül módosítja a jelszót. Ez a forgatókönyv akkor fordulhat elő, ha a jelszó módosításának feldolgozásához szükséges biztonságos csatorna nem hozható létre. Előfordulhat, hogy a megbízható tartomány tartományvezérlője nem érhető el a folyamat egyes pontjain, és előfordulhat, hogy nem kapja meg a frissített jelszót.

Olyan helyzetekben, amikor a jelszó módosítása nem sikerült, a meghatalmazó tartomány tartományvezérlője soha nem módosítja az új jelszót, kivéve, ha sikeresen hitelesítette (biztonságos csatorna beállítása) az új jelszó használatával. Ennek a viselkedésnek az az oka, hogy a régi és az új jelszavak a megbízó tartomány TDO objektumában maradnak.

A jelszó módosítása nem fejeződött be, amíg a jelszóval való hitelesítés nem sikerül. A régi, tárolt jelszó a védett csatornán keresztül is használható, amíg a megbízható tartomány tartományvezérlője megkapja az új jelszót, így lehetővé téve a zavartalan szolgáltatást.

Ha az új jelszóval történő hitelesítés sikertelen, mert a jelszó érvénytelen, a megbízó tartományvezérlő a régi jelszóval kísérli meg a hitelesítést. Ha a régi jelszóval sikeresen hitelesíti magát, a jelszó-módosítási folyamatot 15 percen belül folytatja.

A megbízhatósági kapcsolatok jelszavas frissítéseinek 30 napon belül replikálni kell a megbízhatóság mindkét oldalának tartományvezérlőjére. Ha a megbízhatósági jelszó 30 nap után módosul, és a tartományvezérlőnek csak az N-2 jelszava van, akkor nem használhatja a megbízhatósági kapcsolatot a megbízó oldalról, és nem tud biztonságos csatornát létrehozni a megbízható oldalon.

## <a name="network-ports-used-by-trusts"></a>A Megbízhatóságok által használt hálózati portok

Mivel a megbízhatósági kapcsolatokat a különböző hálózati határokon belül kell telepíteni, előfordulhat, hogy egy vagy több tűzfalat kell kiterjedniük. Ebben az esetben vagy a tűzfalon keresztüli megbízhatósági kapcsolat, vagy a tűzfal adott portjainak megnyitása lehetővé teszi a forgalom továbbítását.

> [!IMPORTANT]
> A Active Directory Domain Services nem támogatja az RPC-forgalom adott portokra való korlátozását Active Directory.

Olvassa el a Microsoft ügyfélszolgálata cikk **Windows Server 2008-es és újabb verziói** című szakaszát, amely [bemutatja, hogyan konfigurálhat tűzfalat Active Directory tartományokhoz és megbízhatósági kapcsolatokhoz](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) , hogy megismerje az erdőszintű megbízhatósághoz szükséges portokat.

## <a name="supporting-services-and-tools"></a>Támogató szolgáltatások és eszközök

A megbízhatósági kapcsolatok és a hitelesítés támogatásához néhány további funkció és felügyeleti eszköz is használatos.

### <a name="net-logon"></a>Hálózati bejelentkezés

A Net Logon szolgáltatás biztonságos csatornát tart fenn egy Windows-alapú számítógépről a TARTOMÁNYVEZÉRLŐre. A megbízhatósággal kapcsolatos következő folyamatokban is használható:

* A megbízhatóság beállítása és a felügyelet – net-bejelentkezés segít megőrizni a megbízhatósági jelszavakat, összegyűjti a megbízhatósági adatokat, és ellenőrzi a megbízhatósági kapcsolatot az LSA-folyamattal és a TDO való interakcióval.

    Az erdőszintű megbízhatósági kapcsolatok esetében a megbízhatósági adatok tartalmazzák az erdőszintű megbízhatósági adatok (*FTInfo*) rekordját, amely magában foglalja a megbízható erdő által felügyelt jogcímek készletét, amely azt jelzi, hogy az egyes jogcímeket a megbízó erdő megbízhatónak tekinti.

* Hitelesítés – a felhasználó hitelesítő adatait egy biztonságos csatornán keresztül egy tartományvezérlőre továbbítja, és visszaadja a felhasználó tartományi biztonsági azonosítóit és felhasználói jogosultságait.

* Tartományvezérlő helye – segítséget nyújt a tartományvezérlők tartományon vagy tartományon belüli keresésében vagy keresésében.

* Átmenő ellenőrzés – a más tartományokban lévő felhasználók hitelesítő adatait a rendszer a net-bejelentkezéssel dolgozza fel. Ha egy megbízó tartománynak ellenőriznie kell a felhasználó identitását, a hitelesítő adatokat a megbízható tartományba való hálózati bejelentkezésen keresztül továbbítja ellenőrzésre.

* Jogosultsági attribútum tanúsítványa (PAC) ellenőrzése – ha a hitelesítéshez Kerberos protokollt használó kiszolgálónak ellenőriznie kell a PAC-t egy szolgáltatási jegyben, a PAC-t a biztonságos csatornán keresztül továbbítja a tartományvezérlőnek ellenőrzés céljából.

### <a name="local-security-authority"></a>Helyi biztonsági szervezet

A helyi biztonsági szervezet (LSA) egy védett alrendszer, amely a rendszer helyi biztonságának minden aspektusával kapcsolatos információt tárol. A helyi biztonsági házirend néven ismert, hogy az LSA különböző szolgáltatásokat biztosít a nevek és azonosítók közötti fordításhoz.

Az LSA biztonsági alrendszer kernel módban és felhasználói módban is biztosít szolgáltatásokat az objektumokhoz való hozzáférés ellenőrzéséhez, a felhasználói jogosultságok ellenőrzéséhez és a naplózási üzenetek létrehozásához. Az LSA feladata a megbízható vagy nem megbízható tartományokban lévő szolgáltatások által bemutatott összes munkamenet-jegy érvényességének ellenőrzése.

### <a name="management-tools"></a>Felügyeleti eszközök

A rendszergazdák *Active Directory tartományokat és megbízhatóságokat*, *Netdom* és *nltest* használhatják a megbízhatósági kapcsolatok elérhetővé tételéhez, létrehozásához, eltávolításához vagy módosításához.

* *Active Directory tartományok és megbízhatósági kapcsolatok* a Microsoft Management Console (MMC), amely a tartományi Megbízhatóságok, a tartomány és az erdő működési szintjeinek, valamint az egyszerű felhasználónevek utótagjának felügyeletére szolgál.
* A *Netdom* és a *nltest* parancssori eszközöket a megbízhatósági kapcsolatok kereséséhez, megjelenítéséhez, létrehozásához és kezeléséhez használhatja. Ezek az eszközök közvetlenül kommunikálnak az LSA-szolgáltatóval egy tartományvezérlőn.

## <a name="next-steps"></a>Következő lépések

További információ az erőforrás-erdőkről: [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

A felügyelt tartomány erőforrás-erdővel való létrehozásának első lépéseiért tekintse meg [Az Azure AD DS felügyelt tartomány létrehozása és konfigurálása][tutorial-create-advanced]című témakört. Ezután [létrehozhat egy kimenő erdőszintű megbízhatósági kapcsolatot a helyszíni tartományba][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
