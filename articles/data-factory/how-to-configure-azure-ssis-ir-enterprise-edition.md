---
title: Enterprise Edition kiépítése a Azure-SSIS Integration Runtime számára
description: Ez a cikk a Azure-SSIS Integration Runtime Enterprise Edition funkcióit és a kiépítésének módját ismerteti
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: be49d56d301c23791777f4d89e8c6c6fd35d7035
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636306"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Enterprise Edition kiépítése a Azure-SSIS Integration Runtime számára

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure-SSIS Integration Runtime Enterprise kiadása a következő speciális és prémium funkciók használatát teszi lehetővé:
-   Az adatváltozások rögzítése (CDC) összetevői
-   Oracle-, Teradata-és SAP BW-összekötők
-   SQL Server Analysis Services (SSAS) és Azure Analysis Services (AAS) összekötők és átalakítások
-   Fuzzy csoportosítás és fuzzy keresési átalakítások
-   A kinyerési és kifejezési keresési átalakítások kifejezése

Ezeknek a szolgáltatásoknak a használatához további összetevőket kell telepíteni a Azure-SSIS IR testreszabásához. További információ a további összetevők telepítéséről: [Az Azure SSIS Integration Runtime egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Vállalati funkciók

| **Vállalati funkciók** | **Leírások** |
|---|---|
| CDC-összetevők | A CDC forrása, a vezérlési feladat és a Splitter átalakító előre telepítve van a Azure-SSIS IR Enterprise kiadásban. Az Oracle-hez való kapcsolódáshoz is telepítenie kell a CDC designert és a szolgáltatást egy másik számítógépre. |
| Oracle-összekötők | Az Oracle-Csatlakozáskezelő, a forrás és a cél a Azure-SSIS IR Enterprise kiadásban van előtelepítve. Telepítenie kell az Oracle Call Interface (OCI) illesztőprogramot is, és szükség esetén konfigurálnia kell az Oracle Transport Network szubsztrátot (TNS) a Azure-SSIS IR. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-összekötők | Telepítenie kell a Teradata Csatlakozáskezelőt, a forrást és a célhelyet, valamint a Teradata párhuzamos Transporter (TPT) API-t és az Teradata ODBC-illesztőt a Azure-SSIS IR Enterprise kiadásban. További információ: [Az Azure SSIS integrációs modul egyéni beállításai](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW összekötők | A SAP BW Csatlakozáskezelő, a forrás és a cél a Azure-SSIS IR Enterprise kiadásban van előtelepítve. A SAP BW illesztőprogramot is telepítenie kell a Azure-SSIS IR. Ezek az összekötők támogatják a SAP BW 7,0-es vagy korábbi verzióit. A SAP BW vagy más SAP-termékek újabb verzióihoz való csatlakozáshoz vásárolhat és telepíthet SAP-összekötőket külső szoftvergyártók által a Azure-SSIS IR. További információ a további összetevők telepítéséről: [Az Azure SSIS Integration Runtime egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Összetevők Analysis Services               | Az adatbányászati modell betanítási célját, a dimenzió feldolgozásának célját, valamint a partíció feldolgozási célját, valamint az adatbányászati lekérdezés átalakítását a Azure-SSIS IR Enterprise kiadásban előtelepítik. Ezek az összetevők támogatják a SQL Server Analysis Services (SSAS), de csak a partíció-feldolgozási cél támogatja a Azure Analysis Services (AAS). A SSAS-hez való kapcsolódáshoz a [Windows-hitelesítés hitelesítő adatait is konfigurálnia kell a SSISDB-ben](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Ezen összetevők mellett a Analysis Services DDL végrehajtása feladat, a Analysis Services feldolgozási feladat és az adatbányászati lekérdezési feladat is előre telepítve van a Azure-SSIS IR standard/Enterprise kiadásban. |
| Fuzzy csoportosítás és fuzzy keresési átalakítások  | A rendszer a Azure-SSIS IR Enterprise kiadásban előre telepíti a fuzzy csoportosítási és a homályos keresési átalakításokat. Ezek az összetevők a SQL Server és a Azure SQL Database is támogatják a hivatkozási adattároláshoz. |
| A kinyerési és kifejezési keresési átalakítások kifejezése | A kinyerési és kifejezési keresési átalakítások a Azure-SSIS IR Enterprise kiadásban előre telepítve vannak. Ezek az összetevők a SQL Server és a Azure SQL Database is támogatják a hivatkozási adattároláshoz. |

## <a name="instructions"></a>Utasítások

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  [Azure PowerShell](/powershell/azure/install-az-ps)letöltése és telepítése.

2.  A Azure-SSIS IR PowerShell-lel való kiépítésekor vagy újrakonfigurálásakor a `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR elindítása előtt futtassa a with **Enterprise** értéket a **kiadás** paraméter értékeként. Íme egy példa a parancsfájlra:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Következő lépések

-   [Az Azure-SSIS Integration Runtime egyéni beállítása](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Fizetett vagy licencelt egyéni összetevők fejlesztése az Azure-SSIS Integration Runtime számára](how-to-develop-azure-ssis-ir-licensed-components.md)