---
title: A helyi Azure Service Fabric-fürt telepítésének hibáinak megoldása
description: Ez a cikk a helyi fejlesztési fürttel kapcsolatos hibaelhárítási javaslatok körét ismerteti
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575907"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Helyi fejlesztői fürtkonfiguráció hibaelhárítása
Ha problémába ütközne a helyi Azure Service Fabric fejlesztői fürttel való interakció során, tekintse át a következő javaslatokat a lehetséges megoldásokról.

## <a name="cluster-setup-failures"></a>Fürt telepítési hibái
### <a name="cannot-clean-up-service-fabric-logs"></a>Nem lehet törölni Service Fabric naplókat
#### <a name="problem"></a>Probléma
A DevClusterSetup parancsfájl futtatásakor a következő hibaüzenet jelenik meg:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Megoldás
Az aktuális PowerShell-ablak bezárásával nyisson meg egy új PowerShell-ablakot rendszergazdaként. Most már sikeresen futtathatja a szkriptet.

## <a name="cluster-connection-failures"></a>Fürt csatlakoztatási hibái

### <a name="type-initialization-exception"></a>Inicializálási kivétel típusa
#### <a name="problem"></a>Probléma
Ha a PowerShellben csatlakozik a fürthöz, a System. Fabric. Common. AppTrace TypeInitializationException hibaüzenet jelenik meg.

#### <a name="solution"></a>Megoldás
Az elérésiút-változó nem lett megfelelően beállítva a telepítés során. Jelentkezzen ki a Windowsból, és jelentkezzen be újra. Ez frissíti az elérési utat.

### <a name="cluster-connection-fails-with-object-is-closed"></a>A fürthöz való csatlakozás meghiúsul "az objektum bezárva"
#### <a name="problem"></a>Probléma
A Connect-ServiceFabricCluster hívása a következőhöz hasonló hibával meghiúsul:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Megoldás
Az aktuális PowerShell-ablak bezárásával nyisson meg egy új PowerShell-ablakot rendszergazdaként.

### <a name="fabric-connection-denied-exception"></a>Fabric-kapcsolódás megtagadásának kivétele
#### <a name="problem"></a>Probléma
A Visual studióból történő hibakereséskor FabricConnectionDeniedException hibaüzenet jelenik meg.

#### <a name="solution"></a>Megoldás
Ez a hiba általában akkor fordul elő, ha manuálisan próbál meg elindítani egy Service Host-folyamatot.

Győződjön meg arról, hogy nem rendelkezik indítási projektként beállított szolgáltatási projektekkel a megoldásban. Csak Service Fabric alkalmazás-projekteket kell beállítani indítási projektként.

> [!TIP]
> Ha a telepítés után a helyi fürt rendellenesen megkezdi a működést, alaphelyzetbe állíthatja a helyi cluster Manager rendszertálca-alkalmazás használatával. Ezzel eltávolítja a meglévő fürtöt, és beállíthat egy újat. Vegye figyelembe, hogy a rendszer eltávolítja az összes telepített alkalmazást és a hozzá tartozó összes adatmennyiséget.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [A fürt megismerése és hibakeresése rendszerállapot-jelentésekkel](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)

