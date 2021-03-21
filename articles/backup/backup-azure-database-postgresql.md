---
title: Az Azure Database for PostgreSQL biztonsági mentése
description: Ismerkedjen meg Azure Database for PostgreSQL biztonsági mentéssel hosszú távú adatmegőrzéssel (előzetes verzió)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 1e2d83d4a5e21ed747ec9d4dcf2fa03d1e3935cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737572"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL biztonsági mentés hosszú távú adatmegőrzéssel (előzetes verzió)

A Azure Backup és az Azure Database Services együttes használatával olyan nagyvállalati szintű biztonsági mentési megoldás készíthető Azure Database for PostgreSQL kiszolgálókhoz, amely akár 10 évig is megőrzi a biztonsági mentéseket.

A hosszú távú adatmegőrzés mellett a megoldás számos más képességgel is rendelkezik, az alább látható módon:

- Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) az adatbázishoz Azure Active Directory és Managed Service Identity (MSI) hitelesítés használatával.
- Az ügyfél által vezérelt ütemezett és igény szerinti biztonsági mentés az egyes adatbázis szintjén.
- Az adatbázis-szintű visszaállítás bármely postgres-kiszolgálóra vagy közvetlenül a blob Storage-tárolóba.
- Hosszú távú megőrzés.
- Az összes művelet és feladat központi figyelése.
- A biztonsági mentések külön biztonsági és tartalék tartományokban vannak tárolva. Így még akkor is, ha a forráskiszolgáló biztonsága vagy leölése is megszakadt, a biztonsági másolatok biztonságban maradhatnak a [Backup-tárolóban](backup-vault-overview.md).
- A **pg_dump** használata nagyobb rugalmasságot biztosít a visszatárolásban, így az adatbázis-verziókban visszaállíthatók, vagy akár a biztonsági mentés egy részét is visszaállíthatja.

Ezt a megoldást önállóan vagy az Azure PostgreSQL által kínált natív biztonsági mentési megoldás mellett is használhatja, amely akár 35 napig is megtartja a megtartást. A natív megoldás az operatív helyreállításokhoz alkalmas, például ha a legújabb biztonsági másolatokból kíván helyreállítani. A Azure Backup megoldás segítséget nyújt a megfelelőségi igények és a részletesebb és rugalmas biztonsági mentés és helyreállítás érdekében.

## <a name="support-matrix"></a>Támogatási mátrix

