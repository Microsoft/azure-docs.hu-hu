---
title: A Windows operációs rendszer javítása a Service Fabric-fürtben
description: Ez a cikk azt ismerteti, hogyan automatizálható az operációs rendszerek javítása egy Service Fabric-fürtön a javítás-előkészítési alkalmazás használatával.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: e51b247f8c1a5a9ed8f6ec8e24363015afb2f7de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614411"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>A Windows operációs rendszer javítása a Service Fabric-fürtben

## <a name="automatic-os-image-upgrades"></a>Operációs rendszer rendszerképének automatikus frissítése

Az operációs rendszer javításának az Azure-ban való megtartásához az ajánlott eljárás az, hogy automatikusan Virtual Machine Scale Sets az operációsrendszer- [képek frissítését](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) . A virtuálisgép-méretezési csoporton alapuló automatikus operációsrendszer-képek frissítése a méretezési csoportokon ezüst vagy nagyobb tartósságot igényel.

Az operációs rendszer rendszerképének automatikus frissítésére vonatkozó követelmények Virtual Machine Scale Sets szerint
-   Service Fabric [tartóssági szint](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Silver vagy Gold, és nem bronz.
-   A méretezési csoport modelljének definíciójában a Service Fabric-bővítménynek TypeHandlerVersion 1,1 vagy újabb értékűnek kell lennie.
-   A tartóssági szintnek azonosnak kell lennie a Service Fabric-fürtön, és Service Fabric bővítményt a méretezési csoport modelljének definíciójában.
- Nem szükséges egy további állapot-mintavétel vagy az alkalmazás-állapot kiterjesztésének használata Virtual Machine Scale Setshoz.

Győződjön meg arról, hogy a tartóssági beállítások nem egyeznek meg a Service Fabric-fürtön, és Service Fabric a bővítményt, mivel az eltérés a frissítési hibákat eredményezi. A tartóssági szintek az [ezen az oldalon](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)vázolt irányelvek szerint módosíthatók.

A bronz tartóssággal az operációs rendszer rendszerképének automatikus frissítése nem érhető el. A [patch](#patch-orchestration-application ) -összehangoló alkalmazás (csak a nem Azure-beli üzemeltetett fürtök esetében) *nem ajánlott* ezüst vagy nagyobb tartóssági szint esetén, ezért az egyetlen lehetőség a Windows-frissítések automatizálására Service Fabric frissítési tartományok tekintetében.

> [!IMPORTANT]
> A virtuális gépeken történő verziófrissítések, ahol a "Windows Update" az operációsrendszer-lemez cseréje nélkül az operációs rendszerre vonatkozó javításokat alkalmaz, az Azure Service Fabric nem támogatottak.

Két lépés szükséges ahhoz, hogy a szolgáltatás a letiltott Windows Update megfelelően legyen engedélyezve az operációs rendszeren.

1. Az operációsrendszer-rendszerkép automatikus frissítésének engedélyezése, a Windows Updates ARM letiltása 
    ```json
    "virtualMachineProfile": { 
        "properties": {
          "upgradePolicy": {
            "automaticOSUpgradePolicy": {
              "enableAutomaticOSUpgrade":  true
            }
          }
        }
      }
    ```
    
    ```json
    "virtualMachineProfile": { 
        "osProfile": { 
            "windowsConfiguration": { 
                "enableAutomaticUpdates": false 
            }
        }
    }
    ```

    Azure PowerShell
    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -AutomaticOSUpgrade $true -EnableAutomaticUpdate $false
    ``` 
    
1. A méretezési csoport modell frissítése a konfiguráció módosítása után a méretezési csoport modelljének frissítéséhez szükség van az összes gép rendszerképének módosítására, hogy a módosítás érvénybe lépjen.
    
    Azure PowerShell
    ```azurepowershell-interactive
    $scaleSet = Get-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName
    $instances = foreach($vm in $scaleSet)
    {
        Set-AzVmssVM -ResourceGroupName $resourceGroupName -VMScaleSetName $scaleSetName -InstanceId $vm.InstanceID -Reimage
    }
    ``` 
    
További útmutatásért tekintse meg [Virtual Machine Scale sets automatikus operációsrendszer-rendszerkép frissítéseit](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) .

## <a name="patch-orchestration-application"></a>Javítás-előkészítési alkalmazás

> [!IMPORTANT]
> 2019. április 30-ig a patch-előkészítési alkalmazás 1,2. * verziója már nem támogatott. Ügyeljen arra, hogy a legújabb verzióra frissítsen.

A javítási előkészítési alkalmazás (POA) az Azure Service Fabric javításkezelő szolgáltatás egyik burkolója, amely lehetővé teszi a konfiguráció-alapú operációsrendszer-javítások ütemezését a nem Azure-beli üzemeltetett fürtökhöz. A POA nem szükséges a nem Azure-beli üzemeltetett fürtökhöz, de a frissítési tartományon belüli javítás telepítésének ütemezése szükséges ahhoz, hogy leállást ne kelljen kijavítani Service Fabric-fürtöt.

A POA egy Service Fabric alkalmazás, amely az operációs rendszer javítását automatizálja egy Service Fabric-fürtön anélkül, hogy leállás lenne.

A POA a következő funkciókat biztosítja:

- Az **operációs rendszer automatikus frissítésének telepítése**. Az operációs rendszer frissítéseinek letöltése és telepítése automatikusan megtörténik. A fürtcsomópontok szükség szerint újraindulnak a fürt leállásának hiánya nélkül.

- **Fürtöket támogató javítások és állapot-integráció**. Amíg a POA a frissítéseket alkalmazza, a a fürtcsomópontok állapotát figyeli. A fürtcsomópontok egyszerre egy csomópontot vagy egy frissítési tartományt frissítenek. Ha a fürt állapota a javítási folyamat miatt leáll, a javítás leáll a probléma súlyosbodása érdekében.

## <a name="internal-details-of-poa"></a>A POA belső adatai

A POA a következő alösszetevőkből áll:

- **Koordinátori szolgáltatás**: ez az állapot-nyilvántartó szolgáltatás a következőkért felelős:
    - A Windows Update feladatok koordinálása a teljes fürtön.
    - A befejezett Windows Update műveletek eredményének tárolása.

- **Node Agent szolgáltatás**: ez az állapot nélküli szolgáltatás minden Service Fabric fürtcsomóponton fut. A szolgáltatás feladatai a következők:
    - A csomópont-ügynök NTService elindításához.
    - A csomópont-ügynök NTService figyelése.

- **Node Agent NTService**: Ez a Windows NT-szolgáltatás magasabb szintű jogosultsággal (rendszer) fut. Ezzel szemben a Node Agent szolgáltatás és a koordinátor szolgáltatás alacsonyabb szintű jogosultsággal (hálózati szolgáltatás) fut. A szolgáltatás felelős a következő Windows Update feladatok végrehajtásához az összes fürtcsomóponton:
    - Az automatikus Windows-frissítések letiltása a csomóponton.
    - Windows-frissítések letöltése és telepítése a felhasználó által megadott szabályzat alapján.
    - A gép újraindítása a Windows-frissítések telepítése után.
    - A Windows-frissítések eredményeinek feltöltése a koordinátori szolgáltatásba.
    - Jelentési állapot-jelentések, ha egy művelet meghiúsult, miután az összes újrapróbálkozást kimeríti.

> [!NOTE]
> A POA a Service Fabric javításkezelő szolgáltatást használja a csomópont letiltásához vagy engedélyezéséhez, illetve az állapot-ellenőrzés elvégzéséhez. A POA által létrehozott javítási feladat nyomon követi az egyes csomópontok Windows Update folyamatát.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A .NET-keretrendszer szükséges minimális verziója 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A javításkezelő szolgáltatás engedélyezése (ha már nem fut)

A POA megköveteli, hogy a javításkezelő szolgáltatás engedélyezve legyen a fürtön.

#### <a name="azure-clusters"></a>Azure-fürtök

Az ezüst tartóssági szinten található Azure-fürtökön alapértelmezés szerint engedélyezve van a javításkezelő szolgáltatás. Az arany tartóssági szinten lévő Azure-fürtök esetében előfordulhat, hogy a fürt létrehozásakor a javításkezelő szolgáltatás engedélyezve van. A bronz tartóssági szinten lévő Azure-fürtök esetében alapértelmezés szerint nincs engedélyezve a javításkezelő szolgáltatás. Ha a szolgáltatás már engedélyezve van, a Service Fabric Explorer-ben a rendszerszolgáltatások szakaszban láthatja a rendszert.

##### <a name="the-azure-portal"></a>Azure Portal
A fürt beállításakor engedélyezheti javításkezelő a Azure Portal. A fürt konfigurálásakor jelölje be a **javításkezelő belefoglalása** lehetőséget a **kiegészítő funkciók** területen.

![A javításkezelő engedélyezésének képe a Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>A Azure Resource Manager üzembe helyezési modell
Azt is megteheti, hogy a [Azure Resource Manager üzembe helyezési modell](./service-fabric-cluster-creation-via-arm.md) segítségével engedélyezi a javításkezelő szolgáltatást az új és a meglévő Service Fabric-fürtökön. Szerezze be a telepíteni kívánt fürt sablonját. Használhatja a minta sablonokat, vagy létrehozhat egy egyéni Azure Resource Manager telepítési modell sablonját. 

A javításkezelő szolgáltatás a [Azure Resource Manager telepítési modell sablon](./service-fabric-cluster-creation-via-arm.md)használatával történő engedélyezéséhez tegye a következőket:

1. Győződjön meg arról, hogy `apiVersion` a értéke *2017-07-01 – előzetes* verzió a *Microsoft. ServiceFabric/Clusters* erőforráshoz. Ha ez különbözik, frissítenie kell `apiVersion` a *2017-07-01 – Preview* vagy újabb verzióra:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. A javításkezelő szolgáltatás engedélyezéséhez adja hozzá a következő `addonFeatures` szakaszt a `fabricSettings` szakasz után:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Miután frissítette a fürtözött sablont ezekkel a módosításokkal, alkalmazza őket, és hagyja befejezni a frissítést. Most már megtekintheti a fürtben futó javításkezelő szolgáltatást. Ezt a *háló:/System/RepairManagerService* nevű rendszer a Service Fabric Explorer rendszerszolgáltatások szakaszában találja. 

### <a name="standalone-on-premises-clusters"></a>Önálló helyszíni fürtök

Az javításkezelő szolgáltatás új vagy meglévő Service Fabric-fürtön való engedélyezéséhez használhatja a [különálló Windows-fürt konfigurációs beállításait](./service-fabric-cluster-manifest.md).

A javításkezelő szolgáltatás engedélyezése:

1. Győződjön meg arról, hogy `apiVersion` az [általános fürtkonfiguráció](./service-fabric-cluster-manifest.md#general-cluster-configurations) *04-2017* vagy újabb értékre van állítva, ahogy az itt látható:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. A javításkezelő szolgáltatás engedélyezéséhez adja hozzá a következő `addonFeatures` szakaszt a `fabricSettings` szakasz után, ahogy az itt látható:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Frissítse a fürt jegyzékfájlját ezekkel a módosításokkal a frissített fürtcsomópont használatával [hozzon létre egy új fürtöt](./service-fabric-cluster-creation-for-windows-server.md) , vagy [frissítse a fürt konfigurációját](./service-fabric-cluster-upgrade-windows-server.md). 

   Miután a fürt frissítette a fürt jegyzékét, megtekintheti a fürtben futó javításkezelő szolgáltatást. Ennek neve *Fabric:/System/RepairManagerService*, és a Service Fabric Explorer rendszer rendszerszolgáltatások szakaszában található.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-frissítések konfigurálása az összes csomóponthoz

Az automatikus Windows-frissítések a rendelkezésre állás elvesztéséhez vezethetnek, mert több fürtcsomópont is újraindulhat egyszerre. A POA alapértelmezés szerint megkísérli letiltani az automatikus Windows-frissítéseket az egyes fürtcsomópontokon. Ha azonban a beállításokat egy rendszergazda vagy egy Csoportházirend felügyeli, javasoljuk, hogy a Windows Update szabályzatot explicit módon "értesítés a letöltés előtt" beállítással állítsa be.

## <a name="download-the-application-package"></a>Alkalmazáscsomag letöltése

Az alkalmazáscsomag letöltéséhez nyissa meg a javítás-előkészítési [alkalmazás kiadása lapot](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) a githubon.

## <a name="configure-poa-behavior"></a>A POA viselkedés konfigurálása

A POA viselkedést az igényeinek megfelelően konfigurálhatja. Az alkalmazás létrehozásakor vagy frissítésekor az alkalmazás paraméterének megadásával felülbírálhatja az alapértelmezett értékeket. Az alkalmazás paramétereinek megadásához adja meg `ApplicationParameter` a `Start-ServiceFabricApplicationUpgrade` vagy a `New-ServiceFabricApplication` parancsmagot.

| Paraméter        | Típus                          | Részletek |
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | A gyorsítótárazható Windows Update eredmények maximális száma. <br><br>Az alapértelmezett érték 3000, feltéve, hogy: <br> &nbsp;&nbsp;– A csomópontok száma 20. <br> &nbsp;&nbsp;– A csomópontok havonta történő frissítéseinek száma 5. <br> &nbsp;&nbsp;– A másodpercenkénti eredmények száma 10 lehet. <br> &nbsp;&nbsp;– Az elmúlt három hónap eredményeit tárolni kell. |
|TaskApprovalPolicy   |Enumeráció <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy azt a házirendet jelzi, amelyet a koordinátori szolgáltatás a Windows-frissítések a Service Fabric-fürt csomópontjain való telepítéséhez használ.<br><br>Az engedélyezett értékek a következők: <br>*NodeWise*: a Windows-frissítések egyszerre egy csomópontot telepítenek. <br> *UpgradeDomainWise*: a Windows-frissítések egyszerre egy frissítési tartományt telepítenek. (A legtöbb frissítési tartományhoz tartozó csomópont a Windows Update számára is elérhető.)<br><br> Annak eldöntéséhez, hogy melyik házirend a legmegfelelőbb a fürthöz, tekintse meg a [Gyakori kérdések](#frequently-asked-questions) szakaszt.
|LogsDiskQuotaInMB   |Hosszú  <br> (Alapértelmezett: *1024*)               | A javítás-előkészítési alkalmazás maximális mérete MB-ban, amely helyileg tárolható a csomópontokon.
| WUQuery               | sztring<br>(Alapértelmezett: *IsInstalled = 0*)                | Lekérdezés a Windows-frissítések beszerzéséhez. További információ: [WuQuery.](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search)
| InstallWindowsOSOnlyUpdates | *Logikai* <br> (alapértelmezett: hamis)                 | Ezzel a jelzővel szabályozhatja, hogy mely frissítéseket kell letölteni és telepíteni. A következő értékek engedélyezettek <br>True – csak a Windows operációs rendszer frissítéseinek telepítése.<br>false (hamis) – telepíti az összes elérhető frissítést a gépre.          |
| WUOperationTimeOutInMinutes | Int <br>(Alapértelmezett: *90*)                   | Megadja a Windows Update művelet időtúllépését (keresés vagy letöltés vagy telepítés). Ha a művelet nem fejeződött be a megadott időkorláton belül, a rendszer megszakítja a műveletet.       |
| WURescheduleCount     | Int <br> (Alapértelmezett: *5*)                  | A szolgáltatás által a Windows Update újraütemezett maximális száma, ha egy művelet tartósan leáll.          |
| WURescheduleTimeInMinutes | Int <br>(Alapértelmezés: *30*) | Az az időtartam, amikor a szolgáltatás átütemezze a Windows-frissítéseket, ha a hiba továbbra is fennáll. |
| WUFrequency           | Vesszővel tagolt karakterlánc (alapértelmezett: *hetente, szerda, 7:00:00*)     | A Windows-frissítések telepítésének gyakorisága. A formátum és a lehetséges értékek a következők: <br>-Havonta, NN, óó: PP: SS (példa: *havonta, 5, 12:22:32*). A _dd_ (nap) mező megengedett értékei _1 és 28_ közötti számok. <br>-Hetente, naponta, óó: PP: SS (példa: *hetente, kedd, 12:22:32*)  <br>– Naponta, óó: PP: SS (példa: *napi, 12:22:32*)  <br>– Hét, nap, óó: PP: mm (példa: *2, péntek, 21:00:00,* 9:00 PM UTC a második hét minden hónapjának pénteken) <br>- A *none* érték azt jelzi, hogy a Windows-frissítések nem lesznek végrehajtva.  <br><br> Az időpontok UTC-ben vannak.|
| AcceptWindowsUpdateEula | Logikai <br>(Alapértelmezett: *true*) | A jelző beállításával az alkalmazás elfogadja a számítógép tulajdonosának nevében Windows Update End-User licencszerződést.              |

> [!TIP]
> Ha azt szeretné, hogy a Windows-frissítések azonnal megtörténjenek, állítsa `WUFrequency` az alkalmazás központi telepítésének időpontjához képest. Tegyük fel például, hogy rendelkezik egy öt csomópontos tesztelési fürttel, és tervezi az alkalmazás üzembe helyezését a 5:00 PM UTC-kor. Ha azt feltételezi, hogy az alkalmazás frissítése vagy üzembe helyezése legfeljebb 30 percet vesz igénybe, állítsa a WUFrequency *naponta, 17:30:00-* re.

## <a name="deploy-poa"></a>A cselekvési terv üzembe helyezése

1. Fejezze be az összes előfeltételként szükséges lépést a fürt előkészítéséhez.
1. A POA üzembe helyezése, mint bármely más Service Fabric alkalmazás. A PowerShell használatával történő telepítéséhez tekintse meg az [alkalmazások telepítése és eltávolítása a PowerShell használatával](./service-fabric-deploy-remove-applications.md)című témakört.
1. Az alkalmazás az üzembe helyezés időpontjában történő konfigurálásához adja át a parancsot a `ApplicationParameter` `New-ServiceFabricApplication` parancsmagnak. Az Ön kényelme érdekében a szkript Deploy.ps1 az alkalmazással együtt. A szkript használata:

    - Kapcsolódjon Service Fabric-fürthöz a használatával `Connect-ServiceFabricCluster` .
    - Futtassa a PowerShell-parancsfájlt Deploy.ps1 a megfelelő `ApplicationParameter` értékkel.

> [!NOTE]
> Tartsa meg a parancsfájlt és az alkalmazás mappáját a *PatchOrchestrationApplication* ugyanabban a címtárban.

## <a name="upgrade-poa"></a>A cselekvési terv frissítése

A POA-verzió PowerShell használatával történő frissítéséhez kövesse a [Service Fabric alkalmazás frissítése a PowerShell használatával](./service-fabric-application-upgrade-tutorial-powershell.md)című témakör utasításait.

## <a name="remove-poa"></a>POA eltávolítása

Az alkalmazás eltávolításához kövesse az [alkalmazások telepítése és eltávolítása a PowerShell használatával](./service-fabric-deploy-remove-applications.md)című témakör utasításait.

Az Ön kényelme érdekében a Undeploy.ps1 szkriptet az alkalmazással együtt adtuk meg. A szkript használata:

  - Kapcsolódjon Service Fabric-fürthöz a használatával ```Connect-ServiceFabricCluster``` .
  - Futtassa a PowerShell-parancsfájlt Undeploy.ps1.

> [!NOTE]
> Tartsa meg a parancsfájlt és az alkalmazás mappáját a *PatchOrchestrationApplication* ugyanabban a címtárban.

## <a name="view-the-windows-update-results"></a>A Windows Update eredményeinek megtekintése

A POA a REST API-kat teszi elérhetővé a felhasználók korábbi eredményeinek megjelenítéséhez. Íme egy példa az eredmény JSON-ra:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

A JSON-mezőket a következő táblázat ismerteti:

Mező | Értékek | Részletek
-- | -- | --
Operationresult tevékenységen | 0 – sikeres<br> 1 – sikeres hibák<br> 2 – sikertelen<br> 3 – megszakítva<br> 4 – időtúllépés miatt megszakítva | A teljes művelet eredményét jelzi, amely általában egy vagy több frissítés telepítését jelenti.
ResultCode | Ugyanaz, mint a Operationresult tevékenységen | Ez a mező egy egyedi frissítés telepítési műveletének eredményét jelzi.
OperationType | 1 – telepítés<br> 0 – keresés és letöltés| Alapértelmezés szerint a telepítés az egyetlen OperationType, amely az eredmények között látható.
WindowsUpdateQuery | Az alapértelmezett érték a "IsInstalled = 0" | A frissítések kereséséhez használt Windows Update-lekérdezés. További információ: [WuQuery](/windows/win32/api/wuapi/nf-wuapi-iupdatesearcher-search).
RebootRequired | igaz – újraindítás szükséges<br> hamis – újraindítás nem szükséges | Azt jelzi, hogy szükséges-e újraindítás a frissítések telepítésének befejezéséhez.
OperationStartTime | DateTime | A művelet (Letöltés/telepítés) elindításának időpontját jelzi.
OperationTime | DateTime | Azt jelzi, hogy mikor fejeződött be a művelet (Letöltés/telepítés).
HResult | 0 – sikeres<br> egyéb – hiba| A "7392acaf-6a85-427c-8a8d-058c25beb0d6" updateID rendelkező Windows Update hibájának okát jelzi.

Ha még nincs ütemezve frissítés, az eredmény JSON üres.

Windows Update eredmények lekérdezéséhez jelentkezzen be a fürtbe. Keresse meg a koordinátori szolgáltatás elsődleges címének replika IP-címét, és nyissa meg a következő URL-címet a böngészőből: http:// &lt; replika-IP &gt; : &lt; ApplicationPort &gt; /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

A koordinátori szolgáltatás REST-végpontja dinamikus porttal rendelkezik. A pontos URL-cím megadásához tekintse meg a Service Fabric Explorer. Az eredmények például a következő címen érhetők el: *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults* .

![A REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Ha a fordított proxy engedélyezve van a fürtön, akkor a fürtön kívülről is elérheti az URL-címet.

Az elérni kívánt végpont *http:// &lt; SERVERURL &gt; : &lt; REVERSEPROXYPORT &gt; /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

A fordított proxynak a fürtön való engedélyezéséhez kövesse az [Azure Service Fabric fordított proxy](./service-fabric-reverseproxy.md)utasításait. 

> 
> [!WARNING]
> A fordított proxy konfigurálása után a fürtben lévő összes olyan szolgáltatás, amely egy HTTP-végpontot tesz elérhetővé, a fürtön kívülről címezhető.

## <a name="diagnostics-and-health-events"></a>Diagnosztikai és állapot-események

Ez a szakasz azt ismerteti, hogyan lehet hibakeresést végezni, illetve diagnosztizálni a javítási frissítésekkel kapcsolatos problémákat Service Fabric fürtökön a POA használatával.

> [!NOTE]
> Ahhoz, hogy a következő kinevezett, öndiagnosztika funkciók közül sokat megszerezzen, a POA Version 1.4.0 vagy újabb verziójára lesz szükség.

A csomópont-ügynök NTService a frissítések telepítésére szolgáló [javítási feladatokat](/dotnet/api/system.fabric.repair.repairtask) hoz létre a csomópontokon. Ezután a koordinátori szolgáltatás előkészíti a feladatokat a feladat-jóváhagyási házirend szerint. Végezetül javításkezelő jóváhagyja az előkészített feladatokat, ami nem hagy jóvá semmilyen feladatot, ha a fürt nem kifogástalan állapotú. 

Ha meg szeretné ismerni, hogy a frissítések hogyan folytatódnak egy adott csomóponton, ugorjon a lépésről lépésre:

1. Az összes csomóponton futó NodeAgentNTService az elérhető Windows-frissítéseket az ütemezett időpontban keresi. Ha a frissítések elérhetők, letölti őket a csomóponton.

1. A frissítések letöltése után a Node Agent NTService létrehoz egy megfelelő javítási feladatot a (z) *POS___ \<unique_id>* nevű csomóponthoz. Ezeket a javítási feladatokat a [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask) parancsmag használatával vagy az SFX használatával tekintheti meg a csomópont részletei szakaszban. A javítási feladat létrehozása után a rendszer gyorsan áthelyezi az [ *igényelt* állapotba](/dotnet/api/system.fabric.repair.repairtaskstate).

1. A koordinátori szolgáltatás rendszeresen megkeresi a *kért* állapotú javítási feladatokat, majd frissíti őket a TaskApprovalPolicy alapján történő *előkészítésre* . Ha a TaskApprovalPolicy úgy van konfigurálva, hogy NodeWise, a csomópontnak megfelelő javítási feladat csak akkor készüljön fel, ha nincs más javítási feladat jelenleg *előkészítési*, *jóváhagyási*, *végrehajtási* vagy *visszaállítási* állapotban. 

   Hasonlóképpen, a UpgradeWise TaskApprovalPolicy esetében az előző állapotokban csak az azonos frissítési tartományhoz tartozó csomópontok vannak feladatai. Ha a javítási feladatot áthelyezi az *előkészítési* állapotba, a megfelelő Service Fabric csomópont [le lesz tiltva](/powershell/module/servicefabric/disable-servicefabricnode) az *Újraindítás* beállítással.

   A POA-verziók 1.4.0 és újabb eseményeket tesznek közzé a CoordinatorService ClusterPatchingStatus tulajdonságával a javított csomópontok megjelenítéséhez. A frissítések a _poanode_0ra vannak telepítve, ahogy az az alábbi képen is látható:

    [![A fürt javítási állapotának képe](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. A csomópont letiltása után a rendszer áthelyezi a javítási feladatot a *végrehajtás* állapotba. 
   
   > [!NOTE]
   > Egy *letiltott* állapotú csomópont blokkolhat egy új javítási feladatot, amely leállítja a javítás műveletet a fürtön.

1. Ha a javítási feladat *végrehajtási* állapotban van, a rendszer megkezdi a javítás telepítését a csomóponton. A javítás telepítését követően előfordulhat, hogy a rendszer nem indítja újra a csomópontot a javítástól függően. Ezután a javítási feladat átkerül a *visszaállítási* állapotba, amely újraengedélyezi a csomópontot. A javítási feladat ezután befejezettként van megjelölve.

   A POA-verziók 1.4.0 és újabb verzióiban megtekintheti a frissítés állapotát, ha megtekinti az NodeAgentService és a WUOperationStatus-Property állapotú eseményeket \<NodeName> . A következő képek Kiemelt fejezetei a Windows-frissítések állapotát jelenítik meg a csomópontokon *poanode_0* és *poanode_2*:

   [![A képernyőképen látható Windows Update műveleti állapot konzoljának ablaka poanode_0 kiemelve.](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![A képernyőképen látható Windows Update műveleti állapot konzoljának ablaka poanode_1 kiemelve.](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   A részleteket a PowerShell használatával is lekérheti. Ehhez kapcsolódjon a fürthöz, és kérje le a javítási feladat állapotát a [Get-ServiceFabricRepairTask](/powershell/module/servicefabric/get-servicefabricrepairtask)használatával. 
   
   A következő példában a "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" feladat *DownloadComplete* állapotban van. Ez azt jelenti, hogy a frissítések le lettek töltve a *poanode_2* csomóponton, és a telepítés a művelet *végrehajtásakor* történik.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Ha további problémák is megtalálhatók, jelentkezzen be a virtuális gépre vagy virtuális gépekre, és ismerkedjen meg velük a Windows-eseménynaplók használatával. A korábban említett javítási feladat csak a következő végrehajtó alállapotokban létezhet:

      ExecutorSubState | Leírás
    -- | -- 
      Nincs = 1 |  Azt jelenti, hogy nem volt folyamatban művelet a csomóponton. Lehet, hogy az állapot átmeneti állapotban van.
      DownloadCompleted = 2 | Azt jelenti, hogy a letöltési művelet sikerrel, részleges meghibásodással vagy meghibásodással fejeződött be.
      InstallationApproved = 3 | Azt jelenti, hogy a letöltési művelet korábban fejeződött be, és javításkezelő jóváhagyta a telepítést.
      InstallationInProgress = 4 | Megfelel a javítási feladat végrehajtásának állapotának.
      InstallationCompleted = 5 | Azt jelenti, hogy a telepítés sikeres, részleges sikerrel vagy hibával fejeződött be.
      RestartRequested = 6 | Azt jelenti, hogy a javítás telepítése befejeződött, és a csomóponton folyamatban van egy újraindítási művelet.
      RestartNotNeeded = 7 |  Azt jelenti, hogy a javítás telepítésének befejeződése után nem volt szükség az újraindításra.
      RestartCompleted = 8 | Azt jelzi, hogy az újraindítás sikeresen befejeződött.
      OperationCompleted = 9 | A Windows Update művelet sikeresen befejeződött.
      OperationAborted = 10 | Azt jelenti, hogy a Windows Update művelet meg lett szakítva.

1. A POA-verziók 1.4.0 és újabb verzióiban a csomópont-frissítési kísérlet befejeződése után egy "WUOperationStatus-[csomópontnév]" tulajdonsággal rendelkező esemény jelenik meg a NodeAgentService, amely értesíti a felhasználót, ha a Windows-frissítések letöltésére és telepítésére vonatkozó következő kísérlet megkezdődik. Ez az alábbi képen látható:

     [![A képernyőképen látható a konzol ablaka Windows Update műveleti állapot a NodeAgentService.](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Diagnosztikai naplók

A javítási előkészítési alkalmazások naplói a Service Fabric futtatókörnyezeti naplók részeként lesznek összegyűjtve.

A naplókat a választott diagnosztikai eszköz vagy folyamat használatával rögzítheti. A POA a következő rögzített szolgáltatói azonosítókat használja az események naplózásához az [eseményforrás](/dotnet/api/system.diagnostics.tracing.eventsource)használatával:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Állapotjelentés

A POA a következő helyzetekben is közzéteszi a Node Agent szolgáltatás vagy a koordinátor szolgáltatás állapotát:

* A csomópont-ügynök NTService nem érhető el

   Ha a csomópont-ügynök NTService leállt egy csomóponton, a rendszer figyelmeztetési szintű állapotjelentést hoz létre a csomópont-ügynök szolgáltatással szemben.

* A javításkezelő szolgáltatás nincs engedélyezve

   Ha a javításkezelő szolgáltatás nem található a fürtön, a rendszer figyelmeztetési szintű állapotjelentést hoz létre a koordinátori szolgáltatáshoz.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért jelenik meg a fürt hibás állapotú, ha a POA fut?**

Válasz: a telepítési folyamat során a POA letiltja vagy újraindítja a csomópontokat, ami átmenetileg egy nem kifogástalan állapotú fürtöt eredményezhet.

Az alkalmazás házirendjétől függően akár egy csomópont is lemehet a javítási művelet során, *vagy* egy teljes frissítési tartomány egyszerre is lemehet.

A Windows-frissítések telepítésének végén a csomópontok újraindítást követően ismét engedélyezve lesznek.

A következő példában a fürt átmenetileg hibás állapotba került, mert két csomópont leállt, és a MaxPercentageUnhealthyNodes szabályzat megsértése megsértette. A hiba ideiglenes, amíg a javítási művelet megkezdődhet.

![Nem kifogástalan állapotú fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma továbbra is fennáll, tekintse meg a hibaelhárítás szakaszt.

**K: Mit tehetek, ha a POA figyelmeztetési állapotban van?**

A: Ellenőrizze, hogy az alkalmazáson közzétett állapotjelentés a kiváltó okot jelzi-e. A figyelmeztetés általában a probléma részleteit tartalmazza. Ha a probléma átmeneti, a rendszer automatikusan helyreállítja az alkalmazást.

**K: Mit tehetek, ha a fürtem nem kifogástalan állapotú, és sürgős operációs rendszerre van szükségem?**

A: a POA nem telepíti a frissítéseket, amíg a fürt állapota nem megfelelő. Próbálja meg a fürtöt Kifogástalan állapotba hozni, hogy feloldja a POA-munkafolyamatot.

**K: be kell-e állítani a TaskApprovalPolicy "NodeWise" vagy "UpgradeDomainWise" értékre a fürthöz?**

A: a "UpgradeDomainWise" beállítás felgyorsítja a fürt teljes javítását a frissítéssel párhuzamosan, a frissítési tartományhoz tartozó összes csomóponton. A folyamat során a teljes frissítési tartományhoz tartozó csomópontok nem érhetők el ( [ *letiltott* állapotban](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabled)).

Ezzel szemben a "NodeWise" beállítás egyszerre csak egy csomópontot javít, ami azt jelenti, hogy a fürt teljes javítása hosszabb időt is igénybe vehet. A javítási folyamat során azonban csak egyetlen csomópont nem érhető el ( *letiltott* állapotban).

Ha a fürt a javítási ciklus során N-1 számú frissítési tartományon fut, akkor a (z) N érték a fürt frissítési tartományának teljes számát jelöli, a szabályzatot pedig "UpgradeDomainWise" értékre állíthatja. Ellenkező esetben állítsa "NodeWise" értékre.

**K: mennyi időt kell igénybe venni a csomópontok javításához?**

Válasz: a csomópontok javítását percek alatt (például a [Windows Defender definíciójának frissítésein](https://www.microsoft.com/en-us/wdsi/definitions)) is elvégezheti (például a [Windows összegző frissítéseit](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). A csomópontok javításához szükséges idő többnyire a következőktől függ: 
 - A frissítések mérete.
 - Azon frissítések száma, amelyeket egy javítási ablakban kell alkalmazni.
 - A frissítések telepítéséhez szükséges idő, indítsa újra a csomópontot (ha szükséges), majd fejezze be az újraindítás utáni telepítési lépéseket.
 - A virtuális gép vagy a gép teljesítménye, valamint a hálózati feltételek.

**K: mennyi ideig tart a teljes fürt javítása?**

A: a teljes fürt javításához szükséges idő a következőktől függ:

- A csomópontok javításához szükséges idő.

- A koordinátori szolgáltatás szabályzata. Az alapértelmezett "NodeWise" házirend egyszerre csak egy csomópont javítását eredményezi, ami lassabb, mint a "UpgradeDomainWise" használata. 

   Ha például egy csomópont ~ 1 órát vesz igénybe, a javításhoz egy 20 csomópontos (azonos típusú csomópont) fürtöt kell kijavítani 5 frissítési tartománnyal, amelyek mindegyike 4 csomópontot tartalmaz, a következőt igényli:
    - A "NodeWise" esetében: ~ 20 óra.
    - A "UpgradeDomainWise": ~ 5 óra.

- A fürt terhelése. Az egyes javítási műveletekhez az ügyfél munkaterhelésének áthelyezésére van szükség a fürt más elérhető csomópontjain. Az éppen javított csomópont [ *letiltási* állapotban](/dotnet/api/system.fabric.query.nodestatus#System_Fabric_Query_NodeStatus_Disabling) lenne ebben az időszakban. Ha a fürt a csúcs terhelése közelében fut, a letiltási folyamat hosszabb időt is igénybe veheti. Ezért előfordulhat, hogy a teljes javítási folyamat lassúnak tűnhet az ilyen feltett körülmények között.

- A fürtök állapotával kapcsolatos hibák a javítások során. A [fürt állapotának](./service-fabric-health-introduction.md) [romlása](/dotnet/api/system.fabric.health.healthstate#System_Fabric_Health_HealthState_Error) megszakítja a javítási folyamatot. Ezt a problémát a teljes fürt javításához szükséges teljes időre adja hozzá a rendszer.

**K: Miért látok néhány frissítést a Windows Update találatok között, amelyek REST APIn keresztül szerezhetők be, de nem a gép Windows Update előzményeiben?**

A: egyes Termékfrissítések csak a saját frissítési vagy javítási előzményeiben jelennek meg. Előfordulhat például, hogy a Windows Defender frissítései nem jelennek meg Windows Update előzményekben a Windows Server 2016 rendszeren.

**K: a fejlesztési fürt (egycsomópontos fürt) javításához is használható.**

A: nem, a POA nem használható egy egycsomópontos fürt javításához. Ez a korlátozás a tervezés szerint történik, mivel [Service Fabric rendszerszolgáltatások](./service-fabric-technical-overview.md#system-services) vagy más ügyfélalkalmazások állásidőt okozna. Ezért a javítási feladatok javítását soha nem hagyja jóvá javításkezelő.

**K: Hogyan a Linux rendszeren futó fürtcsomópontok javítását?**

A: Ha többet szeretne tudni a Linux-frissítések előkészítéséről, tekintse meg az Azure-beli [virtuálisgép-méretezési csoport automatikus operációsrendszer-rendszerképének frissítéseit](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

**K: milyen sokáig tart a frissítési ciklus?**

A: lekérdezés az eredmény JSON-hez, adja meg az összes csomópont frissítési ciklusát, majd próbálja meg megkeresni a frissítés telepítésének idejét az összes csomóponton a OperationStartTime és a OperationTime (OperationCompletionTime) használatával. 

Ha van olyan nagy időablak, amelyben nem zajlik frissítés, előfordulhat, hogy a fürt hibás állapotban van, és ezért javításkezelő nem hagyhat jóvá semmilyen POA javítási feladatot. Ha a frissítés telepítése bármely csomóponton hosszú időt vesz igénybe, előfordulhat, hogy a csomópont egy ideje nem frissült. Előfordulhat, hogy sok frissítés van függőben, ami késéseket eredményezhet. 

Előfordulhat, hogy a csomópont-javítások le vannak tiltva, mert a *letiltási* állapotban van. Ez általában azért történik, mert a csomópont letiltásával kvórumhoz vagy adatvesztéshez vezethet.

**K: Miért kell letiltani a csomópontot a POA javításakor?**

A: POA letiltja a csomópontot egy *Újraindítási* szándéktal, amely leállítja vagy újraosztja a csomóponton futó összes Service Fabric-szolgáltatást. A POA ezt biztosítja, hogy az alkalmazások ne használják fel az új és a régi DLL-eket, ezért azt javasoljuk, hogy tiltsa le a csomópontokat anélkül, hogy le kellene tiltani.

**K: hány csomópontot lehet frissíteni a POA használatával?**

A: a POA Service Fabric javításkezelő használatával hoz létre javítási feladatokat a csomópontokhoz a frissítésekhez. Ugyanakkor nem lehet több mint 250 javítási feladat egyszerre. A POA jelenleg az egyes csomópontok javítási feladatait egy időben hozza létre, így a POA legfeljebb 250 csomópontot tud frissíteni a fürtben. 

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- A POA elfogadja a End-User licencszerződését Windows Update a felhasználó nevében. Opcionálisan a beállítás kikapcsolható az alkalmazás konfigurációjában.

- A POA a használat és a teljesítmény nyomon követésére szolgáló telemetria gyűjt. Az alkalmazás telemetria követi a Service Fabric futtatókörnyezet telemetria beállítását (amely alapértelmezés szerint be van kapcsolva).

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz a javítási csomópontokkal kapcsolatos problémák megoldásához nyújt lehetséges hibaelhárítási megoldásokat.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Egy csomópont nem jön vissza állapotba

* Előfordulhat, hogy a csomópont *letiltási* állapotban van, mert:

  - A biztonsági ellenőrzések függőben vannak. A helyzet orvoslása érdekében győződjön meg arról, hogy elegendő csomópont áll rendelkezésre kifogástalan állapotban.

* Előfordulhat, hogy a csomópont *letiltott állapotban van* , mert:

  - A rendszer manuálisan letiltotta.
  - Egy folyamatban lévő Azure-infrastruktúra-feladatok miatt le lett tiltva.
  - Ezt a POA a csomópont javításához átmenetileg letiltotta.

* Előfordulhat, hogy a csomópont le van ragadva, mert:

  - A szolgáltatás manuálisan lett elhelyezve.
  - A rendszer újraindítást végez (amelyet a POA is aktiválhat).
  - Hibás virtuális géppel vagy géppel rendelkezik, vagy hálózati kapcsolati problémák léptek fel.

### <a name="updates-were-skipped-on-some-nodes"></a>Egyes csomópontokon a frissítések ki lettek hagyva

A POA megpróbál telepíteni egy Windows-frissítést az átütemezési házirendnek megfelelően. A szolgáltatás megkísérli helyreállítani a csomópontot, és az alkalmazás házirendjének megfelelően kihagyja a frissítést.

Ebben az esetben a rendszer figyelmeztetési szintű állapotjelentést hoz létre a csomópont-ügynök szolgáltatással kapcsolatban. A Windows Update eredmény a hiba lehetséges okát is tartalmazza.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A fürt állapota hiba miatt leáll a frissítés telepítésekor

Egy hibás Windows-frissítés egy adott csomóponton vagy frissítési tartományban lévő alkalmazás vagy fürt állapotát leállíthatja. POA felhagy minden további Windows Update műveletet, amíg a fürt újra Kifogástalan állapotba nem kerül.

A rendszergazdának beavatkoznia kell, és meg kell határoznia, hogy az alkalmazás vagy a fürt miért nem megfelelő állapotba került a Windows Update miatt.

## <a name="poa-release-notes"></a>POA kibocsátási megjegyzések

>[!NOTE]
> A POA-verziók 1.4.0 és újabb verzióiban megtalálhatja a kiadással kapcsolatos megjegyzéseket és kiadásokat a GitHubon a javítás-előkészítési [alkalmazás kiadás lapján](https://github.com/microsoft/Service-Fabric-POA/releases/) .

### <a name="version-110"></a>1.1.0-es verzió
- Nyilvános kiadás

### <a name="version-111"></a>1.1.1-es verzió
- Kijavítva egy olyan hibát a NodeAgentService SetupEntryPoint, amely meggátolta a NodeAgentNTService telepítését.

### <a name="version-120"></a>1.2.0 verziója

- Hibajavítások a rendszerindító munkafolyamatok köré.
- Hibajavítás az RM-feladatok létrehozásakor, mert a javítási feladatok előkészítése során felmerülő állapot-ellenőrzés nem a várt módon történt.
- A Windows-szolgáltatás POANodeSvc indítási üzemmódjának módosítása automatikusról késleltetve – automatikus.

### <a name="version-121"></a>1.2.1-es verzió

- Hibajavítás a fürt Felskálázási munkafolyamatában. A nem létező csomópontokhoz tartozó POA javítási feladatok esetében bevezette a Garbage-gyűjtési logikát.

### <a name="version-122"></a>1.2.2-es verzió

- Egyéb hibajavítások.
- A bináris fájlok most már alá vannak írva.
- Az alkalmazáshoz hozzáadott sfpkg-hivatkozás.

### <a name="version-130"></a>1.3.0 verziója

- A InstallWindowsOSOnlyUpdates beállítása hamis értékre most telepíti az összes elérhető frissítést.
- Módosult az automatikus frissítések letiltásának logikája. Ez egy olyan hibát javít, amelyben az automatikus frissítések nem voltak letiltva a 2016-es és újabb kiszolgálókon.
- Paraméteres elhelyezési megkötés a speciális használati esetekhez tartozó POA-szolgáltatások esetében is.

### <a name="version-131"></a>1.3.1-es verzió
- A regresszió javítása, ha a POA 1.3.0 nem fog működni a Windows Server 2012 R2 vagy korábbi verzióban, mert hiba történt az automatikus frissítések letiltásakor. 
- Kijavíthatja a hibát, hogy a InstallWindowsOSOnlyUpdates-konfiguráció mindig igaz legyen.
- A InstallWindowsOSOnlyUpdates alapértelmezett értékének hamisra váltása.

### <a name="version-132"></a>1.3.2-es verzió
- Olyan probléma elhárítása, amely hatással van a javítási életciklusra egy csomóponton, ha vannak olyan csomópontok, amelyek neve az aktuális csomópont nevének egy részhalmaza. Ilyen csomópontok esetén előfordulhat, hogy a javítás kimaradt, vagy függőben van egy újraindítás.
