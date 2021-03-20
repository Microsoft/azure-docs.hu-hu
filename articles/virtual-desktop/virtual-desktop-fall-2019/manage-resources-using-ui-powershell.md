---
title: Felügyeleti eszköz üzembe helyezése a Windows Virtual Desktopban (klasszikus) a szolgáltatásnév használatával – Azure
description: A Windows rendszerű virtuális asztali környezet (klasszikus) felügyeleti eszközének üzembe helyezése a PowerShell használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069017"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Windows rendszerű virtuális asztali (klasszikus) felügyeleti eszköz üzembe helyezése a PowerShell-lel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

Ez a cikk bemutatja, hogyan helyezheti üzembe a felügyeleti eszközt a PowerShell használatával.

## <a name="important-considerations"></a>Fontos szempontok

Minden Azure Active Directory-(Azure AD-) bérlő előfizetéséhez a felügyeleti eszköz saját külön üzembe helyezésére van szükség. Ez az eszköz nem támogatja az Azure AD vállalatközi (B2B) forgatókönyveit.

Ez a felügyeleti eszköz egy minta. A Microsoft fontos biztonsági és minőségi frissítéseket biztosít. [A forráskód a githubon érhető el](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Akár ügyfél, akár partner, javasoljuk, hogy szabja testre az eszközt az üzleti igények kielégítése érdekében.

A következő böngészők kompatibilisek a felügyeleti eszközzel:

- Google Chrome 68 vagy újabb verzió
- Microsoft Edge 40,15063 vagy újabb verzió
- Mozilla Firefox 52,0 vagy újabb verzió
- Safari 10 vagy újabb (csak macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezéséhez szükséges tudnivalók

A felügyeleti eszköz telepítése előtt szüksége lesz egy Azure Active Directory (Azure AD) felhasználóra az alkalmazások regisztrálásának létrehozásához és a felügyeleti felhasználói felület telepítéséhez. A felhasználónak a következőket kell tennie:

- Engedéllyel rendelkezik erőforrások létrehozásához az Azure-előfizetésében
- Engedéllyel rendelkezik Azure AD-alkalmazás létrehozásához. Kövesse az alábbi lépéseket annak ellenőrzéséhez, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a [szükséges engedélyek](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)utasításait követve.

A felügyeleti eszköz üzembe helyezése és konfigurálása után javasoljuk, hogy kérje meg a felhasználót, hogy indítsa el a felügyeleti felhasználói felületet, és győződjön meg róla, hogy minden működik. A felügyeleti felhasználói felületet indító felhasználónak szerepkör-hozzárendeléssel kell rendelkeznie, amely lehetővé teszi a Windows rendszerű virtuális asztali bérlő megtekintését vagy szerkesztését.

## <a name="set-up-powershell"></a>A PowerShell beállítása

Első lépésként jelentkezzen be mind az az, mind az Azure AD PowerShell-modulba. A következő módon jelentkezhet be:

1. Nyissa meg a PowerShellt rendszergazdaként, és navigáljon arra a könyvtárra, ahová a PowerShell-parancsfájlokat mentette.
2. Jelentkezzen be az Azure-ba egy olyan fiókkal, amely tulajdonosi vagy közreműködői engedélyekkel rendelkezik azon Azure-előfizetésben, amelyet a felügyeleti eszköz létrehozásához kíván használni a következő parancsmag futtatásával:

    ```powershell
    Login-AzAccount
    ```

3. A következő parancsmag futtatásával jelentkezzen be az Azure AD-be ugyanazzal a fiókkal, amelyet az az PowerShell-modulhoz használt:

    ```powershell
    Connect-AzureAD
    ```

4. Ezt követően navigáljon ahhoz a mappához, ahová a RDS-Templates GitHub-tárházból mentette a két PowerShell-szkriptet.

Tartsa meg úgy a PowerShell-ablak megnyitását, hogy további PowerShell-parancsmagokat futtasson a bejelentkezéskor.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrációjának létrehozása

A felügyeleti eszköz sikeres telepítéséhez és konfigurálásához először le kell töltenie a következő PowerShell-parancsfájlokat az [RDS-templates GitHub-](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) adattárból.

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Futtassa az alábbi parancsokat az alkalmazás regisztrációjának létrehozásához a szükséges API-engedélyekkel:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy végrehajtotta az Azure AD-alkalmazás regisztrációját, üzembe helyezheti a felügyeleti eszközt.

## <a name="deploy-the-management-tool"></a>A felügyeleti eszköz üzembe helyezése

Futtassa a következő PowerShell-parancsokat a felügyeleti eszköz üzembe helyezéséhez és az imént létrehozott egyszerű szolgáltatáshoz való hozzárendeléséhez:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

A webalkalmazás létrehozása után hozzá kell adnia egy átirányítási URI-t az Azure AD-alkalmazáshoz, hogy sikeresen bejelentkezzen a felhasználókba.

## <a name="set-the-redirect-uri"></a>Az átirányítási URI beállítása

Futtassa a következő PowerShell-parancsokat a webalkalmazás URL-címének lekéréséhez, és állítsa be hitelesítési átirányítási URI-ként (más néven válasz-URL-ként):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Most, hogy hozzáadta az átirányítási URI-t, az API URL-címét frissítenie kell, hogy a felügyeleti eszköz kommunikálni tudjon az API-backend szolgáltatással.

## <a name="update-the-api-url-for-the-web-application"></a>A webalkalmazás API URL-címének frissítése

Futtassa a következő szkriptet az API URL-cím konfigurációjának frissítéséhez a webalkalmazás-előtérben:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Most, hogy teljesen konfigurálta a felügyeleti eszköz webalkalmazását, itt az ideje, hogy ellenőrizze az Azure AD-alkalmazást, és adja meg az engedélyt.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Ellenőrizze az Azure AD-alkalmazást, és adja meg a beleegyezett

Az Azure AD-alkalmazás konfigurációjának ellenőrzése és a beleegyező engedély megadása:

1. Nyissa meg az Internet böngészőt, és jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a rendszergazdai fiókjával.
2. A Azure Portal tetején található keresősáv alatt keresse meg **Alkalmazásregisztrációk** , és válassza ki az elemet a **szolgáltatások** területen.
3. Válassza a **minden alkalmazás** lehetőséget, és keresse meg a PowerShell-parancsfájlhoz megadott egyedi alkalmazás-nevet a [Azure Active Directory alkalmazás regisztrációjának létrehozása](#create-an-azure-active-directory-app-registration)lapon.
4. A böngésző bal oldali paneljén válassza a **hitelesítés** lehetőséget, és győződjön meg arról, hogy az átirányítási URI megegyezik a felügyeleti eszköz webalkalmazás URL-címével, ahogy az alábbi képen is látható.

   [![A hitelesítés lap a megadott átirányítási URI ](../media/management-ui-redirect-uri-inline.png) -val](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. A bal oldali panelen válassza az **API-engedélyek** lehetőséget az engedélyek hozzáadásának megerősítéséhez. Ha Ön globális rendszergazda, jelölje be a **rendszergazdai jóváhagyás `tenantname` megadása** gombra, és kövesse a párbeszédpanel utasításait, hogy rendszergazdai jogosultságot adjon a szervezet számára.

    [![Az API-engedélyek lap ](../media/management-ui-permissions-inline.png)](../media/management-ui-permissions-expanded.png#lightbox)

Most már megkezdheti a felügyeleti eszköz használatát.

## <a name="use-the-management-tool"></a>A felügyeleti eszköz használata

Most, hogy bármikor beállította a felügyeleti eszközt, bármikor elindíthatja, bárhol is. A következőképpen indíthatja el az eszközt:

1. Nyissa meg a webalkalmazás URL-címét egy böngészőben. Ha nem emlékszik az URL-címre, bejelentkezhet az Azure-ba, megkeresheti a felügyeleti eszközre telepített app Service-t, majd kiválaszthatja az URL-címet.
2. Jelentkezzen be a Windows rendszerű virtuális asztali hitelesítő adataival.

   > [!NOTE]
   > Ha a felügyeleti eszköz konfigurálása során nem adta meg a rendszergazdai jóváhagyást, akkor a bejelentkező összes felhasználónak meg kell adnia a saját felhasználói hozzájárulásukat az eszköz használatához.

3. Amikor a rendszer kéri, hogy válasszon ki egy bérlői csoportot, válassza ki az **alapértelmezett bérlői csoportot** a legördülő listából.
4. Amikor kiválasztja az **alapértelmezett bérlői csoportot**, egy menü jelenik meg az ablak bal oldalán. Ebben a menüben keresse meg a bérlői csoport nevét, és jelölje ki.

   > [!NOTE]
   > Ha egyéni bérlői csoporttal rendelkezik, a legördülő listából válassza a nevet manuálisan.

## <a name="report-issues"></a>Problémák bejelentése

Ha a felügyeleti eszközzel vagy más Windowsos virtuális asztali eszközökkel kapcsolatos problémákba ütközik, kövesse az [Azure Resource Manager-távoli asztali szolgáltatások sablonok](https://github.com/Azure/RDS-Templates/blob/master/README.md) című témakör utasításait a githubon való jelentéséhez.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan helyezheti üzembe és csatlakozhat a felügyeleti eszközhöz, megtudhatja, hogyan használhatja a Azure Service Healtht a szolgáltatási problémák és az állapot-útmutatók figyelésére. További információ: [set up Service riasztások oktatóanyaga](set-up-service-alerts-2019.md).
