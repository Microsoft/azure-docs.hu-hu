---
title: Alkalmazás felhasználó-hozzárendelésének kezelése az Azure Active Directoryban
description: Megtudhatja, hogyan rendelhet hozzá és rendelhet hozzá felhasználókat és csoportokat egy alkalmazáshoz az Azure Active Directory identitáskezeléshez.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377972"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Alkalmazás felhasználó-hozzárendelésének kezelése az Azure Active Directoryban

Ez a cikk bemutatja, hogyan rendelhet hozzá felhasználókat és csoportokat vállalati alkalmazásokhoz az Azure Active Directory (Azure AD) szolgáltatásban a Azure Portal vagy a PowerShell használatával. Amikor felhasználót rendel egy alkalmazáshoz, az alkalmazás megjelenik a felhasználó Saját alkalmazások [a](https://myapps.microsoft.com/) könnyű hozzáférés érdekében. Ha az alkalmazás elérhetővé teszi a szerepköröket, hozzárendelhet egy adott szerepkört is a felhasználóhoz.

A nagyobb ellenőrzés érdekében bizonyos típusú vállalati alkalmazások konfigurálhatóak úgy, hogy felhasználói [hozzárendelést követelnek meg.](#configure-an-application-to-require-user-assignment) 

> [!IMPORTANT]
> Amikor csoportot rendel egy alkalmazáshoz, csak a csoport felhasználói férhetnek hozzá. A hozzárendelés nem kaszkádolt a beágyazott csoportokhoz.

> [!NOTE]
> A csoportalapú hozzárendeléshez prémium szintű Azure Active Directory P1 vagy P2 kiadás szükséges. A csoportalapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagság és Microsoft 365 csoport jelenleg nem támogatott. A cikkben tárgyalt szolgáltatások licencelési követelményeivel kapcsolatos további információkért lásd a Azure Active Directory [oldalon.](https://azure.microsoft.com/pricing/details/active-directory) 

## <a name="configure-an-application-to-require-user-assignment"></a>Alkalmazás konfigurálása felhasználó-hozzárendelés megkövetelni

A következő típusú alkalmazások esetében megkövetelheti a felhasználók alkalmazáshoz való hozzárendelését, mielőtt hozzáférhetnek az alkalmazáshoz:

- SAML-alapú hitelesítéssel rendelkező összevont egyszeri bejelentkezésre (SSO) konfigurált alkalmazások
- alkalmazásproxy előhitelesítést Azure Active Directory alkalmazásokat
- Az Azure AD alkalmazásplatformra épített, OAuth 2.0/OpenID Connect hitelesítést használó alkalmazások, miután egy felhasználó vagy rendszergazda hozzájárulását adta az alkalmazáshoz.

Ha felhasználói hozzárendelésre van szükség, csak azok a felhasználók tudnak bejelentkezni, akik explicit módon vannak hozzárendelve az alkalmazáshoz (akár közvetlen felhasználó-hozzárendelésen keresztül, akár csoporttagság alapján). Az alkalmazást elérhetik a Saját alkalmazások oldalon vagy egy közvetlen hivatkozással. 

Ha nincs szükség hozzárendelésre, vagy azért,  mert ezt a beállítást Nem vagy mert az alkalmazás egy másik SSO-módot használ, bármely felhasználó hozzáférhet az alkalmazáshoz, ha közvetlen hivatkozással rendelkezik az alkalmazáshoz vagy a Felhasználói hozzáférés **URL-címe** az alkalmazás Tulajdonságok lapján.  

Ez a beállítás nem befolyásolja, hogy egy alkalmazás megjelenik-e a Saját alkalmazások. Az alkalmazások akkor jelennek meg a Saját alkalmazások hozzáférési paneleken, ha már hozzárendelt egy felhasználót vagy csoportot az alkalmazáshoz. Háttér-információkért [lásd: Alkalmazásokhoz való hozzáférés kezelése.](what-is-access-management.md)

Felhasználó-hozzárendelés megkövetelni egy alkalmazáshoz:
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) rendszergazdai fiókkal vagy az alkalmazás tulajdonosaként.
2. Válassza a **Azure Active Directory** lehetőséget. A bal oldali navigációs menüben válassza a Vállalati **alkalmazások lehetőséget.**
3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy a szűrővezérlőkkel válassza ki az alkalmazás típusát, állapotát vagy láthatóságát, majd válassza az Alkalmaz **lehetőséget.**
4. A bal oldali navigációs menüben válassza a Tulajdonságok **lehetőséget.**
5. Győződjön meg arról, hogy a **Felhasználó-hozzárendelés szükséges?** váltógomb **igenre van állítva.**
   > [!NOTE]
   > Ha a **Felhasználó-hozzárendelés szükséges?** kapcsoló nem érhető el, a PowerShell használatával beállíthatja az appRoleAssignmentRequired tulajdonságot a szolgáltatásnévhez.
6. Válassza **a képernyő** tetején található Mentés gombot.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Felhasználók és csoportok hozzárendelése vagy hozzárendelésének Azure Portal
Ha meg szeretne ismerkedni egy felhasználó vagy csoport hozzárendelésének vagy hozzárendelésének az Azure Portal használatával való hozzárendelésével, tekintse meg az Alkalmazáskezelés gyorsindítási [sorozatát.](add-application-portal-assign-users.md)

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Felhasználók és csoportok hozzárendelése vagy hozzárendelésének Graph API
Az alkalmazáshoz a Graph API felhasználók és csoportok hozzárendelésére vagy hozzárendelésének megszétlére használhatja. További információ: [Alkalmazásszerepk szerepkör-hozzárendelések.](/graph/api/resources/approleassignment)

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Felhasználók és csoportok hozzárendelése egy alkalmazáshoz a PowerShell használatával
1. Nyisson meg egy emelt Windows PowerShell parancssort.
   > [!NOTE]
   > Telepítenie kell az AzureAD modult (használja a `Install-Module -Name AzureAD` parancsot). Ha a rendszer arra kéri, hogy telepítsen egy NuGet-modult vagy az új Azure Active Directory V2 PowerShell-modult, írja be az Y parancsot, majd nyomja le az ENTER billentyűt.
2. Futtassa `Connect-AzureAD` a parancsot, és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő szkript használatával rendelhet hozzá egy felhasználót és egy szerepkört egy alkalmazáshoz:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
A felhasználók alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADUserAppRoleAssignment dokumentációját.](/powershell/module/azuread/new-azureaduserapproleassignment)

Ha egy csoportot vállalati alkalmazáshoz szeretne hozzárendelni, a helyére a helyére, a helyére pedig a következőt `Get-AzureADUser` `Get-AzureADGroup` kell be `New-AzureADUserAppRoleAssignment` helyettesítenie: `New-AzureADGroupAppRoleAssignment` .

A csoportok alkalmazásszerepkhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADGroupAppRoleAssignment dokumentációját.](/powershell/module/azuread/new-azureadgroupapproleassignment)