|Támogatás  |Részletek  |
|---------|---------|
|Támogatott központi telepítések   |  [Azure Database for PostgreSQL – Önálló kiszolgáló](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Támogatott Azure-régiók |  USA keleti régiója, USA 2. keleti régiója, USA középső régiója, USA déli középső régiója, USA nyugati régiója, USA 2. nyugati régiója, Dél-Brazília, Közép-Kanada, Észak-Európa, Nyugat-Európa, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Középnyugat-Németország, Észak-Svájc, Nyugat-Svájc, Kelet-Ázsia, Dél-Ausztrália, Kelet-Japán, Nyugat-Japán, Dél-Korea  |
|Támogatott Azure PostgreSQL-verziók    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Szolgáltatásokkal kapcsolatos megfontolások és korlátozások

- Az összes művelet csak a Azure Portal támogatott.
- Az adatbázis maximális méretének ajánlott korlátja 400 GB.
- A régiók közötti biztonsági mentés nem támogatott. Ez azt jelenti, hogy nem lehet biztonsági másolatot készíteni egy Azure PostgreSQL-kiszolgálóról egy másik régióban lévő tárba. Hasonlóképpen, a biztonsági mentést csak a tárolóval azonos régióban található kiszolgálóra lehet visszaállítani.
- A visszaállítás időpontjában csak az adatmennyiséget állítja helyre a rendszer. A "szerepkörök" nem állíthatók vissza.
- Az előzetes verzióban azt javasoljuk, hogy csak a tesztkörnyezetben futtassa a megoldást.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. Ez a megoldás a **pg_dump** használatával készít biztonsági másolatot az Azure PostgreSQL-adatbázisokról.

2. Miután megadta az Azure PostgreSQL-adatbázisokat, amelyekről biztonsági másolatot szeretne készíteni, a szolgáltatás ellenőrzi, hogy megfelelő engedélyekkel és hozzáféréssel rendelkezik-e a biztonsági mentési művelet végrehajtásához a kiszolgálón és az adatbázisban.

3. Azure Backup elindít egy feldolgozói szerepkört, amelyen telepítve van egy biztonsági mentési bővítmény. Ez a bővítmény a postgres-kiszolgálóval kommunikál.

4. Ez a bővítmény egy koordinátort és egy Azure postgres beépülő modult tartalmaz. Míg a koordinátor felelős a munkafolyamatok különböző műveletekhez, például a biztonsági mentés, a biztonsági mentés és a visszaállítás konfigurálásához, a beépülő modul felelős a tényleges adatforgalomért.
  
5. Miután elindította a védelem konfigurálását a kiválasztott adatbázisokon, a Backup szolgáltatás beállítja a koordinátort a biztonsági mentési ütemtervekkel és egyéb házirend-adatokkal.

6. Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és a **pg_dump** használatával elindítja a postgres-kiszolgálóról érkező biztonsági mentési adatok továbbítását.

7. A beépülő modul közvetlenül a Backup-tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adattitkosítás a Microsoft által felügyelt kulcsokkal történik, és a Azure Backup szolgáltatás tárolja a Storage-fiókokban.

8. Az adatátvitel befejezésekor a koordinátor a biztonsági mentési szolgáltatással megerősíti a véglegesítést.

    ![A biztonsági mentés folyamata](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Biztonsági mentés konfigurálása az Azure PostgreSQL-adatbázisokban

A biztonsági mentést több adatbázison is konfigurálhatja több Azure PostgreSQL-kiszolgáló között. Győződjön meg arról, hogy a szolgáltatás által a postgres-kiszolgálók biztonsági mentéséhez szükséges [előfeltétel-engedélyek](#prerequisite-permissions-for-configure-backup-and-restore) már konfigurálva vannak.

Az alábbi utasítások részletes útmutatóul szolgálnak az Azure PostgreSQL-adatbázisok biztonsági mentésének konfigurálásához Azure Backup használatával:

1. Kétféleképpen lehet megkezdeni a folyamatot:

    1. Nyissa meg a [Backup Center](backup-center-overview.md)  ->  **– Áttekintés**  ->  **biztonsági mentést**.

        ![Ugrás a biztonsági mentési központba](./media/backup-azure-database-postgresql/backup-center.png)

        A **kezdeményezése: biztonsági mentés konfigurálása** területen válassza ki az **adatforrás típusát** **Azure Database for PostgreSQLként**.

        ![A kezdeményezése: biztonsági mentés konfigurálása területen válassza az adatforrás típusa lehetőséget.](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Azt is megteheti, hogy közvetlenül a [Backup](backup-vault-overview.md)-tárolók  ->  **biztonsági mentését** is megnyithatja.

        ![Ugrás a Backup-tárolók számára](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Biztonsági mentés kiválasztása a Backup-tárolóban](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. A **biztonsági mentés konfigurálása** területen válassza ki azt a **mentési tárolót** , amelyre biztonsági másolatot szeretne készíteni a postgres-adatbázisokról. Ez az információ előre ki van töltve, ha már szerepel a tároló környezetében.

    ![Backup-tároló kiválasztása a biztonsági mentés konfigurálása területen](./media/backup-azure-database-postgresql/configure-backup.png)

1. Válasszon ki vagy hozzon létre egy **biztonsági mentési szabályzatot**.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-database-postgresql/backup-policy.png)

1. Válassza az erőforrások vagy postgres-adatbázisok lehetőséget a biztonsági mentéshez.

    ![Válassza ki az erőforrásokat a biztonsági mentéshez](./media/backup-azure-database-postgresql/select-resources.png)

1. Az összes előfizetéshez tartozó Azure PostgreSQL-kiszolgáló listájából választhat, ha azok ugyanabban a régióban vannak, mint a tároló. A nyílra kattintva megtekintheti a kiszolgálón belüli adatbázisok listáját.

    ![Kiszolgálók kiválasztása](./media/backup-azure-database-postgresql/choose-servers.png)

1. A szolgáltatás futtatja ezeket az ellenőrzéseket a kiválasztott adatbázisokon annak ellenőrzéséhez, hogy a tár rendelkezik-e engedéllyel a kiválasztott postgres-kiszolgálók és-adatbázisok biztonsági mentéséhez.
    1. A folytatáshoz az összes adatbázis **biztonsági mentési készültségét** **kell olvasni.**
    1. Ha hiba merül fel, **javítsa** ki a hibát, és **ellenőrizze** újra, vagy távolítsa el az adatbázist a kijelölésből.

    ![Kijavítani kívánt érvényesítési hibák](./media/backup-azure-database-postgresql/validation-errors.png)

1. Erősítse meg az összes adatot a **felülvizsgálat és konfigurálás** területen, majd válassza a **biztonsági mentés konfigurálása** elemet a művelet elküldéséhez.

    ![Részletek ellenőrzése és konfigurálása](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Az indítás után a **biztonsági mentési** művelet létrehoz egy biztonsági mentési példányt. A művelet állapotát a Backup Center vagy a tároló nézet [biztonsági mentési példányok](backup-center-monitor-operate.md#backup-instances) paneljén követheti nyomon.

    ![Biztonsági mentési példányok](./media/backup-azure-database-postgresql/backup-instances.png)

1. A biztonsági mentések a szabályzatban meghatározott biztonsági mentési ütemezésnek megfelelően aktiválódnak. A feladatok nyomon követhetők a [biztonsági mentési feladatok](backup-center-monitor-operate.md#backup-jobs)alatt. Jelenleg az elmúlt hét napban is megtekintheti a feladatokat.

    ![Biztonsági mentési feladatok](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Biztonsági mentési szabályzat létrehozása

1. Válassza a **Backup Center**  ->  **biztonsági mentési szabályzatok**  ->  **Hozzáadás** lehetőséget. Azt is megteheti, hogy a **Backup**-  ->  **tároló biztonsági mentési szabályzatának**  ->  **hozzáadása** lehetőségre kattint.

    ![Biztonsági mentési szabályzat hozzáadása](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Adja meg az új szabályzat **nevét** .

    ![Adja meg a szabályzat nevét](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Adja meg a biztonsági mentési ütemtervet. Jelenleg a **heti** biztonsági mentést támogatjuk. A biztonsági mentéseket ütemezheti a hét egy vagy több napján.

    ![A biztonsági mentés ütemtervének megadása](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **Adatmegőrzési** beállítások megadása. Egy vagy több megőrzési szabályt is hozzáadhat. Az egyes adatmegőrzési szabályok feltételezik az adott biztonsági másolatok bemeneteit, valamint az adattárak és a megőrzés időtartamát.

1. Dönthet úgy, hogy a biztonsági másolatokat a két adattár (vagy rétegek) egyikében tárolja: **biztonsági mentési** adattár (standard szint) vagy **archiválási** adattár (előzetes verzió). **Kétszintű rétegbeli beállítás** közül választhat, amelyekkel meghatározhatja, hogy a biztonsági másolatok hogyan legyenek a két adattárra bontva:

    - Ha szeretné, hogy a rendszer **azonnal** készítsen biztonsági másolatot a biztonsági mentési és az archiválási adattárakban, válassza a másolást.
    - Ha a biztonsági mentési adattár lejárta után szeretné áthelyezni a biztonsági mentést az archiválási adattárba, válassza a **lejárati idő** áthelyezése lehetőséget.

1. Az **alapértelmezett adatmegőrzési szabály** minden más megőrzési szabály hiányában érvényes, és az alapértelmezett érték három hónap.

    - A megőrzési időtartam a **biztonsági mentési** adattár hét napjától 10 évig terjedő tartományba esik.
    - A megőrzés időtartamának időtartama hat hónap és 10 év között van az **archiválási** adattárban.

    ![Megőrzési időtartam szerkesztése](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Az adatmegőrzési szabályok értékelése előre meghatározott prioritási sorrendben történik. Az **éves** szabály prioritása a legmagasabb, amelyet a **havonta** , majd a **heti** szabály követ. Az alapértelmezett megőrzési beállítások akkor lépnek életbe, ha más szabályok nem érvényesek. Például ugyanaz a helyreállítási pont lehet az első sikeres biztonsági mentés hetente, valamint az első sikeres biztonsági mentés minden hónapban. Mivel azonban a havi szabály prioritása nagyobb, mint a heti szabály, a havonta végrehajtott első sikeres biztonsági mentésnek megfelelő adatmegőrzést kell végrehajtani.

## <a name="restore"></a>Visszaállítás

Az adatbázist bármely Azure PostgreSQL-kiszolgálóra visszaállíthatja ugyanazon az előfizetésen belül, ha a szolgáltatás rendelkezik a megfelelő engedélyekkel a célkiszolgálón. Győződjön meg arról, hogy a szolgáltatás által a postgres-kiszolgálók biztonsági mentéséhez szükséges [előfeltétel-engedélyek](#prerequisite-permissions-for-configure-backup-and-restore) már konfigurálva vannak.

A visszaállítás elindításához kövesse ezt a lépésenkénti útmutatót:

1. A visszaállítási folyamat két módon indítható el:
    1. Nyissa meg a [Backup Center](backup-center-overview.md)  ->  **Áttekintés**  ->  **visszaállítását**.

    ![Visszaállítás kiválasztása a Backup Centerben](./media/backup-azure-database-postgresql/backup-center-restore.png)

    A **kezdeményezés: visszaállítás** alatt válassza ki az **adatforrás típusát** **Azure Database for PostgreSQLként**. Válassza ki a **biztonsági mentési példányt**.

    ![Adatforrás típusának kiválasztása a kezdeményezés: visszaállítás](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Azt is megteheti, hogy közvetlenül a **Backup**-  ->  **tároló biztonsági mentési példányait** nyitja meg. Válassza ki a visszaállítani kívánt adatbázishoz tartozó **biztonsági mentési példányt** .

    ![Biztonsági mentési példányok visszaállításhoz](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Biztonsági mentési példányok listája](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Visszaállítás kiválasztása](./media/backup-azure-database-postgresql/select-restore.png)

1. **Válassza ki a helyreállítási pontot** a kiválasztott biztonsági mentési példányhoz elérhető teljes biztonsági másolatok listájából. Alapértelmezés szerint a legújabb helyreállítási pont van kiválasztva.

    ![Helyreállítási pont kiválasztása](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Helyreállítási pontok listája](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Bemeneti **visszaállítási paraméterek**. Ezen a ponton két visszaállítási típus közül választhat: **visszaállítás adatbázisként** és **visszaállítás fájlként**.

1. **Visszaállítás adatbázisként**: állítsa vissza a biztonsági mentési adatkészletet egy új adatbázis létrehozásához a cél PostgreSQL-kiszolgálón.

    - A célkiszolgáló megegyező lehet a forráskiszolgálón. Az eredeti adatbázis felülírása azonban nem támogatott.
    - A kiszolgáló közül választhat az összes előfizetésben, de a tárolóval megegyező régióban is.
    - Válassza a **felülvizsgálat + visszaállítás** lehetőséget. Ezzel a művelettel ellenőrizhető, hogy a szolgáltatás megfelelő visszaállítási engedéllyel rendelkezik-e a célkiszolgálón.

    ![Visszaállítás adatbázisként](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Visszaállítás fájlként**: a biztonságimásolat-fájlok kiírása a célként megadott Storage-fiókba (Blobok).

    - A Storage-fiókok közül választhat az összes előfizetésben, de a tárolóval megegyező régióban is.
    - Válassza ki a kiválasztott Storage-fiókhoz szűrni kívánt tárolót a tároló listából.
    - Válassza a **felülvizsgálat + visszaállítás** lehetőséget. Ezzel a művelettel ellenőrizhető, hogy a szolgáltatás megfelelő visszaállítási engedéllyel rendelkezik-e a célkiszolgálón.

    ![Visszaállítás fájlként](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Tekintse át az adatokat, és válassza a **visszaállítás** lehetőséget. Ez egy megfelelő visszaállítási feladatot indít el, amely nyomon követhető a **biztonsági mentési feladatok** alatt.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>A biztonsági mentés és a visszaállítás konfigurálásához szükséges előfeltételek

A Azure Backup szigorú biztonsági irányelveket követ. Annak ellenére, hogy egy natív Azure-szolgáltatás, az erőforrásra vonatkozó engedélyek nem feltételezik, és a felhasználónak explicit módon kell megadnia.  Hasonlóképpen, az adatbázishoz való kapcsolódáshoz szükséges hitelesítő adatok nem tárolódnak. Ez fontos az adatai védelméhez. Ehelyett Azure Active Directory hitelesítést használunk.

[Töltse le ezt a dokumentumot](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) egy automatizált parancsfájl és a kapcsolódó utasítások beszerzéséhez. A biztonsági mentéshez és visszaállításhoz megfelelő engedélyeket biztosít az Azure PostgreSQL-kiszolgálóhoz.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Az Azure PostgreSQL-adatbázisok biztonsági mentésének kezelése

A következő felügyeleti műveletek végezhetők el a **biztonsági mentési példányokon**:

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

Ha a biztonsági mentést nem a szabályzatban megadott ütemterv szerint szeretné elindítani, ugorjon a **biztonsági másolatok** biztonsági mentése  ->  **most** lehetőségre.
Válassza ki a vonatkozó biztonsági mentési házirendben definiált megőrzési szabályok listáját.

![Biztonsági mentés indítása most](./media/backup-azure-database-postgresql/backup-now.png)

![Adatmegőrzési szabályok listájának kiválasztása](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>A védelem kikapcsolása

A biztonsági másolati elemek védelmét leállíthatja. Ezzel a beállítással az adott biztonsági mentési tételhez tartozó helyreállítási pontokat is törli. Még nem biztosítjuk a védelem leállításának lehetőségét a meglévő helyreállítási pontok megőrzése mellett.

![A védelem kikapcsolása](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Házirend módosítása

A kapcsolódó házirendet megváltoztathatja egy biztonsági mentési példánnyal.

1. Válassza ki a **biztonsági mentési példány**  ->  **változási szabályzatát**.

    ![Házirend módosítása](./media/backup-azure-database-postgresql/change-policy.png)

1. Válassza ki az adatbázisra alkalmazni kívánt új szabályzatot.

    ![Házirend ismételt kiosztása](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a szakasz az Azure PostgreSQL-adatbázisok Azure Backup használatával történő biztonsági mentésére vonatkozó hibaelhárítási információkat tartalmaz.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Adja meg a Backup-tároló MSI **olvasási** hozzáférését azon a PG-kiszolgálón, amelyről biztonsági másolatot szeretne készíteni vagy visszaállítani:

A PostgreSQL-adatbázis biztonságos kapcsolatának létrehozásához Azure Backup a [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) hitelesítési modellt használja. Ez azt jelenti, hogy a Backup-tároló csak azokhoz az erőforrásokhoz férhet hozzá, amelyek kifejezetten engedélyt kaptak a felhasználótól.

A rendszer az MSI-t automatikusan a létrehozáskor rendeli hozzá a tárolóhoz. Meg kell adnia a tároló MSI-hozzáférését a PostgreSQL-kiszolgálókhoz, amelyekről biztonsági másolatot szeretne készíteni az adatbázisokról.

Lépések:

1. A postgres-kiszolgálón nyissa meg a **Access Control (iam)** ablaktáblát.

    ![Access Control ablaktábla](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. A megnyíló jobb oldali kontextus ablaktáblán adja meg a következőt:<br>

    **Szerepkör:** Olvasó<br>
    **Hozzáférés társítása a** következőhöz: **Backup-tároló** kiválasztása<br>
    Ha nem találja a **Backup-tároló** lehetőséget a legördülő listában, válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév lehetőséget** .<br>

    ![Válasszon szerepkört](./media/backup-azure-database-postgresql/select-role.png)

    **Válassza a következőket:** Adja meg azt a mentési tároló nevét, amelyről biztonsági másolatot szeretne készíteni a kiszolgálóról és annak adatbázisairól.<br>

    ![Adja meg a Backup-tároló nevét](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Hozzon létre egy adatbázis-biztonsági mentési felhasználót, amely a Azure Active Directory használatával tud hitelesíteni:

Ez a hiba a PostgreSQL-kiszolgáló Azure Active Directory rendszergazdájától, illetve a Azure Active Directory használatával hitelesíthető biztonsági mentési felhasználó hiányában lehet.

Lépések:

Active Directory-rendszergazda hozzáadása az OSS-kiszolgálóhoz:

Ez a lépés szükséges ahhoz, hogy egy olyan felhasználón keresztül kapcsolódjon az adatbázishoz, amely hitelesíti magát Azure Active Directory jelszó helyett. Az Azure AD rendszergazdai felhasználója Azure Database for PostgreSQL a szerepkör **azure_ad_admin**. Csak **azure_ad_admin** -szerepkör hozhat létre olyan új adatbázis-felhasználókat, akik hitelesíteni tudják az Azure ad-t.

1. Nyissa meg a Active Directory adminisztrátor lapot a kiszolgáló nézet bal oldali navigációs paneljén, és adja hozzá magát (vagy valaki másnak) a Active Directory-rendszergazdaként.

    ![Active Directory-rendszergazda beállítása](./media/backup-azure-database-postgresql/set-admin.png)

1. Ügyeljen arra, hogy az AD rendszergazdai felhasználói beállítást **mentse** .

    ![Active Directory rendszergazdai felhasználói beállítás mentése](./media/backup-azure-database-postgresql/save-admin-setting.png)

Tekintse át [ezt a dokumentumot](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) az engedélyek megadásához szükséges lépések végrehajtásához.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Hozzon létre hálózati vonalat, ha engedélyezi az **Azure-szolgáltatások hozzáférésének engedélyezése** a kiszolgáló nézetben lehetőséget. A kiszolgáló nézetben a **kapcsolat biztonsága** panelen állítsa az **Azure-szolgáltatások hozzáférésének engedélyezése** **beállítást igen** értékre.

![Hozzáférés engedélyezése Azure-szolgáltatások számára](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>A Storage-fiók tárolóba való visszaállításának engedélyezése fájlként

1. Adja meg a Backup-tároló MSI-fájljának a Storage-fiók tárolóinak elérésére vonatkozó engedélyt a Azure Portal használatával.
    1. Nyissa meg a **Storage-fiók**  ->  **Access Control**  ->  **szerepkör-hozzárendelés hozzáadása** lehetőséget.
    1. Rendeljen hozzá **Storage blob-adatközreműködői** szerepkört a Backup-tároló MSI-fájlhoz.

    ![Tároló blob-adatközreműködői szerepkörének kiosztása](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Azt is megteheti, hogy részletes engedélyeket ad a visszaállításhoz használt tárolóhoz az Azure CLI az az [role hozzárendelés Create](/cli/azure/role/assignment) paranccsal.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Cserélje le a hozzárendelt paramétert a tár MSI- **azonosítójával** és a hatókör-paraméterrel, hogy az adott tárolóra hivatkozzon.
    1. A tároló MSI- **azonosítójának** lekéréséhez válassza a **minden alkalmazás** lehetőséget az **alkalmazás típusa** területen:

        ![Az összes alkalmazás kijelölése](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Keresse meg a tár nevét, és másolja ki az alkalmazás AZONOSÍTÓját:

        ![Tár nevének keresése](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Következő lépések

- [Backup-tárolók áttekintése](backup-vault-overview.md)