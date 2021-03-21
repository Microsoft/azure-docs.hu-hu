---
title: Az Azure Data Share támogatott adattárai
description: Ismerje meg az Azure-adatmegosztásban használható adattárakat.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963679"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Az Azure Data Share támogatott adattárai

Az Azure-beli adatmegosztás nyitott és rugalmas adatmegosztást biztosít, többek között a különböző adattárakból történő megosztási képességet is beleértve. Az adatszolgáltatók megoszthatnak egy adattárolóból származó adatait, és az adatok felhasználói az adattárakat is kiválaszthatják az adatok fogadásához. 

Ebből a cikkből megtudhatja, milyen Azure-adattárakat támogat az Azure-adatmegosztás. Arról is tájékozódhat, hogy az adatszolgáltatók és az adatfogyasztók hogyan kombinálhatók különböző adattárakkal. 

## <a name="supported-data-stores"></a>Támogatott adattárak 

Az alábbi táblázat azokat az adattárakat ismerteti, amelyeket az Azure-beli adatmegosztás támogat. 

| Adattár | Megosztás teljes Pillanatképek alapján | Megosztás növekményes Pillanatképek alapján | Megosztás helyben 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| 1. generációs Azure Data Lake Storage |✓ |✓ | |
| 2. generációs Azure Data Lake Storage |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (korábban Azure SQL Data Warehouse) |✓ | | |
| Azure szinapszis Analytics (munkaterület) dedikált SQL-készlet |✓ | | |
| Azure Adatkezelő | | |✓ |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Az Azure-adatmegosztás lehetővé teszi, hogy a felhasználók adattárakat válasszanak az adatfogadáshoz. Előfordulhat például, hogy a Azure SQL Database által megosztott adatok Azure Data Lake Storage Gen2ba, Azure SQL Databaseba vagy az Azure szinapszis Analyticsbe érkeznek. Amikor az ügyfelek egy fogadó adatmegosztást állítanak be, kiválaszthatják, hogy milyen formátumban kapják meg az adatgyűjtést. 

Az alábbi táblázat ismerteti azokat a kombinációkat és lehetőségeket, amelyeket az adatfogyasztók választhatnak, amikor elfogadják és konfigurálják az adatmegosztást. További információ: adatkészlet- [hozzárendelés konfigurálása](how-to-configure-mapping.md).

| Adattár | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Szinapszis Analytics (korábban SQL Data Warehouse) | A szinapszis Analytics (munkaterület) dedikált SQL-készlete | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Szinapszis Analytics (korábban SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| A szinapszis Analytics (munkaterület) dedikált SQL-készlete | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Megosztás egy Storage-fiókból
Az Azure-beli adatmegosztás támogatja a fájlok, mappák és fájlrendszerek megosztását Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Emellett támogatja a Blobok, mappák és tárolók megosztását az Azure Blob Storageból. Jelenleg csak a blokk Blobok támogatottak. 

Ha a fájlrendszer, a tárolók vagy a mappák a pillanatkép-alapú megosztásban vannak megosztva, az adatok felhasználói dönthetnek úgy, hogy teljes másolatot készítenek a megosztott adatokról. Vagy használhatják a növekményes pillanatképet is, amely csak az új fájlokat és a frissített fájlokat másolja. 

A növekményes Pillanatképek a fájlok utolsó módosításának időpontján alapulnak. A meglévő fájlok neve megegyezik a kapott adatokban található fájlokkal, a rendszer felülírja a pillanatképeket. A forrásból törölt fájlok nem törlődnek a célhelyen. 

További információ: az [Azure Blob Storage és Azure Data Lake Storage adatainak megosztása és fogadása](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Az Azure-beli adatmegosztás támogatja a táblák és nézetek megosztását a Azure SQL Database és az Azure szinapszis Analyticsből (korábban Azure SQL Data Warehouse). Támogatja az Azure szinapszis Analytics (munkaterület) dedikált SQL-készletből származó táblák megosztását. Az Azure szinapszis Analytics (munkaterület) kiszolgáló nélküli SQL-készletének megosztása jelenleg nem támogatott. 

Az adatfogyasztók dönthetnek úgy, hogy elfogadják az adatAzure Data Lake Storage Gen2 vagy az Azure Blob Storage CSV-fájlként vagy a parketta-fájlként. Az Azure SQL Database és az Azure szinapszis Analytics szolgáltatásban táblázatokként is elfogadhatják az adattáblákat.

Ha a felhasználók az Azure Data Lake Storage Gen2 vagy az Azure Blob Storageba fogadnak el adatmennyiséget, a teljes Pillanatképek felülírják a célfájl tartalmát, ha a fájl már létezik. Amikor az Adatfogadás egy táblába történik, és a céltábla még nem létezik, az Azure-beli adatmegosztás egy SQL-táblázatot hoz létre a forrás sémájának használatával. Ha egy céltábla már létezik, és a neve megegyezik, a rendszer eldobta és felülírja a legújabb teljes pillanatképet. A növekményes Pillanatképek jelenleg nem támogatottak.

További információ: [a Azure SQL Database és az Azure szinapszis Analytics adatainak megosztása és fogadása](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Megosztás Adatkezelő
Az Azure-beli adatmegosztás lehetővé teszi az adatbázisok helyben történő megosztását az Azure Adatkezelő-fürtökön. Az adatszolgáltató az adatbázis vagy a fürt szintjén is megosztható. 

Az adatbázis szintjén megosztva az adatfogyasztók csak az adatszolgáltató által megosztott adatbázisokhoz férhetnek hozzá. Ha a szolgáltató fürt szintjén osztja meg az adatait, az adatfogyasztók hozzáférhetnek a szolgáltató fürtje összes adatbázisához, beleértve az adatszolgáltató által létrehozott jövőbeli adatbázisokat is.

A megosztott adatbázisok eléréséhez az adatfogyasztóknak saját Azure Adatkezelő-fürtre van szükségük. A fürtnek ugyanabban az Azure-adatközpontban kell lennie, mint az adatszolgáltató Azure Adatkezelő-fürtje. 

Megosztási kapcsolat létrehozásakor az Azure-beli adatmegosztás szimbolikus hivatkozást hoz létre a szolgáltató fürtje és a fogyasztó fürtje között. A forrás-fürtön a Batch mód használatával betöltött adat néhány percen belül megjelenik a cél fürtön.

További információ: [adatok megosztása és fogadása az Azure Adatkezelőból](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.
