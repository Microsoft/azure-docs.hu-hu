---
title: Verziószámozási házirend – Azure Database for PostgreSQL – egyetlen kiszolgáló és egy rugalmas kiszolgáló (előzetes verzió)
description: A Azure Database for PostgreSQL-egyetlen kiszolgálón található postgres fő és alverzióinak házirendjét ismerteti.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518419"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL verziószámozási szabályzat

Ez az oldal leírja a Azure Database for PostgreSQL verziószámozási házirendjét, és alkalmazható az Azure Database for PostgreSQL-Single Server és a Azure Database for PostgreSQL-rugalmas kiszolgáló (előzetes verzió) üzembe helyezési módjaira.

## <a name="supported--postgresql-versions"></a>Támogatott PostgreSQL-verziók

A Azure Database for PostgreSQL a következő adatbázis-verziókat támogatja.

| Verzió | Önálló kiszolgáló | Rugalmas kiszolgáló (előzetes verzió) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| *PostgreSQL 9,5 (kivonva)* | X |  |

## <a name="major-version-support"></a>Főverzió támogatása
A PostgreSQL minden jelentős verzióját a Azure Database for PostgreSQL fogja támogatni attól az időponttól kezdve, amikor az Azure megkezdi a verzió támogatását, egészen addig, amíg a PostgreSQL-Közösség kivonja a verziót, ahogyan azt a [PostgreSQL közösségi verziószámozási szabályzata](https://www.postgresql.org/support/versioning/)biztosítja.

## <a name="minor-version-support"></a>Másodlagos verzió támogatása
A Azure Database for PostgreSQL a rendszeres karbantartás részeként automatikusan végrehajtja az elsődleges verzió frissítését az Azure által preferált PostgreSQL-verzióra. 

## <a name="major-version-retirement-policy"></a>Főverzió kivezetési szabályzata
Az alábbi táblázat a PostgreSQL főverzióinak kivonulási részleteit tartalmazza. A dátumok követik a [PostgreSQL közösségi verziószámozási szabályzatát](https://www.postgresql.org/support/versioning/).

| Verzió | Újdonságok | Azure-támogatás kezdő dátuma | Nyugdíjazás dátuma|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9,5 (kivonva)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [Funkciók](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018. április 18.   | Február 11., 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funkciók](https://wiki.postgresql.org/wiki/NewIn96) | 2018. április 18.  | November 11., 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funkciók](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018. június 4.  | November 10., 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funkciók](https://www.postgresql.org/docs/11/release-11.html) | 2019. július 24.  | November 9., 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funkciók](https://www.postgresql.org/docs/12/release-12.html) | Szeptember 22., 2020  | November 14., 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Nem támogatottak a Azure Database for PostgreSQL

A kivont verziót továbbra is futtathatja Azure Database for PostgreSQLban. Vegye figyelembe azonban az alábbi korlátozásokat az egyes PostgreSQL-adatbázisok verziójának kivonulási dátuma után:
- Mivel a Közösség nem szabadít fel semmilyen további hibajavítást vagy biztonsági javítást, Azure Database for PostgreSQL nem fogja feljavítani a kivont adatbázismotor számára az esetleges hibákat vagy biztonsági problémákat, vagy egyéb módon biztonsági intézkedéseket hajt végre a kivont adatbázismotor tekintetében. Ennek eredményeképpen biztonsági réseket vagy más problémákat tapasztalhat. Az Azure azonban továbbra is rendszeres karbantartást és javítást végez a gazdagép, az operációs rendszer, a tárolók és a szolgáltatásokkal kapcsolatos egyéb összetevők számára.
- Ha bármilyen támogatási probléma merülhet fel a PostgreSQL-adatbázissal kapcsolatban, előfordulhat, hogy nem tudunk támogatást nyújtani. Ilyen esetekben frissítenie kell az adatbázist ahhoz, hogy támogatást nyújtson Önnek.
- Nem fog tudni új adatbázis-kiszolgálókat létrehozni a kivont verzióhoz. Ugyanakkor időponthoz kapcsolódó helyreállításokat is végrehajthat, és a meglévő kiszolgálókhoz olvasási replikákat hozhat létre.
- A Azure Database for PostgreSQL által fejlesztett új szolgáltatási képességek csak a támogatott adatbázis-kiszolgáló verziók esetében érhetők el.
- Az üzemidőre vonatkozó SLA-kat kizárólag Azure Database for PostgreSQL szolgáltatással kapcsolatos problémákra, és nem az adatbázismotor által okozott hibák okozta állásidőre alkalmazza a rendszer.  
- Abban az esetben, ha a PostgreSQL adatbázismotor által a kivont adatbázis-verzióban azonosított biztonsági rés miatt a szolgáltatás súlyos fenyegetést jelent, az Azure dönthet úgy, hogy leállítja az adatbázis-kiszolgálót a szolgáltatás biztonságossá tételéhez. Ilyen esetben értesítést kap a kiszolgáló frissítéséről, mielőtt online állapotba hozza a kiszolgálót.

## <a name="postgresql-version-syntax"></a>PostgreSQL-verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _kisebb_ kiadású frissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>Következő lépések
- Lásd: Azure Database for PostgreSQL – egy kiszolgáló által [támogatott verziók](./concepts-supported-versions.md)
- Lásd: Azure Database for PostgreSQL – rugalmas kiszolgáló (előzetes [verzió) támogatott verziók](flexible-server/concepts-supported-versions.md)
- További információ a főverziók verziófrissítésének végrehajtásáról: a [főverzió frissítéseinek](how-to-upgrade-using-dump-and-restore.md) dokumentációja.
- A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
