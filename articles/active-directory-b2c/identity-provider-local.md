---
title: Azure AD B2C helyi fiók identitás-szolgáltatójának beállítása
titleSuffix: Azure AD B2C
description: Adja meg, hogy az azonosító típusok használatával hogyan regisztrálhatók vagy bejelentkezhetnek (e-mailek, felhasználónevek, telefonszámok) a Azure Active Directory B2C-bérlőben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9eb28ad19d53df542769a89b839668bbb205e30
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256106"
---
# <a name="set-up-the-local-account-identity-provider"></a>A helyi fiók identitás-szolgáltatójának beállítása

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C különböző módszereket biztosít a felhasználók hitelesítéséhez. A felhasználók bejelentkezhetnek a helyi fiókba Felhasználónév és jelszó használatával, telefonos ellenőrzéssel (más néven jelszó nélküli hitelesítéssel) vagy közösségi identitás-szolgáltatókkal. Az e-mail-regisztráció alapértelmezés szerint engedélyezve van a helyi fiók identitás-szolgáltatójának beállításaiban. 

Ez a cikk azt ismerteti, hogy a felhasználók hogyan hozhatnak létre helyi fiókokat a Azure AD B2C bérlő számára. A közösségi vagy vállalati identitások esetében, ahol a felhasználó identitását egy összevont identitás-szolgáltató, például a Facebook vagy a Google kezeli, tekintse meg az [identitás-szolgáltató hozzáadása](add-identity-provider.md)című témakört.

## <a name="email-sign-in"></a>E-mail bejelentkezés

Az e-mail-cím használatával a felhasználók bejelentkezhetnek az e-mail-címükkel és jelszavával:

- **Bejelentkezéskor** a rendszer felszólítja a felhasználókat a levelezés és a jelszó megadására.
- A **regisztráció** során a rendszer a felhasználókat egy e-mail-címre fogja kérni, amelyet a regisztrációkor (opcionális) és a bejelentkezési azonosítójuk alapján kell ellenőrizni. A felhasználó ezután beírja a regisztrálási lapon kért egyéb adatokat, például a megjelenítendő nevet, a nevet és a vezetéknevet. Ezután válassza a Folytatás lehetőséget a fiók létrehozásához.
- **Jelszó alaphelyzetbe állítása**, a felhasználóknak meg kell adniuk és igazolniuk kell az e-mailjeit, amely után a felhasználó alaphelyzetbe állíthatja a jelszót

