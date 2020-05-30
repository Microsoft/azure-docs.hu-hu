---
title: Az Adatmásolás növekményes másolása
description: Ezekből az oktatóanyagokból megtudhatja, hogyan másolhat adatokat növekményes módon egy forrásadattárból egy céladattárba. Az első kimásolja az adatokat egy táblából.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: f1fb209f44ae7922de87277100fbf55add184766
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194491"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Növekményes módon betölti az adatokat egy forrásadattárból egy céladattárba

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes (vagy delta-) betöltése egy kezdeti, teljes adatbetöltést követően. A jelen szakaszban található oktatóanyagokból különböző módszereket ismerhet meg az adatok növekményes betöltésére vonatkozóan az Azure Data Factory használatával.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Adatok betöltésének különbözete az adatbázisból vízjel használatával
Ebben az esetben a forrásadatbázisban meghatároz egy vízjelet. A küszöbérték egy olyan oszlop, amely az utoljára frissített időbélyeget, vagy egy növekvő kulcsot tartalmaz. A változásbetöltési megoldás keretében a rendszer azokat az adatokat tölti be, amelyek megváltoztak egy régi és egy új küszöbérték között. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható: 

![Vízjel használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

A részletes utasításokhoz tekintse meg a következő oktatóanyagokat: 
- [Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
- [Adatok növekményes másolása egy SQL Server-példány több táblájából a Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)

Sablonok esetében tekintse meg a következőket:
- [Változásadatok másolása vezérlőtáblával](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Betöltés az SQL-adatbázisból az Change Tracking technológia használatával
A változáskövetési technológia az SQL Server és az Azure SQL Database szolgáltatás egyik egyszerű megoldása, amely hatékony változáskövetési mechanizmust biztosít az alkalmazások számára. Lehetővé teszi az alkalmazások számára a beszúrt, frissített vagy törölt adatok egyszerű azonosítását. 

A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![A változáskövetés használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Adatok növekményes másolása az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Új és módosított fájlok betöltése csak a LastModifiedDate használatával
Az új és módosított fájlokat csak a LastModifiedDate használatával másolhatja a célhelyre. Az ADF megvizsgálja az összes fájlt a forrás-áruházból, alkalmazza a LastModifiedDate, és csak az új és frissített fájlt másolja át a célhelyre való legutóbbi idő óta.  Kérjük, vegye figyelembe, hogy ha lehetővé teszi, hogy az ADF nagy mennyiségű fájlt helyezzen el, de csak néhány fájl másolását kívánja átmásolni, akkor a fájl-ellenőrzés miatt is hosszú időt kell várnia.   

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Új és módosított fájlok növekményes másolása az Azure Blob Storage-ból az Azure Blob Storage-ba való LastModifiedDate alapján](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Sablonok esetében tekintse meg a következőket:
- [Új fájlok másolása az utolsó módosítás dátuma szerint](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Új fájlok betöltése csak a Time particionált mappa vagy fájlnév használatával.
Csak az új fájlokat másolhatja, ahol a fájlok vagy mappák már időben particionálva vannak a fájl-vagy timeslice részeként (például/yyyy/MM/DD/file.csv). Ez a legnagyobb teljesítményű megközelítés az új fájlok növekményes betöltéséhez. 

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Új fájlok növekményes másolása az Azure Blob Storage-ból az Azure Blob Storage-ba, az idő particionált mappája vagy fájlneve alapján](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
>[Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
