---
title: Windows 10 üzembe helyezése az Azure-ban több-bérlős üzemeltetési jogosultságokkal
description: Ismerje meg, hogyan maximalizálhatja a Windows-frissítési garanciát, hogy a helyszíni licenceket az Azure-ba több-bérlős üzemeltetési jogosultságokkal lehessen elérni.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555771"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Windows 10 üzembe helyezése az Azure-ban több-bérlős üzemeltetési jogosultságokkal 
A Windows 10 Enterprise E3/E5 felhasználónkénti vagy a Windows virtuális asztali hozzáférés felhasználónként (felhasználói előfizetési licencek vagy kiegészítő felhasználói előfizetési licencek) rendelkező ügyfelei számára a Windows 10 rendszerhez készült több-bérlős üzemeltetési jogosultság lehetővé teszi a Windows 10-es licencek felhőbe való bevezetését, valamint a Windows 10 Virtual Machines Azure-beli futtatását anélkül, hogy más licenccel kellene fizetnie. A több-bérlős üzemeltetési jogosultságok csak a Windows 10 rendszerhez (1703-es vagy újabb verzió) érhetők el.

További információ: több- [bérlős üzemeltetés a Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)rendszerhez.

> [!NOTE]
> - Windows 7, 8,1 és 10 rendszerkép használata fejlesztéshez vagy teszteléshez lásd: [Windows-ügyfél az Azure-ban fejlesztési és tesztelési forgatókönyvekhez](client-images.md)
> - A Windows Server licencelési előnyeinek kihasználásához tekintse meg a [Windows Server rendszerképekhez készült Azure Hybrid use Benefits](hybrid-use-benefit-licensing.md)című témakört.

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>A több-bérlős üzemeltetési jogosultságokra jogosult előfizetési licencek

A [Microsoft felügyeleti központjának](/microsoft-365/admin/admin-overview/about-the-admin-center)használatával ellenőrizheti, hogy egy felhasználóhoz van-e hozzárendelve Windows 10 támogatott licenc.

> [!IMPORTANT]
> A felhasználóknak az alábbi előfizetési licencek egyikével kell rendelkezniük ahhoz, hogy Windows 10 rendszerképeket használjanak az Azure-ban. Ha nem rendelkezik ezekkel az előfizetési licencekkel, akkor a [Cloud Service-partneren](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) keresztül vagy közvetlenül a [Microsofton](https://www.microsoft.com/microsoft-365?rtc=1)keresztül vásárolhatók meg.

**Jogosult előfizetési licencek:**

-   E3/E5 Microsoft 365 
-   Microsoft 365 F3 
-   Microsoft 365 a3/a5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 Education a3/a5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Windows 10 rendszerkép üzembe helyezése az Azure Marketplace-en 
A PowerShell, a parancssori felület és a Azure Resource Manager sablon központi telepítése esetén a Windows 10-es rendszerképeket a és a használatával lehet megtalálni `PublisherName: MicrosoftWindowsDesktop` `Offer: Windows-10` . A Windows 10-es verzió-létrehozói frissítés (1809) vagy újabb verziója támogatott a több-bérlős üzemeltetési jogosultságok esetén. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

További információ az elérhető lemezképekről: [Azure Marketplace virtuálisgép-rendszerképek keresése és használata Azure PowerShell](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 virtuális merevlemez feltöltése az Azure-ba
Ha általánosított Windows 10 virtuális merevlemezt tölt fel, vegye figyelembe, hogy a Windows 10 alapértelmezés szerint nincs engedélyezve a beépített rendszergazdai fiókkal. A beépített rendszergazdai fiók engedélyezéséhez a következő parancsot adja meg az egyéni szkriptek bővítményének részeként.

```powershell
Net user <username> /active:yes
```

A következő PowerShell-kódrészlet az összes rendszergazdai fiók aktívként való megjelölése, beleértve a beépített rendszergazdát is. Ez a példa akkor hasznos, ha a beépített rendszergazda felhasználóneve ismeretlen.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
További információk: 
* [Virtuális merevlemez feltöltése az Azure-ba](upload-generalized-managed.md)
* [Windows rendszerű virtuális merevlemez előkészítése az Azure-ba való feltöltésre](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>A Windows 10 telepítése több-bérlős üzemeltetési jogosultságokkal
Győződjön meg arról, hogy [telepítette és konfigurálta a legújabb Azure PowerShell](/powershell/azure/). Miután előkészítette a VHD-t, töltse fel a virtuális merevlemezt az Azure Storage-fiókjába az `Add-AzVhd` alábbi parancsmaggal:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Üzembe helyezés Azure Resource Manager sablon használatával** A Resource Manager-sablonokon belül egy további paraméter is megadható `licenseType` . További információ a [Azure Resource Manager sablonok létrehozásáról](../../azure-resource-manager/templates/template-syntax.md). Miután feltöltötte a VHD-t az Azure-ba, szerkessze a Resource Manager-sablont, hogy tartalmazza a licenc típusát a számítási szolgáltató részeként, és telepítse a sablont a szokásos módon:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Üzembe helyezés a PowerShell** használatával A Windows Server rendszerű virtuális gép PowerShell használatával történő telepítésekor további paramétert is megadhat `-LicenseType` . Miután feltöltötte a VHD-t az Azure-ba, létrehoz egy virtuális gépet a használatával, `New-AzVM` és megadja a licencelés típusát a következő módon:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Annak ellenőrzése, hogy a virtuális gép használja-e a licencelési kedvezményt
Miután telepítette a virtuális gépet a PowerShell vagy a Resource Manager üzembe helyezési módszerével, ellenőrizze a licenc típusát a `Get-AzVM` következő módon:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A kimenet a következő példához hasonló a Windows 10-es megfelelő licenccel:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ez a kimenet ellentétben áll a következő, Azure Hybrid Use Benefit licencelés nélkül üzembe helyezett virtuális géppel, például egy közvetlenül az Azure-katalógusból üzembe helyezett virtuális géppel:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>További információ az Azure AD-hez való csatlakozásról
Az Azure minden olyan Windowsos virtuális gépet kiépít, amely beépített rendszergazdai fiókkal rendelkezik, ezért nem használható a HRE való csatlakozáshoz. Például a *beállítások > a fiók > hozzáférés munkahelyi vagy iskolai > + kapcsolódás* nem fog működni. Az Azure AD-hez való csatlakozáshoz második rendszergazdai fiókot kell létrehoznia és bejelentkeznie. Az Azure AD-t egy kiépítési csomaggal is konfigurálhatja, ha további információkat szeretne megtudni, használja a *következő lépések* szakaszban található hivatkozást.

## <a name="next-steps"></a>Következő lépések
- További információ a [Windows 10 rendszerhez készült VDA konfigurálásáról](/windows/deployment/vda-subscription-activation)
- További információ a [Windows 10 rendszerhez készült több-bérlős üzemeltetésről](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)