![E-mail regisztrációs vagy bejelentkezési élmény](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Felhasználónév-bejelentkezés

A felhasználó lehetőséggel a felhasználók bejelentkezhetnek a felhasználónévvel és a jelszóval:

- **Bejelentkezés**: a rendszer felszólítja a felhasználókat a felhasználónevek és a jelszó megadására.
- **Regisztráció**: a rendszer felhasználónevet kér a felhasználótól, amely a bejelentkezési azonosítójuk lesz. A rendszer a felhasználóktól egy e-mail-címet is kér, amely a regisztrációkor lesz ellenőrizve. Az e-mail-cím a jelszó-visszaállítási folyamat során lesz használatban. A felhasználó bármilyen más, a regisztrációs oldalon kért információt megad, például a megjelenítendő nevet, a nevet és a vezetéknevet. A felhasználó ezután kiválasztja a folytatás elemet a fiók létrehozásához.
- **Jelszó-visszaállítás**: a felhasználóknak meg kell adniuk a felhasználónevét és a hozzá tartozó e-mail-címüket. Az e-mail-címet ellenőrizni kell, amely után a felhasználó alaphelyzetbe állíthatja a jelszót.

![Felhasználónév-regisztrációs vagy bejelentkezési élmény](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Telefonos bejelentkezés (előzetes verzió)

A jelszó nélküli hitelesítés olyan típusú hitelesítés, amelyben a felhasználónak nem kell bejelentkeznie a jelszavával. A telefonos regisztrációt és bejelentkezést követően a felhasználó az elsődleges bejelentkezési azonosítójának megfelelő telefonszámon regisztrálhat az alkalmazásra. A regisztráció és a bejelentkezés során a felhasználó a következő élményt nyújtja:

- **Bejelentkezés**: Ha a felhasználó rendelkezik egy meglévő, telefonszámmal rendelkező fiókkal, a felhasználó beírja a telefonszámát, és kiválasztja a *Bejelentkezés* lehetőséget. A *Folytatás* gombra kattintva megerősítik az országot és a telefonszámot, és egy egyszeri ellenőrző kódot kapnak a telefonjára. A felhasználó beírja az ellenőrző kódot, és kiválasztja a *tovább* lehetőséget a bejelentkezéshez.
- **Regisztráció**: Ha a felhasználó még nem rendelkezik fiókkal az alkalmazáshoz, létrehozhat egyet a *regisztráció most* hivatkozásra kattintva. 
    1. Megjelenik egy regisztrációs oldal, ahol a felhasználó kiválasztja az *országot*, beírja a telefonszámát, és kiválasztja a *kód küldése* lehetőséget. 
    1. A rendszer egy egyszeri ellenőrző kódot továbbít a felhasználó telefonszámára. A felhasználó beírja az *ellenőrző kódot* a regisztrációs oldalra, majd kiválasztja a *kód ellenőrzése* lehetőséget. (Ha a felhasználó nem tudja beolvasni a kódot, akkor válassza az *új kód küldése* lehetőséget). 
    1. A felhasználó bármilyen más, a regisztrációs oldalon kért információt megad, például a megjelenítendő nevet, a nevet és a vezetéknevet. Ezután válassza a Folytatás elemet.
    1. Ezután a rendszer megkéri a felhasználót, hogy adjon meg egy **helyreállítási e-mailt**. A felhasználó megadja az e-mail-címét, majd kiválasztja az *ellenőrző kód küldése* lehetőséget. A rendszer elküld egy kódot a felhasználó e-mail-fiókjába, amelyet az ellenőrző kód mezőbe beolvashat és megadhat. Ezután a felhasználó kiválasztja a kód ellenőrzése lehetőséget.
    1. A kód ellenőrzése után a felhasználó a *Létrehozás* gombra kattint a fiók létrehozásához. 

![Telefonos regisztrációs vagy bejelentkezési élmény](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Díjszabás

Az egyszeri jelszavak SMS szöveges üzenetek használatával érkeznek a felhasználókhoz. A mobileszköz-kezelőtől függően az egyes elküldött üzenetekért díjat számítunk fel. A díjszabással kapcsolatos információkért tekintse meg a [Azure Active Directory B2C díjszabásának](https://azure.microsoft.com/pricing/details/active-directory-b2c/) **külön** díjszabását ismertető szakaszt.

> [!NOTE]
> A többtényezős hitelesítés (MFA) alapértelmezés szerint le van tiltva a felhasználói folyamatok telefonos regisztrációval való konfigurálásakor. Engedélyezheti az MFA használatát a felhasználói folyamatokban a telefonos regisztrációval, de mivel a telefonszámot elsődleges azonosítóként használja, az egyszeri e-mail-jelszó az egyetlen lehetőség a második hitelesítési tényezőhöz.

### <a name="phone-recovery"></a>Telefonos helyreállítás

Ha engedélyezi a telefonos regisztrációt és a bejelentkezést a felhasználói folyamatokban, érdemes engedélyezni a helyreállítási e-mail funkciót is. Ezzel a funkcióval a felhasználók megadhatnak egy e-mail-címet, amellyel helyreállíthatja a fiókját, amikor nem rendelkeznek a telefonjára. Ezt az e-mail-címet csak a fiók-helyreállításhoz használja a rendszer. Nem használható a bejelentkezéshez.

- Ha a helyreállítási e-mail-üzenet **be van kapcsolva**, a rendszer első alkalommal regisztrál egy felhasználót, hogy ellenőrizze a biztonsági mentési e-mailt. Az a felhasználó, aki még nem adott meg helyreállítási e-mailt, a következő bejelentkezéskor a rendszer kéri, hogy ellenőrizze a biztonsági mentési e-maileket.

- Ha a helyreállítási e-mail **ki van kapcsolva**, a regisztrációt vagy bejelentkezést kérő felhasználó nem jelenik meg a helyreállítási e-mail-üzenetben.
 
A következő Képernyőképek a telefon helyreállítási folyamatát mutatják be:

![Telefonos helyreállítási felhasználói folyamat](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Telefonos vagy e-mail-bejelentkezés (előzetes verzió)

Dönthet úgy, hogy összekapcsolja a [telefonos bejelentkezést](#phone-sign-in-preview)és az [e-mail-bejelentkezést](#email-sign-in). A regisztrációs vagy bejelentkezési oldalon a felhasználó beírhat egy telefonszámot vagy e-mail-címet. A felhasználói bevitel alapján Azure AD B2C viszi a felhasználót a megfelelő folyamatba. 

![Telefonos vagy e-mail-regisztrációs vagy bejelentkezési élmény](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Helyi fiók identitás-szolgáltatói beállításainak konfigurálása

A felhasználói folyamaton belül elérhető helyi identitás-szolgáltatókat úgy is beállíthatja, hogy engedélyezi vagy letiltja a szolgáltatókat (e-mailek, felhasználónevek vagy telefonszámok).  A bérlői szinten több helyi identitás-szolgáltató is engedélyezhető.

A felhasználói folyamatokat csak akkor lehet konfigurálni, ha a helyi fiók identitás-szolgáltatóinak egyikét szeretné egyszerre használni. Minden felhasználói folyamat rendelkezhet egy másik helyi fiók identitás-szolgáltatóval, ha a bérlő szintjén egynél több van engedélyezve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. A **kezelés** területen válassza az **identitás-szolgáltatók** elemet.
1. Az identitás-szolgáltató listában válassza a **helyi fiók** lehetőséget.
1. A **helyi identitásszolgáltató konfigurálása** lapon válassza ki, hogy a felhasználók közül legalább az egyik engedélyezhető identitási típust használja a helyi fiókok létrehozásához a Azure ad B2C-bérlőben.
1. Kattintson a **Mentés** gombra.

## <a name="configure-your-user-flow"></a>A felhasználói folyamat konfigurálása

1. A Azure Portal bal oldali menüjében válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek be szeretné állítani a regisztrációs és bejelentkezési élményét.
1. **Identitás-szolgáltatók** kiválasztása
1. A **helyi fiókok** területen válassza a következők egyikét: **E-mail regisztráció**,  **felhasználói azonosító regisztráció**, **telefonos regisztráció**, **telefonos/e-mail-regisztráció** vagy **nincs**.

### <a name="enable-the-recovery-email-prompt"></a>A helyreállítási e-mail kérésének engedélyezése

Ha a **telefonos regisztráció**, **telefon/e-mail regisztráció** lehetőséget választja, engedélyezze a helyreállítási e-mail-üzenetet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Azure AD B2C a **házirendek** területen válassza a **felhasználói folyamatok** elemet.
1. Válassza ki a felhasználói folyamatot a listából.
1. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
1. A **helyreállítási értesítő e-mailek küldésének engedélyezése a telefonszám-regisztrációhoz és bejelentkezéshez (előzetes verzió)** kattintson a következőkre:
   - **Bekapcsolva** a helyreállítási e-mail-üzenet megjelenítéséhez és a bejelentkezéshez.
   - **Kikapcsolva** a helyreállítási e-mail üzenet elrejtéséhez.
1. Kattintson a **Mentés** gombra.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Az alapszintű csomag beszerzése

Az egyéni házirendek olyan XML-fájlok készletei, amelyeket feltölt a Azure AD B2C bérlőre a felhasználói útvonalak definiálásához. Az alapszintű csomagokat számos előre elkészített szabályzattal biztosítjuk. Töltse le a megfelelő starter-csomagot: 

- [E-mail bejelentkezés](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Felhasználónév-bejelentkezés](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Telefonos bejelentkezés](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Válassza ki [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) függő entitás házirendjét. 
- [Telefonos vagy e-mail-bejelentkezés](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Válassza ki [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) függő entitás házirendjét.

Az alapszintű csomag letöltése után.

1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .

1. Hajtsa végre az [alkalmazás-azonosítók hozzáadása az egyéni házirendhez](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) című szakasz lépéseit az [Egyéni szabályzatok beszerzése Azure Active Directory B2Cban](tutorial-create-user-flows.md?pivots=b2c-custom-policy)című témakörben. Például az `/phone-number-passwordless/` **`Phone_Email_Base.xml`** Előfeltételek, a *IdentityExperienceFramework* és a *ProxyIdentityExperienceFramework* végrehajtásakor regisztrált két alkalmazás **alkalmazás-(ügyfél-) azonosítójának** frissítése.
1. A szabályzat fájljainak feltöltése

::: zone-end

## <a name="next-steps"></a>Következő lépések

- [Külső identitás-szolgáltatók hozzáadása](add-identity-provider.md)
- [Felhasználói folyamat létrehozása](tutorial-create-user-flows.md)
