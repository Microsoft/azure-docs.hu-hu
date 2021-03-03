---
title: HPC-alkalmazások méretezése – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg, hogyan méretezheti a HPC-alkalmazásokat az Azure-beli virtuális gépeken.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d560b261e058d01040616f3c59ede60e5986c672
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666976"
---
# <a name="scaling-hpc-applications"></a>HPC-alkalmazások méretezése

Az Azure-beli HPC-alkalmazások optimális, vertikális Felskálázási teljesítménye az adott számítási feladathoz szükséges teljesítmény-hangolási és optimalizálási kísérleteket igényli. Ez a szakasz és a virtuálisgép-sorozat-specifikus lapok általános útmutatást nyújtanak az alkalmazások skálázásához.

## <a name="compiling-applications"></a>Alkalmazások fordítása

Bár nem szükséges, az alkalmazások megfelelő optimalizálási jelzővel való fordítása biztosítja a legjobb méretezési teljesítményt a HB és a HC sorozatú virtuális gépeken.

### <a name="amd-optimizing-cc-compiler"></a>A C/C++ fordító AMD optimalizálása

Az AMD-optimalizáló C/C++ Compiler (AOCC) fordítórendszer magas szintű fejlett optimalizálási, többszálas és processzor-támogatást kínál, amely globális optimalizációt, vektorizációt, eljárások közötti elemzéseket, hurkos átalakításokat és a kód generálását is magában foglalja. A AOCC Compiler bináris fájljai a 2,17-es és újabb verziójú GNU C-függvénytárat (folyékonyan) futtató Linux rendszerekhez alkalmasak. A fordítói csomag egy C/C++ fordítóból (csenget), egy Fortran fordítóból (FLANG) és egy Fortran előtérből áll a csenget (Dragon Egg).

### <a name="clang"></a>Csenget

A csenget egy C, C++ és Objective-C fordító, amely az előfeldolgozást, az elemzést, az optimalizálást, a kód generálását, a szerelvényt és a csatolást kezeli. A csenget támogatja a  `-march=znver1` jelölőt, amely lehetővé teszi a legjobb kód generálását és finomhangolását az AMD Zen-alapú x86-architektúrája számára.

### <a name="flang"></a>FLANG

A FLANG Compiler a AOCC Suite (2018. április) újabb verziója, és jelenleg előzetes kiadásban érhető el a fejlesztők számára a letöltéshez és teszteléshez. A FORTRAN 2008 alapján az AMD kiterjeszti a FLANG GitHub-verzióját ( https://github.com/flang-compiler/flang) . A FLANG Compiler támogatja az összes csenget-fordítói beállítást és a FLANG-specifikus fordítási lehetőségek további számát.

### <a name="dragonegg"></a>DragonEgg

A DragonEgg egy GCC beépülő modul, amely felváltja a GCC optimalizálási és programkód-generátorait a LLVM projektből származókkal. A AOCC-vel együtt használható DragonEgg a GCC-4.8. x-mel lett tesztelve x86-32/x86-64-célokhoz, és számos Linux platformon sikeresen használatban volt.

A GFortran az előfeldolgozásra, elemzésre és szemantikai elemzésre szolgáló Fortran-programok tényleges előállítása, amely a GCC-GIMPLE köztes ábrázolást (IR) hozza létre. A DragonEgg egy GNU beépülő modul, amely a GFortran fordítási folyamatához csatlakozik. Implementálja a GNU beépülő modul API-ját. A beépülő modul architektúrájában a DragonEgg lesz a fordító illesztőprogramja, amely a fordítás különböző fázisait hajtja majd meg.  A letöltési és telepítési utasítások követése után a Dragon Egg a következő használatával hívható meg: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>OFJ-fordító
OFJ Community Edition ver. a 17 megerősítve az AMD-EPYC való együttműködésre. A STREAM OFJ-vel lefordított verziója teljes memória-sávszélességet biztosít a platform számára. Az újabb közösségi kiadás 18,10 (nov 2018) is jól működik. Az alábbi példa a CLI-t az Intel Compiler használatával optimálisan fordítóprogramba foglalja:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel fordító
Intel Compiler ver. a 18 megerősítve az AMD EPYC való együttműködésre. Az alábbi példa a CLI-t mutatja be optimálisan az Intel fordítóval.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC-fordító 
A HPC esetében az AMD a GCC Compiler 7,3-es vagy újabb verzióját javasolja. A régebbi verziók, például a RHEL/CentOS 7,4-es verzióban található 4.8.5 használata nem ajánlott. A GCC 7,3 és újabb verziók jelentősen nagyobb teljesítményt tesznek elérhetővé a HPL, a HPCG és a DGEMM teszteken.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Alkalmazások méretezése 

A következő javaslatok alkalmazhatók az alkalmazások optimális méretezésének hatékonyságára, teljesítményére és konzisztenciájára:

* A folyamatokat egy szekvenciális rögzítési megközelítéssel, a 0-59 magokra rögzítheti (az automatikus egyensúly megközelítése helyett). 
* A Numa/Core/HwThread kötése jobb, mint az alapértelmezett kötés.
* Hibrid párhuzamos alkalmazások esetében (OpenMP dokumentáció + MPI) 4 szálat és CCX 1 MPI-rangsort használhat.
* A tiszta MPI-alkalmazások esetében a CCX az optimális teljesítmény érdekében a 1-4 MPI soraival kísérletezik.
* Egyes alkalmazások, amelyek rendkívül érzékenyek a memória sávszélességére, kisebb számú magot használhatnak a CCX. Ezekhez az alkalmazásokhoz 3 vagy 2 mag/CCX használatával csökkentheti a memória sávszélességének növelését, és magasabb, valós teljesítményt vagy konzisztens méretezhetőséget eredményezhet. Az MPI-Allreduce különösen hasznosak lehetnek.
* A nagyobb méretű méretezési futtatásokhoz javasolt az UD vagy a Hybrid RC + UD átvitel használata. Számos MPI-függvénytár/futásidejű kódtár végzi ezt belsőleg (például UCX vagy MVAPICH2). Ellenőrizze a nagy léptékű futtatások átviteli konfigurációit.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli [HPC](/azure/architecture/topics/high-performance-computing/) -ről.
