---
title: Csomópontok hozzáadása vagy eltávolítása önálló Service Fabric-fürthöz
description: Megtudhatja, hogyan adhat hozzá vagy távolíthat el csomópontokat egy Azure Service Fabric-fürthöz egy Windows Servert futtató fizikai vagy virtuális gépen, amely a helyszínen vagy bármilyen felhőben is lehetséges.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790596"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Csomópontok hozzáadása vagy eltávolítása a Windows Serveren futó önálló Service Fabric-fürt esetében
Miután [létrehozta az önálló Service Fabric-fürtöt a Windows Server rendszerű gépeken](service-fabric-cluster-creation-for-windows-server.md), a (vállalati) igények változhatnak, és hozzá kell adnia vagy el kell távolítania a fürt csomópontjait a jelen cikkben leírtak szerint.

> [!NOTE]
> A csomópont-hozzáadási és-eltávolítási funkciók nem támogatottak a helyi fejlesztési fürtökben.

## <a name="add-nodes-to-your-cluster"></a>Csomópontok hozzáadása a fürthöz

1. Készítse elő a fürthöz hozzáadni kívánt virtuális gépet/gépet a [Service Fabric-fürt üzembe helyezésének megtervezése és előkészítése](service-fabric-cluster-standalone-deployment-preparation.md)című témakörben ismertetett lépéseket követve.

2. Határozza meg, hogy melyik tartalék tartományhoz és frissítési tartományhoz kívánja hozzáadni a virtuális gépet/gépet.

   Ha tanúsítványokat használ a fürt biztonságossá tételéhez, a rendszer a csomópontnak a fürthöz való csatlakoztatásához szükséges tanúsítványokat a helyi tanúsítványtárolókban fogja telepíteni. Az analóg a biztonság más formáinak használatakor alkalmazható.

3. A távoli asztal (RDP) a fürthöz hozzáadni kívánt virtuális gépre vagy gépre.

4. Másolja vagy [töltse le a Windows Server rendszerhez készült Service Fabric önálló csomagját](https://go.microsoft.com/fwlink/?LinkId=730690) a virtuális gépre vagy gépre, és bontsa ki a csomagot.

5. Futtassa a PowerShellt emelt szintű jogosultságokkal, és nyissa meg a kibontott csomag helyét.

6. Futtassa a *AddNode.ps1* szkriptet a hozzáadandó új csomópontot leíró paraméterekkel. A következő példa egy VM5 nevű új csomópontot helyez üzembe a NodeType0 és az IP-182.17.34.52, a UD1 és az FD:/DC1/R0 típussal. `ExistingClusterConnectionEndPoint` a már meglévő fürtben lévő csomóponthoz tartozó kapcsolatok végpontja, amely a fürt *bármely* CSOMÓPONTJÁNAK IP-címe lehet. 

   Nem biztonságos (prototípus):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Biztonságos (tanúsítvány-alapú):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Ha a parancsfájl futása befejeződött, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) parancsmag futtatásával megtekintheti, hogy az új csomópont hozzá lett-e adva.

7. A fürt különböző csomópontjai közötti konzisztencia biztosításához a konfiguráció frissítését kell elindítania. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) parancsot a legújabb konfigurációs fájl beszerzéséhez, és adja hozzá az újonnan hozzáadott csomópontot a "Nodes" (csomópontok) szakaszhoz. Azt is javasoljuk, hogy mindig legyen elérhető a legújabb fürtkonfiguráció abban az esetben, ha újra kell telepíteni egy olyan fürtöt, amely ugyanazzal a konfigurációval rendelkezik.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) parancsot.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade)t futtatja.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Csomópontok hozzáadása a Windows biztonsággal konfigurált fürtökhöz a gMSA használatával
A csoportosan felügyelt szolgáltatásfiók (gMSA) használatával konfigurált fürtök esetében egy https://technet.microsoft.com/library/hh831782.aspx) új csomópontot adhat hozzá a konfiguráció frissítésével:
1. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) parancsot bármelyik meglévő csomóponton a legújabb konfigurációs fájl beszerzéséhez, és adja meg a "csomópontok" szakaszban hozzáadni kívánt új csomópont részleteit. Győződjön meg arról, hogy az új csomópont ugyanahhoz a csoportosan felügyelt fiókhoz tartozik. Ennek a fióknak az összes gépen rendszergazdának kell lennie.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) parancsot.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade) t futtatja

### <a name="add-node-types-to-your-cluster"></a>Csomópont-típusok hozzáadása a fürthöz
Új csomópont-típus hozzáadásához módosítsa úgy a konfigurációt, hogy az tartalmazza az új csomópont típusát a "tulajdonságok" területen található "NodeTypes" szakaszban, majd kezdjen el egy konfigurációs frissítést a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)használatával. Ha a frissítés befejeződött, új csomópontokat adhat hozzá a fürthöz ehhez a csomópont-típushoz.

## <a name="remove-nodes-from-your-cluster"></a>Csomópontok eltávolítása a fürtből
A csomópontokat a következő módon távolíthatja el egy fürtből a fürtből a konfiguráció frissítésével:

1. Futtassa a [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) parancsot a legújabb konfigurációs fájl beszerzéséhez, és *távolítsa el* a csomópontot a "csomópontok" szakaszból.
Adja hozzá a "NodesToBeRemoved" paramétert a "Setup" szakaszhoz a "FabricSettings" szakaszon belül. A "value" értéknek vesszővel tagolt listának kell lennie, amelyeknek el kell távolítania a csomópontok nevét.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. A frissítés megkezdéséhez futtassa a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) parancsot.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Service Fabric Exploreron figyelheti a frissítés előrehaladását. Azt is megteheti, [hogy a Get-ServiceFabricClusterUpgrade-](/powershell/module/servicefabric/get-servicefabricclusterupgrade)t futtatja.

> [!NOTE]
> A csomópontok eltávolítása több frissítést is kezdeményezhet. Egyes csomópontok címkével vannak megjelölve `IsSeedNode=”true”` , és a fürt jegyzékfájljának a használatával történő lekérdezésével azonosíthatók `Get-ServiceFabricClusterManifest` . Az ilyen csomópontok eltávolítása több időt is igénybe vehet, mivel a vetőmag-csomópontokat ilyen helyzetekben kell áthelyezni. A fürtnek legalább 3 elsődleges csomópont típusú csomópontot kell fenntartania.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Csomópont-típusok eltávolítása a fürtből
A csomópont típusának eltávolítása előtt ellenőrizze, hogy vannak-e olyan csomópontok, amelyek hivatkoznak a csomópont típusára. Távolítsa el ezeket a csomópontokat, mielőtt eltávolítja a megfelelő csomópont-típust. Az összes kapcsolódó csomópont eltávolítása után eltávolíthatja a NodeType a fürt konfigurációjától, és megkezdheti a konfiguráció frissítését a [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)használatával.


### <a name="replace-primary-nodes-of-your-cluster"></a>A fürt elsődleges csomópontjainak cseréje
Az elsődleges csomópontok cseréjét egy másik csomópont után kell végrehajtani, a kötegek eltávolítása és hozzáadása helyett.


## <a name="next-steps"></a>Következő lépések
* [Önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Önálló fürt biztonságossá tétele a Windowsban X509-tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md)
* [Önálló Service Fabric-fürt létrehozása Windows rendszerű Azure-beli virtuális gépekkel](./service-fabric-cluster-creation-via-arm.md)
