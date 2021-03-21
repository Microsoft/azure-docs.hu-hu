---
title: A Azure AD Domain Services Felügyeleti fogalmai | Microsoft Docs
description: Útmutató Azure Active Directory Domain Services felügyelt tartomány felügyeletéhez, valamint a felhasználói fiókok és jelszavak működésének megismeréséhez
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: justinha
ms.openlocfilehash: 5473ef46751d64fdbbf1d52f39c66f49d707e615
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631387"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Felügyeleti fogalmak a felhasználói fiókokhoz, jelszavakhoz és felügyelethez Azure Active Directory Domain Services

Ha Azure Active Directory Domain Services (AD DS) felügyelt tartományt hoz létre és futtat, a viselkedés a hagyományos helyszíni AD DS környezethez képest valamilyen eltérést mutat. Ugyanazokat a felügyeleti eszközöket használja az Azure AD DS önálló felügyelt tartományként, de nem férhet hozzá közvetlenül a tartományvezérlőhöz (DC). A felhasználói fiókok létrehozásának forrásától függően a jelszóházirend és a jelszó-kivonatok viselkedése is fennáll.

Ez a fogalmi cikk részletesen ismerteti a felügyelt tartományok felügyeletét és a felhasználói fiókok különböző viselkedését a létrehozásuk módjától függően.

## <a name="domain-management"></a>Tartományi felügyelet

A felügyelt tartomány a DNS-névtér és a hozzá tartozó könyvtár. A felügyelt tartományokban az összes erőforrást (például a felhasználókat és a csoportokat, a hitelesítő adatokat és a szabályzatokat) tartalmazó tartományvezérlők (DCs-EK) a felügyelt szolgáltatás részét képezik. A redundancia érdekében a rendszer két tartományvezérlőt hoz létre egy felügyelt tartomány részeként. A felügyeleti feladatok elvégzéséhez nem lehet bejelentkezni a tartományvezérlőre. Ehelyett a felügyelt tartományhoz csatlakoztatott felügyeleti virtuális gépet kell létrehoznia, majd telepítenie kell a rendszeres AD DS felügyeleti eszközöket. Használhatja például a Active Directory felügyeleti központ vagy a Microsoft Management Console (MMC) beépülő modult, például a DNS-vagy Csoportházirend-objektumokat.

## <a name="user-account-creation"></a>Felhasználói fiók létrehozása

A felhasználói fiókok több módon is létrehozhatók felügyelt tartományokban. A legtöbb felhasználói fiók szinkronizálva van az Azure AD-ből, amely tartalmazhatja a helyszíni AD DS környezetből szinkronizált felhasználói fiókot is. A fiókokat manuálisan is létrehozhatja közvetlenül a felügyelt tartományban. Bizonyos funkciók, például a kezdeti jelszó-szinkronizálás vagy a jelszóházirend, eltérően viselkednek, attól függően, hogyan és hol jönnek létre a felhasználói fiókok.

* A felhasználói fiók szinkronizálható az Azure AD-ből. Ez magában foglalja a közvetlenül az Azure AD-ben létrehozott felhőalapú felhasználói fiókokat, valamint a helyi AD DS környezetből Azure AD Connect használatával szinkronizált hibrid felhasználói fiókokat.
    * A felügyelt tartományokban lévő felhasználói fiókok többségét az Azure AD szinkronizálási folyamata hozza létre.
* A felhasználói fiók manuálisan is létrehozható egy felügyelt tartományban, és nem létezik az Azure AD-ben.
    * Ha olyan alkalmazásokhoz kell szolgáltatásfiókot létrehoznia, amelyek csak a felügyelt tartományban futnak, manuálisan is létrehozhatja őket a felügyelt tartományban. Mivel a szinkronizálás az Azure AD egyik módja, a felügyelt tartományban létrehozott felhasználói fiókok nem szinkronizálhatók vissza az Azure AD-be.

## <a name="password-policy"></a>Jelszóházirend

Az Azure AD DS tartalmaz egy alapértelmezett jelszóházirend-szabályzatot, amely meghatározza a fiókok zárolási beállításait, a jelszavak maximális élettartamát és a jelszó bonyolultságát. Az olyan beállítások, mint a fiókzárolási házirend a felügyelt tartomány összes felhasználójára érvényesek, függetlenül attól, hogy a felhasználó hogyan lett létrehozva az előző szakaszban leírt módon. Néhány beállítás, például a jelszó minimális hossza és a jelszó bonyolultsága, csak a felügyelt tartományokban közvetlenül létrehozott felhasználókra érvényesek.

