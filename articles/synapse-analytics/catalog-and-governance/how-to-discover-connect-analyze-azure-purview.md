---
title: Az Azure hatáskörébe tartozó szinapszisok felderítése, összekötése és megismerése
description: Útmutató az adatfelderítéshez, a kapcsolódáshoz és a szinapszisok megismeréséhez
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 79090bfbf08cde3f18b1ca734b8af22d16e7e0ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628662"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Az Azure hatáskörébe tartozó szinapszisok felderítése, összekötése és megismerése 

Ebből a dokumentumból megtudhatja, milyen típusú interakciókat kell végrehajtania, amikor egy Azure-beli hatáskörébe tartozó fiókot Szinapszisba regisztrál. 

## <a name="prerequisites"></a>Előfeltételek 

- [Azure-beli hatáskörébe tartozó fiók](../../purview/create-catalog-portal.md) 
- [Szinapszis-munkaterület](../quickstart-create-workspace.md) 
- [Azure-beli hatáskörébe tartozó fiók összekötése a Szinapszisba](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Az Azure-beli hatáskörébe használata a Szinapszisban 

Az Azure-beli hatáskörébe a Szinapszisban való használatához hozzáféréssel kell rendelkeznie ehhez a hatáskörébe tartozó fiókhoz. A szinapszis átadja a hatáskörébe tartozó engedélyeit. Ha például egy kurátori jogosultsági szerepkörrel rendelkezik, az Azure hatáskörébe tartozó metaadatokat is szerkesztheti. 

### <a name="data-discovery-search-datasets"></a>Adatfelderítés: az adathalmazok keresése 

Az Azure hatáskörébe tartozó, regisztrált és beolvasott adatmennyiség felderítéséhez használhatja a szinapszis munkaterület felső középső sávján található keresési sávot. Győződjön meg arról, hogy az Azure hatáskörébe kattint az összes szervezeti adattal való kereséshez. 

[![Azure hatáskörébe tartozó eszközök keresése](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure-beli hatáskörébe tartozó műveletek 

Itt látható a Szinapszisban elérhető Azure-beli hatáskörébe tartozó funkciók listája: 
- A metaadatok **áttekintése** 
- A metaadatok **sémájának** megtekintése és szerkesztése besorolásokkal, szószedeti feltételekkel, adattípusokkal és leírásokkal 
- A függőségek megismeréséhez és a hatás elemzéséhez tekintse meg a **Lineage** nézetet. További információ: [Lineage](../../purview/catalog-lineage-user-guide.md)
- Megtekintheti és szerkesztheti a **névjegyeket** , amelyekkel megtudhatja, ki a tulajdonos vagy szakértő egy adatkészleten 
- Egy adott adatkészlet hierarchikus függőségeinek megismeréséhez **kapcsolódik** . Ez a felhasználói élmény hasznos az adathierarchia tallózásához.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>A szinapszis-erőforrásokkal rendelkező adatkészleteken végrehajtható műveletek 

### <a name="connect-data-to-synapse"></a>Adatkapcsolat a Szinapszishoz 

- Létrehozhat egy **új társított szolgáltatást** a szinapszishoz. Ehhez a művelethez az szükséges, hogy a rendszer átmásolja az adatait a Szinapszisba, vagy megossza azokat az adatközpontban (támogatott adatforrások esetén, például ADLSg2) 
- Objektumok, például fájlok, mappák vagy táblák esetében közvetlenül létrehozhat egy **új integrációs adatkészletet** , és kihasználhat egy meglévő társított szolgáltatást, ha már létrehozták 

Még nem következtethető ki, hogy van-e meglévő társított szolgáltatás vagy integrációs adatkészlet. 

###  <a name="develop-in-synapse"></a>Fejlesztés a Szinapszisban 

Három műveletet végezhet el: **új SQL-parancsfájlt**, **új jegyzetfüzetet** és **új adatfolyamot**. 

Az **új SQL-szkripttel** a támogatás típusától függően a következőket teheti: 
- Tekintse meg az első 100 sort, hogy megértse az adatalakzatokat. 
- Külső tábla létrehozása a szinapszis SQL Database-ből 
- Az adatgyűjtés egy szinapszis SQL Database-adatbázisba 
 
Az **új jegyzetfüzettel** a következőket teheti: 
- Adatgyűjtés Spark-DataFrame 
- Hozzon létre egy Spark-táblázatot (ha ezt a parketta formátuma teszi meg, létrehoz egy kiszolgáló nélküli SQL Pool-táblázatot is). 
 
Az **új adatfolyam** használatával olyan integrációs adatkészletet hozhat létre, amely egy adatfolyam-folyamat forrásaként használható. Az adatfolyam a kód nélküli fejlesztői képesség az adatok átalakításához. További információ az [adatáramlás használatáról a szinapszisban](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>További lépések 

- [Azure szinapszis-eszközök regisztrálása és vizsgálata az Azure hatáskörébe](../../purview/register-scan-azure-synapse-analytics.md)
- [Adatkeresés az Azure-beli hatáskörébe Data Catalog](../../purview/how-to-search-catalog.md)
