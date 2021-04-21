---
title: Az Azure AD használata Azure Automationben az Azure-beli hitelesítéshez
description: Ez a cikk bemutatja, hogyan használhatja az Azure AD-Azure Automation az Azure-beli hitelesítésszolgáltatóként.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34033589a297b1a3a2abb97d346f1da478f950e6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830285"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Az Azure AD használata az Azure-beli hitelesítéshez

A [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) szolgáltatás számos felügyeleti feladatot tesz lehetővé, például felhasználókezelést, tartománykezelést és egyszeri bejelentkezéses konfigurációt. Ez a cikk azt ismerteti, hogyan használható az Azure AD Azure Automation az Azure-beli hitelesítésszolgáltatóként. 

## <a name="install-azure-ad-modules"></a>Azure AD-modulok telepítése

Az Azure AD a következő PowerShell-modulokon keresztül engedélyezhető:

* Azure Active Directory PowerShell for Graph (AzureRM és Az modulok) használata. Azure Automation AzureRM modult és annak legújabb frissítését, az Az modult is. A funkciók közé tartozik az Azure-ba történő nem interaktív hitelesítés Az Azure AD-felhasználó (OrgId) hitelesítőadat-alapú hitelesítése. Lásd: [Azure AD 2.0.2.76.](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)

* Microsoft Azure Active Directory a Windows PowerShell (MSOnline modul). Ez a modul lehetővé teszi a Microsoft Online-lal való kommunikációt, beleértve a Microsoft 365.

>[!NOTE]
>PowerShell Core nem támogatja az MSOnline modult. A modul parancsmagok futtatásához ezeket a parancsmagokat a Windows PowerShell. Ajánlott az MSOnline modul helyett Azure Active Directory PowerShell for Graph-modulok újabb modulját használni. 

### <a name="preinstallation"></a>Előtelepítési

Az Azure AD-modulok telepítése előtt a számítógépen:

* Távolítsa el az AzureRM/Az modul és az MSOnline modul korábbi verzióit. 

* Távolítsa el a Microsoft Online Services Sign-In Assistant eszközt az új PowerShell-modulok megfelelő működésének biztosításához.  

### <a name="install-the-azurerm-and-az-modules"></a>Az AzureRM és az Az modulok telepítése

>[!NOTE]
>A modulokkal való munkához a PowerShell 5.1-es vagy újabb verzióját kell használnia a Windows 64 bites verziójával. 

1. Telepítse Windows Management Framework (WMF) 5.1-et. Lásd: [A WMF 5.1 telepítése és konfigurálása.](/powershell/scripting/wmf/setup/install-configure)

2. Telepítse az AzureRM-et és/vagy az Az-t az Install Azure PowerShell on Windows with PowerShellGet (Az azurerm és/vagy az Az telepítése Windows rendszeren [a PowerShellGet használatával) útmutató alapján.](/powershell/azure/azurerm/install-azurerm-ps)

### <a name="install-the-msonline-module"></a>Az MSOnline modul telepítése

>[!NOTE]
>Az MSOnline modul telepítéséhez rendszergazdai szerepkörnek kell lennie. Lásd: [About admin roles (A rendszergazdai szerepkörökről).](/microsoft-365/admin/add-users/about-admin-roles)

1. Győződjön meg arról, Microsoft .NET a Microsoft .NET Framework 3.5.x szolgáltatás engedélyezve van a számítógépen. Valószínű, hogy a számítógépére újabb verzió van telepítve, de a korábbi verziókkal való kompatibilitás a .NET-keretrendszer engedélyezhető vagy letiltható. 

