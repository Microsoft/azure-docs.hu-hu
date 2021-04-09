---
title: Virtuális gép létrehozása a DevTest Labs szolgáltatásban Azure PowerShell
description: Megtudhatja, hogyan hozhat létre és kezelhet Azure DevTest Labs virtuális gépeket Azure PowerShell használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499254"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Virtuális gép létrehozása a DevTest Labs használatával Azure PowerShell
Ez a cikk bemutatja, hogyan hozhat létre virtuális gépet a Azure DevTest Labsban Azure PowerShell használatával. A PowerShell-parancsfájlok segítségével automatizálhatja a virtuális gépek létrehozását a laborban Azure DevTest Labs. 

## <a name="prerequisites"></a>Előfeltételek
Előkészületek:

- [Hozzon létre egy labort](devtest-lab-create-lab.md) , ha nem szeretne meglévő labort használni a szkript vagy a parancsok teszteléséhez ebben a cikkben. 
- [Telepítse a Azure PowerShellt](/powershell/azure/install-az-ps) , vagy használja a Azure Portalba integrált Azure Cloud shell. 

## <a name="powershell-script"></a>PowerShell-parancsprogram
Az ebben a szakaszban szereplő minta parancsfájl a [rehív-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) parancsmagot használja.  Ez a parancsmag a labor erőforrás-AZONOSÍTÓját, a végrehajtandó művelet nevét ( `createEnvironment` ) és a művelet végrehajtásához szükséges paramétereket veszi át. A paraméterek egy olyan kivonatoló táblában találhatók, amely tartalmazza az összes virtuális gép leírásának tulajdonságait. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

A fenti parancsfájlban található virtuális gép tulajdonságai lehetővé teszik, hogy a Windows Server 2016 DataCenter operációs rendszert futtató virtuális gépet hozzon létre operációs rendszerként. Minden egyes virtuális gép esetében ezek a tulajdonságok kis mértékben eltérőek lesznek. A [virtuális gép definiálása](#define-virtual-machine) szakaszban megtudhatja, hogyan határozhatja meg, hogy mely tulajdonságokat szeretné használni ebben a parancsfájlban.

Az alábbi parancs egy példát mutat be a következő fájlnévre mentett parancsfájl futtatására: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Virtuális gép definiálása
Ez a szakasz bemutatja, hogyan kérheti le a létrehozni kívánt virtuális géphez tartozó tulajdonságokat. 

### <a name="use-azure-portal"></a>Az Azure Portal használata
Létrehozhat egy Azure Resource Manager sablont, amikor virtuális gépet hoz létre a Azure Portalban. Nem kell befejeznie a virtuális gép létrehozásának folyamatát. Csak akkor kövesse a lépéseket, amíg meg nem jelenik a sablon. Ez a legjobb módszer a szükséges JSON-Leírás beszerzésére, ha még nincs létrehozva labor virtuális gép. 

1. Navigáljon a [Azure Portal](https://portal.azure.com).
2. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget.
3. Keresse meg és válassza ki a **DevTest Labs** elemet a szolgáltatások listájából. 
4. A **DevTest Labs** lapon válassza ki a labort a laborok listájában.
5. A labor kezdőlapján válassza a **+ Hozzáadás** lehetőséget az eszköztáron. 
6. Válasszon ki egy **alapképet** a virtuális géphez. 
7. Válassza az **automatizálási beállítások** lehetőséget az oldal alján a **Küldés** gomb felett. 
8. Megjelenik a virtuális gép létrehozásához **Azure Resource Manager sablon** . 
9. A **Resources (erőforrások** ) szakaszban található JSON-szegmens a korábban kiválasztott rendszerkép típusának definícióját tartalmazza. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Ebből a példából megtudhatja, hogyan szerezhet be egy Azure Market-rendszerkép definícióját. Egy egyéni rendszerkép, egy képlet vagy egy környezet definícióját is lekérheti. Adja hozzá a virtuális géphez szükséges összes összetevőt, és adja meg a szükséges speciális beállításokat. Miután megadta a kötelező mezők és az opcionális mezők értékét, mielőtt kiválasztja az **Automation Options (automatizálási beállítások** ) gombot.

### <a name="use-azure-rest-api"></a>Az Azure REST API használata
Az alábbi eljárás egy rendszerkép tulajdonságainak beolvasását ismerteti a REST API használatával: ezek a lépések csak egy meglévő virtuális gép esetében működnek a laborban. 

1. Navigáljon a [Virtual Machines-List](/rest/api/dtl/virtualmachines/list) lapra, válassza a **kipróbálás** gombot. 
2. Válassza ki az **Azure-előfizetését**.
3. Adja meg a **laborhoz tartozó erőforráscsoportot**.
4. Adja meg a **labor nevét**. 
5. Válassza a **Futtatás** lehetőséget.
6. A rendszerkép azon **tulajdonságait** látja, amelyeken a virtuális gép létrejött. 

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
Olyan forgatókönyvek esetén, mint például a képzés, a bemutatók és a próbaverziók, érdemes lehet virtuális gépeket létrehozni, és azokat automatikusan törölni rögzített időtartam után, hogy ne kelljen szükségtelen költségeket fizetnie. Megadhat egy virtuális gép lejárati dátumát a PowerShell használatával, ahogy az a [PowerShell-parancsfájl](#powershell-script) című szakaszban is látható.

Itt látható egy PowerShell-parancsfájl, amely a laborban lévő összes meglévő virtuális gép lejárati dátumát állítja be:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő tartalmakat: [Azure PowerShell dokumentációja Azure DevTest Labs](/powershell/module/az.devtestlabs/)
