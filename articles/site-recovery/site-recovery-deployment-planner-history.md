---
title: Azure Site Recovery Deployment Planner korábbi verziók
description: Ismert különböző Site Recovery Deployment Planner verziók javításai és ismert korlátai a kiadási dátumokkal együtt.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133799"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery Deployment Planner korábbi verziók

Ez a cikk a Azure Site Recovery Deployment Planner összes verziójának előzményeit, valamint a javításokat, az ismert korlátozásokat és azok kiadási dátumát tartalmazza.

## <a name="version-252"></a>2,52-es verzió

**Kiadás dátuma: június 4., 2020**

**Hibajavítások**

- A vCenter 7,0 támogatása
- A következő operációs rendszerek támogatása:

    - SUSE Linux Enterprise 15 (minden másodlagos verzióval)
    - Red Hat Enterprise Linux 8 (az összes alverzióval)


## <a name="version-251"></a>2,51-es verzió

**Kiadás dátuma: augusztus 22., 2019**

**Hibajavítások**

- A Deployment Planner 2,5-es verzióval kapcsolatos Cost-javaslati probléma kijavítva

## <a name="version-25"></a>2,5-es verzió

**Kiadás dátuma: július 29., 2019**

**Hibajavítások**

- A VMware virtuális gépek és a fizikai gépek esetében az ajánlás frissítése a Managed Disks replikálásán alapul.
- Windows 10 (x64), Windows 8,1 (x64), Windows 8 (x64), Windows 7 (x64) SP1 vagy újabb verzió támogatása

## <a name="version-24"></a>2,4-es verzió

**Kiadás dátuma: április 17., 2019**

**Hibajavítások**

- Továbbfejlesztett operációs rendszerek kompatibilitása, pontosabban a honosított hibák kezelésekor.
- A kompatibilitási ellenőrzőlistán akár 20 Mbps adatváltozási sebességgel (forgalommal) is bővült a virtuális gépek.
- Továbbfejlesztett hibaüzenetek
- A vCenter 6,7 támogatása.
- A Windows Server 2019 és a Red Hat Enterprise Linux (RHEL) munkaállomás támogatása.



## <a name="version-23"></a>2,3-es verzió

**Kiadás dátuma: december 3., 2018**

**Hibajavítások**

- Kijavított egy problémát, amely megakadályozta, hogy a Deployment Planner a jelentést a megadott célhely és előfizetés alapján hozza létre.

## <a name="version-22"></a>2,2-es verzió 

**Kiadás dátuma: április 25., 2018**

**Hibajavítások**

- GetVMList műveletek:
  - Kijavított egy problémát, amely miatt a GetVMList sikertelen volt, ha a megadott mappa nem létezik. Most létrehoz egy alapértelmezett könyvtárat, vagy létrehozza a OUTPUTFILE paraméterben megadott könyvtárat.
  - A GetVMList részletesebb meghibásodási okait adtak hozzá.
- A virtuálisgép-típus információi a Deployment Planner jelentés kompatibilis virtuális gépek lapjának oszlopként szerepelnek.
- Hyper-V – Azure vész-helyreállítás:
  - A kizárt virtuális gépek megosztott virtuális merevlemezekkel és továbbító lemezekkel rendelkeznek. A Startprofiling művelet a kizárt virtuális gépek listáját jeleníti meg a konzolon.
  - A rendszer több mint 64 lemezzel rendelkező virtuális gépeket adott hozzá a nem kompatibilis virtuális gépek listájához.
  - Frissítettük a kezdeti replikálási (IR) és a különbözeti replikáció (DR) tömörítési tényezőt.
  - Korlátozott támogatás lett hozzáadva az SMB-tároláshoz.

## <a name="version-21"></a>2,1-es verzió

**Kiadás dátuma: 2018. január 3.**

**Hibajavítások**

- Frissült az Excel-jelentés.
- Kijavított hibák a GetThroughput műveletben.
- Hozzáadott beállítás a virtuális gépek számának vagy a jelentés előállításának korlátozására. Az alapértelmezett korlát 1 000 virtuális gép.
- VMware – Azure vész-helyreállítás:
  - A Windows Server 2016 virtuális gép hibája javítva lett a nem kompatibilis táblába. 
  - Frissített kompatibilitási üzenetek Extensible Firmware Interface (EFI) Windows rendszerű virtuális gépekhez.
- A VMware-ről az Azure-ra és a Hyper-V-ről az Azure-ra frissítette a virtuális gép adatváltozási korlátját. 
- A virtuális gépek listájának fájl-elemzésének jobb megbízhatósága.

## <a name="version-201"></a>2.0.1-es verzió

**Kiadás dátuma: december 7., 2017**

**Hibajavítások**

- A hálózati sávszélesség optimalizálására vonatkozó javaslat hozzáadva.

