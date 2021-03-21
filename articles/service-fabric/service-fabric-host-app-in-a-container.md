---
title: .NET-alkalmazás üzembe helyezése tárolóban az Azure Service Fabric
description: Megtudhatja, hogyan helyezhet tárolóba egy meglévő .NET-alkalmazást a Visual Studio segítségével, illetve hogyan végezhet helyi hibakeresést a Service Fabric szolgáltatásbeli tárolókon. A tárolóba helyezett alkalmazást a rendszer Azure-tárolóregisztrációs adatbázisba küldi, és üzembe helyezi egy Service Fabric-fürtben. Az Azure-ban való üzembe helyezéskor az alkalmazás Azure SQL-adatbázist használ adatmegőrzéshez.
ms.topic: tutorial
ms.date: 07/08/2019
ms.openlocfilehash: 7930651a0faa5f37336c15557e2a0f068d613011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98936721"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Oktatóanyag: Windows-tárolóban lévő .NET-alkalmazás telepítése Azure Service Fabricre

Ez az oktatóanyag bemutatja, hogyan helyezhet tárolóba egy meglévő ASP.NET-alkalmazást és csomagot Service Fabric-alkalmazásként.  Futtassa a tárolókat helyileg egy Service Fabric fejlesztési fürtön, és telepítse az alkalmazást az Azure-ba.  Az alkalmazás [Azure SQL-adatbázisban](../azure-sql/database/sql-database-paas-overview.md) őrzi meg az adatokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Meglévő alkalmazás tárolóba helyezése a Visual Studióval
> * Adatbázis létrehozása Azure SQL Database
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Service Fabric-alkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
2. Engedélyezze a Windows **Hyper-V** és a **tárolók** használatát.
3. Telepítse a Windows rendszerhez készült [Docker Desktopot](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) , hogy a tárolók futtathatók legyenek a Windows 10-es verzióban.
4. Telepítse a [Service Fabric futtatókörnyezet 6.2-es](service-fabric-get-started.md) vagy újabb verzióját és a [Service Fabric SDK 3.1-es](service-fabric-get-started.md) vagy újabb verzióját.
5. Telepítse a [Visual Studio 2019 16,1](https://www.visualstudio.com/) -es vagy újabb verzióját az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** számítási feladatokkal.
6. [Azure PowerShell][link-azure-powershell-install] telepítése

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>A Fabrikam Fiber CallCenter letöltése és futtatása

1. Töltse le a [Fabrikam Fiber CallCenter][link-fabrikam-github] mintaalkalmazást.  Kattintson a **download archive** (archívum letöltése) hivatkozásra.  A *fabrikam.zip* fájl *sourceCode* mappájából bontsa ki a *sourceCode.zip* fájlt, majd bontsa ki abból a *VS2015* könyvtárat a számítógépére.

2. Győződjön meg róla, hogy a Fabrikam Fiber CallCenter alkalmazás hiba nélkül buildelhető és futtatható.  Indítsa el a Visual Studiót **rendszergazdaként**, és nyissa meg a [FabrikamFiber.CallCenter.sln][link-fabrikam-github] fájlt.  Nyomja le az F5 billentyűt az alkalmazás debugolásához és futtatásához.

   ![A helyi gazdagépen futó Fabrikam Fiber CallCenter alkalmazás kezdőlapjának képernyőképe. Az oldalon megjelenik egy irányítópult, amelyen a támogatási hívások listája látható.][fabrikam-web-page]

## <a name="create-an-azure-sql-db"></a>Azure SQL-adatbázis létrehozása

A Fabrikam Fiber CallCenter alkalmazás éles futtatásakor az adatokat egy adatbázisban kell tárolni. Jelenleg nincs mód az állandó adatok biztosítására egy tárolóban, ezért nem tárolhat éles adatokat az SQL Serveren egy tárolóban.

Javasoljuk, hogy használjon egy [Azure SQL-adatbázist](../azure-sql/database/powershell-script-content-guide.md). Egy felügyelt SQL Server-adatbázis Azure-ban való létrehozásához és futtatásához hajtsa végre az alábbi szkriptet.  Szükség esetén módosítsa a szkript változóit. A *clientIP* a fejlesztői számítógépe IP-címe. Jegyezze fel a parancsfájl által kiszolgált kiszolgáló nevét.

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzAccount -SubscriptionId $subscriptionID

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Create the database in the server.
New-AzSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```

> [!TIP]
> Ha vállalati tűzfal mögött dolgozik, akkor előfordulhat, hogy a fejlesztői számítógépe IP-címe nem érhető el az internetről. Annak ellenőrzéséhez, hogy az adatbázis a megfelelő IP-címmel rendelkezik a tűzfalszabályhoz, keresse meg az adatbázist az [Azure Portal](https://portal.azure.com) SQL-adatbázisok szakaszában. Kattintson a nevére, majd az Áttekintés szakaszban kattintson a „Kiszolgálótűzfal beállítása” elemre. Az „Ügyfél IP-címe” a fejlesztői gép IP-címe. Győződjön meg arról, hogy megegyezik az „AllowClient” szabályban lévő IP-címmel.

## <a name="update-the-web-config"></a>A webes konfiguráció frissítése

Lépjen vissza a **FabrikamFiber.Web** projektre, frissítse a kapcsolati sztringet a **web.config** fájlban, hogy az a tárolóban lévő SQL Serverre mutasson.  Frissítse a kapcsolati karakterlánc *kiszolgálói* részét az előző szkript által létrehozott kiszolgálónévre. Ehhez hasonlónak kell lennie: "fab-fiber-751718376.database.windows.net". A következő XML-fájlban csak az attribútumot kell frissítenie `connectionString` ; a `providerName` és `name` attribútumokat nem szükséges módosítani.

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```

>[!NOTE]
>A helyi hibakereséshez tetszése szerinti SQL Servert választhat, csak legyen elérhető a gazdagépről. A **localdb** azonban nem támogatja a `container -> host` irányú kommunikációt. Ha egy másik SQL-adatbázist kíván használni a webes alkalmazása kiadásra szánt verziójában, adjon hozzá egy másik kapcsolati sztringet a *web.release.config* fájlhoz.

## <a name="containerize-the-application"></a>Az alkalmazás tárolóba helyezése

1. Kattintson a jobb gombbal a **FabrikamFiber.Web** projektre, és válassza a **Hozzáadás** > **Container Orchestrator Support** (Tárolóvezénylés támogatása) lehetőséget.  Válassza a **Service Fabric** lehetőséget tárolóvezénylőként, és kattintson az **OK** gombra.

2. Ha a rendszer kéri, kattintson az **Igen** gombra a Docker Windows-tárolók közötti váltásához.

   Ekkor létrejön a megoldásban egy új, **FabrikamFiber.CallCenterApplication** nevű Service Fabric-alkalmazásprojekt.  A meglévő **FabrikamFiber.Web** projekthez hozzáad a program egy Dockerfile-t.  A **PackageRoot** könyvtárat is hozzáadja a program a **FabrikamFiber.Web** projekthez, mely az új FabrikamFiber.Web szolgáltatás szolgáltatásjegyzékét és beállításait tartalmazza.

   A tároló készen áll a Service Fabric-alkalmazásban való létrehozásra és csomagolásra. Ha a tárolórendszerkép létrejött a számítógépén, továbbíthatja bármely más tárolójegyzékbe, és lekérheti bármelyik gazdagépre, majd futtathatja.

## <a name="run-the-containerized-application-locally"></a>A tárolóba helyezett alkalmazás helyi futtatása

Nyomja meg az **F5** billentyűt a tárolóban lévő alkalmazás helyi Service Fabric fejlesztői fürtön való debug módú futtatásához. Ha megjelenik egy üzenetablak, amelyben a rendszer arra kéri, hogy adjon a „ServiceFabricAllowedUsers” csoportnak olvasási és végrehajtási engedélyt a Visual Studio projekt könyvtárához, kattintson az **Igen** gombra.

Ha az F5 Futtatás egy kivételt jelez, például a következőt, akkor a megfelelő IP-címet nem adta hozzá az Azure Database-tűzfalhoz.

```text
System.Data.SqlClient.SqlException
HResult=0x80131904
Message=Cannot open server 'fab-fiber-751718376' requested by the login. Client with IP address '123.456.789.012' is not allowed to access the server.  To enable access, use the Windows Azure Management Portal or run sp_set_firewall_rule on the master database to create a firewall rule for this IP address or address range.  It may take up to five minutes for this change to take effect.
Source=.Net SqlClient Data Provider
StackTrace:
<Cannot evaluate the exception stack trace>
```

A megfelelő IP-címet az Azure Database-tűzfalhoz való hozzáadásához futtassa a következő parancsot.

```powershell
# The IP address of your development computer that accesses the SQL DB.
$clientIPNew = "<client IP from the Error Message>"

# Create the firewall rule to allow your development computer to access the server.
New-AzSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClientNew" -StartIpAddress $clientIPNew -EndIpAddress $clientIPNew
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Most, hogy az alkalmazás fut a helyi környezetben, megkezdheti az Azure-ba való telepítést.  A tárolólemezképeket tárolóregisztrációs adatbázisban kell tárolni.  Hozzon létre egy [Azure tárolóregisztrációs adatbázist](../container-registry/container-registry-intro.md) az alábbi szkript segítségével. A tárolóregisztrációs adatbázis neve más Azure-előfizetések számára is látható, ezért egyedinek kell lennie.
Az alkalmazás Azure-ba való telepítése előtt a tárolólemezképet fel kell töltenie ebbe az adatbázisba.  Amikor az alkalmazást telepíti az Azure-ban lévő fürtbe, a tárolólemezképet a rendszer lekéri az adatbázisból.

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>Service Fabric-fürt létrehozása az Azure-ban

A Service Fabric-alkalmazások egy fürtön, azaz virtuális vagy fizikai gépek hálózaton keresztül csatlakozó halmazán futnak.  Mielőtt üzembe helyezné az alkalmazást az Azure-ban, hozzon létre egy Service Fabric-fürtöt az Azure-ban.

A következőket teheti:

* Hozzon létre egy tesztfürtöt a Visual Studióból. Ez lehetővé teszi, hogy közvetlenül a Visual Studióból hozzon létre egy biztonságos fürtöt a választott konfigurációval.
* [Biztonságos fürt létrehozása sablonból](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

Ez az oktatóanyag a fürt Visual Studióból való létrehozását ismerteti, mely ideális megoldás tesztelési forgatókönyvekhez. Ha más módon hoz létre fürtöt, vagy ha már rendelkezik fürttel, akkor átmásolhatja a kapcsolati végpontját, vagy kiválaszthatja azt az előfizetéséből.

Mielőtt elkezdené, nyissa meg a FabrikamFiber. Web-> PackageRoot-> ServiceManifest.xml a Megoldáskezelőban. Jegyezze fel a **végpontban** felsorolt webes kezelőfelület portját.

A fürt létrehozásakor:

1. A Megoldáskezelőben kattintson a jobb gombbal a **FabrikamFiber.CallCenterApplication** alkalmazásprojektre, és válassza a **Közzététel** lehetőséget.
2. Jelentkezzen be az Azure-fiókjával, hogy hozzáférjen az előfizetéséhez vagy előfizetéseihez.
3. A **kapcsolatok végpontjának** legördülő menüben válassza az **új fürt létrehozása...** lehetőséget.
4. A **Fürt létrehozása** párbeszédpanelen módosítsa az alábbi beállításokat:

    a. Adja meg a fürtje nevét a **Fürt neve** mezőben, valamint a használni kívánt előfizetést és helyet. Jegyezze fel a fürterőforrás-csoport nevét.

    b. Választható lehetőségként módosíthatja a csomópontok számát. Alapértelmezés szerint három csomópont van. Ez a Service Fabric-forgatókönyvek teszteléséhez szükséges minimális érték.

    c. Válassza a **tanúsítvány** fület. Ezen a lapon adja meg a fürt tanúsítványának védelméhez használandó jelszót. Ez a tanúsítvány segít a fürt biztonságossá tételében. Annak a helynek az elérési útját is módosíthatja, ahová menteni kívánja a tanúsítványt. A Visual Studióval is importálhatja a tanúsítványt, mert erre a lépésre szükség van az alkalmazás fürtön való közzétételéhez.

    >[!NOTE]
    >Jegyezze meg a mappa elérési útját, ahol a tanúsítvány importálva van. A fürt létrehozását követően a következő lépés a tanúsítvány importálása.

    d. Válassza a **virtuális gép részletei** lapot. Itt adhatja meg a fürtöt alkotó Virtual Machineshoz (VM) használni kívánt jelszót. A felhasználónév és jelszó használatával távolról csatlakozhat a virtuális gépekhez. A virtuális gép méretét is ki kell választania és ha szükséges, módosíthatja a virtuális gép rendszerképét is.

    > [!IMPORTANT]
    > Válasszon olyan SKU-t, amely támogatja a futó tárolókat. A fürtcsomópontjain található Windows Server operációs rendszernek kompatibilisnek kell lennie a tárolóján futó Windows Server operációs rendszerével. További tudnivalókat a [Windows Server tároló operációs rendszerének és a gazdagép operációs rendszerének kompatibilitását ismertető cikket](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). Alapértelmezés szerint ez az oktatóanyag létrehoz egy Docker-rendszerképet, amely a Windows Server 2016 LTSC-re épül. Az erre a képre épülő tárolók a tárolókkal rendelkező Windows Server 2016 Datacenterrel létrehozott fürtökön fognak futni. Ha azonban létrehoz egy fürtöt, vagy egy meglévő fürtöt használ a Windows Server egy másik verziója alapján, akkor módosítania kell azt az operációsrendszer-rendszerképet, amelyre a tároló alapul. Nyissa meg a **Docker** a **FabrikamFiber. Web** projektben, véleményezze a `FROM` Windows Server korábbi verzióján alapuló meglévő utasítást, és adjon hozzá egy `FROM` utasítást a kívánt verzió címkéje alapján a [Windows Server Core DockerHub lapról](https://hub.docker.com/_/microsoft-windows-servercore). További információ a Windows Server Core kiadásokról, a támogatási ütemtervekről és a verziószámozásról: [Windows Server Core kiadási információ](/windows-server/get-started/windows-server-release-info). 

    e. A **Speciális** lapon adja meg, hogy mely alkalmazásport legyen megnyitva a terheléselosztón a fürt üzembe helyezésekor. Ezt a portot vette figyelembe a fürt létrehozása előtt. Hozzáadhat egy meglévő Application Insights-kulcsot is, amelyet az alkalmazás naplófájljainak útválasztásához használhat.

    f. Ha végzett a beállítások módosításával, kattintson a **Létrehozás** gombra.

5. A létrehozás több percig is eltarthat, és a kimeneti ablak jelzi, ha a fürt teljesen elkészült.

## <a name="install-the-imported-certificate"></a>Az importált tanúsítvány telepítése

Telepítse az importált tanúsítványt a fürt létrehozási lépése részeként az **aktuális felhasználói** tároló helyére, és adja meg a megadott titkos kulcs jelszavát.

A telepítés megerősítéséhez nyissa meg a **felhasználói tanúsítványok kezelése** elemet a Vezérlőpulton, és erősítse meg, hogy a tanúsítvány a **tanúsítványok-aktuális felhasználó**  ->  **személyes**  ->  **tanúsítványai** területen van telepítve. A tanúsítványnak a következőhöz hasonlónak kell lennie: *[fürt neve]*. *[Fürt helye]*. cloudapp.Azure.com, például *fabrikamfibercallcenter.southcentralus.cloudapp.Azure.com*. 

## <a name="allow-your-application-running-in-azure-to-access-sql-database"></a>Az Azure-ban futó alkalmazás SQL Database elérésének engedélyezése

Korábban egy SQL-tűzfalszabály létrehozásával engedélyezte a hozzáférést a helyi gépen futtatott alkalmazásnak.  Most az Azure-ban futó alkalmazásnak kell engedélyeznie az SQL-adatbázis elérését.  Hozzon létre egy [virtuális hálózati szolgáltatásvégpontot](../azure-sql/database/vnet-service-endpoint-rule-overview.md) a Service Fabric-fürtnek, majd hozzon létre egy szabályt, mely engedélyezi az adott végpontnak az SQL-adatbázis elérését. Ügyeljen arra, hogy a fürt létrehozásakor figyelembe vett fürterőforrás-csoport változót is megadhatja.

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName

# Get the virtual network by name.
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Most, hogy az alkalmazása kész, üzembe helyezheti az Azure-beli fürtben közvetlenül a Visual Studióból.  A Megoldáskezelőben kattintson a jobb gombbal a **FabrikamFiber.CallCenterApplication** alkalmazásprojektre, és válassza a **Közzététel** lehetőséget.  A **Kapcsolati végpont** területen válassza ki a fürt korábban létrehozott végpontját.  Az **Azure Container Registry** területen válassza ki a korábban létrehozott tárolóregisztrációs adatbázist.  Kattintson a **Közzététel** gombra az alkalmazás Azure-fürtön való közzétételéhez.

![Az alkalmazás közzététele][publish-app]

Az üzembe helyezés folyamatát a kimeneti ablakban követheti nyomon. Ha az alkalmazás üzembe helyezése befejeződött, nyisson meg egy böngészőablakot, és írja be a fürt címét és az alkalmazásportot. Például: `http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/`.

![Képernyőkép a fabrikam Fiber CallCenter alkalmazás kezdőlapjának azure.com-on futó oldaláról. Az oldalon megjelenik egy irányítópult, amelyen a támogatási hívások listája látható.][fabrikam-web-page-deployed]

Ha a lap nem tölthető be, vagy a tanúsítvány nem jelenik meg, próbálja meg megnyitni az Explorer elérési útját, `https://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:19080/Explorer` és válassza ki az újonnan telepített tanúsítványt.

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>Folyamatos integráció és üzembe helyezés (CI/CD) beállítása Service Fabric-fürttel

A CI/CD-alkalmazástelepítés Service Fabric-fürtben végzett, Azure DevOpsszal való konfigurálásáról lásd: [Oktatóanyag: Alkalmazások üzembe helyezése Service Fabric-fürtön CI/CD használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md). Az oktatóanyagban ismertetett folyamat ugyanaz ehhez a (FabrikamFiber) projekthez, csak hagyja ki a szavazási minta letöltését, és a szavazás helyett a FabrikamFiber nevet írja be az adattár neveként.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, ne felejtse el eltávolítani a létrehozott erőforrásokat.  Ennek legegyszerűbb módja az, ha törli azt az erőforráscsoportot, amely tartalmazza a Service Fabric-fürtöt, az Azure SQL-adatbázist és az Azure Container Registry-adatbázist.

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Meglévő alkalmazás tárolóba helyezése a Visual Studióval
> * Adatbázis létrehozása Azure SQL Database
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Service Fabric-alkalmazás üzembe helyezése az Azure-ban

Az oktatóanyag következő részéből megtudhatja, hogyan [helyezhet üzembe tárolóalkalmazást Service Fabric-fürtön CI vagy CD segítségével](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md).

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/install-Az-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png