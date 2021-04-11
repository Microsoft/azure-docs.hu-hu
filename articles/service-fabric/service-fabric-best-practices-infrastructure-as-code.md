---
title: Azure Service Fabric-infrastruktúra a Code ajánlott eljárásainak megfelelően
description: Ajánlott eljárások és kialakítási szempontok az Azure-Service Fabric as-ként való kezeléséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b765d92778df40caec0864dc6f547324216fdb07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611980"
---
# <a name="infrastructure-as-code"></a>Infrastruktúra mint kód

Éles környezetben hozzon létre Azure Service Fabric-fürtöket Resource Manager-sablonok használatával. A Resource Manager-sablonok nagyobb mértékben szabályozzák az erőforrás-tulajdonságokat, és gondoskodnak arról, hogy konzisztens erőforrás-modell álljon rendelkezésre.

A Windows és Linux rendszerhez készült Resource Manager-sablonok a [githubon elérhető Azure-mintákon](https://github.com/Azure-Samples/service-fabric-cluster-templates)érhetők el. Ezek a sablonok a fürt sablonjának kiindulási pontként használhatók. Töltse le `azuredeploy.json` és `azuredeploy.parameters.json` szerkessze őket, hogy megfeleljenek az egyéni követelményeknek.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A `azuredeploy.json` fent letöltött és sablonok üzembe helyezéséhez `azuredeploy.parameters.json` használja az alábbi Azure CLI-parancsokat:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Erőforrás létrehozása a PowerShell használatával

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric-erőforrások

Az alkalmazásait és szolgáltatásait Azure Resource Manager használatával telepítheti a Service Fabric-fürtre. A részletekért lásd: [alkalmazások és szolgáltatások kezelése Azure Resource Manager erőforrásként](./service-fabric-application-arm-resource.md) . A következőkben az ajánlott eljárások Service Fabric a Resource Manager-sablonok erőforrásaiba felvenni kívánt alkalmazás-specifikus erőforrások.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Az alkalmazás Azure Resource Manager használatával történő üzembe helyezéséhez először [létre kell hoznia egy sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) Service Fabric alkalmazáscsomag. A következő Python-szkript egy példa arra, hogyan hozhat létre sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure-beli virtuális gép operációs rendszerének automatikus frissítési konfigurációja 
A virtuális gépek frissítése egy felhasználó által kezdeményezett művelet, ezért javasolt a [virtuálisgép-méretezési csoport automatikus verziófrissítése](service-fabric-patch-orchestration-application.md) az Azure Service Fabric-fürtökön, amelyeken a javítások kezelése történik. A patch-előkészítési alkalmazás egy alternatív megoldás, amely az Azure-on kívüli üzemeltetéshez készült, bár a POA az Azure-ban is használható, és az Azure-ban a szolgáltatási terv általános oka az, hogy a virtuális gép operációs rendszerének automatikus frissítése javasolt a cselekvési terv keretében. A következő a számítási virtuálisgép-méretezési csoport Resource Manager-sablonjának tulajdonságai az automatikus operációs rendszer frissítésének engedélyezéséhez:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Ha Service Fabric-val automatikus operációsrendszer-frissítést használ, az új operációsrendszer-rendszerkép egyszerre egy frissítési tartományba kerül, hogy fenntartsa a Service Fabric futó szolgáltatások magas rendelkezésre állását. Ha az operációs rendszer automatikus frissítését szeretné használni Service Fabric a fürtöt úgy kell konfigurálni, hogy az ezüst tartóssági szintet vagy ennél nagyobbat használjon.

Győződjön meg arról, hogy a következő beállításkulcs hamis értékre van állítva, hogy a Windows-gazdagépek ne kezdeményezzenek összehangolatlan frissítéseket: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

A következő a számítási virtuálisgép-méretezési csoport Resource Manager-sablonjának tulajdonságai a WindowsUpdate beállításkulcs hamis értékre állításához:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric-fürt frissítési konfigurációja
A következő a Service Fabric fürterőforrás-kezelő sablon tulajdonsága az automatikus frissítés engedélyezéséhez:
```json
"upgradeMode": "Automatic",
```
A fürt manuális frissítéséhez töltse le a CAB/deb-eloszlást egy fürtözött virtuális gépre, majd hívja meg a következő PowerShell-t:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Következő lépések

* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése
