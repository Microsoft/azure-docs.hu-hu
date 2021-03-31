---
title: Service Fabric Azure Files kötet-illesztőprogram
description: A Service Fabric a Azure Files használatával támogatja a kötetek biztonsági mentését a tárolóból.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247693"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Service Fabric Azure Files kötet-illesztőprogram

A Azure Files kötet-illesztőprogram egy [Docker-kötet beépülő modul](https://docs.docker.com/engine/extend/plugins_volume/) , amely [Azure Files](../storage/files/storage-files-introduction.md) -alapú köteteket biztosít a Docker-tárolók számára. Service Fabric alkalmazásként van csomagolva, amely Service Fabric-fürtön telepíthető, hogy a fürtön belül más Service Fabric-tároló alkalmazások számára is biztosítson köteteket.

> [!NOTE]
> A Azure Files mennyiségi beépülő modul 6.5.661.9590 verziója elérhető az általános rendelkezésre állás érdekében.
>

## <a name="prerequisites"></a>Előfeltételek
* A Azure Files kötet beépülő modul Windows-verziója a Windows [Server 1709](/windows-server/get-started/whats-new-in-windows-server-1709)-es verzióján, a [Windows 10 1709](/windows/whats-new/whats-new-windows-10-version-1709) -es vagy újabb operációs rendszereken is működik.

* A Azure Files kötet beépülő modul linuxos verziója a Service Fabric által támogatott összes operációsrendszer-verzión működik.

* A Azure Files Volume beépülő modul csak Service Fabric 6,2-es és újabb verziókban működik.

* A [Azure Files dokumentációjában](../storage/files/storage-how-to-create-file-share.md) található utasításokat követve hozzon létre egy fájlmegosztást a Service Fabric Container-alkalmazás számára, amely kötetként használható.

* Szüksége lesz [a powershellre a Service Fabric modul vagy a](./service-fabric-get-started.md) telepített [SFCTL](./service-fabric-cli.md) .

* Ha Hyper-V tárolókat használ, a következő kódrészleteket hozzá kell adni a Azure Resource Manager sablonjában (Azure-fürt) vagy ClusterConfig.js(különálló fürtön) található ClusterManifest (helyi fürt) vagy a fabricSettings szakaszban.

A ClusterManifest a következőt kell hozzáadnia az üzemeltetés szakaszhoz. Ebben a példában a kötet neve **sfazurefile** , és a fürtön figyelt port **19100**. Cserélje le őket a fürt megfelelő értékeire.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

A Azure Resource Manager sablonjának fabricSettings szakaszában (az Azure-környezetek esetében) vagy ClusterConfig.js(különálló központi telepítésekhez) a következő kódrészletet kell hozzáadnia. Ismét cserélje le a kötet nevét és a portok értékeit a saját adataira.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Minta alkalmazás üzembe helyezése Service Fabric Azure Files kötet-illesztőprogram használatával

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Azure Resource Manager használata a megadott PowerShell-parancsfájl segítségével (ajánlott)

Ha a fürt az Azure-ban alapul, javasoljuk, hogy az alkalmazások üzembe helyezéséhez használja a Azure Resource Manager alkalmazás-erőforrás modelljét a könnyű használat érdekében, és hogy segítsen a modellben az infrastruktúra mint kód fenntartásában. Ez a megközelítés nem szükséges a Azure Files kötet-illesztőprogramhoz tartozó alkalmazás verziójának nyomon követéséhez. Emellett lehetővé teszi külön Azure Resource Manager sablonok fenntartását minden támogatott operációs rendszerhez. A parancsfájl feltételezi, hogy telepíti a Azure Files alkalmazás legújabb verzióját, és paramétereket fogad az operációs rendszer típusa, a fürt előfizetési azonosítója és az erőforráscsoport számára. A szkriptet a [Service Fabric letöltési webhelyről](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)töltheti le. Vegye figyelembe, hogy ez automatikusan beállítja a ListenPort, amely az a port, amelyen a Azure Files kötet beépülő modul a Docker-démontól érkező kéréseket figyeli, 19100-re. A "listenPort" nevű paraméter hozzáadásával módosíthatja. Győződjön meg arról, hogy a port nem ütközik más, a fürt vagy az alkalmazásai által használt porttal.
 

Azure Resource Manager központi telepítési parancs a Windows rendszerhez:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

A Linux rendszerhez készült Azure Resource Manager üzembe helyezési parancs:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Ha sikeresen futtatta a szkriptet, ugorjon az [alkalmazás konfigurálása szakaszra.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Manuális üzembe helyezés önálló fürtökhöz

A tárolók köteteit biztosító Service Fabric alkalmazást a [Service Fabric letöltési helyről](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)töltheti le. Az alkalmazás a fürtön a [PowerShell](./service-fabric-deploy-remove-applications.md), a [CLI](./service-fabric-application-lifecycle-sfctl.md) vagy a [FabricClient API](./service-fabric-deploy-remove-applications-fabricclient.md)-k használatával telepíthető.

1. A parancssor használatával váltson át a letöltött alkalmazáscsomag gyökérkönyvtárára.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Ezután másolja az alkalmazáscsomag a rendszerkép-tárolóba a [ApplicationPackagePath] és a [ImageStoreConnectionString] megfelelő értékeivel:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Az alkalmazás típusának regisztrálása

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Hozza létre az alkalmazást, és ügyeljen arra, hogy a **ListenPort** alkalmazás paramétereinek értéke közel legyen. Ez az érték az a port, amelyen a Azure Files kötet beépülő modul a Docker-démontól érkező kéréseket figyeli. Győződjön meg arról, hogy az alkalmazás számára megadott port megfelel a ClusterManifest lévő VolumePluginPorts, és nem ütközik a fürt vagy az alkalmazások által használt többi porttal.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> A Windows Server 2016 Datacenter nem támogatja az SMB-csatlakoztatások tárolóhoz való leképezését ([Ez csak a Windows Server 1709-es verziójában támogatott](/virtualization/windowscontainers/manage-containers/container-storage)). Ez a korlátozás megakadályozza a hálózati kötetek leképezését és a 1709-nál régebbi verziókban Azure Files kötet-illesztőprogramokat.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Az alkalmazás üzembe helyezése helyi fejlesztési fürtön
Kövesse a [fenti 1-3. lépést.](#manual-deployment-for-standalone-clusters)

 A Azure Files Volume plugin alkalmazáshoz tartozó alapértelmezett szolgáltatási példányok száma-1, ami azt jelenti, hogy a fürt minden csomópontján telepítve van a szolgáltatás egy példánya. Ha azonban a Azure Files Volume beépülő modult egy helyi fejlesztési fürtön helyezi üzembe, a szolgáltatási példányok számának 1-ként kell megadnia. Ezt a **InstanceCount** Application paraméterrel teheti meg. Ezért a Azure Files Volume beépülő modul alkalmazás helyi fejlesztési fürtön való létrehozásának parancsa a következő:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Alkalmazások konfigurálása a kötet használatára
A következő kódrészlet azt mutatja be, hogyan lehet megadni egy Azure Files alapú kötetet az alkalmazás alkalmazás-jegyzékfájljában. A kamat adott eleme a **kötet** címkéje:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

A Azure Files kötethez tartozó beépülő modul illesztőprogramjának neve **sfazurefile**. Ez az érték az alkalmazás jegyzékfájljának **kötetcímke** elemének **illesztőprogram** -attribútumára van beállítva.

A fenti kódrészletben a **kötet** címkéjén a Azure Files Volume beépülő modulhoz a következő attribútumok szükségesek:
- **Forrás** – ez a kötet neve. A felhasználó bármilyen nevet kiválaszthat a kötethez.
- **Cél** – ez az attribútum az a hely, ahol a kötet a futó tárolón belül van leképezve. Így a célhely nem lehet olyan hely, amely már létezik a tárolón belül

Ahogy a fenti kódrészletben a **DriverOption** elemek is láthatók, a Azure Files kötet beépülő modul a következő illesztőprogram-beállításokat támogatja:
- **megosztásnév** – a tárolóhoz tartozó kötetet biztosító Azure Files fájlmegosztás neve.
- **storageAccountName** – az Azure Files fájlmegosztást tartalmazó Azure Storage-fiók neve.
- a Azure Files fájlmegosztást tartalmazó Azure Storage-fiók **storageAccountKey** kulcsa.
- **storageAccountFQDN** – a Storage-fiókhoz társított tartománynév. Ha a storageAccountFQDN nincs megadva, a rendszer a tartománynevet az alapértelmezett utótag (. file. Core. Windows. net) használatával hozza létre a storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Saját kötet vagy naplózási illesztőprogram használata
A Service Fabric a saját egyéni [kötet](https://docs.docker.com/engine/extend/plugins_volume/) -vagy [naplózási](https://docs.docker.com/engine/admin/logging/overview/) illesztőprogramok használatát is lehetővé teszi. Ha a Docker mennyiségi/naplózási illesztőprogramja nincs telepítve a fürtön, manuálisan is telepítheti az RDP/SSH protokoll használatával. A telepítést a következő protokollokkal végezheti el egy [virtuálisgép-méretezési csoport indítási parancsfájljában](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) vagy egy [SetupEntryPoint-parancsfájl](./service-fabric-application-model.md)használatával.

Az Azure-hoz készült [Docker-kötet illesztőprogramjának telepítéséhez szükséges](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) parancsfájl például a következő:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Az alkalmazásokban a telepített kötet-vagy naplózási illesztőprogram használatához meg kell adnia a megfelelő értékeket a **kötet** és a **LogConfig** elemben az alkalmazás jegyzékfájljának **ContainerHostPolicies** területén.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

A mennyiségi beépülő modul megadásakor a Service Fabric automatikusan létrehozza a kötetet a megadott paraméterek használatával. A **kötet** elemhez tartozó **forrásoldali** címke a kötet neve, az **illesztőprogram** -címke pedig megadja a kötet-illesztőprogram beépülő modulját. A **célként** megadott címke a **forrásnak** a futó tárolón belüli leképezett helye. Így a célhely nem lehet olyan hely, amely már létezik a tárolón belül. A beállítások a **DriverOption** címke használatával adhatók meg a következő módon:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Az alkalmazás paraméterei támogatottak a kötetek esetében, ahogy az előző jegyzékfájlban látható ( `MyStorageVar` példaként használandó).

Ha meg van adva egy Docker-napló illesztőprogramja, az ügynököket (vagy tárolókat) kell telepítenie a fürtben lévő naplók kezelésére. A **DriverOption** címke segítségével megadhatja a napló illesztőprogramjának beállításait.

## <a name="next-steps"></a>Következő lépések
* Ha meg szeretné tekinteni a tárolók mintáit, beleértve a kötet illesztőprogramját, látogasson el a [Service Fabric Container Samples](https://github.com/Azure-Samples/service-fabric-containers)
* Ha tárolókat szeretne üzembe helyezni egy Service Fabric fürtön, tekintse [meg a tároló üzembe helyezése a Service Fabricon](./service-fabric-get-started-containers.md) című cikket.
