---
title: Jelszavas védelem a Azure Active Directoryban
description: Ismerje meg, hogyan lehet dinamikusan betiltani a környezet gyenge jelszavait Azure Active Directory jelszavas védelemmel
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 740201b84323f5a01ab01bb21ec458a19832e499
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652020"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Helytelen jelszavak eltávolítása Azure Active Directory jelszavas védelem használatával

Számos biztonsági útmutatás azt ajánlja, hogy ne használja ugyanazt a jelszót több helyen, hogy az összetett legyen, és elkerülje az egyszerű jelszavakat, például a *Password123*. A felhasználók számára [útmutatást adhat a jelszavak kiválasztásához](https://www.microsoft.com/research/publication/password-guidance), azonban a gyenge vagy nem biztonságos jelszavakat gyakran használják. Az Azure AD jelszavas védelme észleli és blokkolja az ismert gyenge jelszavakat és azok változatait, valamint blokkolhatja a szervezete számára jellemző további gyenge kifejezéseket is.

Az Azure AD jelszavas védelme esetén az alapértelmezett globális tiltott jelszavak listáját a rendszer automatikusan alkalmazza az Azure AD-bérlő összes felhasználója számára. Saját üzleti és biztonsági igényeinek támogatásához megadhat bejegyzéseket egy egyéni tiltott jelszavak listájában. Ha a felhasználók megváltoztatják vagy alaphelyzetbe állítják a jelszavukat, a rendszer ellenőrzi, hogy az erős jelszavak használata megtörténik-e.

Használjon olyan további szolgáltatásokat, mint az [Azure ad multi-Factor Authentication](concept-mfa-howitworks.md), nem csak az Azure ad jelszavas védelem által kényszerített erős jelszavakat használja. További információ a bejelentkezési események több rétegének használatáról: [a PA $ $Word nem számít](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Ez a fogalmi cikk azt ismerteti, hogyan működik az Azure AD jelszavas védelem a rendszergazda számára. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a következőre: [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

## <a name="global-banned-password-list"></a>Globális tiltott jelszavak listája

A Azure AD Identity Protection csapat folyamatosan elemzi az Azure AD biztonsági telemetria az általánosan használt gyenge vagy sérült jelszavakat keresve. Pontosabban, az elemzés olyan alapfogalmakat keres, amelyek gyakran használatosak a gyenge jelszavak alapjaként. Ha gyenge feltételek találhatók, azok hozzáadódnak a *globálisan tiltott jelszavak listájához*. A globálisan tiltott jelszavak listájának tartalma nem a külső adatforrásokon alapul, hanem az Azure AD biztonsági telemetria és elemzésének eredményein.

Ha egy Azure AD-bérlő bármelyik felhasználója módosít vagy visszaállít egy jelszót, a rendszer a globálisan tiltott jelszavak aktuális verzióját használja a jelszó erősségének ellenőrzéséhez. Ez az ellenőrzési ellenőrzés az összes Azure AD-ügyfél esetében erősebb jelszavakat eredményez.

A rendszer automatikusan alkalmazza a globálisan tiltott jelszavak listáját egy Azure AD-bérlő összes felhasználójára. Nincs lehetőség az engedélyezéshez vagy a konfiguráláshoz, és nem lehet letiltani. A rendszer a globálisan tiltott jelszavak listáját alkalmazza a felhasználókra, amikor az Azure AD-n keresztül módosítják vagy visszaállítják saját jelszavukat.

> [!NOTE]
> A Cyber-bűnözők a támadások során hasonló stratégiákat is használnak a gyakori gyenge jelszavak és változatok azonosítására. A biztonság növelése érdekében a Microsoft nem teszi közzé a globálisan tiltott jelszavak listájának tartalmát.

## <a name="custom-banned-password-list"></a>Egyéni tiltott jelszavak listája

Egyes szervezetek szeretnék javítani a biztonságot, és saját testreszabásokat hozzáadni a globálisan tiltott jelszavak listájához. Saját bejegyzések hozzáadásához használhatja az *Egyéni tiltott jelszavak listáját*. Az egyéni tiltott jelszavak listájához hozzáadott feltételeket a szervezeti feltételekre kell összpontosítani, például az alábbi példákra:

- Márkanevek
- Terméknév
- Helyek, például a vállalati központ
- Vállalatra vonatkozó belső feltételek
- Adott céges jelentéssel rendelkező rövidítések

Ha a feltételek hozzáadódnak az egyéni tiltott jelszavak listájához, azok a globálisan tiltott jelszavak listájában szereplő feltételekkel együtt jelennek meg. A jelszó módosítása vagy az alaphelyzetbe állítási események ezután a betiltott jelszavak összesített készletében lesznek érvényesítve.

> [!NOTE]
> Az egyéni tiltott jelszavak listája legfeljebb 1000 feltételre korlátozódik. A rendszer nem blokkolja a jelszavak rendkívül nagy listáját.
>
> Annak érdekében, hogy teljes mértékben kihasználja az egyéni tiltott jelszavak listájának előnyeit, először is megismerheti, [Hogyan történik a jelszavak kiértékelése](#how-are-passwords-evaluated) , mielőtt felveszi a feltételeket az egyéni tiltott listára. Ezzel a módszerrel hatékonyan észlelhetők és letilthatók nagy számú gyenge jelszó és azok változatai.

![Az egyéni tiltott jelszavak listájának módosítása a hitelesítési módszerek területen](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Vegyünk egy *contoso* nevű ügyfelet. A vállalat székhelye Londonban található, és egy *widget* nevű terméket tesz elérhetővé. Ebben a példában az ügyfél számára a felesleges és kevésbé biztonságos megoldás a jelen feltételek adott változatának letiltására, például a következőkre:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

Ehelyett sokkal hatékonyabb és biztonságos, hogy csak a legfontosabb alapfogalmakat blokkolja, például az alábbi példákat:

- "Contoso"
- London
- Widget

A jelszó-ellenőrzési algoritmus ezután automatikusan blokkolja a gyenge változatokat és a kombinációkat.

Az egyéni tiltott jelszavak listájának használatának megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni tiltott jelszavak konfigurálása](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Jelszavas spray-támadások és a harmadik féltől származó feltört jelszavak listája

Az Azure AD jelszavas védelme segít megvédeni a jelszó-szórásos támadásokat. A legtöbb jelszavas szórásos támadás nem próbál meg több alkalommal megtámadni egy adott egyéni fiókot. Ez a viselkedés növelheti az észlelés valószínűségét, akár fiókzárolási, akár más módon.

Ehelyett a jelszó-kitörések többsége csak kis mennyiségű ismert leggyengébb jelszót küld el egy vállalat minden fiókján. Ez a módszer lehetővé teszi, hogy a támadó gyorsan keressen egy könnyen feltört fiókot, és elkerülje a lehetséges észlelési küszöbértékeket.

Az Azure AD jelszavas védelme hatékonyan blokkolja a jelszó-szórásos támadásokban valószínűleg használt összes ismert gyenge jelszót. Ez a védelem az Azure AD-ből származó valós biztonsági telemetria-adatokon alapul, és felépíti a globálisan tiltott jelszavak listáját.

Vannak olyan külső webhelyek, amelyek több millió jelszót is felhasználtak, amelyek a korábban nyilvánosan ismert biztonsági rések miatt sérültek. Gyakori, hogy a harmadik féltől származó jelszó-ellenőrzési termékek a találgatásos összehasonlításon alapulnak a több millió jelszóval. Ezek a technikák azonban nem a legjobb módszer, ha a jelszó-visszatámadók által használt tipikus stratégiák alapján javítják a teljes jelszó erősségét.

> [!NOTE]
> A globálisan tiltott jelszavak listája nem a harmadik féltől származó adatforrásokon alapul, beleértve a feltört jelszavak listáját is.

Bár a globálisan tiltott lista kicsi a harmadik féltől származó tömeges listákkal összehasonlítva, a tényleges jelszó-és telemetria származó valós biztonsági rendszerből származik. Ez a megközelítés javítja a teljes biztonságot és hatékonyságot, és a jelszó-ellenőrzési algoritmus intelligens, fuzzy-egyeztetési technikákat is használ. Ennek eredményeképpen az Azure AD jelszavas védelme hatékonyan észleli és blokkolja a leggyakoribb gyenge jelszavakat a vállalatnál.

## <a name="on-premises-hybrid-scenarios"></a>Helyszíni hibrid forgatókönyvek

Számos szervezet rendelkezik olyan hibrid identitási modellel, amely helyszíni Active Directory Domain Services (AD DS) környezeteket is tartalmaz. Az Azure AD jelszavas védelem biztonsági előnyeinek a AD DS-környezetbe való kiterjesztéséhez a helyszíni kiszolgálókon is telepíthet összetevőket. Ezeknek az ügynököknek a helyi AD DS környezetében jelszó-módosítási eseményeket kell megkövetelni ahhoz, hogy megfeleljenek az Azure AD-ben használt jelszóházirend követelményeinek.

További információ: [Az Azure ad jelszavas védelemének betartatása AD DS](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>A jelszavak kiértékelésének módja

Amikor egy felhasználó megváltoztatja vagy visszaállítja a jelszavát, az új jelszót a rendszer a globális és az egyéni tiltott jelszavak listájában lévő kifejezések összesített listájával ellenőrzi.

Akkor is elfogadhatja a jelszót, ha a felhasználó jelszava tiltott jelszót tartalmaz, ha a teljes jelszó egyébként elég erős. Az újonnan konfigurált jelszavak az alábbi lépésekkel mérik fel az általános erősségét annak megállapítására, hogy el kell-e fogadni vagy el kell-e utasítani:

### <a name="step-1-normalization"></a>1. lépés: normalizálás

Az új jelszó először a normalizálás folyamatán halad át. Ez a módszer lehetővé teszi, hogy a tiltott jelszavak kisebb halmaza legyen leképezve a potenciálisan gyenge jelszavak sokkal nagyobb készletére.

A normalizálás a következő két részből áll:

* Az összes nagybetűt kisbetűsre változtatja.
* Ezt követően a rendszer a közös karakterek helyettesítését hajtja végre, például a következő példában:

   | Eredeti levél | Helyettesített betű |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Tekintse meg a következő példát:

* Az "üres" jelszó tiltott.
* A felhasználó megpróbálja módosítani a jelszavát a következőre: " Bl@nK ".
* Bár a " Bl@nk " nem tiltott, a normalizálás folyamata ezt a jelszót "üres" értékre alakítja át.
* A jelszót a rendszer elutasítja.

### <a name="step-2-check-if-password-is-considered-banned"></a>2. lépés: Ellenőrizze, hogy a jelszó betiltottnak minősül-e

Ezután megtörténik a jelszó megvizsgálása más egyezési viselkedés esetén, és a rendszer egy pontszámot generál. Ez a végső pontszám meghatározza, hogy elfogadják vagy elutasították-e a jelszó módosítására vonatkozó kérést.

#### <a name="fuzzy-matching-behavior"></a>Homályos egyezési viselkedés

A rendszer a normalizált jelszóval azonosítja a fuzzy megfeleltetést, hogy az tartalmazza-e a globális vagy az egyéni tiltott jelszavak listáján található jelszót. A megfeleltetési folyamat egy (1) összehasonlítási távolságon alapul.

Tekintse meg a következő példát:

* A "ABCDEF" jelszó tiltott.
* A felhasználók a következők egyikére próbálják módosítani a jelszavukat:

   * "abcdeg" – az *utolsó karakter módosult "f" értékről "g* "-re
   * "abcdefg" – *"g" Hozzáfűzés vége*
   * "ABCDE" – *az "f" záró törlés vége*

* A fenti jelszavak mindegyike nem felel meg kifejezetten a betiltott "ABCDEF" jelszónak.

    Mivel azonban mindegyik példa a "ABCDEF" betiltott kifejezés 1. részén található, a "ABCDEF" értéknek megfelelőnek számít.
* Ezeket a jelszavakat a rendszer elutasítja.

#### <a name="substring-matching-on-specific-terms"></a>Alkarakterlánc megfeleltetése (adott kifejezéseken)

A normalizált jelszóval egyező alkarakterláncot kell használni a felhasználó utónevét és vezetéknevét, valamint a bérlő nevét. A bérlői név egyeztetése nem történik meg egy AD DS tartományvezérlőn a helyi hibrid forgatókönyvek jelszavának ellenőrzésekor.

> [!IMPORTANT]
> Az alkarakterlánc megfeleltetése csak a nevekre és más kifejezésekre érvényes, amelyek legalább négy karakter hosszúak.

Tekintse meg a következő példát:

* Egy lekérdezés nevű felhasználó, aki szeretné visszaállítani a jelszavát a "p0LL23fb" értékre.
* A normalizálás után ez a jelszó "poll23fb" lesz.
* A karakterláncok egyeztetése azt észleli, hogy a jelszó tartalmazza a felhasználó utónevét (lekérdezés).
* Annak ellenére, hogy a "poll23fb" nem volt kifejezetten a tiltott jelszavak listáján, a rendszer a jelszóban szereplő "lekérdezés" karakterláncot tartalmazza.
* A jelszót a rendszer elutasítja.

#### <a name="score-calculation"></a>Pontszám kiszámítása

A következő lépés a betiltott jelszavak összes példányának azonosítása a felhasználó normalizált új jelszavában. A pontok hozzárendelése a következő feltételek alapján történik:

1. A felhasználó jelszavában található összes betiltott jelszó egy pontot kap.
1. A betiltott jelszónak nem részét képező többi karakter egy pontot kap.
1. A jelszónak legalább öt (5) pontot el kell fogadnia.

A következő két forgatókönyv esetében a contoso az Azure AD jelszavas védelmet használja, és a "contoso" szerepel az egyéni tiltott jelszavak listáján. Azt is feltételezzük, hogy az "üres" a globális listán található.

A következő példában a felhasználó megváltoztatja a jelszavát a "C0ntos0Blank12" értékre:

* A normalizálás után a jelszó "contosoblank12" lesz.
* A megfelelő folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: "contoso" és "blank".
* Ezt a jelszót a következő pontszám adja meg:

    *[contoso] + [blank] + [1] + [2] = 4 pont*

* Mivel ez a jelszó öt (5) pont alatt van, elutasításra kerül.

Nézzük meg egy kissé eltérő példát, amely azt szemlélteti, hogy a jelszó további bonyolultsága hogyan építheti fel a szükséges számú pontot. A következő példában a felhasználó a jelszót a "!" értékre módosítja ContoS0Bl@nkf9 :

* A normalizálás után ez a jelszó "contosoblankf9!" lesz.
* A megfelelő folyamat megállapítja, hogy ez a jelszó két tiltott jelszót tartalmaz: "contoso" és "blank".
* Ezt a jelszót a következő pontszám adja meg:

    *[contoso] + [blank] + [f] + [9] + [!] = 5 pont*

* Mivel ez a jelszó legalább öt (5) pontot mutat, elfogadjuk.

> [!IMPORTANT]
> A betiltott jelszó-algoritmus, valamint a globálisan tiltott jelszavak listája az Azure-ban bármikor megváltoztatható a folyamatos biztonsági elemzés és kutatás alapján.
>
> Hibrid forgatókönyvekben a helyszíni DC Agent szolgáltatás esetében a frissített algoritmusok csak a DC-ügynök szoftverének frissítésekor lépnek érvénybe.

## <a name="what-do-users-see"></a>Mit látnak a felhasználók?

Amikor egy felhasználó megpróbál visszaállítani egy jelszót a betiltott valamire, a következő hibaüzenet jelenik meg:

*"Sajnos a jelszava olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálhatja a jelszavát. Próbálkozzon újra egy másik jelszóval. "*

## <a name="license-requirements"></a>Licenckövetelmények

| Felhasználók | Azure AD jelszavas védelem a globálisan tiltott jelszavak listájával | Azure AD jelszavas védelem egyéni tiltott jelszavak listájával|
|-------------------------------------------|---------------------------|---------------------------|
| Csak felhőalapú felhasználók                          | Azure AD Free             | Prémium szintű Azure AD P1 vagy P2 |
| A helyszíni AD DSról szinkronizált felhasználók | Prémium szintű Azure AD P1 vagy P2 | Prémium szintű Azure AD P1 vagy P2 |

> [!NOTE]
> A helyszíni AD DS az Azure AD-vel nem szinkronizált felhasználók az Azure AD jelszavas védelmet is igénybe vehetik a szinkronizált felhasználók meglévő licencelése alapján.

További licencelési információk, beleértve a költségeket is, a [Azure Active Directory díjszabási oldalon](https://azure.microsoft.com/pricing/details/active-directory/)találhatók.

## <a name="next-steps"></a>Következő lépések

Az egyéni tiltott jelszavak listájának használatának megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni tiltott jelszavak konfigurálása](tutorial-configure-custom-password-protection.md)

Ezután [engedélyezheti a helyszíni Azure ad jelszavas védelmet](howto-password-ban-bad-on-premises-deploy.md)is.
