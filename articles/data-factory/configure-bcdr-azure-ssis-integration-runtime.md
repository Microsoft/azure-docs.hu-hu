---
title: Az Azure SSIS integrációs modul konfigurálása az üzletmenet folytonossága és a vész-helyreállítás érdekében (BCDR)
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure-SSIS integrációs modult Azure Data Factoryban Azure SQL Database/felügyelt példány feladatátvételi csoporttal az üzletmenet folytonossága és a vész-helyreállítás (BCDR) számára.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2744d51b6d68ed494050be10a9f0e4d1f59cdc49
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204065"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Az Azure SSIS integrációs modul konfigurálása az üzletmenet folytonossága és a vész-helyreállítás érdekében (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) Azure SQL Database/felügyelt példánya és SQL Server Integration Services (SSIS) kombinálható a SQL Server áttelepítéshez javasolt, teljes körű szolgáltatásként szolgáló megoldásként (Pásti). A SSIS-projekteket üzembe helyezheti Azure SQL Database/felügyelt példány által üzemeltetett SSIS Catalog Database-ben (SSISDB), és az Azure SSIS Integration Runtime (IR)-ben futtathatja a SSIS-csomagokat az ADF-ben.

Az üzletmenet folytonossága és a vész-helyreállítás (BCDR) esetében a Azure SQL Database/felügyelt példányok [földrajzi replikálási/feladatátvételi csoporttal](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)konfigurálhatók, ahol a SSISDB egy elsődleges Azure-régióban, amely olvasási és írási hozzáféréssel rendelkezik (elsődleges szerepkör), a rendszer folyamatosan replikálja egy másodlagos régióba, csak olvasási hozzáféréssel (másodlagos szerepkörrel). Ha az elsődleges régióban katasztrófa következik be, a rendszer elindítja a feladatátvételt, ahol az elsődleges és másodlagos SSISDBs a szerepköröket fogja felcserélni.

A BCDR olyan kettős készenléti Azure SSIS IR-párokat is beállíthat, amelyek szinkronban vannak Azure SQL Database/felügyelt példányok feladatátvételi csoportjának használatával. Ez lehetővé teszi, hogy az Azure-SSIS IRs-t egy pár alkalommal futtassuk, és a csomagok beolvasására és végrehajtására, valamint a csomag végrehajtási naplói (elsődleges szerepkör) írására csak az elsődleges SSISDB lehessen hozzáférni, míg a másik nem ugyanaz, mint a máshol üzembe helyezett csomagok esetében, például Azure Files (másodlagos szerepkör). A SSISDB feladatátvétel esetén az elsődleges és a másodlagos Azure-SSIS IRs is felcseréli a szerepköröket, és ha mindkettő fut, akkor a közel nulla állásidőt fogja tartalmazni.

Ez a cikk azt ismerteti, hogyan lehet konfigurálni a BCDR Azure SQL Database/felügyelt példányok feladatátvételi csoportjának Azure-SSIS IR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Kettős készenléti Azure-SSIS IR pár beállítása Azure SQL Database feladatátvételi csoporttal

Ha olyan kettős készenléti Azure-SSIS IR szeretne konfigurálni, amely Azure SQL Database feladatátvételi csoporttal szinkronizálva működik, hajtsa végre a következő lépéseket.

1. Azure Portal/ADF felhasználói felület használatával új Azure-SSIS IR hozhat létre az elsődleges Azure SQL Database-kiszolgálóval az elsődleges régióban lévő SSISDB üzemeltetéséhez. Ha van olyan meglévő Azure-SSIS IR, amely már az elsődleges Azure SQL Database-kiszolgáló által üzemeltetett SSIDB van csatolva, és még fut, akkor először le kell állítania az újrakonfigurálást. Ez lesz az elsődleges Azure-SSIS IR.

   Ha a [SSISDB használatát választja](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) az **Integration Runtime telepítése** ablaktábla **központi telepítési beállítások** lapján, jelölje be a **kettős készenléti Azure-SSIS Integration Runtime pár használata SSISDB feladatátvételsel** jelölőnégyzetet is. A **kettős készenléti páros neve** mezőben adjon meg egy nevet az elsődleges és másodlagos Azure-SSIS IRs-példány azonosításához. Az elsődleges Azure-SSIS IR létrehozásának befejezése után a rendszer elindít egy elsődleges SSISDB, amelyet az Ön nevében az írási és olvasási hozzáféréssel fog létrehozni. Ha most újrakonfigurálta, újra kell indítania.

