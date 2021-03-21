---
title: Helyszíni SQL Server Integration Services (SSIS) számítási feladatok migrálása Azure Data Factory (ADF) SSIS
description: Telepítse át a helyszíni SSIS számítási feladatokat az ADF-SSIS.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373606"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>Helyszíni SSIS számítási feladatok migrálása az ADF-SSIS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

Amikor áttelepíti az adatbázis-munkaterheléseket a helyszíni SQL Serverról az Azure Database Servicesre, azaz Azure SQL Database vagy az Azure SQL felügyelt példányára, akkor a SQL Server Integration Services (SSIS) az elsődleges értéknövelt szolgáltatások egyikét is át kell telepítenie.

A Azure Data Factory (ADF) Azure-SSIS Integration Runtime (IR) támogatja a SSIS-csomagok futtatását. A Azure-SSIS IR kiépítése után a csomagok üzembe helyezéséhez és futtatásához használhatja a jól ismert eszközöket, például a SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) és a parancssori segédeszközöket, például a dtinstall/dtutil/dtexec. További információ: az [Azure SSIS lift-and-SHIFT áttekintése](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

Ebből a cikkből megtudhatja, hogy a helyszíni SSIS származó ETL számítási feladatok áttelepítési folyamata az ADF-ben SSIS. Az áttelepítési folyamat két fázisból áll: az **értékelésből** és az **áttelepítésből**.

## <a name="assessment"></a>Értékelés

A teljes áttelepítési terv létrehozásához alapos értékelés segít azonosítani a forrásként szolgáló SSIS-csomagokkal kapcsolatos problémákat, amelyek megakadályozhatják a sikeres áttelepítést.

A Data Migration Assistant (DMA) egy ingyenesen letölthető eszköz erre a célra, amelyet helyileg lehet telepíteni és végrehajtani. Az **integrációs szolgáltatások** típusú DMA-értékelési projekt hozható létre a kötegekben lévő SSIS-csomagok értékeléséhez, valamint a következő kategóriákban bemutatott kompatibilitási problémák azonosításához:

- Áttelepítési blokkolók: kompatibilitási problémák, amelyek letiltják az áttelepítési forrás csomagjainak futtatását Azure-SSIS IRon. A DMA útmutatást nyújt a problémák megoldásához.

- Informatív problémák: a forrás-csomagokban használt részben támogatott vagy elavult funkciók. A DMA a javaslatok átfogó készletét, az Azure-ban elérhető alternatív megközelítéseket és a megoldási lépések enyhítését teszi lehetővé.

### <a name="four-storage-types-for-ssis-packages"></a>Négy tárolási típus a SSIS-csomagokhoz

- SSIS-katalógus (SSISDB). A SQL Server 2012-ben jelent meg, és tárolt eljárásokat, nézeteket és táblázat értékű függvényeket tartalmaz, amelyek a SSIS-projektek/-csomagok használatával működnek.
- Fájlrendszer.
- SQL Server rendszeradatbázis (MSDB).
- SSIS-csomag tárolója. Két altípusra épülő csomagkezelő réteg:
  - A MSDB, amely a SSIS-csomagok tárolására használt SQL Server rendszeradatbázisa.
  - Felügyelt fájlrendszer, amely a SSIS-csomagok tárolására szolgáló SQL Server telepítési útvonal adott mappája.

A DMA jelenleg támogatja a **fájlrendszerben**, a **Package Store**-ban és a **SSIS-katalógusban** tárolt csomagok kötegelt értékelését a **DMA v 5.0-s verziójának** futtatása óta.

Szerezze be a [DMA](/sql/dma/dma-overview)-t, és [végezze el a csomag értékelését](/sql/dma/dma-assess-ssis).

## <a name="migration"></a>Áttelepítés

[A forrás](#four-storage-types-for-ssis-packages) SSIS csomagjainak és az adatbázis-munkaterhelések áttelepítési célhelyének függvényében a **SSIS-csomagok** áttelepítésének lépései, valamint a SSIS-csomagok végrehajtásának elvégzésére szolgáló **SQL Server Agent feladatok** eltérőek lehetnek. Két forgatókönyv létezik:

- [Az **Azure SQL felügyelt példánya** adatbázis-munkaterhelési célként](#azure-sql-managed-instance-as-database-workload-destination)
- [**Azure SQL Database** adatbázis-munkaterhelés célhelyként](#azure-sql-database-as-database-workload-destination)

Emellett praktikus módszer a [SSIS DevOps-eszközök](/sql/integration-services/devops/ssis-devops-overview)használatára is, így a Batch-csomagok újratelepítése az áttelepítési célhelyre.  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>Az **Azure SQL felügyelt példánya** adatbázis-munkaterhelési célként

| **Csomag tárolási típusa** |SSIS-csomagok áttelepítésének módja|SSIS-feladatok kötegelt áttelepítésének módja|
|-|-|-|
|SSISDB|[**SSISDB** migrálása](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS-feladatok migrálása az Azure SQL felügyelt példány-ügynökére](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Fájlrendszer|Az dtinstall/dtutil/manuális másolással, vagy a fájlrendszerben a VNet/saját üzemeltetésű integrációs modulon keresztüli eléréshez való hozzáférés érdekében telepítse újra azokat a fájlmegosztás/Azure Files számára. További információ: [dtutil segédprogram](/sql/integration-services/dtutil-utility).|<li>[SSIS-feladatok migrálása az Azure SQL felügyelt példány-ügynökére](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Migrálás a [SSIS-feladatok áttelepítése varázslóval a SSMS-ben](how-to-migrate-ssis-job-ssms.md) <li>Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket a fájlrendszerekbe/fájlmegosztásba/Azure Filesba a SSMS/dtutil használatával. További információ: SSIS- [csomagok exportálása](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Package Store|Exportálja őket a Package Store-ba a SSMS/dtutil-n keresztül, vagy telepítse újra a csomagokat a dtinstall/dtutil/manuális másolás használatával. További információ: [csomagok kezelése Azure-SSIS Integration Runtime Package Store](azure-ssis-integration-runtime-package-store.md)-ban.|<li>[SSIS-feladatok migrálása az Azure SQL felügyelt példány-ügynökére](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

### <a name="azure-sql-database-as-database-workload-destination"></a>**Azure SQL Database** adatbázis-munkaterhelés célhelyként

| **Csomag tárolási típusa** |SSIS-csomagok áttelepítésének módja|Feladatok áttelepítésének módja|
|-|-|-|
|SSISDB|Újból üzembe helyezhető az Azure-SSISDB a SSDT/SSMS használatával. További információ: [SSIS-csomagok üzembe helyezése az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Fájlrendszer|Az dtinstall/dtutil/manuális másolással, vagy a fájlrendszerben a VNet/saját üzemeltetésű integrációs modulon keresztüli eléréshez való hozzáférés érdekében telepítse újra azokat a fájlmegosztás/Azure Files számára. További információ: [dtutil segédprogram](/sql/integration-services/dtutil-utility).|<li> Migrálás a [SSIS-feladatok áttelepítése varázslóval a SSMS-ben](how-to-migrate-ssis-job-ssms.md) <li> Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|SQL Server (MSDB)|Exportálja őket a fájlrendszerekbe/fájlmegosztásba/Azure Filesba a SSMS/dtutil használatával. További információ: SSIS- [csomagok exportálása](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|
|Package Store|Exportálja őket a fájlrendszerek/fájlmegosztás/Azure Files SSMS/dtutil keresztül, vagy telepítse azokat a fájlmegosztás/Azure Files a dtinstall/dtutil/manuális másolással, vagy tartsa azokat a fájlrendszerekben a VNet/saját üzemeltetésű integrációs modulon keresztül való hozzáféréshez. További információ: dtutil segédprogram. További információ: [dtutil segédprogram](/sql/integration-services/dtutil-utility).|Konvertálja őket ADF-folyamatokba/tevékenységekbe/eseményindítókkal parancsfájlok/SSMS/ADF-portálon keresztül. További információ: SSMS- [ütemezési funkció](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms).|

## <a name="additional-resources"></a>További források

- [Azure Data Factory](./introduction.md)
- [Database Migration Assistant](/sql/dma/dma-overview)
- [A SSIS számítási feladatainak átemelése és áthelyezése a felhőbe](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps-eszközök](/sql/integration-services/devops/ssis-devops-overview)
- [SSIS-csomagok migrálása a felügyelt Azure SQL-példányra](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [Csomagok újbóli üzembe helyezése Azure SQL Database](../dms/how-to-migrate-ssis-packages.md)

- [Helyszíni adatokhoz való hozzáférés Azure-SSIS Integration Runtime](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Azure-SSIS Integration Runtime beállításainak testreszabása](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Adattárak és fájlmegosztások elérése Windows-hitelesítéssel, Azure-beli SSIS-csomagokból](ssis-azure-connect-with-windows-auth.md)
- [Felügyelt identitások hitelesítésének használata](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Az Azure Key Vault használata](store-credentials-in-key-vault.md)
- [A Azure-SSIS Integration Runtime konfigurálása nagy teljesítményhez](configure-azure-ssis-integration-runtime-performance.md)
- [Azure-SSIS integrációs modul indítása és leállítása ütemezés szerint](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Következő lépések

- [Az Azure-ba telepített SSIS-csomagok ellenőrzése](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Az Azure-ban üzembe helyezett SSIS-csomagok futtatása](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Figyelő Azure-SSIS Integration Runtime](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [SSIS-csomagok végrehajtásának ütemezett végrehajtása az Azure-ban](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)