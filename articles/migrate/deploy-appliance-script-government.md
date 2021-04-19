---
title: Beállít egy Azure Migrate berendezést a Azure Government
description: Megtudhatja, hogyan állíthat be Azure Migrate berendezést a Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714793"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Berendezés beállítása a Azure Government 

Ebből a cikkből [](./migrate-appliance-architecture.md) Azure Migrate VMware-környezetben található kiszolgálókhoz, Hyper-V-kiszolgálókhoz és fizikai kiszolgálókhoz egy virtuális Azure Government üzembe. Egy szkript futtatásával hozza létre a berendezést, és ellenőrizze, hogy tud-e csatlakozni az Azure-hoz. Ha berendezést szeretne beállítani a nyilvános felhőben, kövesse [ezt a cikket.](deploy-appliance-script.md)


> [!NOTE]
> A VMware-környezetben és Hyper-V-környezetben található kiszolgálók esetén a sablonnal való üzembe helyezés lehetősége nem támogatott a Azure Government.


## <a name="prerequisites"></a>Előfeltételek

A szkript beállítja a Azure Migrate berendezést egy meglévő fizikai kiszolgálón vagy egy virtualizált kiszolgálón.

- A berendezésként szolgáló kiszolgálónak Windows Server 2016 rendszert kell futtatónak lennie, 32 GB memóriával, nyolc vCPU-val, körülbelül 80 GB lemezterülettel és egy külső virtuális kapcsolóval. Statikus vagy dinamikus IP-címet igényel. 
- A berendezés üzembe helyezése előtt tekintse át a [VMware,](migrate-appliance.md#appliance---vmware) [Hyper-V](migrate-appliance.md#appliance---hyper-v)és fizikai kiszolgálók kiszolgálóira vonatkozó részletes [berendezéskövetelményeket.](migrate-appliance.md#appliance---physical)
- Ne futtassa a szkriptet egy meglévő Azure Migrate berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A berendezés beállítása A VMware-hez

A berendezés VMware-hez való beállításhoz töltsön le egy tömörített fájlt a Azure Portal, és bontsa ki a tartalmát. A berendezés webalkalmazásának elindításához futtassa a PowerShell-szkriptet. Először beállítja és konfigurálja a berendezést. Ezután regisztrálja a berendezést a projektben.

### <a name="download-the-script"></a>A szkript letöltése

1. A **Windows,** Linux és SQL Servers áttelepítési célok Azure Migrate: Felderítés és értékelés  >    >  alatt kattintson a **Felderítés elemre.**
2. A **Kiszolgáló felderítése**  >  **Virtualizáltak a kiszolgálók?** beállításban válassza az **Igen, VMware vSphere hipervizorral lehetőséget.**
3. Kattintson **a Download (Letöltés)** gombra a tömörített fájl letöltéséhez.

### <a name="verify-file-security"></a>Fájlbiztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A kiszolgálón, amelyre letöltötte a fájlt, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Ellenőrizze a berendezés legújabb verzióját és kivonatértékét:

    **Algoritmus** | **Letöltés** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőt teszi:

- Ügynököket és egy webalkalmazást telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölt és telepít egy újraírható IIS-modult. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) a kulcs állandó Azure Migrate.
- A következőképpen hozza létre a napló- és konfigurációs fájlokat:
    - **Konfigurációs fájlok:**%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok:**%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. Győződjön meg arról, hogy a szkriptet nem olyan kiszolgálón futtatja, amely már Azure Migrate berendezést.
