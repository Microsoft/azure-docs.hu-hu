---
title: Operációsrendszer-rendszerkép automatikus frissítése azure-beli virtuálisgép-méretezési készletekkel
description: Ismerje meg, hogyan frissítheti automatikusan az operációsrendszer-rendszerképet egy méretezési készlet virtuálisgép-példányán
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: automatic-os-upgrade
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 047eab6cb90caa18362830c8c74656f76865a9ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762874"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure-beli virtuálisgép-méretezési csoport operációsrendszer-képének automatikus frissítései

Az operációsrendszer-lemezképek automatikus frissítésének a méretezési készleten való engedélyezése megkönnyíti a frissítéskezelést a méretezési készlet összes példánya operációsrendszer-lemezének biztonságos és automatikus frissítésével.

Az operációs rendszer automatikus frissítése a következő jellemzőkkel rendelkezik:

- A konfigurálás után a rendszer automatikusan alkalmazza a rendszerkép-közzétevők által közzétett legújabb operációsrendszer-képet a méretezési csoportra felhasználói beavatkozás nélkül.
- A példánykötegeket működés közben frissíti minden alkalommal, amikor a közzétevő közzétesz egy új rendszerképet.
- Integrálható az alkalmazás állapot-mintavételével és [az Application Health bővítővel.](virtual-machine-scale-sets-health-extension.md)
- Minden virtuálisgép-mérethez, valamint Windows- és Linux-rendszerképhez is használható.
- Az automatikus frissítéseket bármikor kikapcsolhatja (az operációsrendszer-frissítések manuálisan is kezdeményezhetők).
- A virtuális gép operációsrendszer-lemezét a rendszerkép legújabb verziójával létrehozott új operációsrendszer-lemez váltja fel. A rendszer futtatja a konfigurált bővítményeket és az egyéni adatszkprogramokat, miközben megőrzi a megőrzött adatlemezeket.
- [A bővítmény-szekvenálás](virtual-machine-scale-sets-extension-sequencing.md) támogatott.
- Az operációs rendszer rendszerképének automatikus frissítése bármilyen méretű méretezési csoporton engedélyezhető.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Hogyan működik az operációs rendszer rendszerképének automatikus frissítése?

A frissítés úgy működik, hogy a virtuális gép operációsrendszer-lemezét új lemezre cseréli, amelyet a legújabb rendszerképverzióval hoztak létre. A rendszer minden konfigurált bővítményt és egyéni adatszkprogramot futtat az operációsrendszer-lemezen, miközben megőrzi a megőrzött adatlemezeket. Az alkalmazás állásideének minimalizálása érdekében a frissítések kötegekben zajlnak, és a méretezési készletnek nem lehet mindig 20%-nál több frissítése. Egy alkalmazás állapot-mintavételét Azure Load Balancer alkalmazás állapot-mintavételét vagy [alkalmazás állapotbővítményét is integrálhatja.](virtual-machine-scale-sets-health-extension.md) Javasoljuk, hogy építsen be egy alkalmazás szívverését, és ellenőrizze a frissítési folyamat minden kötegének sikeres frissítését.

A frissítési folyamat a következőképpen működik:
1. A frissítési folyamat megkezdése előtt a vezénylési rendszer biztosítja, hogy a teljes méretezési készletben a példányok 20%-a ne legyen megfelelő (bármilyen okból).
2. A frissítési vezénylés azonosítja a frissíthető virtuálisgép-példányok kötegét, ahol minden köteg a példányok teljes darabszámának legfeljebb 20%-ával rendelkezik, egy virtuális gép minimális kötegméretének megfelelően.
3. A kiválasztott virtuálisgép-példánykötet operációsrendszer-lemezét a rendszer a legújabb rendszerképből létrehozott új operációsrendszer-lemezre cseréli. A méretezésikészlet-modellben megadott összes bővítmény és konfiguráció a frissített példányra lesz alkalmazva.
4. A konfigurált alkalmazás-állapot-mintavételekkel vagy Application Health bővítővel konfigurált méretezési készletek esetén a frissítés legfeljebb 5 percet vár, amíg a példány kifogástalanná válik, mielőtt továbblép a következő köteg frissítésére. Ha egy példány állapota a frissítés után 5 percen belül nem áll helyre, akkor a rendszer alapértelmezés szerint visszaállítja a példány előző operációsrendszer-lemezét.
5. A frissítési vezénylés nyomon követi a frissítés után nem megfelelővé vált példányok százalékos arányát is. A frissítés leáll, ha a frissített példányok több mint 20%-a nem megfelelő a frissítési folyamat során.
6. A fenti folyamat addig folytatódik, amíg a méretezési készletben az összes példány frissítése meg nem történt.

