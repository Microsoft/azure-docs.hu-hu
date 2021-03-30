---
title: HTTPS-végpont hozzáadása a vércse használatával
description: Ez az oktatóanyag azt ismerteti, hogyan adhat hozzá HTTPS-végpontot egy ASP.NET Core előtér-webszolgáltatáshoz a Kestrel használatával, és hogyan helyezheti üzembe az alkalmazást egy fürtön.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: c675f8ece8369bcfc0055343221ac82aea59dec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326235"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Oktatóanyag: HTTPS-végpont hozzáadása ASP.NET Core Web API előtér-szolgáltatáshoz a Kestrel használatával

Ez az oktatóanyag egy sorozat harmadik része.  Az oktatóanyag során el fogja sajátítani a HTTPS engedélyezését a Service Fabricen futó ASP.NET Core-szolgáltatásban. Ha ezzel végzett, rendelkezni fog egy HTTPS-kompatibilis ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely a 443-as porton figyel. Ha nem szeretné manuálisan létrehozni a szavazóalkalmazást a [NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-deploy-app-to-party-cluster.md) szakaszban, akkor [letöltheti a forráskódot](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) a kész alkalmazáshoz.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * HTTPS-végpont meghatározása a szolgáltatásban
> * A Kestrel konfigurálása HTTPS használatára
> * A TLS/SSL-tanúsítvány telepítése a távoli fürt csomópontjain
> * Hálózatiszolgáltatás-hozzáférés hozzáadása a tanúsítvány titkos kulcsához
> * A 443-as port megnyitása az Azure Load Balancerben
> * Az alkalmazás üzembe helyezése egy távoli fürtön

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Az alkalmazás üzembe helyezése egy távoli fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * HTTPS-végpont hozzáadása ASP.NET Core kezelőfelületi szolgáltatáshoz
> * [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Telepítse a Visual Studio 2019](https://www.visualstudio.com/) 16,5-es vagy újabb verzióját az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** számítási feladatokkal.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Tanúsítvány beszerzése vagy egy önaláírt fejlesztési tanúsítvány létrehozása

Éles alkalmazásokhoz használjon [hitelesítésszolgáltatói (CA-)](https://wikipedia.org/wiki/Certificate_authority) tanúsítványt. Fejlesztési és tesztelési célok érdekében létrehozhat és használhat önaláírt tanúsítványt. A Service Fabric SDK biztosítja a *CertSetup.ps1* szkriptet, amely létrehozza az önaláírt tanúsítványt, és importálja azt a `Cert:\LocalMachine\My` tanúsítványtárolóba. Nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot egy, a "CN = mytestcert" tárgyú tanúsítvány létrehozásához:

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Ha már rendelkezik a tanúsítvány PFX kiterjesztésű fájljával, a következő lekérdezés futtatásával importálhatja a tanúsítványt a `Cert:\LocalMachine\My` tanúsítványtárolóba:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>HTTPS-végpont meghatározása a szolgáltatásjegyzékben

Indítsa el a Visual Studiót **rendszergazdaként**, és nyissa meg a Választási megoldást. A Megoldáskezelőben nyissa meg a következőt: *VotingWeb/PackageRoot/ServiceManifest.xml*. A szolgáltatásjegyzék meghatározza a szolgáltatásvégpontokat.  Keresse meg a **Végpontok** szakaszt, és szerkessze a meglévő ServiceEndpoint végpontot.  Módosítsa a nevet VégpontHttps névre, és állítsa be a protokollt *https*, a típust *Bemenet*, a portot pedig *443* értékre.  Mentse a módosításokat.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>A Kestrel konfigurálása HTTPS használatára

A Megoldáskezelőben nyissa meg a következő fájlt: *VotingWeb/VotingWeb.cs*.  A Kestrel konfigurálása HTTPS használatára és tanúsítvány megkeresésére a `Cert:\LocalMachine\My` tárolóban. Adja hozzá a következő using utasításokat:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Frissítse a `ServiceInstanceListener` elemet úgy, hogy az új *VégpontHttps* végpontot használja, és a 443-as porton figyeljen. Ha Kestrel-kiszolgáló használatára konfigurálja a webgazdát, a Kestrelt minden hálózati adapteren az IPv6-címek figyelésére kell konfigurálni: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Adja hozzá a következő metódust, hogy a Kestrel megtalálja a tanúsítványt a `Cert:\LocalMachine\My` tárolóban a tárgy segítségével.  

&lt; &gt; Ha az előző PowerShell-paranccsal létrehozott egy önaláírt tanúsítványt, vagy használja a tanúsítvány CN-t, cserélje le a "your_CN_value" kifejezést "mytestcert" értékre.
Vegye figyelembe, hogy a helyi üzembe helyezés esetén `localhost` érdemes a "CN = localhost" kifejezést használni a hitelesítési kivételek elkerülése érdekében.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>HÁLÓZATI SZOLGÁLTATÁShoz való hozzáférés biztosítása a tanúsítvány titkos kulcsához

Előzőleg importálta a tanúsítványt a fejlesztői számítógép `Cert:\LocalMachine\My` tárolójába.  Most explicit módon adja meg a szolgáltatást futtató fiókot (a hálózati szolgáltatás alapértelmezés szerint), és hozzáférést biztosít a tanúsítvány titkos kulcsához. Ezt a lépést manuálisan is elvégezheti (a certlm. msc eszközzel), de jobb, ha egy PowerShell-parancsfájlt úgy szeretne futtatni, hogy a szolgáltatás jegyzékfájljának **SetupEntryPoint** [konfigurálja az indítási parancsfájlt](service-fabric-run-script-at-service-startup.md) .

>[!NOTE]
> Service Fabric támogatja a végponti tanúsítványok bejelentését az ujjlenyomat vagy a tulajdonos köznapi neve alapján. Ebben az esetben a futtatókörnyezet beállítja a kötést, és a tanúsítvány titkos kulcsát a szolgáltatás által futtatott identitásnak megfelelően ACL-ként fogja beállítani. A futtatókörnyezet a módosításokat és megújításokat is figyeli a tanúsítványon, és ennek megfelelően a megfelelő titkos kulcsot újra ACL-ként ellenőrzi.

### <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása

A Megoldáskezelőben nyissa meg a következőt: *VotingWeb/PackageRoot/ServiceManifest.xml*.  A **CodePackage** szakaszban adja hozzá a **SetupEntryPoint** csomópontot, majd az **ExeHost** csomópontot.  Az **ExeHost** elemben állítsa be a **Program** értékét Setup.bat értékre, a **WorkingFolder** értékét pedig CodePackage értékre.  A VotingWeb szolgáltatás elindulásakor a rendszer futtatja a Setup.bat szkriptet a CodePackage mappában, még a VotingWeb.exe elindulása előtt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>A köteg és a PowerShell beállítási szkriptjeinek hozzáadása

A PowerShell a **SetupEntryPoint** pontból való futtatásához futtathatja a PowerShell.exe fájlt egy kötegfájlban, amely egy PowerShell-fájlra mutat. Először adja hozzá a kötegfájlt a szolgáltatásprojekthez.  A Megoldáskezelőben kattintson a jobb gombbal a **VotingWeb** pontra, és válassza az **Add**->**New Item** (Hozzáadás, Új elem) pontot, majd adjon hozzá egy új fájlt Setup.bat névvel.  Szerkessze a *Setup.bat* fájlt, és adja hozzá az alábbi parancsot:

```cmd
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Módosítsa a *Setup.bat* fájltulajdonságokat a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elem Copy if newer (Másolás, ha újabb) értékre állításához.

![Fájltulajdonságok beállítása][image1]

A Megoldáskezelőben kattintson a jobb gombbal a **VotingWeb** pontra, és válassza az **Add**->**New Item** (Hozzáadás, Új elem) pontot, majd adjon hozzá egy új fájlt SetCertAccess.ps1 névvel.  Szerkessze a *SetCertAccess.ps1* fájlt, és adja hozzá az alábbi szkriptet:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Módosítsa a *SetCertAccess.ps1* fájltulajdonságokat a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) elem Copy if newer (Másolás, ha újabb) értékre állításához.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Beállítási szkript futtatása helyi rendszergazdaként

Alapértelmezés szerint a szolgáltatásbeállítás belépési pontjának végrehajtható fájlja ugyanolyan hitelesítő adatok alatt fut, mint a Service Fabric (általában a Hálózati szolgáltatási fiók). A *SetCertAccess.ps1* rendszergazdai jogosultságokat igényel. Az alkalmazásjegyzékben módosíthatja a biztonsági engedélyeket az indítási szkript futtatásához, egy helyi rendszergazdai fiókkal.

A Solution Explorerben (Megoldáskezelő) nyissa meg a *Voting/ApplicationPackageRoot/ApplicationManifest.xml* fájlt. Először hozzon létre egy **Rendszerbiztonsági tagok** szakaszt, és adjon hozzá új felhasználót (például SetupAdminUser). Adja hozzá a SetupAdminUser felhasználói fiókot a rendszergazdák rendszercsoportjához.
Ezután a VotingWebPkg **ServiceManifestImport** szakaszban konfigurálja a **RunAsPolicy** elemet, hogy alkalmazza a SetupAdminUser rendszerbiztonsági tagot a beállítási belépési pontra. Ez a szabályzat tájékoztatja a Service Fabricet, hogy a Setup.bat fájl SetupAdminUser-ként fut (rendszergazdai jogosultságokkal).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A Megoldáskezelő válassza ki a **szavazati** alkalmazást, és állítsa be az **alkalmazás URL-címe** tulajdonságot a "https: \/ /localhost: 443" értékre.

Mentse a fájlokat és nyomja le az F5 billentyűt az alkalmazás helyi futtatásához.  Az alkalmazás üzembe helyezését követően megnyílik egy webböngésző a https: \/ /localhost: 443. Ha önaláírt tanúsítványt használ, látni fog egy figyelmeztetést, amely szerint a számítógépe nem bízik az adott webhely biztonságában.  Tovább a weblapra.

![Képernyőkép az URL-címet tartalmazó böngészőablakban futó Service Fabric szavazási minta alkalmazásról https://localhost/ .][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Tanúsítvány telepítése fürtcsomópontokon

Mielőtt telepítené az alkalmazást az Azure-ban, telepítse a tanúsítványt a `Cert:\LocalMachine\My` távoli fürtcsomópontok tárolójába.  A szolgáltatások a fürt különböző csomópontjaira léphetnek át.  Ha az előtér-webszolgáltatás elindul a fürtcsomóponton, az indítási szkript megkeresi a tanúsítványt, és konfigurálja a hozzáférési engedélyeket.

Először exportálja a tanúsítványt egy PFX-fájlba. Nyissa meg a certlm. msc alkalmazást, és navigáljon a **személyes** > **tanúsítványok** elemre.  Kattintson a jobb gombbal a *mytestcert* tanúsítványra, és válassza a **minden feladat** > **Exportálás** lehetőséget.

![Tanúsítvány exportálása][image4]

Az exportálási varázslóban válassza az **Igen, a titkos kulcs exportálását választom** lehetőséget, majd a Személyes információcsere (PFX) formátumot.  Exportálja a fájlt a *C:\Users\sfuser\votingappcert.pfx* helyre.

Ezután telepítse a tanúsítványt a távoli fürtön a [megadott PowerShell-parancsfájlok](./scripts/service-fabric-powershell-add-application-certificate.md)használatával.

> [!Warning]
> Az önaláírt tanúsítvány elegendő alkalmazások fejlesztéséhez és teszteléséhez. Éles alkalmazásokhoz használja a [hitelesítésszolgáltatói (CA-)](https://wikipedia.org/wiki/Certificate_authority) tanúsítványt az önaláírt tanúsítvány helyett.

## <a name="open-port-443-in-the-azure-load-balancer-and-virtual-network"></a>Nyissa meg az 443-es portot az Azure Load balancerben és a virtuális hálózaton

Nyissa meg a 443-as portot a Load Balancerben, ha még nem tette meg.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

Tegye ugyanezt a társított virtuális hálózattal.

```powershell
$rulename="allowAppPort$port"
$nsgname="voting-vnet-security"
$RGname="voting_RG"
$port=443

# Get the NSG resource
$nsg = Get-AzNetworkSecurityGroup -Name $nsgname -ResourceGroupName $RGname

# Add the inbound security rule.
$nsg | Add-AzNetworkSecurityRuleConfig -Name $rulename -Description "Allow app port" -Access Allow `
    -Protocol * -Direction Inbound -Priority 3891 -SourceAddressPrefix "*" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange $port

# Update the NSG.
$nsg | Set-AzNetworkSecurityGroup
```

## <a name="deploy-the-application-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Mentsen minden fájlt, váltson Hibakeresésről Kiadásra, majd nyomja le az F6 billentyűt az újraépítéshez.  A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget. Válassza ki az [Alkalmazás üzembe helyezése egy fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md) területen létrehozott fürt kapcsolati végpontját, vagy válasszon ki egy másik fürtöt.  Kattintson a **Közzététel** gombra az alkalmazás közzétételéhez a távoli fürtön.

Az alkalmazás üzembe helyezése után nyisson meg egy webböngészőt, majd keresse meg a következő elemet: `https://mycluster.region.cloudapp.azure.com:443` (frissítse az URL-címet a fürtjéhez tartozó kapcsolati végponttal). Ha önaláírt tanúsítványt használ, látni fog egy figyelmeztetést, amely szerint a számítógépe nem bízik az adott webhely biztonságában.  Tovább a weblapra.

![Képernyőkép az URL-címet tartalmazó böngészőablakban futó Service Fabric szavazási minta alkalmazásról https://mycluster.region.cloudapp.azure.com:443 .][image3]

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * HTTPS-végpont meghatározása a szolgáltatásban
> * A Kestrel konfigurálása HTTPS használatára
> * A TLS/SSL-tanúsítvány telepítése a távoli fürt csomópontjain
> * Hálózatiszolgáltatás-hozzáférés hozzáadása a tanúsítvány titkos kulcsához
> * A 443-as port megnyitása az Azure Load Balancerben
> * Az alkalmazás üzembe helyezése egy távoli fürtön

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
