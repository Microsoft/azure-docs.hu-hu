---
title: Az Azure Service Fabric ajánlott biztonsági eljárásai
description: Ajánlott eljárások és kialakítási szempontok az Azure Service Fabric-fürtök és-alkalmazások biztonságának megőrzéséhez.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b7af0a4c26a47644973e936eb37e221853d74c03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784663"
---
# <a name="azure-service-fabric-security"></a>Az Azure Service Fabric biztonsága 

További információ az [Azure biztonsági eljárásairól](../security/index.yml): az [Azure Service Fabric ajánlott biztonsági eljárásainak](../security/fundamentals/service-fabric-best-practices.md) áttekintése

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](../key-vault/index.yml) a javasolt Secrets Management szolgáltatás az Azure Service Fabric-alkalmazásokhoz és-fürtökhöz.
> [!NOTE]
> Ha egy Key Vault tanúsítványait/titkos kulcsait virtuálisgép-méretezési csoportként telepítik, akkor a Key Vaultnak és a virtuálisgép-méretezési csoportnak közös helyen kell lennie.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Service Fabric tanúsítványt kiállító hitelesítésszolgáltató létrehozása

Egy Azure Key Vault tanúsítvány hozható létre vagy importálható egy Key Vaultba. Key Vault-tanúsítvány létrehozásakor a titkos kulcs a Key Vaulton belül jön létre, és soha nem lesz kitéve a tanúsítvány tulajdonosának. A következő módszerekkel hozhat létre tanúsítványokat a Key Vaultban:

- Hozzon létre egy önaláírt tanúsítványt egy nyilvános titkos kulcspár létrehozásához, és társítsa azt egy tanúsítványhoz. A tanúsítványt a saját kulcsa fogja aláírni. 
- Hozzon létre egy új tanúsítványt manuálisan egy nyilvános titkos kulcspár létrehozásához, és hozzon létre egy X. 509 tanúsítvány-aláírási kérelmet. Az aláírási kérelmet a regisztrációs hatóság vagy a hitelesítésszolgáltató aláírhatja. Az aláírt x509-tanúsítvány egyesíthető a függőben lévő kulcspár használatával, hogy elvégezze a KV-os tanúsítványt Key Vaultban. Bár ez a módszer több lépést igényel, nagyobb biztonságot nyújt, mivel a titkos kulcsot a-ben hozza létre, és a Key Vaultre korlátozódik. Ezt az alábbi ábra ismerteti. 

További részletekért tekintse át az [Azure kulcstartó tanúsítvány-létrehozási módszereit](../key-vault/certificates/create-certificate.md) .

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Key Vault tanúsítványok központi telepítése Service Fabric fürt virtuálisgép-méretezési csoportjaihoz

Ha egy közös elhelyezésű kulcstartóból szeretne tanúsítványokat telepíteni egy virtuálisgép-méretezési csoportba, használja a virtuálisgép-méretezési csoport [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). A Resource Manager-sablon tulajdonságai a következők:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> A tárolót engedélyezni kell a Resource Manager-sablonok telepítéséhez.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Access Control listájának (ACL) alkalmazása a Service Fabric-fürthöz tartozó tanúsítványra

A [virtuális gépek méretezési csoportjának bővítményei](/cli/azure/vmss/extension) a Microsoft. Azure. ServiceFabric a csomópontok biztonságának konfigurálására szolgálnak.
Ha ACL-t szeretne alkalmazni a Service Fabric-fürt folyamataihoz tartozó tanúsítványokra, használja az alábbi Resource Manager-sablon tulajdonságait:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Service Fabric-fürt tanúsítványának védelme köznapi név szerint