A méretezési készlet operációsrendszer-frissítési vezénylője minden köteg frissítése előtt ellenőrzi a méretezési készlet általános állapotát. A kötegek frissítése közben lehetnek más egyidejű tervezett vagy nem tervezett karbantartási tevékenységek is, amelyek hatással lehetnek a méretezésikészlet-példányok állapotára. Ilyen esetekben, ha a méretezési készlet példányainak több mint 20%-a nem megfelelő, akkor a méretezési készlet frissítése az aktuális köteg végén leáll.

> [!NOTE]
>Az operációs rendszer automatikus frissítése nem frissíti a méretezési készlet referencia-rendszerkép-termékváltozatát. A termékváltozat (például Az Ubuntu 16.04-LTS) 18.04-LTS-re való módosítása érdekében a méretezésikészlet-modellt közvetlenül a kívánt rendszerkép-termékváltozatra kell frissítenie. [](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) A rendszerkép-közzétevő és az ajánlat nem módosítható egy meglévő méretezési készletben.  

## <a name="supported-os-images"></a>Támogatott operációsrendszer-lemezképek
Jelenleg csak bizonyos operációsrendszer-platformrendszerképek támogatottak. Az egyéni [rendszerképek akkor támogatottak,](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) ha a méretezési csoport egyéni rendszerképeket használ a [Shared Image Gallery.](../virtual-machines/shared-image-galleries.md)

Jelenleg a következő platform-SKUS-k támogatottak (és rendszeresen bővül újabbak):

| Publisher               | Operációsrendszer-ajánlat      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Az operációs rendszer rendszerképének automatikus frissítésének konfigurálásával kapcsolatos követelmények

- A *rendszerkép* verziótulajdonságát a latest (legújabb) beállításra *kell állítani.*
- Az alkalmazás állapot-mintavételei vagy [az Application Health bővítmény használata](virtual-machine-scale-sets-health-extension.md) nem Service Fabric méretezési készletekhez.
- Használja a Compute API 2018-10-01-es vagy újabb verzióját.
- Győződjön meg arról, hogy a méretezésihalmaz-modellben megadott külső erőforrások elérhetők és frissítve vannak. Ilyenek például a virtuálisgép-bővítmény tulajdonságaiban lévő hasznos adatok rendszerindítására vonatkozó SAS URI, a tárfiók hasznos adata, a modellben lévő titkos kulcsokra való hivatkozás stb.
- A Windows rendszerű virtuális gépeket használó méretezési készletek esetében a Compute API 2019-03-01-es verziójától kezdődően a *virtualMachineProfile.osProfile.windowsConfiguration.enableAutomaticUpdates* tulajdonságnak *false* (hamis) értéket kell beállítania a méretezésicsoport-modell definíciójában. A fenti tulajdonság olyan virtuálisgép-frissítéseket tesz lehetővé, amelyekben a "Windows Update" az operációsrendszer-lemez cseréje nélkül alkalmazza az operációs rendszer javításokat. Ha az operációs rendszer rendszerképének automatikus frissítése engedélyezve van a méretezési csoportban, nincs szükség további frissítésre a "Windows Update" funkcióval.

### <a name="service-fabric-requirements"></a>Service Fabric követelmények

