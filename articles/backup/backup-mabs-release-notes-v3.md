---
title: A Microsoft Azure Backup Server v3 kibocsátási megjegyzései
description: Ez a cikk a Microsoft Azure Backup Server (MABS) v3 ismert problémáit és megkerülő megoldásait ismerteti.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254261"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup-kiszolgáló kibocsátási megjegyzései

Ez a cikk az Microsoft Azure Backup Server (MABS) v3 ismert problémáit és megkerülő megoldásait ismerteti.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Fürtözött munkaterhelések esetén a biztonsági mentés és a helyreállítás meghiúsul

**Leírás:** A Backup/Restore művelet sikertelen a fürtözött adatforrások, például a Hyper-V-fürt vagy az SQL-fürt (SQL always on) vagy az adatbázis rendelkezésre állási csoportjában (DAG) az MABS v2 MABS v3 verzióra való frissítése után.

**Megkerülő megoldás:** Ennek elkerüléséhez nyissa meg SQL Server Management Studio (SSMS)), és futtassa a következő SQL-parancsfájlt a DPM-ADATBÁZISon:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Az MABS v3-re való frissítés meghiúsul az orosz területi beállításban

**Leírás:** A MABS v2 verzióról a MABS v3-re való frissítés az orosz területi beállításban meghiúsul, hibakód: **4387**.

**Megkerülő megoldás:** Az alábbi lépéseket követve frissítsen az MABS v3-re az orosz telepítési csomag használatával:

1. [Készítsen biztonsági másolatot](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) az SQL-adatbázisról, és távolítsa el a MABS v2-et (válassza a védett adat megőrzését az eltávolítás során).
2. Frissítsen az SQL 2017 (Enterprise) verzióra, és távolítsa el a jelentéskészítést a frissítés részeként.
3. [Telepítés](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Telepítés](/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. Konfigurálja a jelentéskészítést a paraméterekkel az [SSRS-konfigurációban az SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs)használatával dokumentált módon.
6. [Telepítés](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Visszaállítás](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL SSMS használatával és DPM-Sync eszköz futtatása az [itt](/system-center/dpm/back-up-the-dpm-server#using-dpmsync)leírtak szerint.
8. Frissítse a "DataBaseVersion" tulajdonságot dbo.tbl_DLS_GlobalSetting táblában a következő parancs használatával:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Indítsa el a MSDPM szolgáltatást.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>A UR1 telepítése után a MABS-jelentések nem frissülnek az új RDL-fájlokkal

**Leírás**: a UR1 a MABS-jelentés formázásával kapcsolatos problémát a frissített RDL-fájlok rögzítik. Az új RDL-fájlok nem lettek automatikusan lecserélve a meglévő fájlokra.

**Áthidaló megoldás**: az RDL-fájlok cseréjéhez kövesse az alábbi lépéseket:

1. A MABS gépen nyissa meg SQL Reporting Services webes portál URL-címét.
1. A webportál URL-címében a DPMReports mappa a következő formátumban jelenik meg: **`DPMReports_<GUID>`**

    >[!NOTE]
    >Az elnevezési konvenciónak mindig csak egy mappája van. Ha a MABS egy korábbi verzióról frissít, lehet, hogy egy másik régebbi mappa is létezik, de nem fogja tudni megnyitni.

    ![DPMReports mappa](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Válassza ki és nyissa meg a **`DPMReports_<GUID>`** mappát. Az egyes jelentési fájlok az alább látható módon lesznek felsorolva.

    ![Az egyes jelentési fájlok listája](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Jelölje ki **a jelentéssel** nem rendelkező jelentések fájljait, kattintson a jobb gombbal a **lehetőségre** , és válassza a **kezelés** lehetőséget.

    ![A jelentési fájlok kezelésének kiválasztása](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Az új lapon válassza a **replace (csere** ) lehetőséget, hogy a fájlokat a legújabb jelentési fájlokkal cserélje le.

    A legfrissebb jelentési fájlok az elérési útban találhatók `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Például: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Cserélje le a fájlokat a legújabb jelentési fájlokra](./media/backup-mabs-release-notes-v3/replace-files.png)

    A fájlok cseréje után ügyeljen arra, hogy a **név** és a **Leírás** sértetlen legyen, és ne legyen üres.

1. A fájlok cseréje után indítsa újra a MABS-szolgáltatásokat, és használja a jelentési fájlokat.

## <a name="next-steps"></a>Következő lépések

[A MABS újdonságai](backup-mabs-whats-new-mabs.md)
