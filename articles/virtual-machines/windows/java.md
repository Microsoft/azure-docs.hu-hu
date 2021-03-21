---
title: Azure-beli virtuális gép létrehozása és kezelése Java használatával
description: A Java és a Azure Resource Manager használatával helyezzen üzembe egy virtuális gépet és annak összes támogató erőforrását.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: ea9d5d92d45db50470276929b6e7b4200bb427d6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557522"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Windows rendszerű virtuális gépek létrehozása és kezelése az Azure-ban Java használatával

Egy [Azure-beli virtuális gépnek](overview.md) (VM) több támogató Azure-erőforrásra van szüksége. Ez a cikk a VM-erőforrások létrehozását, kezelését és törlését ismerteti a Java használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Maven-projekt létrehozása
> * Függőségek hozzáadása
> * Hitelesítő adatok létrehozása
> * Az erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezek a lépések körülbelül 20 percet vesznek igénybe.

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Java](/azure/developer/java/fundamentals/java-jdk-long-term-support)-t.
2. Telepítse a [mavent](https://maven.apache.org/download.cgi).
3. Hozzon létre egy új mappát és a projektet:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A `testAzureApp` mappában Nyissa meg a `pom.xml` fájlt, és adja hozzá a Build konfigurációt a &lt; projekthez &gt; , hogy lehetővé váljon az alkalmazás létrehozása:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Adja meg a függőségeket, amelyek szükségesek az Azure Java SDK eléréséhez.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Mentse a fájlt.

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lépés elkezdése előtt győződjön meg arról, hogy van hozzáférése egy [Active Directory egyszerű szolgáltatáshoz](../../active-directory/develop/howto-create-service-principal-portal.md). Az alkalmazás AZONOSÍTÓját, a hitelesítési kulcsot és a bérlő AZONOSÍTÓját is rögzíteni kell egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. Hozzon létre egy nevű fájlt `azureauth.properties` , és adja hozzá ezeket a tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Cserélje le az **&lt; előfizetés &gt; -azonosítót** az előfizetési azonosítóra, az **&lt; application-ID &gt; -** t a Active Directory alkalmazás-azonosítóra, a **&lt; hitelesítési kulcsot &gt;** az alkalmazás kulcsára, és **&lt; &gt; a bérlő azonosítóját** a bérlő azonosítójával.

2. Mentse a fájlt.
3. Állítson be egy AZURE_AUTH_LOCATION nevű környezeti változót a rendszerhéjban a hitelesítési fájl teljes elérési útjával.

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a `App.java` fájlt a `src\main\java\com\fabrikam` (z) területen, és győződjön meg róla, hogy a Package utasítás felül van:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. A Package utasítás alatt adja hozzá a következő importálási utasításokat:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. A kérések elvégzéséhez szükséges Active Directory hitelesítő adatok létrehozásához adja hozzá ezt a kódot az App osztály fő metódusához:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Az erőforrások létrehozása

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Az összes erőforrást egy [erőforráscsoporthoz](../../azure-resource-manager/management/overview.md)kell foglalni.

Az alkalmazás értékének megadásához és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>A rendelkezésre állási csoport létrehozása

A [rendelkezésre állási](tutorial-availability-sets.md) csoportok megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

A rendelkezésre állási csoport létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/public-ip-addresses.md) szükséges.

A virtuális gép nyilvános IP-címének létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A virtuális gépnek egy [virtuális hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatán kell lennie.

Alhálózat és virtuális hálózat létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása

A virtuális gépnek hálózati adapterre van szüksége a virtuális hálózaton való kommunikációhoz.

Hálózati adapter létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Most, hogy létrehozta az összes támogatási erőforrást, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Ez az oktatóanyag egy olyan virtuális gépet hoz létre, amely a Windows Server operációs rendszer egy verzióját futtatja. További információ a többi rendszerkép kiválasztásáról: [Azure-beli virtuálisgép-rendszerképek navigálása és kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md)használatával.
> 
>

Ha a Piactéri rendszerkép helyett meglévő lemezt szeretne használni, használja a következő kódot: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett érdemes lehet kódot létrehozni az ismétlődő vagy összetett feladatok automatizálásához.

Ha bármit kell tennie a virtuális géppel, le kell kérnie a példányát. Adja hozzá ezt a kódot a Main metódus Try blokkjában:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>A virtuális géppel kapcsolatos információk lekérése

Ha információt szeretne kapni a virtuális gépről, adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fizetnie kell, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Ha a virtuális gép fel van foglalva, a hozzá társított összes erőforrás fel van foglalva, és a számlázás véget ér.

Ha le szeretné állítani a virtuális gépet a felszabadítása nélkül, adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Ha felszabadítani szeretné a virtuális gépet, módosítsa a kikapcsoló hívást a következő kódra:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

A virtuális gép elindításához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A központi telepítés számos aspektusát figyelembe kell venni a virtuális gép méretének meghatározásakor. További információ: virtuális gépek [méretei](../sizes.md).  

A virtuális gép méretének módosításához adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

Ha olyan adatlemezt szeretne hozzáadni a virtuális géphez, amely 2 GB méretű, 0 LUN és ReadWrite gyorsítótárazási típust tartalmaz, adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért kell fizetnie, mindig érdemes törölni a már nem szükséges erőforrásokat. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

1. Az erőforráscsoport törléséhez adja hozzá ezt a kódot a Main metódusban található Try blokkhoz:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Mentse az app. Java fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe ahhoz, hogy a konzol alkalmazás teljes körűen fusson az elejétől a végéig.

1. Az alkalmazás futtatásához használja a következő Maven-parancsot:

    ```
    mvn compile exec:java
    ```

2. Mielőtt megnyomja az **ENTER** billentyűt az erőforrások törlésének megkezdéséhez, eltarthat néhány percig, hogy ellenőrizze az erőforrások létrehozását a Azure Portalban. A központi telepítésre vonatkozó információk megtekintéséhez kattintson a központi telepítés állapotára.


## <a name="next-steps"></a>Következő lépések
* További információ a [Javához készült Azure-kódtárak](/java/azure/java-sdk-azure-overview)használatáról.