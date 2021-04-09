---
title: Facebook hozzáadása identitás-szolgáltatóként – Azure AD
description: A összevonása a Facebook használatával lehetővé teheti a külső felhasználók (vendégek) számára, hogy saját Facebook-fiókjával jelentkezzenek be az Azure AD-alkalmazásokba.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcacfc8cede0ab91241fd431aace10cbbfbea916
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709684"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Facebook hozzáadása identitás-szolgáltatóként külső identitásokhoz

Hozzáadhat Facebook-t önkiszolgáló bejelentkezési felhasználói folyamatokhoz, így a felhasználók saját Facebook-fiókjaikat is bejelentkezhetnek az alkalmazásaiba. Ahhoz, hogy a felhasználók bejelentkezzenek a Facebook használatával, először engedélyeznie kell az [önkiszolgáló regisztrációt](self-service-sign-up-user-flow.md) a bérlő számára. Miután hozzáadta a Facebookot identitás-szolgáltatóként, állítson be egy felhasználói folyamatot az alkalmazáshoz, és válassza a Facebook lehetőséget a bejelentkezési lehetőségek közül.

Miután hozzáadta a Facebookot az alkalmazás bejelentkezési lehetőségeinek egyikéhez **, a felhasználó** egyszerűen megadhatja a facebookba való bejelentkezéshez használt e-mail-címet, vagy kiválaszthatja a bejelentkezési **beállításokat** , és **a bejelentkezés a Facebookon** lehetőséget is választhatja. Mindkét esetben a rendszer átirányítja a Facebook bejelentkezési oldalára a hitelesítéshez.

![Bejelentkezési beállítások Facebook-felhasználók számára](media/facebook-federation/sign-in-with-facebook-overview.png)

> [!NOTE]
> A felhasználók csak a Facebook-fiókjaikat használhatják az önkiszolgáló bejelentkezési és felhasználói folyamatokat használó alkalmazásokban való regisztrációhoz. A felhasználók nem hívhatják meg és nem válthatják be a meghívót Facebook-fiók használatával.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Alkalmazás létrehozása a Facebook Fejlesztői konzolon

