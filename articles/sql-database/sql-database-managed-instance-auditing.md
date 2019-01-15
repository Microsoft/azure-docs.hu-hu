---
title: Az Azure SQL Database felügyelt példány naplózása |} A Microsoft Docs
description: Ismerje meg, hogyan kezdheti el az Azure SQL Database felügyelt példány naplózási T-SQL használatával
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: vainolo
ms.reviewer: vanto
manager: craigg
ms.date: 01/12/2019
ms.openlocfilehash: 716c4caa1b28cc40470d366e5fc6901de9462f9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267266"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Ismerkedés az Azure SQL Database felügyelt példány naplózási szolgáltatásával

[Az Azure SQL Database felügyelt példányain](sql-database-managed-instance.md) naplózási nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban. A naplózás is:

- Segít a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést nyerhet az eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági problémákat.
- Lehetővé teszi, hogy, és megkönnyíti a megfelelőségi szabványok betartásának, bár ez nem garantálja a megfelelőség. Az Azure-ral kapcsolatos további információkat a támogatási szabványoknak való megfelelés programokat, tekintse meg a [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/compliance/).

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>A kiszolgáló, az Azure Storage-naplózás beállítása 

Az alábbi szakasz ismerteti a felügyelt példány naplózásának konfigurálása.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Az alábbi lépéseket, hozzon létre egy Azure Storage **tároló** naplók tárolására.

   - Keresse meg az Azure Storage, ahol szeretné a naplófájlok.

     > [!IMPORTANT]
     > A felügyelt példány kiszolgáló ugyanabban a régióban a storage-fiók használatával régiók közötti írásának/olvasásának elkerülése érdekében.

   - A storage-fiókban, lépjen a **áttekintése** kattintson **Blobok**.

     ![Navigációs ablaktábla][1]

   - A felső menüben kattintson **+ tároló** hozhat létre egy új tárolót.

     ![Navigációs ablaktábla][2]

   - Adjon meg egy tároló **neve**, állítsa a nyilvános hozzáférés szintet **privát**, és kattintson a **OK**.

     ![Navigációs ablaktábla][3]

   - A tárolók listájában kattintson az újonnan létrehozott tárolóban majd **tároló tulajdonságai**.

     ![Navigációs ablaktábla][4]

   - Másolja a tároló URL-címet a másolási ikonra kattintva, és mentse az URL-címe (például a Jegyzettömbben) későbbi használatra. A tároló URL-cím formátumban kell megadni `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigációs ablaktábla][5]

3. Az alábbi lépéseket, hozzon létre egy Azure Storage **SAS-Token** felügyelt példány naplózás hozzáférési jogosultságokat biztosíthat a storage-fiók segítségével.

   - Keresse meg az Azure Storage-fiók, amelyben az előző lépésben létrehozta a tárolót.

   - Kattintson a **közös hozzáférésű jogosultságkód** tárolási beállítások menüjében.

     ![Navigációs ablaktábla][6]

   - Az SAS a következőképpen konfigurálja:
     - **Engedélyezett szolgáltatások**: Blob
     - **Kezdő dátum**: időzóna kapcsolatos problémák elkerülése érdekében javasoljuk, hogy tegnapi dátum használata.
     - **Befejező dátum**: válassza ki, amelyen a SAS-jogkivonat lejárati dátumának. 

       > [!NOTE]
       > A naplózási hibák elkerülése érdekében a lejárat után-token megújításához.

     - Kattintson az **SAS előállítása** gombra.

       ![Navigációs ablaktábla][7]

   - SAS létrehozása gombra kattintva a SAS-Token alján jelenik meg. Jogkivonat másolása a másolási ikonra kattintva, és későbbi használatra mentse azt (például a Jegyzettömbbel).

     > [!IMPORTANT]
     > Távolítsa el a kérdőjel ("?") karaktert a jogkivonat az elejétől.

     ![Navigációs ablaktábla][8]

4. Csatlakozhat a felügyelt példány az SQL Server Management Studio (SSMS) használatával.

5. Hajtsa végre a következő T-SQL utasítást **hozzon létre egy új hitelesítő adat** a tároló URL-címe és a SAS-Token, amely az előző lépésekben létrehozott használatával:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Hajtsa végre a következő T-SQL utasítást hozzon létre egy új Server Audit (a saját naplózási nevet, használja az előző lépésekben létrehozott tároló URL-címe):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Ha nincs megadva, `RETENTION_DAYS` alapértelmezett érték a 0 (korlátlan a megőrzés).

    További információ:
    - [Naplózás a felügyelt példány, az Azure SQL Database és az SQL Server közötti különbségek](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [KISZOLGÁLÓ NAPLÓZÁSI LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [AZ ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Az SQL Server ugyanúgy Server Audit Specification vagy Specifikációjába létrehozása:
    - [Hozzon létre a kiszolgáló naplózási specifikáció T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Hozzon létre az adatbázis naplózási specifikáció T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. A 6. lépésben létrehozott kiszolgálói naplózás engedélyezése:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="set-up-auditing-for-your-server-to-event-hub-or-log-analytics"></a>Állítsa be a naplózást a kiszolgáló Event Hubs és a Log Analytics számára

A felügyelt példány auditnaplók még Hubs és a Log Analytics az Azure Monitor használatával lehet küldeni. Ez a szakasz ismerteti, hogyan konfigurálhatja ezt:

1. Navigálás a [az Azure Portal](https://portal.azure.com/) az SQL felügyelt példánya.

2. Kattintson a **diagnosztikai beállítások**.

3. Kattintson a **diagnosztika bekapcsolása**. Ha diagnosztikai már engedélyezve van a *+ diagnosztikai beállítás hozzáadása* jelennek meg helyette.

4. Válassza ki **SQLSecurityAuditEvents** naplók listájában.

5. Válassza ki a célhelyet a naplózási események – Event Hub, a Log Analytics vagy mindkettőt. Minden egyes célhoz konfigurálja a szükséges paramétereket (például: Log Analytics-munkaterület).

6. Kattintson a **Save** (Mentés) gombra.

  ![Navigációs ablaktábla][9]

7. Csatlakozhat a felügyelt példány használatával **SQL Server Management Studio (SSMS)** vagy más támogatott ügyfél.

8. Hajtsa végre a következő T-SQL utasítást a kiszolgáló naplózási létrehozásához:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Hozzon létre egy kiszolgáló naplóspecifikáció vagy a specifikációjába, mint az SQL Server:

   - [Hozzon létre a kiszolgáló naplózási specifikáció T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Hozzon létre az adatbázis naplózási specifikáció T-SQL-útmutató](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. A 7. lépésben létrehozott kiszolgálói naplózás engedélyezése:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>A naplófájlok felhasználása

### <a name="consume-logs-stored-in-azure-storage"></a>Az Azure Storage szolgáltatásban tárolt naplókért felhasználása

Többféleképpen naplófájlokat blob megtekintéséhez használhatja.

- A rendszer függvénnyel `sys.fn_get_audit_file` (T-SQL) a naplózási adatokat vissza a táblázatos formátumban. Ez a funkció használatáról további információkért lásd: a [sys.fn_get_audit_file dokumentáció](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Például egy eszköz használatával megvizsgálhatja auditnaplók [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/). Az Azure storage-ban naplói sqldbauditlogs nevű tárolóban lévő blob fájlok kerülnek mentésre. A tároló mappa a hierarchiával kapcsolatos további részletekért elnevezési konvenciók és a napló formátuma, tekintse meg a [Blob auditálási napló fájlformátum referenciája](https://go.microsoft.com/fwlink/?linkid=829599).

- Auditálási napló felhasználási módszert teljes listájáért tekintse meg a [első lépései az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Megtekintése az Azure Portalon ("Naplórekordok" ablaktábla) a naplózási rekordoknak a felügyelt példány jelenleg nem érhetők el.

### <a name="consume-logs-stored-in-event-hub"></a>Event Hub tárolt naplók használata

Az Event Hubs naplózási adatok felhasználásához, szüksége lesz egy stream események felhasználásához, és a cél beállítása. További információkért tekintse meg az Azure Event Hubs – dokumentáció.

### <a name="consume-and-analyze-logs-stored-in-log-analytics"></a>Ugyanúgy használják, és tárolja a Log Analytics-naplók elemzése

A Log Analytics naplók írt, ha azok elérhetők a Log Analytics-munkaterületet, ahol a Speciális keresés futtatásához a naplózási adatok. Kiindulási pontként, keresse meg a Log Analytics és a *általános* szakaszban kattintson *naplók* írjon be egy egyszerű lekérdezéssel, például: `search "SQLSecurityAuditEvents"` naplózza a naplózási megtekintéséhez.  

A log Analytics azonnal elemezze a rekordok millióit, a számítási feladatok és kiszolgálók integrált keresést és egyéni irányítópultok segítségével valós idejű az operational insights biztosítja. További hasznos információkat a Log Analytics keresési nyelv és a parancsok, lásd: [Log Analytics keresési referenciáját bemutató](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Naplózás a felügyelt példány, az Azure SQL Database és az SQL Server közötti különbségek

A felügyelt példány, az Azure SQL Database és az SQL Server helyszíni SQL-naplózási közötti fő különbségeket a következők:

- A felügyelt példány az SQL Audit működik, a kiszolgáló szintjén, és a tárolók `.xel` naplófájlokat az Azure blob storage-fiók.
- Az Azure SQL Database SQL-naplózás az adatbázis szintjén működik.
- A helyszíni SQL Server / virtuális gépek, az SQL Audit működik a kiszolgálón szintű, de a tárolók eseményeket fájlok rendszer-, Windows eseménynaplók.

Az Azure blob storage tárolók XEvent naplózási a felügyelt példányt támogat. Fájl- és windows-naplók **nem támogatott**.

A kulcs közötti különbségek a `CREATE AUDIT` szintaxist a naplózás az Azure blob storage-vannak:

- Egy új szintaxis `TO URL` van megadva, és lehetővé teszi a URL-címét az Azure blob Storage-tárolóba, a `.xel` fájlok kerülnek.
- Egy új szintaxis `TO EXTERNAL MONITOR` ahhoz, hogy még a hubot és a Log Analytics célok biztosított.
- A szintaxist `TO FILE` van **nem támogatott** , mert a felügyelt példány nem érhető el Windows-fájlmegosztásokon.
- Leállítási lehetőség **nem támogatott**.
- `queue_delay` a 0 van **nem támogatott**.

## <a name="next-steps"></a>További lépések

- Auditálási napló felhasználási módszert teljes listájáért tekintse meg a [első lépései az SQL database naplózási szolgáltatásával](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).
- Az Azure-ral kapcsolatos további információkat a támogatási szabványoknak való megfelelés programokat, tekintse meg a [Azure adatvédelmi központ](https://azure.microsoft.com/support/trust-center/compliance/).

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
[9]: ./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png
