---
title: Windows rendszerű Service Fabric-fürt létrehozása az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe Windows Service Fabric-fürtöt egy Azure-beli virtuális hálózatban és hálózati biztonsági csoportban a PowerShell használatával.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245041"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Oktatóanyag: Windows rendszert futtató Service Fabric-fürt üzembe helyezése Azure-beli virtuális hálózatban

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan helyezhet üzembe Windows rendszerű Azure Service Fabric-fürtöt egy Azure-beli [virtuális hálózatban](../virtual-network/virtual-networks-overview.md) és [hálózati biztonsági csoportban](../virtual-network/virtual-network-vnet-plan-design-arm.md) a PowerShell és egy sablon használatával. Ha elkészült, egy olyan fürt fut a felhőben, amelybe alkalmazásokat telepíthet. Az Azure CLI-t használó linuxos fürtök létrehozásával kapcsolatban lásd: [biztonságos Linux-fürt létrehozása az Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md)-ban.

Ez az oktatóanyag egy éles forgatókönyvet ismertet. Ha egy kisebb fürtöt szeretne tesztelési célokra létrehozni, tekintse meg [a tesztelési fürt létrehozása](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)című témakört.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * A telepítő Azure Active Directory hitelesítés
> * Diagnosztikai gyűjtemény konfigurálása
> * A EventStore szolgáltatás beállítása
> * Azure Monitor naplók beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-ban
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Service FABRIC SDK-t és a PowerShell-modult](service-fabric-get-started.md).
* Telepítse a [Azure PowerShell](/powershell/azure/install-az-ps).
* Tekintse át az [Azure-fürtök](service-fabric-azure-clusters-overview.md)főbb fogalmait.
* [Tervezze meg és készítse elő](service-fabric-cluster-azure-deployment-preparation.md) az üzemi fürtök üzembe helyezését.

Az alábbi eljárások hét csomópontos Service Fabric fürtöt hoznak létre. Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) kiszámíthatja a Service Fabric-fürt Azure-ban való futtatásával járó költségeket.

## <a name="download-and-explore-the-template"></a>A sablon letöltése és megismerése

Töltse le a következő Azure Resource Manager sablonfájlokat:

* [azuredeploy.jsbekapcsolva][template]
* [azuredeploy.parameters.jsbekapcsolva][parameters]

Ez a sablon hét virtuális gép és három csomópont típusú biztonságos fürtöt telepít egy virtuális hálózatba és egy hálózati biztonsági csoportba.  További mintasablonokat a [GitHubon](https://github.com/Azure-Samples/service-fabric-cluster-templates) talál. A [azuredeploy.js][template] több erőforrást helyez üzembe, beleértve a következőket is.

### <a name="service-fabric-cluster"></a>Service Fabric-fürt

A **Microsoft.ServiceFabric/clusters** erőforrásban egy Windows-fürt az alábbi jellemzőkkel lesz konfigurálva:

* Három csomópont-típus.
* Az elsődleges csomópont típusának öt csomópontja (a sablon paramétereinek megfelelően konfigurálható), a másik két csomópontban pedig egy csomópont található.
* Operációs rendszer: Windows Server 2016 Datacenter tárolókkal (konfigurálható a sablon paraméterei között).
* A tanúsítvány védett (a sablon paramétereinek megfelelően konfigurálható).
* A [fordított proxy](service-fabric-reverseproxy.md) engedélyezve van.
* A [DNS-szolgáltatás](service-fabric-dnsservice.md) engedélyezve van.
* A bronz [tartóssági szintje](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) (a sablon paramétereinek megfelelően konfigurálható).
* Az ezüst [megbízhatósági szintje](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) (a sablon paramétereinek megfelelően konfigurálható).
* Ügyfélkapcsolati végpont: 19000 (konfigurálható a sablon paraméterei között).
* HTTP-átjáró végpontja: 19080 (konfigurálható a sablon paraméterei között).

### <a name="azure-load-balancer"></a>Azure Load Balancer

A **Microsoft. Network/loadBalancers** erőforrásban a terheléselosztó konfigurálva van. A mintavételek és szabályok a következő portokra vannak beállítva:

* Ügyfélkapcsolati végpont: 19000
* HTTP-átjáró végpontja: 19080;
* Alkalmazás portja: 80
* Alkalmazás portja: 443
* Service Fabric fordított proxyja: 19081.

Ha más alkalmazás-portok is szükségesek, akkor módosítania kell a **Microsoft. Network/loadBalancers** erőforrást és a **Microsoft. Network/networkSecurityGroups** erőforrást a forgalom engedélyezéséhez.

### <a name="virtual-network-subnet-and-network-security-group"></a>Virtuális hálózat, alhálózat és hálózati biztonsági csoport

A virtuális hálózat, az alhálózat és a hálózati biztonsági csoport neve a sablon paramétereiben van meghatározva. A virtuális hálózat és az alhálózat címtere szintén a sablon paramétereiben határozható meg, és a **Microsoft.Network/virtualNetworks** erőforrásban van konfigurálva:

* Virtuális hálózati címtartomány: 172.16.0.0/20
* Service Fabric-alhálózat címtere: 172.16.2.0/23.

Az alábbi bejövő forgalmi szabályok vannak engedélyezve a **Microsoft.Network/networkSecurityGroups** erőforrásban. A portok értékét a sablon változóinak módosításával módosíthatja.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Ideiglenes porttartomány: 49152 – 65534 (legalább 256 portnak kell lennie).
* alkalmazások által használható portok: 80 és 443,
* Alkalmazás-porttartomány: 49152 – 65534 (a szolgáltatás és a szolgáltatás közötti kommunikációhoz használatos). Más portok nincsenek megnyitva a terheléselosztó esetében).
* összes többi port letiltása.

