---
title: 'Gyors útmutató: hibrid üzemmódú példány létrehozása Azure Portal'
titleSuffix: Azure Database Migration Service
description: A Azure Portal használatával hibrid módban hozhat létre Azure Database Migration Service-példányt.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: 39f893e9375970ff4bb6e3cfa2c93ceea48ec896
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92331804"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Gyors útmutató: hibrid üzemmódú példány létrehozása Azure Portal & Azure Database Migration Service

Azure Database Migration Service a hibrid üzemmód az adatbázisok áttelepítését a helyszínen üzemeltetett áttelepítési feldolgozóval, a felhőben futó Azure Database Migration Service egy példányával kezeli. A hibrid mód különösen olyan esetekben hasznos, amikor a helyszíni hálózat és az Azure közötti helyek közötti kapcsolat hiánya, illetve ha a helyek közötti kapcsolat sávszélessége korlátozott.

>[!NOTE]
>Jelenleg a hibrid módban futó Azure Database Migration Service támogatja SQL Server áttelepítését a következőre:
>
>- Az Azure SQL felügyelt példánya közel nulla állásidővel (online).
>- Azure SQL Database egy adatbázist bizonyos állásidővel (offline).
>- MongoDb az Azure CosmosDB a közel nulla állásidővel (online).
>- MongoDb az Azure CosmosDB bizonyos állásidővel (offline).

Ebben a rövid útmutatóban a Azure Portal használatával hozza létre a Azure Database Migration Service egy példányát hibrid módban. Ezt követően töltse le, telepítse és állítsa be a hibrid feldolgozót a helyszíni hálózatán. Az előzetes verzió használata során Azure Database Migration Service hibrid mód használatával áttelepítheti az adatok áttelepítését a SQL Server helyszíni példányáról Azure SQL Databasere.

> [!NOTE]
> A Azure Database Migration Service Hybrid Installer a Microsoft Windows Server 2012 R2, a Server 2016, a Windows Server 2019 és a Windows 10 rendszeren fut.

> [!IMPORTANT]
> A Azure Database Migration Service hibrid telepítőhöz .NET 4.7.2 vagy újabb verzió szükséges. A .NET legújabb verzióinak megkereséséhez tekintse meg a [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework) lapot.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőjét, keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/), majd adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

A Azure Database Migration Service első példányának létrehozása előtt regisztrálja a Microsoft. DataMigration erőforrás-szolgáltatót.

