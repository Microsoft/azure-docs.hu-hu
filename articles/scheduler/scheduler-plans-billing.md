---
title: Csomagok és számlázás
description: Az Azure Scheduler csomagjaira és számlázására vonatkozó információk
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "78898473"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Csomagok és számlázás az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

## <a name="job-collection-plans"></a>A feladatütemezés csomagjai

Az Azure Schedulerben a feladatütemezés meghatározott számú feladatot tartalmaz. A Feladatkártya a számlázható entitás, amely a standard, P10 Premium és P20 prémium csomagokban érhető el, amelyek itt olvashatók: 

| Job Collection terv | Feladatok maximális száma gyűjtemény szerint | Maximális ismétlődés | Feladatok maximális száma előfizetéssel | Korlátok | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 feladat/gyűjtemény | Percenként egyszer. A feladatokat nem lehet percenként egynél többre futtatni. | Minden Azure-előfizetés legfeljebb 100 szabványos feladatot tartalmazhat. | Hozzáférés a Scheduler teljes szolgáltatáskészlet-készletéhez | 
| **Prémium szintű P10** | 50 feladat/gyűjtemény | Percenként egyszer. A feladatokat nem lehet percenként egynél többre futtatni. | Minden Azure-előfizetés akár 10 000 P10 prémium szintű feladatot is tartalmazhat. További gyűjteményekért <a href="mailto:wapteams@microsoft.com">vegye fel velünk a kapcsolatot</a>. | Hozzáférés a Scheduler teljes szolgáltatáskészlet-készletéhez |
| **Prémium szintű P20** | 1000 feladat/gyűjtemény | Percenként egyszer. A feladatokat nem lehet percenként egynél többre futtatni. | Minden Azure-előfizetés akár 5 000 P20 prémium szintű feladatot is tartalmazhat. További gyűjteményekért <a href="mailto:wapteams@microsoft.com">vegye fel velünk a kapcsolatot</a>. | Hozzáférés a Scheduler teljes szolgáltatáskészlet-készletéhez |
|||||| 

## <a name="pricing"></a>Díjszabás

A díjszabással kapcsolatos részletekért lásd a [Scheduler díjszabását](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Frissítési vagy visszalépési csomagok

Bármikor frissítheti vagy visszaállíthatja a feladatok gyűjtésének tervét a standard, P10 Premium és P20 prémium csomagokon keresztül.

## <a name="active-status-and-billing"></a>Aktív állapot és számlázás

A feladatütemezés mindig aktív, kivéve, ha a teljes Azure-előfizetés számlázási problémák miatt átmenetileg letiltott állapotba kerül. Bár egyetlen művelettel letilthatja a feladatütemezés összes feladatát, ez a művelet nem módosítja a feladatütemezés számlázási állapotát, így a feladatütemezés számlázása *is* megtörténik. Az üres feladatok gyűjtése aktívnak minősül, és számlázásra kerül.

Ahhoz, hogy a feladatok gyűjteménye ne legyen kiszámlázva, törölnie kell a begyűjtési feladatot.

## <a name="standard-billable-units"></a>Standard számlázandó egységek

A standard számlázható egység legfeljebb 10 szabványos feladatot tartalmazhat. Mivel a standard szintű feladatok gyűjteménye legfeljebb 50 feladattal rendelkezhet, az egyik standard számlázási egység lehetővé teszi, hogy az Azure-előfizetése akár 500-es feladattal rendelkezzen, akár havonta csaknem 22 *millió* feladat-végrehajtással. Ez a lista azt ismerteti, hogyan számítjuk fel a számlázást a standard szintű feladatütemezés különböző száma alapján:

* 1 és 10 standard szintű feladatütemezés esetén egy standard számlázási egységért kell fizetnie. 

* 11 és 20 standard szintű feladatütemezés esetén két szabványos számlázási egységért kell fizetnie. 

* Ha 21 és 30 standard szintű beosztása van, akkor három standard számlázási egységért kell fizetnie, és így tovább.

## <a name="p10-premium-billable-units"></a>P10 prémium számlázható egységek

Az P10 prémium számlázható egység akár 10 000 P10 prémium szintű feladatot is tartalmazhat. Mivel a P10 Premium-feladatok gyűjteménye akár 50-es feladattal is rendelkezhet, az egyik P10 Premium számlázási egység lehetővé teszi, hogy az Azure-előfizetése akár 500 000-es feladattal rendelkezzen, vagy akár havonta csaknem 22 *milliárd* feladat-végrehajtásra. 

A prémium szintű P10 ugyanazokat a képességeket nyújtják, mint a standard Job Collections, de a sok feladattípust igénylő alkalmazások esetében olyan díjszabást kínálnak, amely nagyobb méretezhetőséget biztosít. Ez a lista azt ismerteti, hogyan történik a számlázás a P10 prémium szintű feladatok különböző száma alapján:

* Ha 1 és 10 000 P10 prémium szintű beosztással rendelkezik, akkor egy P10 Premium számlázási egységért kell fizetnie. 

* Ha 10 001 és 20 000 P10 Premium Job Collections között van, akkor 2 P10 prémium számlázási egységért kell fizetnie, és így tovább.

## <a name="p20-premium-billable-units"></a>P20 prémium számlázható egységek

Az P20 prémium számlázható egység akár 5 000 P20 prémium szintű feladatot is tartalmazhat. Mivel a P20 Premium-feladatok gyűjteménye akár 1 000-es feladattal is rendelkezhet, az egyik P20 Premium számlázási egység lehetővé teszi, hogy az Azure-előfizetése akár 5 000 000-es feladattal rendelkezzen, akár csaknem 220 *milliárd* feladat-végrehajtással havonta.

A prémium szintű P20 ugyanazokat a képességeket nyújtják, mint a prémium szintű P10, de több feladatot is támogatnak egy gyűjteményen belül, és a prémium szinthez képest nagyobb számú feladatot is megadhatnak, ami nagyobb méretezhetőséget biztosít.

## <a name="plan-comparison"></a>Terv összehasonlítása

* Ha több mint 100 standard felállása van (10 standard számlázási egység), akkor jobb megoldás, ha az összes feladatot egy prémium csomaggal látja el.

* Ha rendelkezik egy standard szintű feladatokkal és egy prémium szintű feladatokkal, akkor a számlázás egy standard számlázási egység *és* egy prémium szintű számlázási egység alapján történik.

  Az ütemező szolgáltatás a standard vagy a prémium szintű aktív Webhelycsoportok száma alapján számláz.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)