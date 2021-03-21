---
title: StorSimple 8000 Series támogatási csomag létrehozása
description: Megtudhatja, hogyan hozhat létre, fejtheti ki és szerkesztheti a StorSimple 8000 Series-eszköz támogatási csomagját.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017168"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Támogatási csomag létrehozása és kezelése a StorSimple 8000 sorozathoz

## <a name="overview"></a>Áttekintés

A StorSimple-támogatási csomag egy könnyen használható mechanizmus, amely az összes releváns naplót gyűjti, hogy segítséget nyújtson Microsoft ügyfélszolgálatanek a StorSimple-eszközök hibáinak elhárításában. Az összegyűjtött naplók titkosítottak és tömörítettek.

Ez az oktatóanyag részletes útmutatást tartalmaz a StorSimple 8000 Series-eszköz támogatási csomagjának létrehozásához és kezeléséhez. Ha StorSimple virtuális Tömbvel dolgozik, ugorjon a [naplófájl létrehozása](storsimple-ova-web-ui-admin.md#generate-a-log-package)lehetőségre.

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

Bizonyos esetekben manuálisan kell létrehoznia a támogatási csomagot Windows PowerShell StorSimple-bővítményeon keresztül. Például:

* Ha el kell távolítania a bizalmas adatokat a naplófájlokból a Microsoft ügyfélszolgálata megosztása előtt.
* Ha a csatlakozási problémák miatt nem sikerül feltölteni a csomagot.

A manuálisan létrehozott támogatási csomagot megoszthatja Microsoft ügyfélszolgálata e-mail-cím használatával. A következő lépések végrehajtásával hozzon létre egy támogatási csomagot a Windows PowerShell StorSimple-bővítménye.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Támogatási csomag létrehozása Windows PowerShell StorSimple-bővítménye

1. Ha a Windows PowerShell-munkamenetet rendszergazdaként szeretné elindítani a StorSimple-eszközhöz való kapcsolódáshoz használt távoli számítógépen, írja be a következő parancsot:
   
    `Start PowerShell`
2. A Windows PowerShell-munkamenetben kapcsolódjon az eszköz SSAdmin-konzolhoz:
   
   1. A parancssorba írja be a következőt:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. A megnyíló párbeszédpanelen adja meg az eszköz rendszergazdai jelszavát. Az alapértelmezett jelszó a _jelszó1_.
     
      ![PowerShell hitelesítő adatai párbeszédpanel](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Válassza az **OK** lehetőséget.
   4. A parancssorba írja be a következőt:
     
      `Enter-PSSession $MS`
3. A megnyíló munkamenetben adja meg a megfelelő parancsot.
   
   * A jelszóval védett hálózati megosztások esetében írja be a következőt:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Meg kell adnia egy jelszót és egy titkosítási jelszót (mivel a támogatási csomag titkosítva van). A rendszer Ezután létrehoz egy támogatási csomagot az alapértelmezett mappában (az eszköz neve az aktuális dátummal és idővel van hozzáfűzve).
   * A jelszóval nem védett megosztások esetében nincs szükség a `-Credential` paraméterre. Adja meg a következőket:
     
       `Export-HcsSupportPackage`
     
       A támogatási csomag az alapértelmezett mappában mindkét vezérlőhöz létrejön. A csomag egy titkosított, tömörített fájl, amelyet a rendszer a hibaelhárításhoz Microsoft ügyfélszolgálata küldhet. További információ: [Contact Microsoft ügyfélszolgálata](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>A Export-HcsSupportPackage parancsmag paraméterei

A következő paramétereket használhatja a Export-HcsSupportPackage parancsmaggal.

| Paraméter | Kötelező vagy nem kötelező | Leírás |
| --- | --- | --- |
| `-Path` |Kötelező |A használatával megadhatja annak a hálózati megosztott mappának a helyét, amelyben a támogatási csomag el van helyezve. |
| `-EncryptionPassphrase` |Kötelező |A használatával adjon meg egy jelszót a támogatási csomag titkosításához. |
| `-Credential` |Választható |A használatával adja meg a hálózati megosztott mappához tartozó hozzáférési hitelesítő adatokat. |
| `-Force` |Választható |Ezzel a paranccsal átugorhatja a titkosítási jelszó megerősítésének lépését. |
| `-PackageTag` |Választható |Ezzel a paranccsal adhatja meg a könyvtárat a támogatási csomag elhelyezésének *elérési útja* alatt. Az alapértelmezett érték [eszköznév] – [aktuális dátum és idő: ÉÉÉÉ-HH-NN-HH-hh-SS]. |
| `-Scope` |Választható |Válassza a **fürtként** (alapértelmezett) lehetőséget egy támogatási csomag létrehozásához mindkét vezérlőhöz. Ha csak az aktuális vezérlőhöz szeretne csomagot létrehozni, akkor a **vezérlőt** kell megadnia. |

## <a name="edit-a-support-package"></a>Támogatási csomag szerkesztése

A támogatási csomag létrehozása után előfordulhat, hogy a bizalmas adatok eltávolításához szerkesztenie kell a csomagot. Ebbe beletartozhatnak a kötetek neve, az eszköz IP-címei és a biztonsági mentési nevek a naplófájlokban.

> [!IMPORTANT]
> Csak Windows PowerShell StorSimple-bővítménye által generált támogatási csomagot lehet szerkeszteni. A Azure Portalban létrehozott csomagok nem szerkeszthetők a StorSimple Eszközkezelő szolgáltatással.

Ha a Microsoft ügyfélszolgálata helyre történő feltöltés előtt szeretne szerkeszteni egy támogatási csomagot, először fejtse vissza a támogatási csomagot, szerkessze a fájlokat, majd újra titkosítsa. Hajtsa végre a következő lépéseket.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Támogatási csomag szerkesztése Windows PowerShell StorSimple-bővítménye

1. Hozzon létre egy támogatási csomagot az előzőekben leírtak szerint a [támogatási csomag létrehozásához Windows PowerShell StorSimple-bővítményeban](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Töltse le helyileg a parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) az ügyfélen.
3. Importálja a Windows PowerShell-modult. Adja meg annak a helyi mappának az elérési útját, amelyben a parancsfájlt letöltötte. A modul importálásához írja be a következőt:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Az összes fájl tömörített és titkosított *. AES* -fájl. A fájlok kibontásához és visszafejtéséhez írja be a következőt:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Vegye figyelembe, hogy a tényleges fájlkiterjesztések mostantól megjelennek az összes fájlhoz.
   
    ![Támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Ha a rendszer a titkosítási jelszó megadását kéri, adja meg a támogatási csomag létrehozásakor használt jelszót.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Keresse meg a naplófájlokat tartalmazó mappát. Mivel a naplófájlok kibontása és visszafejtése már megtörtént, a fájlok eredeti kiterjesztéssel rendelkeznek. A fájlok módosításával távolítsa el az ügyfélre vonatkozó adatokat, például a kötetek nevét és az eszköz IP-címeit, és mentse a fájlokat.
7. A fájlok bezárásával tömörítheti azokat a gzip használatával, és titkosíthatja azokat az AES-256-mel. Ez a támogatási csomag hálózaton keresztül történő átvitelének gyorsasága és biztonsága. A fájlok tömörítéséhez és titkosításához írja be a következőt:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![2. támogatási csomag szerkesztése](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Ha a rendszer kéri, adjon meg egy titkosítási jelszót a módosított támogatási csomaghoz.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Jegyezze fel az új jelszót, hogy megossza azt Microsoft ügyfélszolgálata ha szükséges.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Példa: fájlok szerkesztése egy támogatási csomagban a jelszóval védett megosztáson

Az alábbi példa egy támogatási csomag visszafejtését, szerkesztését és újratitkosítását mutatja be.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [támogatási csomagban gyűjtött információkról](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Ismerje meg, hogyan [használhatók a támogatási csomagok és az eszközök naplófájljai az eszköz üzembe helyezésének hibakereséséhez](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Ismerje meg, hogyan kezelheti a [StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással](storsimple-8000-manager-service-administration.md).

