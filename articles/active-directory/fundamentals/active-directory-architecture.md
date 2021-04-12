---
title: Architektúra áttekintése – Azure Active Directory | Microsoft Docs
description: Megtudhatja, mi az Azure Active Directory bérlő, és hogyan kezelheti az Azure-t a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476cf8013f5dc8b5d54efb573cf305d81fc690b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996712"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Mi a Azure Active Directory architektúra?

Az Azure Active Directory (Azure AD) lehetővé teszi, hogy biztonságosan kezelje az Azure-szolgáltatások és -erőforrások elérését a felhasználók számára. Az Azure AD-ben megtalálható az identitáskezelési megoldások teljes palettája. Az Azure AD-funkciókkal kapcsolatos információért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md)

Az Azure AD-vel felhasználókat és csoportokat hozhat létre és kezelhet, valamint engedélyeket adhat meg a vállalati erőforrások hozzáférésének engedélyezéséhez és megtagadásához. Az identitáskezeléssel kapcsolatos információért tekintse meg az [Azure-identitáskezelés alapjait leíró szakaszt](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Azure AD-architektúra

Az Azure AD földrajzilag elosztott architektúrája ötvözi a széleskörű monitorozást, az automatikus átirányítást, a feladatátvételt és a helyreállítási képességeket, amelyek a vállalati szintű rendelkezésre állást és teljesítményt biztosítják az ügyfeleknek.

Ez a cikk a következő architektúraelemeket tárgyalja:

*   Szolgáltatásarchitektúra kialakítása
*   Méretezhetőség
*   Folyamatos rendelkezésre állás
*   Adatközpontok

### <a name="service-architecture-design"></a>Szolgáltatásarchitektúra kialakítása

Az elérhető és használható, adatgazdag rendszerek létrehozásának leggyakoribb módja a független építőelemek vagy skálázási egységek használata. Az Azure AD adatszintjéhez a skálázási egységeket *partícióknak* nevezzük.

Az adatréteg több front-end szolgáltatással rendelkezik, amelyek olvasási és írási képességeket nyújtanak. Az alábbi ábra azt mutatja be, hogyan történik egy egykönyvtáros partíció összetevőinek terjesztése a földrajzilag elosztott adatközpontokban.

  ![Egykönyvtáras partíciós diagram](./media/active-directory-architecture/active-directory-architecture.png)

Az Azure AD architektúra összetevői között egy elsődleges replika és másodlagos replikák szerepelnek.

#### <a name="primary-replica"></a>Elsődleges replika

Az *elsődleges replika* fogadja azon partíció összes *írását*, amelyhez tartozik. Minden írási művelet azonnal replikálódik egy másik adatközpontban lévő másodlagos replikára, mielőtt a rendszer sikert jelezne a hívónak. Ezáltal biztosítható az írások georedundáns tartóssága.

#### <a name="secondary-replicas"></a>Másodlagos replikák

Az összes címtárbeli *olvasás* a *másodlagos replikák* szolgáltatásból származik, amelyek olyan adatközpontokban találhatók, amelyek fizikailag a különböző földrajzi régiókban találhatók. Sok másodlagos replika van, mivel az adatok replikálása aszinkron módon történik. A címtárbeli olvasások, például a hitelesítési kérelmek olyan adatközpontokban vannak kiszolgálva, amelyek az ügyfelekhez vannak közelebb. A másodlagos replikák felelősek az olvasás méretezhetőségéért.

### <a name="scalability"></a>Méretezhetőség

A méretezhetőség azon képesség, amellyel a szolgáltatások ki tudnak bővülni a növekvő teljesítményigények kielégítése érdekében. Az írás méretezhetősége az adatok particionálásával érhető el. Az olvasás méretezhetősége az adatok egy partícióról több, a világ számos részén elosztott másodlagos replikákra történő replikálásával érhető el.

A címtárbeli alkalmazásoktól érkező kérelmeket az adatközponthoz irányítja a rendszer, amely fizikailag a legközelebb van. Az írások transzparens módon vannak átirányítva az elsődleges replikához az írás-olvasás konzisztenciájának biztosítása érdekében. A másodlagos replikák jelentősen kibővítik a partíciók méretét, mert a címtárak leginkább olvasásokat szolgáltatnak.

A címtáralkalmazások a legközelebbi adatközpontokhoz csatlakoznak. Ez a hálózat javítja a teljesítményt, így a horizontális felskálázás is lehetséges. Mivel egy címtárpartíció sok másodlagos replikával rendelkezhet, a másodlagos replikák közelebb helyezhetők a címtár ügyfeleihez. Csak a belső címtárszolgáltatás írásigényes összetevői célozzák meg közvetlenül az aktív elsődleges replikát.

### <a name="continuous-availability"></a>Folyamatos rendelkezésre állás

A rendelkezésre állás (vagy üzemidő) határozza meg, hogy egy rendszer mennyire tud zavartalanul működni. Az Azure AD magas rendelkezésre állásának kulcsa az, hogy a szolgáltatások gyorsan áthelyezhetik a forgalmat több földrajzilag elosztott adatközpont között. Az egyes adatközpontok függetlenek, ami lehetővé teszi a dekorrelált meghibásodási módokat. Ezen a magas rendelkezésre állású kialakításon keresztül az Azure AD nem igényel állásidőt a karbantartási tevékenységekhez.

Az Azure AD partíciós kialakítását egyszerűsítettük a nagyvállalati szintű AD-kialakításhoz képest, egyetlen főkiszolgálós kialakítással, amely gondosan előkészített és determinisztikus elsődleges replika feladatátvételi folyamatot tartalmaz.

#### <a name="fault-tolerance"></a>Hibatűrés

A rendszereknek jobb a rendelkezésre állása, ha képesek tűrni a hardveres, hálózati és szoftverhibákat. A címtárban lévő összes partícióhoz tartozik egy magas rendelkezésre állású főreplika. Ez az elsődleges replika. Ezen a replikán csak a partíció írása történik. Ez a replika folyamatos megfigyelés alatt áll, és az írások azonnal egy másik replikára helyezhetők (amely az új elsődleges replikává válik), ha a rendszer hibát észlel. Feladatátvétel esetén általában 1-2 percig megszűnhet az írás rendelkezésre állása. Ez nincs hatással az olvasás rendelkezésre állására.

Az olvasási műveletek (amelyek száma nagyságrendekkel meghaladja az írásokét) csak a másodlagos replikákhoz érkeznek. Mivel a másodlagos replikák idempotensek, egy adott partíció bármely replikájának elvesztése könnyedén orvosolható az olvasások egy másik replikára történő irányításával, általában ugyanabban az adatközpontban.

#### <a name="data-durability"></a>Adatok tartóssága

Az írás a tartósan előtt legalább két adatközponthoz van véglegesítve. Ez először véglegesíti az írást az elsődlegesen, majd azonnal replikálja az írást legalább egy másik adatközpontba. Ez az írási művelet biztosítja, hogy az elsődlegesként szolgáló adatközpont potenciális katasztrofális elvesztése ne okozza az adatvesztést.

Az Azure AD egy nulla [helyreállítási időre vonatkozó célkitűzést (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) tart fenn, hogy ne veszítse el a feladatátvételsel kapcsolatos adatvesztést. Ide tartoznak az alábbiak:

* Jogkivonat-kiállítási és könyvtár-olvasások
* Csak körülbelül 5 perces RTO engedélyezése a címtárbeli írásokhoz

### <a name="datacenters"></a>Adatközpontok

Az Azure AD replikái a világ számos részén található adatközpontokban vannak tárolva. További információ: [Azure globális infrastruktúra](https://azure.microsoft.com/global-infrastructure/).

Az Azure AD az alábbi jellemzőkkel rendelkezik az adatközpontok között:

* A hitelesítés, a gráf és az egyéb AD-szolgáltatások az átjáró szolgáltatás mögött találhatók. Az átjáró kezeli ezen szolgáltatások terheléselosztását. A művelet automatikusan átadja a feladatátvételt, ha a rendszer nem kifogástalan állapotú kiszolgálókat észlel a tranzakciós állapot-tesztek használatával. Ezen állapot-tesztek alapján az átjáró dinamikusan irányítja át a forgalmat az egészséges adatközpontokhoz.
* *Olvasás* esetén a címtár másodlagos replikákkal és megfelelő előtér-szolgáltatásokkal rendelkezik, amelyek több adatközpontban működő aktív-aktív konfigurációban működnek. Egy teljes adatközpont meghibásodása esetén a rendszer automatikusan átirányítja a forgalmat egy másik adatközpontba.
 * Az *írások* esetében a címtár az elsődleges (fő) replikát az adatközpontok között tervezett (az új elsődleges szinkronizálása a régi elsődleges) vagy a vészhelyzeti feladatátvételi eljárások segítségével végzi. Az adattartósságot úgy érheti el, hogy legalább két adatközpontba replikálja a végrehajtást.

#### <a name="data-consistency"></a>Adatkonzisztencia

A címtár-modell a végső konzisztencia egyike. Az elosztott aszinkron replikálási rendszerek egyik tipikus problémája, hogy az "adott" replika által visszaadott adatok nem naprakészek. 

Az Azure AD-ben a másodlagos replikát megcélzó alkalmazások  olvasási és írási konzisztenciája úgy valósul meg, hogy az írásokat a rendszer az elsődleges replikára irányítja, és ezzel egyidejűleg visszahúzza azokat a másodlagos replikára.

Az Azure AD Microsoft Graph API-ját használó alkalmazások írása absztrakt marad a címtár-replika számára az írási-olvasási konzisztencia érdekében. A Microsoft Graph API szolgáltatás egy logikai munkamenetet tart fenn, amely affinitással rendelkezik az olvasáshoz használt másodlagos replikához. Az affinitás "replika jogkivonatban" van rögzítve, amelyet a szolgáltatás a másodlagos replika adatközpontjában lévő elosztott gyorsítótár használatával gyorsítótáraz. A jogkivonat ezután a logikai munkamenet következő műveleteihez lesz felhasználva. Ha továbbra is ugyanazt a logikai munkamenetet szeretné használni, akkor a későbbi kérelmeket ugyanahhoz az Azure AD-adatközponthoz kell irányítani. Ha a címtár-ügyfél kérései több Azure AD-adatközpontba vannak irányítva, nem lehet folytatni a logikai munkamenetet. Ha ez történik, az ügyfél több olyan logikai munkamenettel rendelkezik, amelyeknek független írási és olvasási konzisztencia van.

 >[!NOTE]
 >Az írásokat a rendszer azonnal replikálja arra a másodlagos replikára, amelyre a logikai munkamenet olvasásai ki lettek adva.

#### <a name="backup-protection"></a>Biztonsági másolatok védelme

A címtár a végleges törlés helyett helyreállítható törlést valósít meg a felhasználók és bérlők számára, amely lehetővé teszi az egyszerű helyreállítást, ha az ügyfél esetleg véletlenül törölne valamit. Ha a bérlői rendszergazda véletlenül törli a felhasználókat, egyszerűen visszavonhatja és visszaállíthatja a törölt felhasználókat.

Az Azure AD naponta biztonsági másolatot készít az összes adatról, ezért képes az adatok mérvadó visszaállítására bármilyen logikai törlés vagy sérülés esetében. Az adatcsomag hibát alkalmaz a kódok kijavításakor, így az képes a hibák keresésére és a lemezes hibák automatikus kijavítására.

#### <a name="metrics-and-monitors"></a>Mérőszámok és figyelők

Egy magas rendelkezésre állású szolgáltatás futtatásához világszínvonalú metrikára és monitorozási képességekre van szükség. Az Azure AD folyamatosan elemzi a szolgáltatások állapotával kapcsolatos legfontosabb mérőszámokat és az egyes szolgáltatások sikerességi feltételeit, illetve jelentést készít azokról. A metrikák, a monitorozás és a riasztások folyamatos fejlesztése és finomhangolása minden egyes Azure AD-szolgáltatáson belül és az összes szolgáltatás között megtörténik.

Ha bármely Azure AD-szolgáltatás nem a várt módon működik, a lehető leggyorsabban végrehajtja a működés helyreállítását. A legfontosabb mérőszám az Azure AD nyomon követése, hogy milyen gyorsan észlelhetők és csökkenthetők az ügyfelek az élő helyekkel kapcsolatos problémák. Komoly erőfeszítéseket teszünk a monitoring és a riasztások területén azért, hogy minimalizáljuk az észleléshez szükséges időt (cél: <5 perc). A működőképesség javításával pedig a hiba súlyosságának mérsékléséhez szükséges időt igyekszünk csökkenteni (cél: <30 perc).

#### <a name="secure-operations"></a>Biztonságos műveletek

Az operatív vezérlők, például a többtényezős hitelesítés (MFA) használata bármely művelethez, valamint az összes művelet naplózása. Emellett egy igény szerinti jogosultságszint-emelési rendszer használatával biztosíthatja a szükséges ideiglenes hozzáférést bármely operatív feladathoz, folyamatosan. További információkért lásd: [A megbízható felhő](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Következő lépések

[Az Azure Active Directory fejlesztői útmutatója](../develop/index.yml)