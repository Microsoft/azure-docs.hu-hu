---
title: Adatok felderítése, csatlakoztatása és felfedezése a Synapse-ban az Azure Purview használatával
description: Útmutató az adatok felderítéséhez, csatlakoztatásukhoz és feltárásukhoz a Synapse-ban
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567893"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Adatok felderítése, csatlakoztatása és felfedezése a Synapse-ban az Azure Purview használatával 

Ebben a dokumentumban megismerheti az Azure Purview-fiók Synapse-regisztrációja során végrehajtható interakciók típusát. 

## <a name="prerequisites"></a>Előfeltételek 

- [Azure Purview-fiók](../../purview/create-catalog-portal.md) 
- [Synapse-munkaterület](../quickstart-create-workspace.md) 
- [Azure Purview-fiók csatlakoztatása a Synapse-hez](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Az Azure Purview használata a Synapse-ban 

Az Azure Purview synapse-beli használatához hozzá kell férni ehhez a Purview-fiókhoz. A Synapse átadja a Purview engedélyt. Ha például egy curator jogosultsági szerepköre van, szerkesztheti az Azure Purview által beolvasott metaadatokat. 

### <a name="data-discovery-search-datasets"></a>Adatfelderítés: adatkészletek keresése 

Az Azure Purview által regisztrált és beolvasott adatok felderítéséhez használhatja a Synapse-munkaterület tetején található keresősávot. Győződjön meg arról, hogy az Azure Purview lehetőséget választja az összes szervezeti adat kereséséhez. 

[![Azure Purview-eszközök keresése](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview-műveletek 

Az alábbi lista a Synapse-ban elérhető Azure Purview-funkciókat tartalmazza: 
- **A** metaadatok áttekintése 
- A metaadatok **sémájának** megtekintése és szerkesztése besorolásokkal, szószedetekkel, adattípusokkal és leírásokkal 
- A **leszúszás megtekintése** a függőségek és a hatáselemzés megtekintéséhez. További információ: [leszúslatás](../../purview/catalog-lineage-user-guide.md)
- A kapcsolattartók **megtekintése és szerkesztése** annak megtekintéséhez, hogy ki egy adatkészlet tulajdonosa vagy szakértője 
- **Egy** adott adatkészlet hierarchikus függőségeinek a rendszerével kapcsolatos. Ez a felhasználói élmény hasznos lehet az adathierarchia böngészéséhez.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Synapse-erőforrásokkal adatkészletek végrehajtásához szükséges műveletek 

### <a name="connect-data-to-synapse"></a>Adatok csatlakoztatása a Synapse-hoz 

- Létrehozhat egy új **összekapcsolt szolgáltatást** a Synapse-hoz. Ez a művelet szükséges ahhoz, hogy adatokat másoljon a Synapse-be, vagy az adatközpontban legyen (olyan támogatott adatforrások esetén, mint az ADLSg2) 
- Az olyan objektumokhoz, mint a fájlok, mappák vagy táblák, közvetlenül létrehozhat egy új **integrációs adatkészletet,** és egy már létrehozott meglévő csatolt szolgáltatást is kihasználhat 

Még nem tudjuk kiveszni, hogy van-e meglévő csatolt szolgáltatás vagy integrációs adatkészlet. 

###  <a name="develop-in-synapse"></a>Fejlesztés a Synapse-ban 

Három műveletet hajthat végre: **Új SQL-szkript,** Új **jegyzetfüzet** és Új **Adatfolyam.** 

Az **Új SQL-szkript használatával** a támogatás típusától függően a következő lehetőségeket használhatja: 
- Tekintse meg az első 100 sort, hogy megértse az adatok alakját. 
- Külső tábla létrehozása Synapse SQL adatbázisból 
- Adatok betöltése egy Synapse SQL adatbázisba 
 
Az **Új jegyzetfüzettel** a következő lehetőségeket használhatja: 
- Adatok betöltése Spark DataFrame-be 
- Hozzon létre egy Spark-táblát (ha parquet formátummal használja, akkor egy kiszolgáló nélküli SQL-készlettáblát is létrehoz). 
 
A **New data flow (Új adatfolyam)** segítségével létrehozhat egy integrációs adatkészletet, amely felhasználható egy adatforrásra egy adatfolyam-folyamatban. Az adatfolyam kód nélküli fejlesztői képesség az adatátalakítás végrehajtásához. További információ az adatfolyam [Synapse-ban való használatával kapcsolatban.](../quickstart-data-flow.md)

##  <a name="nextsteps"></a>Következő lépések 

- [Az azure-beli Azure Synapse regisztrálása és beolvasása](../../purview/register-scan-azure-synapse-analytics.md)
- [Adatok keresése az Azure Purview-Data Catalog](../../purview/how-to-search-catalog.md)
