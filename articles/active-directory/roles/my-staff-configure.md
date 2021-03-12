---
title: A saját munkatársak használata a felhasználók felügyeletének delegálására – Azure AD | Microsoft Docs
description: Felhasználói felügyelet delegálása a saját munkatársak és a felügyeleti egységek használatával
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103224996"
---
# <a name="manage-your-users-with-my-staff"></a>A felhasználók kezelése a saját munkatársaival

A saját munkatársak lehetővé teszik az engedélyek delegálását a hatóság, például az áruház vagy a csapat érdeklődői számára, hogy a személyzet tagjai hozzáférhessenek az Azure AD-fiókokhoz. A központi ügyfélszolgálatra való támaszkodás helyett a szervezetek olyan gyakori feladatokat delegálnak, mint például a jelszavak alaphelyzetbe állítása vagy a telefonszámok módosítása a helyi csapat kezelőjére. A munkatársakkal egy olyan felhasználó, aki nem tud hozzáférni a fiókjához, mindössze néhány kattintással visszanyerheti a hozzáférést, és nem igényel ügyfélszolgálatot vagy informatikai személyzetet.

Mielőtt megkezdené a saját munkatársainak konfigurálását, javasoljuk, hogy tekintse át ezt a dokumentációt, valamint a [felhasználói dokumentációt](../user-help/my-staff-team-manager.md) , hogy megtudja, hogyan működik, és hogyan befolyásolja a felhasználókat. A felhasználó dokumentációját kihasználva betaníthatja és felkészítheti a felhasználókat az új felületre, és segítséget nyújthat a sikeres bevezetéshez.

## <a name="how-my-staff-works"></a>A munkatársak működése

