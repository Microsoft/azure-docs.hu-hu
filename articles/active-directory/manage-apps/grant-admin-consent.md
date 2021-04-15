---
title: Bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz – Azure AD
description: Megtudhatja, hogyan adhat bérlői szintű jóváhagyást egy alkalmazáshoz, hogy a végfelhasználók ne kapják meg a hozzájárulásukat, amikor bejelentkeznek egy alkalmazásba.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374182"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bérlőszintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz

  Megtudhatja, hogyan adhat bérlői szintű rendszergazdai jóváhagyást egy alkalmazáshoz. Ez a cikk ennek különböző módjait adja meg.

Az alkalmazások jóváhagyásával kapcsolatos további információkért lásd: Azure Active Directory [hozzájárulási keretrendszer.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Előfeltételek

A bérlői szintű rendszergazdai jóváhagyás megadásához olyan felhasználóként kell bejelentkeznie, aki jogosult a szervezet nevében történő jóváhagyásra. Ebbe beletartozik [a globális rendszergazda](../roles/permissions-reference.md#global-administrator) és a [kiemelt](../roles/permissions-reference.md#privileged-role-administrator)szerepkör-rendszergazda, valamint egyes alkalmazások esetén az alkalmazás-rendszergazda és a [felhőalkalmazás-rendszergazda.](../roles/permissions-reference.md#cloud-application-administrator) [](../roles/permissions-reference.md#application-administrator) A felhasználók bérlői szintű hozzájárulás megadására is engedélyt [](../roles/custom-create.md) kaphatnak, ha olyan egyéni címtárszerepkedőhöz vannak rendelve, amely engedélyt biztosít az [alkalmazásoknak.](../roles/custom-consent-permissions.md)

> [!WARNING]
> Ha bérlői szintű rendszergazdai jóváhagyást ad egy alkalmazáshoz, az alkalmazás és az alkalmazás közzétevője hozzáférést biztosít a szervezet adataihoz. A hozzájárulás megadása előtt gondosan tekintse át az alkalmazás által kért engedélyeket.

> [!IMPORTANT]
> Ha egy alkalmazás bérlői szintű rendszergazdai jóváhagyást kapott, minden felhasználó bejelentkezik az alkalmazásba, kivéve, ha úgy lett konfigurálva, hogy felhasználói hozzárendelést követelje meg. Annak korlátozásához, hogy mely felhasználók jelentkeznek be egy alkalmazásba, felhasználói hozzárendelésre van szükség, majd felhasználókat vagy csoportokat kell hozzárendelni az alkalmazáshoz. További információ: Felhasználók és csoportok [hozzárendelésének módszerei.](./assign-user-or-group-access-portal.md)

## <a name="grant-admin-consent-from-the-azure-portal"></a>Rendszergazdai jóváhagyás megadása a Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Rendszergazdai jóváhagyás megadása vállalati alkalmazásokban

A bérlői szintű rendszergazdai  jóváhagyást vállalati alkalmazásokon keresztül is meg lehet adni, ha az alkalmazás már ki lett építve a bérlőben. Például üzembe lehet építve egy alkalmazást a bérlőben, ha legalább egy felhasználó már hozzájárult az alkalmazáshoz. További információ: Hogyan és miért adnak hozzá alkalmazásokat a [Azure Active Directory.](../develop/active-directory-how-applications-are-added.md)

Bérlői szintű rendszergazdai jóváhagyás megadása a vállalati alkalmazásokban felsorolt **alkalmazásokhoz:**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális [rendszergazdaként,](../roles/permissions-reference.md#global-administrator)alkalmazás-rendszergazdaként [vagy](../roles/permissions-reference.md#application-administrator) [felhőalkalmazás-rendszergazdaként.](../roles/permissions-reference.md#cloud-application-administrator)
2. Válassza **a Azure Active Directory,** majd **a Vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jóváhagyást kíván adni.
4. Válassza **az Engedélyek lehetőséget,** majd kattintson a **Rendszergazdai hozzájárulás megadása elemre.**
5. Gondosan tekintse át az alkalmazás által megkövetelt engedélyeket.
6. Ha elfogadja az alkalmazás által megkövetelt engedélyeket, adja meg a hozzájárulást. Ha nem, kattintson a **Mégse gombra,** vagy zárja be az ablakot.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás vállalati alkalmazásokon keresztüli megadása visszavon minden olyan engedélyt, amely korábban bérlői szintű volt.  Azok az engedélyek, amelyeket korábban a felhasználók saját nevükben adtak meg, nem lesznek hatással. 

### <a name="grant-admin-consent-in-app-registrations"></a>Rendszergazdai jóváhagyás megadása a Alkalmazásregisztrációk

A szervezet által fejlesztett vagy közvetlenül az Azure AD-bérlőben regisztrált alkalmazásokhoz bérlői  szintű rendszergazdai jóváhagyást is Alkalmazásregisztrációk a Azure Portal.

Bérlői szintű rendszergazdai jóváhagyás megadása a **Alkalmazásregisztrációk:**

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális [rendszergazdaként,](../roles/permissions-reference.md#global-administrator)alkalmazás-rendszergazdaként [vagy](../roles/permissions-reference.md#application-administrator) [felhőalkalmazás-rendszergazdaként.](../roles/permissions-reference.md#cloud-application-administrator)
2. Válassza **a Azure Active Directory,** majd **Alkalmazásregisztrációk** lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jóváhagyást kíván adni.
4. Válassza **az API-engedélyek lehetőséget,** majd kattintson a **Rendszergazdai hozzájárulás megadása elemre.**
5. Gondosan tekintse át az alkalmazás által megkövetelt engedélyeket.
6. Ha elfogadja az alkalmazás által megkövetelt engedélyeket, adja meg a hozzájárulást. Ha nem, kattintson a **Mégse gombra,** vagy zárja be az ablakot.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás Alkalmazásregisztrációk **visszavon** minden olyan engedélyt, amely korábban bérlői szintű volt. A felhasználók által a saját nevükben korábban megadott engedélyekre ez nem lesz hatással. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Az URL-cím megépítése a bérlői szintű rendszergazdai jóváhagyás megadásához

Ha a bérlői szintű rendszergazdai jóváhagyást a fent leírt bármelyik módszerrel megadja, megnyílik egy ablak a Azure Portal a bérlői szintű rendszergazdai hozzájárulás megadására. Ha ismeri az alkalmazás ügyfél-azonosítóját (más néven alkalmazásazonosítóját), ugyanazt az URL-címet is felépítheti a bérlői szintű rendszergazdai jóváhagyás megadásához.

A bérlői szintű rendszergazdai hozzájárulás URL-címe a következő formátumot követi:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

ahol:

* `{client-id}` Az az alkalmazás ügyfél-azonosítója (más néven alkalmazásazonosító).
* `{tenant-id}` A a szervezet bérlőazonosítója vagy bármely ellenőrzött tartománynév.

Mint mindig, a jóváhagyás megadása előtt gondosan tekintse át az alkalmazás által kért engedélyeket.

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás ezen az URL-címen keresztüli megadása visszavon minden olyan engedélyt, amely korábban bérlői szintű volt. A felhasználók által a saját nevükben korábban megadott engedélyekre ez nem lesz hatással. 

## <a name="next-steps"></a>Következő lépések

[A végfelhasználók alkalmazásokra vonatkozó hozzájárulásának konfigurálása](configure-user-consent.md)

[A rendszergazdai jóváhagyás munkafolyamatának konfigurálása](configure-admin-consent-workflow.md)

[Engedélyek és jóváhagyás a Microsoft identitásplatformján](../develop/v2-permissions-and-consent.md)

[Azure AD a Microsoft Q&A-n](/answers/topics/azure-active-directory.html)
