---
title: Kapcsolódás Azure Database for MySQL a MySQL-hez készült dbForge Studio használatával
description: A cikk bemutatja, hogyan csatlakozhat Azure Database for MySQL-kiszolgálóhoz a MySQL-hez készült dbForge Studio használatával.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d5d694820c2ffd09868d81693d4f98f839a139d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591877"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Kapcsolódás Azure Database for MySQL a MySQL-hez készült dbForge Studio használatával

Kapcsolódás Azure Database for MySQL a MySQL- [hez készült DbForge Studio](https://www.devart.com/dbforge/mysql/studio/)használatával:

1. Az adatbázis menüben válassza az új kapcsolatok lehetőséget.

2. Adja meg a gazdagép nevét és a bejelentkezési hitelesítő adatokat.

3. A konfiguráció ellenőrzéséhez kattintson a kapcsolódás tesztelése gombra.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-kapcsolat":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Adatbázis migrálása a biztonsági mentési és visszaállítási funkció használatával

A Studio számos módon lehetővé teszi az adatbázisok áttelepítését az Azure-ba, amelynek a választása kizárólag az Ön igényeitől függ. Ha a teljes adatbázist át kell helyeznie, a legjobb megoldás a biztonsági mentési és visszaállítási funkció használata. Ebben a példában migráljuk a MySQL-kiszolgálón található *sakila* -adatbázist a Azure Database for MySQLra. Az áttelepítési folyamat mögött a dbForge Studio for MySQL biztonsági mentési és visszaállítási funkciója segítségével hozza létre a MySQL-adatbázis biztonsági másolatát, majd állítsa vissza Azure Database for MySQL.

### <a name="back-up-the-database"></a>Az adatbázis biztonsági mentése

1. Az adatbázis menüben mutasson a biztonsági mentés és visszaállítás elemre, majd válassza a biztonsági mentési adatbázis elemet. Megjelenik az adatbázis biztonsági mentése varázsló.

2. Az adatbázis biztonsági mentése varázsló biztonsági mentés tartalma lapján válassza ki azokat az adatbázis-objektumokat, amelyekről biztonsági másolatot szeretne készíteni.

3. A beállítások lapon konfigurálja úgy a biztonsági mentési folyamatot, hogy az megfeleljen a követelményeknek.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="A varázsló beállításainak biztonsági mentése":::

4. Következő lépésként határozza meg a hibák feldolgozási viselkedését és naplózási beállításait.

5. Válassza a biztonsági mentés lehetőséget.

### <a name="restore-the-database"></a>Az adatbázis visszaállítása

1. Kapcsolódjon az Azure-hoz a MySQL-hez készült adatbázishoz a fent leírtak szerint.

2. Kattintson a jobb gombbal a Adatbázis-böngésző törzsre, mutasson a biztonsági mentés és visszaállítás elemre, majd válassza az adatbázis visszaállítása lehetőséget.

3. A megnyíló adatbázis-visszaállítás varázslóban válasszon ki egy, az adatbázis biztonsági másolatát tartalmazó fájlt.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Visszaállítási lépés":::

4. Válassza a visszaállítás lehetőséget.

5. Az eredmény ellenőrzéséhez.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Adatbázis migrálása az adatbázisok másolása funkció használatával

A másolási adatbázisok funkciója hasonló a biztonsági mentéshez és a visszaállításhoz, azzal a kivétellel, hogy nem szükséges két lépés az adatbázisok áttelepíteni. És mi több, a szolgáltatás lehetővé teszi két vagy több adatbázis átvitelét egy menetben. A másolási adatbázisok funkció csak a dbForge Studio for MySQL Enterprise kiadásában érhető el.
Ebben a példában a *world_x* -adatbázist a MySQL-kiszolgálóról Azure Database for MySQLra migráljuk.
Adatbázis migrálása az adatbázisok másolása funkció használatával:

1. Az adatbázis menüben válassza az adatbázisok másolása lehetőséget. 

2. A megjelenő adatbázis másolása lapon adja meg a forrás és a cél kapcsolatokat, és válassza ki az áttelepítendő adatbázis (oka) t. Beírjuk az Azure MySQL-kapcsolatokat, és kiválasztjuk a *world_x* -adatbázist. A folyamat elindításához kattintson a zöld nyílra.

3. Az eredmény ellenőrzéséhez.

Az adatbázis-áttelepítési erőfeszítések eredményeképpen a *world_x* adatbázis sikeresen megjelent az Azure MySQL-ben.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Adatbázisok másolása eredmény":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Adatbázis migrálása a séma és az adatösszehasonlítási eszközök használatával

a dbForge Studio for MySQL több eszközt is tartalmaz, amelyek lehetővé teszik a MySQL-adatbázisok áttelepítését, a MySQL-sémák pedig and\or az Azure-ba. A választható funkciók a saját igényeitől és a projekt követelményeitől függenek. Ha szelektíven át kell helyeznie egy adatbázist, azaz bizonyos MySQL-táblákat át kell telepíteni az Azure-ba, érdemes a séma és az adatösszehasonlítás funkciót használni.
Ebben a példában migráljuk a MySQL-kiszolgálón található *globális* adatbázist a Azure Database for MySQLra. Az áttelepítési folyamat mögötti logikája a dbForge Studio for MySQL sémájának és adatösszehasonlítási funkciójának használatával egy üres adatbázist hoz létre Azure Database for MySQLban, szinkronizálja a szükséges MySQL-adatbázissal először a séma-összehasonlítás eszköz használatával, majd az adatösszehasonlítás eszközzel. Így a MySQL sémák és az adathalmazok pontosan át lesznek helyezve az Azure-ba.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Kapcsolódás Azure Database for MySQLhoz és üres adatbázis létrehozása

Kapcsolódjon egy Azure Database for MySQLhoz, és hozzon létre egy üres adatbázist.

### <a name="step-2-schema-synchronization"></a>2. lépés Séma szinkronizálása

1. Az összehasonlítás menüben válassza az új séma-összehasonlítás elemet.
Megjelenik az új séma-összehasonlítás varázsló.

2. Válassza ki a forrást és a célt, majd adja meg a séma-összehasonlítási beállításokat. Válassza az összehasonlítás lehetőséget.

3. A megjelenő összehasonlító eredmények táblázatban válassza az objektumok szinkronizáláshoz lehetőséget. Kattintson a zöld nyíl gombra a séma-szinkronizálás varázsló megnyitásához.

4. Végigvezeti a varázsló szinkronizálási konfigurálásának lépésein. A módosítások telepítéséhez válassza a szinkronizálás lehetőséget.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Séma-szinkronizálás varázsló":::

### <a name="data-comparison"></a>Az adatösszehasonlítás

1. Az összehasonlítás menüben válassza az új adatösszehasonlítás elemet. Megjelenik az új adatösszehasonlítási varázsló.

2. Válassza ki a forrást és a célt, majd adja meg az adatösszehasonlítási beállításokat, és szükség esetén módosítsa a leképezéseket. Válassza az összehasonlítás lehetőséget.

3. A megjelenő összehasonlító eredmények táblázatban válassza az objektumok szinkronizáláshoz lehetőséget. Kattintson a zöld nyíl gombra az adatszinkronizálási varázsló megnyitásához.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Adatellenőrzés eredménye":::

4. Végigvezeti a varázsló szinkronizálási konfigurálásának lépésein. A módosítások telepítéséhez válassza a szinkronizálás lehetőséget.

5. Az eredmény ellenőrzéséhez.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Adatszinkronizálás eredménye":::

## <a name="summary"></a>Összefoglalás

Manapság több vállalat helyezi át az adatbázisait Azure Database for MySQLba, mivel ez az adatbázis-szolgáltatás egyszerűen beállítható, kezelhető és méretezhető. Az áttelepítésnek nem kell fájdalmasnak lennie. a dbForge Studio for MySQL olyan makulátlan áttelepítési eszközökkel büszkélkedhet, amelyek jelentősen megkönnyítik a folyamatot. A Studio lehetővé teszi, hogy az adatbázis-átvitel könnyen konfigurálható, menthető, módosítható, automatizált és ütemezett legyen.

## <a name="next-steps"></a>Következő lépések
- [A MySQL áttekintése](overview.md)
