---
title: A verzió támogatási szabályzata – Azure Database for MySQL – egyetlen kiszolgáló és egy rugalmas kiszolgáló (előzetes verzió)
description: A MySQL főbb és alverzióinak házirendjét ismerteti Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 9f34e1ff7134788d064a82ec7b646df915dbe24e
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462558"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL verzió támogatási szabályzata

Ez az oldal leírja a Azure Database for MySQL verziószámozási házirendjét, és alkalmazható az Azure Database for MySQL-Single Server és a Azure Database for MySQL-rugalmas kiszolgáló (előzetes verzió) üzembe helyezési módjaira.

## <a name="supported--mysql-versions"></a>Támogatott MySQL-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB Storage Engine használatával. A szolgáltatás a Közösség által támogatott összes jelenlegi főverziót támogatja, azaz a MySQL 5,6, 5,7 és 8,0. A MySQL az X. Y. Z elnevezési sémát használja, ahol az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Az egyetlen kiszolgálóból álló központi telepítési beállításban az átjáró a kiszolgálói példányokkal való kapcsolat átirányítására szolgál. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a MySQL következő fő és másodlagos verzióit támogatja:

| Verzió | Önálló kiszolgáló <br/> Aktuális alverzió |Rugalmas kiszolgáló (előzetes verzió) <br/> Aktuális alverzió  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 5,6-es verzió |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | Nem támogatott|
|MySQL 5,7-es verzió | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 8,0-es verzió | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|


## <a name="major-version-support"></a>Főverzió támogatása
A MySQL minden jelentős verzióját a Azure Database for MySQL fogja támogatni attól az időponttól kezdve, amikor az Azure megkezdi a verzió támogatását, egészen addig, amíg a MySQL-Közösség el nem távolítja a verziót, ahogy azt a [verziószámozási házirend](https://www.mysql.com/support/eol-notice.html)biztosítja.

## <a name="minor-version-support"></a>Másodlagos verzió támogatása
A Azure Database for MySQL a rendszeres karbantartás részeként automatikusan végrehajtja az Azure-beli elsődleges MySQL-verzióra való frissítést. 

## <a name="major-version-retirement-policy"></a>Főverzió kivezetési szabályzata
Az alábbi táblázat a MySQL főverzióinak kivonulási részleteit tartalmazza. A dátumok a [MySQL verziószámozási házirendjét](https://www.mysql.com/support/eol-notice.html)követik.

| Verzió | Újdonságok | Azure-támogatás kezdő dátuma | Nyugdíjazás dátuma|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | Március 20., 2018 | 2021. február
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | Március 20., 2018 | Október 2023
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Szolgáltatások](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | December 11., 2019 | Április 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>A kivont MySQL Engine-verziók nem támogatottak Azure Database for MySQL

Ha továbbra is a kivont verziót futtatja, az egyes MySQL-adatbázisok lejárati dátuma után vegye figyelembe a következő korlátozásokat:
- Mivel a Közösség nem szabadít fel semmilyen további hibajavítást vagy biztonsági javítást, Azure Database for MySQL nem fogja feljavítani a kivont adatbázismotor számára az esetleges hibákat vagy biztonsági problémákat, vagy egyéb módon biztonsági intézkedéseket hajt végre a kivont adatbázismotor tekintetében. Az Azure azonban továbbra is rendszeres karbantartást és javítást végez a gazdagép, az operációs rendszer, a tárolók és a szolgáltatásokkal kapcsolatos egyéb összetevők számára.
- Ha bármilyen támogatási probléma merülhet fel a MySQL-adatbázissal kapcsolatban, előfordulhat, hogy nem tudunk támogatást nyújtani Önnek. Ilyen esetekben frissítenie kell az adatbázist ahhoz, hogy támogatást nyújtson Önnek.
<!-- - You will not be able to create new database servers for the retired version. However, you will be able to perform point-in-time recoveries and create read replicas for your existing servers. -->
- A Azure Database for MySQL által fejlesztett új szolgáltatási képességek csak a támogatott adatbázis-kiszolgáló verziók esetében érhetők el.
- Az üzemidőre vonatkozó SLA-kat kizárólag Azure Database for MySQL szolgáltatással kapcsolatos problémákra, és nem az adatbázismotor által okozott hibák okozta állásidőre alkalmazza a rendszer.  
- Ha súlyos fenyegetést észlelt a szolgáltatás a kivont adatbázis-verzióban azonosított MySQL adatbázismotor biztonsági rése miatt, az Azure úgy dönthet, hogy leállítja az adatbázis-kiszolgáló számítási csomópontját, hogy először biztonságossá tegye a szolgáltatást. A rendszer a kiszolgáló online állapotba hozása előtt frissíti a kiszolgálót. A frissítési folyamat során a rendszer mindig a szolgáltatásban végzett automatikus biztonsági mentéssel védi az adatokat, amelyek igény szerint visszaállíthatók a régebbi verzióra. 



## <a name="next-steps"></a>Következő lépések
- Lásd: Azure Database for MySQL – egy kiszolgáló által [támogatott verziók](./concepts-supported-versions.md)
- Lásd: Azure Database for MySQL – rugalmas kiszolgáló (előzetes [verzió) támogatott verziók](flexible-server/concepts-supported-versions.md)
- A frissítések végrehajtásához tekintse meg a MySQL [-memóriaképet és a visszaállítást](./concepts-migrate-dump-restore.md) .
