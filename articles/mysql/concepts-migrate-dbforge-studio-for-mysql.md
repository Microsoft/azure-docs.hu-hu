---
title: Csatlakozás Azure Database for MySQL dbForge Studio for MySQL használatával
description: A cikk bemutatja, hogyan csatlakozhat a Azure Database for MySQL Serverhez a dbForge Studio for MySQL segítségével.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377225"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Csatlakozás Azure Database for MySQL dbForge Studio for MySQL használatával

Csatlakozás a Azure Database for MySQL [dbForge Studio for MySQL használatával:](https://www.devart.com/dbforge/mysql/studio/)

1. Az Adatbázis menüben válassza az Új kapcsolat lehetőséget.

2. Adja meg az állomásnevet és a bejelentkezési hitelesítő adatokat.

3. A konfiguráció ellenőrzéshez válassza a Kapcsolat tesztelése gombot.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-kapcsolat":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Adatbázis áttelepítése a Biztonsági mentés és visszaállítás funkcióval

A Studio számos módon teszi lehetővé az adatbázisok azure-ba való mibrálását, amelyek közül a választás kizárólag az Ön igényeitől függ. Ha a teljes adatbázist át kell költöztetni, a legjobb, ha a Biztonsági mentés és visszaállítás funkciót használja. Ebben a példában a MySQL-kiszolgálón található *Sakila-adatbázist* telepítjük át az Azure Database for MySQL. A dbForge Studio for MySQL biztonsági mentési és visszaállítási funkcióját használó migrálási folyamat logikája a MySQL-adatbázis biztonsági másolatának létrehozása, majd visszaállítása a Azure Database for MySQL.

### <a name="back-up-the-database"></a>Az adatbázis biztonsági mentése

1. Az Adatbázis menüben mutasson a Biztonsági mentés és visszaállítás pontra, majd válassza az Adatbázis biztonsági mentése lehetőséget. Megjelenik az Adatbázis biztonsági mentése varázsló.

2. Az Adatbázis biztonsági mentése varázsló Biztonsági másolat tartalmának lapján válassza ki azokat az adatbázis-objektumokat, amelyekről biztonsági másolatot kíván készíteni.

3. A Beállítások lapon konfigurálja a biztonsági mentési folyamatot az igényeinek megfelelően.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="A varázsló beállításainak biztonsági létrehozása":::

4. Ezután adja meg a hibák feldolgozási viselkedését és a naplózási beállításokat.

5. Válassza a Biztonsági mentés lehetőséget.

### <a name="restore-the-database"></a>Az adatbázis visszaállítása

1. Csatlakozzon az Azure for Database for MySQL-hez a fent leírtak szerint.

2. Kattintson a jobb gombbal a Adatbázis-böngésző, mutasson a Biztonsági mentés és visszaállítás elemre, majd válassza az Adatbázis visszaállítása lehetőséget.

3. A megnyíló Adatbázis-visszaállítás varázslóban válasszon ki egy adatbázis biztonsági mentését készítő fájlt.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Visszaállítási lépés":::

4. Válassza a Visszaállítás lehetőséget.

5. Ellenőrizze az eredményt.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Adatbázis áttelepítése az Adatbázisok másolása funkcióval

Az Adatbázisok másolása funkció hasonló a biztonsági mentéshez és a visszaállításhoz, azzal a kivételsel, hogy az adatbázis áttelepítéséhez nincs szükség két lépésre. Ráadásul a funkció lehetővé teszi két vagy több adatbázis egy ugrással történő átvitelét. Az Adatbázisok másolása funkció csak a dbForge Studio for MySQL Enterprise kiadásában érhető el.
Ebben a példában a  world_x mySQL-kiszolgálóról egy Azure Database for MySQL.
Adatbázis áttelepítése az Adatbázisok másolása funkcióval:

1. Az Adatbázis menüben válassza az Adatbázisok másolása lehetőséget. 

2. A megjelenő Adatbázisok másolása lapon adja meg a forrás- és célkapcsolatot, és válassza ki az áttelepíteni kívánt adatbázisokat. Beírjuk az Azure MySQL-kapcsolatot, és kiválasztjuk *a world_x* adatbázist. Válassza a zöld nyilat a folyamat kezdeményezésére.

3. Ellenőrizze az eredményt.

Az adatbázis-migrálási folyamataink eredményeként a world_x *sikeresen* megjelent az Azure MySQL-be.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Adatbázisok másolásának eredménye":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Adatbázis áttelepítése Séma- és adat-összehasonlítási eszközökkel

A dbForge Studio for MySQL tartalmaz néhány olyan eszközt, amelyek lehetővé teszik a MySQL-adatbázisok, MySQL-sémák és\vagy adatok azure-ba való áttelepítését. A funkciók megválasztása az igényektől és a projekt követelményeitől függ. Ha egy adatbázist szelektíven kell áthelyezni, azaz bizonyos MySQL-táblákat az Azure-ba kell milegálni, a legjobb, ha a Schema és az Data Compare funkciót használja.
Ebben a példában a  MySQL-kiszolgálón található világadatbázist telepítjük át a Azure Database for MySQL. A dbForge Studio for MySQL Schema and Data Compare funkcióját használó migrálási folyamat logikája egy üres adatbázis létrehozása a Azure Database for MySQL-ben, szinkronizálása a szükséges MySQL-adatbázissal először a Schema Compare eszköz, majd a Data Compare eszköz használatával. Így a MySQL-sémák és -adatok pontosan átkerülnek az Azure-ba.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>1. lépés Csatlakozás Azure Database for MySQL és üres adatbázis létrehozása

### <a name="step-2-schema-synchronization"></a>2. lépés Séma-szinkronizálás

1. Az Összehasonlítás menüben válassza az Új séma-összehasonlítás lehetőséget.
Megjelenik az Új séma-összehasonlító varázsló.

2. Válassza ki a Forrást és a Célt, majd adja meg a séma-összehasonlítási beállításokat. Válassza az Összehasonlítás lehetőséget.

3. A megjelenő összehasonlító eredmények rácsában válassza ki a szinkronizáláshoz használható objektumokat. Kattintson a zöld nyíl gombra a Sémaszinkronizálási varázsló megnyitásához.

4. Kövesse végig a szinkronizálás konfigurálását konfiguráló varázsló lépéseit. A módosítások üzembe helyezéséhez válassza a Szinkronizálás lehetőséget.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Sémaszinkronizálási varázsló":::

### <a name="step-3-data-comparison"></a>3. lépés Adatok összehasonlítása

1. Az Összehasonlítás menüben válassza az Új adatok összehasonlítása lehetőséget. Megjelenik az Új adatok összehasonlítása varázsló.

2. Válassza ki a Forrás és a Cél adatokat, majd adja meg az adat-összehasonlítási beállításokat, és szükség esetén módosítsa a leképezéseket. Válassza az Összehasonlítás lehetőséget.

3. A megjelenő összehasonlító eredmények rácsában válassza ki a szinkronizáláshoz használható objektumokat. A zöld nyíl gombra kattintva nyissa meg az Adatszinkronizálás varázslót.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Adatberekedés eredménye":::

4. Kövesse a szinkronizálás konfigurálását konfiguráló varázsló lépéseit. A módosítások üzembe helyezéséhez válassza a Szinkronizálás lehetőséget.

5. Ellenőrizze az eredményt.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Adatszinkronizálás eredménye":::

## <a name="summary"></a>Összefoglalás

Napjainkban egyre több vállalkozás költöztet adatbázisokat a Azure Database for MySQL, mivel ez az adatbázis-szolgáltatás könnyen beállítható, kezelhető és skálázható. Ennek a migrálásnak nem kell elkesésnek lennie. A dbForge Studio for MySQL nem jól megfelelő migrálási eszközöket kínál, amelyek jelentősen megkönnyítik a folyamatot. A Studio lehetővé teszi az adatbázis-átvitel egyszerű konfigurálását, mentését, szerkesztését, automatizálását és ütemezését.

## <a name="next-steps"></a>Következő lépések
- [A MySQL áttekintése](overview.md)
