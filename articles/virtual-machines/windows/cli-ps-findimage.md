---
title: Piactér-vásárlási terv adatainak keresése és használata a PowerShell használatával
description: A Azure PowerShell használatával megkeresheti a lemezképek és a vásárlási terv paramétereit, például a közzétevőt, az ajánlatot, az SKU-t és a verziót a piactéren futó virtuálisgép-lemezképekhez
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 34fd6720b93a1462836b51856d73573a86809367
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022823"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Az Azure Marketplace virtuálisgép-rendszerképeinek megkeresése és használata Azure PowerShell

Ez a cikk azt ismerteti, hogyan használhatók a Azure PowerShell a virtuálisgép-rendszerképek megkereséséhez az Azure Marketplace-en. Ezután megadhatja a piactér rendszerképét, és megtervezheti a virtuális gépek létrehozásakor feladatait.

Az elérhető lemezképek és ajánlatok az [Azure Marketplace](https://azuremarketplace.microsoft.com/) vagy az [Azure CLI](../linux/cli-ps-findimage.md)használatával is tallózhatók. 

## <a name="terminology"></a>Terminológia

Az Azure Piactéri rendszerképének jellemzői a következők:

* **Közzétevő**: az a szervezet, amely létrehozta a rendszerképet. Példák: Canonical, MicrosoftWindowsServer
* **Ajánlat**: a közzétevő által létrehozott kapcsolódó rendszerképek csoportjának neve. Példák: UbuntuServer, WindowsServer
* **SKU**: egy ajánlat egy példánya, például egy terjesztés fő kiadása. Példák: 18,04-LTS, 2019-Datacenter
* **Version (verzió**): egy RENDSZERKÉP-SKU verziószáma. 

Ezek az értékek a kettősponttal elválasztott értékek (:). Például: *közzétevő*:*ajánlat*:*SKU*:*Version*. Az URN verziószámát lecserélheti a `latest` lemezkép legújabb verziójának használatára. 

Ha a rendszerkép kiadója további licenceket és vásárlási feltételeket is tartalmaz, akkor a rendszerkép használata előtt el kell fogadnia ezeket.  További információ: a [vásárlási terv feltételeinek elfogadása](#accept-purchase-plan-terms).

## <a name="list-images"></a>Lemezképek listázása

A PowerShell segítségével szűkítheti a képek listáját. Cserélje le a változók értékeit az igényeinek megfelelően.

1. A [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher)használatával sorolja fel a rendszerkép közzétevőit.
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Egy adott közzétevő esetében sorolja fel ajánlatait a [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)használatával.
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Egy adott közzétevő és ajánlat esetében sorolja fel a [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)használatával elérhető SKU-ket.
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. SKU esetén a [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage)használatával sorolja fel a rendszerkép verzióját.

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    Akkor is használhatja, `latest` Ha a legújabb lemezképet szeretné használni, és nem egy adott régebbi verziót.


Most már egyesítheti a kiválasztott közzétevőt, ajánlatot, SKU-t és verziót egy URN (:) által elválasztott értékekkel). `--image`Ha a [New-AzVM](/powershell/module/az.compute/new-azvm) parancsmaggal hoz létre egy virtuális gépet, adja át ezt az urn-t a paraméterrel. Az URN-ben lévő verziószámot lecserélheti a (a) verzióra, `latest` hogy lekérje a lemezkép legújabb verzióját.

Ha Resource Manager-sablonnal helyez üzembe egy virtuális gépet, akkor a tulajdonságok paramétereit egyenként kell beállítania `imageReference` . Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Vásárlási terv tulajdonságainak megtekintése

Az Azure Marketplace-en néhány virtuálisgép-rendszerkép további licenccel és vásárlási feltételekkel rendelkezik, amelyeket el kell fogadnia, mielőtt programozott módon telepítené őket. Az előfizetéshez csak egyszer kell elfogadnia a képet.

Ha meg szeretné tekinteni a rendszerkép vásárlási tervének adatait, futtassa a `Get-AzVMImage` parancsmagot. Ha a `PurchasePlan` kimenetben szereplő tulajdonság nem `null` , a rendszerképnek a programozott üzembe helyezés előtt el kell fogadnia a feltételeket.  

Például a *Windows Server 2016 Datacenter* -rendszerkép nem rendelkezik további feltételekkel, így az `PurchasePlan` információ `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

A kimenet az alábbihoz hasonlóan fog kinézni:

```output
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

A kimenet az alábbihoz hasonlóan fog kinézni:

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

A licencfeltételek megtekintéséhez használja a [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) parancsmagot, és adja meg a vásárlási terv paramétereit. A kimenet a Piactéri rendszerkép feltételeire mutató hivatkozást tartalmaz, és megjeleníti, hogy korábban elfogadta-e a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

A kimenet az alábbihoz hasonlóan fog kinézni:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Vásárlási terv feltételeinek elfogadása

Használja a [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) parancsmagot a feltételek elfogadásához vagy elutasításához. A rendszerképhez csak egyszer kell elfogadnia a feltételeket. Ügyeljen arra, hogy az összes kisbetűs betűt használja a paraméterek értékeiben. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
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

Ha megjelenik egy üzenet, amely a rendszerkép feltételeit fogadja el, tekintse meg a [vásárlási terv feltételeinek elfogadása](#accept-purchase-plan-terms)című szakaszt.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Új virtuális gép létrehozása a vásárlási terv adataival rendelkező virtuális merevlemezről

Ha rendelkezik egy meglévő VHD-vel, amelyet egy Azure Marketplace-rendszerkép használatával hoztak létre, lehet, hogy meg kell adnia a vásárlási terv adatait, amikor új virtuális gépet hoz létre a virtuális merevlemezről.

Ha továbbra is az eredeti virtuális gép vagy egy másik, azonos rendszerképből létrehozott virtuális gép található, a Get-AzVM használatával beszerezheti a csomag nevét, közzétevőjét és termékét. Ez a példa egy *myVM* nevű virtuális gépet olvas be a *myResourceGroup* -erőforráscsoporthoz, majd megjeleníti a vásárlási terv információit.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Ha nem kapta meg a csomag információit az eredeti virtuális gép törlése előtt, akkor egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support)is megadhat. Szükségük lesz a virtuális gép nevére, az előfizetés AZONOSÍTÓJÁRA és a törlési művelet időbélyegzőre.

Virtuális gép virtuális merevlemez használatával történő létrehozásához tekintse meg a [virtuális gép létrehozása speciális virtuális merevlemezről](create-vm-specialized.md) című cikket, és adjon hozzá egy sort a terv adatainak a virtuálisgép-konfigurációhoz való hozzáadásához a [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) hasonló módon, a következőhöz hasonlóan:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Következő lépések

A virtuális gép `New-AzVM` egyszerű rendszerkép-információkkal történő gyors létrehozásával kapcsolatban lásd: [Windows rendszerű virtuális gép létrehozása a PowerShell](quick-create-powershell.md)használatával.

További információ az Azure Marketplace-lemezképek egyéni lemezképek létrehozásához megosztott képtárban való használatáról: az [Azure Marketplace vásárlási terv információinak megadása a lemezképek létrehozásakor](../marketplace-images.md).
