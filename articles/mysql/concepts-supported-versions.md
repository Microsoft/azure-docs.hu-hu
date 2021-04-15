---
title: Támogatott verziók – Azure Database for MySQL
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verziói támogatottak a Azure Database for MySQL szolgáltatásban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1be15c16a1897797326ea869c34c3590ffb07691
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363869"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Az Azure Database for MySQL-kiszolgáló támogatott verziói

Azure Database for MySQL a [MySQL Community Edition](https://www.mysql.com/products/community/)kiadásból lett kifejlesztve az InnoDB tárolási motor használatával. A szolgáltatás a közösség által támogatott összes aktuális főverziót támogatja: MySQL 5.6, 5.7 és 8.0. A MySQL az X.Y.Z elnevezési sémát használja, ahol az X a főverzió, az Y az alverzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját.](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)



## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Csatlakozás adott MySQL-verziót futtató átjárócsomóponthoz

Az Egykiszolgálós telepítés lehetőségben egy átjáró irányítja át a kapcsolatokat a kiszolgálópéldányokhoz. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában. Tekintse [át a kapcsolati](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture#connectivity-architecture) architektúrát, ha többet szeretne megtudni az átjárókról Azure Database for MySQL szolgáltatásarchitektúrában.

Mivel a Azure Database for MySQL támogatja az 5.6-os, 5.7-es és 8.0-s főverziót, az Azure Database for MySQL alapértelmezett 3306-os port a MySQL-ügyfél 5.6-os (legkevésbé gyakori nevező) verzióját futtatja a három támogatott főverzió kiszolgálóihoz való csatlakozás támogatásához. Ha azonban az alkalmazásnak egy adott főverzióhoz (például 5.7-es vagy 8.0-s verzió) kell csatlakoznia, ezt a kiszolgálói kapcsolati sztring portjának módosításával használhatja.

Az Azure Database for MySQL szolgáltatásban az átjárócsomópontok a 3308-as portot figyelik az 5.7-es és a 3309-es portot a v8.0-s ügyfelek esetében. Más szóval, ha az 5.7-es átjáró ügyfeléhez szeretne csatlakozni, használja a teljes kiszolgálónevet és a 3308-as portot a kiszolgálóhoz való csatlakozáshoz az ügyfélalkalmazásból. Hasonlóképpen, ha csatlakozni szeretne a 8.0-s átjáró ügyfeléhez, használhatja a teljes kiszolgálónevet és a 3309-es portot a kiszolgálóhoz való csatlakozáshoz. A további átláthatóság érdekében tekintse meg az alábbi példát.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Példa különböző átjárók mysql-verzióival való csatlakozásra":::


## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL a MySQL következő fő- és alverzióit támogatja:


| Verzió | [Önálló kiszolgáló](overview.md) <br/> Aktuális alverzió |[Rugalmas kiszolgáló (előzetes verzió)](/azure/mysql/flexible-server/overview) <br/> Aktuális alverzió  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 5.6-os verzió |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (Kivezetve) | Nem támogatott|
|MySQL 5.7-es verzió | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 8.0-s verzió | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

A visszavont verziókra vonatkozó verziótámogatási szabályzatot a [verziótámogatási szabályzat dokumentációjában olvashatja el.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítéseit. Például: 5.7.20–5.7.21.  

A főverziók frissítését a szolgáltatás jelenleg a MySQL 5.6-os verziójáról az 5.7-es verzióra való frissítéshez támogatja. További részletekért tekintse meg a [főverziók frissítésének elvégzését.](how-to-major-version-upgrade.md) Ha az 5.7-esről a 8.0-s verzióra szeretne frissíteni, javasoljuk, hogy végezzen memóriaképet és visszaállítást egy olyan kiszolgálón, amely az új motorverzióval lett létrehozva. [](./concepts-migrate-dump-restore.md)

## <a name="next-steps"></a>Következő lépések

- A verziószámozási szabályzat Azure Database for MySQL ebben a [dokumentumban talál további információt.](concepts-version-policy.md)
- A szolgáltatásszinten alapuló konkrét erőforráskvótákról és korlátozásokról a Szolgáltatási szintek oldalon [talál további információt.](./concepts-pricing-tiers.md) 
