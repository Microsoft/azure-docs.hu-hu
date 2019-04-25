---
title: Kibocsátási megjegyzések a Microsoft Azure Backup Server v3
description: Ez a cikk az ismert problémák és a lehetséges megoldások a MABS v3 kapcsolatos információkat tartalmazza.
services: backup
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: d37245d7eed39ee9d219578db9e0a50d758ba9a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499698"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Kibocsátási megjegyzések a Microsoft Azure Backup Server
Ez a cikk a Microsoft Azure Backup Server (MABS) V3 az ismert problémákról és megkerülő megoldásokról nyújt.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Biztonsági mentés és helyreállítás sikertelen lesz, a fürtözött számítási feladatokhoz

**Leírás:** Fürtözött adatforrás, például a Hyper-V-fürt vagy SQL-fürt (SQL Always On) vagy az Exchange adatbázis-elérhetőségi csoportban (DAG) található a biztonsági mentési és visszaállítási MABS V2 MABS V3 való frissítés után sikertelen lesz.

**Kerülő:** Ennek megelőzése érdekében nyissa meg az SQL Server Management Studio (SSMS)) és a DPM-adatbázis a következő SQL-parancsfájlt futtathat:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>A MABS v3-as frissítés sikertelen lesz, orosz területi beállításai szerint

**Leírás:** MABS V3 orosz területi beállítás a MABS V2 történő frissítés nem sikerül egy hibakódot **4387**.

**Kerülő:** Kövesse az alábbi lépéseket a MABS v3-as frissítés orosz használatával telepítse a csomagot:

1.  [Biztonsági mentés](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) az SQL-adatbázis, és távolítsa el a MABS V2 (válassza ki a védett adatok megőrzése az eltávolítás során).
2.  Frissítse az SQL 2017 (Enterprise), és távolítsa el a verziófrissítés részeként reporting.
3. [Telepítés](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Telepítés](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017#ssms-installation-tips-and-issues-ssms-1791) SQL Server Management Studio (SSMS).
5.  A paraméterekkel leírtak szerint jelentéskészítés konfigurálásához [SQL 2017 SSRS-konfiguráció](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Telepítés](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Visszaállítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL használatával az ssms-ben, és futtassa a DPM-Szinkronizáló eszköz leírtak szerint [Itt](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Frissítse a "Verziója" tulajdonság dbo.tbl_DLS_GlobalSetting tábla a következő paranccsal:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Indítsa el az MSDPM szolgáltatást.


## <a name="next-steps"></a>További lépések

[Újdonságok a MABS V3](backup-mabs-whats-new-mabs.md)
