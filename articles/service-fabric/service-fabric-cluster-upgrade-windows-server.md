---
title: Önálló fürt verziójának frissítése
description: Frissítse az önálló Service Fabric-fürtöt futtató Azure Service Fabric-kódot.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842937"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>A fürtön futó Service Fabric verziójának frissítése 

Bármely modern rendszer esetében a frissítés a termék hosszú távú sikerességének kulcsa. Az Azure Service Fabric-fürt egy saját erőforrás. Ez a cikk az önálló fürtön futó Service Fabric verziójának frissítését ismerteti.

> [!NOTE]
> Győződjön meg arról, hogy a fürt mindig támogatott Service Fabric verziót futtat. Ha a Microsoft bejelenti Service Fabric új verziójának kiadását, az előző verzió a támogatás végére van megjelölve, a bejelentés dátumától számítva legalább 60 nappal. Az új kiadások a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)jelennek meg. Az új kiadás elérhető az adott pontra való választáshoz.
>
>

A fürtöt csak akkor frissítheti az új verzióra, ha éles stílusú csomópont-konfigurációt használ, ahol minden Service Fabric csomópont külön fizikai vagy virtuális gépen van lefoglalva. Ha olyan fejlesztési fürttel rendelkezik, amelyben több Service Fabric csomópont egyetlen fizikai vagy virtuális gépen van, akkor újra létre kell hoznia a fürtöt az új verzióval.