Ha más alkalmazás-portok is szükségesek, akkor módosítania kell a **Microsoft. Network/loadBalancers** erőforrást és a **Microsoft. Network/networkSecurityGroups** erőforrást a forgalom engedélyezéséhez.

### <a name="windows-defender"></a>Windows Defender
Alapértelmezés szerint a Windows [Defender víruskereső program](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) telepítve van és működőképes a windows Server 2016 rendszeren. A felhasználói felület néhány SKU-ban alapértelmezés szerint telepítve van, de nem kötelező. A sablonban deklarált minden egyes csomópont típus/virtuálisgép-méretezési csoport esetében az [Azure VM antimalware bővítmény](../virtual-machines/extensions/iaas-antimalware-windows.md) a Service Fabric címtárak és folyamatok kizárására szolgál:

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
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
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

## <a name="set-template-parameters"></a>Sablon paramétereinek megadása

Az [azuredeploy.parameters.json][parameters] paraméterfájl számos, a fürt és a társított erőforrások üzembe helyezéséhez használt értéket meghatároz. A következő paramétereket kell módosítani a telepítéshez:

**Paraméter** | **Példa értéke** | **Megjegyzések** 
|---|---|---|
|adminUserName|vmadmin| Rendszergazdai felhasználónév a fürt virtuális gépeihez. [A virtuális gép felhasználónévre vonatkozó követelményei](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| Rendszergazdai jelszó a fürt virtuális gépeihez. [A virtuális gép jelszavára vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| A fürt neve. Csak betűket és számokat tartalmazhat. 3–23 karakter hosszú lehet.|
|location|southcentralus| A fürt helye. |
|certificateThumbprint|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány SHA1 ujjlenyomatának értékét. Például: „6190390162C988701DB5676EB81083EA608DCCF3”</p> |
|certificateUrlValue|| <p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie. </p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a tanúsítvány URL-címét. Példa: "https: \/ /mykeyvault.Vault.Azure.net:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Önaláírt tanúsítvány létrehozása vagy tanúsítványfájl megadása esetén az értéknek üresnek kell lennie.</p><p>Ha meglévő, egy kulcstárolóba korábban feltöltött tanúsítványt szeretne használni, adja meg a forrástároló értékét. For example, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Azure Active Directory ügyfél-hitelesítés beállítása
Az Azure-ban üzemeltetett nyilvános hálózatokban üzembe helyezett Service Fabric-fürtök esetén az ügyfél és a csomópont közötti kölcsönös hitelesítésre vonatkozó javaslat a következő:
* Azure Active Directory használata az ügyfél identitásához.
* Tanúsítvány használata a HTTP-kommunikáció kiszolgálói identitására és TLS-titkosítására.

Az Azure Active Directory (Azure AD) beállításával a [fürt létrehozása](#createvaultandcert)előtt el kell végezni a Service Fabric-fürthöz tartozó ügyfelek hitelesítését. Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlők) az alkalmazásokhoz való felhasználói hozzáférést kezeljék. 

A Service Fabric-fürtök több belépési pontot biztosítanak a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) és a [Visual studiót](service-fabric-manage-application-in-visual-studio.md)is. Ennek eredményeképpen két Azure AD-alkalmazást hoz létre a fürt elérésének vezérléséhez: egy webalkalmazást és egy natív alkalmazást.  Az alkalmazások létrehozása után a felhasználókat a csak olvasási és rendszergazdai szerepkörökhöz rendelheti hozzá.

> [!NOTE]
> A fürt létrehozása előtt végre kell hajtania a következő lépéseket. Mivel a parancsfájlok a fürtök és a végpontok számára is várnak, az értékeket meg kell tervezni, és nem a már létrehozott értékeket.

Ez a cikk azt feltételezi, hogy már létrehozott egy bérlőt. Ha még nem tette meg, először olvassa el a [Azure Active Directory bérlő beszerzését ismertető témakört](../active-directory/develop/quickstart-create-new-tenant.md).

Az Azure AD Service Fabric-fürttel való konfigurálásának lépéseinek egyszerűbbé tétele érdekében létrehoztunk egy Windows PowerShell-szkriptet. [Töltse le a szkripteket](https://github.com/Azure-Samples/service-fabric-aad-helpers) a számítógépre.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Azure AD-alkalmazások létrehozása és felhasználók szerepkörökhöz rendelése
Hozzon létre két Azure AD-alkalmazást a fürt elérésének vezérléséhez: egy webalkalmazást és egy natív alkalmazást. Miután létrehozta az alkalmazásokat a fürt képviseletére, rendelje hozzá a felhasználókat a [Service Fabric által támogatott szerepkörökhöz](service-fabric-cluster-security-roles.md): csak olvasható és rendszergazda.

Futtassa `SetupApplications.ps1` a parancsot, és adja meg a bérlő azonosítóját, a fürt nevét és a webalkalmazás válaszának URL-címét paraméterként. Felhasználónevek és jelszavak megadása a felhasználók számára. Például:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Az országos felhőknél (például Azure Government, Azure China, Azure Germany) a paramétert kell megadnia `-Location` .

A *TenantId* vagy a könyvtár azonosítóját a [Azure Portalban](https://portal.azure.com)találja. Válassza ki **Azure Active Directory**  >  **tulajdonságokat** , és másolja a **címtár-azonosító** értékét.

A *ClusterName* a parancsfájl által létrehozott Azure ad-alkalmazások előtagját használja. Nem kell pontosan megegyeznie a fürt tényleges nevével. Ez a művelet csak az Azure AD-összetevők leképezését teszi lehetővé a használatban lévő Service Fabric-fürthöz.

A *WebApplicationReplyUrl* az az alapértelmezett végpont, amelyet az Azure ad visszaküld a felhasználóknak a bejelentkezés befejezését követően. Állítsa ezt a végpontot a fürt Service Fabric Explorer végpontjának, amely alapértelmezés szerint a következő:

https:// &lt; cluster_domain &gt; : 19080/Explorer

A rendszer arra kéri, hogy jelentkezzen be egy olyan fiókba, amely rendszergazdai jogosultságokkal rendelkezik az Azure AD-bérlőhöz. A bejelentkezést követően a parancsfájl létrehozza a webes és natív alkalmazásokat, hogy az Service Fabric-fürtöt képviseljék. A [Azure Portal](https://portal.azure.com)bérlő alkalmazásaiban két új bejegyzést kell látnia:

   * *ClusterName* \_ Fürt
   * *ClusterName* \_ Ügyfél

A parancsfájl a fürt létrehozásakor kinyomtatja a Resource Manager-sablon által megkövetelt JSON-t, ezért érdemes megnyitnia a PowerShell ablakát.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Azure AD-konfiguráció hozzáadása az Azure AD ügyfél-hozzáféréshez való használatához
A [azuredeploy.jsa][template](z) alkalmazásban konfigurálja az Azure ad-t a **Microsoft. ServiceFabric/fürtök** szakaszban. Adja hozzá a bérlői azonosító, a fürt alkalmazás-azonosítója és az ügyfélalkalmazás AZONOSÍTÓjának paramétereit.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Adja hozzá a paraméter értékeit a [azuredeploy.parameters.jsa][parameters] Parameters fájlban. Például:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Diagnosztikai gyűjtemény konfigurálása a fürtön
Ha Service Fabric fürtöt futtat, érdemes összegyűjteni a naplókat egy központi helyen lévő összes csomópontról. A központi helyen található naplók segítségével elemezheti és elháríthatja a fürtben felmerülő problémákat, illetve a fürtben futó alkalmazások és szolgáltatások hibáit.

A naplók feltöltésének és összegyűjtésének egyik módja a Azure Diagnostics (WAD) bővítmény használata, amely a naplókat feltölti az Azure Storage-ba, és lehetővé teszi a naplók küldését az Azure-ba Application Insights vagy Event Hubs. Külső folyamattal is elolvashatja az eseményeket a tárolóból, és elhelyezheti azokat egy Analysis platform termékében, például Azure Monitor naplókban vagy egy másik naplózási megoldásban.

Ha ezt az oktatóanyagot követi, a diagnosztikai gyűjtemény már konfigurálva van a [sablonban][template].

Ha olyan meglévő fürttel rendelkezik, amelyen nincs telepítve a diagnosztika, a fürt sablonján keresztül is hozzáadhatja vagy frissítheti azt. Módosítsa a meglévő fürt létrehozásához használt Resource Manager-sablont, vagy töltse le a sablont a portálról. Módosítsa a fájl template.jsa következő feladatok végrehajtásával:

Adjon hozzá egy új tárolási erőforrást a sablon erőforrások szakaszához:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Ezután adjon hozzá paramétereket a Storage-fiók nevéhez, és írja be a sablon parameters (paraméterek) szakaszát. Cserélje le a helyőrző szöveges Storage-fiók nevét a kívánt Storage-fiók nevére.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Ezután adja hozzá a **IaaSDiagnostics** bővítményt a fürt minden **Microsoft. számítási/virtualMachineScaleSets** -erőforrásának **VirtualMachineProfile** tulajdonságának Extensions tömbéhez.  A [minta sablon][template]használata esetén három virtuálisgép-méretezési csoport létezik (egyet a fürt minden egyes csomópont-típusához).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>A EventStore szolgáltatás konfigurálása
A EventStore szolgáltatás egy figyelési lehetőség a Service Fabricban. A EventStore lehetővé teszi a fürt vagy a számítási feladatok állapotának megértését egy adott időpontban. A EventStore olyan állapot-nyilvántartó Service Fabric szolgáltatás, amely az eseményeket a fürtből tartja karban. Az eseményt a Service Fabric Explorer, a REST és az API-k teszik elérhetővé. A EventStore közvetlenül lekérdezi a fürtöt a fürtben lévő bármely entitás diagnosztikai adataihoz, és a következő segítségére használható:

* A fejlesztési vagy tesztelési problémák diagnosztizálása, illetve a figyelési folyamat használata
* Győződjön meg arról, hogy a fürtön végzett felügyeleti műveletek megfelelően vannak feldolgozva
* Az Service Fabric egy adott entitással való interakciójának pillanatképe



A EventStore szolgáltatás fürtön való engedélyezéséhez adja hozzá a következőt a **Microsoft. ServiceFabric/Clusters** erőforrás **fabricSettings** tulajdonságához:

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Azure Monitor naplók beállítása a fürthöz

Azure Monitor naplókat ajánljuk a fürt szintű események figyelésére. Ha Azure Monitor naplókat szeretne beállítani a fürt figyeléséhez, engedélyezni kell a [diagnosztika szolgáltatást a fürt szintű események megtekintéséhez](#configure-diagnostics-collection-on-the-cluster).  

A munkaterületet a fürtről érkező diagnosztikai adatokhoz kell csatlakoztatni.  A rendszer a *applicationDiagnosticsStorageAccountName* a WADServiceFabric * EventTable, a WADWindowsEventLogsTable és a WADETWEventTable táblákban tárolja.

Adja hozzá az Azure Log Analytics munkaterületet, és adja hozzá a megoldást a munkaterülethez:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Következő lépésként adja hozzá a paramétereket
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Következő lépésként adja hozzá a változókat:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adja hozzá a Log Analytics Agent bővítményt a fürt minden virtuálisgép-méretezési készletéhez, és az ügynököt a Log Analytics munkaterülethez. Ez lehetővé teszi a tárolók, alkalmazások és Teljesítményfigyelés diagnosztikai adatainak gyűjtését. Ha hozzáad egy bővítményt a virtuálisgép-méretezési csoport erőforrásához, Azure Resource Manager biztosítja, hogy az minden csomóponton telepítve legyen, még a fürt skálázásakor is.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>A virtuális hálózat és a fürt üzembe helyezése

Ezután állítsa be a hálózati topológiát, és helyezze üzembe a Service Fabric-fürtöt. A Resource Manager-sablon [azuredeploy.js][template] létrehoz egy virtuális hálózatot, alhálózatot és hálózati biztonsági csoportot a Service Fabrichoz. A sablon emellett egy fürtöt is üzembe helyez engedélyezett tanúsítványalapú biztonsággal. Éles fürtök esetén használjon tanúsítványokat a hitelesítésszolgáltatótól a fürt tanúsítványa alapján. A tesztfürtök számára önaláírt tanúsítvánnyal is biztosítható védelem.

A cikkben található sablon olyan fürtöt helyez üzembe, amely a tanúsítvány ujjlenyomatát használja a fürt tanúsítványának azonosításához. Nincs két tanúsítvány ugyanazzal az ujjlenyomattal, ami nehezebbé teszi a Tanúsítványkezelőt. Ha egy telepített fürtöt a tanúsítvány ujjlenyomatai megfelelnek a tanúsítvány köznapi neveire vált, egyszerűsíti a Tanúsítványkezelőt. Ha meg szeretné tudni, hogyan frissítheti a fürtöt a tanúsítványok köznapi neveinek használatára, olvassa el a [fürt módosítása a tanúsítvány köznapi nevének kezelése](service-fabric-cluster-change-cert-thumbprint-to-cn.md)című témakört.

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Fürt létrehozása meglévő tanúsítvány használatával

Az alábbi szkript a [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) parancsmagot és egy sablont használ egy új fürt üzembe helyezéséhez az Azure-ban. A parancsmag létrehoz egy új Key vaultot az Azure-ban, és feltölti a tanúsítványt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Fürt létrehozása új, önaláírt tanúsítvány használatával

Az alábbi szkript a [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) parancsmagot és egy sablont használ egy új fürt üzembe helyezéséhez az Azure-ban. A parancsmag létrehoz egy új kulcstartót az Azure-ban, hozzáadja az új önaláírt tanúsítványt a kulcstartóhoz, és helyileg letölti a tanúsítványt.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz

Kapcsolódjon a fürthöz a Service Fabric SDK-val telepített Service Fabric PowerShell-modul használatával.  Először telepítse a tanúsítványt a számítógépen az aktuális felhasználó Személyes (Saját) tárolójába. Futtassa az alábbi PowerShell-parancsot:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Most már készen áll a biztonságos fürthöz való kapcsolódásra.

A **Service Fabric** PowerShell-modul számos parancsmagot biztosít a Service Fabric-fürtök, -alkalmazások és -szolgáltatások kezelésére. A biztonságos fürthöz való kapcsolódáshoz használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmagot. A tanúsítvány SHA1 ujjlenyomatával és kapcsolati végpontjával kapcsolatos részletek az előző lépés kimenetében találhatók.

Ha korábban már beállította az Azure AD-ügyfél hitelesítését, futtassa a következő parancsot: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Ha nem állította be az Azure AD-ügyfél hitelesítését, futtassa a következő parancsot:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

A [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmag használatával győződjön meg arról, hogy csatlakoztatva van, és hogy a fürt kifogástalan állapotú.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag-Sorozat további cikkei a létrehozott fürtöt használják. Ha nem azonnal tér rá a következő cikkre, érdemes [törölnie a fürtöt](./service-fabric-tutorial-delete-cluster.md) a felmerülő költségek elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan méretezheti a fürtöt.

> [!div class="checklist"]
> * VNET létrehozása a PowerShell használatával
> * Kulcstartó létrehozása és tanúsítvány feltöltése
> * A telepítő Azure Active Directory hitelesítés
> * Diagnosztikai gyűjtemény konfigurálása
> * A EventStore szolgáltatás beállítása
> * Azure Monitor naplók beállítása
> * Biztonságos Service Fabric-fürt létrehozása az Azure PowerShellben
> * A fürt védelme X.509-tanúsítvánnyal
> * Csatlakozás a fürthöz PowerShell használatával
> * Fürt eltávolítása

Ezután folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan figyelheti meg a fürtöt.
> [!div class="nextstepaction"]
> [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