A Service Fabric-fürt tanúsítvány alapján történő biztonságossá tételéhez `Common Name` használja a Resource Manager-sablon tulajdonságának [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)a következő módon:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service Fabric-fürtök az első érvényes tanúsítványt fogják használni, amelyet a gazdagép tanúsítványtárolójában talál. Windows rendszeren ez az a tanúsítvány, amelynek a legkésőbbi lejárati dátuma megegyezik a köznapi névvel és a kiállító ujjlenyomatával.

Az Azure-tartományok, például a * \<YOUR SUBDOMAIN\> . cloudapp.Azure.com vagy \<YOUR SUBDOMAIN\> a. trafficmanager.net, a Microsoft tulajdonában vannak. A hitelesítésszolgáltatók nem adnak ki tanúsítványokat a tartományokhoz a jogosulatlan felhasználók számára. A legtöbb felhasználónak meg kell vásárolnia egy tartományt a regisztrátorból, vagy pedig a hitelesítő tartományi rendszergazdának kell lennie ahhoz, hogy egy hitelesítésszolgáltató kiadja az adott köznapi névvel rendelkező tanúsítványt.

Ha további információt szeretne arról, hogyan konfigurálhatja a DNS-szolgáltatást a tartomány Microsoft IP-címhez való feloldására, tekintse át a Azure DNS konfigurálása a [tartomány üzemeltetéséhez](../dns/dns-delegate-domain-azure-dns.md)című témakört.

> [!NOTE]
> Miután delegálta a tartományneveket a Azure DNS zónák névkiszolgálók számára, adja hozzá a következő két rekordot a DNS-zónához:
> - A tartományhoz tartozó "A" rekord, amely nem az `Alias record set` összes olyan IP-cím, amelyet az egyéni tartomány fel fog oldani.
> - "C" rekord a kiépített Microsoft sub-tartományokhoz `Alias record set` . Használhatja például a Traffic Manager vagy a Load Balancer DNS-nevét.

Ha a portált úgy szeretné frissíteni, hogy az Service Fabric-fürthöz tartozó egyéni DNS-nevet jelenítse meg `"managementEndpoint"` , frissítse a következő Service Fabric fürterőforrás-kezelő sablon tulajdonságait:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Service Fabric csomag titkos értékeinek titkosítása

Service Fabric csomagokba titkosított közös értékek közé tartoznak a Azure Container Registry (ACR) hitelesítő adatai, a környezeti változók, a beállítások és az Azure Volume plugin Storage-fiók kulcsa.

[Titkosítási tanúsítvány beállítása és a titkok titkosítása Windows-fürtökön](./service-fabric-application-secret-management-windows.md):

Önaláírt tanúsítvány létrehozása a titkos kulcs titkosításához:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

A [Key Vault tanúsítványok központi telepítése Service Fabric fürt virtuálisgép-méretezési csoportokban](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) található utasításokkal Key Vault tanúsítványokat telepíthet a Service Fabric fürt Virtual Machine Scale sets.

Titkosítsa a titkos kulcsot a következő PowerShell-parancs használatával, majd frissítse a Service Fabric alkalmazás jegyzékfájlját a titkosított értékkel:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

[Titkosítási tanúsítvány beállítása és a titkok titkosítása Linux-fürtökön](./service-fabric-application-secret-management-linux.md):

Önaláírt tanúsítvány létrehozása a titkos kulcsok titkosításához:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

A [Key Vault tanúsítványok központi telepítése Service Fabric fürt virtuálisgép-méretezési csoportokra](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) vonatkozó utasításokat a Service Fabric fürt Virtual Machine Scale sets.

Titkosítsa a titkos kulcsot a következő parancsokkal, majd frissítse a Service Fabric alkalmazás jegyzékfájlját a titkosított értékkel:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

