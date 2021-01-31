---
title: IBM számítási feladatok az Azure-ban | Microsoft Docs
description: A Microsoft partnereitől származó mainframe-emulátorral és más szolgáltatásokkal áthelyezheti az IBM z/OS munkaterheléseket Microsoft Azure használatával.
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: a2b5350e1aebeff3cc9dc3996f7e0a92c424ecf3
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219994"
---
# <a name="ibm-workloads-on-azure"></a>IBM számítási feladatok az Azure-ban

Számos, a z/OS-alapú IBM-es nagyszámítógépes számítási feladat replikálható az Azure-ban a funkciók elvesztése nélkül, és anélkül, hogy a felhasználók még a mögöttes rendszerek változásait is észrevették. Az Azure-beli alkalmazások újraüzemeltetése biztosítja a szükséges nagyvállalati funkciókat, valamint a felhő rugalmasságát, rendelkezésre állását és lehetséges költségmegtakarítását.

Az Azure támogatja a meglévő IBM mainframe-környezetekkel való integrációt, lehetővé téve az alkalmazások átirányítását, és szükség esetén hibrid megoldások futtatását, valamint az idő múlásával történő Migrálás. Habár teljes mértékben újraírhatók a meglévő Nagyvállalati alkalmazások az Azure-hoz, sokkal gyakoribb, hogy áthelyezi őket. Az újraírással az áttelepítési projektek költségeit, összetettségét és időpontját adhatja meg. Az újraüzemeltetéssel a következőket teheti:

- Alkalmazások áthelyezése felhőalapú emulátorba.

- Telepítse át az adatbázist egy felhőalapú adatbázisba.

- Cserélje le a modulokat és a kódokat a Code Transformation Engines használatával.

Ezen kívül az IBM-szoftverek, például a WebSphere és az MQ, már az Azure piactéren is elérhetők. Az IBM-szoftverek licence lehetővé teszi az Azure által biztosított igény szerinti infrastruktúra-méretezés előnyeit a virtuális gépek gyors elindításához.

Egy kiterjedt partneri ökoszisztéma segíti az IBM mainframe rendszerek áttelepíthetők az Azure-ba. A legtöbb esetben az újraírás vagy az alkalmazások cseréjének fokozatos üzembe helyezése előtt a lehető legalkalmasabb módszert kell alkalmazni az újrafelhasználásra. További útmutatást és segítséget nyújt a partnereknek az [Azure mainframe Migration Centerben](https://azure.microsoft.com/migration/mainframe/).

**Következő lépések**

- [Mainframe-áttelepítés: mítoszok és tények](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Az IBM&T fejlesztési és tesztelési környezet telepítése az Azure-ban](./install-ibm-z-environment.md)
- [Alkalmazás-fejlesztőknek vezérelt terjesztés (ADCD) beállítása az IBM&T v1-ben](./demo.md)
- [IBM DB2-pureScale az Azure-ban](ibm-db2-purescale-azure.md)
