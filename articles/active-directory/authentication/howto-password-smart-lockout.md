---
title: Támadások megelőzése az intelligens zárolás használatával – Azure Active Directory
description: Ismerje meg, hogy a Azure Active Directory Smart zárolásával miként biztosíthatja a szervezete számára a felhasználói jelszavakat kitaláló találgatásos támadások elleni védelmet.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255373"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Felhasználói fiókok támadások elleni védelme az Azure Active Directory intelligens zárolással

Az intelligens zárolás segít kizárni azokat a kártékony elemeket, amelyek megpróbálják kitalálni a felhasználó jelszavát, vagy találgatásos módszerrel próbálnak meg bejutni. Az intelligens zárolás felismeri az érvényes felhasználóktól érkező bejelentkezéseket, és a támadóktól és más ismeretlen forrásoktól érkező bejelentkezésektől eltérően kezeli őket. A támadók ki lesznek zárva, miközben a felhasználók továbbra is hozzáférnek a fiókjukhoz, és folytathatják a hatékony munkavégzést.

## <a name="how-smart-lockout-works"></a>Hogyan működik az intelligens zárolás?

Alapértelmezés szerint az intelligens zárolás 10 sikertelen kísérlet után egy percen belül zárolja a fiókot a bejelentkezési kísérletekből. A fiók minden további sikertelen bejelentkezési kísérlet után ismét zárolja a műveletet, és a későbbi próbálkozások során egy percen belül megszakad. Annak érdekében, hogy a támadók milyen módon tudjanak megkerülni a viselkedést, nem adjuk ki azt a sebességet, amellyel a zárolási időszak a további sikertelen bejelentkezési kísérleteknél nő.

Az intelligens zárolási szolgáltatás az utolsó három rossz jelszó-kivonatot követi, így elkerülhető, hogy a zárolási számláló ugyanazon a jelszónál legyen növelve. Ha valaki többször is ugyanazt a jelszót adja meg, akkor ez a viselkedés nem eredményezi a fiók zárolását.

> [!NOTE]
> A kivonatoló követés funkció nem érhető el olyan ügyfelek számára, akiknek engedélyezve van az átmenő hitelesítés, mert a hitelesítés a helyszínen nem a felhőben történik.

A AD FS 2016-es és AF FS 2019-et használó összevont központi telepítések a [AD FS extranetes zárolás és az extranetes intelligens zárolás](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)használatával is engedélyezhetik a hasonló előnyöket.

Az intelligens zárolás mindig be van kapcsolva az összes Azure AD-ügyfél esetében, ezekkel az alapértelmezett beállításokkal a biztonság és a használhatóság megfelelő kombinációját kínálja. Az intelligens zárolási beállítások testre szabása a szervezetre jellemző értékekkel prémium szintű Azure AD P1 vagy magasabb szintű licenceket igényel a felhasználók számára.

Az intelligens zárolás használata nem garantálja, hogy egy valódi felhasználót soha nem zároltak. Amikor az intelligens zárolás zárol egy felhasználói fiókot, igyekszünk a legjobbat kizárni, hogy ne zárja ki a valódi felhasználót. A zárolási szolgáltatás megkísérli biztosítani, hogy a rossz szereplők ne férhessenek hozzá valódi felhasználói fiókhoz. A következő szempontokat kell figyelembe venni:

* Az egyes Azure AD-adatközpontok egymástól függetlenül nyomon követik a zárolást. A felhasználók (*threshold_limit * datacenter_count*) a kísérletek száma, ha a felhasználó minden adatközpontot elér.
* Az intelligens zárolás ismerős helyet és ismeretlen helyet használ a rossz színész és a valódi felhasználó közötti különbségtételhez. A nem ismerős és ismerős helyszínek külön zárolási számlálókkal rendelkeznek.

Az intelligens zárolás olyan hibrid környezetekben integrálható, amelyek jelszavas kivonatoló szinkronizálást vagy átmenő hitelesítést használnak a helyszíni Active Directory Domain Services (AD DS) fiókok a támadók általi zárolásának biztosításához. Ha intelligens zárolási szabályzatokat állít be az Azure AD-ben, akkor a támadások kiszűrhetők a helyszíni AD DS elérése előtt.

[Átmenő hitelesítés](../hybrid/how-to-connect-pta.md)használatakor a következő szempontokat kell figyelembe venni:

* Az Azure AD zárolási küszöbértéke **kisebb** , mint az AD DS fiókzárolás küszöbértéke. Állítsa be úgy az értékeket, hogy a AD DS fiókzárolás küszöbértéke legalább kettő vagy háromszor hosszabb legyen, mint az Azure AD zárolási küszöbértéke.
* Az Azure AD zárolási időtartamának hosszabbnak kell lennie, mint a fiókzárolás visszavonása AD DS az időtartam után. Az Azure AD időtartama másodpercben van megadva, míg az AD időtartama percben van megadva.

