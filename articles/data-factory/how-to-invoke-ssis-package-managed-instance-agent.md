---
title: SSIS-csomagok futtatása az Azure SQL felügyelt példány-ügynök használatával
description: Ismerje meg, hogyan futtathat SSIS-csomagokat az Azure SQL felügyelt példány-ügynök használatával.
ms.service: data-factory
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 916d799ba08f46cb86ee2e22c4af7fc1b92b385f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386152"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>SSIS-csomagok futtatása az Azure SQL felügyelt példány-ügynök használatával

Ez a cikk azt ismerteti, hogyan futtathat egy SQL Server Integration Services-(SSIS-) csomagot az Azure SQL felügyelt példány-ügynök használatával. Ez a funkció olyan viselkedést biztosít, amely hasonló ahhoz, hogy a SSIS-csomagokat a helyszíni környezetben SQL Server Agent használatával ütemezze.

Ezzel a szolgáltatással futtathatja a SSISDB-ben tárolt SSIS-csomagokat egy SQL felügyelt példányban, egy fájlrendszerben, például Azure Files vagy egy Azure-SSIS Integration Runtime Package Store áruházban.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához [töltse le](/sql/ssms/download-sql-server-management-studio-ssms) és telepítse a legújabb SQL Server Management Studiot (SSMS). A verzió támogatásának részletei az alábbiak szerint:

- A csomagok SSISDB vagy fájlrendszerben történő futtatásához telepítse a SSMS 18,5-es vagy újabb verzióját.
- Ha csomagokat szeretne futtatni a Package Store-ban, telepítse a SSMS 18,6-es vagy újabb verzióját.

[Egy Azure-SSIS integrációs](./tutorial-deploy-ssis-packages-azure.md) modult is létre kell hoznia Azure Data Factory-ben. Egy SQL felügyelt példányt használ végpont-kiszolgálóként.

## <a name="run-an-ssis-package-in-ssisdb"></a>SSIS-csomag futtatása a SSISDB-ben

Ebben az eljárásban az SQL felügyelt példány-ügynök használatával meghívja a SSISDB-ben tárolt SSIS-csomagot.

