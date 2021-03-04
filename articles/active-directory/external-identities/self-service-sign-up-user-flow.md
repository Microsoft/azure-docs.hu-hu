---
title: Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása – Azure AD
description: Felhasználói folyamatokat hozhat létre a szervezete által készített alkalmazásokhoz. Ezután a felhasználók, akik meglátogatják az alkalmazást, megszerezhetik a vendég fiókot a felhasználói folyamaton konfigurált beállítások használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59e573c9b24d9a8b5577b55d143fcaca67952f0
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120759"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz

> [!NOTE]
> A cikkben említett funkciók némelyike Azure Active Directory nyilvános előzetes verziójú funkciói. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A létrehozott alkalmazások esetében olyan felhasználói folyamatokat hozhat létre, amelyek lehetővé teszik, hogy egy felhasználó regisztráljon az alkalmazásra, és hozzon létre egy új vendég fiókot. Az önkiszolgáló regisztrációs felhasználói folyamat meghatározza azokat a lépéseket, amelyeket a felhasználó a regisztráció során követ, az azonosítókat, amelyeket a felhasználók használhatnak, valamint a gyűjteni kívánt felhasználói attribútumokat. Egy vagy több alkalmazást egyetlen felhasználói folyamathoz rendelhet hozzá.

> [!NOTE]
> A felhasználói folyamatokat a szervezet által készített alkalmazásokkal társíthatja. A felhasználói folyamatok nem használhatók Microsoft-alkalmazásokhoz, például a SharePointhoz vagy a Teams szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

### <a name="add-identity-providers-optional"></a>Identitás-szolgáltatók hozzáadása (nem kötelező)

Az Azure AD az önkiszolgáló regisztráció alapértelmezett identitás-szolgáltatója. Ez azt jelenti, hogy a felhasználók alapértelmezés szerint egy Azure AD-fiókkal regisztrálhatnak. Az önkiszolgáló bejelentkezési felhasználói folyamatokban olyan közösségi identitás-szolgáltatókat is használhat, mint például a Google és a Facebook, a Microsoft-fiók (előzetes verzió) és az e-mailek egyszeri jelszava (előzetes verzió).

- [Microsoft-fiók (előzetes verzió) identitás-szolgáltatója](microsoft-account.md)
- [Egyszeri jelszavas hitelesítés e-mailben](one-time-passcode.md)
- [Facebook hozzáadása a közösségi identitású szolgáltatók listájához](facebook-federation.md)
- [A Google hozzáadása a közösségi identitású szolgáltatók listájához](google-federation.md)

### <a name="define-custom-attributes-optional"></a>Egyéni attribútumok definiálása (nem kötelező)