1. A Azure Portal válassza az **előfizetések** elemet, válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltató keresése](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>A szolgáltatás egy példányának létrehozása

1. Válassza az +**erőforrás létrehozása** lehetőséget Azure Database Migration Service-példány létrehozásához.

2. Keresse meg a piactéren az áttelepítés lehetőséget, válassza a **Azure Database Migration Service** lehetőséget, majd a **Azure Database Migration Service** képernyőn válassza a **Létrehozás** elemet.

3. A **Migrálási szolgáltatás létrehozása** képernyőn:

    - Válasszon egy emlékezetes és egyedi **szolgáltatásnevet** a Azure Database Migration Service példányának azonosításához.
    - Válassza ki azt az **Azure-előfizetést**, amelyben a példányt létre szeretné hozni.
    - Válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre egy újat.
    - Válassza ki a forráshoz vagy a célkiszolgálóhoz legközelebb eső **Helyet**.
    - **Szolgáltatási mód** esetén válassza a **hibrid (előzetes verzió)** lehetőséget.

         ![Áttelepítési szolgáltatás létrehozása – alapismeretek](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Válassza az **Áttekintés + létrehozás** lehetőséget.

5. A **felülvizsgálat + létrehozás** lapon tekintse át a feltételeket, ellenőrizze a többi megadott információt, majd válassza a **Létrehozás** lehetőséget.

    ![Áttelepítési szolgáltatás létrehozása – felülvizsgálat + létrehozás](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Néhány pillanat múlva létrejön a Azure Database Migration Service hibrid módban, és készen áll a beállításra. A Azure Database Migration Service példány az alábbi képen látható módon jelenik meg:

    ![Azure Database Migration Service hibrid üzemmód példánya](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. A szolgáltatás létrehozása után válassza a **Tulajdonságok** elemet, majd másolja ki az **erőforrás-azonosító** mezőben megjelenő értéket, amelyet az Azure Database Migration Service Hybrid Worker telepítéséhez fog használni.

    ![Azure Database Migration Service hibrid üzemmód tulajdonságai](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Azure-alkalmazás regisztrációs AZONOSÍTÓjának létrehozása

Létre kell hoznia egy Azure-alkalmazás regisztrációs AZONOSÍTÓját, amelyet a helyszíni hibrid feldolgozó használhat a felhőben lévő Azure Database Migration Service való kommunikációhoz.

1. A Azure Portal válassza a **Azure Active Directory** lehetőséget, válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
2. Adja meg az alkalmazás nevét, majd a **támogatott fióktípus** területen válassza ki a támogatni kívánt fiókok típusát annak megadásához, hogy ki használhatja ki az alkalmazást.

    ![Azure Database Migration Service Hybrid Mode – alkalmazás regisztrálása](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Használja az **átirányítási URI (nem kötelező)** mezők alapértelmezett értékeit, majd válassza a **regisztráció** lehetőséget.

4. Az alkalmazás-azonosító regisztrációjának befejeződése után jegyezze fel az **alkalmazás (ügyfél) azonosítóját**, amelyet a hibrid feldolgozó telepítésekor fog használni.

5. A Azure Portalban navigáljon a Azure Database Migration Service elemre, válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **szerepkör-hozzárendelés hozzáadása** lehetőséget a közreműködői hozzáférés az alkalmazás-azonosítóhoz való hozzárendeléséhez.

    ![Azure Database Migration Service hibrid mód hozzárendelt közreműködői szerepkör](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Válassza ki a **közreműködő** szerepkört, rendeljen hozzá hozzáférést az **Azure ad-felhasználóhoz vagy a szolgáltatáshoz**, majd válassza ki az alkalmazás azonosítójának nevét.

    ![A hibrid mód Azure Database Migration Service közreműködői szerepkör részleteinek megadása](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Válassza a **Mentés** lehetőséget a Azure Database Migration Service erőforrásban található alkalmazás-azonosító szerepkör-hozzárendelésének mentéséhez.

## <a name="download-and-install-the-hybrid-worker"></a>A hibrid feldolgozó letöltése és telepítése

1. A Azure Portal navigáljon a Azure Database Migration Service-példányához.

2. A **Beállítások** területen válassza a **hibrid** lehetőséget, majd a hibrid feldolgozó letöltéséhez válassza a **telepítő letöltése** lehetőséget.

    ![Azure Database Migration Service Hybrid Worker letöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Bontsa ki a ZIP-fájlt azon a kiszolgálón, amely a Azure Database Migration Service Hybrid Worker szolgáltatást fogja üzemeltetni.

    > [!IMPORTANT]
    > A Azure Database Migration Service hibrid telepítőhöz .NET 4.7.2 vagy újabb verzió szükséges. A .NET legújabb verzióinak megkereséséhez tekintse meg a [.NET-keretrendszer letöltése](https://dotnet.microsoft.com/download/dotnet-framework) lapot.

4. A telepítési mappában keresse meg és nyissa meg a **dmsSettings.js** fájlt, adja meg a **ApplicationId** és a **resourceId**, majd mentse a fájlt.

    ![Hibrid feldolgozói beállítások Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. A következő parancs használatával létrehozhat egy olyan tanúsítványt, amelyet a Azure Database Migration Service használhat a hibrid feldolgozóval folytatott kommunikáció hitelesítéséhez.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    A rendszer létrehoz egy tanúsítványt a telepítési mappában.

    ![Hibrid feldolgozói tanúsítvány Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. A Azure Portalban navigáljon az alkalmazás-AZONOSÍTÓhoz a **kezelés** területen válassza a **tanúsítványok & Secrets** elemet, majd válassza a **tanúsítvány feltöltése** lehetőséget a létrehozott nyilvános tanúsítvány kiválasztásához.

    ![Azure Database Migration Service hibrid feldolgozói tanúsítvány feltöltése](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. A következő parancs futtatásával telepítse a Azure Database Migration Service hibrid feldolgozót a helyszíni kiszolgálóra:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Az install parancs futtatásakor a következő paramétereket is használhatja:
    >
    > - **-TelemetryOptOut** – leállítja a feldolgozó számára a telemetria küldését, de az továbbra is kis mértékben naplózza a helyileg.  A telepítő továbbra is telemetria küld.
    > - **-p {INSTALLLOCATION}**. Lehetővé teszi a telepítési útvonal módosítását, amely alapértelmezés szerint "C:\Program Files\DatabaseMigrationServiceHybrid".

8. Ha a telepítő hiba nélkül fut, akkor a szolgáltatás a Azure Database Migration Service online állapotot jeleníti meg, és készen áll az adatbázisok áttelepítésére.

    ![Online Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Azure Database Migration Service hibrid üzemmód eltávolítása

Jelenleg a Azure Database Migration Service hibrid üzemmód eltávolítása csak a helyszíni kiszolgálón lévő Azure Database Migration Service Hybrid Worker-telepítőn keresztül támogatott a következő parancs használatával:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Az eltávolítási parancs futtatásakor használhatja a "-ReuseCert" paramétert is, amely megtartja a generateCert-munkafolyamat által generált AdApp-tanúsítványt.  Ez lehetővé teszi, hogy ugyanazt a tanúsítványt használja, amelyet korábban hozott létre és töltött fel.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>A Azure Database Migration Service Hybrid Worker beállítása a PowerShell használatával

A Azure Database Migration Service Hybrid Worker Azure Portal használatával történő telepítése mellett egy [PowerShell-szkriptet](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) is biztosítunk, amellyel automatizálható a feldolgozó telepítési lépései, miután létrehozta a Azure Database Migration Service új példányát hibrid módban. A szkript a következőket hajtja végre:

1. Új AdApp hoz létre.
2. Letölti a telepítőt.
3. Futtatja a generateCert munkafolyamatot.
4. Feltölti a tanúsítványt.
5. Hozzáadja a AdApp a Azure Database Migration Service-példányhoz közreműködőként.
6. Futtatja a telepítési munkafolyamatot.

Ez a szkript a gyors prototípusok készítésére szolgál, ha a felhasználó már rendelkezik a szükséges engedélyekkel a környezetben. Vegye figyelembe, hogy az éles környezetben a AdApp és a tanúsítvány eltérő követelményekkel rendelkezhet, így a szkript sikertelen lehet.

> [!IMPORTANT]
> Ez a szkript feltételezi, hogy a Azure Database Migration Service egy meglévő példánya hibrid módban van, és hogy a használt Azure-fiók rendelkezik a AdApps létrehozásához szükséges engedélyekkel a bérlőben, valamint az Azure-RBAC az előfizetésben való módosításához.

Adja meg a paramétereket a parancsfájl tetején, majd futtassa a parancsfájlt egy rendszergazdai PowerShell-példányból.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [SQL Server migrálása egy online Azure SQL felügyelt példányra](tutorial-sql-server-managed-instance-online.md) 
>  [SQL Server migrálása a Azure SQL Database offline állapotba](tutorial-sql-server-to-azure-sql.md)