1. A SSMS legújabb verziójában kapcsolódjon egy felügyelt SQL-példányhoz.
1. Hozzon létre egy új Agent-feladatot és egy új feladatot. A **SQL Server Agent** alatt kattintson a jobb gombbal a **feladatok** mappára, majd válassza az **új feladat** elemet.

   ![Új ügynök feladatainak létrehozásához kiválasztott beállítások](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Az **új feladatütemezés** lapon válassza ki **SQL Server Integration Services csomagot** típusként.

   ![Új SSIS-feladatok létrehozásának lépései](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. A **csomag** lapon válassza a **SSIS katalógus** lehetőséget a csomag helyeként.
1. Mivel a SSISDB egy SQL-felügyelt példányban található, nem kell megadnia a hitelesítést.
1. SSIS-csomagot kell megadnia a SSISDB.

   ![Csomag lap a csomag forrásának kiválasztásával](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. A **konfiguráció** lapon a következőket teheti:
  
   - A paraméterek területen állítsa be a **paramétereket**.
   - Felülbírálja az értékeket a **kapcsolatkezelő** alatt.
   - Bírálja felül a tulajdonságot, és válassza a naplózási szintet a **speciális** területen.

   ![Konfiguráció lap a csomag forrás típusának kiválasztásával](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Kattintson az **OK** gombra az ügynök-feladatok konfigurációjának mentéséhez.
1. Indítsa el az ügynök feladatot a SSIS-csomag futtatásához.

## <a name="run-an-ssis-package-in-the-file-system"></a>SSIS-csomag futtatása a fájlrendszerben

Ebben az eljárásban az SQL felügyelt példány-ügynök használatával futtatja a fájlrendszerben tárolt SSIS-csomagot.

1. A SSMS legújabb verziójában kapcsolódjon egy felügyelt SQL-példányhoz.
1. Hozzon létre egy új Agent-feladatot és egy új feladatot. A **SQL Server Agent** alatt kattintson a jobb gombbal a **feladatok** mappára, majd válassza az **új feladat** elemet.

   ![Új ügynök feladatainak létrehozásához kiválasztott beállítások](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Az **új feladatütemezés** lapon válassza ki **SQL Server Integration Services csomagot** típusként.

   ![Új SSIS-feladatok létrehozásának lépései](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. A **csomag** lapon:

   1. A **csomag helye** területen válassza a **fájlrendszer** lehetőséget.

   1. A **forrásfájl típusa**:

      - Ha a csomag fel van töltve Azure Filesre, válassza az **Azure-fájlmegosztás** lehetőséget.

        ![A forrásfájl típusának beállításai](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        A csomag elérési útja: **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        Az Azure-fájl eléréséhez adja meg az Azure-fiók nevét és a fiók kulcsát a **fájl-hozzáférési hitelesítő adatok** megadása alatt. A tartomány az **Azure**-ban van beállítva.

      - Ha a csomag egy hálózati megosztásra van feltöltve, válassza a **hálózati megosztás** lehetőséget.

        A csomag elérési útja a csomagfájl UNC elérési útja a. dtsx kiterjesztéssel.

        A hálózati megosztási csomag fájljának eléréséhez adja meg a megfelelő tartományt, felhasználónevet és jelszót.
   1. Ha a csomagfájl jelszóval van titkosítva, válassza a **titkosítási jelszó** lehetőséget, és adja meg a jelszót.
1. Ha a SSIS-csomag futtatásához konfigurációs fájlra van szüksége, a **konfigurációk** lapon adja meg a konfigurációs fájl elérési útját.
   Ha Azure Files tárolja a konfigurációt, annak konfigurációs elérési útja a lesz **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. A **végrehajtási beállítások** lapon kiválaszthatja, hogy a SSIS-csomag futtatásához **Windows-hitelesítést** vagy **32-bites futtatókörnyezetet** szeretne használni.
1. A **naplózás** lapon kiválaszthatja a naplózási útvonalat és a naplózási hozzáférési hitelesítő adatokat a naplófájlok tárolásához. 
   Alapértelmezés szerint a naplózási útvonal megegyezik a csomag mappájának elérési útjával, és a naplózási hozzáférési hitelesítő adatok megegyeznek a csomag hozzáférési hitelesítő adataival.
   Ha Azure Files tárolja a naplókat, a naplózási útvonala lesz **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Az **értékek beállítása** lapon megadhatja a tulajdonság elérési útját és értékét a csomag tulajdonságainak felülbírálásához.

   A felhasználói változó értékének felülbírálásához például adja meg az elérési útját a következő formátumban: **`\Package.Variables[User::<variable name>].Value`** .
1. Kattintson az **OK** gombra az ügynök-feladatok konfigurációjának mentéséhez.
1. Indítsa el az ügynök feladatot a SSIS-csomag futtatásához.

## <a name="run-an-ssis-package-in-the-package-store"></a>SSIS-csomag futtatása a Package Store-ban

Ebben az eljárásban az SQL felügyelt példány-ügynök használatával futtat egy SSIS-csomagot, amelyet a Azure-SSIS IR csomagkezelő tárol.

1. A SSMS legújabb verziójában kapcsolódjon egy felügyelt SQL-példányhoz.
1. Hozzon létre egy új Agent-feladatot és egy új feladatot. A **SQL Server Agent** alatt kattintson a jobb gombbal a **feladatok** mappára, majd válassza az **új feladat** elemet.

   ![Új ügynök feladatainak létrehozásához kiválasztott beállítások](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Az **új feladatütemezés** lapon válassza ki **SQL Server Integration Services csomagot** típusként.

   ![Új SSIS-feladatok létrehozásának lépései](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. A **csomag** lapon:

   1. A **csomag helye** területen válassza a **csomag tároló** elemet.

   1. **Csomag elérési útja**:

      A csomag elérési útja: **`<package store name>\<folder name>\<package name>`** .

      ![Csomagok tárolási típusának beállításai](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Ha a csomagfájl jelszóval van titkosítva, válassza a **titkosítási jelszó** lehetőséget, és adja meg a jelszót.
1. Ha a SSIS-csomag futtatásához konfigurációs fájlra van szüksége, a **konfigurációk** lapon adja meg a konfigurációs fájl elérési útját.
   Ha Azure Files tárolja a konfigurációt, annak konfigurációs elérési útja a lesz **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. A **végrehajtási beállítások** lapon kiválaszthatja, hogy a SSIS-csomag futtatásához **Windows-hitelesítést** vagy **32-bites futtatókörnyezetet** szeretne használni.
1. A **naplózás** lapon kiválaszthatja a naplózási útvonalat és a naplózási hozzáférési hitelesítő adatokat a naplófájlok tárolásához.
   Alapértelmezés szerint a naplózási útvonal megegyezik a csomag mappájának elérési útjával, és a naplózási hozzáférési hitelesítő adatok megegyeznek a csomag hozzáférési hitelesítő adataival.
   Ha Azure Files tárolja a naplókat, a naplózási útvonala lesz **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Az **értékek beállítása** lapon megadhatja a tulajdonság elérési útját és értékét a csomag tulajdonságainak felülbírálásához.

   A felhasználói változó értékének felülbírálásához például adja meg az elérési útját a következő formátumban: **`\Package.Variables[User::<variable name>].Value`** .
1. Kattintson az **OK** gombra az ügynök-feladatok konfigurációjának mentéséhez.
1. Indítsa el az ügynök feladatot a SSIS-csomag futtatásához.

## <a name="cancel-ssis-package-execution"></a>SSIS-csomag végrehajtásának megszakítása

Ha vissza szeretné vonni a csomagok végrehajtását egy SQL felügyelt példány-ügynök feladataiból, hajtsa végre az alábbi lépéseket az ügynök feladat közvetlen leállítása helyett:

1. Az SQL Agent **jobId** megkeresése **msdb.dbo.sysfeladatokból**.
1. Keresse meg a megfelelő SSIS- **executionId** a feladatsor alapján a következő lekérdezés használatával:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Ha a SSIS-csomagok SSISDB vannak, akkor a feladatok végrehajtásához használja a **ssisdb.internal.execution_parameter_values** . Ha a SSIS-csomagok a fájlrendszerben vannak, akkor használja a **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Kattintson a jobb gombbal a SSISDB-katalógusra, majd válassza az **aktív műveletek** elemet.

   !["Aktív műveletek" a SSISDB-katalógus helyi menüjében](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Állítsa le a megfelelő műveletet a **executionId** alapján.

## <a name="next-steps"></a>Következő lépések
Azure Data Factory használatával is ütemezhet SSIS-csomagokat. Részletes útmutatásért lásd: [Azure Data Factory eseményvezérelt eseményindító](how-to-create-event-trigger.md).