A védett értékek titkosítása után [adja meg a titkosított titkokat Service Fabric alkalmazásban](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application), és [fejtse vissza a titkosított titkokat a szolgáltatási kódból](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Tanúsítvány belefoglalása Service Fabric alkalmazásokban

Ha hozzáférést szeretne adni az alkalmazáshoz a titkokhoz, vegye fel a tanúsítványt egy **SecretsCertificate** elem hozzáadásával az alkalmazás jegyzékfájlba.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Service Fabric-alkalmazások hitelesítése az Azure-erőforrásokhoz Managed Service Identity (MSI) használatával

Az Azure-erőforrások felügyelt identitásának megismeréséhez tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)című témakört.
Az Azure Service Fabric-fürtök a [Managed Service Identityt](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)támogató Virtual Machine Scale sets futnak.
Az MSI-t használó szolgáltatások listájának lekéréséhez tekintse meg az [Azure Active Directory hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).


A rendszerhez rendelt felügyelt identitás engedélyezéséhez egy virtuálisgép-méretezési csoport vagy egy meglévő virtuálisgép-méretezési csoport létrehozása során deklarálja a következő `"Microsoft.Compute/virtualMachinesScaleSets"` tulajdonságot:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
További információért lásd: [Mi az az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity) .

Ha  [felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)hozott létre, deklarálja a sablonban a következő erőforrást a virtuálisgép-méretezési csoporthoz való hozzárendeléshez. Cserélje le a `\<USERASSIGNEDIDENTITYNAME\>` nevet a létrehozott felhasználó által hozzárendelt felügyelt identitás nevére:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Ahhoz, hogy a Service Fabric alkalmazás használhassa a felügyelt identitást, engedélyeket kell adni a hitelesítéshez szükséges Azure-erőforrásokhoz.
Az alábbi parancsok hozzáférést biztosítanak egy Azure-erőforráshoz:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

A Service Fabric alkalmazás kódjában [szerezzen be egy hozzáférési jogkivonatot](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) a Azure Resource Managerhoz, és az alábbihoz hasonló Rest-t biztosít:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

A Service Fabric alkalmazás a hozzáférési token használatával hitelesítheti a Active Directory támogató Azure-erőforrásokat.
Az alábbi példa bemutatja, hogyan végezheti el ezt Cosmos DB erőforráshoz:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows biztonsági alaptervek
[Javasoljuk, hogy olyan iparági szabványnak megfelelő konfigurációt alkalmazzon, amely széles körben ismert és jól tesztelt, mint például a Microsoft biztonsági alapkonfigurációi, és ne hozzon létre egy alaptervet](/windows/security/threat-protection/windows-security-baselines); Ha ezeket a Virtual Machine Scale Sets szeretné kiépíteni, az Azure desired State Configuration (DSC) bővítmény kezelőjét kell használnia a virtuális gépek online állapotba való konfigurálásához, hogy azok az éles szoftvert használják.

## <a name="azure-firewall"></a>Azure Firewall
A [Azure Firewall felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure-Virtual Network erőforrásait. Ez egy teljesen állapot-nyilvántartó tűzfal, amely beépített, magas rendelkezésre állású és korlátlan Felhőbeli méretezhetőséggel rendelkezik.](../firewall/overview.md) Ez lehetővé teszi, hogy a kimenő HTTP/S forgalmat a teljes tartománynevek (FQDN) egy adott listájára korlátozza, beleértve a helyettesítő kártyákat is. Ez a funkció nem igényli a TLS/SSL-megszakítást. Javasoljuk, hogy a Windows-frissítések [Azure Firewall FQDN-címkéit](../firewall/fqdn-tags.md) használja, és engedélyezze a hálózati forgalmat a Microsoft Windows Update végpontok számára a tűzfalon keresztül. [Azure Firewall üzembe helyezése sablon használatával](../firewall/deploy-template.md) a Microsoft. Network/azureFirewalls erőforrás-sablon definícióját tartalmazza. Az Service Fabric alkalmazások számára közös tűzfalszabályok lehetővé teszik a következők használatát a fürtök virtuális hálózata számára:

- * download.microsoft.com
- * servicefabric.azure.com
- *.core.windows.net

