---
title: A helyi fiók identitás-szolgáltatójának beállítása
titleSuffix: Azure AD B2C
description: Adja meg a helyi fiók hitelesítéséhez használható identitási típusokat (e-mail-cím, Felhasználónév, telefonszám), amikor felhasználói folyamatokat állít be a Azure Active Directory B2C bérlőben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557225"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Telefonos regisztráció és bejelentkezés beállítása felhasználói folyamatokhoz (előzetes verzió)

> [!NOTE]
> A felhasználói folyamatok telefonos regisztrációs és bejelentkezési és helyreállítási e-mail-funkciói nyilvános előzetes verzióban érhetők el.

Az e-mailek és a felhasználónevek mellett a telefonszámot engedélyezheti a bérlők számára, ha telefonos regisztrációt és bejelentkezést végez a helyi fiók identitás-szolgáltatójában. Miután engedélyezte a telefonos regisztrációt és a helyi fiókokhoz való bejelentkezést, telefonos regisztrációt adhat hozzá a felhasználói folyamatokhoz.

A telefonos regisztráció és a felhasználói folyamaton belüli bejelentkezés beállítása a következő lépéseket foglalja magában:

- [Adja meg a telefonos regisztrációt és a bejelentkezési bérlőt](#configure-phone-sign-up-and-sign-in-tenant-wide) a helyi fiók identitás-szolgáltatóján, hogy fogadja a telefonszámot a felhasználó identitása alapján. 

- Telefonos [regisztráció hozzáadása a felhasználói folyamathoz](#add-phone-sign-up-to-a-user-flow) annak lehetővé tétele érdekében, hogy a felhasználók a telefonszám alapján regisztráljanak az alkalmazásban.

- [Engedélyezze a helyreállítási e-mail-kérést (előzetes verzió)](#enable-the-recovery-email-prompt-preview) , hogy a felhasználók megadják a fiókjuk helyreállításához használható e-mailt, amikor nem rendelkeznek a telefonjára.

A többtényezős hitelesítés (MFA) alapértelmezés szerint le van tiltva a felhasználói folyamatok telefonos regisztrációval való konfigurálásakor. Engedélyezheti az MFA használatát a felhasználói folyamatokban a telefonos regisztrációval, de mivel a telefonszámot elsődleges azonosítóként használja, az egyszeri e-mail-jelszó az egyetlen lehetőség a második hitelesítési tényezőhöz.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>A telefonos regisztráció és a bejelentkezési bérlő széles körű konfigurálása

Az e-mail-regisztráció alapértelmezés szerint engedélyezve van a helyi fiók identitás-szolgáltatójának beállításaiban. Az e-mail-regisztráció, a Felhasználónév vagy a telefonszám kiválasztásával és törlésével módosíthatja a bérlő által támogatott identitás-típusokat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.

3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.

4. A **kezelés** területen válassza az **identitás-szolgáltatók** elemet.

5. Az identitás-szolgáltató listában válassza a **helyi fiók** lehetőséget.

   ![Identitás-szolgáltatók válassza a helyi fiók lehetőséget](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. A **helyi identitásszolgáltató konfigurálása** lapon győződjön meg róla, hogy a **telefon** lehetőség van kiválasztva, hogy a felhasználók a helyi fiókjaikat a Azure ad B2C bérlőben hozza létre.

   ![Az engedélyezett identitások típusának kiválasztása](media/phone-authentication-user-flows/configure-local-idp.png)

1. Kattintson a **Mentés** gombra.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Telefonos regisztráció hozzáadása felhasználói folyamathoz

Miután hozzáadta a telefonos regisztrációt identitási beállításként a helyi fiókoknál, felveheti azt a felhasználói folyamatokhoz, amennyiben azok a legújabb **ajánlott** felhasználói folyamatok. Az alábbi példa bemutatja, hogyan adhat hozzá telefonos regisztrációt az új felhasználói folyamatokhoz. Hozzáadhat azonban telefonos regisztrációt is a meglévő ajánlott verziójú felhasználói folyamatokhoz (válassza **Felhasználókövetés**  >  *felhasználói folyamat neve*  >  **Identity Providers**  >  **helyi fiók telefonos regisztráció**) lehetőséget. 

Az alábbi példa bemutatja, hogyan adhat hozzá telefonos regisztrációt egy új felhasználói folyamathoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

    ![B2C-bérlő, címtár és előfizetés panel, Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
4. A **házirendek** területen válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.

    ![Felhasználói folyamatok lap a portálon új felhasználói folyamat gomb kiemelve](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. A **felhasználói folyamat létrehozása** lapon válassza a **regisztráció és bejelentkezés** felhasználói folyamat elemet.

    ![Válassza ki a felhasználói folyamat lapot a regisztrációs és bejelentkezési folyamat kiemelésével](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. **A verzió kiválasztása** területen válassza az **ajánlott** lehetőséget, majd válassza a **Létrehozás** lehetőséget. ([További](user-flow-versions.md) információ a felhasználói folyamatok verzióiról.)

    ![Felhasználói folyamat létrehozása gomb](./media/phone-authentication-user-flows/select-version.png)

7. Adja meg a felhasználói folyamat **nevét** . Például: *signupsignin1*.
8. Az **Identity Providers** szakaszban a **helyi fiókok** területen válassza a **telefonos regisztráció** lehetőséget.

   ![A felhasználói folyamat telefonos regisztrációja lehetőség van kiválasztva](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. A **közösségi identitás-szolgáltatók** területen válassza ki az összes olyan személyazonosság-szolgáltatót, amelyet engedélyezni szeretne ehhez a felhasználói folyamathoz.

   > [!NOTE]
   > A többtényezős hitelesítés (MFA) alapértelmezés szerint le van tiltva a regisztrációs felhasználói folyamatok esetében. Engedélyezheti az MFA-t egy telefonos regisztrációs felhasználói folyamat számára, de mivel a telefonszámot elsődleges azonosítóként használja, a második hitelesítési tényező esetében az egyetlen lehetőség a levelezés egyszeri jelszava.

1. A **felhasználói attribútumok és jogkivonatok jogcímei** szakaszban válassza ki azokat a jogcímeket és attribútumokat, amelyeket szeretne összegyűjteni, majd a felhasználótól a regisztráció során elküldeni. Válassza például a **továbbiak megjelenítése** lehetőséget, majd az **ország/régió**, a **megjelenítendő név** és az **Irányítószám** elemnél válassza az attribútumok és jogcímek lehetőséget. Válassza az **OK** lehetőséget.

1. A felhasználói folyamat hozzáadásához válassza a **Létrehozás** lehetőséget. A *B2C_1* előtagja automatikusan előtagértéke a nevet.

## <a name="enable-the-recovery-email-prompt-preview"></a>A helyreállítási e-mail kérdésének engedélyezése (előzetes verzió)

Ha engedélyezi a telefonos regisztrációt és a bejelentkezést a felhasználói folyamatokban, érdemes engedélyezni a helyreállítási e-mail funkciót is. Ezzel a funkcióval a felhasználók megadhatnak egy e-mail-címet, amellyel helyreállíthatja a fiókját, amikor nem rendelkeznek a telefonjára. Ezt az e-mail-címet csak a fiók-helyreállításhoz használja a rendszer. Nem használható a bejelentkezéshez.

- Ha a helyreállítási e-mail-üzenet **be van kapcsolva**, a rendszer első alkalommal regisztrál egy felhasználót, hogy ellenőrizze a biztonsági mentési e-mailt. Az a felhasználó, aki még nem adott meg helyreállítási e-mailt, a következő bejelentkezéskor a rendszer kéri, hogy ellenőrizze a biztonsági mentési e-maileket.

- Ha a helyreállítási e-mail **ki van kapcsolva**, a regisztrációt vagy bejelentkezést kérő felhasználó nem jelenik meg a helyreállítási e-mail-üzenetben.
 
A helyreállítási e-mail-kérést a felhasználói folyamat tulajdonságaiban engedélyezheti.

> [!NOTE]
> Mielőtt elkezdené, győződjön meg arról, hogy a fent ismertetett módon [adta hozzá a felhasználói folyamathoz a telefonos regisztrációt](#add-phone-sign-up-to-a-user-flow) .

### <a name="to-enable-the-recovery-email-prompt"></a>A helyreállítási e-mail kérdésének engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
3. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
4. Azure AD B2C a **házirendek** területen válassza a **felhasználói folyamatok** elemet.
5. Válassza ki a felhasználói folyamatot a listából.
6. A **Beállítások** alatt válassza a **Tulajdonságok** elemet.
7. A **helyreállítási értesítő e-mailek küldésének engedélyezése a telefonszám-regisztrációhoz és bejelentkezéshez (előzetes verzió)** kattintson a következőkre:

   - **Bekapcsolva** a helyreállítási e-mail-üzenet megjelenítéséhez és a bejelentkezéshez.
   - **Kikapcsolva** a helyreállítási e-mail üzenet elrejtéséhez.

    ![Felhasználói folyamatok tulajdonságai a helyreállítási e-mailek engedélyezésével](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Kattintson a **Mentés** gombra.

### <a name="to-test-the-recovery-email-prompt"></a>A helyreállítási e-mail-üzenet tesztelése

Miután engedélyezte a telefonos regisztrációt és a bejelentkezést, valamint a helyreállítási e-mail-kérést a felhasználói folyamaton, a felhasználói **folyamat futtatásával** tesztelheti a felhasználói élményt.

1. Válassza a **szabályzatok**  >  **felhasználói folyamatok** lehetőséget, majd válassza ki a létrehozott felhasználói folyamatot. A felhasználói folyamat áttekintése lapon válassza a **felhasználói folyamat futtatása** lehetőséget.

2. Az **alkalmazás** lapon válassza ki az 1. lépésben regisztrált webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és ellenőrizze a következő viselkedést:

   - A rendszer az első alkalommal regisztráló felhasználót kéri a helyreállítási e-mailek megadására. 
   - Az a felhasználó, aki már regisztrált, de nem adott meg helyreállítási e-mailt, a rendszer megkéri, hogy adjon meg egyet a bejelentkezéskor.

4. Adjon meg egy e-mail-címet, majd válassza az **ellenőrző kód küldése** lehetőséget. Ellenőrizze, hogy a rendszer elküldte-e a kódot a megadott e-mail-fiókba. Kérje le a kódot, és írja be az **ellenőrző kód** mezőbe. Ezután válassza a **kód ellenőrzése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Külső identitás-szolgáltatók hozzáadása](add-identity-provider.md)
- [Felhasználói folyamat létrehozása](tutorial-create-user-flows.md)