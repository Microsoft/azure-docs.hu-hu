---
title: Fürt biztonságossá tétele Windows rendszeren tanúsítványok használatával
description: Biztonságos kommunikáció egy Azure Service Fabric önálló vagy helyszíni fürtön, valamint az ügyfelek és a fürt között.
ms.topic: conceptual
ms.date: 10/15/2017
ms.openlocfilehash: d75c644be47ea44f6a8a6ccac91b785af0132833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791037"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Önálló fürt biztonságossá tétele Windows rendszeren X. 509 tanúsítványok használatával
Ez a cikk az önálló Windows-fürt különböző csomópontjai közötti kommunikáció biztonságossá tételét ismerteti. Azt is leírja, hogyan lehet hitelesíteni azokat az ügyfeleket, amelyek X. 509 tanúsítványokkal csatlakoznak ehhez a fürthöz. A hitelesítés biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és az üzembe helyezett alkalmazásokhoz, valamint a felügyeleti feladatok elvégzéséhez. A fürt létrehozásakor engedélyezni kell a tanúsítvány biztonságát a fürtön.  

A fürt biztonságával, például a csomópontok közötti biztonsággal, az ügyfél és a csomópont biztonságával, valamint a szerepköralapú hozzáférés-vezérléssel kapcsolatos további információkért lásd a [fürtök biztonsági forgatókönyveit](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Mely tanúsítványokra van szüksége?
Először [töltse le a Windows Server-csomag Service Fabric](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) a fürt egyik csomópontjára. A letöltött csomagban található egy ClusterConfig.X509.MultiMachine.jsa fájlon. Nyissa meg a fájlt, és tekintse át a biztonság szakaszban található biztonsági szakaszt a tulajdonságok szakaszban:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Ez a szakasz azokat a tanúsítványokat ismerteti, amelyek szükségesek a különálló Windows-fürt biztonságossá tételéhez. Ha a fürt tanúsítványát adja meg, állítsa a ClusterCredentialType értéket a _X509_ értékre. Ha egy kiszolgálói tanúsítványt ad meg a külső kapcsolatokhoz, állítsa a ServerCredentialType _X509_ értékre. Bár nem kötelező megadni, javasoljuk, hogy mindkét tanúsítványt egy megfelelően biztonságos fürthöz. Ha ezeket az értékeket *X509* értékre állítja, akkor a megfelelő tanúsítványokat is meg kell adnia, vagy a Service Fabric kivételt jelez. Bizonyos esetekben előfordulhat, hogy csak a _ClientCertificateThumbprints_ vagy a _ReverseProxyCertificate_ szeretné megadni. Ezekben a forgatókönyvekben nem kell beállítania a _ClusterCredentialType_ vagy a _ServerCredentialType_ a _X509_.


> [!NOTE]
> Az [ujjlenyomat](https://en.wikipedia.org/wiki/Public_key_fingerprint) a tanúsítvány elsődleges identitása. A létrehozott tanúsítványok ujjlenyomatának megállapításához tekintse meg a [tanúsítvány ujjlenyomatának beolvasása](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)című témakört.
> 
> 

A következő táblázat felsorolja a fürt beállításához szükséges tanúsítványokat:

| **CertificateInformation-beállítás** | **Leírás** |
| --- | --- |
| ClusterCertificate |Tesztelési környezethez ajánlott. Ez a tanúsítvány szükséges a fürt csomópontjai közötti kommunikáció biztonságossá tételéhez. A frissítéshez két különböző tanúsítványt, egy elsődleges és egy másodlagost használhat. Állítsa be az elsődleges tanúsítvány ujjlenyomatát az ujjlenyomat szakaszban, a másodlagos értéket pedig a ThumbprintSecondary változóban. |
| ClusterCertificateCommonNames |Éles környezetben ajánlott. Ez a tanúsítvány szükséges a fürt csomópontjai közötti kommunikáció biztonságossá tételéhez. Használhat egy vagy két egyszerű fürtöt is. A CertificateIssuerThumbprint megfelel a tanúsítvány kiállítójának ujjlenyomatának. Ha egynél több, azonos névvel rendelkező tanúsítvány van használatban, több kiállítói ujjlenyomatai megfelelnek is megadhat.|
| ClusterCertificateIssuerStores |Éles környezetben ajánlott. Ez a tanúsítvány a fürt tanúsítványának kibocsátójának felel meg. A kiállító köznapi nevét és a hozzá tartozó tároló nevét ebben a szakaszban adhatja meg a kiállítói ujjlenyomat megadása helyett a ClusterCertificateCommonNames területen.  Ez megkönnyíti a fürt kiállítói tanúsítványainak átváltását. Több kiállító is megadható, ha egynél több fürtcsomópont van használatban. Az üres IssuerCommonName lehetővé teszi az összes tanúsítvány használatát a X509StoreNames alatt megadott megfelelő tárolókban.|
| ServerCertificate |Tesztelési környezethez ajánlott. Ez a tanúsítvány az ügyfél számára jelenik meg, amikor megpróbál csatlakozni a fürthöz. Az egyszerűség kedvéért dönthet úgy, hogy ugyanazt a tanúsítványt használja a ClusterCertificate és a ServerCertificate. A frissítéshez két különböző kiszolgálói tanúsítvány, egy elsődleges és egy másodlagos kiszolgáló is használható. Állítsa be az elsődleges tanúsítvány ujjlenyomatát az ujjlenyomat szakaszban, a másodlagos értéket pedig a ThumbprintSecondary változóban. |
| ServerCertificateCommonNames |Éles környezetben ajánlott. Ez a tanúsítvány az ügyfél számára jelenik meg, amikor megpróbál csatlakozni a fürthöz. A CertificateIssuerThumbprint megfelel a tanúsítvány kiállítójának ujjlenyomatának. Ha egynél több, azonos névvel rendelkező tanúsítvány van használatban, több kiállítói ujjlenyomatai megfelelnek is megadhat. Az egyszerűség kedvéért dönthet úgy, hogy ugyanazt a tanúsítványt használja a ClusterCertificateCommonNames és a ServerCertificateCommonNames. Használhat egy vagy két kiszolgálói tanúsítvány köznapi nevét. |
| ServerCertificateIssuerStores |Éles környezetben ajánlott. Ez a tanúsítvány a kiszolgálói tanúsítvány kiállítójának felel meg. A kiállító köznapi nevét és a hozzá tartozó tároló nevét ebben a szakaszban adhatja meg a kiállítói ujjlenyomat megadása helyett a ServerCertificateCommonNames területen.  Ez megkönnyíti a kiszolgáló-kiállítói tanúsítványok átváltását. Több kiállító is megadható, ha egynél több kiszolgálótanúsítvány van használatban. Az üres IssuerCommonName lehetővé teszi az összes tanúsítvány használatát a X509StoreNames alatt megadott megfelelő tárolókban.|
| ClientCertificateThumbprints |Telepítse ezeket a tanúsítványokat a hitelesített ügyfelekre. Számos különböző ügyféltanúsítványt telepíthet azon gépekre, amelyeknek engedélyezni szeretné a fürthöz való hozzáférést. Állítsa be az egyes tanúsítványok ujjlenyomatát a CertificateThumbprint változóban. Ha a IsAdmin értéke *true (igaz*) értékre van állítva, akkor az adott tanúsítvánnyal rendelkező ügyfél a fürtön rendszergazdai felügyeleti tevékenységeket hajthat végre. Ha a IsAdmin *hamis*, akkor az ezzel a tanúsítvánnyal rendelkező ügyfél csak a csak olvasási jogosultsággal rendelkező felhasználók számára engedélyezett műveleteket hajthat végre. További információ a szerepkörökről: [Service Fabric szerepköralapú hozzáférés-vezérlés](service-fabric-cluster-security.md#service-fabric-role-based-access-control). |
| ClientCertificateCommonNames |Állítsa be az első ügyféltanúsítvány köznapi nevét a CertificateCommonName. A CertificateIssuerThumbprint a tanúsítvány kiállítójának ujjlenyomata. A köznapi nevekről és a kibocsátóról további információt a [tanúsítványok használata](/dotnet/framework/wcf/feature-details/working-with-certificates)című témakörben talál. |
| ClientCertificateIssuerStores |Éles környezetben ajánlott. Ez a tanúsítvány az ügyféltanúsítvány kiállítójának felel meg (a rendszergazda és a nem rendszergazdai szerepkörök is). A kiállító köznapi nevét és a hozzá tartozó tároló nevét ebben a szakaszban adhatja meg a kiállítói ujjlenyomat megadása helyett a ClientCertificateCommonNames területen.  Ez megkönnyíti az ügyfél-kiállítói tanúsítványok átváltását. Több kiállító is megadható, ha egynél több ügyféltanúsítvány van használatban. Az üres IssuerCommonName lehetővé teszi az összes tanúsítvány használatát a X509StoreNames alatt megadott megfelelő tárolókban.|
| ReverseProxyCertificate |Tesztelési környezethez ajánlott. Ez a választható tanúsítvány megadható, ha meg szeretné védeni a [fordított proxyt](service-fabric-reverseproxy.md). Ha ezt a tanúsítványt használja, győződjön meg róla, hogy a reverseProxyEndpointPort be van állítva a nodeTypes. |
| ReverseProxyCertificateCommonNames |Éles környezetben ajánlott. Ez a választható tanúsítvány megadható, ha meg szeretné védeni a [fordított proxyt](service-fabric-reverseproxy.md). Ha ezt a tanúsítványt használja, győződjön meg róla, hogy a reverseProxyEndpointPort be van állítva a nodeTypes. |

Íme egy példa a fürt, a kiszolgáló és az Ügyféltanúsítványok megadására szolgáló fürtkonfiguráció. A fürt/kiszolgáló/reverseProxy tanúsítványok esetében az ujjlenyomatot és a köznapi nevet nem lehet egyszerre konfigurálni ugyanahhoz a tanúsítvány típushoz.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Tanúsítvány-rollover
Ha az ujjlenyomat helyett köznapi nevet használ, a tanúsítvány-átváltáshoz nem szükséges a fürt konfigurációjának frissítése. A kiállítói ujjlenyomatok frissítései esetében győződjön meg arról, hogy az új ujjlenyomat-lista a régi listával együtt metszi a listát. Először el kell végeznie a konfiguráció frissítését az új kiállítói ujjlenyomatai megfelelnek, majd telepítenie kell az új tanúsítványokat (a fürt/kiszolgáló tanúsítvány és a kiállítói tanúsítványokat is) az áruházban. Az új kiállítói tanúsítvány telepítése után a tanúsítványtárolóban tartsa meg a régi kiállítói tanúsítványt legalább két órával.
Ha kiállítói tárolókat használ, a kiállítói tanúsítvány átváltásához nem kell elvégezni a konfiguráció frissítését. Telepítse az új kiállítói tanúsítványt egy újabb lejárati dátummal a megfelelő tanúsítványtárolóban, és néhány óra elteltével távolítsa el a régi kiállítói tanúsítványt.

## <a name="acquire-the-x509-certificates"></a>Az X. 509 tanúsítványok beolvasása
A fürtön belüli kommunikáció biztonságossá tételéhez először be kell szereznie az X. 509 tanúsítványokat a fürtcsomópontok számára. Emellett a fürthöz tartozó, engedélyezett gépekhez/felhasználókhoz való kapcsolódás korlátozásához be kell szereznie és telepítenie kell az ügyfélgépek tanúsítványait.

Éles számítási feladatokat futtató fürtök esetén a fürt biztonságossá tételéhez használjon [hitelesítésszolgáltató (CA)](https://en.wikipedia.org/wiki/Certificate_authority)által aláírt X. 509 tanúsítványt. A tanúsítványok beszerzésével kapcsolatos további információkért lásd: a [Tanúsítvány beszerzése](/dotnet/framework/wcf/feature-details/how-to-obtain-a-certificate-wcf). 

A tanúsítvány megfelelő működéséhez számos tulajdonság szükséges:

* A tanúsítvány szolgáltatójának a **Microsoft Enhanced RSA és AES titkosítási szolgáltatónak** kell lennie

* RSA-kulcs létrehozásakor győződjön meg arról, hogy a kulcs **2048 bit**.

* A kulcshasználati bővítmény értéke **digitális aláírás, kulcs titkosítási (a0)**

* A kibővített kulcshasználat bővítmény a **kiszolgálói hitelesítés** (OID: 1.3.6.1.5.5.7.3.1) és az **ügyfél-hitelesítés** (OID: 1.3.6.1.5.5.7.3.2) értékeit használja.

A tesztelési célra használt fürtök esetében dönthet úgy, hogy önaláírt tanúsítványt használ.

További kérdésekért forduljon a [Gyakori kérdések a tanúsítványokhoz](./cluster-security-certificate-management.md#troubleshooting-and-frequently-asked-questions).

## <a name="optional-create-a-self-signed-certificate"></a>Nem kötelező: önaláírt tanúsítvány létrehozása
Egy olyan önaláírt tanúsítvány létrehozásának egyik módja, amely megfelelően biztonságossá tehető, ha a C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. könyvtárban található Service Fabric SDK mappában a CertSetup.ps1 parancsfájlt használja. A fájl szerkesztésével módosíthatja a tanúsítvány alapértelmezett nevét. (A CN = ServiceFabricDevClusterCert érték megkeresése.) Futtassa ezt a parancsfájlt a következőként: `.\CertSetup.ps1 -Install` .

Ezután exportálja a tanúsítványt egy védett jelszóval rendelkező. pfx fájlba. Először kérje le a tanúsítvány ujjlenyomatát. 
1. A **Start** menüben futtassa a **számítógép-tanúsítványok kezelése** elemet. 

2. Lépjen a **helyi számítógép személyes tanúsítványtárolójában** mappára, és keresse meg a létrehozott tanúsítványt. 

3. Kattintson duplán a tanúsítványra a megnyitásához, válassza a **részletek** lapot, és görgessen le az **ujjlenyomat** mezőhöz. 

4. Távolítsa el a szóközöket, és másolja az ujjlenyomat értékét a következő PowerShell-parancsba. 

5. Módosítsa az `String` értéket egy megfelelő biztonságos jelszóra a védelemhez, és futtassa a következőt a PowerShellben:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. A számítógépen telepített tanúsítvány részleteinek megtekintéséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Ha Azure-előfizetéssel rendelkezik, kövesse a [Service Fabric-fürt létrehozása Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md)című témakör lépéseit.

## <a name="install-the-certificates"></a>A tanúsítványok telepítése
A tanúsítványok használata után telepítheti őket a fürtcsomópontokon. A csomópontoknak telepíteniük kell a legújabb Windows PowerShell 3. x verzióját. Ismételje meg ezeket a lépéseket a fürt és a kiszolgálói tanúsítványok, valamint a másodlagos tanúsítványok mindegyik csomópontján.

1. Másolja a. pfx-fájlt vagy-fájlokat a csomópontra.

2. Nyisson meg egy PowerShell-ablakot rendszergazdaként, és írja be a következő parancsokat. Cserélje le a *$pswdt* a tanúsítvány létrehozásához használt jelszóra. Cserélje le a *$PfxFilePatht* a csomópontra másolt. pfx fájl teljes elérési útjára.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Most állítsa be a tanúsítvány hozzáférés-vezérlését úgy, hogy a hálózati szolgáltatás fiókja alatt futó Service Fabric folyamat a következő parancsfájl futtatásával is használható legyen. Adja meg a szolgáltatás fiókja tanúsítványának és **hálózati szolgáltatásának** ujjlenyomatát. Győződjön meg arról, hogy a tanúsítvány ACL-jei helyesek, ha megnyitja a tanúsítvány **megkezdése** a  >  **számítógép-tanúsítványok kezelése** és a   >  **titkos kulcsok kezelése** minden feladat számára.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow" # "NT AUTHORITY\NetworkService" is the service account
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Ismételje meg az előző lépéseket az egyes kiszolgálói tanúsítványokhoz. Ezekkel a lépésekkel telepítheti az Ügyféltanúsítványok azon számítógépekre, amelyeknek engedélyezni szeretné a fürthöz való hozzáférést.

## <a name="create-the-secure-cluster"></a>A biztonságos fürt létrehozása
Miután konfigurálta a ClusterConfig.X509.MultiMachine.jsbiztonsági szakaszát a fájlon, folytathatja a [fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) szakaszt a csomópontok konfigurálásához és az önálló fürt létrehozásához. Ne felejtse el használni a ClusterConfig.X509.MultiMachine.jsfájlt a fürt létrehozásakor. Előfordulhat például, hogy a parancs a következőhöz hasonlóan néz ki:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Miután sikeresen futtatta a biztonságos önálló Windows-fürtöt, és beállította a hitelesített ügyfeleket a kapcsolódáshoz, kövesse a [Kapcsolódás fürthöz a PowerShell használatával](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) való kapcsolódáshoz című szakaszt. Például:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Ezután más PowerShell-parancsokat is futtathat a fürttel való együttműködéshez. A [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) futtatásával például megjelenítheti a biztonságos fürt csomópontjainak listáját.


A fürt eltávolításához kapcsolódjon ahhoz a fürthöz, amelyen a Service Fabric csomagot letöltötte, nyisson meg egy parancssort, és lépjen a csomag mappájába. Most futtassa a következő parancsot:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> A tanúsítvány helytelen konfigurációja miatt előfordulhat, hogy a fürt nem jön létre az üzembe helyezés során. A biztonsági problémák önálló diagnosztizálásához tekintse meg a Eseménynapló Group **Applications and Services**(  >  **Microsoft-Service Fabric**) naplókat.
> 
> 
