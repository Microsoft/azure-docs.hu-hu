---
title: Biztonságos kapcsolódás Azure Service Fabric-fürthöz
description: Útmutatás a Service Fabric-fürthöz való ügyfél-hozzáférés hitelesítéséhez, valamint az ügyfelek és a fürt közötti kommunikáció biztonságossá tételéhez.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791121"
---
# <a name="connect-to-a-secure-cluster"></a>Csatlakozás biztonságos fürthöz

Amikor az ügyfél egy Service Fabric fürthöz csatlakozik, az ügyfél hitelesíthető és biztonságossá tehető a tanúsítványalapú biztonság vagy a Azure Active Directory (HRE) használatával létesített kommunikációban. Ez a hitelesítés biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a központilag telepített alkalmazásokhoz, valamint felügyeleti feladatokat hajtsanak végre.  A fürt létrehozásakor a tanúsítvány-vagy HRE biztonsági szolgáltatást előzőleg engedélyezni kell a fürtön.  A fürtök biztonsági forgatókönyvekkel kapcsolatos további információkért lásd: a [fürt biztonsága](service-fabric-cluster-security.md). Ha tanúsítványokkal védett fürthöz csatlakozik, [állítsa be az ügyféltanúsítványt](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) azon a számítógépen, amely a fürthöz csatlakozik. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Kapcsolódás biztonságos fürthöz az Azure Service Fabric parancssori felület (sfctl) használatával

A Service Fabric CLI (sfctl) használatával többféleképpen csatlakozhat egy biztonságos fürthöz. Ha a hitelesítés ügyféltanúsítvány használatával történik, a tanúsítvány adatainak meg kell egyeznie egy, a fürtcsomópontokon telepített tanúsítvány adataival. Ha a tanúsítvány rendelkezik hitelesítésszolgáltatók (CAs) használatával, adja meg a megbízható hitelesítésszolgáltatókat is.

A parancs használatával kapcsolódhat egy fürthöz `sfctl cluster select` .

Az ügyféltanúsítványok két különböző módon adhatók meg tanúsítványként és kulcspárként, vagy egyetlen PFX-fájlként. A jelszóval védett PEM-fájlok esetében a rendszer automatikusan kéri a jelszó megadását. Ha az ügyféltanúsítványt PFX-fájlként szerezte be, először alakítsa át a PFX-fájlt egy PEM-fájlba a következő parancs használatával. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Ha a. pfx fájl jelszava nem védett, használja a-passin pass: (utolsó paraméter) értékét.

Az ügyféltanúsítvány PEM-fájlként való megadásához az argumentumban meg kell adni a fájl elérési útját `--pem` . Például:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

A jelszóval védett PEM-fájlok a parancsok futtatása előtt kérik a jelszót.

A tanúsítvány megadásához a kulcspár a `--cert` és az `--key` argumentumokat használja az egyes fájlok elérési útjának megadásához.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Esetenként a teszt-vagy fejlesztői fürtök biztonságos tanúsítvány-ellenőrzése nem sikerült. A tanúsítvány ellenőrzésének megkerüléséhez válassza a `--no-verify` lehetőséget. Például:

> [!WARNING]
> Ne használja a `no-verify` kapcsolót éles Service Fabric fürtökhöz való csatlakozáskor.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Emellett megadhatja a megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok vagy az egyes tanúsítványok könyvtárainak elérési útját is. Az elérési utak megadásához használja az `--ca` argumentumot. Például:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

A kapcsolódás után [más sfctl-parancsokat is futtathat](service-fabric-cli.md) a fürttel való kommunikációhoz.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Kapcsolódás fürthöz a PowerShell használatával
Mielőtt műveleteket hajt végre a fürtön a PowerShell használatával, először létesítsen kapcsolatot a fürttel. A fürt kapcsolata az adott PowerShell-munkamenetben lévő összes további parancshoz használatos.

### <a name="connect-to-an-unsecure-cluster"></a>Kapcsolódás nem biztonságos fürthöz

A nem biztonságos fürthöz való kapcsolódáshoz adja meg a fürt végpontjának címe beállítást a **ServiceFabricCluster** parancshoz:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Kapcsolódás biztonságos fürthöz Azure Active Directory használatával

