---
title: Felgyorsított adatbázis-helyreállítás
titleSuffix: Azure SQL
description: A gyorsított adatbázis-helyreállítás gyors és konzisztens adatbázis-helyreállítást, azonnali tranzakció-visszaállítást, valamint az Azure SQL-portfólióban lévő adatbázisok agresszív naplózási felcsonkítát teszi lehetővé.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/19/2020
ms.openlocfilehash: bd1b33b2f6b1b0e0bf94639b3991b6507a89f5a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97400794"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Gyorsított adatbázis-helyreállítás az Azure SQL-ben 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A **gyorsított adatbázis-helyreállítás (ADR)** egy SQL Server adatbázismotor-szolgáltatás, amely nagy mértékben javítja az adatbázisok rendelkezésre állását, különösen a hosszú ideig futó tranzakciók jelenlétében, a SQL Server adatbázismotor helyreállítási folyamatának újratervezésével. 

Az ADR jelenleg a Azure SQL Database, az Azure SQL felügyelt példányaihoz, az Azure szinapszis Analytics-adatbázisokhoz és az Azure-beli virtuális gépek SQL Server SQL Server 2019-től kezdődően érhető el. 

> [!NOTE] 
> Az automatikus központi telepítési szabály alapértelmezés szerint engedélyezve van Azure SQL Database és az Azure SQL felügyelt példányain, és a termékre vonatkozó ADR letiltása nem támogatott. 

## <a name="overview"></a>Áttekintés

Az ADR elsődleges előnyei a következők:

- **Gyors és konzisztens adatbázis-helyreállítás**

  Az ADR-sel a hosszan futó tranzakciók nem befolyásolják a teljes helyreállítási időt, így a gyors és konzisztens adatbázis-helyreállítást, a rendszer aktív tranzakcióinak számától vagy azok méretétől függetlenül.

- **Tranzakció azonnali visszaállítása**

  Az automatikus központi telepítési szabály használata esetén a tranzakció-visszaállítás azonnal megtörténik, függetlenül attól, hogy a tranzakció Mikor aktív, vagy hogy hány frissítést hajtottak végre.

- **Agresszív naplózási csonkítás**

  Az ADR-sel a tranzakciós naplót a rendszer agresszív módon csonkolja, még akkor is, ha aktív, hosszan futó tranzakciókat is használ, ami megakadályozza, hogy az IT-csoport ne legyen nagyobb a szabályozásban.

## <a name="standard-database-recovery-process"></a>Szabványos adatbázis-helyreállítási folyamat

Az adatbázis-helyreállítás az [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) helyreállítási modellt követi, és három szakaszból áll, amelyek az alábbi ábrán láthatók, és a diagramon részletesebben is ismertetve vannak.

![jelenlegi helyreállítási folyamat](./media/accelerated-database-recovery/current-recovery-process.png)

- **Elemzési fázis**

  A tranzakciós napló továbbítása a legutóbbi sikeres ellenőrzőpont elejétől (vagy a legrégebbi piszkos oldal LSN) kezdve egészen addig, amíg az adatbázis le nem fejeződik az egyes tranzakciók állapotának meghatározásához.

- **Ismétlési fázis**

  Az összes véglegesített művelet visszavonásával továbbíthatja a tranzakciós naplót a legrégebbi, nem véglegesített tranzakcióból egészen addig, amíg az összeomlás időpontjában az adatbázis bekerül az állapotba.

- **Visszavonás fázisa**

  Minden olyan tranzakció esetében, amely az összeomlás időpontjában aktív volt, a napló visszafelé halad, és visszaveszi a tranzakció által végrehajtott műveleteket.

Ennek a kialakításnak a alapján a SQL Server adatbázismotor nem várt újraindítást igényel (nagyjából), amely az összeomlás időpontjában a rendszer leghosszabb aktív tranzakciójának méretével arányos. A helyreállításhoz az összes befejezetlen tranzakció visszaállítására van szükség. A szükséges időtartam a tranzakció által elvégzett munkához és az aktív időponthoz képest arányos. Ezért a helyreállítási folyamat hosszú időt is igénybe vehet a hosszú ideig futó tranzakciók (például nagyméretű tömeges beszúrási műveletek vagy index-létrehozási műveletek nagyméretű táblán való létrehozása) esetén.

A nagy tranzakciók ezen terv alapján történő megszakítása/visszaállítása is hosszú időt vehet igénybe, mivel a fentiekben leírtak szerint ugyanazt a visszavonás-helyreállítási szakaszt használja.

Emellett a SQL Server adatbázismotor nem tudja lerövidíteni a tranzakciónaplót, ha hosszú ideig futó tranzakciók vannak, mert a helyreállítási és a visszaállítási folyamatokhoz a megfelelő naplóbejegyzések szükségesek. A SQL Server adatbázismotor ezen kialakításának eredményeképpen egyes ügyfelek a probléma megoldására használták a tranzakciós napló méretét, és nagy mennyiségű lemezterületet igényelnek.

## <a name="the-accelerated-database-recovery-process"></a>A gyorsított adatbázis-helyreállítási folyamat

Az ADR a fenti problémákat a SQL Server adatbázismotor helyreállítási folyamatának teljes újratervezésével javítja:

- Ha nem szeretné, hogy a rendszer a naplót a legrégebbi aktív tranzakció elejétől, akkor ne végezzen állandó időt/pillanatot. Az ADR használata esetén a tranzakciónapló csak az utolsó sikeres ellenőrzőponton (vagy a legrégebbi inkonzisztens oldal naplózási sorszáma (LSN)) lesz feldolgozva. Ennek eredményeképpen a hosszú ideig futó tranzakciók nem befolyásolják a helyreállítási időt.
- Csökkentse a szükséges tranzakciós napló területét, mert már nem kell feldolgoznia a naplót a teljes tranzakcióhoz. Ennek eredményeképpen a tranzakciós naplót agresszíven lehet lerövidíteni ellenőrzőpontok és biztonsági mentések esetén.

Magas szinten az ADR a fizikai adatbázis összes módosításának verziószámozásával gyors adatbázis-helyreállítást tesz lehetővé, és csak a logikai műveleteket végzi el, amelyek korlátozottak, és szinte azonnal visszavonhatók. Minden olyan tranzakció, amely egy összeomlás időpontjában aktív, meg lett szakítva, ezért az ilyen tranzakciók által generált verziók figyelmen kívül hagyhatók egyidejű felhasználói lekérdezések esetén.

Az ADR helyreállítási folyamata a jelenlegi helyreállítási folyamattal megegyező három fázissal rendelkezik. Az alábbi ábrán látható, hogy a fázisok hogyan működnek az ADR-sel, és részletesebben ismerteti a diagramot követő részleteket.

![ADR helyreállítási folyamat](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Elemzési fázis**

  A folyamat ugyanaz marad, mint korábban a üt újraépítése és a nem verziószámmal rendelkező műveletek naplófájljainak másolása.
  
- **Ismétlési** fázis

  Két fázisra bontva (P)
  - 1. fázis

      Újra a üt (a legrégebbi, nem véglegesített tranzakció egészen az utolsó ellenőrzőpontig). Az ismétlés egy gyors művelet, mivel csak néhány rekordot kell feldolgoznia a üt-ból.

  - 2. fázis

     Visszaállítás a tranzakciónaplóból az utolsó ellenőrzőponttól indul (a legrégebbi nem véglegesített tranzakciók helyett)

- **Visszavonás fázisa**

   A visszavonási fázis az ADR használatával szinte azonnal elvégezhető a üt paranccsal a nem verziószámmal ellátott műveletek és a megőrzött verzió-tároló (PVS) visszavonásához a logikai visszaállítással a sor szintű verzió-alapú visszavonás végrehajtásához.

## <a name="adr-recovery-components"></a>ADR helyreállítási összetevők

Az ADR négy fő összetevője a következők:

- **Megőrzött verzió tárolója (PVS)**

  A megőrzött verzió tároló egy új SQL Server adatbázismotor-mechanizmus, amellyel megőrizhető az adatbázisból előállított, a hagyományos verziójú tároló helyett létrehozott sor verziója `tempdb` . A PVS lehetővé teszi az erőforrások elkülönítését, valamint javítja az olvasható formátumú másodlagos zónák rendelkezésre állását.

- **Logikai visszaállítása**

  A logikai visszaállítás az aszinkron folyamat, amely az összes verziószámmal ellátott művelet visszavonásának és visszavonásának elvégzéséhez felelős. A logikai visszavertség a következőket hajtja végre:

  - Nyomon követheti az összes megszakított tranzakciót, és láthatatlanként megjelölheti azokat más tranzakciókra. 
  - A visszaállítást a PVS használatával végezheti el az összes felhasználói tranzakcióhoz, nem pedig fizikailag ellenőrzi a tranzakciós naplót, és nem végezheti el a módosításokat.
  - A tranzakciók megszakítása után azonnal felszabadítja az összes zárolást. Mivel a megszakítás egyszerűen megjelöli a memória változásait, a folyamat nagyon hatékony, ezért a zárolásokat nem kell hosszú ideig tárolni.

- **Üt**

  a üt egy másodlagos memóriában tárolt log stream, amely a nem verziószámú műveletek (például a metaadat-gyorsítótár érvénytelenítése, a zárolási beszerzések stb.) rekordjait tárolja. A üt a következőket:

  - Kis mennyiségű és memóriában tárolt
  - Lemez megtartása az ellenőrzőpont-folyamat során szerializálva
  - Időszakosan csonkítva tranzakció véglegesítve
  - A csak a nem verziószámmal rendelkező műveletek feldolgozásával gyorsítja újra és vonja vissza a visszavonást.  
  - Engedélyezi az agresszív tranzakciónaplók csonkítása csak a szükséges naplóbejegyzések megőrzésével

- **Tisztító**

  A tisztító az aszinkron folyamat, amely időről időre felébred, és megtisztítja a szükségtelen lapokat.

## <a name="accelerated-database-recovery-patterns"></a>Gyorsított adatbázis-helyreállítási minták

A következő típusú munkaterhelések leginkább az ADR-ből származnak:

- Hosszú ideig futó tranzakciókkal rendelkező munkaterhelések.
- Olyan munkaterhelések, amelyek olyan eseteket észleltek, amikor az aktív tranzakciók jelentősen növekednek a tranzakciós naplóban.  
- Azok a munkaterhelések, amelyek hosszú ideig tartó adatbázis-leállást észleltek, mert hosszú ideje fut a helyreállítás (például a váratlan szolgáltatás újraindítása vagy a manuális tranzakciók visszaállítása).
