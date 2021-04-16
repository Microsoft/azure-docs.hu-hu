---
title: Verziótámogatási szabályzat – Azure Database for MySQL – Egykiszolgálós és rugalmas kiszolgáló (előzetes verzió)
description: A MySQL fő- és alverzióira vonatkozó szabályzatot ismerteti a Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389570"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL verziótámogatási szabályzat

Ez az oldal a Azure Database for MySQL házirendet ismerteti, és az Azure Database for MySQL – Egykiszolgálós és Azure Database for MySQL – Rugalmas kiszolgáló (előzetes verzió) üzembe helyezési módokra vonatkozik.

## <a name="supported--mysql-versions"></a>Támogatott MySQL-verziók

Azure Database for MySQL a [MySQL Community Edition](https://www.mysql.com/products/community/)kiadásból lett kifejlesztve az InnoDB tárolási motor használatával. A szolgáltatás a közösség által támogatott összes aktuális főverziót támogatja: MySQL 5.6, 5.7 és 8.0. A MySQL az X.Y.Z elnevezési sémát használja, ahol az X a főverzió, az Y az alverzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját.](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)

Azure Database for MySQL a MySQL következő fő- és alverzióit támogatja:

| Verzió | [Önálló kiszolgáló](overview.md) <br/> Aktuális alverzió |[Rugalmas kiszolgáló (előzetes verzió)](/azure/mysql/flexible-server/overview) <br/> Aktuális alverzió  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL 5.6-os verzió |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(Kivezetve) | Nem támogatott|
|MySQL 5.7-es verzió | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL 8.0-s verzió | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> Az Egykiszolgálós telepítés lehetőségben egy átjáró irányítja át a kapcsolatokat a kiszolgálópéldányokhoz. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában. Ha az alkalmazásnak egy adott főverzióhoz (például 5.7-es vagy 8.0-s verzió) kell csatlakoznia, ezt a kiszolgáló kapcsolati sztringje portjának módosításával tudja megtenni az itt található dokumentációban leírtak [szerint.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> A MySQL 5.6-osát 2021 febuary óta kivezették egykiszolgálós gépről. 2021. szeptember 1-től kezdve nem hozhat létre új 5.6-os Azure Database for MySQL - Egykiszolgálós üzembe helyezési lehetőséggel. Ugyanakkor képes lesz időponthoz időben helyreállítani és olvasási replikákat létrehozni a meglévő kiszolgálókhoz.

A visszavont verziókra vonatkozó verziótámogatási szabályzatot a [verziótámogatási szabályzat dokumentációjában olvashatja el.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Főverziók támogatása
A MySQL minden főverzióját támogatja a Azure Database for MySQL attól a dátumtól, amikor az Azure elkezdi támogatni a verziót, amíg a MySQL-közösség ki nem veszi a verziót a verziószámozási szabályzatban [megadottak szerint.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Alverzió támogatása
Azure Database for MySQL rendszeres karbantartás részeként a rendszer automatikusan elvégzi az alverziófrissítéseket az Azure előnyben részesített MySQL-verziójára. 

## <a name="major-version-retirement-policy"></a>Főverzió ki nyugdíjba vonulási szabályzata
Az alábbi táblázat a MySQL főverzióira vonatkozó kieső adatokra vonatkozó adatokat tartalmazza. A dátumok a [MySQL verziószámozási szabályzatát követik.](https://www.mysql.com/support/eol-notice.html)

| Verzió | Újdonságok | Azure-támogatás kezdő dátuma | Kieső dátum|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 2018. március 20. | 2021. február
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funkciók](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 2018. március 20. | 2023. október
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Szolgáltatások](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 2019. december 11. | 2026. április


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>A MySQL-motor visszavont verziói nem támogatottak a Azure Database for MySQL

Ha folytatja a kivezetett verzió futtatását, az egyes MySQL-adatbázisverziók kivezetett dátumának lejárati dátuma után vegye figyelembe a következő korlátozásokat:
- Mivel a közösség nem ad ki további hibajavításokat vagy biztonsági javításokat, a Azure Database for MySQL nem javítja ki a kivezetett adatbázismotort a hibák vagy biztonsági problémák elhárítása érdekében, és nem hoz egyéb biztonsági intézkedéseket a visszavont adatbázismotorral kapcsolatban. Az Azure azonban továbbra is elvégzi a gazdagép, az operációs rendszer, a tárolók és a szolgáltatáshoz kapcsolódó egyéb összetevők rendszeres karbantartását és javításait.
- Ha bármilyen, a MySQL-adatbázissal kapcsolatos támogatási probléma előfordulhat, előfordulhat, hogy nem tudunk támogatást nyújtani Önnek. Ilyen esetekben frissítenie kell az adatbázist, hogy bármilyen támogatást nyújtsunk Önnek.
- A visszavont verzióhoz nem hozhat létre új adatbázis-kiszolgálókat. Ugyanakkor képes lesz időponthoz időben helyreállítani és olvasási replikákat létrehozni a meglévő kiszolgálókhoz.
- Előfordulhat, hogy a Azure Database for MySQL által fejlesztett új szolgáltatási képességek csak a támogatott adatbázis-kiszolgáló verziók számára érhetők el.
- Az üzemidőre vonatkozó szolgáltatói feltételek kizárólag a Azure Database for MySQL kapcsolatos problémákra vonatkoznak, az adatbázismotorral kapcsolatos hibák által okozott állásidőkre nem.  
- Abban az esetben, ha a MySQL adatbázismotor kivezetett verziójában azonosított biztonsági rés súlyos fenyegetést jelent a szolgáltatásra nézve, az Azure úgy is dönt, hogy leállítja az adatbázis-kiszolgáló számítási csomópontját, hogy először a szolgáltatás biztonságát biztosítsa. A rendszer megkéri, hogy frissítse a kiszolgálót, mielőtt online állapotba hozné. A frissítési folyamat során az adatok védelme mindig a szolgáltatáson végrehajtott automatikus biztonsági mentésekkel történik, amelyek szükség esetén a régebbi verzióra való visszaállításhoz használhatók. 



## <a name="next-steps"></a>Következő lépések
- Lásd: Azure Database for MySQL – Egykiszolgálós [támogatott verziók](./concepts-supported-versions.md)
- Lásd: Azure Database for MySQL – Rugalmas kiszolgáló (előzetes verzió) [által támogatott verziók](flexible-server/concepts-supported-versions.md)
- A frissítések végrehajtásához [lásd:](./concepts-migrate-dump-restore.md) MySQL dump and restore (MySQL-memóriakép és -visszaállítás).
