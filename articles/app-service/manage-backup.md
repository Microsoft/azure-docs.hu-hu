---
title: Alkalmazás biztonsági mentése
description: Ismerje meg, hogyan hozhat létre biztonsági másolatokat az alkalmazásairól Azure App Serviceban. Manuális vagy ütemezett biztonsági mentések futtatása. A biztonsági mentéseket a csatolt adatbázissal is testreszabhatja.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 7c00205e2931400caa64f35db962d94a732f2524
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714493"
---
# <a name="back-up-your-app-in-azure"></a>Adatok biztonsági mentése az Azure-ban
A [Azure app Service](overview.md) biztonsági mentési és visszaállítási funkciója lehetővé teszi, hogy egyszerűen hozza létre az alkalmazások biztonsági másolatait manuálisan vagy ütemezés szerint. Beállíthatja, hogy a biztonsági másolatok határozatlan ideig maradjanak. Az alkalmazást visszaállíthatja egy korábbi állapot pillanatképére a meglévő alkalmazás felülírásával vagy egy másik alkalmazásra való visszaállítással.

Az alkalmazások biztonsági mentésből való visszaállításáról az [alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben olvashat bővebben.

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Mi történik a biztonsági mentéssel
App Service a következő információk biztonsági mentését végezheti egy Azure Storage-fiókba és-tárolóba, amelyet az alkalmazás használatára konfigurált. 

* Alkalmazáskonfiguráció
* Fájl tartalma
* Az alkalmazáshoz csatlakoztatott adatbázis

A biztonsági mentési funkció a következő adatbázis-megoldásokat támogatja: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [beépített mySQL](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Minden biztonsági mentés az alkalmazás teljes offline példánya, nem Növekményes frissítés.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Követelmények és korlátozások
* A biztonsági mentési és visszaállítási funkció használatához a App Service tervnek a **standard**, **prémium** vagy **elkülönített** szinten kell lennie. További információ a App Service terv magasabb szintű használatára való méretezéséről: alkalmazás felskálázása [Az Azure-ban](manage-scale-up.md). A **prémium** és az **elkülönített** szintek nagyobb számú napi biztonsági mentést tesznek lehetővé, mint a **standard** szint.
* Szüksége lesz egy Azure Storage-fiókra és-tárolóra abban az előfizetésben, amelyben a biztonsági mentéshez használni kívánt alkalmazás található. Az Azure Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md).
* A biztonsági másolatok akár 10 GB-nyi alkalmazás-és adatbázis-tartalommal is rendelkezhetnek. Ha a biztonsági másolat mérete meghaladja ezt a korlátot, hibaüzenet jelenik meg.
* A TLS-kompatibilis Azure Database for MySQL biztonsági mentései nem támogatottak. Ha a biztonsági mentés konfigurálva van, akkor a biztonsági mentési hibákba ütközik.
* A TLS-kompatibilis Azure Database for PostgreSQL biztonsági mentései nem támogatottak. Ha a biztonsági mentés konfigurálva van, akkor a biztonsági mentési hibákba ütközik.
* Az alkalmazáson belüli MySQL-adatbázisokat konfiguráció nélkül automatikusan biztonsági másolat készül. Ha az alkalmazáson belüli MySQL-adatbázisok esetében manuálisan állítja be a beállításokat, például a kapcsolatok karakterláncok hozzáadását, előfordulhat, hogy a biztonsági másolatok nem működnek megfelelően.
* Ha tűzfallal védett Storage-fiókot használ, a biztonsági másolatok célhelye nem támogatott. Ha a biztonsági mentés konfigurálva van, akkor a biztonsági mentési hibákba ütközik.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
1. A [Azure Portal](https://portal.azure.com)navigáljon az alkalmazás lapjára, majd válassza a **biztonsági mentések** lehetőséget. Megjelenik a **biztonsági másolatok** lap.

    ![Biztonsági másolatok oldal](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Ha a következő üzenet jelenik meg, kattintson rá a App Service terv frissítéséhez, mielőtt folytatná a biztonsági mentéseket.
    > További információ: alkalmazás vertikális [Felskálázása az Azure-ban](manage-scale-up.md).
    > :::image type="content" source="./media/manage-backup/upgrade-plan.png" alt-text="Képernyőkép a szalagcímről egy üzenettel, amely frissíti a App Service tervet a biztonsági mentési és visszaállítási funkció eléréséhez.":::
    > 
    > 

2. A **biztonsági mentés** lapon válassza a **biztonsági mentés nincs konfigurálva lehetőséget. Kattintson ide az alkalmazás biztonsági mentésének konfigurálásához**.

    ![Kattintson a konfigurálás elemre.](./media/manage-backup/configure-start.png)

3. A **biztonsági mentési konfiguráció** lapon kattintson a **Storage nincs konfigurálva** a Storage-fiók konfigurálása elemre.

    :::image type="content" source="./media/manage-backup/configure-storage.png" alt-text="Képernyőfelvétel a biztonsági mentési tár szakaszról a nincs konfigurálva beállítással.":::

4. Válassza ki a biztonsági mentési célhelyet a **Storage-fiók** és a **tároló** kiválasztásával. A Storage-fióknak ugyanahhoz az előfizetéshez kell tartoznia, mint annak az alkalmazásnak, amelyről biztonsági másolatot szeretne készíteni. Ha szeretné, létrehozhat egy új Storage-fiókot vagy egy új tárolót a megfelelő lapokon. Ha elkészült, kattintson a **kiválasztás** gombra.

5. A **biztonsági mentési konfiguráció** lapon, amely továbbra is nyitva marad, konfigurálhatja a **biztonsági mentési adatbázist**, majd kiválaszthatja a biztonsági másolatokban szerepeltetni kívánt adatbázisokat (SQL Database vagy MySQL), majd kattintson **az OK** gombra.

    :::image type="content" source="./media/manage-backup/configure-database.png" alt-text="Képernyőkép a biztonsági mentési adatbázisról szakasz, amely tartalmazza a Belefoglalás a biztonsági mentésbe lehetőséget.":::

    > [!NOTE]
    > Ahhoz, hogy egy adatbázis megjelenjen a listában, a kapcsolati karakterláncnak léteznie kell az **alkalmazás Alkalmazásbeállítások** lapjának **kapcsolati karakterláncok** szakaszában. 
    >
    > Az alkalmazáson belüli MySQL-adatbázisokat konfiguráció nélkül automatikusan biztonsági másolat készül. Ha az alkalmazáson belüli MySQL-adatbázisok beállításait manuálisan hajtja végre, például a kapcsolatok karakterláncok hozzáadását, előfordulhat, hogy a biztonsági másolatok nem működnek megfelelően.
    > 
    > 

6. A **biztonsági mentési konfiguráció** lapon kattintson a **Mentés** gombra.
7. A **biztonsági másolatok** lapon kattintson a **biztonsági mentés** elemre.

    ![BackUpNow gomb](./media/manage-backup/manual-backup.png)

    A biztonsági mentési folyamat során állapotjelző üzenet jelenik meg.

A Storage-fiók és a tároló konfigurálása után bármikor kezdeményezheti a manuális biztonsági mentést. A manuális biztonsági mentések határozatlan ideig megmaradnak.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Automatikus biztonsági mentések konfigurálása
1. A **biztonsági mentési konfiguráció** lapon állítsa be az **ütemezett biztonsági mentést** **be értékre.** 

    ![Automatikus biztonsági mentések engedélyezése](./media/manage-backup/scheduled-backup.png)

2. Konfigurálja a biztonsági mentési ütemtervet igény szerint, és kattintson **az OK gombra**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Részleges biztonsági másolatok konfigurálása
Előfordulhat, hogy nem szeretne biztonsági másolatot készíteni az alkalmazás minden eleméről. Íme, néhány példa:

* Az alkalmazás olyan [heti biztonsági mentését állíthatja be](#configure-automated-backups) , amely soha nem módosult statikus tartalmat tartalmaz, például a régi blogbejegyzéseket vagy képeket.
* Az alkalmazás több mint 10 GB tartalmat tartalmaz (ez a maximális mennyiség, amelyet egyszerre tud készíteni).
* Nem kíván biztonsági másolatot készíteni a naplófájlokról.

A részleges biztonsági másolatok lehetővé teszik, hogy pontosan melyik fájlokat szeretné biztonsági másolatot készíteni.

> [!NOTE]
> A biztonsági mentésben szereplő egyéni adatbázisok 4 GB-os maximálisak lehetnek, de a biztonsági mentés maximális mérete 10 GB

### <a name="exclude-files-from-your-backup"></a>Fájlok kizárása a biztonsági másolatból
Tegyük fel, hogy rendelkezik egy olyan alkalmazással, amely olyan naplófájlokat és statikus lemezképeket tartalmaz, amelyek nem változnak. Ilyen esetekben kizárhatja ezeket a mappákat és fájlokat a későbbi biztonsági másolatokban való tároláshoz. Ha fájlokat és mappákat szeretne kizárni a biztonsági másolatokból, hozzon létre egy `_backup.filter` fájlt az `D:\home\site\wwwroot` alkalmazás mappájába. Itt adhatja meg a fájlban kizárni kívánt fájlok és mappák listáját. 

A fájlok eléréséhez navigáljon a következővel: `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Ha a rendszer kéri, jelentkezzen be az Azure-fiókjába.

Azonosítsa a biztonsági másolatokból kizárni kívánt mappákat. Tegyük fel például, hogy ki szeretné szűrni a kijelölt mappát és fájlokat.

![Lemezképek mappa](./media/manage-backup/kudu-images.png)

Hozzon létre egy nevű fájlt `_backup.filter` , és helyezze el az előző listát a fájlban, de távolítsa el a fájlt `D:\home` . Soronként egy könyvtárat vagy fájlt listázhat. Így a fájl tartalmának a következőket kell tennie:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Töltse fel a `_backup.filter` fájlt a `D:\home\site\wwwroot\` webhely könyvtárába [FTP](deploy-ftp.md) használatával vagy bármilyen más módszerrel. Ha szeretné, közvetlenül is létrehozhatja a fájlt a kudu használatával, `DebugConsole` és beszúrhatja a tartalmat.

A biztonsági mentések ugyanúgy működnek, mint a szokásos módon, [manuálisan](#create-a-manual-backup) vagy [automatikusan](#configure-automated-backups). Mostantól a ben megadott fájlok és mappák ki vannak `_backup.filter` zárva a jövőbeli biztonsági mentések ütemezett vagy manuális elindításával. 

> [!NOTE]
> A hely részleges biztonsági mentéseit ugyanúgy állíthatja vissza, mint a [normál biztonsági mentést](web-sites-restore.md). A visszaállítási folyamat a megfelelő dolog.
> 
> A teljes biztonsági mentés visszaállításakor a helyen lévő összes tartalmat lecseréli a biztonsági mentésben szereplő összes tartalomra. Ha egy fájl van a helyen, de a biztonsági másolatban nem szerepel, a rendszer törli. A részleges biztonsági mentés visszaállításakor azonban az egyik korlátozott könyvtárban vagy bármely korlátozott fájlban található tartalom a következőképpen marad.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>A biztonsági másolatok tárolása
Miután elvégezte az alkalmazás egy vagy több biztonsági mentését, a biztonsági másolatok a Storage-fiók **tárolók** lapján és az alkalmazásban láthatók. A Storage-fiókban minden biztonsági másolat egy olyan `.zip` fájlból áll, amely tartalmazza a biztonsági mentési és a fájl `.xml` tartalmának jegyzékfájlját tartalmazó fájlt `.zip` . Ezeket a fájlokat kibonthatja és böngészheti, ha a biztonsági mentéseket nem az alkalmazás-visszaállítás tényleges végrehajtása nélkül szeretné elérni.

Az alkalmazás adatbázisának biztonsági másolatát a. zip fájl gyökerében tárolja a rendszer. SQL Database esetén ez egy BACPAC-fájl (fájlkiterjesztés nélkül), és importálható. Ha Azure SQL Database adatbázist szeretne létrehozni a BACPAC-exportálás alapján, tekintse meg a [BACPAC-fájl importálása egy adatbázis létrehozásához Azure SQL Database-ban](../azure-sql/database/database-import.md)című témakört.

> [!WARNING]
> A **websitebackups** -tárolóban található fájlok bármelyikének módosítása miatt a biztonsági mentés érvénytelenné válik, ezért nem állítható be.
> 
> 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

A parancsfájlok segítségével automatizálhatja a biztonsági mentési felügyeletet az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/)használatával.

Példákat a következő témakörben talál:

- [Azure CLI-minták](samples-cli.md)
- [Azure PowerShell minták](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Következő lépések
Az alkalmazások biztonsági másolatból való visszaállításáról az [alkalmazás visszaállítása az Azure-ban](web-sites-restore.md)című témakörben olvashat bővebben.