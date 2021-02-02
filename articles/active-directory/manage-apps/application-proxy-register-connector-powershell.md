---
title: Csendes telepítés Azure AD alkalmazás proxy-összekötővel | Microsoft Docs
description: Az Azure AD Application Proxy-összekötő felügyelet nélküli telepítését ismerteti a helyszíni alkalmazások biztonságos távoli elérésének biztosításához.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac442e895850de04bedf7673ffe267ac8697d26
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258150"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Felügyelet nélküli telepítési parancsfájl létrehozása az Azure AD Application Proxy-összekötőhöz

Ebből a témakörből megtudhatja, hogyan hozhat létre felügyelet nélküli telepítést és regisztrációt lehetővé tevő Windows PowerShell-parancsfájlt az Azure AD Application Proxy-összekötőhöz.

Ez a funkció akkor hasznos, ha a következőket kívánja használni:

* Telepítse az összekötőt olyan Windows-kiszolgálókra, amelyeken nincs engedélyezve a felhasználói felület, vagy nem férhet hozzá Távoli asztalhoz.
* Egyszerre több összekötőt is telepíthet és regisztrálhat.
* Az összekötő telepítésének és regisztrálásának integrálása egy másik eljárás részeként.
* Hozzon létre egy szabványos kiszolgálói rendszerképet, amely tartalmazza az összekötő biteit, de nincs regisztrálva.

Az [alkalmazásproxy-összekötő](application-proxy-connectors.md) működéséhez regisztrálnia kell az Azure ad-címtárban az alkalmazás-rendszergazda és a jelszó használatával. Általában ez az információ az összekötő telepítésekor kerül be egy előugró párbeszédpanelen, de a PowerShell használatával automatizálhatja ezt a folyamatot.

A felügyelet nélküli telepítéshez két lépés szükséges. Először telepítse az összekötőt. Másodszor regisztrálja az összekötőt az Azure AD-ben.

> [!IMPORTANT]
> Ha a Azure Government-felhőhöz tartozó összekötőt telepíti, tekintse át az [Előfeltételek](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) és a [telepítés lépéseit](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Ehhez engedélyezni kell a hozzáférést egy másik URL-készlethez, és egy további paramétert a telepítés futtatásához.

## <a name="install-the-connector"></a>Az összekötő telepítése
A következő lépésekkel telepítheti az összekötőt a regisztráció nélkül:

1. Nyisson meg egy parancssort.
2. Futtassa a következő parancsot, amelyben a/q csendes telepítést jelent. Egy csendes telepítés nem kéri, hogy fogadja el a End-User licencszerződést.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Az összekötő regisztrálása az Azure AD-ben
Az összekötő regisztrálására két módszer használható:

* Az összekötő regisztrálása Windows PowerShell hitelesítőadat-objektum használatával
* Az összekötő regisztrálása offline kapcsolattal létrehozott jogkivonat használatával

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Az összekötő regisztrálása Windows PowerShell hitelesítőadat-objektum használatával
1. Hozzon létre egy Windows PowerShell hitelesítő adatokat `$cred` tartalmazó objektumot, amely tartalmazza a címtárhoz tartozó rendszergazdai felhasználónevet és jelszót. Futtassa a következő parancsot, és cserélje le *\<username\>* *\<password\>* :

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. Nyissa meg a **C:\Program FILES\MICROSOFT HRE app proxy-összekötőt** , és futtassa a következő szkriptet a `$cred` létrehozott objektum használatával:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>Az összekötő regisztrálása offline kapcsolattal létrehozott jogkivonat használatával
1. Hozzon létre egy offline tokent a AuthenticationContext osztály használatával az alábbi kódrészlet vagy PowerShell-parancsmagok értékeivel:

   **A C# használatával:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **A PowerShell használata:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. Ha rendelkezik a jogkivonattal, hozzon létre egy SecureString a jogkivonat használatával:

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. Futtassa a következő Windows PowerShell-parancsot, \<tenant GUID\> és cserélje le a KÖNYVTÁRAT azonosítóra:

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>Következő lépések
* [Alkalmazások közzététele saját tartománynév használatával](application-proxy-configure-custom-domain.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)