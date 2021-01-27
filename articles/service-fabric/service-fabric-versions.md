---
title: Az Azure Service Fabric-fürt verziójának frissítése
description: Ismerje meg az Azure Service Fabric-beli fürtök verzióit, beleértve a Service Fabric csapat blogján elérhető legújabb kiadásokra mutató hivatkozást is.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: a99b38a33a5bb83345898769b466ce0cab0a4d4d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918109"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Az Azure Service Fabric-fürt verziójának frissítése

Győződjön meg arról, hogy a fürt mindig az Azure Service Fabric támogatott verzióját futtatja. Legalább 60 nappal azután, hogy bejelentjük a Service Fabric új verziójának kiadását, az előző verzió támogatása megszűnik. Az új kiadásokról a [Service Fabric csapat blogján](https://azure.microsoft.com/updates/?product=service-fabric)talál hirdetményeket.

A Service Fabric futtatókörnyezet minden egyes verziójához használhatja az SDK/NuGet csomagok megadott vagy régebbi verzióit. Előfordulhat, hogy a csomagok újabb verziói nem tudják megcélozni a régebbi fürtöket. Előfordulhat, hogy a régebbi fürtök olyan szolgáltatás-vagy protokoll-változásokkal rendelkeznek, amelyeket az újabb csomag-környezetek nem támogatják.

A következő cikkekből megtudhatja, hogyan tarthatja meg a fürtöt a támogatott Service Fabric verziójának futtatásával:

- [Azure Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md)
- [Az önálló Windows Server-fürtön futó Service Fabric verziójának frissítése](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Nem támogatott verziók

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Frissítési riasztás a 5,7-es és a 6.3.63 közötti verziókhoz. *

A biztonság és a rendelkezésre állás javítása érdekében az Azure-infrastruktúra olyan módosítást hajtott végre, amely hatással lehet Service Fabric ügyfelekre. Ez a módosítás az 5,7-6,3-es verziót futtató összes Service Fabric-fürtre hatással van.

Az Service Fabric futtatókörnyezet frissítése minden régióban támogatott Service Fabric-verzióhoz elérhető. A szolgáltatás megszakadásának elkerülése érdekében frissítsen a legújabb támogatott verziók egyikére a 2021 január 19-én.

Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége, az Azure-támogatási csatornákon keresztül érhető el. Nyisson meg egy támogatási kérést az Azure Service Fabric számára, és nevezze meg ezt a kontextust a támogatási jegyben.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Ha nem frissít egy támogatott verzióra

Az 5,7-es verzióról a 6.3.63-re futó Azure Service Fabric-fürtök nem lesznek elérhetők, ha nem frissítette a 2021. január 19-én.

#### <a name="required-action"></a>Szükséges művelet

Frissítsen egy támogatott Service Fabric verzióra, hogy megakadályozza az állásidőt vagy a változáshoz kapcsolódó funkciók elvesztését. Győződjön meg arról, hogy a fürtök legalább a következő verziókon futnak a környezettel kapcsolatos problémák megelőzése érdekében.

> [!Note]
> A 7,2 összes kiadott verziója tartalmazza a szükséges módosításokat.
  
  | Operációs rendszer | Jelenlegi Service Fabric futtatókörnyezet a fürtben | CU/patch kiadás |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Frissítési riasztás a 6,3-nál újabb verziókhoz

A biztonság és a rendelkezésre állás javítása érdekében az Azure-infrastruktúra olyan módosítást hajtott végre, amely hatással lehet Service Fabric ügyfelekre. Ez a módosítás minden olyan Service Fabric-fürtöt érint, amely [nyitott hálózatkezelési módot használ a tárolók számára](./service-fabric-networking-modes.md#set-up-open-networking-mode) , és 6,3-7,0 vagy nem kompatibilis támogatott verziót futtat 7,0-nál újabb verziókban. Az Service Fabric futtatókörnyezet frissítése minden régióban támogatott Service Fabric-verzióhoz elérhető.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Ha nem frissít egy támogatott verzióra

Az 6,3-nál későbbi, változatlan verziókon futó Azure Service Fabric-fürtök a működés vagy a szolgáltatás megszakadásának elvesztését tapasztalják, ha a 2021. január 19-én nem frissítettek támogatott verzióra.
  
  - A **6,3-nál nagyobb Service Fabric-verziót futtató fürtök esetében** a fürt továbbra is nyitva marad.

 - A **6,3-nál nagyobb Service Fabric-t futtató fürtök esetében, és a [tárolók nyitott hálózati funkciójának](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) használata** esetén a fürt elérhetetlenné válhat, és megszűnik a működés, ami a munkaterhelések szolgáltatás általi megszakítását okozhatja.
 
 -   A **[7.0.457 és a 7.0.466 közötti Windows-verziókat futtató fürtök esetében (mindkét verzióban megtalálható)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) és a Windows rendszerű tárolók funkció engedélyezve van. Megjegyzés: a Linux-verziók 7.0.457, a 7.0.464 és a 7.0.465 nincs hatással**.
    - **Hatás**: a fürt működése megszűnik, ami a munkaterhelések szolgáltatás általi megszakítását okozhatja.
    
#### <a name="required-action"></a>Szükséges művelet

A leállás vagy a funkciók elvesztésének elkerülése érdekében győződjön meg arról, hogy a fürtök a következő verziók egyikét futtatják.

A táblázatban lévő Service Fabric verziói tartalmazzák a szükséges módosításokat a funkció elvesztése érdekében. Győződjön meg arról, hogy a következő verziók egyikét használja.  

> [!Note]
> A 7,2 összes kiadási verziója tartalmazza a szükséges módosításokat.

  | Operációs rendszer | Jelenlegi Service Fabric futtatókörnyezet a fürtben | CU/patch kiadás |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16,04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16,04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16,04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

## <a name="supported-versions"></a>Támogatott verziók

A következő táblázat felsorolja a Service Fabric verzióit és azok támogatásának befejezési dátumát.

| Service Fabric futtatókörnyezet a fürtben | Közvetlenül a fürt verziójáról is frissíthető |Kompatibilis SDK-vagy NuGet-csomag verziója | Támogatás vége |
| --- | --- |--- | --- |
| A 5.3.121 előtti összes fürt verziója | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,3-es verzió |Január 20, 2017 |
| 5,3. * | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,3-es verzió |Február 24., 2017 |
| 5,4. * | 5.1.158.* |Kisebb vagy egyenlő, mint a 2,4-es verzió |2017. május 10.       |
| 5,5. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,5-es verzió |2017. augusztus 10.    |
| 5,6. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,6-es verzió |2017. október 13.   |
| 5,7. * | 5.4.164.* |Kisebb vagy egyenlő, mint a 2,7-es verzió |2017. december 15.  |
| 6,0. * | 5.6.205.* |Kisebb vagy egyenlő, mint a 2,8-es verzió |Március 30., 2018     |
| 6,1. * | 5.7.221.* |Kisebb vagy egyenlő, mint a 3,0-es verzió |Július 15., 2018      |
| 6,2. * | 6.0.232.* |Kisebb vagy egyenlő, mint a 3,1-es verzió |Október 26., 2018   |
| 6,3. * | 6.1.480.* |Kisebb vagy egyenlő, mint a 3,2-es verzió |Március 31., 2019  |
| 6,4. * | 6.2.301.* |Kisebb vagy egyenlő, mint a 3,3-es verzió |Szeptember 15., 2019 |
| 6,5. * | 6.4.617.* |Kisebb vagy egyenlő, mint a 3,4-es verzió |Augusztus 1-től 2020 |
| 7.0.466.* | 6.4.664.* |Kisebb vagy egyenlő, mint a 4,0-es verzió|2021. január 31.  |
| 7.0.466.* | 6,5. * |Kisebb vagy egyenlő, mint a 4,0-es verzió|2021. január 31. |
| 7.0.470.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,0-es verzió |2021. január 31.  |
| 7.0.472.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,0-es verzió |2021. január 31.  |
| 7.0.478.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,0-es verzió |2021. január 31.  |
| 7.1.409.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.417.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.428.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.456.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.458.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.459.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.503.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.1.510.* | 7.0.466.* |Kisebb vagy egyenlő, mint a 4,1-es verzió |Március 31., 2021 |
| 7.2.413.* | 7.0.470.* |Kisebb vagy egyenlő, mint a 4,2-es verzió |Aktuális verzió, így nincs befejező dátum |
| 7.2.432.* | 7.0.470.* |Kisebb vagy egyenlő, mint a 4,2-es verzió |Aktuális verzió, így nincs befejező dátum |
| 7.2.433.* | 7.0.470.* |Kisebb vagy egyenlő, mint a 4,2-es verzió |Aktuális verzió, így nincs befejező dátum |
| 7.2.445.* | 7.0.470.* |Kisebb vagy egyenlő, mint a 4,2-es verzió |Aktuális verzió, így nincs befejező dátum |
| 7.2.452.* | 7.0.470.* |Kisebb vagy egyenlő, mint a 4,2-es verzió |Aktuális verzió, így nincs befejező dátum |

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázat felsorolja a támogatott Service Fabric-verziókhoz tartozó támogatott operációs rendszereket.

| Operációs rendszer | A legkorábbi támogatott Service Fabric verziója |
| --- | --- |
| Windows Server 2012 R2 | Az összes verzió |
| Windows Server 2016 | Az összes verzió |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16,04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Támogatott verziók nevei

A következő táblázat a Service Fabric verziószámait és a hozzájuk tartozó verziószámokat sorolja fel.

| Verzió neve | Windows verziószáma | Linux verziószáma |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | Nem alkalmazható|
| 5,3 CU1 | 5.3.204.9494 | Nem alkalmazható|
| 5,3 CU2 | 5.3.301.9590 | Nem alkalmazható|
| 5,3 CU3 | 5.3.311.9590 | Nem alkalmazható|
| 5,4 CU2 | 5.4.164.9494 | Nem alkalmazható|
| 5,5 CU1 | 5.5.216.0    | Nem alkalmazható|
| 5,5 CU2 | 5.5.219.0 | Nem alkalmazható|
| 5,5 CU3 | 5.5.227.0 | Nem alkalmazható|
| 5,5 CU4 | 5.5.232.0 | Nem alkalmazható|
| 5,6 RTO | 5.6.204.9494 | Nem alkalmazható|
| 5,6 CU2 | 5.6.210.9494 | Nem alkalmazható|
| 5,6 CU3 | 5.6.220.9494 | Nem alkalmazható|
| 5,7 RTO | 5.7.198.9494 | Nem alkalmazható|
| 5,7 CU4 | 5.7.221.9494 | Nem alkalmazható|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Nem alkalmazható|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Nem alkalmazható|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Nem alkalmazható|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Nem alkalmazható|
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,1 CU10 | 7.1.510.9590 | NA |
| 7,2 RTO | 7.2.413.9590 | NA |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | Nem alkalmazható|
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