2. Indítsa el a PowerShellt a kiszolgálón rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Miután a szkript sikeresen lefutott, elindítja a berendezés webalkalmazását, hogy be tudja állítani a berendezést. Ha bármilyen probléma merül fel, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés képes csatlakozni a kormányzati felhők Azure [URL-címeihez.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>A berendezés beállítása Hyper-V-hez

A berendezés Hyper-V-hez való beállításhoz töltsön le egy tömörített fájlt a Azure Portal, és bontsa ki a tartalmát. A berendezés webalkalmazásának elindításához futtassa a PowerShell-szkriptet. Először beállítja és konfigurálja a berendezést. Ezután regisztrálja a berendezést a projektben.

### <a name="download-the-script"></a>A szkript letöltése

1.  A **Windows,** Linux és SQL Servers áttelepítési célok Azure Migrate: Felderítés és értékelés  >    >  alatt kattintson a **Felderítés elemre.**
2.  A **Kiszolgálók felderítése**  >  **Virtualizáltak a kiszolgálók?** alatt válassza az **Igen, a Hyper-V-vel lehetőséget.**
3.  Kattintson **a Download (Letöltés)** gombra a tömörített fájl letöltéséhez. 


### <a name="verify-file-security"></a>Fájlbiztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A kiszolgálón, amelyre letöltötte a fájlt, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Ellenőrizze a berendezés legújabb verzióját és kivonatértékét:

    **Forgatókönyv** | **Letöltés** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a167183a94e8347456db9c5749c7332ff2eb4b77798765e48

          

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőt teszi:

- Ügynököket és egy webalkalmazást telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölt és telepít egy újraírható IIS-modult. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) a kulcs állandó Azure Migrate.
- A következőképpen hozza létre a napló- és konfigurációs fájlokat:
    - **Konfigurációs fájlok:**%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok:**%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. Győződjön meg arról, hogy nem futtatja a szkriptet egy meglévő Azure Migrate kiszolgálón.
2. Indítsa el a PowerShellt a kiszolgálón rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Miután a szkript sikeresen lefutott, elindítja a berendezés webalkalmazását, hogy be tudja állítani a berendezést. Ha bármilyen problémába ütközik, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés képes csatlakozni a kormányzati felhőkHöz való Azure [URL-címekhez.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>A berendezés beállítása fizikai kiszolgálókhoz

A berendezés VMware-hez való beállításhoz töltsön le egy tömörített fájlt a Azure Portal, és bontsa ki a tartalmát. A berendezés webalkalmazásának elindításához futtassa a PowerShell-szkriptet. Először beállítja és konfigurálja a berendezést. Ezután regisztrálja a berendezést a projektben.

### <a name="download-the-script"></a>A szkript letöltése

1. A **Windows,** Linux és SQL Servers áttelepítési célok Azure Migrate: Felderítés és értékelés  >    >  alatt kattintson a **Felderítés elemre.**
2. A **Kiszolgálók felderítése**  >  **Virtualizáltak a kiszolgálók?** alatt válassza a Nem **virtualizált/Egyéb lehetőséget.**
3. Kattintson **a Download (Letöltés)** gombra a tömörített fájl letöltéséhez.

### <a name="verify-file-security"></a>Fájlbiztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. A kiszolgálókon, amelyekre letöltötte a fájlt, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Ellenőrizze a berendezés legújabb verzióját és kivonatértékét:

    **Forgatókönyv** | **Letöltés** _ | _ *Kivonatérték**
    --- | --- | ---
    Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőt teszi:

- Ügynököket és egy webalkalmazást telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölt és telepít egy újraírható IIS-modult. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Frissíti a beállításkulcsot (HKLM) a kulcs állandó Azure Migrate.
- A következőképpen hozza létre a napló- és konfigurációs fájlokat:
    - **Konfigurációs fájlok:**%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok:**%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. Győződjön meg arról, hogy nem futtatja a szkriptet egy meglévő Azure Migrate kiszolgálón.
2. Indítsa el a PowerShellt a kiszolgálón rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Miután a szkript sikeresen lefutott, elindítja a berendezés webalkalmazását, hogy be tudja állítani a berendezést. Ha bármilyen problémába ütközik, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés képes csatlakozni a kormányzati felhőkHöz való Azure [URL-címekhez.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Következő lépések

A berendezés üzembe helyezése után először konfigurálnia kell azt, és regisztrálnia kell a projektben.

- A berendezés beállítása a [VMware-hez.](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)
- A berendezés beállítása a [Hyper-V-hez.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- A berendezés beállítása fizikai [kiszolgálókhoz.](how-to-set-up-appliance-physical.md)