### <a name="example"></a>Példa

Ez a példa Britta Simon felhasználót rendeli hozzá a [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) alkalmazáshoz a PowerShell használatával.

1. A PowerShellben rendelje hozzá a megfelelő értékeket a következő változókhoz: $username, $app_name és $app_role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. Ebben a példában nem tudjuk pontosan annak az alkalmazás-szerepkörnek a nevét, amit Britta Simonhoz szeretnénk rendelni. Futtassa a következő parancsokat a felhasználó ($user) és a szolgáltatásnév ($sp) a felhasználó egyszerű felhasználónevének és a szolgáltatásnév megjelenítendő nevének használatával.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Futtassa az parancsot a Workplace Analytics alkalmazáshoz elérhető szerepkörök `$sp.AppRoles` megjelenítéséhez. Ebben a példában Britta Simon, az elemző (korlátozott hozzáférés) szerepkört szeretnénk hozzárendelni.
   ![Megjeleníti a felhasználók számára a Munkahelyi elemzési szerepkör használatával elérhető szerepköröket](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Rendelje hozzá a szerepkör nevét a `$app_role_name` változóhoz.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Az alábbi parancs futtatásával rendelje hozzá a felhasználót az alkalmazás-szerepkörhöz:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Felhasználók és csoportok hozzárendelésének kiosztása egy alkalmazásból a PowerShell használatával

1. Nyisson meg egy emelt Windows PowerShell parancssort.
   > [!NOTE]
   > Telepítenie kell az AzureAD modult (használja a `Install-Module -Name AzureAD` parancsot). Ha a rendszer arra kéri, hogy telepítsen egy NuGet-modult vagy az új Azure Active Directory V2 PowerShell-modult, írja be az Y parancsot, majd nyomja le az ENTER billentyűt.
2. Futtassa `Connect-AzureAD` a parancsot, és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő szkript használatával távolíthat el egy felhasználót és egy szerepkört egy alkalmazásból:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>Kapcsolódó cikkek

- [További információ az alkalmazások végfelhasználói hozzáférésről](end-user-experiences.md)
- [Azure AD-Saját alkalmazások megterve](my-apps-deployment-plan.md)
- [Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
 
## <a name="next-steps"></a>Következő lépések

- [Az összes csoportom](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó- vagy csoport-hozzárendelés eltávolítása vállalati alkalmazásból]()
- [Felhasználói bejelentkezések letiltása vállalati alkalmazás kapcsán](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](./add-application-portal-configure.md)