Ha alkalmazásokat Service Fabric, győződjön meg arról, hogy teljesülnek a következő feltételek:
-   Service Fabric tartóssági [](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) szint ezüst vagy arany, nem bronz (kivéve az állapot nélküli csomóponttípusokat, amelyek támogatják az operációs rendszer automatikus frissítését).
-   A Service Fabric modelldefiníción a TypeHandlerVersion 1.1-es vagy magasabb verziószámú bővítménynek kell lennie.
-   A tartóssági szintnek azonosnak kell lennie a Service Fabric fürtön és Service Fabric méretezési csoport modelldefinícióján.
- Nincs szükség további állapot-mintavételre vagy az alkalmazás állapotbővítményének használatára.

Győződjön meg arról, hogy a tartóssági beállítások nem egyezőek a Service Fabric fürtön és Service Fabric bővítményen, mivel az eltérés frissítési hibákat eredményez. A tartóssági szintek az ezen az oldalon ismertetett [irányelvek szerint módosíthatók.](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Egyéni rendszerképek operációsrendszer-lemezképének automatikus frissítése

Az operációs rendszer rendszerképének automatikus frissítése támogatott a-ben üzembe helyezett egyéni [rendszerképek Shared Image Gallery.](../virtual-machines/shared-image-galleries.md) Más egyéni rendszerképek nem támogatottak az operációs rendszer rendszerképének automatikus frissítésében.

### <a name="additional-requirements-for-custom-images"></a>Az egyéni rendszerképek további követelményei
- Az operációs rendszer rendszerképének automatikus frissítésére vonatkozó beállítási és konfigurációs folyamat minden méretezési csoport esetén ugyanaz, ahogy azt az oldal konfigurációs [szakaszában](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) is ismertetheti.
- Az operációs rendszer rendszerképének automatikus frissítésére konfigurált méretezésikészlet-példányok a Shared Image Gallery-rendszerkép legújabb verziójára [](../virtual-machines/shared-image-galleries.md#replication) lesznek frissítve, amikor a rendszerkép új verzióját közzéteszik és replikálják a méretezési készlet régiójába. Ha az új rendszerkép nincs replikálva arra a régióra, ahol a méretezés üzembe van állítva, a méretezésikészlet-példányok nem lesznek frissítve a legújabb verzióra. A regionális rendszerkép-replikáció lehetővé teszi a méretezési csoport új rendszerképének bevezetési szabályozását.
- Az új rendszerképverziót nem szabad kizárni a katalóguskép legújabb verziójából. A katalógus rendszerképének legújabb verziójából kizárt rendszerképverziók nem kerülnek a méretezési készletbe az operációs rendszer lemezképének automatikus frissítésén keresztül.

> [!NOTE]
>Akár 3 órát is igénybe vehet, hogy egy méretezési készlet elindítsa az első rendszerképfrissítési bevezetést, miután a méretezési készletet először konfigurálta az operációs rendszer automatikus frissítésére. Ez egy egyszeres késleltetés méretezési készletenként. A rendszer a további rendszerkép-bevezetéseket 30–60 percen belül aktiválja a méretezési készleten.


## <a name="configure-automatic-os-image-upgrade"></a>Az operációs rendszer rendszerképének automatikus frissítésének konfigurálása
Az operációs rendszer rendszerképének automatikus frissítésének konfigurálása érdekében győződjön meg arról, hogy  az *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* tulajdonság true (igaz) értékre van állítva a méretezésicsoport-modell definíciójában.

### <a name="rest-api"></a>REST API
Az alábbi példa bemutatja, hogyan állíthatja be az operációs rendszer automatikus frissítését egy méretezésikészlet-modellen:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag használatával konfigurálhatja az operációs rendszer rendszerképének automatikus frissítését a méretezési készlethez. A következő példa a myResourceGroup nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának automatikus *frissítését konfigurálja:*

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az [az vmss update használatával](/cli/azure/vmss#az_vmss_update) konfigurálhatja az operációs rendszer rendszerképének automatikus frissítését a méretezési készlethez. Használja az Azure CLI 2.0.47-es vagy magasabb vagy azt. A következő példa a myResourceGroup nevű erőforráscsoport *myScaleSet* nevű méretezési csoportjának automatikus *frissítését konfigurálja:*

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Miután konfigurálta a méretezési készlethez az operációs rendszer rendszerképének automatikus frissítését, a méretezési készlet virtuális gépeit is a legújabb méretezésikészlet-modellbe kell vinnie, ha a méretezési készlet a manuális frissítési szabályzatot [használja.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

## <a name="using-application-health-probes"></a>Alkalmazás állapot-mintavételének használata

Az operációs rendszer frissítése során a méretezési készletben található virtuálisgép-példányok egyszerre egy kötegben vannak frissítve. A frissítés csak akkor folytatódik, ha az ügyfélalkalmazás kifogástalan állapotú a frissített virtuálisgép-példányon. Javasoljuk, hogy az alkalmazás állapotjeleket biztosít a méretezési készlet operációsrendszer-frissítési motorja számára. Alapértelmezés szerint az operációs rendszer frissítései során a platform a virtuális gép energiaállapotát és a bővítmények kiépítési állapotát veszi figyelembe annak megállapításához, hogy a virtuálisgép-példány állapota megfelelő-e a frissítés után. A virtuálisgép-példány operációsrendszer-frissítése során a virtuálisgép-példány operációsrendszer-lemezét egy új lemez váltja fel a rendszerkép legújabb verziója alapján. Az operációs rendszer frissítésének befejezése után a konfigurált bővítmények ezeken a virtuális gépeken futnak. Az alkalmazás csak akkor minősül kifogástalan állapotúnak, ha a példány összes bővítménye sikeresen ki lett építve.

A méretezési készleteket alkalmazásállapot-mintavételekkel is konfigurálhatja, hogy a platform pontos információkat biztosítson az alkalmazás folyamatos állapotáról. Az alkalmazás állapot-mintavételei olyan egyéni Load Balancer mintavételek, amelyek állapotjelzőként vannak használva. A méretezési csoport virtuálisgép-példányán futó alkalmazás válaszolhat a külső HTTP- vagy TCP-kérésekre, amelyek jelzik, hogy kifogástalan állapotú-e. Az Egyéni terheléselosztási mintavételek Load Balancer lásd: [A terheléselosztási mintavételek .](../load-balancer/load-balancer-custom-probe-overview.md) Az alkalmazás állapot-mintavételei nem támogatottak a Service Fabric méretezési készletekben. A nem Service Fabric méretezési készletek alkalmazás Load Balancer állapot-mintavételeket vagy [az Application Health bővítményt igénylik.](virtual-machine-scale-sets-health-extension.md)

Ha a méretezési csoport több elhelyezési csoport használatára [](../load-balancer/load-balancer-overview.md) van konfigurálva, standard Load Balancer mintavételeket kell használni.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Egyéni adatbázis-Load Balancer mintavétel konfigurálása alkalmazás állapot-mintavételként egy méretezési csoporton
Ajánlott eljárásként hozzon létre kifejezetten egy terheléselosztási mintavételt a méretezési csoport állapotához. Egy meglévő HTTP-mintavételhez vagy TCP-mintavételhez ugyanazt a végpontot is használhatja, de az állapot-mintavételhez más viselkedésre lehet szükség, mint a hagyományos terheléselelosztási mintavétel esetében. Egy hagyományos terheléselelosztási mintavétel például akkor lehet nem megfelelő, ha a példány terhelése túl magas, de ez nem lenne megfelelő a példány állapotának meghatározásához az operációs rendszer automatikus frissítése során. Konfigurálja úgy a mintavételt, hogy két percnél rövidebb mintavételi sebességet állítson be.

A terheléselelosztási mintavételre a méretezési készlet *networkProfile* hálózatprofilja hivatkozhat, és a következőképpen társítható egy belső vagy nyilvános terheléseltöltőhöz:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Ha automatikus operációsrendszer-frissítéseket használ a Service Fabric, az új operációsrendszer-rendszerkép frissítési tartomány szerint lesz elérhető, hogy fenntartsa a frissítésben futó szolgáltatások magas Service Fabric. Az operációs rendszer automatikus frissítésének Service Fabric fürtcsomóponttípusát úgy kell konfigurálni, hogy az Ezüst tartóssági szintet vagy annál magasabb szintűt használjon. A bronz tartóssági szint esetében az automatikus operációsrendszer-frissítés csak az állapot nélküli csomóponttípusok esetében támogatott. A fürtök tartóssági jellemzőivel kapcsolatos további Service Fabric tekintse meg ezt a [dokumentációt.](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)

### <a name="keep-credentials-up-to-date"></a>Hitelesítő adatok naprakészentartása
Ha a méretezési készlet hitelesítő adatokat használ a külső erőforrások eléréséhez, például egy SAS-jogkivonat használatára konfigurált virtuálisgép-bővítményt a tárfiókhoz, akkor győződjön meg arról, hogy a hitelesítő adatok frissültek. Ha bármely hitelesítő adat( beleértve a tanúsítványokat és a jogkivonatokat) lejárt, a frissítés sikertelen lesz, és a virtuális gépek első kötege sikertelen állapotban marad.

A virtuális gépek helyreállításához és az operációs rendszer automatikus frissítésének újra engedélyezéséhez javasolt lépések az erőforrás-hitelesítési hiba esetén:

* Újra létrehozza a bővítmény(ök)nek átadott jogkivonatot (vagy bármely más hitelesítő adatokat).
* Győződjön meg arról, hogy a virtuális gépen belülről a külső entitásokkal való beszélgetéshez használt hitelesítő adatok naprakészek.
* Frissítse a bővítmény(öke)t a méretezésihalmaz-modellben az új jogkivonatokkal.
* Telepítse a frissített méretezési készletet, amely frissíti az összes virtuálisgép-példányt, beleértve a hibásakat is.

## <a name="using-application-health-extension"></a>Az Application Health bővítmény használata
Az Application Health bővítmény egy virtuálisgép-méretezésikészlet-példányon belül van üzembe állítva, és a méretezésikészlet-példányon belül jelenti a virtuális gépek állapotát. A bővítmény konfigurálható úgy, hogy mintavételt állítson be egy alkalmazásvégponton, és frissítse az alkalmazás állapotát a példányon. Az Azure ellenőrzi a példány állapotát annak megállapításához, hogy egy példány jogosult-e frissítési műveletekre.

[Mivel](../load-balancer/load-balancer-custom-probe-overview.md)a bővítmény egy virtuális gépen belülről jelenti az állapotadatokat, a bővítmény olyan helyzetekben használható, amikor nem használhatók külső mintavételek, például az Alkalmazás állapot-mintavételei (amelyek egyéni Azure Load Balancer mintavételeket használnak).

Az Application Health bővítmény többféleképpen is üzembe helyezhető a méretezési készletekben a cikkben lévő [példákban részletezett módon.](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Az operációs rendszer rendszerképének automatikus frissítésének előzményeit lekérte
A méretezési készleten végrehajtott legutóbbi operációsrendszer-frissítés előzményeit az Azure PowerShell, az Azure CLI 2.0 vagy a REST API-k használatával ellenőrizheti. Az utolsó öt operációsrendszer-frissítési kísérlet előzményeit az elmúlt két hónapban kaphatja meg.

### <a name="rest-api"></a>REST API
Az alábbi példa [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) a myResourceGroup nevű erőforráscsoportban a *myScaleSet* nevű méretezési csoport *állapotának ellenőrzésére:*

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

A GET hívás az alábbi példakimenethez hasonló tulajdonságokat ad vissza:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Get-AzVmss](/powershell/module/az.compute/get-azvmss) parancsmag használatával ellenőrizheti a méretezési készlet operációsrendszer-frissítési előzményeit. Az alábbi példa részletesen bemutatja, hogyan tekintse át egy *myScaleSet* nevű méretezési csoport operációsrendszer-frissítési állapotát a *myResourceGroup nevű erőforráscsoportban:*

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az [az vmss get-os-upgrade-history](/cli/azure/vmss#az_vmss_get_os_upgrade_history) használatával ellenőrizheti a méretezési készlet operációsrendszer-frissítési előzményeit. Használja az Azure CLI 2.0.47-es vagy magasabb vagy azt. Az alábbi példa részletesen bemutatja, hogyan tekintse át egy *myScaleSet* nevű méretezési csoport operációsrendszer-frissítési állapotát a *myResourceGroup nevű erőforráscsoportban:*

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Hogyan szerezd be a platform operációsrendszer-rendszerképének legújabb verzióját?

Az operációs rendszer automatikus frissítésének támogatott SKUs-hez elérhető rendszerképverziókat az alábbi példák segítségével kaphatja meg:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Operációsrendszer-rendszerkép frissítésének manuális aktiválása
Ha a méretezési csoporton engedélyezve van az operációs rendszer rendszerképének automatikus frissítése, nem kell manuálisan aktiválnia a rendszerkép-frissítéseket a méretezési csoportban. Az operációs rendszer verziófrissítési vezénylője manuális beavatkozás nélkül automatikusan alkalmazza a méretezésikészlet-példányokhoz elérhető legújabb rendszerképverziót.

Olyan esetekben, amikor nem szeretné megvárni, amíg a vezénylési rendszerkép alkalmazza a legújabb rendszerképet, az alábbi példák segítségével manuálisan aktiválhatja az operációs rendszer lemezképének frissítését.

> [!NOTE]
> Az operációsrendszer-lemezképek frissítésének manuális triggere nem biztosít automatikus visszaállítási képességeket. Ha egy példány nem áll helyre a frissítési művelet után, a korábbi operációsrendszer-lemeze nem állítható vissza.

### <a name="rest-api"></a>REST API
Az [operációs rendszer frissítésének indítási](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API-hívása segítségével elindíthat egy működés közbeni frissítést, hogy az összes virtuálisgép-méretezésihalmaz-példányt a rendszerkép operációs rendszerének legújabb elérhető verziójára helyezze át. A már a legújabb elérhető operációsrendszer-verziót futtató példányok nem érintettek. Az alábbi példa részletesen bemutatja, hogyan indíthat működés közbeni operációsrendszer-frissítést egy *myScaleSet* nevű méretezési csoporton a *myResourceGroup nevű erőforráscsoportban:*

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) parancsmag használatával ellenőrizheti a méretezési készlet operációsrendszer-frissítési előzményeit. Az alábbi példa részletesen bemutatja, hogyan indíthat működés közbeni operációsrendszer-frissítést egy *myScaleSet* nevű méretezési csoporton a *myResourceGroup nevű erőforráscsoportban:*

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az [az vmss rolling-upgrade start](/cli/azure/vmss/rolling-upgrade#az_vmss_rolling_upgrade_start) használatával ellenőrizheti a méretezési készlet operációsrendszer-frissítési előzményeit. Használja az Azure CLI 2.0.47-es vagy magasabb vagy azt. Az alábbi példa részletesen bemutatja, hogyan indíthat működés közbeni operációsrendszer-frissítést egy *myScaleSet* nevű méretezési csoporton a *myResourceGroup nevű erőforráscsoportban:*

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Üzembe helyezés sablon használatával

Sablonokkal üzembe helyezhet egy méretezési csoportokat az operációs rendszer automatikus frissítésekkel a támogatott rendszerképekhez, például az [Ubuntu 16.04-LTS-hez.](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Következő lépések
Az operációs rendszer automatikus frissítésének méretezési készletekkel való használatával kapcsolatos további példákért tekintse át a [GitHub-adattárat.](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)