Ha Azure Active Directoryt használó biztonságos fürthöz szeretne csatlakozni a fürt rendszergazdai hozzáférésének engedélyezéséhez, adja meg a fürt tanúsítványának ujjlenyomatát, és használja a *AzureActiveDirectory* jelzőt.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Kapcsolódás biztonságos fürthöz ügyféltanúsítvány használatával
Futtassa a következő PowerShell-parancsot egy olyan biztonságos fürthöz való kapcsolódáshoz, amely ügyféltanúsítványt használ a rendszergazdai hozzáférés engedélyezéséhez. 

#### <a name="connect-using-certificate-common-name"></a>Összekapcsolás a tanúsítvány köznapi nevével
Adja meg a fürt tanúsítványának köznapi nevét és a fürt felügyeletére vonatkozó engedélyeket kapott ügyféltanúsítvány köznapi nevét. A tanúsítvány részleteinek meg kell egyezniük a fürtcsomópontok tanúsítványával.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
A *ServerCommonName* a fürtcsomópontokon telepített kiszolgálótanúsítvány köznapi neve. A *FindValue* a felügyeleti ügyféltanúsítvány köznapi neve. A paraméterek kitöltése után a parancs a következő példához hasonlóan néz ki:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Kapcsolat a tanúsítvány ujjlenyomatával
Adja meg a fürt tanúsítványának ujjlenyomatát és az ügyféltanúsítvány ujjlenyomatát, amely engedélyt kapott a fürtözési felügyeletre. A tanúsítvány részleteinek meg kell egyezniük a fürtcsomópontok tanúsítványával.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

A *servercertthumbprint értékeknek egyezniük* a fürtcsomópontokon telepített kiszolgálótanúsítvány ujjlenyomata. A *FindValue* a felügyeleti ügyféltanúsítvány ujjlenyomata.  A paraméterek kitöltése után a parancs a következő példához hasonlóan néz ki:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Kapcsolódás biztonságos fürthöz Windows Active Directory használatával
Ha az önálló fürt üzembe helyezése az AD Security használatával történik, kapcsolódjon a fürthöz a "WindowsCredential" kapcsoló hozzáfűzésével.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Kapcsolódás fürthöz a FabricClient API-k használatával
A Service Fabric SDK biztosítja a [FabricClient](/dotnet/api/system.fabric.fabricclient) osztályt a fürt felügyeletéhez. A FabricClient API-k használatához szerezze be a Microsoft. ServiceFabric NuGet-csomagot.

### <a name="connect-to-an-unsecure-cluster"></a>Kapcsolódás nem biztonságos fürthöz

Távoli nem biztonságos fürthöz való kapcsolódáshoz hozzon létre egy FabricClient-példányt, és adja meg a fürt címe:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

A fürtön belül futó kód esetében például egy megbízható szolgáltatásban hozzon létre egy FabricClient a fürt címe megadása *nélkül* . A FabricClient csatlakozik a helyi felügyeleti átjáróhoz azon a csomóponton, amelyen a kód jelenleg fut, elkerülve a további hálózati ugrásokat.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Kapcsolódás biztonságos fürthöz ügyféltanúsítvány használatával

A fürt csomópontjainak érvényes tanúsítványokkal kell rendelkezniük, amelyek köznapi neve vagy DNS-neve a SAN-ban jelenik meg a [RemoteCommonNames tulajdonságban](/dotnet/api/system.fabric.x509credentials) a [FabricClient](/dotnet/api/system.fabric.fabricclient). Ennek a folyamatnak a követése lehetővé teszi a kölcsönös hitelesítést az ügyfél és a fürtcsomópontok között.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Kapcsolódás biztonságos fürthöz interaktív módon a Azure Active Directory használatával

Az alábbi példa a kiszolgáló identitásához Azure Active Directory használ az ügyfél-identitáshoz és a kiszolgálói tanúsítványhoz.

A fürthöz való csatlakozáskor a párbeszédpanel automatikusan megjelenik az interaktív bejelentkezéshez.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Kapcsolódás biztonságos fürthöz nem interaktív módon a Azure Active Directory használatával