A felhasználói attribútumok a felhasználó által az önkiszolgáló regisztráció során gyűjtött értékek. Az Azure AD számos attribútumot tartalmaz, de létrehozhat egyéni attribútumokat is a felhasználói folyamatokban való használathoz. Ezeket az attribútumokat a Microsoft Graph API használatával is elolvashatja és elvégezheti. Lásd: [Egyéni attribútumok definiálása felhasználói folyamatokhoz](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Önkiszolgáló regisztráció engedélyezése a bérlő számára

Ahhoz, hogy önkiszolgáló regisztrációs felhasználói folyamatot lehessen hozzáadni az alkalmazásaihoz, engedélyeznie kell a szolgáltatást a bérlő számára. Az engedélyezése után a vezérlők elérhetővé válnak a felhasználói folyamat során, amely lehetővé teszi a felhasználói folyamat alkalmazáshoz való hozzárendelését.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. Válassza a **felhasználói beállítások** lehetőséget, majd a **külső felhasználók** területen válassza a **külső együttműködési beállítások kezelése** lehetőséget.
4. Állítsa be a **vendég önkiszolgáló regisztráció engedélyezése felhasználói adatforgalomon keresztül** **beállítást igen** értékre.

   ![Vendég önkiszolgáló regisztrálásának engedélyezése](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Kattintson a **Mentés** gombra.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Felhasználói folyamat létrehozása önkiszolgáló regisztrációhoz

Ezután létre kell hoznia a felhasználói folyamatot az önkiszolgáló regisztrációhoz, és hozzá kell adnia egy alkalmazáshoz.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. A bal oldali menüben válassza a **külső identitások** lehetőséget.
4. Válassza a **felhasználói folyamatok** lehetőséget, majd válassza az **új felhasználói folyamat** elemet.

   ![Új felhasználói folyamat hozzáadása gomb](media/self-service-sign-up-user-flow/new-user-flow.png)

5. A **Létrehozás** lapon adja meg a felhasználói folyamat **nevét** . Vegye figyelembe, hogy a név automatikusan **B2X_1_**.
6. Az **Identity Providers** listán válasszon ki egy vagy több olyan identitás-szolgáltatót, amelyet a külső felhasználók használhatnak az alkalmazásba való bejelentkezéshez. A **Azure Active Directory regisztráció** alapértelmezés szerint ki van választva. (Lásd [még](#before-you-begin) a jelen cikk korábbi részében ismertetett tudnivalókat az identitás-szolgáltatók hozzáadásának megismeréséhez.)
7. A **felhasználói attribútumok** területen válassza ki a felhasználótól gyűjteni kívánt attribútumokat. További attribútumok esetén válassza a **továbbiak megjelenítése** lehetőséget. Válassza például a **továbbiak megjelenítése** lehetőséget, majd az **ország/régió**, a **megjelenítendő név** és az **Irányítószám** elemnél válassza az attribútumok és jogcímek lehetőséget. Válassza az **OK** lehetőséget.

   ![Új felhasználói folyamat létrehozása lap](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> Csak akkor gyűjthet attribútumokat, ha a felhasználó első alkalommal jelentkezik be. A felhasználó regisztrálása után a rendszer többé nem kéri az attribútum-információk gyűjtésére, még akkor sem, ha megváltoztatja a felhasználói folyamatot.

8. Válassza a **Létrehozás** lehetőséget.
9. Az új felhasználói folyamat megjelenik a **felhasználói folyamatok** listájában. Ha szükséges, frissítse az oldalt.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Az attribútumok gyűjteménye űrlap elrendezésének kiválasztása

Megadhatja, hogy az attribútumok milyen sorrendben jelenjenek meg a regisztrációs oldalon. 

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.
2. Válassza ki a **külső identitások** lehetőséget, majd válassza a **felhasználói folyamatok** lehetőséget.
3. Válassza ki az önkiszolgáló bejelentkezési felhasználói folyamatot a listából.
4. A **Testreszabás** **területen válassza a lapelrendezések elemet**.
5. A program felsorolja a begyűjteni kívánt attribútumokat. A Megjelenítés sorrendjének módosításához válasszon ki egy attribútumot, **majd válassza a** feljebb, a **lejjebb,** **a feljebb** vagy a **lentre ugrás** lehetőséget.
6. Kattintson a **Mentés** gombra.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Alkalmazások hozzáadása az önkiszolgáló bejelentkezési felhasználói folyamathoz

Most már hozzárendelheti az alkalmazásokat a felhasználói folyamathoz.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. A bal oldali menüben válassza a **külső identitások** lehetőséget.
4. Az **önkiszolgáló regisztráció** területen válassza a **felhasználói folyamatok** lehetőséget.
5. Válassza ki az önkiszolgáló bejelentkezési felhasználói folyamatot a listából.
6. A bal oldali menüben a **használat** alatt válassza az **alkalmazások** lehetőséget.
7. Válassza az **alkalmazás hozzáadása** lehetőséget.

   ![Alkalmazás társítása a felhasználói folyamathoz](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Válassza ki az alkalmazást a listából. Vagy használja a keresőmezőt az alkalmazás megkereséséhez, majd jelölje ki.
9. Kattintson a **Kiválasztás** elemre.

## <a name="next-steps"></a>Következő lépések

- [A Google hozzáadása a közösségi identitású szolgáltatók listájához](google-federation.md)
- [Facebook hozzáadása a közösségi identitású szolgáltatók listájához](facebook-federation.md)
- [API-összekötők használata a felhasználói folyamatok webes API-kon keresztüli testreszabásához és bővítéséhez](api-connectors-overview.md)
- [Egyéni jóváhagyási munkafolyamat hozzáadása a felhasználói folyamathoz](self-service-sign-up-add-approvals.md)
