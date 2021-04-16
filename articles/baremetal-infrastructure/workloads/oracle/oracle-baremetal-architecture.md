---
title: Az Oracle BareMetal-infrastruktúrájának architektúrája
description: Megismeri az Oracle-hez szükséges BareMetal-infrastruktúra számos konfigurációjának architektúráját.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559172"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Az Oracle BareMetal-infrastruktúrájának architektúrája

Ebben a cikkben az Oracle BareMetal-infrastruktúrájának architekturális lehetőségeit és az ezek által támogatott funkciókat fogjuk átfedni.

## <a name="single-instance"></a>Egy példány

Ez a topológia az Oracle Data Guard Oracle Database-példányát támogatja az BareMetal-infrastruktúrába való áttelepítéshez. Támogatja a készenléti csomópontok magas rendelkezésre állású és karbantartási feladatokhoz való használatát.

[![Az Oracle Data Guardot Oracle Database példány architektúráját bemutató ábra.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) One Node

Ez a topológia támogatja a megosztott tárolóval és GRID-fürtöt is támogató RAC-konfigurációt. Az adatbázispéldányok csak egy csomóponton futnak (aktív-passzív konfiguráció).

Funkciók:

- Aktív-passzív Oracle RAC egy csomóponttal

    - Automatikus feladatátvétel

    - Gyors újraindítás második csomóponton

- Valós idejű feladatátvétel és méretezhetőség az Oracle RAC-val

- Állásidő leállásmentes működés közbeni karbantartása

[![Az Oracle RAC egycsomópontos aktív-passzív konfiguráció architektúráját bemutató ábra.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Rac

Ez a topológia támogatja az Oracle RAC-konfigurációt megosztott tárolóval és Grid-fürtgal, miközben adatbázisonként több példány fut egyidejűleg (aktív-aktív konfiguráció).

- A teljesítmény egyszerűen skálázható a hozzáadott kiszolgálók online kiépítése révén. 
-  A felhasználók az összes kiszolgálón aktívak, és minden kiszolgáló ugyanazon a kiszolgálón Oracle Database. 
-  Az adatbázis-karbantartások minden típusa elvégezhető online vagy gördülő módon minimális vagy nulla állásidőig. 
- Az Oracle Active Data Guard (ADG) készenléti rendszerei könnyen szolgálhatnak kettős célú tesztrendszerként. 

Ez a konfiguráció lehetővé teszi, hogy az éles környezetben való alkalmazás előtt tesztelje az éles adatbázis pontos másolatán végrehajtott módosításokat.

> [!NOTE]
> Ha Active Data Guard Far Syncet (szinkron mód) kíván használni, figyelembe kell vennie a regionális zónákat, ahol ez a funkció támogatott. Csak földrajzilag elosztott régiók esetében javasoljuk a Data Guard aszinkron módban való használatát.

[![Aktív-aktív Oracle RAC-konfiguráció architektúráját bemutató ábra.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Ismerje meg az BareMetal-példányok Oracle számítási feladatokhoz való üzembehelyezését.

> [!div class="nextstepaction"]
> [BareMetal-infrastruktúra kiépítése Oracle-hez](oracle-baremetal-provision.md)

