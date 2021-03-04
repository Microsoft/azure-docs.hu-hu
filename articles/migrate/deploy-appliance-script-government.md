---
title: Azure Migrate berendezés beállítása Azure Government
description: Megtudhatja, hogyan állíthat be Azure Migrate készüléket a Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 6778c8e5e1e4fa83d34141fd13ba21d483ab76e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041348"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Berendezés beállítása Azure Government 

Ezt a cikket követve üzembe helyezhet egy [Azure Migrate berendezést](./migrate-appliance-architecture.md) VMWare virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók számára egy Azure Government-felhőben. Parancsfájl futtatásával hozza létre a készüléket, és ellenőrizze, hogy tud-e csatlakozni az Azure-hoz. Ha a készüléket a nyilvános felhőben szeretné beállítani, kövesse [ezt a cikket](deploy-appliance-script.md).


> [!NOTE]
> A készülékek sablon használatával történő üzembe helyezésének lehetősége (VMware virtuális gépek és Hyper-V virtuális gépek esetén) nem támogatott Azure Governmentban.


## <a name="prerequisites"></a>Előfeltételek

A szkript beállítja a Azure Migrate készüléket egy meglévő fizikai gépre vagy virtuális gépre.

- A készülékként működni kívánó gépnek Windows Server 2016, 32 GB memóriával, nyolc vCPU, körülbelül 80 GB lemezterülettel és külső virtuális kapcsolóval kell futnia. Statikus vagy dinamikus IP-címet igényel, és hozzáférést biztosít az internethez.
- A készülék központi telepítése előtt tekintse át a [VMWare virtuális gépek](migrate-appliance.md#appliance---vmware), a [Hyper-V virtuális gépek](migrate-appliance.md#appliance---hyper-v)és a [fizikai kiszolgálók](migrate-appliance.md#appliance---physical)részletes készülékre vonatkozó követelményeit.
- Ne futtassa a parancsfájlt egy meglévő Azure Migrate berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A készülék beállítása a VMware-hez

A készülék VMware-hez való beállításához töltse le a tömörített fájlt a Azure Portalból, és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.

### <a name="download-the-script"></a>A parancsfájl letöltése

1.  Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés** gombra.
2.  A **felderítési gépeken** a  >  **gépek virtualizáltak?** területen válassza **az igen, VMware vSphere Hypervisort**.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra. 


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Ellenőrizze a készülék legújabb verziójának és kivonatának értékét:

    **Algoritmus** | **Letöltés** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információk](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa **AzureMigrateInstaller.ps1** szkriptet a következő módon: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címeihez a [kormányzati felhőkhöz](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>A készülék beállítása a Hyper-V-hez

A Hyper-V berendezésének beállításához töltse le a tömörített fájlt a Azure Portalból, és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.

### <a name="download-the-script"></a>A parancsfájl letöltése

1.  Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés** gombra.
2.  A **felderítési gépek** a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V** lehetőséget.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra. 


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Ellenőrizze a készülék legújabb verziójának és kivonatának értékét:

    **Forgatókönyv** | **Letöltés** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információk](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa **AzureMigrateInstaller.ps1** szkriptet a következő módon: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címeihez a [kormányzati felhőkhöz](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>A készülék beállítása fizikai kiszolgálókhoz

A készülék VMware-hez való beállításához töltse le a tömörített fájlt a Azure Portalból, és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.

### <a name="download-the-script"></a>A parancsfájl letöltése

1.  Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderítés** gombra.
2.  A **felderítési gépek** a  >  **gépek virtualizáltak?** területen válassza a **nem virtualizált/egyéb** lehetőséget.
3.  A tömörített fájl letöltéséhez kattintson a **Letöltés** gombra. 


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Ellenőrizze a készülék legújabb verziójának és kivonatának értékét:

    **Forgatókönyv** | **Letöltés** _ | _ *Kivonatoló érték**
    --- | --- | ---
    Fizikai (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információk](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa **AzureMigrateInstaller.ps1** szkriptet a következő módon: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék tud csatlakozni az Azure URL-címeihez a [kormányzati felhőkhöz](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Következő lépések

A készülék üzembe helyezése után először be kell állítania, majd regisztrálnia kell a Azure Migrate projektben.

- Állítsa be a készüléket a [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)-hez.
- Állítsa be a készüléket a [Hyper-V-](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)hez.
- Állítsa be a készüléket [fizikai kiszolgálókhoz](how-to-set-up-appliance-physical.md).
