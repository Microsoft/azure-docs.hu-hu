---
title: Az adatok kezelése az Azure Cosmos DB explorer használatával
description: Az Azure Cosmos DB explorer egy önálló webes felület, amely lehetővé teszi, hogy az Azure Cosmos DB-ben tárolt adatok megtekintése és kezelése.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: dech
ms.openlocfilehash: d50bf7d865cb286f2a1421156b477dc92dc978d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889458"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>Adatok kezelése az Azure Cosmos Explorerrel 

Az Azure Cosmos DB explorer egy önálló webes felület, amely lehetővé teszi, hogy az Azure Cosmos DB-ben tárolt adatok megtekintése és kezelése. Az Azure Cosmos DB explorer megegyezik a meglévő **adatkezelő** lap, amely az Azure Portalon érhető el, az Azure Cosmos DB-fiók létrehozásakor. A meglévő adatkezelő keresztül Azure Cosmos DB explorer főbb előnyei a következők:

* A teljes képernyős érdekében az adatok, a lekérdezések futtatását, a tárolt eljárások, eseményindítók megtekintéséhez rendelkezik, és megtekintheti az eredményeiket.  

* Ideiglenes vagy állandó olvasási vagy írási-olvasási hozzáférést biztosíthat a fiókot, és annak gyűjteményeiről más felhasználóknak, akik nem rendelkeznek hozzáféréssel az Azure portal vagy az előfizetés számára.  

* A lekérdezési eredményeket megoszthatja más felhasználókkal, akik nem rendelkeznek hozzáféréssel az Azure portal vagy előfizetésbe.  

## <a name="access-azure-cosmos-db-explorer"></a>Hozzáférés az Azure Cosmos DB explorer

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com/). 

2. A **összes erőforrás**, keresse meg és nyissa meg az Azure Cosmos DB-fiók, jelölje be kulcsokat, és másolja a **elsődleges kapcsolati karakterlánc**.  

3. Lépjen a https://cosmos.azure.com/, illessze be a kapcsolati karakterláncot, és válassza ki **Connect**. A kapcsolati karakterlánc használatával is elérheti az Azure Cosmos DB explorer minden olyan alkalommal korlátok nélkül.  

   Ha azt szeretné, hogy az Azure Cosmos DB-fiók ideiglenes hozzáférést biztosíthat más felhasználók, az olvasási és írási és olvasási hozzáférés URL-címek használatával megteheti. 

4. Nyissa meg a **adatkezelő** panelen válassza ki **Megnyitás teljes képernyős**. Az előugró párbeszédpanelen, megtekintheti a két URL-címek – hozzáférés **olvasási és írási** és **olvasási**. Az URL-címek lehetővé teszik az Azure Cosmos DB-fiókja átmenetileg megosztása más felhasználókkal. A fiókhoz való hozzáférés lejár, 24 órában, ami után újra egy új hozzáférési URL-cím vagy a kapcsolati karakterlánc használatával. 

   **Olvasási és írási** – Ha az olvasási és írási URL-címet, megoszthatja más felhasználókkal, megtekintheti és módosíthatja az adatbázisok, gyűjtemények, lekérdezések és az adott fiókot társított egyéb erőforrások.

   **Olvasási** – Ha más felhasználókkal megosztott csak olvasható URL-CÍMÉT az adatbázisok, gyűjtemények, lekérdezések és az adott fiókot társított egyéb erőforrások is megtekinthetik. Például ha meg szeretné osztani az eredményeket a lekérdezés a csapattársai, akiknek nincs hozzáférésük az Azure portal vagy az Azure Cosmos DB-fiókot, biztosíthat azokat az URL-címet.

   Válassza ki az nyissa meg a fiókot, és kattintson a kívánt hozzáférési **nyissa meg a**. Az megnyitása után az a felhasználói élményt, azonos, mivel a az Azure portal adatkezelő lap kellett.   

   ![Nyissa meg az Azure Cosmos DB-adatkezelőt](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>Ismert problémák

Jelenleg a **Megnyitás teljes képernyős** környezetet biztosít, amely lehetővé teszi, hogy megosztása ideiglenes olvasási és írási vagy olvasási hozzáférés jelenleg nem támogatott az Azure Cosmos DB Gremlin- és Table API-fiókok. A kapcsolati karakterláncot adja át az Azure Cosmos DB Explorer továbbra is megtekintheti a Gremlin- és Table API-fiók. 

## <a name="next-steps"></a>További lépések
Most, hogy megtanulhatta, hogyan kezelheti az adatokat, akkor is tovább az Azure Cosmos DB explorer használatának első lépései:

* Indítsa el a definiálása [lekérdezések](sql-api-query-reference.md) SQL-szintaxis használatával, és hajtsa végre [kiszolgáló kiszolgálóoldali programozása](stored-procedures-triggers-udfs.md) a tárolt eljárásokat, felhasználói függvények, eseményindítók. 
