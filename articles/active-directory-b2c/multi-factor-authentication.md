---
title: Multi-Factor Authentication a Azure Active Directory B2Cban | Microsoft Docs
description: A Multi-Factor Authentication engedélyezése a Azure Active Directory B2C által védett, a felhasználók felé irányuló alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d7bc92af31eb179155fd473356c741f365a07a35
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525144"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Többtényezős hitelesítés engedélyezése az Azure Active Directory B2C-ben

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

A Azure Active Directory B2C (Azure AD B2C) közvetlenül az [Azure multi-Factor Authentication ad](../active-directory/authentication/concept-mfa-howitworks.md) -vel integrálódik, így egy második biztonsági réteget adhat hozzá az alkalmazásaiban való regisztráláshoz és bejelentkezési élményekhez. Egyetlen sornyi kód megírása nélkül engedélyezheti a többtényezős hitelesítést. Ha már létrehozott egy regisztrációs és bejelentkezési felhasználói folyamatot, továbbra is engedélyezheti a többtényezős hitelesítést.

Ez a funkció megkönnyíti az alkalmazások számára a következő forgatókönyvek kezelését:

- Nincs szükség többtényezős hitelesítésre egy alkalmazáshoz való hozzáféréshez, de ehhez egy másikra van szükség. Például az ügyfél be tud jelentkezni egy automatikus biztosítási alkalmazásba egy közösségi vagy helyi fiókkal, de ellenőriznie kell a telefonszámot, mielőtt hozzáfér az ugyanabban a címtárban regisztrált Home Insurance-alkalmazáshoz.
- A többtényezős hitelesítés nem szükséges ahhoz, hogy általánosan hozzáférjen egy alkalmazáshoz, de ehhez a bizalmas részeit is hozzá kell férnie. Például az ügyfél bejelentkezhet egy banki alkalmazásba egy közösségi vagy helyi fiókkal, és ellenőrizheti a fiók egyenlegét, de a hálózati átvitel megkísérlése előtt ellenőriznie kell a telefonszámot.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication beállítása

::: zone pivot="b2c-user-flow"

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A felső menüben a **könyvtár + előfizetés** szűrő használatával válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **felhasználói folyamatok** lehetőséget.
1. Válassza ki azt a felhasználói folyamatot, amelynek engedélyezni szeretné az MFA-t. Például *B2C_1_signinsignup*.
1. Válassza ki a **Tulajdonságok** elemet.
1. A **többtényezős hitelesítés** szakaszban válassza ki a kívánt **MFA-módszert**, majd az **MFA-kényszerítés** területen válassza az **Always on vagy a** **feltételes (ajánlott)** lehetőséget.
   > [!NOTE]
   >
   > - Ha a **feltételes (ajánlott)** lehetőséget választja, hozzá kell adnia [egy feltételes hozzáférési szabályzatot](conditional-access-identity-protection-setup.md#add-a-conditional-access-policy) , és meg kell adnia azokat az alkalmazásokat, amelyekre alkalmazni szeretné a szabályzatot.
   > - A többtényezős hitelesítés (MFA) alapértelmezés szerint le van tiltva a regisztrációs felhasználói folyamatok esetében. Engedélyezheti az MFA használatát a felhasználói folyamatokban a telefonos regisztrációval, de mivel a telefonszámot elsődleges azonosítóként használja, az egyszeri e-mail-jelszó az egyetlen lehetőség a második hitelesítési tényezőhöz.

1. Kattintson a **Mentés** gombra. Az MFA mostantól engedélyezve van ennél a felhasználói folyamatnál.

A **felhasználói folyamat futtatásával** ellenőrizheti a felhasználói élményt. Erősítse meg a következő helyzetet:

A multi-Factor Authentication lépése előtt létrejön egy ügyfél-fiók a bérlőben. A lépés során a rendszer megkéri az ügyfelet, hogy adjon meg egy telefonszámot, és ellenőrizze azt. Ha az ellenőrzés sikeres, a telefonszámot a rendszer a fiókhoz csatolja későbbi használatra. Annak ellenére, hogy az ügyfél megszakítja vagy kiesik, az ügyfél megkérheti, hogy a következő bejelentkezéskor ismét ellenőrizze a telefonszámot a többtényezős hitelesítés engedélyezésekor.

::: zone-end

::: zone pivot="b2c-custom-policy"

Ha engedélyezni szeretné, hogy Multi-Factor Authentication beolvassa a GitHubról az egyéni szabályzat kezdő csomagjait, frissítse a **SocialAndLocalAccountsWithMFA** Starter Pack csomag XML-fájljait a Azure ad B2C bérlői nevével. A **SocialAndLocalAccountsWithMFA**  lehetővé teszi a közösségi, helyi és multi-Factor Authentication lehetőségeket. További információ: Ismerkedés [az egyéni szabályzatokkal Active Directory B2Cban](custom-policy-get-started.md). 

::: zone-end
