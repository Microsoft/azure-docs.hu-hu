---
title: SQL felügyelt példányok naplózása
description: Ismerje meg, hogyan kezdheti el az Azure SQL felügyelt példányok naplózását a T-SQL használatával
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: ae0d9696d869b2a260de643482a9f86c34bcc824
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575469"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Ismerkedés az Azure SQL felügyelt példányok naplózásával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az [Azure SQL felügyelt példányának](sql-managed-instance-paas-overview.md) naplózása nyomon követi az adatbázis eseményeit, és az Azure Storage-fiókban lévő naplóba írja azokat. A naplózás további előnyei:

- Segít a jogszabályi megfelelőség fenntartásában és az adatbázison végzett tevékenység megértésében, valamint az esetleg üzleti veszélyeket vagy biztonsági problémákat jelző rendellenességek feltárásában.
- Lehetővé teszi és megkönnyíti a megfelelőségi szabványok betartását, bár nem mindig garantálja a megfelelőséget. A szabványok megfelelőségét támogató Azure-programokkal kapcsolatos további információkért tekintse meg a [Azure biztonsági és adatkezelési központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálhatja a megfelelőségi tanúsítványok legújabb listáját.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Naplózás beállítása a kiszolgálóhoz az Azure Storage-hoz

A következő szakasz ismerteti a felügyelt példány naplózásának konfigurációját.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Hozzon létre egy Azure Storage- **tárolót** , amelyben a naplók tárolása történik.

   1. Navigáljon ahhoz az Azure Storage-fiókhoz, ahol a naplókat tárolni szeretné.

      > [!IMPORTANT]
      > - A régiók közötti olvasási/írási műveletek elkerülése érdekében használjon a felügyelt példányokkal megegyező régióban található Storage-fiókot. 
      > - Ha a Storage-fiók egy Virtual Network vagy egy tűzfal mögött található, tekintse meg [a virtuális hálózatról való hozzáférés engedélyezése](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)című témakört.
      > - Ha a megőrzési időszakot 0 (korlátlan megőrzés) értékre módosítja bármely más értékre, vegye figyelembe, hogy az adatmegőrzés csak a megőrzési érték módosítását követően írt naplókra vonatkozik

   1. A Storage-fiókban válassza az **Áttekintés** lehetőséget, majd kattintson a **Blobok** elemre.

      ![Azure-Blobok widget](./media/auditing-configure/1_blobs_widget.png)

   1. A felső menüben kattintson a **+ tároló** elemre egy új tároló létrehozásához.

      ![BLOB-tároló létrehozása ikon](./media/auditing-configure/2_create_container_button.png)

   1. Adja meg a tároló **nevét**, állítsa a **nyilvános hozzáférési szintet** **magán** értékre, majd kattintson **az OK** gombra.

      ![BLOB-tároló konfigurációjának létrehozása](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Azok az ügyfelek, akik nem módosítható napló-tárolót kívánnak konfigurálni a kiszolgálói vagy adatbázis-szintű naplózási események esetében, az [Azure Storage által biztosított utasításokat](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes)követik. (Ügyeljen arra, hogy a megváltoztathatatlan blob-tároló konfigurálásakor a **további Hozzáfűzések engedélyezése lehetőséget** választotta.)
  
3. Miután létrehozta a tárolót a naplók számára, kétféleképpen konfigurálhatja azt célként a naplók számára: a [T-SQL használatával](#blobtsql) vagy [a SQL Server Management Studio (SSMS) felhasználói felület használatával](#blobssms):

   - <a id="blobtsql"></a>**BLOB Storage konfigurálása a naplókhoz a T-SQL használatával:**

     1. A tárolók listában kattintson az újonnan létrehozott tárolóra, majd a **tároló tulajdonságai** elemre.

        ![BLOB-tároló tulajdonságai gomb](./media/auditing-configure/4_container_properties_button.png)

     1. Másolja a tároló URL-címét a másolás ikonra kattintva, és mentse az URL-címet (például a Jegyzettömbben) későbbi használatra. A tároló URL-formátumának `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![BLOB-tároló másolási URL-címe](./media/auditing-configure/5_container_copy_name.png)

     1. Azure Storage sas- **token** létrehozása a felügyelt példányok naplózásához a Storage-fiókhoz való hozzáférési jogosultságok biztosításához:

        - Navigáljon ahhoz az Azure Storage-fiókhoz, ahol az előző lépésben létrehozta a tárolót.

        - Kattintson a **megosztott hozzáférés aláírása** lehetőségre a **tárolási beállítások** menüben.

          ![Közös hozzáférésű aláírás ikon a tárolási beállítások menüben](./media/auditing-configure/6_storage_settings_menu.png)

        - Konfigurálja az SAS-t a következőképpen:

          - **Engedélyezett szolgáltatások**: blob

          - **Kezdési dátum**: az időzónával kapcsolatos problémák elkerülése érdekében használja a tegnapi dátumot

          - **Befejezési dátum**: válassza ki a dátumot, AMELYEN az SAS-jogkivonat lejár

            > [!NOTE]
            > A naplózási hibák elkerülése érdekében újítsa meg a tokent a lejárat után.

          - Kattintson az **SAS előállítása** gombra.

            ![SAS-konfiguráció](./media/auditing-configure/7_sas_configure.png)

        - Az SAS-jogkivonat alul jelenik meg. Másolja a tokent a másolás ikonra kattintva, és mentse (például a Jegyzettömbben) későbbi használatra.

          ![SAS-token másolása](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Eltávolítja a kérdőjel ("?") karaktert a jogkivonat elejétől.

     1. Kapcsolódjon a felügyelt példányhoz SQL Server Management Studio vagy bármely más támogatott eszközön keresztül.

     1. A következő T-SQL-utasítás végrehajtásával **hozzon létre egy új hitelesítő adatot** az előző lépésekben létrehozott Container URL-lel és Sas-token használatával:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. A következő T-SQL-utasítás végrehajtásával hozzon létre egy új kiszolgáló-naplózást (válassza ki a saját napló nevét, és használja az előző lépésekben létrehozott Container URL-címet). Ha nincs megadva, az `RETENTION_DAYS` alapértelmezett érték a 0 (korlátlan megőrzés):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Folytassa [a kiszolgáló naplózási specifikációjának vagy adatbázis-naplózási specifikációjának létrehozásával](#createspec).

   - <a id="blobssms"></a>**BLOB Storage konfigurálása a naplókhoz a SQL Server Management Studio 18 használatával:**

     1. Kapcsolódjon a felügyelt példányhoz a SQL Server Management Studio felhasználói felület használatával.

     1. Bontsa ki a Object Explorer legfelső szintű megjegyzését.

     1. Bontsa ki a **Biztonság** csomópontot, kattintson a jobb gombbal a **naplózás** csomópontra, majd kattintson az **új naplózás** elemre:

        ![Biztonsági és naplózási csomópont kibontása](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Győződjön meg arról, hogy az **URL-cím** van kijelölve a **naplózási célhelyen** , és kattintson a **Tallózás** gombra

        ![Az Azure Storage tallózása](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. Választható Jelentkezzen be az Azure-fiókjába:

        ![Bejelentkezés az Azure-ba](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Válassza ki az előfizetést, a Storage-fiókot és a BLOB-tárolót a legördülő listából, vagy hozzon létre egy saját tárolót a **Létrehozás** gombra kattintva. Ha elkészült, kattintson **az OK gombra**:

        ![Válassza az Azure-előfizetés, a Storage-fiók és a blob-tároló elemet.](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. A **naplózás létrehozása** párbeszédpanelen kattintson az **OK** gombra.
     
     1. <a id="createspec"></a>Miután konfigurálta a BLOB tárolót célként a naplók számára, hozzon létre és engedélyezzen egy kiszolgáló-naplózási specifikációt vagy egy adatbázis-naplózási specifikációt, ahogy az SQL Server:

   - [Kiszolgáló naplózási specifikációjának létrehozása T-SQL-útmutató](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-naplózási specifikáció létrehozása T-SQL-útmutató](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Engedélyezze a 3. lépésben létrehozott kiszolgáló-naplózást:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

További információ:

- [A felügyelt Azure SQL-példányok és a SQL Server-adatbázisok közötti különbségek naplózása](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [KISZOLGÁLÓ NAPLÓZÁSÁNAK LÉTREHOZÁSA](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Naplózás beállítása a kiszolgálón Event Hubs vagy Azure Monitor naplókhoz

A felügyelt példányok naplóit az Azure-Event Hubsba vagy Azure Monitor naplókba lehet elküldeni. Ez a szakasz a konfigurálásának módját ismerteti:

1. Navigáljon a [Azure Portal](https://portal.azure.com/) a felügyelt példányhoz.

2. Kattintson a **diagnosztikai beállítások** elemre.

3. Kattintson a **diagnosztika bekapcsolása** elemre. Ha a diagnosztika már engedélyezve van, a **+ diagnosztikai beállítások hozzáadása** lehetőség jelenik meg helyette.

4. A naplók listájában válassza a **SQLSecurityAuditEvents** lehetőséget.

5. Válassza ki a naplózási események célhelyét: Event Hubs, Azure Monitor naplókat vagy mindkettőt. Konfigurálja az egyes célkitűzésekhez a szükséges paramétereket (például Log Analytics munkaterület).

6. Kattintson a **Mentés** gombra.

    ![Diagnosztikai beállítások konfigurálása](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Kapcsolódjon a felügyelt példányhoz **SQL Server Management Studio (SSMS)** vagy bármely más támogatott ügyfél használatával.

8. A következő T-SQL-utasítás végrehajtásával hozzon létre egy kiszolgáló-naplózást:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. A kiszolgáló naplózási specifikációjának vagy adatbázis-naplózási specifikációjának létrehozása és engedélyezése SQL Server esetén:

   - [Kiszolgáló naplózási specifikációjának létrehozása T-SQL-útmutató](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Adatbázis-naplózási specifikáció létrehozása T-SQL-útmutató](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Engedélyezze a 8. lépésben létrehozott kiszolgáló-naplózást:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Naplók felhasználása

### <a name="consume-logs-stored-in-azure-storage"></a>Az Azure Storage-ban tárolt naplók felhasználása

Több módszer is használható a blob-naplózási naplók megtekintésére.

- A rendszerfunkció `sys.fn_get_audit_file` (T-SQL) segítségével táblázatos formátumban visszaküldheti a naplózási adatokat. A függvény használatáról a [sys.fn_get_audit_file dokumentációjában](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)olvashat bővebben.

- A naplókat a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)eszközzel is megismerheti. Az Azure Storage-ban a naplózási naplók a naplófájlok tárolására megadott tárolóban lévő blob-fájlok gyűjteményében lesznek mentve. A tárolási mappa hierarchiájának, az elnevezési konvencióknak és a napló formátumának további részleteiért tekintse meg a [blob naplózási napló formátumának referenciáját](../database/audit-log-format.md).

- A naplózási naplók használati módszereinek teljes listájáért tekintse meg az [Ismerkedés a Azure SQL Database naplózással](../../azure-sql/database/auditing-overview.md)című témakört.

### <a name="consume-logs-stored-in-event-hubs"></a>Event Hubs tárolt naplók felhasználása

A Event Hubsi naplók adatainak felhasználásához be kell állítania egy streamet, hogy az eseményeket felhasználja, és egy célhelyre írja őket. További információkért tekintse meg az Azure Event Hubs dokumentációját.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Monitor-naplókban tárolt naplók felhasználása és elemzése

Ha a naplókat Azure Monitor naplókba írja a rendszer, azok a Log Analytics munkaterületen érhetők el, ahol a naplózási adatként speciális kereséseket futtathat. Kiindulási pontként navigáljon a Log Analytics munkaterületre. A **General (általános** ) szakaszban kattintson a **naplók** elemre, és adjon meg egy egyszerű lekérdezést, például: a naplók `search "SQLSecurityAuditEvents"` megtekintéséhez.  

A Azure Monitor naplók valós idejű üzemeltetési elemzéseket biztosítanak az integrált keresés és az egyéni irányítópultok használatával, amelyekkel a munkaterhelések és a kiszolgálók több millió rekordját is könnyedén elemezheti. További hasznos információk Azure Monitor naplók keresési nyelvéről és parancsairól: [Azure monitor naplók keresési referenciája](../../azure-monitor/logs/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Az Azure SQL felügyelt példányai és adatbázisai közötti különbségek naplózása SQL Server

Az Azure SQL felügyelt példányaiban és SQL Serverban található adatbázisok naplózása közötti fő különbségek a következők:

- Az Azure SQL felügyelt példányain a naplózás a kiszolgáló szintjén működik, és a `.xel` naplófájlokat az Azure Blob Storage-ban tárolja.
- SQL Server a naplózás a kiszolgáló szintjén működik, de az eseményeket a fájlrendszer/Windows eseménynaplókban tárolja.

A felügyelt példányok XEvent-naplózása támogatja az Azure Blob Storage-célokat. A fájl-és Windows-naplók **nem támogatottak**.

Az `CREATE AUDIT` Azure Blob Storage-ba való naplózás szintaxisának főbb eltérései a következők:

- A rendszer új szintaxist `TO URL` biztosít, és lehetővé teszi az Azure Blob Storage-tároló URL-címének megadását, ahol a `.xel` fájlok el vannak helyezve.
- A rendszer új szintaxist `TO EXTERNAL MONITOR` biztosít a Event Hubs és Azure monitor naplózási célok engedélyezéséhez.
- A szintaxis `TO FILE` **nem támogatott** , mert az Azure SQL felügyelt példánya nem fér hozzá a Windows-fájlmegosztást.
- A Leállítás beállítás **nem támogatott**.
- `queue_delay` a 0 érték **nem támogatott**.

## <a name="next-steps"></a>Következő lépések

- A naplózási naplók használati módszereinek teljes listájáért tekintse meg az [Ismerkedés a Azure SQL Database naplózással](../../azure-sql/database/auditing-overview.md)című témakört.
- A szabványok megfelelőségét támogató Azure-programokkal kapcsolatos további információkért tekintse meg a [Azure biztonsági és adatkezelési központ](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálhatja a megfelelőségi tanúsítványok legújabb listáját.

<!--Image references-->
