---
title: Azure Marketplace-lemezképek és-csomagok keresése és használata
description: A Azure PowerShell használatával megkeresheti és használhatja a Piactéri virtuálisgép-rendszerképek közzétevői, ajánlati, SKU-, verzió-és megtervezési információit.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906267"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Az Azure Marketplace virtuálisgép-rendszerképeinek megkeresése és használata Azure PowerShell     

Ez a cikk azt ismerteti, hogyan használhatók a Azure PowerShell a virtuálisgép-rendszerképek megkereséséhez az Azure Marketplace-en. Ezután megadhatja a piactér rendszerképét, és megtervezheti a virtuális gépek létrehozásakor feladatait.

Az elérhető lemezképek és ajánlatok az [Azure Marketplace](https://azuremarketplace.microsoft.com/) kirakat, a [Azure Portal](https://portal.azure.com)vagy az [Azure CLI](../linux/cli-ps-findimage.md)használatával is tallózhatók. 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Virtuális gép létrehozása a csomaggal kapcsolatos információkkal

Ha rendelkezik egy meglévő VHD-vel, amelyet egy Azure Marketplace-rendszerkép használatával hoztak létre, lehet, hogy meg kell adnia a vásárlási terv adatait, amikor új virtuális gépet hoz létre a virtuális merevlemezről.

Ha továbbra is az eredeti virtuális gép vagy egy másik, azonos rendszerképből létrehozott virtuális gép található, a Get-AzVM használatával beszerezheti a csomag nevét, közzétevőjét és termékét. Ez a példa egy *myVM* nevű virtuális gépet olvas be a *myResourceGroup* -erőforráscsoporthoz, majd megjeleníti a vásárlási terv információit.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Ha nem kapta meg a csomag információit az eredeti virtuális gép törlése előtt, akkor egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support)is megadhat. Szükségük lesz a virtuális gép nevére, az előfizetés azonosítójára és a törlési művelet időbélyegzőre.

Virtuális gép virtuális merevlemez használatával történő létrehozásához tekintse meg a [virtuális gép létrehozása speciális virtuális merevlemezről](create-vm-specialized.md) című cikket, és adjon hozzá egy sort a terv adatainak a virtuálisgép-konfigurációhoz való hozzáadásához a [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) hasonló módon, a következőhöz hasonlóan:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Új virtuális gép létrehozása Piactéri rendszerképből

Ha már rendelkezik a használni kívánt rendszerképekkel, akkor átadhatja ezeket az információkat a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) parancsmagban, hogy képet adjon a virtuálisgép-konfigurációhoz. A piactéren elérhető rendszerképek kereséséhez és listázásához tekintse meg a következő szakaszt.

Egyes fizetős lemezképek esetében a [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan)használatával is meg kell adnia a vásárlási tervre vonatkozó információkat. 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Ezután átadja a virtuális gép konfigurációját, valamint a többi konfigurációs objektumot a `New-AzVM` parancsmaghoz. A virtuális gépek PowerShell használatával történő konfigurálásával kapcsolatos részletes példákért tekintse meg ezt a [parancsfájlt](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Ha a rendszerképek elfogadásával kapcsolatos üzenet jelenik meg, tekintse meg a jelen cikk későbbi részében található [feltételek elfogadása](#accept-the-terms) című szakaszt.

## <a name="list-images"></a>Lemezképek listázása

Az egyik lehetőség, hogy egy helyen található rendszerképet keres, a [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), a [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)és a [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) parancsmagokat a következő sorrendben futtassa:

1. Listázza a rendszerkép-közzétevőket.
2. Listázza egy adott közzétevő ajánlatait.
3. Listázza egy adott ajánlathoz tartozó termékváltozatokat.

Ezután a kiválasztott SKU-hoz futtassa a [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) parancsot a telepítendő verziók listázásához.

1. Közzétevők listázása:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Adja meg a választott közzétevő nevét, és sorolja fel az ajánlatokat:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Adja meg a választott ajánlat nevét, és sorolja fel az SKU-ket:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Adja meg a választott SKU-nevet, és szerezze be a rendszerkép verzióját:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
A parancs kimenetében `Get-AzVMImage` kiválaszthatja az új virtuális gép üzembe helyezéséhez használt lemezképet.

Az alábbi példa a parancsok és azok kimenetének teljes sorát mutatja be:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Részleges kimenet:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

A *MicrosoftWindowsServer* -közzétevő esetében:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Kimenet:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

A *windowsserver* ajánlathoz:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Részleges kimenet:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Ezután az *2019-Datacenter* SKU esetében:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Most már egyesítheti a kiválasztott közzétevőt, ajánlatot, SKU-t és verziót egy URN (:) által elválasztott értékekkel). `--image`Ha a [New-AzVM](/powershell/module/az.compute/new-azvm) parancsmaggal hoz létre egy virtuális gépet, adja át ezt az urn-t a paraméterrel. A lemezkép legújabb verziójának lekéréséhez igény szerint lecserélheti az URN verziószámát a "legutóbbi" értékre.

Ha Resource Manager-sablonnal helyez üzembe egy virtuális gépet, akkor a tulajdonságok paramétereit egyenként kell beállítania `imageReference` . Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Terv tulajdonságainak megtekintése

Ha meg szeretné tekinteni a rendszerkép vásárlási tervének adatait, futtassa a `Get-AzVMImage` parancsmagot. Ha a `PurchasePlan` kimenetben szereplő tulajdonság nem `null` , a rendszerképnek a programozott üzembe helyezés előtt el kell fogadnia a feltételeket.  

Például a *Windows Server 2016 Datacenter* -rendszerkép nem rendelkezik további feltételekkel, így az `PurchasePlan` információ `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Az alábbi példa egy hasonló parancsot mutat be a *Data Science Virtual Machine-Windows 2016* rendszerképhez, amely a következő `PurchasePlan` tulajdonságokkal rendelkezik:, `name` `product` és `publisher` . Néhány rendszerkép is rendelkezik egy `promotion code` tulajdonsággal. A rendszerkép üzembe helyezéséhez tekintse át a következő részeket a feltételek elfogadásához és a programozott telepítés engedélyezéséhez.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Kimenet:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>A feltételek elfogadása

A licencfeltételek megtekintéséhez használja a [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) parancsmagot, és adja meg a vásárlási terv paramétereit. A kimenet a Piactéri rendszerkép feltételeire mutató hivatkozást tartalmaz, és megjeleníti, hogy korábban elfogadta-e a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Kimenet:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Használja a [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) parancsmagot a feltételek elfogadásához vagy elutasításához. A rendszerképhez csak egyszer kell elfogadnia a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Kimenet:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Következő lépések

A virtuális gép `New-AzVM` egyszerű rendszerkép-információkkal történő gyors létrehozásával kapcsolatban lásd: [Windows rendszerű virtuális gép létrehozása a PowerShell](quick-create-powershell.md)használatával.

További információ az Azure Marketplace-lemezképek egyéni lemezképek létrehozásához megosztott képtárban való használatáról: az [Azure Marketplace vásárlási terv információinak megadása a lemezképek létrehozásakor](../marketplace-images.md).
