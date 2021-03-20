---
title: Fürt létrehozása a tanúsítvány köznapi neve alapján
description: Megtudhatja, hogyan hozhat létre Service Fabric-fürtöt a tanúsítvány köznapi neve alapján sablonból.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c852b40d35f936753d3c16420159676da239b6c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86246435"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Tanúsítvány köznapi nevét használó Service Fabric-fürt üzembe helyezése ujjlenyomat helyett
Két tanúsítvány nem rendelkezhet ugyanazzal az ujjlenyomattal, ami lehetővé teszi a fürt tanúsítványainak átváltását vagy felügyeletét. Több tanúsítvány, azonban ugyanaz a köznapi név vagy a tárgy lehet.  A tanúsítvány köznapi neveit használó fürtök sokkal egyszerűbbé teszik a Tanúsítványkezelőt. Ez a cikk bemutatja, hogyan helyezhet üzembe egy Service Fabric-fürtöt a tanúsítványhoz tartozó köznapi név használatára a Tanúsítvány ujjlenyomata helyett.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Tanúsítvány beszerzése
Először kérje le a tanúsítványt egy [hitelesítésszolgáltatótól (CA)](https://wikipedia.org/wiki/Certificate_authority).  A tanúsítvány köznapi nevének a saját egyéni tartományának kell lennie, és egy tartományregisztráló alapján kell megvásárolnia. Például: "azureservicefabricbestpractices.com"; azok, akik nem Microsoft-alkalmazottak, nem tudnak kiépíteni tanúsítványokat az MS-tartományokhoz, így nem használhatja az LB vagy a Traffic Manager DNS-neveit a tanúsítvány általános neveként, és létre kell hoznia egy [Azure DNS zónát](../dns/dns-delegate-domain-azure-dns.md) , ha az egyéni tartomány feloldható az Azure-ban. Ha azt szeretné, hogy a portál tükrözze a fürt egyéni tartományának aliasát, azt is be kell jelentenie, hogy a fürt "z".

Tesztelési célból a HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványt egy ingyenes vagy nyitott hitelesítésszolgáltatótól szerezheti be.

> [!NOTE]
> Az önaláírt tanúsítványok, beleértve azokat is, amelyek akkor jönnek létre, amikor egy Service Fabric fürtöt telepítenek a Azure Portal, nem támogatottak. 

## <a name="upload-the-certificate-to-a-key-vault"></a>A tanúsítvány feltöltése kulcstartóba
Az Azure-ban egy Service Fabric-fürt üzembe helyezése egy virtuálisgép-méretezési csoporton történik.  Töltse fel a tanúsítványt egy kulcstartóba.  A fürt telepítésekor a tanúsítvány települ a virtuálisgép-méretezési csoportba, amelyen a fürt fut.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Minta sablon letöltése és frissítése
Ez a cikk az [5 csomópontos biztonságos fürt – példa](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) sablont és a sablon paramétereit használja. Töltse le a *azuredeploy.jst* , és *azuredeploy.parameters.js* fájlokat a számítógépre.

### <a name="update-parameters-file"></a>Paraméterek frissítése fájl
Először nyissa meg a *azuredeploy.parameters.js* fájlt egy szövegszerkesztőben, és adja hozzá a következő paraméter értékét:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Ezután állítsa be a *certificateCommonName*, a *SourceVaultValue* és a *certificateUrlValue* paraméter értékét az előző parancsfájl által visszaadott értékekre:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>A sablonfájl módosítása
Ezután nyissa meg a *azuredeploy.js* fájlt egy szövegszerkesztőben, és három frissítést készítsen a tanúsítvány köznapi nevének támogatásához.

1. A **Parameters (paraméterek** ) szakaszban adjon hozzá egy *certificateCommonName* paramétert:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Érdemes megfontolni a *certificateThumbprint* eltávolítását is, ha már nincs rá szükség.

2. Állítsa a *sfrpApiVersion* változó értékét "2018-02-01" értékre:
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. A **Microsoft. számítási/virtualMachineScaleSets** erőforrásban frissítse a virtuálisgép-bővítményt, hogy az ujjlenyomat helyett az általános nevet használja a tanúsítvány beállításainál.  A **virtualMachineProfile** -> **extensionProfile**- -> **bővítmények** -> **Tulajdonságok** -> **beállításainak** -> **tanúsítványa** területen adja hozzá a 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    és távolítsa el `"thumbprint": "[parameters('certificateThumbprint')]",` .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. A **Microsoft. ServiceFabric/Clusters** erőforrásban frissítse az API verzióját "2018-02-01"-re.  Vegyen fel egy **certificateCommonNames** -beállítást egy **commonNames** tulajdonsággal, és távolítsa el a **tanúsítvány** beállítását (az ujjlenyomat tulajdonsággal), ahogy az alábbi példában is látható:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > A "certificateIssuerThumbprint" mező lehetővé teszi a tanúsítványok várt kibocsátóinak megadását egy adott tulajdonos köznapi nevével. Ez a mező az SHA1 ujjlenyomatai megfelelnek vesszővel tagolt enumerálását fogadja el. Vegye figyelembe, hogy ez a tanúsítvány érvényesítésének megerősítése – abban az esetben, ha a kiállító nincs megadva vagy üres, akkor a rendszer a tanúsítványt akkor fogadja el hitelesítésre, ha a lánca felépíthető, és az érvényesítő által megbízhatónak tartott legfelső szinttel végződik. Ha a kiállító meg van adva, a rendszer elfogadja a tanúsítványt, ha a közvetlen kiállító ujjlenyomata megfelel az ebben a mezőben megadott értékek bármelyikének, függetlenül attól, hogy a gyökér megbízható-e. Vegye figyelembe, hogy a nyilvános kulcsokra épülő infrastruktúra különböző hitelesítésszolgáltatók használatával állít ki tanúsítványokat ugyanahhoz a tárgyhoz, ezért fontos megadnia az adott tárgyhoz tartozó összes várható kiállítói ujjlenyomatai megfelelnek.
   >
   > A kibocsátó meghatározása ajánlott eljárásnak minősül; Ha kihagyja, továbbra is működni fog – a megbízható legfelső szintű tanúsítványok esetében – ez a viselkedés korlátozásokkal jár, és a közeljövőben fokozatosan elvégezhető. Azt is vegye figyelembe, hogy az Azure-ban üzembe helyezett fürtök, valamint a privát PKI által kiadott és a tulajdonos által deklarált X509-tanúsítványokkal védettek, az Azure Service Fabric szolgáltatás nem tudja érvényesíteni (a fürtök közötti kommunikációhoz), ha a PKI tanúsítvány-házirendje nem felderíthető, elérhető és hozzáférhető. 

## <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése
A módosítások végrehajtása után telepítse újra a frissített sablont.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Következő lépések
* További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
* Tudnivalók a [fürt tanúsítványainak átváltásáról](service-fabric-cluster-rollover-cert-cn.md)
* [Fürt tanúsítványainak frissítése és kezelése](service-fabric-cluster-security-update-certs-azure.md)
* A Tanúsítványkezelő leegyszerűsítése a [fürt a tanúsítvány ujjlenyomatáról a köznapi névre való módosításával](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
