---
title: Azure Migrate berendezés beállítása parancsfájlokkal
description: Megtudhatja, hogyan állíthat be egy Azure Migrate készüléket parancsfájl használatával
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: d5603aaef0a1c3e784f455777302c23e6724fbe7
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052469"
---
# <a name="set-up-an-appliance-with-a-script"></a>Berendezés beállítása parancsfájlsal

Ez a cikk a VMware virtuális gépek és a Hyper-V virtuális gépek értékeléséhez/áttelepítéséhez [Azure Migrate berendezés](deploy-appliance.md) létrehozásához nyújt segítséget. Parancsfájl futtatásával létrehozhat egy berendezést, és ellenőrizheti, hogy tud-e csatlakozni az Azure-hoz. 

A készüléket a VMware és a Hyper-V virtuális gépekhez parancsfájl használatával, vagy a Azure Portalból letöltött sablon használatával telepítheti. A szkriptek használata akkor hasznos, ha nem tud virtuális gépet létrehozni a letöltött sablonnal.

- A sablonok használatához kövesse a [VMware](tutorial-prepare-vmware.md) vagy a [Hyper-V](tutorial-prepare-hyper-v.md)oktatóanyagokat.
- Ha fizikai kiszolgálókon szeretné beállítani a készüléket, csak szkripteket használhat. Kövesse [ezt a cikket](how-to-set-up-appliance-physical.md).
- Ha Azure Government felhőben szeretné beállítani a készüléket, kövesse [ezt a cikket](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Előfeltételek

A szkript beállítja a Azure Migrate készüléket egy meglévő fizikai gépre vagy virtuális gépre.

- A készülékként működni kívánó gépnek meg kell felelnie az alábbi hardver-és operációsrendszer-követelményeknek:

Forgatókönyv | Követelmények
--- | ---
VMware | Windows Server 2016, 32 GB memóriával, nyolc vCPU, körülbelül 80 GB lemezes tárolással
Hyper-V | Windows Server 2016, 16 GB memóriával, nyolc vCPU, körülbelül 80 GB lemezes tárolással
- A gépnek külső virtuális kapcsolóra is szüksége van. Statikus vagy dinamikus IP-címet igényel, és hozzáférést biztosít az internethez.
- A készülék üzembe helyezése előtt tekintse át a következőt: [VMWare virtuális](migrate-appliance.md#appliance---vmware)gépek, [Hyper-V virtuális gépek](migrate-appliance.md#appliance---hyper-v)részletes berendezési követelményei.
- Ne futtassa a parancsfájlt egy meglévő Azure Migrate berendezésen.

## <a name="set-up-the-appliance-for-vmware"></a>A készülék beállítása a VMware-hez

A készülék VMware-hez való beállításához töltse le a AzureMigrateInstaller.zip nevű tömörített fájlt [innen](https://go.microsoft.com/fwlink/?linkid=2105112), és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Ellenőrizze a készülék legújabb verzióját és parancsfájlját az Azure nyilvános felhőhöz:

    **Algoritmus** | **Letöltés** | **SHA256**
    --- | --- | ---
    VMware (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0



### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa **AzureMigrateInstaller.ps1**szkriptet a következő módon:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék képes csatlakozni a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőhöz tartozó Azure URL-címekhez.

## <a name="set-up-the-appliance-for-hyper-v"></a>A készülék beállítása a Hyper-V-hez

A Hyper-V berendezésének beállításához töltse le a AzureMigrateInstaller.zip nevű tömörített fájlt [innen](https://go.microsoft.com/fwlink/?linkid=2105112), és bontsa ki a tartalmat. Futtatja a PowerShell-szkriptet a készülék webalkalmazásának elindításához. Állítsa be a készüléket, és konfigurálja az első alkalommal. Ezután regisztrálja a készüléket a Azure Migrate projekttel.


### <a name="verify-file-security"></a>A fájl biztonságának ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Például: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Ellenőrizze a készülék legújabb verzióját és parancsfájlját az Azure nyilvános felhőhöz:

    **Forgatókönyv** | **Letöltés** | **SHA256**
    --- | --- | ---
    Hyper-V (63,1 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2105112) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

### <a name="run-the-script"></a>A szkript futtatása

A szkript a következőket teszi:

- Ügynököket és webalkalmazásokat telepít.
- Telepíti a Windows-szerepköröket, beleértve a Windows aktiválási szolgáltatást, az IIS-t és a PowerShell ISE-t.
- Letölti és telepíti az IIS újraírható modulját. [További információ](https://www.microsoft.com/download/details.aspx?id=7435).
- Egy beállításkulcs (HKLM) frissítése a Azure Migrate állandó beállításaival.
- A a következőképpen hozza létre a naplófájlokat és a konfigurációs fájlokat:
    - **Konfigurációs fájlok**:%ProgramData%\Microsoft Azure\Config
    - **Naplófájlok**:%ProgramData%\Microsoft Azure\Logs

A szkript futtatása:

1. Bontsa ki a tömörített fájlt egy olyan mappába a gépen, amely a készüléket fogja üzemeltetni. Győződjön meg arról, hogy nem futtatja a parancsfájlt egy meglévő Azure Migrate berendezésen lévő gépen.
2. Indítsa el a PowerShellt a gépen, rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kinyert tartalmat tartalmazza.
4. Futtassa **AzureMigrateInstaller.ps1**szkriptet a következő módon:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. A szkript sikeres futtatása után elindítja a berendezés webalkalmazását, hogy beállítsa a készüléket. Ha bármilyen problémába ütközik, tekintse át a szkriptek naplóit a következő helyen: C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Hozzáférés ellenőrzése

Győződjön meg arról, hogy a készülék képes csatlakozni a [nyilvános](migrate-appliance.md#public-cloud-urls) felhőhöz tartozó Azure URL-címekhez.

## <a name="next-steps"></a>További lépések

A készülék üzembe helyezése után először be kell állítania, majd regisztrálnia kell a Azure Migrate projektben.

- Állítsa be a készüléket a [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance)-hez.
- Állítsa be a készüléket a [Hyper-V-](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)hez.
