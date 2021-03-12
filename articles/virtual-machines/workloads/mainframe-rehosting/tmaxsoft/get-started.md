---
title: Ismerkedés az Azure Virtual Machines TmaxSoft OpenFrame Kiindulópontul
description: Az IBM z/OS mainframe-alapú munkaterheléseket a TmaxSoft OpenFrame Kiindulópontul-környezettel helyezheti át az Azure Virtual Machines (VM) használatával.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: workloads
ms.openlocfilehash: a8210a47cdc31b28bd7dd2b0021570c18db89423
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558202"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Ismerkedés a TmaxSoft OpenFrame Kiindulópontul az Azure-ban

Helyezze el meglévő mainframe-eszközeit, és helyezze át őket Microsoft Azure TmaxSoft OpenFrame Kiindulópontul használatával. Ez a népszerű visszaüzemeltetési megoldás egy emulációs környezetet hoz létre az Azure-ban, amely lehetővé teszi az alkalmazások gyors átköltöztetését. Nincs szükség újraformázásra.

## <a name="openframe-rehosting-environment"></a>OpenFrame Kiindulópontul-környezet újraüzemeltetése

OpenFrame Kiindulópontul-környezet létrehozása az Azure-ban fejlesztési, bemutatók, tesztelési és éles számítási feladatokhoz. Az alábbi ábrán látható, hogy a OpenFrame Kiindulópontul több olyan összetevőt tartalmaz, amelyek az Azure-ban létrehozzák a nagyszámítógépi emulációs környezetet. Például a OpenFrame Kiindulópontul online szolgáltatások cserélje le a nagyvállalati middleware-t, például az IBM Customer Information Control System (CICS) szolgáltatást. A OpenFrame Kiindulópontul Batch a TJES összetevővel helyettesíti az IBM mainframe-EK feladatának bejegyzési alrendszerét (csatlakoztatva). 

![OpenFrame Kiindulópontul-újraüzemeltetési folyamat](media/openframe-01.png)

> [!NOTE]
> A OpenFrame Kiindulópontul-környezet Azure-beli futtatásához érvényes licenccel vagy próbaverziós licenccel kell rendelkeznie a TmaxSoft.

## <a name="openframe-components"></a>OpenFrame Kiindulópontul-összetevők

A következő összetevők az Azure-beli OpenFrame Kiindulópontul-környezet részét képezik:

- **Áttelepítési eszközök** , beleértve a OFMiner, a nagyszámítógépeket elemző megoldást, majd áttelepíti őket az Azure-ba.
- **Fordítóprogramok**, beleértve a OFCOBOL, egy fordítót, amely a nagyszámítógép Cobol programját értelmezi. OFPLI, amely a nagyszámítógépek PL/I programjait értelmezi. a OFASM pedig egy fordító, amely a nagyvállalati szerelvények programjait értelmezi.
- **Előtér** -összetevők, beleértve a Java Enterprise felhasználói megoldást (JEUS), egy webalkalmazás-kiszolgálót, amely a Java Enterprise Edition 6. OFGW, valamint a OpenFrame Kiindulópontul-átjáró összetevője, amely egy 3270-figyelőt biztosít.
- **Alkalmazás** -környezet. A OpenFrame Kiindulópontul Base a teljes rendszer kezelésére szolgáló middleware. A OpenFrame Kiindulópontul Server Type C (OSC) a nagyszámítógépek köztes és az IBM CICS váltja fel.
- A **kapcsolódó adatbázis**, például a Tibero (látható), a Oracle Database, a Microsoft SQL Server, az IBM DB2 vagy a MySQL. A OpenFrame Kiindulópontul alkalmazások az adatbázissal való kommunikációhoz az Open Database Connectivity (ODBC) protokollt használják.
- **Biztonság** a TACF-on keresztül, amely a rendszerekhez és erőforrásokhoz való felhasználói hozzáférés szabályozását vezérli. 
- A **OFManager** olyan megoldás, amely a OpenFrame Kiindulópontul működését és felügyeleti funkcióit biztosítja a webes környezetben.

![OpenFrame Kiindulópontul architektúra](media/openframe-02.png)

## <a name="next-steps"></a>Következő lépések

- [TmaxSoft-OpenFrame Kiindulópontul telepítése az Azure-ban](./install-openframe-azure.md)