A felügyelt tartományokban lévő alapértelmezett házirend felülbírálásához létrehozhat saját egyéni jelszóházirend-szabályzatokat is. Ezeket az egyéni házirendeket ezután a felhasználók adott csoportjaira lehet alkalmazni, igény szerint.

A jelszó-szabályzatok felhasználói létrehozási forrástól függően történő alkalmazásával kapcsolatos további információkért lásd: [jelszó-és fiókzárolási házirendek a felügyelt tartományokon][password-policy].

## <a name="password-hashes"></a>Jelszó-kivonatok

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS a jelszó-kivonatokat az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban kell megadni. Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak módosítaniuk kell a jelszavukat, mielőtt használhatják a felügyelt tartományt. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához. A fiók nincs szinkronizálva az Azure AD-ből az Azure AD DSig, amíg meg nem változtatják a jelszót.

A helyszíni AD DS környezetből Azure AD Connect használatával szinkronizált felhasználók számára [engedélyezze a jelszó-kivonatok szinkronizálását][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect csak a régi jelszavakat szinkronizálja, amikor engedélyezi az Azure AD DSt az Azure AD-bérlő számára. A rendszer nem használja az örökölt jelszavas kivonatokat, ha a helyszíni AD DS-környezet Azure AD-vel való szinkronizálásához csak Azure AD Connectt használ.
>
> Ha az örökölt alkalmazásai nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, javasoljuk, hogy tiltsa le az NTLM-jelszó kivonatának szinkronizálását az Azure AD DS. További információ: a [gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása][secure-domain].

A megfelelő konfigurálást követően a rendszer a használható jelszavak kivonatait a felügyelt tartományban tárolja. Ha törli a felügyelt tartományt, az ezen a ponton tárolt jelszó-kivonatokat is törli a rendszer. A szinkronizált hitelesítő adatokat az Azure AD-ben nem lehet újra felhasználni, ha később egy másik felügyelt tartományt hoz létre – újra kell konfigurálnia a jelszó-kivonat szinkronizálását, hogy a rendszer újra tárolja a jelszó-kivonatokat. Korábban a tartományhoz csatlakoztatott virtuális gépek vagy felhasználók nem fognak tudni azonnal hitelesíteni magukat – az Azure AD-nek a jelszó-kivonatokat az új felügyelt tartományban kell kiállítania és tárolnia. További információ: jelszó- [kivonatolási szinkronizálási folyamat az Azure AD DS és Azure ad Connect][azure-ad-password-sync].

> [!IMPORTANT]
> A Azure AD Connect csak a helyszíni AD DS környezetekkel való szinkronizálásra kell telepíteni és konfigurálni. A felügyelt tartományokban való Azure AD Connect telepítése nem támogatott az objektumok Azure AD-be való visszaszinkronizálásához.

## <a name="forests-and-trusts"></a>Erdők és megbízhatóságok

Az *erdők* Active Directory Domain Services (AD DS) által használt logikai szerkezetek egy vagy több *tartomány* csoportosításához. A tartományok ezután objektumokat tárolnak a felhasználók vagy csoportok számára, és biztosítják a hitelesítési szolgáltatásokat.

Az Azure AD DSban az erdő csak egy tartományt tartalmaz. A helyszíni AD DS erdők gyakran sok tartományt tartalmaznak. A nagyméretű szervezeteknél – különösen az összevonások és a beszerzések után – előfordulhat, hogy több helyszíni erdővel is rendelkezik, amelyek mindegyike több tartományt tartalmaz.

Alapértelmezés szerint a felügyelt tartomány *felhasználói* erdőként jön létre. Ez a típusú erdő az Azure AD összes objektumát szinkronizálja, beleértve a helyszíni AD DS környezetben létrehozott felhasználói fiókokat is. A felhasználói fiókok közvetlenül hitelesíthetők a felügyelt tartományon, például bejelentkezhetnek egy tartományhoz csatlakoztatott virtuális gépre. A felhasználói erdő akkor működik, ha a jelszó-kivonatok szinkronizálhatók, és a felhasználók nem használnak exkluzív bejelentkezési módszereket, például az intelligens kártyás hitelesítést.

Egy Azure-AD DS *erőforrás* -erdőben a felhasználók egy egyirányú erdőszintű *megbízhatósági kapcsolaton* keresztül hitelesíthetők a helyszíni AD DS. Ezzel a módszerrel a felhasználói objektumok és jelszavak kivonatai nem szinkronizálhatók az Azure AD DS. A felhasználói objektumok és a hitelesítő adatok csak a helyszíni AD DSban találhatók. Ez a megközelítés lehetővé teszi, hogy a vállalatok az Azure-ban olyan erőforrásokat és alkalmazás-platformokat működtessenek, amelyek a klasszikus hitelesítéstől (például LDAPs, Kerberos vagy NTLM) függenek, de a hitelesítési problémák és a problémák el

További információ az Azure AD DS erdők típusairól: Mik az [erőforrás-erdők?][concepts-forest] és [hogyan működnek az erdőszintű megbízhatósági kapcsolatok az Azure ad DSban?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU-i

Az Azure AD DS a rendelkezésre álló teljesítmény és szolgáltatások az SKU-on alapulnak. A felügyelt tartomány létrehozásakor ki kell választania egy SKU-t, és az üzleti követelmények változása után átválthat a SKU-ra a felügyelt tartomány telepítése után. Az alábbi táblázat a rendelkezésre álló SKU-ket és a köztük lévő különbségeket ismerteti:

| SKU neve   | Objektumok maximális száma | Biztonsági mentés gyakorisága | Kimenő erdőszintű Megbízhatóságok maximális száma |
|------------|----------------------|------------------|----|
| Standard   | Korlátlan            | 5 naponta     | 0  |
| Enterprise | Korlátlan            | 3 naponta     | 5  |
| Prémium    | Korlátlan            | Napi            | 10 |

Ezeknek az Azure AD DS SKU-nak a megkezdése előtt a felügyelt tartomány objektumainak (felhasználói és számítógépfiókok) számán alapuló számlázási modell volt használatban. A felügyelt tartomány objektumainak száma alapján már nem változik a díjszabás.

További információkért tekintse meg az [Azure AD DS díjszabását ismertető oldalt][pricing].

### <a name="managed-domain-performance"></a>Felügyelt tartomány teljesítménye

A tartomány teljesítménye attól függően változik, hogy a hitelesítés Hogyan valósul meg az alkalmazáshoz. A további számítási erőforrások segíthetnek a lekérdezési válaszidő javításában és a szinkronizálási műveletekben eltöltött idő csökkentésében. Az SKU szintjének növekedésével a felügyelt tartomány számára elérhető számítási erőforrások megnövekednek. Figyelje az alkalmazások teljesítményét, és tervezze meg a szükséges erőforrásokat.

Ha a vállalat vagy az alkalmazás megváltoztatja a változást, és további számítási teljesítményre van szüksége a felügyelt tartományhoz, átválthat egy másik SKU-ra.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

A biztonsági mentés gyakorisága határozza meg, hogy a rendszer milyen gyakran készítsen pillanatképet a felügyelt tartományról. A biztonsági mentések az Azure platform által felügyelt automatizált folyamatok. Ha a felügyelt tartománnyal kapcsolatos probléma merül fel, az Azure-támogatás segíthet a biztonsági mentésből való visszaállításban. Mivel a szinkronizálás csak *az* Azure ad egyik módja, a felügyelt tartományokban előforduló problémák nem befolyásolják az Azure ad-t vagy a helyszíni AD DS környezeteket és funkciókat.

Az SKU szintjének növekedésével a biztonsági mentési Pillanatképek gyakorisága növekszik. A felügyelt tartomány szükséges biztonsági mentési gyakoriságának meghatározásához tekintse át az üzleti követelmények és a helyreállítási pont célkitűzését (RPO). Ha az üzleti vagy az alkalmazásra vonatkozó követelmények változnak, és több gyakori biztonsági mentésre van szüksége, átválthat egy másik SKU-ra.

### <a name="outbound-forest-trusts"></a>Kimenő erdőszintű megbízhatósági kapcsolatok

Az előző szakaszban az egyirányú kimenő erdő megbízhatónak tekinti a felügyelt tartományból egy helyszíni AD DS környezetet. Az SKU meghatározza a felügyelt tartományhoz létrehozható erdőszintű megbízhatósági kapcsolatok maximális számát. Tekintse át az üzleti és az alkalmazásra vonatkozó követelményeket, hogy meghatározza, hány megbízhatónak kell lennie, és válassza ki a megfelelő Azure AD DS SKU-t. Ha az üzleti követelmények megváltoznak, és további erdőszintű megbízhatósági kapcsolatokat kell létrehoznia, váltson át egy másik SKU-ra.

## <a name="next-steps"></a>Következő lépések

Első lépésként [hozzon létre egy Azure AD DS felügyelt tartományt][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
