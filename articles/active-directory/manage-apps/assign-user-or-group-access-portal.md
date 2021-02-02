---
title: Alkalmazás felhasználó-hozzárendelésének kezelése az Azure Active Directoryban
description: Megtudhatja, hogyan rendelhet hozzá és rendelhet hozzá felhasználókat és csoportokat a Azure Active Directory for Identity Management alkalmazáshoz.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50dcde478b708cd53d8229d70a9ddf4b1ff271be
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259734"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Alkalmazás felhasználó-hozzárendelésének kezelése az Azure Active Directoryban

Ebből a cikkből megtudhatja, hogyan rendelhet hozzá felhasználókat és csoportokat a Azure Active Directory (Azure AD) vállalati alkalmazásaihoz, akár a Azure Portal, akár a PowerShell használatával. Amikor felhasználót rendel hozzá egy alkalmazáshoz, az alkalmazás könnyen elérhetővé válik a felhasználó [saját alkalmazásaiban](https://myapps.microsoft.com/) . Ha az alkalmazás szerepköröket tesz elérhetővé, akkor egy adott szerepkört is hozzárendelhet a felhasználóhoz.

A nagyobb fokú szabályozás érdekében a vállalati alkalmazások bizonyos típusai úgy konfigurálhatók, hogy [felhasználói hozzárendelést kérjenek](#configure-an-application-to-require-user-assignment). 

> [!IMPORTANT]
> Ha egy alkalmazáshoz rendel hozzá egy csoportot, akkor csak a csoport felhasználói férhetnek hozzá. A hozzárendelés nem kaszkádba ágyazott csoportokba kerül.

> [!NOTE]
> A csoport alapú hozzárendeléshez prémium szintű Azure Active Directory P1 vagy P2 kiadás szükséges. A csoport alapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagságok és Microsoft 365 csoportok jelenleg nem támogatottak. A cikkben tárgyalt funkciókra vonatkozó további licencelési követelményekért tekintse meg a [Azure Active Directory díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Alkalmazás konfigurálása felhasználói hozzárendelés megköveteléséhez

A következő típusú alkalmazások esetében lehetősége van megkövetelni, hogy a felhasználók az alkalmazáshoz legyenek hozzárendelve ahhoz, hogy hozzáférhessenek a szolgáltatáshoz:

- Az összevont egyszeri bejelentkezéshez (SSO) SAML-alapú hitelesítéssel konfigurált alkalmazások
- Azure Active Directory előhitelesítést használó alkalmazásproxy-alkalmazások
- Az Azure AD Application platformra épülő, OAuth 2,0/OpenID Connect hitelesítést használó alkalmazások, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba.

Ha felhasználói hozzárendelésre van szükség, csak azokat a felhasználókat lehet bejelentkezni, akiket kifejezetten az alkalmazáshoz rendeltek (közvetlen felhasználói hozzárendelés vagy csoporttagság alapján). Hozzáférhetnek az alkalmazáshoz a saját alkalmazások oldalon vagy egy közvetlen hivatkozás használatával. 

Ha nincs *szükség* hozzárendelésre, mert ezt a beállítást a **nem** értékre állította be, vagy mert az alkalmazás más egyszeri bejelentkezéses módot használ, akkor bármely felhasználó hozzáférhet az alkalmazáshoz, ha az alkalmazás **Tulajdonságok** lapján közvetlen hivatkozással rendelkezik az alkalmazásra vagy a **felhasználói hozzáférési URL-címre** . 

Ez a beállítás nem befolyásolja, hogy az alkalmazások megjelennek-e az alkalmazásokban. Az alkalmazások a felhasználók saját alkalmazások hozzáférési panelén jelennek meg, miután hozzárendelt egy felhasználót vagy csoportot az alkalmazáshoz. A háttérben tekintse meg az [alkalmazások hozzáférésének kezelése](what-is-access-management.md)című témakört.

Felhasználói hozzárendelés megkövetelése egy alkalmazáshoz:
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy rendszergazdai fiókkal vagy az alkalmazás tulajdonosaként.
2. Válassza a **Azure Active Directory** lehetőséget. A bal oldali navigációs menüben válassza a **vállalati alkalmazások** lehetőséget.
3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy használja a szűrő vezérlőelemeket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza az **alkalmaz** lehetőséget.
4. A bal oldali navigációs menüben válassza a **Tulajdonságok** lehetőséget.
5. Győződjön meg arról, hogy a **felhasználó-hozzárendelés szükséges?** a váltógomb **Igen** értékre van állítva.
   > [!NOTE]
   > Ha a **felhasználó-hozzárendelés szükséges?** a váltógomb nem érhető el, a PowerShell használatával állíthatja be a appRoleAssignmentRequired tulajdonságot az egyszerű szolgáltatásnév számára.
6. Kattintson a **Save (Mentés** ) gombra a képernyő tetején.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Felhasználók és csoportok hozzárendelésének vagy hozzárendelésének megszüntetése a Azure Portal használatával
Ha meg szeretné tudni, hogyan rendeljen hozzá vagy rendeljen hozzá egy felhasználót vagy csoportot a Azure Portal használatával, tekintse meg a gyors üzembe helyezési [sorozatot az alkalmazások kezelésében](add-application-portal-assign-users.md).

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Felhasználók és csoportok hozzárendelésének vagy hozzárendelésének megszüntetése a Graph API használatával
A Graph API a felhasználók és csoportok hozzárendelésére és hozzárendelésének visszavonására használhatja az alkalmazáshoz. További információ: alkalmazás- [szerepkör hozzárendelése](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>Felhasználók és csoportok társítása egy alkalmazáshoz a PowerShell használatával
1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.
   > [!NOTE]
   > Telepítenie kell a AzureAD modult (használja a parancsot `Install-Module -Name AzureAD` ). Ha a rendszer kéri, hogy telepítsen egy NuGet modult vagy az új Azure Active Directory v2 PowerShell-modult, írja be az Y értéket, és nyomja le az ENTER
2. Futtassa a parancsot, `Connect-AzureAD` és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő parancsfájl használatával rendelhet hozzá felhasználót és szerepkört egy alkalmazáshoz:

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
A felhasználók alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADUserAppRoleAssignment](/powershell/module/azuread/new-azureaduserapproleassignment)dokumentációját.

Ha vállalati alkalmazáshoz szeretne hozzárendelni egy csoportot, a és a helyére a következőt kell cserélnie: `Get-AzureADUser` `Get-AzureADGroup` `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

A csoportok alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment)dokumentációját.

### <a name="example"></a>Példa

Ez a példa a Britta-felhasználót a [Microsoft munkahelyi elemzési](https://products.office.com/business/workplace-analytics) alkalmazáshoz a PowerShell használatával rendeli hozzá.

1. A PowerShellben rendelje hozzá a megfelelő értékeket a változókhoz $username, $app _name és $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. Ebben a példában nem tudjuk, mi a pontos neve annak az alkalmazási szerepkörnek, amelyet a Britta Simonhoz szeretne rendelni. Futtassa a következő parancsokat a felhasználó ($user) és az egyszerű szolgáltatásnév ($sp) beszerzéséhez a felhasználói UPN és a szolgáltatásnév megjelenített neveinek használatával.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. Futtassa a parancsot a `$sp.AppRoles` munkahelyi elemzési alkalmazáshoz elérhető szerepkörök megjelenítéséhez. Ebben a példában a Britta Simon (korlátozott hozzáférés) szerepkört szeretnénk hozzárendelni.
   ![A munkahelyi elemzési szerepkört használó felhasználó számára elérhető szerepkörök megjelenítése](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Rendelje hozzá a szerepkör nevét a `$app_role_name` változóhoz.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. A következő parancs futtatásával rendelje hozzá a felhasználót az alkalmazás szerepkörhöz:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>Felhasználók és csoportok hozzárendelésének megszüntetése egy alkalmazásból a PowerShell használatával

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.
   > [!NOTE]
   > Telepítenie kell a AzureAD modult (használja a parancsot `Install-Module -Name AzureAD` ). Ha a rendszer kéri, hogy telepítsen egy NuGet modult vagy az új Azure Active Directory v2 PowerShell-modult, írja be az Y értéket, és nyomja le az ENTER
2. Futtassa a parancsot, `Connect-AzureAD` és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő parancsfájl használatával távolíthatja el a felhasználót és a szerepkört egy alkalmazásból:

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

- [További információ az alkalmazásokhoz való végfelhasználói hozzáférésről](end-user-experiences.md)
- [Azure AD-beli alkalmazások üzembe helyezésének megtervezése](my-apps-deployment-plan.md)
- [Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
 
## <a name="next-steps"></a>Következő lépések

- [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból]()
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](./add-application-portal-configure.md)
