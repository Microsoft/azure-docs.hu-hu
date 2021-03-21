---
title: Önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: Hogyan működik az önkiszolgáló jelszó-visszaállítási funkció
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 496a8dba9b9ea7fb82ad9016479154d6a61cb767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703275"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Működés: Azure AD önkiszolgáló jelszóátállítás

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha a felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, akkor az utasításokat követve letilthatja önmagát, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására.

> [!IMPORTANT]
> Ez a fogalmi cikk egy rendszergazdát ismerteti, hogy az önkiszolgáló jelszó-visszaállítás hogyan működik. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a következőre: [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

## <a name="how-does-the-password-reset-process-work"></a>Hogyan működik a jelszó-visszaállítási folyamat?

A felhasználók visszaállíthatják vagy módosíthatják a jelszavukat a [SSPR-portál](https://aka.ms/sspr)használatával. Először regisztrálniuk kell a kívánt hitelesítési módszereket. Amikor egy felhasználó hozzáfér a SSPR-portálhoz, az Azure platform a következő tényezőket veszi figyelembe:

* Hogyan kell honosítani a lapot?
* Érvényes a felhasználói fiók?
* Milyen szervezethez tartozik a felhasználó?
* Hol van a felhasználó jelszava felügyelve?
* A felhasználó licenccel rendelkezik a szolgáltatás használatához?

Ha a felhasználó kiválasztja, hogy **nem fér hozzá a fiók** hivatkozásához egy alkalmazásból vagy lapról, vagy közvetlenül a-re mutat [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , a SSPR-portálon használt nyelv a következő lehetőségektől függ:

* Alapértelmezés szerint a böngésző területi beállítása a SSPR a megfelelő nyelven való megjelenítésére szolgál. A jelszó-visszaállítási élmény a [Microsoft 365 által támogatott](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)nyelvekre van honosítva.
* Ha a SSPR egy adott honosított nyelven szeretné összekapcsolni, fűzze hozzá a `?mkt=` jelszó-visszaállítási URL-cím végéhez, valamint a szükséges területi beállításhoz.
    * Például a spanyol *es-US* területi beállítás megadásához használja a következőt: `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Miután a SSPR-portál megjelenik a szükséges nyelven, a rendszer felszólítja a felhasználót, hogy adjon meg egy felhasználói azonosítót, és adja át a CAPTCHA-t. Az Azure AD most ellenőrzi, hogy a felhasználó használhatja-e a SSPR a következő ellenőrzések végrehajtásával:

* Ellenőrzi, hogy a felhasználó rendelkezik-e engedélyezett SSPR, és hozzá van-e rendelve egy Azure AD-licenc.
  * Ha a felhasználó nincs engedélyezve a SSPR, vagy nincs hozzárendelt licence, a rendszer felkéri a felhasználót, hogy lépjen kapcsolatba a rendszergazdával a jelszavának alaphelyzetbe állításához.
* Ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési módszerekkel a fiókjában a rendszergazdai házirendnek megfelelően.
  * Ha a házirend csak egyetlen módszert igényel, ellenőrizze, hogy a felhasználó rendelkezik-e a rendszergazdai házirend által engedélyezett hitelesítési módszerek közül legalább az egyikhez megadott megfelelő adattal.
    * Ha a hitelesítési módszerek nincsenek konfigurálva, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszavuk alaphelyzetbe állításához.
  * Ha a házirend két módszert igényel, ellenőrizze, hogy a felhasználó rendelkezik-e a felügyeleti házirend által engedélyezett legalább két hitelesítési módszerhez megadott megfelelő adatmennyiséggel.
    * Ha a hitelesítési módszerek nincsenek konfigurálva, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszavuk alaphelyzetbe állításához.
  * Ha a felhasználóhoz egy Azure-rendszergazdai szerepkör van rendelve, akkor a rendszer kikényszeríti a kétkapus erős jelszavas házirendet. További információ: [rendszergazdai beállítások visszaállítása a szabályzatban](concept-sspr-policy.md#administrator-reset-policy-differences).
* Ellenőrzi, hogy a felhasználó jelszava felügyelt-e a helyszínen, például ha az Azure AD-bérlő összevont, átmenő hitelesítés vagy jelszó-kivonatolási szinkronizálást használ:
  * Ha a SSPR visszaírási konfigurálva van, és a felhasználó jelszava felügyelve van a helyszínen, a felhasználó továbbra is engedélyezheti és visszaállíthatja a jelszavát.
  * Ha a SSPR visszaírási nincs telepítve, és a felhasználó jelszava felügyelve van a helyszínen, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszavuk alaphelyzetbe állításához.

Ha az összes korábbi ellenőrzés sikeresen befejeződött, a felhasználó végigvezeti a jelszó alaphelyzetbe állításának vagy módosításának folyamatán.

> [!NOTE]
> A SSPR e-mailes értesítéseket küldhet a felhasználóknak a jelszó-visszaállítási folyamat részeként. Ezeket az e-maileket a rendszer az SMTP Relay szolgáltatással küldi el, amely aktív-aktív módban működik több régióban.
>
> Az SMTP-továbbító szolgáltatások fogadják és feldolgozzák az e-mail törzsét, de nem tárolják azt. A SSPR e-mail-címe, amely esetleg tartalmazhatja az ügyfél által megadott adatokat, nem tárolódik az SMTP Relay szolgáltatás naplófájljaiban. A naplók csak protokoll-metaadatokat tartalmaznak.

A SSPR megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>A felhasználók regisztrálásának megkövetelése a bejelentkezéskor

Engedélyezheti, hogy a felhasználó megkövetelje a SSPR regisztrációját, ha az Azure AD-t használó alkalmazásokba jelentkeznek be. A munkafolyamat a következő alkalmazásokat tartalmazza:

* Microsoft 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazások
* Az Azure AD-t használó egyéni alkalmazások

Ha nem igényel regisztrációt, a rendszer nem kéri a felhasználókat a bejelentkezés során, de manuálisan is regisztrálhatnak. A felhasználók vagy megkereshetik [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) vagy kiválaszthatják a **jelszó-visszaállítási hivatkozás regisztrálása** hivatkozást a **profil** lapon a hozzáférési panelen.

![A Azure Portal SSPR regisztrációs beállításai][Registration]

> [!NOTE]
> A felhasználók a **Mégse gombra** kattintva vagy az ablak bezárásával SSPR el a regisztrációs portált. A rendszer azonban minden alkalommal regisztrálja őket, amikor bejelentkeznek a regisztráció befejezéséhez.
>
> Ez a SSPR való regisztrálási megszakítás nem bontja a felhasználó kapcsolódását, ha már be van jelentkezve.

## <a name="reconfirm-authentication-information"></a>Hitelesítési adatok újbóli megerősítése

Annak érdekében, hogy a hitelesítési módszerek helyesek legyenek, amikor szükség van a jelszó alaphelyzetbe állítására vagy módosítására, megkövetelheti a felhasználók számára, hogy bizonyos idő elteltével erősítse meg az adataikat a regisztrált információk alapján. Ez a beállítás csak akkor érhető el, ha engedélyezi a **felhasználók regisztrációját a bejelentkezéskor** lehetőségre.

Érvényes értékek, amelyek arra kérik a felhasználókat, hogy regisztrált módszereiket *0* és *730* nap között adják meg. Ha ez az érték *0* , azt jelenti, hogy a rendszer soha nem kéri a felhasználókat, hogy erősítse meg a hitelesítési adataikat.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha egy felhasználó engedélyezve van a SSPR, legalább egy hitelesítési módszert regisztrálnia kell. Javasoljuk, hogy két vagy több hitelesítési módszert válasszon, hogy a felhasználók nagyobb rugalmasságot biztosítsanak abban az esetben, ha nem tudnak hozzáférni az egyik metódushoz, amikor szükségük van rá. További információ: [Mi a hitelesítési módszer?](concept-authentication-methods.md).

A SSPR a következő hitelesítési módszerek érhetők el:

* Mobilalkalmazás-értesítés
* Mobilalkalmazás-kód
* E-mail
* Mobiltelefon
* Munkahelyi telefon
* Biztonsági kérdések

Ha a rendszergazda engedélyezte a hitelesítést, a felhasználók csak akkor állíthatják alaphelyzetbe a jelszavukat, ha regisztráltak egy hitelesítési módszert.

> [!WARNING]
> Az Azure- *rendszergazdai* szerepkörökhöz rendelt fiókok használatához a következő szakaszban meghatározott módszerek szükségesek: a [rendszergazda-visszaállítási házirend eltérései](concept-sspr-policy.md#administrator-reset-policy-differences).

![A hitelesítési módszerek kiválasztása a Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>A szükséges hitelesítési módszerek száma

Megadhatja a rendelkezésre álló hitelesítési módszerek számát, amelyet a felhasználónak meg kell adnia a jelszavuk visszaállításához vagy feloldásához. Ezt az értéket beállíthatja *egy* vagy *kettő* értékre.

A felhasználók több hitelesítési módszert is regisztrálhatnak. Azt javasoljuk, hogy a felhasználók legalább két hitelesítési módszert regisztráljanak, így nagyobb rugalmasságot biztosítanak, ha nem tudnak hozzáférni az egyik metódushoz, amikor szükségük van rá.

Ha a felhasználó nem rendelkezik a szükséges módszerek minimális számával, amikor a SSPR használják, egy hibaüzenet jelenik meg, amely arra utasítja a felhasználót, hogy kérje meg, hogy a rendszergazda állítsa vissza a jelszavát. Ügyeljen arra, hogy növelje a szükséges módszerek számát, ha a meglévő felhasználók regisztrálva vannak a SSPR, és ezt követően nem tudják használni a szolgáltatást. További információt a [hitelesítési módszerek módosításához](#change-authentication-methods)a következő szakaszban talál.

#### <a name="mobile-app-and-sspr"></a>Mobile App és SSPR

Ha mobil alkalmazást használ a jelszó-visszaállítási módszerként, például a Microsoft Authenticator alkalmazáshoz, a következő szempontokat kell figyelembe vennie:

* Ha a rendszergazdáknak a jelszó alaphelyzetbe állításához egy metódust kell használni, az ellenőrzési kód az egyetlen elérhető lehetőség.
* Ha a rendszergazdáknak két módszert kell használniuk a jelszó alaphelyzetbe állításához, a felhasználók a többi engedélyezett módszer mellett értesítési **vagy** ellenőrzési kódokat is használhatnak.

| Az alaphelyzetbe állításhoz szükséges módszerek száma | Eggyel | Kettővel |
| :---: | :---: | :---: |
| Elérhető a Mobile App szolgáltatásai | Code | Kód vagy értesítés |

A felhasználók nem regisztrálhatják a mobileszköz regisztrációját az önkiszolgáló jelszó-visszaállításhoz való regisztráció során [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . A felhasználók a következő címen regisztrálhatják a mobil alkalmazást: [https://aka.ms/mfasetup](https://aka.ms/mfasetup) vagy a kombinált biztonsági adatok regisztrációjában [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> A hitelesítő alkalmazás nem választható ki az egyetlen hitelesítési módszerként, ha csak egy metódusra van szükség. Hasonlóképpen, a hitelesítő alkalmazás és csak egy további metódus nem választható ki két módszer megkövetelése esetén.
>
> A hitelesítő alkalmazást metódusként tartalmazó SSPR házirendek konfigurálásakor legalább egy további metódust ki kell választani, ha egy metódusra van szükség, és a két módszer konfigurálásakor legalább két további módszert kell kiválasztani.
>
> Ennek a követelménynek az az oka, hogy a jelenlegi SSPR-regisztrációs felület nem tartalmazza a hitelesítő alkalmazás regisztrálásának lehetőségét. A hitelesítő alkalmazás regisztrálásának lehetősége az új, [kombinált regisztrálási élmény](./concept-registration-mfa-sspr-combined.md)részét képezi.
>
> Olyan házirendek engedélyezése, amelyek csak a hitelesítő alkalmazást használják (ha az egyik módszert kötelező megadni), vagy a hitelesítő alkalmazást, és csak egy további metódust (ha két módszerre van szükség) a felhasználók számára blokkolhatja a SSPR való regisztrációt, amíg az új, összevont regisztrációs élmény használatára nem lettek konfigurálva.

### <a name="change-authentication-methods"></a>Hitelesítési módszerek módosítása

Ha olyan házirenddel indul, amely csak egy szükséges hitelesítési módszert tartalmaz a regisztrációhoz vagy a zárolás feloldásához, és ezt két módszerre módosítja, mi történik?

| Regisztrált metódusok száma | Szükséges metódusok száma | Eredmény |
| :---: | :---: | :---: |
| 1 vagy több | 1 |  Alaphelyzetbe állítás vagy zárolás feloldása |
| 1 | 2 | **Nem lehet** alaphelyzetbe állítani vagy feloldani |
| 2 vagy több | 2 |  Alaphelyzetbe állítás vagy zárolás feloldása |

A rendelkezésre álló hitelesítési módszerek módosítása a felhasználók számára is problémákat okozhat. Ha megváltoztatja a felhasználó által használható hitelesítési módszerek típusait, a felhasználók véletlenül sem használhatják a SSPR, ha nem rendelkeznek a rendelkezésre álló minimális mennyiségű adattal.

Vegye figyelembe a következő példát:

1. Az eredeti házirend két hitelesítési módszerrel van konfigurálva. Csak az Office-telefonszámot és a biztonsági kérdéseket használja.
1. A rendszergazda úgy módosítja a szabályzatot, hogy a továbbiakban ne használja a biztonsági kérdéseket, de lehetővé teszi a mobiltelefon és egy másodlagos e-mail használatát.
1. A mobiltelefon vagy a másodlagos e-mail-mezők nélkül feltöltött felhasználók már nem állíthatják alaphelyzetbe a jelszavukat.

## <a name="notifications"></a>Értesítések

A jelszóval kapcsolatos események megismerése érdekében a SSPR lehetővé teszi az értesítések konfigurálását mind a felhasználók, mind az identitás-rendszergazdák számára.

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha ez a beállítás **Igen** értékre van állítva, a felhasználók a jelszavuk módosítására vonatkozó értesítő e-mailt kapnak. Az e-mailt az SSPR-portálon keresztül küldi el a rendszer az Azure AD-ben tárolt elsődleges és másodlagos e-mail-címeire. Az alaphelyzetbe állítási eseményről senki más nem kap értesítést.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Minden rendszergazda értesítése, ha más rendszergazdák visszaállítják a jelszavukat

Ha ez a beállítás **Igen** értékre van állítva, akkor az összes többi Azure-rendszergazda e-mailt kap az Azure ad-ben tárolt elsődleges e-mail-címére. Az e-mail értesíti őket arról, hogy egy másik rendszergazda módosította a jelszavát a SSPR használatával.

Vegye figyelembe a következő példát:

* Négy rendszergazda található a környezetben.
* *A rendszergazda a* SSPR használatával visszaállítja a jelszavukat.
* A *B*, *C* és *D* rendszergazdák e-mailben értesítést kapnak a jelszó-visszaállításról.

## <a name="on-premises-integration"></a>Helyszíni integráció

Ha hibrid környezettel rendelkezik, beállíthatja, hogy Azure AD Connect a jelszó-módosítási események visszaírását az Azure AD-ből egy helyszíni könyvtárba.

![A jelszó visszaírási ellenőrzése engedélyezve van és működik][Writeback]

Az Azure AD ellenőrzi a jelenlegi hibrid kapcsolatát, és az alábbi üzenetek egyikét biztosítja a Azure Portalban:

* A helyszíni visszaírási-ügyfél működik.
* Az Azure AD online állapotban van, és kapcsolódik a helyszíni visszaírási-ügyfélhez. Azonban a Azure AD Connect telepített verziójához hasonlóan elavult. Vegye fontolóra [Azure ad Connect frissítését](../hybrid/how-to-upgrade-previous-version.md) , és győződjön meg arról, hogy rendelkezik a legújabb csatlakozási funkciókkal és fontos hibajavításokkal.
* Sajnos a helyszíni visszaírási-ügyfél állapota nem ellenőrizhető, mert a Azure AD Connect telepített verziója elavult. [Frissítse Azure ad Connect](../hybrid/how-to-upgrade-previous-version.md) a kapcsolódási állapotának ellenőrzését.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási-ügyfélhez. A kapcsolatok visszaállításával [kapcsolatos Azure ad Connect](./troubleshoot-sspr-writeback.md) .
* Sajnos nem lehet csatlakozni a helyszíni visszaírási-ügyfélhez, mert a jelszó visszaírási nincs megfelelően konfigurálva. [Konfigurálja a jelszó visszaírási](./tutorial-enable-sspr-writeback.md) a kapcsolódás visszaállításához.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási-ügyfélhez. Ennek oka az lehet, hogy a végén átmenetileg problémák léptek fel. Ha a probléma továbbra is fennáll, hárítsa el a [Azure ad Connect](./troubleshoot-sspr-writeback.md) a kapcsolódás visszaállításához.

A SSPR-visszaírási első lépéseihez hajtsa végre a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás (SSPR) visszaírási engedélyezése](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavak visszaírása a helyszíni címtárba

A jelszó visszaírási a Azure Portal használatával engedélyezheti. Átmenetileg letilthatja a jelszó visszaírási anélkül, hogy újra kellene konfigurálnia Azure AD Connect.

* Ha a beállítás értéke **Igen**, akkor a visszaírási engedélyezve van. Összevont, átmenő hitelesítés vagy jelszó-kivonatolással szinkronizált felhasználók képesek visszaállítani a jelszavukat.
* Ha a beállítás értéke **nem**, akkor a visszaírási le van tiltva. Összevont, átmenő hitelesítés vagy jelszó-kivonatolással szinkronizált felhasználók nem tudják visszaállítani a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Fiókok feloldásának engedélyezése a felhasználók számára a jelszó alaphelyzetbe állítása nélkül

Alapértelmezés szerint az Azure AD feloldja a fiókokat, amikor a jelszó-visszaállítást végzi. A rugalmasság biztosítása érdekében dönthet úgy, hogy lehetővé teszi a felhasználók számára, hogy a jelszavuk alaphelyzetbe állítása nélkül is feloldják a helyszíni fiókjaikat. Ezzel a beállítással elkülönítheti a két műveletet.

* Ha az **Igen** értékre van állítva, a felhasználók lehetőséget kapnak a jelszavuk visszaállítására és a fiók zárolásának feloldására, illetve a fiók zárolásának feloldására a jelszó alaphelyzetbe állítása nélkül.
* Ha a **nem** értékre van állítva, a felhasználók csak a jelszó-visszaállítási és a fiók-feloldási művelet végrehajtására képesek.

### <a name="on-premises-active-directory-password-filters"></a>Helyszíni Active Directory jelszavak szűrői

A SSPR elvégzi a rendszergazda által kezdeményezett jelszó-visszaállítás megfelelőjét Active Directoryban. Ha harmadik féltől származó jelszavas szűrőt használ az egyéni jelszavak érvényesítéséhez, és az Azure AD önkiszolgáló jelszó-visszaállítás során meg kell adni ezt a jelszót, akkor győződjön meg arról, hogy a harmadik féltől származó jelszó-szűrési megoldás a rendszergazdai jelszó alaphelyzetbe állítására van konfigurálva. Alapértelmezés szerint a [Active Directory Domain Services Azure ad jelszavas védelme](concept-password-ban-bad-on-premises.md) is támogatott.

## <a name="password-reset-for-b2b-users"></a>Jelszó alaphelyzetbe állítása B2B-felhasználók számára

A jelszó alaphelyzetbe állítása és módosítása teljes mértékben támogatott az összes vállalatközi (B2B) konfigurációban. A B2B felhasználói jelszó alaphelyzetbe állítása a következő három esetben támogatott:

* **Meglévő Azure ad-Bérlővel rendelkező partnerszervezet felhasználói**: Ha az a szervezet, amelyen a partner, rendelkezik egy meglévő Azure ad-Bérlővel, a jelszó-visszaállítási házirendeket a bérlőn is be kell tartani. A jelszó-visszaállítás működéséhez a partner szervezetnek csak meg kell győződnie arról, hogy az Azure AD SSPR engedélyezve van. Microsoft 365 ügyfelek esetében nem számítunk fel további díjat.
* Önkiszolgáló regisztráción **keresztül regisztrációt végző felhasználók** : Ha a szervezet, amellyel az önkiszolgáló [bejelentkezési](../enterprise-users/directory-self-service-signup.md) funkciót használta a bérlőhöz való belépéshez, a jelszó alaphelyzetbe állításával visszaállíthatja a regisztrált e-mail-címet.
* **B2B-felhasználók**: az új [Azure ad B2B-funkciók](../external-identities/what-is-b2b.md) használatával létrehozott összes új B2B-felhasználó is visszaállíthatja a jelszavukat a Meghívási folyamat során regisztrált e-mail-címre.

A forgatókönyv teszteléséhez lépjen a https://passwordreset.microsoftonline.com következő partner-felhasználók egyikével. Ha egy másodlagos e-mail-vagy hitelesítési e-mail-cím van megadva, a jelszó-visszaállítás a várt módon működik.

> [!NOTE]
> Az Azure AD-bérlőhöz (például Hotmail.com, Outlook.com vagy más személyes e-mail-címekhez) hozzáférést biztosító Microsoft-fiókok nem tudják használni az Azure AD-SSPR. A jelszó alaphelyzetbe állításához a következő információkat kell használnia, [Ha nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) cikkbe.

## <a name="next-steps"></a>Következő lépések

A SSPR megkezdéséhez kövesse az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: az önkiszolgáló jelszó-visszaállítás engedélyezése (SSPR)](tutorial-enable-sspr.md)

Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "SSPR-regisztrációs beállítások konfigurálása a Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Helyszíni integráció a SSPR Azure Portal"
