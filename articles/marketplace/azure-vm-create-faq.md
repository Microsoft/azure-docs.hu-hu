---
title: Gyakori kérdések az Azure Marketplace-en futó virtuális gépekről
description: A virtuális gépek az Azure Marketplace-en való létrehozásakor felmerülő gyakori kérdések.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 03/10/2021
ms.openlocfilehash: a74170af61c05d07a189b5ceb61dc0c9b7e14298
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200423"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Gyakori kérdések az Azure Marketplace-en futó virtuális gépekről

Ezek a gyakori kérdések (GYIK) olyan gyakori problémákkal foglalkoznak, amelyekkel a virtuális gépek (VM) ajánlatának létrehozásakor találkozhat az Azure piactéren.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hogyan a virtuális magánhálózat (VPN) konfigurálását a virtuális gépekkel való munkavégzéshez?

Ha a Azure Resource Manager üzembe helyezési modellt használja, három lehetőség közül választhat:

- [Route-alapú VPN-átjáró létrehozása a Azure Portal használatával](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Route-alapú VPN-átjáró létrehozása Azure PowerShell használatával](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Route-alapú VPN-átjáró létrehozása a parancssori felület használatával](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Mik a Microsoft-kiszolgálói szoftverek Azure-alapú virtuális gépeken való futtatására vonatkozó Microsoft-támogatási szabályzatok?

[Az Microsoft Azure Virtual Machines szolgáltatással kapcsolatos](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)további részletekért tekintse meg a Microsoft Server szoftver támogatását.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Egy virtuális gépen Hogyan kezelhetem az egyéni szkriptek bővítményét az indítási feladatban?

További információ az egyéni szkriptek bővítmény használatáról a Azure PowerShell modullal, Azure Resource Manager sablonokkal és a Windows rendszerekkel kapcsolatos hibaelhárítási lépésekkel kapcsolatban: [egyéni parancsfájl-bővítmény a Windowshoz](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Támogatottak-e a 32 bites alkalmazások vagy szolgáltatások az Azure Marketplace-en?

Nem. Az Azure-beli virtuális gépekhez támogatott operációs rendszerek és standard szolgáltatások mind 64 bitesek. Bár a legtöbb 64 bites operációs rendszer támogatja az alkalmazások 32 bites verzióit a visszamenőleges kompatibilitás érdekében, az 32 bites alkalmazások a virtuálisgép-megoldás részeként való használata nem támogatott és nem ajánlott. Hozza létre újra az alkalmazást 64 bites projektként.

További információért lásd a következő cikkeket:

- [32 bites alkalmazások futtatása](/windows/desktop/WinProg64/running-32-bit-applications)
- [32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft kiszolgálószoftveres támogatás a Microsoft Azure Virtual Machines szolgáltatáshoz](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Hiba: a virtuális merevlemez már regisztrálva van a rendszerkép-tárházban erőforrásként

Minden alkalommal, amikor megpróbálok létrehozni egy rendszerképet a virtuális merevlemezekről, a következő hibaüzenet jelenik meg: "a VHD már regisztrálva van a rendszerkép-tárházban erőforrásként" a Azure PowerShellban. Nem hoztam létre a képet, és nem találtam ilyen nevű képet az Azure-ban. Hogyan oldhatom meg ezt?

Ez a probléma általában akkor jelenik meg, ha olyan virtuális merevlemezről hozta létre a virtuális gépet, amelynek van zárolása. Győződjön meg arról, hogy nincs lefoglalva virtuális gép a virtuális merevlemezről, majd próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, nyisson meg egy támogatási jegyet. Lásd: [a partner Center támogatása](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Hogyan létrehozni egy virtuális gépet egy általánosított virtuális merevlemezből?

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager sablon előkészítése

Ez a szakasz azt ismerteti, hogyan hozhat létre és helyezhet üzembe egy felhasználó által megadott virtuálisgép-lemezképet. Ezt úgy teheti meg, hogy az operációs rendszer és az adatlemez VHD-lemezképeit egy Azure-beli üzembe helyezett virtuális merevlemezről biztosítja. Ezek a lépések általánosított VHD használatával helyezik üzembe a virtuális gépet.

1. Jelentkezzen be az Azure portálra.
2. Töltse fel az általánosított operációs rendszer VHD-jét és az adatlemez virtuális merevlemezeit az Azure Storage-fiókjába.
3. A kezdőlapon válassza az erőforrás létrehozása lehetőséget, keressen rá a "sablon központi telepítése" kifejezésre, és válassza a létrehozás lehetőséget.
4. Válassza a saját sablon létrehozása lehetőséget a szerkesztőben.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="A sablon kijelölését jeleníti meg":::

5. Illessze be a következő JSON-sablont a Szerkesztőbe, majd válassza a mentés lehetőséget.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Adja meg a paraméterek értékeit az egyéni központi telepítési tulajdonságlapon.

 **1. TÁBLÁZAT**

| **ResourceGroupName** | **Meglévő Azure-erőforráscsoport neve. Általában ugyanazt a RG-t használja, mint a Key Vault.** |
| --- | --- |
| TemplateFile | A (z) VHDtoImage.jsfájl teljes elérési útja. |
| userStorageAccountName | A Storage-fiók neve. |
| dnsNameForPublicIP | A nyilvános IP-cím DNS-neve; kisbetűsnek kell lennie. |
| subscriptionId | Azure-előfizetés azonosítója. |
| Hely | Az erőforráscsoport szabványos Azure-beli földrajzi helye. |
| vmName | A virtuális gép neve. |
| vhdUrl | A virtuális merevlemez webcíme. |
| vmSize | A virtuálisgép-példány mérete. |
| publicIPAddressName | A nyilvános IP-cím neve. |
| virtualNetworkName | A virtuális hálózat neve. |
| nicName | A virtuális hálózat hálózati kártyájának neve. |
| adminUserName | A rendszergazdai fiók felhasználóneve. |
| adminPassword | Rendszergazdai jelszó. |
|

7. Az értékek megadása után válassza a vásárlás lehetőséget.

Az Azure elindítja az üzembe helyezést. Egy új virtuális gépet hoz létre a megadott nem felügyelt VHD-vel a megadott Storage-fiók elérési útján. A Azure Portal előrehaladását a portál bal oldalán található Virtual Machines kiválasztásával követheti nyomon. A virtuális gép létrehozása után az állapot az indítástól a futtatásig változik.

A 2. generációs virtuális gépek üzembe helyezéséhez használja a következő sablont:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Azure-beli virtuális gép üzembe helyezése a PowerShell-lel

Másolja és szerkessze a következő parancsfájlt a és a változók értékeinek megadásához `$storageaccount` `$vhdUrl` . Futtassa azt egy Azure-beli virtuálisgép-erőforrás létrehozásához a meglévő általánosított VHD-ről.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Következő lépések

- [Virtuális gépek tanúsításával kapcsolatos hibaelhárítás](azure-vm-create-certification-faq.md)
