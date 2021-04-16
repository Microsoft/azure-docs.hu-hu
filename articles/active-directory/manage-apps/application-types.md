---
title: Alkalmazások megtekintése a Azure Active Directory identitáskezeléshez
description: Tudja meg, hogyan lehet megtekinteni az összes alkalmazást a Azure Active Directory identitáskezeléshez való használatával.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378073"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Alkalmazások megtekintése az Azure AD-bérlővel az identitáskezeléshez
Az [alkalmazáskezelés gyorsútmutató-sorozata](view-applications-portal.md) végigvezeti az alapvető ismereteken. Ebből a cikkből megtudhatja, hogyan tekinteni meg az összes alkalmazást az Azure AD-bérlő identitáskezeléshez való használatával. Ez a cikk egy kicsit mélyebben is betekeri a megtalált alkalmazástípusokat.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Miért jelenik meg egy adott alkalmazás az összes alkalmazás listájában?
Ha minden alkalmazásra **szűr,** a **Minden** alkalmazás **lista** a bérlő összes szolgáltatásnév-objektumát megjeleníti. A szolgáltatásnév-objektumok többféleképpen is megjelenhetnek a listában:
- Amikor alkalmazásokat ad hozzá az alkalmazásgyűjteményből, beleértve a következőket:
   - **Azure AD – Vállalati alkalmazások** – A  bérlőhöz az Azure AD portál Vállalati alkalmazások lehetőségének használatával hozzáadott alkalmazások. Általában az SAML szabvány használatával integrált alkalmazások.
   - **Azure AD – Alkalmazásregisztrációk** – A bérlőhöz az Azure AD portál Alkalmazásregisztrációk **lehetőségének** használatával hozzáadott alkalmazások. Általában egyénileg fejlesztett alkalmazások open ID Connect és OAuth szabványokkal.
   - **alkalmazásproxy alkalmazások** – A helyszíni környezetben futó alkalmazás, amely számára biztonságos egyszeri bejelentkezést kíván biztosítani kívülről
- Amikor regisztrál egy, az alkalmazással integrált külső alkalmazást, vagy bejelentkezik Azure Active Directory. Ilyen például a [Smartsheet vagy](https://app.smartsheet.com/b/home) a [DocuSign.](https://www.docusign.net/member/MemberLogin.aspx)
- Microsoft-alkalmazások, például Microsoft 365.
- Új alkalmazásregisztráció hozzáadásakor hozzon létre egy egyénileg fejlesztett alkalmazást az [Alkalmazásregisztrációs adatbázis használatával](../develop/quickstart-register-app.md)
- Új alkalmazásregisztráció hozzáadásakor hozzon létre egy egyénileg fejlesztett alkalmazást a [2.0-s alkalmazásregisztrációs portál használatával](../develop/quickstart-register-app.md)
- Egy alkalmazás hozzáadásakor a fejlesztés a Visual Studio hitelesítési módszereinek vagy ASP.NET [szolgáltatások](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) használatával [történik](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Amikor létrehoz egy szolgáltatásnév-objektumot az [Azure AD PowerShell-modullal](/powershell/azure/active-directory/install-adv2)
- Ha [beleegyezik, hogy egy alkalmazás](../develop/howto-convert-app-to-be-multi-tenant.md) rendszergazdaként használja az adatokat a bérlőben
- Amikor egy [felhasználó beleegyezik, hogy egy alkalmazás](../develop/howto-convert-app-to-be-multi-tenant.md) adatokat használjon a bérlőben
- Ha engedélyez bizonyos adatokat tároló szolgáltatásokat a bérlőben. Ilyen például az Új jelszó visszaállítása, amely szolgáltatásnévként van modellezett a jelszó-visszaállítási szabályzat biztonságos tárolása érdekében.

További információ az alkalmazások címtárhoz való hozzáadásának mikéntjről és okról: Alkalmazások hozzáadása az [Azure AD-hez.](../develop/active-directory-how-applications-are-added.md)

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése Azure Active Directory](what-is-application-management.md)