1. Azure Portal használatával megtekintheti, hogy az elsődleges SSISDB létrejött-e az elsődleges Azure SQL Database-kiszolgáló **Áttekintés** lapján. A létrehozása után [létrehozhat egy feladatátvételi csoportot az elsődleges és másodlagos Azure SQL Database-kiszolgálókhoz, és HOZZÁADHAT SSISDB](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) a **feladatátvételi csoportok** lapon. A feladatátvételi csoport létrehozása után megtekintheti, hogy az elsődleges SSISDB replikálva lett-e a másodlagos Azure SQL Database-kiszolgáló **Áttekintés** lapján található írásvédett hozzáféréssel.

1. Azure Portal/ADF felhasználói felület használatával létrehozhat egy másik Azure-SSIS IR a másodlagos Azure SQL Database-kiszolgálóval a másodlagos régió SSISDB üzemeltetéséhez. Ez lesz a másodlagos Azure-SSIS IR. A teljes BCDR győződjön meg arról, hogy az összes, az általa használt erőforrás a másodlagos régióban is létrejön, például az Azure Storage az egyéni telepítési parancsfájlok/fájlok tárolásához, az ADF előkészítéséhez és az ütemezési csomagok végrehajtásához stb.

   Ha a [SSISDB használatát választja](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) az **Integration Runtime telepítése** ablaktábla **központi telepítési beállítások** lapján, jelölje be a **kettős készenléti Azure-SSIS Integration Runtime pár használata SSISDB feladatátvételsel** jelölőnégyzetet is. A **kettős készenléti párok neve** mezőben adja meg ugyanazt a nevet, hogy azonosítsa az elsődleges és a másodlagos Azure-SSIS IRs-t. A másodlagos Azure-SSIS IR létrehozásának befejezése után a rendszer elindítja és csatolja a másodlagos SSISDB.

1. Ha a SSISDB feladatátvétel esetén közel nulla állásidőt szeretne használni, tartsa meg mindkét Azure-SSIS-t. Csak az elsődleges Azure-SSIS IR férhet hozzá az elsődleges SSISDB a csomagok beolvasásához és végrehajtásához, valamint a csomag-végrehajtási naplók írásához, míg a másodlagos Azure-SSIS IR csak a máshová telepített csomagok esetében lehet ugyanazokat, például Azure Files.

   Ha csökkenteni szeretné a futó költségeket, a létrehozás után leállíthatja a másodlagos Azure-SSIS IR. SSISDB feladatátvétel esetén az elsődleges és a másodlagos Azure-SSIS IRs a szerepköröket fogja felcserélni. Ha az elsődleges Azure-SSIS IR leáll, újra kell indítania. Attól függően, hogy a virtuális hálózatba van-e befecskendezve, és a felhasznált injekciós módszer, 5 percen belül, vagy körülbelül 20-30 percen belül eltarthat.

1. Ha az [ADF-t az előkészítési/ütemezési csomagok végrehajtásához használja](./how-to-invoke-ssis-package-ssis-activity.md), győződjön meg arról, hogy az összes kapcsolódó ADF-folyamat a végrehajtás SSIS csomag tevékenységeivel és a hozzájuk tartozó eseményindítók a másodlagos ADF-be van másolva az eredetileg letiltott eseményindítókkal. SSISDB feladatátvétel esetén engedélyeznie kell azokat.

