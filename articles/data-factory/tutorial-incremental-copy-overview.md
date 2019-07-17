---
title: Adatok növekményes másolása az Azure Data Factory használatával | Microsoft Docs
description: Ezekből az oktatóanyagokból megtudhatja, hogyan másolhat adatokat növekményes módon egy forrásadattárból egy céladattárba. Az első kimásolja az adatokat egy táblából.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 87b5b30738451800da21736d7f139c4ba85ff998
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233691"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Növekményes módon betölti az adatokat egy forrásadattárból egy céladattárba

Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes (vagy delta-) betöltése egy kezdeti, teljes adatbetöltést követően. A jelen szakaszban található oktatóanyagokból különböző módszereket ismerhet meg az adatok növekményes betöltésére vonatkozóan az Azure Data Factory használatával.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Változásadatok betöltése küszöbérték használatával adatbázis
Ebben az esetben a forrásadatbázisban meghatároz egy vízjelet. A küszöbérték egy olyan oszlop, amely az utoljára frissített időbélyeget, vagy egy növekvő kulcsot tartalmaz. A változásbetöltési megoldás keretében a rendszer azokat az adatokat tölti be, amelyek megváltoztak egy régi és egy új küszöbérték között. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható: 

![Vízjel használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

A részletes utasításokhoz tekintse meg a következő oktatóanyagokat: 
- [Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
- [Adatok növekményes másolása a helyszíni SQL Server több táblájából az Azure SQL Database-be](tutorial-incremental-copy-multiple-tables-powershell.md)

A sablonok tekintse meg a következőket:
- [A vezérlő tábla változásadatok másolásához](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Változásadatok betöltése az SQL DB-ből a változáskövetési technológia használatával
A változáskövetési technológia az SQL Server és az Azure SQL Database szolgáltatás egyik egyszerű megoldása, amely hatékony változáskövetési mechanizmust biztosít az alkalmazások számára. Lehetővé teszi az alkalmazások számára a beszúrt, frissített vagy törölt adatok egyszerű azonosítását. 

A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![A változáskövetés használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Adatok növekményes másolása az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Csak a LastModifiedDate segítségével új és módosított fájlok betöltése
Csak a céltár LastModifiedDate használatával másolhatja az új és módosított fájlok. ADF megvizsgáljuk a forrás-tárolóból a fájlokat, azok LastModifiedDate által fájl szűrőt alkalmazza, és csak utolsó óta másolja az új és frissített fájlt a cél tároló.  Felhívjuk a figyelmét arra, ha lehetővé teszik az ADF vizsgálat hatalmas mennyiségű, fájlokat, de csak néhány fájlok másolása a cél, továbbra is hiányol, valamint időt vehet igénybe a hosszú időtartam fájlok ellenőrzése miatt.   

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Növekményes másolása a LastModifiedDate alapján az Azure Blob storage-ból az Azure Blob storage új és módosított fájlok](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

A sablonok tekintse meg a következőket:
- [Új fájlok másolása LastModifiedDate szerint](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Új fájlok betöltése az idő particionálni a fájl vagy mappa neve.
Új fájlok csak, ahol fájlok vagy mappák már timeslice adatokat a fájl vagy mappa neve (például /yyyy/mm/dd/file.csv) részeként particionálni idő másolhatja. Növekményes betöltés új fájlok a legtöbb teljesítmény módszere. 

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Növekményes másolása az új fájlok idő particionált mappa vagy fájl neve az Azure Blob storage-ból az Azure Blob storage-alapú](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
>[Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