Ha például azt szeretné, hogy az Azure AD-számlálója nagyobb legyen, mint AD DS, akkor az Azure AD 120 másodperc (2 perc), míg a helyszíni AD 1 percre (60 másodpercre) van beállítva.

> [!IMPORTANT]
> Jelenleg a rendszergazda nem tudja feloldani a felhasználók felhőalapú fiókjait, ha azokat az intelligens zárolási funkció kizárta. A rendszergazdának várnia kell, amíg lejár a zárolás időtartama. A felhasználó azonban az önkiszolgáló jelszó-visszaállítás (SSPR) használatával egy megbízható eszközről vagy helyről oldhatja fel a zárolást.

## <a name="verify-on-premises-account-lockout-policy"></a>Helyszíni fiókzárolási házirend ellenőrzése

A helyszíni AD DS fiókzárolási házirend ellenőrzéséhez hajtsa végre a következő lépéseket egy tartományhoz csatlakoztatott rendszerből rendszergazdai jogosultságokkal:

1. Nyissa meg a Csoportházirend felügyeleti eszközt.
2. Szerkessze a szervezet fiókzárolási házirendjét, például az **alapértelmezett tartományi házirendet** tartalmazó csoportházirendet.
3. Tallózással keresse meg a **számítógép-konfigurációs**  >  **házirendek**  >  **Windows-beállítások**  >  **biztonsági beállítások**  >  **fiók házirendek** fiókzárolási  >  **házirendet**.
4. Ellenőrizze a fiókzárolás **küszöbértékét** , és **állítsa alaphelyzetbe a fiókzárolás számlálóját** az értékek után.

![A helyszíni Active Directory fiókzárolási házirend módosítása](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD intelligens zárolási értékek kezelése

A szervezeti követelmények alapján testreszabhatja az Azure AD intelligens zárolási értékeit. Az intelligens zárolási beállítások testre szabása a szervezetre jellemző értékekkel prémium szintű Azure AD P1 vagy magasabb szintű licenceket igényel a felhasználók számára.

A szervezet intelligens zárolási értékeinek vizsgálatához vagy módosításához hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és válassza ki *Azure Active Directory*, majd válassza a **biztonsági**  >  **hitelesítési módszerek**  >  **jelszavas védelem** lehetőséget.
1. Állítsa be a **zárolási küszöbértéket** attól függően, hogy az első zárolás előtt hány sikertelen bejelentkezés engedélyezett a fiókon.

    Az alapértelmezett érték 10.

1. Állítsa a **zárolás időtartamát másodpercben**, az egyes zárolások hosszára másodpercben.

    Az alapértelmezett érték 60 másodperc (egy perc).

> [!NOTE]
> Ha az első bejelentkezés a zárolás után is meghiúsul, a fiók ismét leáll. Ha egy fiók többször is zárolva van, a zárolás időtartama növekszik.

![Az Azure AD intelligens zárolási szabályzatának testreszabása a Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Hogyan állapítható meg, hogy az intelligens zárolási funkció működik-e

Az intelligens zárolási küszöbérték kiváltása esetén a következő üzenet jelenik meg a fiók zárolásakor:

*A fiók átmenetileg zárolva van, hogy megakadályozza a jogosulatlan használatot. Próbálkozzon újra később, és ha még mindig problémája van, forduljon a rendszergazdához.*

Az intelligens zárolás tesztelésekor előfordulhat, hogy a bejelentkezési kéréseket különböző adatközpontok kezelik az Azure AD hitelesítési szolgáltatás földrajzilag elosztott és terheléselosztásos jellege miatt. Ebben az esetben, mivel az egyes Azure AD-adatközpontok egymástól függetlenül nyomon követik a zárolást, több időt vehet igénybe, mint a zárolást okozó kísérletek száma. A felhasználó (*threshold_limit * datacenter_count*) a hibás próbálkozások száma, ha a felhasználó a zárolás előtt minden adatközpontot elér.

## <a name="next-steps"></a>Következő lépések

Az [Azure ad jelszavas védelme érdekében egyéni tiltott jelszavakat is konfigurálhat](tutorial-configure-custom-password-protection.md)a további tapasztalatok testreszabásához.

Az [Azure ad önkiszolgáló jelszó-visszaállítást konfigurálhatja](tutorial-enable-sspr.md)úgy, hogy a felhasználók egy webböngészőből visszaállítsák vagy módosítsák a jelszavukat.
