---
title: Az Azure AD használata Azure Automationben az Azure-beli hitelesítéshez
description: Ez a cikk azt ismerteti, hogyan használhatja az Azure AD-t az Azure-beli hitelesítéshez Azure Automationon belül.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576803"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Az Azure AD használata az Azure-beli hitelesítéshez

A [Azure Active Directory (ad)](../active-directory/fundamentals/active-directory-whatis.md) szolgáltatás számos felügyeleti feladatot, például a felhasználók felügyeletét, a tartományi felügyeletet és az egyszeri bejelentkezési konfigurációt teszi lehetővé. Ez a cikk azt ismerteti, hogyan használhatja az Azure AD-t az Azure-ban való hitelesítéshez Azure Automationon belül. 

## <a name="install-azure-ad-modules"></a>Azure AD-modulok telepítése

Az Azure AD-t a következő PowerShell-modulok használatával engedélyezheti:

* Azure Active Directory PowerShell a Graphhoz (AzureRM és az modulok). Azure Automation a AzureRM modult és a legutóbbi frissítését, az az modult. A funkció magában foglalja az Azure nem interaktív hitelesítését az Azure AD User (OrgId) hitelesítőadat-alapú hitelesítés használatával. Lásd: [Azure ad-2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory a Windows PowerShellhez (MSOnline modul). Ez a modul lehetővé teszi az interakciót a Microsoft Online-ral, beleértve a Microsoft 365.

>[!NOTE]
>A PowerShell Core nem támogatja a MSOnline modult. A modul-parancsmagok használatához futtatnia kell őket a Windows PowerShellből. Javasoljuk, hogy a MSOnline modul helyett használja az újabb Azure Active Directory PowerShell-t a Graph-modulokhoz. 

### <a name="preinstallation"></a>Előtelepítési

Mielőtt telepítené az Azure AD-modulokat a számítógépre:

* Távolítsa el a AzureRM/az modul és a MSOnline modul korábbi verzióit. 

* Távolítsa el a Microsoft Online Services Sign-In Assistant eszközt az új PowerShell-modulok helyes működésének biztosításához.  

### <a name="install-the-azurerm-and-az-modules"></a>A AzureRM és az az modulok telepítése

>[!NOTE]
>A modulok használatához a PowerShell 5,1-es vagy újabb verzióját kell használnia a Windows 64-bites verziójával. 

1. Telepítse a Windows Management Framework (WMF) 5,1-es telepítését. Lásd: [WMF 5,1 telepítése és konfigurálása](/powershell/scripting/wmf/setup/install-configure).

2. Telepítse a AzureRM-t és/vagy az az- [t a Windows Azure PowerShell telepítése a PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps)-mel című témakör utasításait követve.

### <a name="install-the-msonline-module"></a>A MSOnline modul telepítése

>[!NOTE]
>A MSOnline modul telepítéséhez rendszergazdai szerepkör tagjának kell lennie. Lásd: [Tudnivalók a rendszergazdai szerepkörökről](/microsoft-365/admin/add-users/about-admin-roles).

1. Győződjön meg arról, hogy a Microsoft .NET Framework 3.5. x funkciója engedélyezve van a számítógépen. Valószínűleg a számítógép újabb verziója van telepítve, de a .NET-keretrendszer régebbi verzióival való visszamenőleges kompatibilitás engedélyezhető vagy letiltható. 

2. Telepítse a [Microsoft Online Services Bejelentkezési segéd](https://www.microsoft.com/Download/details.aspx?id=28177)64 bites verzióját.

3. Futtassa a Windows PowerShellt rendszergazdaként egy emelt szintű Windows PowerShell-parancssor létrehozásához.

4. Azure Active Directory üzembe helyezése a [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0)-ból.

5. Ha a rendszer felszólítja az NuGet-szolgáltató telepítésére, írja be az Y értéket, és nyomja le az ENTER billentyűt.

6. Ha a rendszer kéri, hogy telepítse a modult a [PSGallery](https://www.powershellgallery.com/)-ből, írja be az Y értéket, és nyomja le az ENTER billentyűt

### <a name="install-support-for-pscredential"></a>A PSCredential támogatásának telepítése

A Azure Automation a [PSCredential](/dotnet/api/system.management.automation.pscredential) osztályt használja a hitelesítő adatok megjelenítéséhez. A parancsfájlok lekérik `PSCredential` az objektumokat a `Get-AutomationPSCredential` parancsmag használatával. További információ: [Azure Automationban található hitelesítőadat-eszközök](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Előfizetés-adminisztrátor hozzárendelése

Hozzá kell rendelnie egy rendszergazdát az Azure-előfizetéshez. Ez a személy az előfizetés hatókörének tulajdonosi szerepköre. Lásd: [szerepköralapú hozzáférés-vezérlés Azure Automationban](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Az Azure AD-felhasználó jelszavának módosítása

Az Azure AD-felhasználó jelszavának módosítása:

1. Jelentkezzen ki az Azure-ból.

2. A rendszergazda jelentkezzen be az Azure-ba az imént létrehozott Azure AD-felhasználóként, a teljes felhasználónévvel (a tartománnyal együtt) és egy ideiglenes jelszóval. 

3. Kérje meg a rendszergazdát, hogy változtassa meg a jelszót, amikor a rendszer kéri.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Azure Automation konfigurálása az Azure-előfizetés kezeléséhez

Azure Automation az Azure AD-vel való kommunikációhoz az Azure AD-vel való Azure-kapcsolódáshoz társított hitelesítő adatokat le kell kérni. Ilyen hitelesítő adatok például a bérlő azonosítója, az előfizetés azonosítója és hasonlók. További információ az Azure és az Azure AD közötti kapcsolatról: [a szervezet csatlakoztatása Azure Active Directoryhoz](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Hitelesítőadat-eszköz létrehozása

Az Azure AD számára elérhető Azure-beli hitelesítő adatokkal elkészítheti az Azure AD-beli hitelesítő adatok biztonságos tárolásához szükséges Azure Automation hitelesítőadat-eszköz létrehozását, hogy a runbookok és a Desire State Configuration (DSC) parancsfájlok hozzáférhessenek. Ezt a Azure Portal vagy a PowerShell-parancsmagok használatával teheti meg.

### <a name="create-the-credential-asset-in-azure-portal"></a>A hitelesítőadat-eszköz létrehozása Azure Portal

A hitelesítő adatok létrehozásához használhatja a Azure Portal. Ezt a műveletet az Automation-fiókból hajtsa végre a **megosztott erőforrások** alatt lévő **hitelesítő adatok** használatával. Lásd: [hitelesítő adatok Azure Automationban](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>A hitelesítőadat-eszköz létrehozása a Windows PowerShell használatával

Új hitelesítőadat-eszköz előkészítéséhez a Windows PowerShellben a szkript először létrehoz egy `PSCredential` objektumot a hozzárendelt Felhasználónév és jelszó használatával. A szkript ezután ezt az objektumot használja az eszköz létrehozásához a [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) parancsmag hívásával. Azt is megteheti, hogy a parancsfájl meghívja a [Get-hitelesítőadat](/powershell/module/microsoft.powershell.security/get-credential) parancsmagot, hogy megkérje a felhasználó számára a név és a jelszó megadását. Lásd: [hitelesítő adatok Azure Automationban](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure-erőforrások kezelése Azure Automation runbook

Az Azure-erőforrásokat Azure Automation runbookok kezelheti a hitelesítőadat-eszköz használatával. Az alábbi példa egy PowerShell-runbook, amely az Azure-előfizetésben lévő virtuális gépek leállításához és elindításához használt hitelesítő adatokat gyűjti. Ez a runbook először `Get-AutomationPSCredential` Az Azure-ban való hitelesítéshez használt hitelesítő adatokat kéri le. Ezután meghívja a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot az Azure-hoz való kapcsolódáshoz a hitelesítő adatok használatával. A szkript a [Select-azuresubscription parancsot](/powershell/module/servicemanagement/azure.service/select-azuresubscription) parancsmagot használja, hogy kiválassza az előfizetést, amellyel dolgozni szeretne. 

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

* A hitelesítő adatok használatának részleteit lásd: [a hitelesítő adatok kezelése Azure Automationban](shared-resources/credentials.md).
* A modulokkal kapcsolatos további információkért lásd: [modulok kezelése Azure Automationban](shared-resources/modules.md).
* Ha runbook kell elindítania, tekintse meg [a Runbook elindítása a Azure Automationban](start-runbooks.md)című témakört.
* A PowerShell részleteiért lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
