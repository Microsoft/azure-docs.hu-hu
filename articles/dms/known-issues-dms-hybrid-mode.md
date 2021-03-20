---
title: Ismert problémák/áttelepítési korlátozások hibrid mód használatával
description: Ismerkedjen meg az ismert problémákkal/áttelepítési korlátozásokkal Azure Database Migration Service hibrid módban való használatával.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291843"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Ismert problémák/áttelepítési korlátozások hibrid mód használatával

Az Azure Database Migration Service hibrid módban való használatával kapcsolatos ismert problémákat és korlátozásokat a következő szakaszokban ismertetjük.

## <a name="installer-fails-to-authenticate"></a>A telepítő nem tud hitelesíteni

A tanúsítványnak a AdApp való feltöltése után néhány perc késéssel elvégezhető a hitelesítés az Azure-ban. A telepítő megkísérli az újrapróbálkozást némi késéssel, de lehetséges, hogy a terjesztési késleltetés hosszabb az újrapróbálkozásnál, és egy **FailedToGetAccessTokenException** üzenetet fog látni. Ha a tanúsítvány a megfelelő AdApp lett feltöltve, és a dmsSettings.jsa megfelelő AppId adta meg, akkor próbálja meg újból futtatni a install parancsot.

## <a name="service-offline-after-successful-installation"></a>"Offline" szolgáltatás a sikeres telepítés után

Ha a szolgáltatás offline állapotba kerül, miután a telepítési folyamat sikeresen befejeződött, próbálkozzon az alábbi lépésekkel.

1. A Azure Portal Azure Database Migration Service-példányában navigáljon a **hibrid** beállítások lapra, majd ellenőrizze, hogy a feldolgozó regisztrálva van-e a regisztrált feldolgozók rácsának ellenőrzésével.

    Ennek a feldolgozónak **online** állapotban kell lennie, de probléma esetén **Offline** állapotba kerülhet.

2. A munkavégző számítógépen a következő PowerShell-parancs futtatásával vizsgálja meg a szolgáltatás állapotát:

    ```
    Get-Service Scenario*
    ```

    Ez a parancs a feldolgozót futtató Windows-szolgáltatás állapotát adja meg. Ebben az esetben csak egyetlen eredménynek kell szerepelnie. Ha a feldolgozó leáll, a következő PowerShell-paranccsal próbálja meg újraindítani:

    ```
    Start-Service Scenario*
    ```

    A szolgáltatást a Windows-szolgáltatások felhasználói felületén is megtekintheti.

3. Ha a Windows-szolgáltatás futása és leállítása között fut, akkor a feldolgozó problémákba ütközött. A probléma meghatározásához tekintse meg a Azure Database Migration Service hibrid munkavégző naplókat.

    - A telepítési folyamat naplófájljai a "naplók" mappában találhatók abban a mappában, amelyből a telepítő végrehajtható fájlját futtatták.
    - Azure Database Migration Service hibrid feldolgozói naplók a **WorkerLogs** mappában találhatók, abban a mappában, amelyben a Worker telepítve van. A hibrid munkavégző naplófájlok alapértelmezett helye a **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Saját aláírt tanúsítvány használata

A művelet GenerateCert által létrehozott tanúsítvány egy önaláírt tanúsítvány, amely nem fogadható el a belső biztonsági házirendek alapján. A tanúsítvány használata helyett megadhatja saját tanúsítványát, és megadhatja az ujjlenyomatot a dmsSettings.js. Ezt a tanúsítványt fel kell tölteni a AdApp, és telepítve kell lennie azon a számítógépen, amelyre a Azure Database Migration Service hibrid feldolgozót telepíti. Ezt követően telepítse a tanúsítványt a titkos kulccsal a helyi számítógép tanúsítványtárolóba.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>A Worker szolgáltatás futtatása alacsony jogosultsági szintű fiókként

Alapértelmezés szerint a Azure Database Migration Service Hybrid Worker szolgáltatás helyi rendszerfiókként fut. Módosíthatja a szolgáltatáshoz használt fiókot, ha a használt fiók rendelkezik hálózati engedélyekkel. A szolgáltatás "futtató" fiókjának módosításához használja az alábbi eljárást.

1. Állítsa le a szolgáltatást a Windows-szolgáltatások vagy a PowerShell Stop-Service parancsának használatával.

2. Frissítse a szolgáltatást egy másik bejelentkezési fiók használatára.

3. A helyi számítógép-tanúsítványok certmgr adjon titkos kulcsot az új fióknak a **DMS Hybrid app Key** és a DMS- **forgatókönyvhöz tartozó fő** kulcspár-tanúsítványok számára.

    a. Nyissa meg a certmgr a következő kulcsok megtekintéséhez:

    - DMS – hibrid alkalmazás kulcsa
    - DMS Hybrid Worker telepítési kulcsa
    - DMS-forgatókönyv – motor kulcspár

    b. Kattintson a jobb gombbal a **DMS Hybrid app Key** bejegyzésre, mutasson a **minden feladat** elemre, majd válassza a **titkos kulcsok kezelése** lehetőséget.

    c. A **Biztonság** lapon válassza a **Hozzáadás** lehetőséget, majd adja meg a fiók nevét.

    d. Ugyanazokkal a lépésekkel engedélyezheti a titkos kulcs engedélyét az új fiókhoz a **DMS-forgatókönyvhöz tartozó motor kulcspár** -tanúsítványához.

## <a name="unregistering-the-worker-manually"></a>A feldolgozó manuális regisztrációjának törlése

Ha már nincs hozzáférése a munkavégző számítógéphez, a következő lépések végrehajtásával törölheti a feldolgozót, és újból felhasználhatja Azure Database Migration Service példányát:

1. A Azure Portal a Azure Database Migration Service példányra, majd navigáljon a **hibrid** beállítások lapra.

   A feldolgozói bejegyzés a listában jelenik meg, az állapot pedig **kapcsolat nélküli üzemmódban** jelenik meg.

2. A feldolgozói bejegyzés listájának jobb szélén kattintson a három pontra, majd válassza a **Regisztráció törlése** lehetőséget.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Adott áttelepítési forgatókönyvek problémáinak kezelése

Az alábbi szakaszok a Azure Database Migration Service hibrid mód használatával történő online áttelepítés elvégzéséhez kapcsolódó forgatókönyv-specifikus problémákat írják le.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Online Migrálás az Azure SQL felügyelt példányára

**Magas processzorhasználat**

**Probléma**: az SQL felügyelt példányának online áttelepítése esetén a hibrid feldolgozót futtató számítógép magas CPU-használatot fog tapasztalni, ha túl sok biztonsági mentés van, vagy ha a biztonsági másolatok túl nagyok.

Megoldás **: a probléma enyhítése** érdekében használjon tömörített biztonsági mentéseket, Ossza szét az áttelepítést, hogy az több megosztást használjon, vagy a hibrid feldolgozót futtató számítógépet.