Facebook-fiók [identitás-szolgáltatóként](identity-providers.md)való használatához létre kell hoznia egy alkalmazást a Facebook Fejlesztői konzolon. Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a következő címen: [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> Használja az alábbi URL-címeket az alábbi 9. és 16. lépésekben.
> - A **webhely URL-** címe mezőben adja meg az alkalmazás címét, például: `https://contoso.com` .
> - **Érvényes OAuth átirányítási URI**-k esetén adja meg a következőt: `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . A Azure Active Directory áttekintés paneljén található `<tenant-ID>` .


1. A Facebook-fiók hitelesítő adataival jelentkezzen be a [facebookba a fejlesztők számára](https://developers.facebook.com/) .
2. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
3. Válassza **a saját alkalmazások** lehetőséget, majd **hozza létre az alkalmazást**.
4. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt**.
5. Válassza az **alkalmazás-azonosító létrehozása** lehetőséget. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
6. Válassza a **Beállítások**  >  **alapszintű** lehetőséget.
7. Válasszon egy **kategóriát**, például az üzleti és a lapokat. Ez az érték a Facebook számára szükséges, de nem használható az Azure AD-hez.
8. A lap alján válassza a **platform hozzáadása** lehetőséget, majd válassza a **webhely** lehetőséget.
9. A **webhely URL-címe** mezőben adja meg a megfelelő URL-címet (lásd fent).
10. Az **adatvédelmi szabályzat URL-címe** mezőbe írja be annak az oldalnak az URL-címét, amelyen az alkalmazás adatvédelmi információit megőrzi, például: `http://www.contoso.com` .
11. Válassza a **módosítások mentése** lehetőséget.
12. Az oldal tetején másolja az **alkalmazás-azonosító** értékét.
13. Válassza az **alkalmazás titkos kulcsának** **megjelenítése** és másolása lehetőséget. Mindkettőt használja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** fontos biztonsági hitelesítő adat.
14. Válassza ki a **termékek** melletti plusz jelet, majd válassza a **beállítás** a **Facebook-Bejelentkezés** alatt lehetőséget.
15. A **Facebook-Bejelentkezés** területen válassza a **Beállítások** lehetőséget.
16. Az **érvényes OAuth átirányítási URI**-k mezőben adja meg a megfelelő URL-címet (lásd fent).
17. A lap alján kattintson a **módosítások mentése** gombra.
18. Annak érdekében, hogy a Facebook-alkalmazás elérhető legyen az Azure AD-ben, válassza ki a lap jobb felső sarkában található állapotjelzőt, és kapcsolja be **, hogy az** alkalmazás nyilvános legyen, majd válassza a **váltás mód** lehetőséget. Ezen a ponton az állapotnak a **fejlesztéstől** az **élő** értékre kell váltania.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók konfigurálása identitás-szolgáltatóként
Most be kell állítania a Facebook ügyfél-azonosítót és az ügyfél titkát, vagy megadhatja azt az Azure AD portálon vagy a PowerShell használatával. A Facebook-konfigurációt tesztelheti úgy, hogy az önkiszolgáló regisztrációt engedélyező alkalmazás felhasználói folyamatán keresztül regisztrál.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>A Facebook-összevonás konfigurálása az Azure AD-portálon
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure ad-bérlő globális rendszergazdájaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. A bal oldali menüben válassza a **külső identitások** lehetőséget.
4. Válassza **a minden identitás szolgáltató** lehetőséget, majd válassza a **Facebook** lehetőséget.
5. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás- **azonosítóját** .
6. Az **ügyfél titka** mezőben adja meg a rögzített **alkalmazás titkát** .

   ![A közösségi identitás-szolgáltató hozzáadása lapot ábrázoló képernyőfelvétel](media/facebook-federation/add-social-identity-provider-page.png)

7. Kattintson a **Mentés** gombra.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>A Facebook-összevonás konfigurálása a PowerShell használatával
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa a következő parancsot: `Connect-AzureAD` .
3. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
4. Futtassa az alábbi parancsot: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Használja az ügyfél-azonosítót és az ügyfél titkos kulcsát a fentiekben létrehozott alkalmazásból a Facebook Fejlesztői konzolon. További információt a [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true) című cikkben talál. 

## <a name="how-do-i-remove-facebook-federation"></a>Hogyan eltávolítja a Facebook-összevonást?
A Facebook-összevonási telepítőt törölheti. Ha így tesz, a felhasználói folyamatokon keresztül regisztrált felhasználók Facebook-fiókjai többé nem fognak tudni bejelentkezni. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Facebook-összevonás törlése az Azure AD-portálon: 
1. Nyissa meg az [Azure Portal](https://portal.azure.com). A bal oldali panelen válassza az **Azure Active Directory** lehetőséget. 
2. Válassza a **külső identitások** lehetőséget.
3. Válassza ki **az összes identitás szolgáltatót**.
4. A **Facebook** -vonalon válassza a helyi menüt (**...**), majd válassza a **Törlés** lehetőséget. 
5. A törlés megerősítéséhez válassza az **Igen** lehetőséget.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Facebook-összevonás törlése a PowerShell használatával: 
1. Telepítse az Azure AD PowerShell for Graph modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) legújabb verzióját.
2. Futtassa az `Connect-AzureAD` parancsot.  
4. A bejelentkezési kérésben jelentkezzen be a felügyelt globális rendszergazdai fiókkal.  
5. Írja be a következő parancsot:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > További információ: [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true). 

## <a name="next-steps"></a>Következő lépések

- [Önkiszolgáló regisztráció hozzáadása egy alkalmazáshoz](self-service-sign-up-user-flow.md)