A munkatársak a felügyeleti egységeken alapulnak, amelyek olyan erőforrás-tárolók, amelyekkel korlátozható a szerepkör-hozzárendelés felügyeleti vezérlő hatóköre. További információ: [felügyeleti egységek kezelése Azure Active Directoryban](administrative-units.md). A saját munkatársaiban a felügyeleti egységek az áruházban vagy részlegben lévő felhasználók egy csoportját is tartalmazhatják. Egy csoportvezető ezután egy vagy több egység hatókörén keresztül egy rendszergazdai szerepkörhöz rendelhető hozzá.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.

  * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő.

  * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt](../fundamentals/sign-up-organization.md) , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Az SMS-alapú hitelesítés engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Minden olyan felhasználónak, aki engedélyezve van az SMS-hitelesítési módszer házirendjében, licenccel kell rendelkeznie, még akkor is, ha azok nem használják azt. Minden engedélyezett felhasználónak rendelkeznie kell a következő Azure AD-vagy Microsoft 365-licencek valamelyikével:

  * [Prémium szintű Azure AD P1 vagy P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 vagy F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 vagy E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) vagy [Microsoft 365 (M365) E3 vagy E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Munkatársak engedélyezése

Miután konfigurálta a felügyeleti egységeket, alkalmazhatja ezt a hatókört a munkatársakat elérő felhasználókra. Csak a rendszergazdai szerepkörhöz rendelt felhasználók férhetnek hozzá a munkatársaihoz. A munkatársak engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a Azure Portal felhasználói rendszergazdaként.
2. Tallózással keresse meg **Azure Active Directory** felhasználói  >  **Beállítások**  >  **felhasználói szolgáltatás**  >  **előzetesek kezelése felhasználói szolgáltatás előnézetének beállításait**.
3. A **rendszergazdák hozzáférhetnek a munkatársaihoz** lehetőséggel engedélyezheti az összes felhasználó, a kiválasztott felhasználó vagy a felhasználói hozzáférés beállítást.

> [!Note]
> Csak a rendszergazdai szerepkörhöz rendelt felhasználók férhetnek hozzá a munkatársaihoz. Ha olyan felhasználó számára engedélyezi a munkatársakat, aki nem rendelkezik rendszergazdai szerepkörrel, akkor nem fog tudni hozzáférni a munkatársaihoz.

## <a name="conditional-access"></a>Feltételes hozzáférés

Az Azure AD feltételes hozzáférési szabályzatával védetté teheti a saját munkatársak portálját. Használhatja olyan feladatokhoz, mint például a többtényezős hitelesítés megkövetelése a munkatársak elérése előtt.

Határozottan javasoljuk, hogy az [Azure ad feltételes hozzáférési szabályzatok](../conditional-access/index.yml)használatával megvédje a munkatársakat. Ha feltételes hozzáférési szabályzatot szeretne alkalmazni a saját munkatársaira, először a saját munkahelyét kell meglátogatnia néhány percig, hogy automatikusan kiépítse a szolgáltatást a bérlőben a feltételes hozzáférés általi használatra.

Az egyszerű szolgáltatásnév akkor jelenik meg, ha létrehoz egy feltételes hozzáférési szabályzatot, amely a saját személyzet Felhőbeli alkalmazására vonatkozik.

![Feltételes hozzáférési szabályzat létrehozása a saját munkatársak alkalmazáshoz](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Saját munkatársak használata

Amikor egy felhasználó a saját munkatársaihoz jut, megjelennek azoknak a [felügyeleti egységeknek](administrative-units.md) a neve, amelyeken rendszergazdai jogosultságokkal rendelkeznek. A [saját munkatársak felhasználói dokumentációjában](../user-help/my-staff-team-manager.md)a "location" kifejezést használjuk a felügyeleti egységekre való hivatkozáshoz. Ha egy rendszergazda engedélyei nem rendelkeznek felügyeleti egység hatókörrel, az engedélyek a szervezeten belül érvényesek. A munkatársaim engedélyezése után az engedélyezett és a hozzájuk rendelt felhasználók hozzáférhetnek a szolgáltatáshoz [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Kiválaszthatnak egy felügyeleti egységet az adott egységben lévő felhasználók megtekintéséhez, és kiválaszthatnak egy felhasználót a profil megnyitásához.

## <a name="reset-a-users-password"></a>Új felhasználói jelszó létrehozása

Ahhoz, hogy a helyi felhasználók számára további jelszavakat lehessen biztosítani, meg kell felelnie a következő előfeltételeknek. Részletes útmutatás: az önkiszolgáló [jelszó-visszaállítás engedélyezése](../authentication/tutorial-enable-sspr-writeback.md) oktatóanyag.

* Jelszó-visszaírási engedélyeinek konfigurálása
* Jelszó visszaírási engedélyezése a Azure AD Connectban
* Jelszó visszaírási engedélyezése az Azure AD önkiszolgáló jelszó-visszaállításban (SSPR)

A következő szerepkörök jogosultak a felhasználó jelszavának alaphelyzetbe állítására:

* [Hitelesítés rendszergazdája](permissions-reference.md#authentication-administrator)
* [Kiemelt jogosultságú hitelesítés rendszergazdája](permissions-reference.md#privileged-authentication-administrator)
* [Globális rendszergazda](permissions-reference.md#global-administrator)
* [Segélyszolgálat rendszergazdája](permissions-reference.md#helpdesk-administrator)
* [Felhasználói rendszergazda](permissions-reference.md#user-administrator)
* [Jelszókezelő](permissions-reference.md#password-administrator)

A **saját munkatársak** területen nyisson meg egy felhasználói profilt. Válassza a **jelszó alaphelyzetbe állítása** lehetőséget.

* Ha a felhasználó csak felhőalapú, megtekintheti a felhasználónak megadható ideiglenes jelszót.
* Ha a felhasználó helyi Active Directoryról van szinkronizálva, megadhat egy jelszót, amely megfelel a helyszíni AD-házirendeknek. Ezután megadhatja ezt a jelszót a felhasználónak.

    ![Jelszó-visszaállítási folyamatjelző és sikeres értesítés](./media/my-staff-configure/reset-password.png)

A felhasználónak a következő bejelentkezéskor meg kell változtatnia a jelszavát.

## <a name="manage-a-phone-number"></a>Telefonszám kezelése

A **saját munkatársak** területen nyisson meg egy felhasználói profilt.

* Válassza a **telefonszám hozzáadása** szakaszt a felhasználó telefonszámának hozzáadásához
* Válassza a telefonszám **szerkesztése** lehetőséget a telefonszám módosításához.
* Válassza a **telefonszám eltávolítása** lehetőséget a felhasználó telefonszámának eltávolításához.

A beállításoktól függően a felhasználó használhatja az SMS-be való bejelentkezéshez beállított telefonszámot, a többtényezős hitelesítés végrehajtását, valamint az önkiszolgáló jelszó-visszaállítást.

A felhasználó telefonszámának kezeléséhez a következő szerepkörök egyikét kell hozzárendelni:

* [Hitelesítés rendszergazdája](permissions-reference.md#authentication-administrator)
* [Kiemelt jogosultságú hitelesítés rendszergazdája](permissions-reference.md#privileged-authentication-administrator)
* [Globális rendszergazda](permissions-reference.md#global-administrator)

## <a name="search"></a>Keresés

A munkahelyen a keresési sáv használatával kereshet felügyeleti egységeket és felhasználókat a szervezeten belül. Kereshet a szervezet összes felügyeleti egységén és felhasználóján, de csak olyan felügyeleti egységben lévő felhasználók módosíthatók, akik rendszergazdai jogosultságokkal rendelkeznek.

A felhasználókat egy felügyeleti egységen belül is megkeresheti. Ehhez használja a felhasználói lista tetején található keresési sávot.

## <a name="audit-logs"></a>Naplók

A naplókat megtekintheti a munkatársaim által a Azure Active Directory portálon végrehajtott műveletekhez. Ha a naplót egy, a munkatársain végrehajtott művelet hozta létre, akkor ez a naplózási esemény további részletei között jelenik meg.

## <a name="next-steps"></a>Következő lépések

[Munkatársak felhasználói dokumentációja](../user-help/my-staff-team-manager.md) 
 [Felügyeleti egységek dokumentációja](administrative-units.md)