## <a name="version-20"></a>2,0-es verzió

**Kiadás dátuma: november 28., 2017**

**Hibajavítások**

- A Hyper-V és az Azure közötti vész-helyreállítás támogatása.
- Cost kalkulátor hozzáadva.
- Az operációs rendszer verziószáma a VMware-ből az Azure-ba kerül, hogy a virtuális gép kompatibilis vagy nem kompatibilis-e a védelemmel. Az eszköz a vCenter-kiszolgáló által az adott virtuális géphez visszaadott operációsrendszer-verzió karakterláncot használja. Ez a vendég operációs rendszer azon verziója, amelyet a felhasználó a virtuális gép VMware-ben történő létrehozásakor választott ki.

**Ismert korlátozások:**

- A Hyper-V – Azure vész-helyreállítás esetén a következő karaktereket tartalmazó virtuális gép neve: `,` ,, `"` ,, `[` `]` és ``` ` ``` nem támogatott. A beolvasás során a jelentés létrehozása sikertelen lesz, vagy helytelen az eredmény.
- A VMware – Azure vész-helyreállítás esetén a vesszőt tartalmazó virtuális gép nem támogatott. A beolvasás során a jelentés létrehozása meghiúsul, vagy helytelen eredményt ad.

## <a name="version-131"></a>1.3.1-es verzió

**Kiadás dátuma: július 19., 2017** 

**Hibajavítások**

- Nagyméretű lemezek támogatása (> 1 TB) a jelentéskészítés során. Mostantól a Deployment Planner használatával megtervezheti az 1 TB-nál nagyobb méretű lemezekkel rendelkező virtuális gépek replikálását (akár 4095 GB-ig).
További információ a [Nagyméretű lemezek támogatásáról az Azure Site Recoveryben](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>1,3-es verzió

**Kiadás dátuma: május 9., 2017**

**Hibajavítások**

- A felügyelt lemez támogatása a jelentéskészítés során. Az egyetlen Storage-fiókba helyezhető virtuális gépek számát a rendszer attól függően számítja ki, hogy a felügyelt lemez ki van-e választva feladatátvételi/tesztelési feladatátvételre.

## <a name="version-12"></a>1,2-es verzió

**Kiadás dátuma: április 7., 2017**

**Hibajavítások**

- Az egyes virtuális gépekhez hozzáadott rendszerindítási típus (BIOS vagy EFI) ellenőrzi, hogy a virtuális gép kompatibilis-e vagy nem kompatibilis-e a védelemmel.
- A kompatibilis virtuális gépekhez és a nem kompatibilis virtuális gépekhez tartozó munkalapokhoz hozzáadott operációsrendszer-típusokra vonatkozó információk.
- A GetThroughput művelet támogatása az Egyesült Államok kormánya és Kína Microsoft Azure régióiban.
- A rendszer most már néhány új előfeltételt is ellenőriz a vCenter- és az ESXi-kiszolgálók esetében.
- Kijavítva a helytelen jelentés hibáját, amikor a területi beállítások nem angolra vannak állítva.

## <a name="version-11"></a>1,1-es verzió

**Kiadás dátuma: március 9., 2017**

**Hibajavítások**

- Kijavított egy problémát, amely megakadályozta a virtuális gépek profilkészítését, ha két vagy több virtuális gép ugyanazzal a névvel vagy IP-címmel rendelkezik a különböző vCenter ESXi-gazdagépek között.
- A kompatibilis virtuális gépek és a nem kompatibilis virtuális gépek munkalapjain a másolás és a keresés letiltását okozó probléma kijavítva.

## <a name="version-10"></a>1,0-es verzió

**Kiadás dátuma: február 23., 2017**

**Ismert korlátozások:**

- Csak a VMware – Azure vész-helyreállítási forgatókönyvek esetében támogatott. A Hyper-V – Azure vész-helyreállítási forgatókönyvek esetén használja a [Hyper-v Capacity Planner eszközt](./hyper-v-deployment-planner-overview.md).
- Nem támogatja a GetThroughput műveletet az Egyesült Államok kormánya és Kína Microsoft Azure régióiban.
- Az eszköz Cann 't-profil virtuális gépeket tartalmaz, ha a vCenter-kiszolgálón két vagy több virtuális gép található ugyanazzal a névvel vagy IP-címmel különböző ESXi-gazdagépek között.
Ebben a verzióban az eszköz kihagyja a profilkészítést abban az esetben, ha a VMListFile fájlban ismétlődik egy virtuális gép neve vagy IP-címe. Ez áthidalható úgy, hogy a vCenter-kiszolgáló helyett az ESXi-gazdagéppel készíti el a virtuális gépek profilját. Győződjön meg arról, hogy az egyes ESXi-gazdagépek egyetlen példányát futtatják.
