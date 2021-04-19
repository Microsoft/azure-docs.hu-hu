---
title: Szkriptet Azure Migrate berendezés beállítása
description: Megtudhatja, hogyan állíthat be Azure Migrate berendezést szkript használatával
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714757"
---
# <a name="set-up-an-appliance-with-a-script"></a>Berendezés beállítása szkript használatával

Ebben a cikkben egy virtuális [berendezést Azure Migrate](./migrate-appliance-architecture.md) VMware- és Hyper-V-kiszolgálók értékeléséhez/migráláshoz. Egy szkript futtatásával létrehozhat egy berendezést, és ellenőrizheti, hogy tud-e csatlakozni az Azure-hoz. 

A berendezést üzembe helyezheti a VMware-ben és a Hyper-V-ben található kiszolgálókhoz egy szkript használatával, vagy a virtuális gépről letöltött Azure Portal. A szkriptek használata akkor hasznos, ha nem tud berendezést létrehozni a letöltött sablonnal.

- Sablont a [VMware-hez](./tutorial-discover-vmware.md) vagy a [Hyper-V-hez való oktatóanyagokban használhat.](./tutorial-discover-hyper-v.md)
- Fizikai kiszolgálókhoz csak szkriptet használhat, ha beállít egy berendezést. Kövesse [ezt a cikket.](how-to-set-up-appliance-physical.md)
- Ha egy berendezést egy felhőben Azure Government, kövesse [ezt a cikket.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Előfeltételek

A szkript beállítja a Azure Migrate berendezést egy meglévő kiszolgálón.

- A berendezésként szolgáló kiszolgálónak meg kell felelnie a következő hardver- és operációsrendszer-követelményeknek:

Eset | Követelmények
--- | ---
VMware | Windows Server 2016, 32 GB memóriával, nyolc vCPU-val, körülbelül 80 GB lemezterülettel
Hyper-V | Windows Server 2016, 16 GB memóriával, nyolc vCPU-val, körülbelül 80 GB lemezterülettel

- A kiszolgálónak szüksége van egy külső virtuális kapcsolóra is. Statikus vagy dinamikus IP-címet igényel. 
- A berendezés üzembe helyezése előtt tekintse át a Hyper-V virtuális gépeken található [VMware](migrate-appliance.md#appliance---vmware)kiszolgálók részletes [berendezéskövetelményeit.](migrate-appliance.md#appliance---hyper-v)
- Ne futtassa a szkriptet egy meglévő Azure Migrate berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A berendezés beállítása A VMware-hez

A berendezés VMware-hez való beállításhoz töltse le a AzureMigrateInstaller-Server-Public.zip nevű [](https://go.microsoft.com/fwlink/?linkid=2140334)tömörített fájlt a portálról vagy innen, és bontsa ki a tartalmát. A berendezés webalkalmazásának elindításához futtassa a PowerShell-szkriptet. Először beállítja és konfigurálja a berendezést. Ezután regisztrálja a berendezést a projektben.

### <a name="verify-file-security"></a>Fájlbiztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A kiszolgálón, amelyre letöltötte a fájlt, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Ellenőrizze a berendezés legújabb verzióját és szkriptet az Azure nyilvános felhőhöz:

    **Algoritmus** | **Letöltés** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőt teszi:

- Ügynököket és egy webalkalmazást telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölt és telepít egy újraírható IIS-modult. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM), és állandó beállításokat ad Azure Migrate.
- Napló- és konfigurációs fájlokat hoz létre a következőképpen:
    - **Konfigurációs fájlok:**%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok:**%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. Győződjön meg arról, hogy a szkriptet nem egy meglévő Azure Migrate futtatja.
2. Indítsa el a PowerShellt a kiszolgálón rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Miután a szkript sikeresen lefutott, elindítja a berendezés webalkalmazását, hogy be tudja állítani a berendezést. Ha bármilyen problémába ütközik, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés képes csatlakozni a nyilvános felhőHöz az Azure [URL-címekhez.](migrate-appliance.md#public-cloud-urls)

## <a name="set-up-the-appliance-for-hyper-v"></a>A berendezés beállítása Hyper-V-hez

A berendezés Hyper-V-hez való beállításhoz töltse le az AzureMigrateInstaller-Server-Public.zip nevű [](https://go.microsoft.com/fwlink/?linkid=2105112)tömörített fájlt a portálról vagy innen, és bontsa ki a tartalmát. A berendezés webalkalmazásának elindításához futtassa a PowerShell-szkriptet. Először be kell állítania és konfigurálnia kell a berendezést. Ezután regisztrálja a berendezést a projektben.


### <a name="verify-file-security"></a>Fájlbiztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A kiszolgálón, amelyre letöltötte a fájlt, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Ellenőrizze a berendezés legújabb verzióját és szkriptet az Azure nyilvános felhőhöz:

    **Forgatókönyv** | **Letöltés** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőt teszi:

- Ügynököket és egy webalkalmazást telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölt és telepít egy újraírható IIS-modult. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) az adatbázis állandó Azure Migrate.
- Napló- és konfigurációs fájlokat hoz létre a következőképpen:
    - **Konfigurációs fájlok:**%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok:**%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. Győződjön meg arról, hogy a szkriptet nem egy meglévő Azure Migrate futtatja.
2. Indítsa el a PowerShellt a kiszolgálón rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Miután a szkript sikeresen lefutott, elindítja a berendezés webalkalmazását, hogy be tudja állítani a berendezést. Ha bármilyen probléma merül fel, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés képes csatlakozni a nyilvános felhőhöz elérhető Azure [URL-címekhez.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Következő lépések

A berendezés üzembe helyezése után először konfigurálnia kell, majd regisztrálnia kell a projektben.

- A berendezés beállítása a [VMware-hez.](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)
- A berendezés beállítása a [Hyper-V-hez.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)