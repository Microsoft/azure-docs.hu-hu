---
title: Multi-Factor Authentication a Azure Active Directory B2Cban | Microsoft Docs
description: A Multi-Factor Authentication engedélyezése a Azure Active Directory B2C által védett, a felhasználók felé irányuló alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c150235dc6b867a69b539aba9c90d666122a210
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170446"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Multi-Factor Authentication engedélyezése Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) közvetlenül az [Azure multi-Factor Authentication ad](../active-directory/authentication/concept-mfa-howitworks.md) -vel integrálódik, így egy második biztonsági réteget adhat hozzá az alkalmazásaiban való regisztráláshoz és bejelentkezési élményekhez. A többtényezős hitelesítés engedélyezése egyetlen sor kód írása nélkül. Ha már létrehozott egy regisztrációs és bejelentkezési felhasználói folyamatot, továbbra is engedélyezheti a többtényezős hitelesítést.

Ez a funkció megkönnyíti az alkalmazások számára a következő forgatókönyvek kezelését:

- Nincs szükség többtényezős hitelesítésre egy alkalmazáshoz való hozzáféréshez, de ehhez egy másikra van szükség. Például az ügyfél be tud jelentkezni egy automatikus biztosítási alkalmazásba egy közösségi vagy helyi fiókkal, de ellenőriznie kell a telefonszámot, mielőtt hozzáfér az ugyanabban a címtárban regisztrált Home Insurance-alkalmazáshoz.
- A többtényezős hitelesítés nem szükséges ahhoz, hogy általánosan hozzáférjen egy alkalmazáshoz, de ehhez a bizalmas részeit is hozzá kell férnie. Például az ügyfél bejelentkezhet egy banki alkalmazásba egy közösségi vagy helyi fiókkal, és ellenőrizheti a fiók egyenlegét, de a hálózati átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication beállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek engedélyezni szeretné az MFA-t. Például *B2C_1_signinsignup*.
1. Válassza ki a **Tulajdonságok** elemet.
1. A **többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert**, majd az **MFA-kényszerítés** területen válassza az **Always on vagy a** **[feltételes](conditional-access-user-flow.md) (ajánlott)** lehetőséget. A feltételes hozzáféréshez hozzon létre egy [feltételes hozzáférési](conditional-access-identity-protection-setup.md) szabályzatot, és határozza meg azokat az alkalmazásokat, amelyekre alkalmazni szeretné a szabályzatot. 
1. Kattintson a Mentés gombra. Az MFA mostantól engedélyezve van ennél a felhasználói folyamatnál.

A **felhasználói folyamat futtatásával** ellenőrizheti a felhasználói élményt. Erősítse meg a következő helyzetet:

A multi-Factor Authentication lépése előtt létrejön egy ügyfél-fiók a bérlőben. A lépés során a rendszer megkéri az ügyfelet, hogy adjon meg egy telefonszámot, és ellenőrizze azt. Ha az ellenőrzés sikeres, a telefonszámot a rendszer a fiókhoz csatolja későbbi használatra. Annak ellenére, hogy az ügyfél megszakítja vagy kiesik, az ügyfél megkérheti, hogy a következő bejelentkezéskor ismét ellenőrizze a telefonszámot a többtényezős hitelesítés engedélyezésekor.