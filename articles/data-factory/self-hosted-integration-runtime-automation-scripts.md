---
title: Saját üzemeltetésű Integration Runtime telepítésének automatizálása helyi PowerShell-parancsfájlok használatával
description: Saját üzemeltetésű Integration Runtime helyi gépekre történő telepítésének automatizálása.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376207"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Saját üzemeltetésű Integration Runtime telepítésének automatizálása helyi PowerShell-parancsfájlok használatával
Ha az Azure-beli virtuális gépeken kívül szeretné használni a saját üzemeltetésű Integration Runtime telepítését, akkor a helyi PowerShell-parancsfájlokat használhatja. Ez a cikk két olyan parancsfájlt mutat be, amelyet használhat.

## <a name="prerequisites"></a>Előfeltételek

* Indítsa el a PowerShellt a helyi gépen. A parancsfájlok futtatásához a **Futtatás rendszergazdaként** lehetőséget kell választania.
* [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű Integration Runtime szoftvert. Másolja a letöltött fájl elérési útját. 
* A saját üzemeltetésű integrációs modul regisztrálásához **hitelesítési kulcsra** is szükség van.
* A manuális frissítések automatizálásához rendelkeznie kell egy előre konfigurált, saját üzemeltetésű integrációs futtatókörnyezettel.

## <a name="scripts-introduction"></a>Parancsfájlok bemutatása 

> [!NOTE]
> Ezek a parancsfájlok a saját üzemeltetésű integrációs modulban a [dokumentált parancssori segédprogram](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) használatával jönnek létre. Ha szükséges, a szkripteket ennek megfelelően testreszabhatja az automatizálási igényeik kielégítése érdekében.
> A parancsfájlokat csomóponton kell alkalmazni, ezért mindenképpen futtassa azt az összes csomóponton a magas rendelkezésre állású telepítés (2 vagy több csomópont) esetén.

* A telepítő automatizálása: új, saját üzemeltetésű integrációs modul telepítése és regisztrálása a **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** használatával – a szkript használható a saját üzemeltetésű integrációs modul csomópontjának telepítéséhez és egy hitelesítési kulccsal való regisztrálásához. A parancsfájl két argumentumot fogad el, **először** a saját üzemeltetésű [integrációs](https://www.microsoft.com/download/details.aspx?id=39717) modul helyét adja meg egy helyi lemezen, **Másodszor** a **hitelesítési kulcs** megadását (a saját üzemeltetésű IR-csomópont regisztrálásához).

* Manuális frissítések automatizálása: frissítse a saját üzemeltetésű IR-csomópontot egy adott verzióval vagy a legújabb verzióval **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** – ez abban az esetben is támogatott, ha kikapcsolta az automatikus frissítést, vagy nagyobb mértékben szeretné vezérelni a frissítéseket. A parancsfájl segítségével frissítheti a saját üzemeltetésű integrációs modul csomópontját a legújabb verzióra vagy egy megadott magasabb verzióra (a visszalépés nem működik). Elfogad egy argumentumot a verziószám megadásához (például:-Version 3.13.6942.1). Ha nincs megadva verzió, az mindig frissíti a saját üzemeltetésű integrációs modult a [letöltésekben](https://www.microsoft.com/download/details.aspx?id=39717)található legújabb verzióra.
    > [!NOTE]
    > Csak a legutóbbi 3 verziót lehet megadni. Ideális esetben ez egy meglévő csomópont legújabb verzióra való frissítésére szolgál. **feltételezi, hogy rendelkezik egy regisztrált saját** ÜZEMELTETÉSű IR-vel. 

## <a name="usage-examples"></a>Használati példák

### <a name="for-automating-setup"></a>A telepítő automatizálása
1. Töltse le a saját üzemeltetésű IR [-t innen](https://www.microsoft.com/download/details.aspx?id=39717). 
1. Itt adhatja meg azt az elérési utat, ahol a fenti letöltött (telepítési fájl). Ha például az elérési út *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi*, akkor a következő PowerShell parancssori példát használhatja ehhez a feladathoz:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > A [kulcs] helyére írja be a hitelesítési kulcsot az IR regisztrálásához.
    > Cserélje le a "username" nevet a felhasználónevére.
    > Adja meg a "InstallGatewayOnLocalMachine.ps1" fájl helyét a parancsfájl futtatásakor. Ebben a példában a Desktopban tároltuk.

1. Ha van egy előre telepített, saját üzemeltetésű integrációs modul a gépen, a parancsfájl automatikusan eltávolítja, majd egy újat konfigurál. Ekkor megjelenik az alábbi ablak: ![ Integration Runtime konfigurálása](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. A telepítés és a kulcs regisztrációjának befejezésekor látni fogja, *hogy az átjáró telepítése sikeres* lesz, és a rendszer *sikeresen regisztrálja az átjáró* eredményeit a helyi PowerShellben.
        [![1. parancsfájl futtatási eredménye](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Manuális frissítések automatizálása
Ez a szkript a legújabb saját üzemeltetésű integrációs modul frissítésére/telepítésére és regisztrálására szolgál. A szkript futtatása a következő lépéseket hajtja végre:
1. A jelenlegi saját üzemeltetésű IR-verzió ellenőrzésének megtekintése
2. A legújabb verzió vagy a megadott verzió beolvasása az argumentumból
3. Ha az aktuális verziónál újabb verzió van:
    * saját üzemeltetésű IR MSI letöltése
    * frissítés

Az alábbi parancssori példát követve használhatja ezt a parancsfájlt:
* Töltse le és telepítse a legújabb átjárót:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* A megadott verzió átjárójának letöltése és telepítése:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Ha a jelenlegi verziója már a legújabb, a következő eredményt fogja látni, ami azt sugallja, hogy nincs szükség frissítésre.   
    [![2. parancsfájl futtatása eredmény](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)