A következő példa a Microsoft. IdentityModel. clients. ActiveDirectory, Version: 2.19.208020213 függvényre támaszkodik.

További információ a HRE-jogkivonat beszerzéséről: [Microsoft. IdentityModel. clients. ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Kapcsolódás biztonságos fürthöz a korábbi metaadatok ismerete nélkül a Azure Active Directory használatával

Az alábbi példa a nem interaktív jogkivonat-beszerzést használja, de ugyanezt a megközelítést használhatja egy egyéni interaktív jogkivonat-beszerzési élmény létrehozásához. A jogkivonat-beszerzéshez szükséges Azure Active Directory metaadatok beolvasása a fürt konfigurációjától történik.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Kapcsolódás biztonságos fürthöz Service Fabric Explorer használatával
Ha [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) szeretne elérni egy adott fürthöz, a böngészőt a következőre irányíthatja:

`http://<your-cluster-endpoint>:19080/Explorer`

A teljes URL-cím a Azure Portal fürt Essentials paneljén is elérhető.

Ha Windows vagy OS X rendszerű biztonságos fürthöz szeretne csatlakozni böngésző használatával, importálhatja az ügyféltanúsítványt, és a böngésző kérni fogja a fürthöz való csatlakozáshoz használandó tanúsítvány megadását.  A Linux rendszerű gépeken a tanúsítványt speciális böngésző-beállításokkal kell importálni (minden böngésző különböző mechanizmusokkal rendelkezik), és a tanúsítvány helyére irányítja a lemezt. További információért olvassa el az [ügyféltanúsítvány beállítása](#connectsecureclustersetupclientcert) című témakört.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Kapcsolódás biztonságos fürthöz Azure Active Directory használatával

Ha a HRE védett fürthöz szeretne csatlakozni, mutasson a böngészőre a következőre:

`https://<your-cluster-endpoint>:19080/Explorer`

A rendszer automatikusan kéri a HRE való bejelentkezést.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Kapcsolódás biztonságos fürthöz ügyféltanúsítvány használatával

Ha tanúsítványokkal védett fürthöz szeretne csatlakozni, mutasson a böngészőre a következőre:

`https://<your-cluster-endpoint>:19080/Explorer`

A rendszer automatikusan felszólítja az ügyféltanúsítvány kiválasztására.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Ügyféltanúsítvány beállítása a távoli számítógépen

Legalább két tanúsítványt kell használni a fürt biztonságossá tételéhez, egyet a fürthöz és a kiszolgálói tanúsítványhoz, és egy másikat az ügyfél-hozzáféréshez.  Javasoljuk, hogy használjon további másodlagos tanúsítványokat és ügyfél-hozzáférési tanúsítványokat is.  Az ügyfél és a fürtcsomópont közötti kommunikáció biztonságossá tételéhez először be kell szereznie és telepítenie kell az ügyféltanúsítványt. A tanúsítvány telepíthető a helyi számítógép vagy az aktuális felhasználó személyes (saját) tárolójába.  Szükség van a kiszolgálói tanúsítvány ujjlenyomatára is, hogy az ügyfél hitelesíteni tudja a fürtöt.

* Windows rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a `Certificates - Current User\Personal\Certificates` személyes tárolóban való telepítéséhez. Másik lehetőségként használhatja a PowerShell-parancsot is:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Ha az önaláírt tanúsítvány, akkor a tanúsítvány használatával a biztonságos fürthöz való kapcsolódáshoz importálnia kell a számítógép "megbízható személyek" tárolójába.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Mac rendszeren: Kattintson duplán a PFX-fájlra, és kövesse a lépéseket a tanúsítvány a kulcskarikában való telepítéséhez.

## <a name="next-steps"></a>Következő lépések

* [Service Fabric fürt frissítési folyamata és elvárásai](service-fabric-cluster-upgrade.md)
* [A Service Fabric-alkalmazások kezelése a Visual Studióban](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric Health Model bemutatása](service-fabric-health-introduction.md)
* [Alkalmazás biztonsága és a RunAs](service-fabric-application-runas-security.md)
* [A Service Fabric parancssori felület használatának első lépései](service-fabric-cli.md)