1. [Tesztelheti Azure SQL Database feladatátvételi csoportját](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) , és megtekintheti az [ADF-portál Azure-SSIS IR figyelés lapján](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , hogy az elsődleges és másodlagos Azure-SSIS IRs felcserélte-e a szerepköröket. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Kettős készenléti Azure-SSIS IR pár beállítása az Azure SQL felügyelt példányának feladatátvételi csoportjához

Az Azure SQL felügyelt példányok feladatátvételi csoportjának szinkronizálásával működő kettős készenléti Azure-SSIS IR-párok konfigurálásához hajtsa végre az alábbi lépéseket.

1. A Azure Portal használatával [létrehozhat egy feladatátvételi csoportot az elsődleges és másodlagos Azure SQL felügyelt példányaihoz](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) az elsődleges Azure SQL felügyelt példány **feladatátvételi csoportok** lapján.

1. Azure Portal/ADF felhasználói felület használatával új Azure-SSIS IR hozhat létre az elsődleges Azure SQL felügyelt példányával az elsődleges régióban lévő SSISDB üzemeltetéséhez. Ha van olyan meglévő Azure-SSIS IR, amely már az elsődleges Azure SQL felügyelt példánya által üzemeltetett SSIDB van csatolva, és továbbra is fut, előbb le kell állítania az újrakonfigurálást. Ez lesz az elsődleges Azure-SSIS IR.

   Ha a [SSISDB használatát választja](./create-azure-ssis-integration-runtime.md#creating-ssisdb) az **Integration Runtime telepítése** ablaktábla **központi telepítési beállítások** lapján, jelölje be a **kettős készenléti Azure-SSIS Integration Runtime pár használata SSISDB feladatátvételsel** jelölőnégyzetet is. A **kettős készenléti páros neve** mezőben adjon meg egy nevet az elsődleges és másodlagos Azure-SSIS IRs-példány azonosításához. Az elsődleges Azure-SSIS IR létrehozásának befejezése után a rendszer elindít egy elsődleges SSISDB, amelyet az Ön nevében az írási és olvasási hozzáféréssel fog létrehozni. Ha most újrakonfigurálta, újra kell indítania. Azt is megtekintheti, hogy az elsődleges SSISDB replikálása a másodlagos Azure SQL felügyelt példányának **Áttekintés** lapján csak olvasási hozzáféréssel történt-e.

1. Azure Portal/ADF felhasználói felület használatával létrehozhat egy másik Azure-SSIS IR a másodlagos Azure SQL felügyelt példánnyal a másodlagos régióban lévő SSISDB üzemeltetéséhez. Ez lesz a másodlagos Azure-SSIS IR. A teljes BCDR győződjön meg arról, hogy az összes, az általa használt erőforrás a másodlagos régióban is létrejön, például az Azure Storage az egyéni telepítési parancsfájlok/fájlok tárolásához, az ADF előkészítéséhez és az ütemezési csomagok végrehajtásához stb.

   Ha a [SSISDB használatát választja](./create-azure-ssis-integration-runtime.md#creating-ssisdb) az **Integration Runtime telepítése** ablaktábla **központi telepítési beállítások** lapján, jelölje be a **kettős készenléti Azure-SSIS Integration Runtime pár használata SSISDB feladatátvételsel** jelölőnégyzetet is. A **kettős készenléti párok neve** mezőben adja meg ugyanazt a nevet, hogy azonosítsa az elsődleges és a másodlagos Azure-SSIS IRs-t. A másodlagos Azure-SSIS IR létrehozásának befejezése után a rendszer elindítja és csatolja a másodlagos SSISDB.

1. Az Azure SQL felügyelt példánya bizalmas adatokat biztosíthat az adatbázisokban (például SSISDB) az adatbázis főkulcsával (DMK) történő titkosítással. A DMK önmagában a Service Master Key (SMK) használatával titkosítva van. Az írás időpontjában az Azure SQL felügyelt példányának feladatátvételi csoportja nem replikálja a SMK az elsődleges Azure SQL felügyelt példányból, így a DMK és a SSISDB nem lehet visszafejteni a másodlagos Azure SQL felügyelt példányát a feladatátvétel után. Ennek megkerüléséhez hozzáadhat egy jelszavas titkosítást a DMK, amelyet a másodlagos Azure SQL felügyelt példányon lehet visszafejteni. A SSMS használatával hajtsa végre az alábbi lépéseket.

   1. Futtassa a következő parancsot az elsődleges Azure SQL felügyelt példányának SSISDB, hogy jelszót adjon hozzá a DMK titkosításához.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Futtassa az alábbi parancsot a SSISDB az elsődleges és másodlagos Azure SQL felügyelt példányain, hogy hozzáadja az új jelszót a DMK visszafejtéséhez.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Ha a SSISDB feladatátvétel esetén közel nulla állásidőt szeretne használni, tartsa meg mindkét Azure-SSIS-t. Csak az elsődleges Azure-SSIS IR férhet hozzá az elsődleges SSISDB a csomagok beolvasásához és végrehajtásához, valamint a csomag-végrehajtási naplók írásához, míg a másodlagos Azure-SSIS IR csak a máshová telepített csomagok esetében lehet ugyanazokat, például Azure Files.

   Ha csökkenteni szeretné a futó költségeket, a létrehozás után leállíthatja a másodlagos Azure-SSIS IR. SSISDB feladatátvétel esetén az elsődleges és a másodlagos Azure-SSIS IRs a szerepköröket fogja felcserélni. Ha az elsődleges Azure-SSIS IR leáll, újra kell indítania. Attól függően, hogy a virtuális hálózatba van-e befecskendezve, és a felhasznált injekciós módszer, 5 percen belül, vagy körülbelül 20-30 percen belül eltarthat.

1. Ha az [Azure SQL felügyelt példányának ügynökét használja a előkészítési/ütemezési csomagok végrehajtásához](./how-to-invoke-ssis-package-managed-instance-agent.md), akkor győződjön meg arról, hogy az összes kapcsolódó SSIS-feladat és a hozzá tartozó ütemezések át lesznek másolva a másodlagos Azure SQL-példányra az eredetileg letiltott ütemezéssel. A SSMS használatával hajtsa végre az alábbi lépéseket.

   1. Minden egyes SSIS-feladatokhoz kattintson a jobb gombbal, és válassza ki a **parancsfájl-feladatot** a, a **Létrehozás** és az **új lekérdezési szerkesztő ablak** legördülő menüből, hogy létrehozza a parancsfájlt.

      ![SSIS-feladatok parancsfájljának létrehozása](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Az egyes generált SSIS-feladatok parancsfájljában keresse meg a tárolt eljárás végrehajtásához szükséges parancsot, `sp_add_job` és szükség szerint módosítsa/törölje az érték-hozzárendelést `@owner_login_name` argumentumként.

   1. Minden frissített SSIS-parancsfájl esetében futtassa azt a másodlagos Azure SQL felügyelt példányán, hogy a feladatot a saját feladatok lépéseivel és a hozzájuk tartozó ütemtervekkel másolja.

   1. A következő szkripttel hozzon létre egy új T-SQL-feladatot a SSIS-feladatütemezés engedélyezéséhez/letiltásához az elsődleges és másodlagos SSISDB szerepkör alapján, az elsődleges és másodlagos Azure SQL felügyelt példányain is, és futtassa rendszeresen. A SSISDB feladatátvétel esetén a letiltott SSIS-feladatütemezés engedélyezve lesz, és fordítva.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Ha az [ADF-t az előkészítési/ütemezési csomagok végrehajtásához használja](./how-to-invoke-ssis-package-ssis-activity.md), győződjön meg arról, hogy az összes kapcsolódó ADF-folyamat a végrehajtás SSIS csomag tevékenységeivel és a hozzájuk tartozó eseményindítók a másodlagos ADF-be van másolva az eredetileg letiltott eseményindítókkal. SSISDB feladatátvétel esetén engedélyeznie kell azokat.

1. [Tesztelheti az Azure SQL felügyelt példányának feladatátvételi csoportját](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) , és ELLENŐRIZHETI az [ADF-portál Azure-SSIS IR figyelés lapján](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , hogy az elsődleges és másodlagos Azure-SSIS IRs felcserélte-e a szerepköröket. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Új Azure-SSIS IR csatolása Azure SQL Database/felügyelt példány által üzemeltetett meglévő SSISDB

Ha egy katasztrófa következik be, és hatással van a meglévő Azure-SSIS IRra, de nem Azure SQL Database/felügyelt példány ugyanabban a régióban, akkor egy másik régióban lévő újat is lecserélheti. Ha Azure SQL Database/felügyelt példány által üzemeltetett meglévő SSISDB egy új Azure-SSIS IRhoz szeretné csatolni, hajtsa végre az alábbi lépéseket.

1. Ha a meglévő Azure-SSIS IR továbbra is fut, először le kell állítania Azure Portal/ADF felhasználói felület vagy Azure PowerShell használatával. Ha a katasztrófa is hatással van az ADF-re ugyanabban a régióban, kihagyhatja ezt a lépést.

1. A SSMS használatával futtassa a következő parancsot a Azure SQL Database/felügyelt példányban található SSISDB, hogy frissítse a metaadatokat, amelyek engedélyezik az új ADF/Azure-SSIS IR kapcsolatait.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. [Azure Portal/ADF felhasználói felület](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) vagy [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)használatával hozzon létre egy másik régióban a *YourNewADF* / *YourNewAzureSSISIR* nevű új ADF/Azure-SSIS IR. Ha Azure Portal/ADF felhasználói felületet használ, figyelmen kívül hagyhatja a kapcsolódási hiba tesztelése az **Integration Runtime telepítése** ablaktábla **központi telepítési beállítások** lapján.

## <a name="next-steps"></a>Következő lépések

A Azure-SSIS IR további konfigurációs beállításait is megtekintheti:

- [Csomagok tárolóinak konfigurálása a Azure-SSIS IRhoz](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Egyéni beállítások konfigurálása a Azure-SSIS IRhoz](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [A Azure-SSIS IR virtuális hálózati befecskendezésének konfigurálása](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Saját üzemeltetésű IR konfigurálása proxyként a Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
