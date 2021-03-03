---
title: Egyéni attribútumok hozzáadása önkiszolgáló bejelentkezési folyamatokhoz – Azure AD
description: További információ az önkiszolgáló bejelentkezési felhasználói folyamatok attribútumainak testreszabásáról.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b498f8b8512d0202f47dd31ba25cc851ca71e6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644102"
---
# <a name="define-custom-attributes-for-user-flows"></a>Egyéni attribútumok meghatározása felhasználói folyamatokhoz

Az egyes alkalmazásokhoz különböző követelmények vonatkoznak a regisztráció során gyűjteni kívánt információkra. Az Azure AD az attribútumokban tárolt beépített információkkal (például Utónév, vezetéknév, város és irányítószám) rendelkezik. Az Azure AD segítségével kiterjesztheti a vendég fiókokban tárolt attribútumok készletét, ha a külső felhasználó egy felhasználói folyamaton keresztül jelentkezik be.

Létrehozhat egyéni attribútumokat a Azure Portalban, és használhatja azokat az önkiszolgáló bejelentkezési felhasználói folyamatokban. Ezeket az attribútumokat a [Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md)használatával is elolvashatja és elvégezheti. Microsoft Graph API támogatja a bővítmény-attribútumokkal rendelkező felhasználók létrehozását és frissítését. A Graph API-bővítmény attribútumai az egyezmény használatával vannak elnevezve `extension_<extensions-app-id>_attributename` . Például:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

A a `<extensions-app-id>` bérlőre jellemző. Az azonosító megkereséséhez navigáljon a Azure Active Directory > Alkalmazásregisztrációk > minden alkalmazás elemre. Keresse meg a "HRE-Extensions-app" kezdetű alkalmazást, és jelölje ki. Az alkalmazás áttekintés lapján jegyezze fel az alkalmazás (ügyfél) AZONOSÍTÓját.

## <a name="create-a-custom-attribute"></a>Egyéni attribútum létrehozása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. A bal oldali menüben válassza a **külső identitások** lehetőséget.
4. Válassza az **egyéni felhasználói attribútumok** lehetőséget. Megjelenik a rendelkezésre álló felhasználói attribútumok listája.

   ![Felhasználói attribútumok kiválasztása a regisztrációhoz](media/user-flow-add-custom-attributes/user-attributes.png)

5. Attribútum hozzáadásához válassza a **Hozzáadás** lehetőséget.
6. Az **attribútum hozzáadása** panelen adja meg a következő értékeket:

   - **Név** – adja meg az egyéni attribútum nevét (például "Shoesize").
   - **Adattípus** – válassza ki az adattípust (**karakterlánc**, **logikai** vagy **int**).
   - **Leírás** – nem kötelezően megadhatja az egyéni attribútum leírását belső használatra. Ez a leírás nem látható a felhasználó számára.

   ![Attribútum hozzáadása](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Válassza a **Létrehozás** lehetőséget.

Az egyéni attribútum már elérhető a felhasználói attribútumok listájában és a felhasználói folyamatokban való használathoz. Egyéni attribútum csak akkor jön létre, amikor az első alkalommal használja a felhasználói folyamatokban, és nem, amikor hozzáadja a felhasználói attribútumok listájához.

Miután létrehozott egy új felhasználót egy olyan felhasználói folyamat használatával, amely az újonnan létrehozott egyéni attribútumot használja, az objektum [Microsoft Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer)kérdezhető le. Ekkor látnia kell a **ShoeSize** a felhasználói objektumon a regisztráció során gyűjtött attribútumok listájában. Meghívhatja az alkalmazásból az Graph API az adatoknak a felhasználói objektumhoz való hozzáadása után.

## <a name="next-steps"></a>Következő lépések

[Önkiszolgáló bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md)