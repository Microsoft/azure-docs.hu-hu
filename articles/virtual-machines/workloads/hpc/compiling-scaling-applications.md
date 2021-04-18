---
title: HPC-alkalmazások skálázása – Azure Virtual Machines | Microsoft Docs
description: Megtudhatja, hogyan skálázhatóak a HPC-alkalmazások az Azure-beli virtuális gépeken.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f81d40abdf402b1e19090c5375dfa8c335418248
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600964"
---
# <a name="scaling-hpc-applications"></a>HPC-alkalmazások skálázása

Az Azure-beli HPC-alkalmazások optimális fel- és felskálás teljesítménye teljesítmény-finomhangolási és optimalizálási kísérleteket igényel az adott számítási feladathoz. Ez a szakasz és a virtuálisgép-sorozatra vonatkozó oldalak általános útmutatást nyújtanak az alkalmazások méretezéséhez.

## <a name="application-setup"></a>Alkalmazásbeállítás
Az [azurehpc-repo számos](https://github.com/Azure/azurehpc) példát tartalmaz:
- Alkalmazások optimális [beállítása](https://github.com/Azure/azurehpc/tree/master/apps) és futtatása.
- Fájlrendszerek [és fürtök konfigurálása.](https://github.com/Azure/azurehpc/tree/master/examples)
- [Oktatóanyagok](https://github.com/Azure/azurehpc/tree/master/tutorials) a gyakori alkalmazás-munkafolyamatok könnyű elkezdésében.

## <a name="optimally-scaling-mpi"></a>Az MPI optimális skálázása 

Az alábbi javaslatok az optimális alkalmazásméretezés hatékonyságát, teljesítményét és konzisztenciáját biztosítják:

- Kisebb méretű feladatokhoz (például 256 < kapcsolatokhoz) használja a következő lehetőséget:
   ```bash
   UCX_TLS=rc,sm
   ```

- Nagyobb méretű feladatokhoz (például 256 000 >) használja a következő lehetőséget:
   ```bash
   UCX_TLS=dc,sm
   ```

- A fentiekben az MPI-feladat kapcsolatainak számának kiszámításához használja a következőt:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>Adaptív útválasztás
Az adaptív útválasztás (AR) lehetővé teszi, hogy az Azure Virtual Machines (virtuális gépek) EDR-t és AI InfiniBandet futtató azure-beli virtuális gépek automatikusan észleljék és elkerüljék a hálózati torlódást az optimálisabb hálózati útvonalak dinamikus kiválasztásával. Ennek eredményeképpen az AR nagyobb késést és sávszélességet biztosít az InfiniBand hálózaton, ami pedig nagyobb teljesítményt és skálázást eredményez. További részletekért tekintse meg a [TechCo új cikket.](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)

## <a name="process-pinning"></a>Folyamat rögzítése

- Folyamatok rögzítése magokra szekvenciális rögzítési megközelítéssel (és nem az automatikus kiegyensúlyozási megközelítéssel). 
- A Numa/Core/HwThread kötés jobb, mint az alapértelmezett kötés.
- Hibrid párhuzamos alkalmazásokhoz (OpenMP+MPI) használjon 4 szálat és 1 MPI rangot CCX-enként a HB- és HBv2-virtuálisgép-méretek esetében.
- Tiszta MPI-alkalmazások esetén a HB- és HBv2-virtuálisgép-méretek optimális teljesítménye érdekében kísérletezzen CCX-enként 1–4 MPI-rangsorolással.
- Egyes, a memória sávszélességére rendkívül érzékeny alkalmazások esetében előnyös lehet a CCX-enkénti kevesebb mag használata. Ezen alkalmazások esetében a CCX-enkénti 3 vagy 2 mag használata csökkentheti a memória-sávszélességért való összeesést, és magasabb valós teljesítményt vagy egységesebb méretezhetőséget eredményez. Különösen az MPI Allreduce számára előnyös lehet ez a megközelítés.
- A jelentősen nagyobb léptékű futtatás esetén ajánlott UD vagy hibrid RC+UD szállítást használni. Ezt számos MPI-kódtár/futásidejű kódtár (például UCX vagy MVAPICH2) belsőleg. A nagy léptékű futtatásokat az átviteli konfigurációkban ellenőrizheti.

## <a name="compiling-applications"></a>Alkalmazások fordítása
<br>
<details>
<summary>Kattintással bontsa ki</summary>

Bár nem szükséges, az alkalmazások fordítása a megfelelő optimalizálási jelzőkkel biztosítja a legjobb felskálás teljesítményt a HB- és HC-sorozatú virtuális gépeken.

### <a name="amd-optimizing-cc-compiler"></a>AMD C/C++ fordító optimalizálása

Az AMD C/C++ fordítóprogram (AOCC) fordítórendszer magas szintű fejlett optimalizálást, többszálas és processzortámogatást kínál, amely globális optimalizálást, vektorizációt, eljárásközi elemzéseket, ciklusátalakításokat és kódgenerációt tartalmaz. Az AOCC-fordító bináris fájlok olyan Linux rendszerekhez megfelelőek, amelyek 2.17-es vagy újabb GNU C-kódtára (cinbc) verzióval futnak. A fordítócsomag egy C/C++ fordítóból (tor), egy Fortran-fordítóból (FLANG) és egy Fortran-előlapból és a Lettg (Dragon Egg) felé.

### <a name="clang"></a>Fogag

A Fogg egy C, C++ és Objective-C fordító, amely előfeldolgozást, -kezelést, -optimalizálást, kód generálást, -szerelvényt és -összekapcsolást kezel. A The Flagg támogatja a legjobb kódgenerációt és -finomhangolást  `-march=znver1` az AMD Zen-alapú x86 architektúrája számára.

### <a name="flang"></a>FLANG (FLANG)

A FLANG-fordító az AOCC csomag (2018. áprilistól hozzáadva) egy új része, és jelenleg előzetes kiadásban érhető el a fejlesztők számára letöltésre és tesztelésre. A Fortran 2008 alapján az AMD kibővíti a FLANG ( ) GitHub-verzióját. https://github.com/flang-compiler/flang) A FLANG-fordító támogatja az összes Compileg fordítóprogram-lehetőséget, és további számú FLANG-specifikus fordítói lehetőséget.

### <a name="dragonegg"></a>DragonEgg

A DragonEgg egy gcc beépülő modul, amely lecseréli a GCC optimalizálóit és kódgenerátorát az LLVM-projektből származókra. Az AOCC-hez elérhető DragonEgg együttműködik a gcc-4.8.x-szel, x86-32/x86-64-es célokhoz lett tesztelve, és számos Linux-platformon sikeresen használható.

A GFortran a GCC GIMPLE köztes reprezentációt (IR) generáló előfeldolgozásért, elemzésért és szemantikai elemzésért felelős Fortran programok tényleges előtérde. A DragonEgg egy GNU beépülő modul, amely a GFortran-fordítási folyamathoz csatlakozik. Megvalósítja a GNU beépülő modul API-ját. A beépülő modul architektúrája során a DragonEgg lesz a fordító illesztője, amely a fordítás különböző fázisaiért hajtód.  A letöltési és telepítési utasításokat követve a Dragon Dragon a következővel hívható meg: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI-fordító
PGI Community Edition ver. A program megerősíti, hogy 17-et működik együtt az AMD EPYC-val. A STREAM PGI által lefordított verziója biztosítja a platform teljes memória-sávszélességét. Az újabb Community Edition 18.10 (2018. nov.) szintén jól működik. Az alábbiakban egy cli-minta látható, amely optimálisan fordítja le a fordítót az Intel Compiler segítségével:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel Compiler ver. A program megerősíti, hogy 18 működik együtt az AMD EPYC-val. Az alábbiakban egy cli-mintát olvashat az Intel Compiler optimális fordításához.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-fordító 
A HPC esetében az AMD a GCC-fordító 7.3-as vagy újabb használatát javasolja. A régebbi verziók, például az RHEL/CentOS 7.4 által tartalmazott 4.8.5 nem ajánlottak. A GCC 7.3-as és újabb verziók jelentősen nagyobb teljesítményt fognak eredményezni a HPL-, HPCG- és a FOGEMM-teszteken.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>Következő lépések

- Tesztelje tudását egy tanulási modullal, amely a HPC-alkalmazások azure-beli [optimalizálását használja.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- Tekintse át a [HBv3 sorozat áttekintését és](hbv3-series-overview.md) [a HC sorozat áttekintését.](hc-series-overview.md)
- Olvassa el a legújabb közleményeket, HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- További információ az [Azure-beli](/azure/architecture/topics/high-performance-computing/) HPC-ről.