2. Telepítse a Microsoft Online Services bejelentkezési segéd 64 [bites verzióját.](https://www.microsoft.com/Download/details.aspx?id=28177)

3. Rendszergazdaként Windows PowerShell rendszergazdaként futtatva hozzon létre egy rendszergazdai jogú Windows PowerShell parancssort.

4. Telepítse Azure Active Directory [MSOnline 1.0-ból.](http://www.powershellgallery.com/packages/MSOnline/1.0)

5. Ha a rendszer a NuGet-szolgáltató telepítését kéri, írja be az Y parancsot, majd nyomja le az ENTER billentyűt.

6. Ha a rendszer a [PSGalleryből](https://www.powershellgallery.com/)kéri a modul telepítését, írja be az Y parancsot, majd nyomja le az ENTER billentyűt.

### <a name="install-support-for-pscredential"></a>A PSCredential támogatásának telepítése

Azure Automation [a PSCredential](/dotnet/api/system.management.automation.pscredential) osztályt használja a hitelesítő adateszköz ábrázolásként. A szkriptek a `PSCredential` parancsmag használatával lekérik az `Get-AutomationPSCredential` objektumokat. További információ: Hitelesítő [adateszközök a Azure Automation.](shared-resources/credentials.md)

## <a name="assign-a-subscription-administrator"></a>Előfizetés-adminisztrátor hozzárendelése

Az Azure-előfizetéshez rendszergazdát kell hozzárendelnie. Ez a személy a Tulajdonos szerepkörrel rendelkezik az előfizetés hatókörében. Lásd: [Szerepköralapú hozzáférés-vezérlés a Azure Automation.](automation-role-based-access-control.md) 

## <a name="change-the-azure-ad-users-password"></a>Az Azure AD-felhasználó jelszavának módosítása

Az Azure AD-felhasználó jelszavának módosítása:

1. Jelentkezzen ki az Azure-ba.

2. A rendszergazdának be kell jelentkeznie az Azure-ba az azure AD-felhasználó által az előbb létrehozott fiókként a teljes felhasználónévvel (beleértve a tartományt is) és egy ideiglenes jelszóval. 

3. Kérje meg a rendszergazdát, hogy módosítsa a jelszót, amikor a rendszer erre kéri.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Az Azure Automation konfigurálása az Azure-előfizetés kezeléséhez

Ahhoz Azure Automation kommunikáljon az Azure AD-val, le kellkérnie az Azure-ral létesített Azure-kapcsolathoz társított hitelesítő adatokat. Ilyen hitelesítő adatok például a bérlőazonosító, az előfizetés azonosítója és hasonlók. További információ az Azure és az Azure AD közötti kapcsolatról: A szervezet csatlakoztatása a [Azure Active Directory.](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)

## <a name="create-a-credential-asset"></a>Hitelesítő adateszköz létrehozása

Most, hogy rendelkezésre állnak az Azure-beli hitelesítő adatok az Azure AD-hez, ideje létrehozni egy Azure Automation-beli hitelesítőadat-eszközt az Azure AD hitelesítő adatainak biztonságos tárolására, hogy a runbookok és az Desire State Configuration- (DSC-) szkriptek hozzáférnek hozzájuk. Ezt a következő parancsmagokkal Azure Portal PowerShell-parancsmagokkal.

### <a name="create-the-credential-asset-in-azure-portal"></a>Hozza létre a hitelesítő adateszközt a Azure Portal

A hitelesítő adat Azure Portal használhatja. Ezt a műveletet az Automation-fiókból, a **Megosztott erőforrások alatti Hitelesítő** adatok használatával kell **megtenni.** Lásd: [Hitelesítőadat-eszközök a Azure Automation.](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>A hitelesítő adateszköz létrehozása Windows PowerShell

Egy új hitelesítő adatobjektum előkészítéséhez Windows PowerShell szkript először létrehoz egy objektumot a hozzárendelt `PSCredential` felhasználónévvel és jelszóval. A szkript ezután ezzel az objektummal hozza létre az objektumot a [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) parancsmag hívásával. Másik lehetőségként a szkript a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) parancsmag hívásával kéri a felhasználót, hogy adjon meg egy nevet és egy jelszót. Lásd: [Hitelesítő adateszközök a Azure Automation.](shared-resources/credentials.md) 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure-erőforrások kezelése Azure Automation runbookból

Az Azure-erőforrásokat a Azure Automation a hitelesítő adateszköz használatával kezelheti. Az alábbi példában egy PowerShell-runbook található, amely összegyűjti az Azure-előfizetésben lévő virtuális gépek leállításához és indításához használt hitelesítő eszközt. Ez a runbook először a `Get-AutomationPSCredential` használatával lekéri az Azure-beli hitelesítéshez használt hitelesítő adatokat. Ezután a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot hívja meg, hogy a hitelesítő adatokkal csatlakozzon az Azure-hoz. A szkript a [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) parancsmagot használja a használt előfizetés kiválasztásához. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Következő lépések

* A hitelesítő adatok használatával kapcsolatos részletekért lásd: [Hitelesítő adatok kezelése a Azure Automation.](shared-resources/credentials.md)
* További információ a modulokról: [Modulok kezelése a Azure Automation.](shared-resources/modules.md)
* Ha el kell kezdenie egy runbookot, tekintse meg [a Runbook első](start-runbooks.md)futtatása a Azure Automation.
* A PowerShell részleteiért lásd: [PowerShell Docs.](/powershell/scripting/overview)
