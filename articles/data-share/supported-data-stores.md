---
title: Az Azure Data Share támogatott adattárai
description: Ismerje meg a támogatott adattárakat a Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812623"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Az Azure Data Share támogatott adattárai

Azure Data Share és rugalmas adatmegosztást biztosít, beleértve a különböző adattáraikból és adattáraikba való megosztás képességét. Az adatszolgáltatók megoszthatnak adatokat egy adott típusú adattárból, az adat felhasználók pedig adattárat választhatnak az adatok fogadására. 

Ebben a cikkben megismeri az Azure-adattárak gazdag készletét, amelyek Azure Data Share támogatottak. Azt is megtudhatja, hogyan kombinálják az adatszolgáltatók és az adatszolgáltatók a különböző adattárakat. 

## <a name="supported-data-stores"></a>Támogatott adattárak 

Az alábbi táblázat a támogatott adattárakat Azure Data Share ismerteti. 

| Adattár | Megosztás teljes pillanatképek alapján | Megosztás növekményes pillanatképek alapján | Megosztás a helyén 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| 1. generációs Azure Data Lake Storage |✓ |✓ | |
| 2. generációs Azure Data Lake Storage |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (korábban Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics (munkaterület) dedikált SQL-készlet |✓ | | |
| Azure Adatkezelő | | |✓ |

## <a name="data-store-support-matrix"></a>Adattár támogatási mátrixa

Azure Data Share lehetővé teszi, hogy az adat fogyasztói adattárat válasszanak az adatok elfogadásához. A megosztott adatok például Azure SQL Database, Azure Data Lake Storage Gen2, Azure SQL Database vagy Azure Synapse Analytics. Amikor az ügyfelek beállítják a fogadó adat megosztását, kiválaszthatja a formátumot az adatok fogadására. 

Az alábbi táblázat ismerteti az adat felhasználók által az adat megosztásának elfogadásakor és konfigurálásakor kiválasztható kombinációkat és lehetőségeket. További információ: [Adatkészlet-leképezés konfigurálása.](how-to-configure-mapping.md)

| Adattár | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (korábban SQL Data Warehouse) | Synapse Analytics (munkaterület) dedikált SQL-készlet | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (korábban SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (munkaterület) dedikált SQL-készlet | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Megosztás tárfiókból
Azure Data Share támogatja a fájlok, mappák és fájlrendszerek megosztását a Azure Data Lake Storage Gen1 és Azure Data Lake Storage Gen2. Emellett támogatja a blobok, mappák és tárolók megosztását Azure Blob Storage. A blokkblobok, hozzáfűző és lapblobok megoszthatóak, és blokkblobokként vannak megkapva.

Amikor a fájlrendszerek, tárolók vagy mappák meg vannak osztva pillanatkép-alapú megosztásban, az adat felhasználók választhatnak a megosztott adatok teljes másolatának készítése között. Vagy a növekményes pillanatkép funkcióval csak az új fájlokat vagy a frissített fájlokat másolhatja. 

A növekményes pillanatkép a fájlok utolsó módosítási időpontja alapján készült. Azok a meglévő fájlok, amelyek neve megegyezik a fogadott adatok fájljaival, felül vannak írva egy pillanatképben. A forrásból törölt fájlok nem törlődnek a célon. 

További információ: [Share and receive data from Azure Blob Storage and Azure Data Lake Storage](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>Megosztás SQL-alapú forrásból
Azure Data Share támogatja a táblák és nézetek megosztását a Azure SQL Database és Azure Synapse Analytics (korábban Azure SQL Data Warehouse). Támogatja a táblák megosztását a Azure Synapse Analytics (munkaterület) dedikált SQL-készletből. A Azure Synapse Analytics (munkaterület) kiszolgáló nélküli SQL-készletből való megosztás jelenleg nem támogatott. 

Az adat felhasználók dönthetnek úgy, hogy csv-fájlként Azure Data Lake Storage Gen2 vagy Azure Blob Storage csv-fájlként vagy parquet-fájlként fogadják el az adatokat. Az adatokat táblákként is elfogadhatja a Azure SQL Database és Azure Synapse Analytics.

Amikor a felhasználók adatokat fogadnak Azure Data Lake Storage Gen2 vagy Azure Blob Storage, a teljes pillanatképek felülírják a célfájl tartalmát, ha a fájl már létezik. Amikor adatokat kap egy táblába, és a céltábla még nem létezik, a Azure Data Share létrehoz egy SQL-táblát a forrássémával. Ha egy céltábla már létezik, és ugyanaz a neve, a rendszer eldobja és felülírja a legújabb teljes pillanatképpel. A növekményes pillanatképek jelenleg nem támogatottak.

További információ: Adatok megosztása és fogadása a [Azure SQL Database és Azure Synapse Analytics.](how-to-share-from-sql.md)

## <a name="share-from-data-explorer"></a>Megosztás Adatkezelő
Azure Data Share támogatja az adatbázisok megosztását a helyről a Azure Data Explorer fürtökről. Az adatszolgáltató az adatbázis vagy a fürt szintjén oszthatja meg a megosztást. 

Ha az adatok az adatbázis szintjén vannak megosztva, az adat felhasználók csak az adatszolgáltató által megosztott adatbázisokhoz férhetnek hozzá. Ha egy szolgáltató fürtszinten osztja meg az adatokat, az adat felhasználók a szolgáltató fürtje összes adatbázisához hozzáférhetnek, beleértve az adatszolgáltató által létrehozott jövőbeli adatbázisokat is.

A megosztott adatbázisok eléréséhez az adat-fogyasztóknak saját Azure Data Explorer szükségük. A fürtjüknek ugyanabban az Azure-adatközpontban kell lennie, mint az adatszolgáltató Azure Data Explorer fürtnek. 

Megosztási kapcsolat létrejöttekor a Azure Data Share szimbolikus kapcsolatot hoz létre a szolgáltató fürte és a fogyasztó fürte között. A forrásfürtbe kötegelt mód használatával betöltött adatok néhány percen belül megjelennek a célfürtön.

További információ: Adatok megosztása és [fogadása a Azure Data Explorer.](/azure/data-explorer/data-share) 

## <a name="next-steps"></a>Következő lépések

Ha meg szeretne ismerkedni az adatok megosztásának elkezdődőjéhez, folytassa az Adatok [megosztása oktatóanyagban.](share-your-data.md)