Két különálló munkafolyamat képes frissíteni a fürtöt a legújabb verzióra vagy egy támogatott Service Fabric-verzióra. Az egyik munkafolyamat olyan fürtökhöz tartozik, amelyeknek a kapcsolata automatikusan letölti a legújabb verziót. A másik munkafolyamat olyan fürtök esetében használható, amelyek nem rendelkeznek a legújabb Service Fabric verzió letöltéséhez szükséges kapcsolattal.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>A fürt Service Fabric verziójának automatikus verziófrissítésének engedélyezése
Ha úgy szeretné beállítani a fürtöt, hogy letöltse Service Fabric frissítéseit, amikor a Microsoft új verziót szabadít fel, állítsa igaz értékre a `fabricClusterAutoupgradeEnabled` fürt konfigurációját.  Ha az Service Fabric támogatott verzióját szeretné manuálisan kiválasztani, amelyet a fürtnek be szeretne állítani, állítsa a `fabricClusterAutoupgradeEnabled` fürt konfigurációját *hamis* értékre.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>A legújabb kód és konfiguráció letöltésére szolgáló kapcsolattal rendelkező fürtök frissítése
Ezekkel a lépésekkel frissítheti a fürtöt egy támogatott verzióra, ha a fürtcsomópontok internetkapcsolattal rendelkeznek a [Microsoft letöltőközpontból](https://download.microsoft.com).

Azon fürtök esetében, amelyeknek van kapcsolata a [Microsoft letöltőközpontból](https://download.microsoft.com), a Microsoft rendszeres időközönként ellenőrzi az új Service Fabric verziók rendelkezésre állását.

Ha új Service Fabric verzió érhető el, a csomagot a rendszer helyileg letölti a fürtre, és kiépíti a frissítést. Az új verzió ügyfelének tájékoztatása érdekében a rendszer egy explicit, a fürt állapotára vonatkozó figyelmeztetést jelenít meg, amely a következőhöz hasonló:

"Az aktuális fürt verziója [Version #] támogatás vége [date]."

Ha a fürt a legújabb verziót futtatja, a figyelmeztetés eltűnik.

Amikor megjelenik a fürt állapota figyelmeztetés, frissítse a fürtöt:

1. Kapcsolódjon a fürthöz bármely olyan gépről, amely rendszergazdai hozzáféréssel rendelkezik az összes olyan géphez, amely a fürt csomópontjaiként van felsorolva. A parancsfájl futtatásához használt gépnek nem kell a fürt részét képeznie.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Szerezze be azon Service Fabric verziók listáját, amelyekre frissíthet.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Ehhez a következőhöz hasonló kimenetnek kell megjelennie:

    ![Service Fabric verziók beolvasása][getfabversions]
3. Indítsa el a fürtöt egy elérhető verzióra a [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell-parancs használatával.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   A frissítés előrehaladásának figyeléséhez Service Fabric Explorer vagy a következő PowerShell-parancsot használhatja:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést. Az Start-ServiceFabricClusterUpgrade parancshoz tartozó egyéni állapotházirendek megadásához tekintse meg a [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)dokumentációját.

    A visszaállítást eredményező problémák kijavítása után indítsa újra a frissítést az előzőekben ismertetett lépéseket követve.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>A legújabb kód és konfiguráció letöltéséhez *nem szükséges kapcsolattal* rendelkező fürtök frissítése
Ezekkel a lépésekkel frissítheti a fürtöt egy támogatott verzióra, ha a fürtcsomópontok nem rendelkeznek internetkapcsolattal a [Microsoft letöltőközpontból](https://download.microsoft.com).

> [!NOTE]
> Ha olyan fürtöt futtat, amely nem kapcsolódik az internethez, a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) figyelnie kell az új kiadásokkal kapcsolatos tudnivalókat. A rendszer nem jeleníti meg a fürt állapotára vonatkozó figyelmeztetést, hogy riasztást küldjön az új kiadásokról.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatikus kiépítés és manuális kiépítés
A legújabb kód verziójának automatikus letöltésének és regisztrálásának engedélyezéséhez állítsa be a Service Fabric Update szolgáltatást. Útmutatásért lásd: *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* az [önálló csomagban](service-fabric-cluster-standalone-package-contents.md).

A manuális folyamathoz kövesse az alábbi utasításokat.

Módosítsa a fürt konfigurációját úgy, hogy a következő tulajdonságot *false* értékre állítsa a konfiguráció frissítésének megkezdése előtt:

```json
"fabricClusterAutoupgradeEnabled": false,
```

A használat részleteiért tekintse meg a [Start-ServiceFabricClusterConfigurationUpgrade PowerShell-](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) parancsot. A konfiguráció frissítésének megkezdése előtt győződjön meg arról, hogy az "clusterConfigurationVersion" fájlt a JSON-ban frissíti.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Fürt frissítési munkafolyamata

1. Futtassa a [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) a fürt egyik csomópontján, és jegyezze fel a *TargetCodeVersion*.

2. Futtassa a következőt egy internetkapcsolattal rendelkező gépről az összes verziófrissítéssel kompatibilis verzió listázásához az aktuális verzióval, majd töltse le a megfelelő csomagot a társított letöltési hivatkozásokból:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Kapcsolódjon a fürthöz bármely olyan gépről, amely rendszergazdai hozzáféréssel rendelkezik az összes olyan géphez, amely a fürt csomópontjaiként van felsorolva. A parancsfájl futtatásához használt gépnek nem kell a fürt részét képeznie.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Másolja a letöltött csomagot a fürt rendszerkép-tárolójába.

5. Regisztrálja a másolt csomagot.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Indítsa el a fürt frissítését egy elérhető verzióra.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    A frissítés előrehaladását a Service Fabric Exploreron figyelheti, vagy futtathatja a következő PowerShell-parancsot:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Ha a fürt állapot-házirendjei nem teljesülnek, a rendszer visszaállítja a frissítést. Az Start-ServiceFabricClusterUpgrade parancshoz tartozó egyéni állapotházirendek megadásához tekintse meg a [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade)dokumentációját.

    A visszaállítást eredményező problémák kijavítása után indítsa újra a frissítést az előzőekben ismertetett lépéseket követve.

## <a name="next-steps"></a>Következő lépések
* [Önálló fürt konfigurációjának frissítése](service-fabric-cluster-config-upgrade-windows-server.md)
* Néhány [Service Fabric-fürt beállításainak](service-fabric-cluster-fabric-settings.md)testreszabása
* [A fürt méretezése a és a](service-fabric-cluster-scale-in-out.md)szolgáltatásban.

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
