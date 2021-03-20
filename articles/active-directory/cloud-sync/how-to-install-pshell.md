---
title: A Azure AD Connect Cloud kiépítési ügynök telepítése a PowerShell használatával
description: Ismerje meg, hogyan telepítheti a Azure AD Connect Cloud kiépítési ügynököt a PowerShell-parancsmagok használatával.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613698"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Az Azure AD Connect kiépítési ügynök telepítése PowerShell-parancsmagok használatával 
A következő dokumentum bemutatja, hogyan telepítheti a Azure AD Connect üzembe helyezési ügynököt PowerShell-parancsmagok használatával.
 

## <a name="prerequisite"></a>Előfeltétel: 


>[!IMPORTANT]
>A következő telepítési utasítások feltételezik, hogy az összes [előfeltétel](how-to-prerequisites.md) teljesült.
>
> Ahhoz, hogy a Windows Server a PowerShell-parancsmagok használatával telepítené a Azure AD Connect üzembe helyezési ügynököt, engedélyezni kell a TLS 1,2-et. A TLS 1,2 engedélyezéséhez az [itt](how-to-prerequisites.md#tls-requirements)található lépéseket használhatja.

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Az Azure AD Connect kiépítési ügynök telepítése PowerShell-parancsmagok használatával 


 1. Jelentkezzen be a Azure Portalba, majd lépjen a **Azure Active Directoryra**.
 2. A bal oldali menüben válassza a **Azure ad Connect** lehetőséget.
 3. Válassza a **felügyelet létesítése (előzetes verzió)**  >  **minden ügynök áttekintése** elemet.
 4. Töltse le a Azure AD Connect kiépítési ügynököt a Azure Portalból egy helyileg.  

   ![Helyszíni ügynök letöltése](media/how-to-install/install-9.png)</br>
 5. Ezeknek az utasításoknak az alkalmazásában az ügynököt a rendszer a következő mappába töltötte le: "C:\ProvisioningSetup". 
 6. A ProvisioningAgent telepítése csendes módban

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Kiépítési ügynök PS-moduljának importálása 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Kapcsolódás a AzureAD globális rendszergazdai hitelesítő adatokkal, a szakasz testreszabható a jelszó biztonságos tárolóból való beolvasásához. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD – hitelesítő adatok $globalAdminCreds 

 9. Adja hozzá a gMSA-fiókot, adja meg a tartományi rendszergazda hitelesítő adatait az alapértelmezett gMSA-fiók létrehozásához 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. Vagy használja az alábbi parancsmagot az előre létrehozott gMSA-fiók biztosításához 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Tartomány hozzáadása 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. Vagy használja az alábbi parancsmagot az előnyben részesített tartományvezérlők konfigurálásához 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Ismételje meg az előző lépést további tartományok hozzáadásához, adja meg a megfelelő tartományok fiókjának nevét és tartománynevét. 
 14. Indítsa újra a szolgáltatást 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Nyissa meg az Azure Portalt a Felhőbeli szinkronizálás konfigurációjának létrehozásához.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Kiépítési ügynök gMSA PowerShell-parancsmagjai
Most, hogy telepítette az ügynököt, részletesebb engedélyeket is alkalmazhat a gMSA.  Az engedélyek konfigurálásának részletes ismertetését lásd: [Azure ad Connect Cloud kiépítési ügynök GMSA PowerShell-parancsmagjai](how-to-gmsa-cmdlets.md) .

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Azure AD Connect Cloud kiépítési ügynök gMSA PowerShell-parancsmagjai](how-to-gmsa-cmdlets.md)
- [Mi az Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)