Ezek a tűzfalszabályok kiegészítik az engedélyezett kimenő hálózati biztonsági csoportokat, amelyek a ServiceFabric és a tárterületet is tartalmazzák a virtuális hálózatból engedélyezett célhelyként.

## <a name="tls-12"></a>TLS 1.2

A Microsoft [Azure azt javasolja](https://azure.microsoft.com/updates/azuretls12/) , hogy minden ügyfél teljes áttelepítést végezzen a Transport Layer Security (tls) 1,2-et támogató megoldások felé, és győződjön meg arról, hogy a TLS 1,2 alapértelmezés szerint használatos.

Az Azure-szolgáltatások, beleértve a [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493)is, befejezték a mérnöki munkát a TLS 1.0/1.1 protokolloktól való függőség megszüntetéséhez, és teljes körű támogatást biztosítanak azoknak az ügyfeleknek, akik csak TLS 1,2-kapcsolatok elfogadására és elindítására szeretnének konfigurálva.

Az ügyfeleknek az Azure-szolgáltatásokkal együttműködve az Azure által üzemeltetett számítási feladatait és helyszíni alkalmazásait a TLS 1,2 alapértelmezett használatára kell beállítani. A következőképpen [konfigurálhatja Service Fabric fürtcsomópontok és alkalmazások](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) számára egy adott TLS-verzió használatát.

## <a name="windows-defender"></a>Windows Defender 

Alapértelmezés szerint a Windows Defender víruskereső a Windows Server 2016 rendszerre van telepítve. Részletekért lásd: [Windows Defender víruskereső a Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)rendszeren. A felhasználói felület néhány SKU-ban alapértelmezés szerint telepítve van, de nem szükséges. A Windows Defender által felmerülő teljesítmény-és erőforrás-felhasználás csökkentése érdekében, valamint ha a biztonsági szabályzatok lehetővé teszik a nyílt forráskódú szoftverek folyamatainak és elérési útjának kizárását, állapítsa meg a következő virtuálisgép-méretezési csoport bővítmény Resource Manager-sablon tulajdonságait, hogy kizárja a Service Fabric-fürtöt a vizsgálatoktól:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Ha nem a Windows Defendert használja, tekintse meg a konfigurációs szabályok antimalware-dokumentációját. A Windows Defender Linux rendszeren nem támogatott.

## <a name="platform-isolation"></a>Platform elkülönítése
Alapértelmezés szerint Service Fabric az alkalmazások hozzáférést kapnak a Service Fabric futtatókörnyezethez, amely különböző formákban nyilvánul meg: [környezeti változók](service-fabric-environment-variables-reference.md) , amelyek az alkalmazás és a háló fájljainak megfelelő gazdagép elérési útjaira mutatnak, egy folyamaton belüli kommunikációs végpontot, amely az alkalmazásspecifikus kérelmeket fogadja el, valamint azt az ügyféltanúsítványt, amelyet a háló az alkalmazás hitelesítéséhez használ. Abban az esetben, ha a szolgáltatás nem megbízható programkódot üzemeltet, célszerű letiltani ezt a hozzáférést az SF futtatókörnyezethez – hacsak erre nincs kifejezetten szükség. A futtatókörnyezet elérését az alkalmazás jegyzékfájljának szabályzatok szakaszának következő nyilatkozata alapján távolítja el a rendszer: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Következő lépések

* Hozzon létre egy fürtöt a virtuális gépeken vagy a Windows Servert futtató számítógépeken: [Service Fabric a fürt létrehozását a Windows Server](service-fabric-cluster-creation-for-windows-server.md)rendszerhez.
* Hozzon létre egy fürtöt a virtuális gépeken vagy számítógépeken Linux rendszeren: [hozzon létre egy Linux-fürtöt](service-fabric-cluster-creation-via-portal.md).
* További információ a [Service Fabric támogatási lehetőségeiről](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
