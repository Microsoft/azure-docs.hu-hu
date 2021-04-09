---
title: B2B-felhasználók hozzáférésének biztosítása a helyszíni alkalmazásokhoz – Azure AD
description: Bemutatja, hogyan biztosítható a felhőalapú B2B-felhasználók számára a helyszíni alkalmazások elérése az Azure AD B2B együttműködéssel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131020"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Hozzáférés biztosítása az Azure AD-beli B2B-felhasználóknak a helyszíni alkalmazásokhoz

Azure Active Directory (Azure AD) B2B együttműködési képességeket használó szervezetként, amellyel meghívhatja a vendég felhasználókat a partnerszervezetektól az Azure AD-be, mostantól elérhetővé teheti a B2B-felhasználók számára a helyszíni alkalmazásokhoz való hozzáférést. Ezek a helyszíni alkalmazások SAML-alapú hitelesítést vagy integrált Windows-hitelesítést (IWA) használhatnak a Kerberos által korlátozott delegálással (KCD).

## <a name="access-to-saml-apps"></a>Hozzáférés az SAML-alkalmazásokhoz

Ha a helyszíni alkalmazás SAML-alapú hitelesítést használ, egyszerűen elérhetővé teheti ezeket az alkalmazásokat az Azure AD B2B együttműködési felhasználók számára a Azure Portalon keresztül.

A következőket kell tennie:

- Integrálja az alkalmazást az SAML használatával az [SAML-alapú egyszeri bejelentkezés konfigurálása](../manage-apps/configure-saml-single-sign-on.md)című cikkben leírtak szerint. Ügyeljen arra, hogy a **bejelentkezési URL-cím** értékének milyen értékét kell használnia.
-  Az Azure AD Application Proxy használatával tegye közzé a helyszíni alkalmazást a hitelesítési forrásként konfigurált **Azure Active Directory** . Útmutatásért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](../manage-apps/application-proxy-add-on-premises-application.md). 

   A **belső URL-cím** beállításakor használja a nem katalógus alkalmazás sablonjában megadott bejelentkezési URL-címet. Így a felhasználók a szervezet határain kívülről is hozzáférhetnek az alkalmazáshoz. Az alkalmazásproxy végrehajtja a helyszíni alkalmazás SAML-alapú egyszeri bejelentkezését.
 
   ![A helyszíni Alkalmazásbeállítások belső URL-címét és hitelesítését jeleníti meg](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Hozzáférés IWA-és KCD-alkalmazásokhoz

Ahhoz, hogy a B2B-felhasználók hozzáférhessenek az integrált Windows-hitelesítéssel és a Kerberos által korlátozott delegálással védett helyszíni alkalmazásokhoz, a következő összetevőkre van szükség:

- **Hitelesítés az Azure ad Application Proxyon keresztül**. A B2B-felhasználóknak képesnek kell lenniük a hitelesítésre a helyszíni alkalmazásban. Ehhez közzé kell tennie a helyszíni alkalmazást az Azure AD Application Proxyon keresztül. További információ: [oktatóanyag: helyszíni alkalmazás hozzáadása távoli eléréshez alkalmazásproxy használatával](../manage-apps/application-proxy-add-on-premises-application.md).
- **Engedélyezés vállalatközi felhasználói objektumon keresztül a helyszíni címtárban**. Az alkalmazásnak képesnek kell lennie a felhasználói hozzáférés ellenőrzéséhez, és hozzáférést kell biztosítania a megfelelő erőforrásokhoz. Az engedélyezés végrehajtásához a IWA és a KCD felhasználói objektumra van szükség a helyszíni Windows Server Active Directory. Az [egyszeri bejelentkezés a KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)-mel való használatával című cikkben leírtak szerint az alkalmazásproxy erre a felhasználói objektumra van szüksége a felhasználó megszemélyesítéséhez és egy Kerberos-token beszerzéséhez az alkalmazáshoz. 

   > [!NOTE]
   > Az Azure-AD Application Proxy konfigurálásakor győződjön meg arról, hogy a **delegált bejelentkezési identitás** az integrált Windows-hitelesítés (IWA) egyszeri bejelentkezési konfigurációjában a **felhasználó egyszerű neve** (alapértelmezett) értékre van beállítva.

   A B2B felhasználói forgatókönyvben két módszer áll rendelkezésre, amelyek segítségével létrehozhatja az engedélyezéshez szükséges vendég felhasználói objektumokat a helyszíni címtárban:

   - Microsoft Identity Manager (webszolgáltatások) és a Microsoft Graph-hez készült felügyeleti ügynök. 
   - [Egy PowerShell-parancsfájl](#create-b2b-guest-user-objects-through-a-script-preview). A szkript használata egy olyan könnyűsúlyú megoldás, amely nem igényli a webalkalmazást. 

A következő ábra áttekintést nyújt arról, hogyan működik együtt az Azure AD Application Proxy és a VÁLLALATKÖZI felhasználói objektum létrehozása a helyszíni címtárban, hogy VÁLLALATKÖZI felhasználói hozzáférést biztosítson a helyszíni IWA és KCD-alkalmazásokhoz. A számozott lépések részletes leírását a diagram alatt találja.

![A webalkalmazási és B2B parancsfájl-megoldások ábrája](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  A rendszer meghívja a contoso bérlőt a partner szervezet (a fabrikam bérlője) felhasználójának.
2.  A rendszer létrehoz egy vendég felhasználói objektumot a contoso-bérlőben (például egy olyan felhasználói objektumot, amelynek UPN-je guest_fabrikam. com # EXT # @contoso.onmicrosoft.com ).
3.  A fabrikam vendég a contoso-ból vagy a B2B PowerShell-parancsfájllal lett importálva.
4.  A fabrikam vendég felhasználói objektum (vendég # EXT #) képviselete vagy lábnyoma a helyszíni címtárban, a Contoso.com-ben vagy a B2B PowerShell-parancsfájllal hozható létre.
5.  A vendég felhasználó hozzáfér a helyszíni alkalmazáshoz, a app.contoso.com.
6.  A hitelesítési kérelem a Kerberos által korlátozott delegálással hitelesíthető az alkalmazásproxy használatával. 
7.  Mivel a vendég felhasználói objektum helyileg létezik, a hitelesítés sikeres.

### <a name="lifecycle-management-policies"></a>Életciklus-kezelési szabályzatok

A helyszíni B2B felhasználói objektumokat életciklus-kezelési házirendekkel kezelheti. Például:

- Beállíthatja a többtényezős hitelesítési (MFA) házirendeket a vendég felhasználó számára, hogy az MFA-t használja az alkalmazásproxy hitelesítése során. További információ: [feltételes hozzáférés vállalatközi együttműködéssel rendelkező felhasználók](conditional-access.md)számára.
- A felhőalapú B2B-felhasználó által végrehajtott szponzorálások, hozzáférési felülvizsgálatok, fiókok ellenőrzése stb. a helyszíni felhasználókra is érvényesek. Ha például a Felhőbeli felhasználót az életciklus-kezelési szabályzatok segítségével törli, a helyszíni felhasználót is törli a helyi felhasználó, vagy a Azure AD Connect Sync használatával. További információ: [a vendég hozzáférésének kezelése az Azure ad hozzáférési felülvizsgálatokkal](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>VÁLLALATKÖZI vendég felhasználói objektumok létrehozása a webszolgáltatáson keresztül

További információ a (z) 2016 Service Pack 1 és a Rendszerfelügyeleti webszolgáltatások felügyeleti ügynökének használatáról Microsoft Graph a vendég felhasználói objektumok létrehozásához a helyszíni címtárban: Azure AD-beli vállalatközi [(B2B) együttműködés a Microsoft Identity Manager (platform) 2016 SP1 és az Azure Application proxy](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)használatával.

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>VÁLLALATKÖZI vendég felhasználói objektumok létrehozása parancsfájl használatával (előzetes verzió)

Létezik egy PowerShell-minta parancsfájl, amely kiindulási pontként használható a vendég felhasználói objektumok létrehozásához a helyszíni Active Directory.

Töltse le a parancsfájlt és a Readme fájlt a [Microsoft Identity Manager 2016 és a Forefront Identity Manager 2010 R2-es összekötőről](https://www.microsoft.com/download/details.aspx?id=51495). A letöltési csomagban válassza ki a **parancsfájlt és a readme elemet az Azure ad B2B-felhasználók on-prem.zipfájljának lekéréséhez** .

A szkript használata előtt győződjön meg arról, hogy áttekinti az előfeltételeket és a fontos szempontokat a társított readme fájlban. Továbbá Ismerje meg, hogy a szkript csak mintaként érhető el. A fejlesztői csapatnak vagy partnernek a futtatása előtt testre kell szabnia és ellenőriznie kell a parancsfájlt.

## <a name="license-considerations"></a>Licenctel kapcsolatos megfontolások

Győződjön meg arról, hogy rendelkezik a megfelelő ügyféllicencekkel a külső vendég felhasználók számára, akik hozzáférnek a helyszíni alkalmazásokhoz. További információ: az [ügyfél-hozzáférési licencek és a felügyeleti licencek](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)"külső összekötők" szakasza. Forduljon a Microsoft képviselőjéhez vagy a helyi viszonteladóhoz az adott licencelési igényekkel kapcsolatban.

## <a name="next-steps"></a>Következő lépések

- [Azure Active Directory B2B-együttműködés hibrid szervezeteknél](hybrid-organizations.md)

- A Azure AD Connect áttekintését lásd: a [helyszíni címtárak integrálása Azure Active Directoryokkal](../hybrid/whatis-hybrid-identity.md).