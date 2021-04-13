---
title: Windows Virtual Desktop MSIX-alkalmazás csatolási rendszerképének előkészítése – Azure
description: MSIX-alkalmazás csatolási lemezképének létrehozása Windows Virtual Desktop gazdagépkészlethez.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363665"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>MSIX-rendszerkép előkészítése Windows Virtual Desktop

Az MSIX-alkalmazás csatolása egy alkalmazásréteg-megoldás, amely lehetővé teszi alkalmazások dinamikus csatolását egy MSIX-csomagból egy felhasználói munkamenethez. Az MSIX csomagrendszer elkülöníti az alkalmazásokat az operációs rendszertől, így könnyebb lemezképeket összeépíteni a virtuális gépek számára. Az MSIX-csomagok azt is nagyobb mértékben szabályozják, hogy a felhasználók mely alkalmazásokhoz férhetnek hozzá a virtuális gépeiken. Az alkalmazásokat külön is elkülönítheti a fő rendszerképtől, és később használhatja őket a felhasználóknak.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>VHD- vagy VHDX-csomag létrehozása az MSIX-hez

Az MSIX-csomagoknak VHD- vagy VHDX-formátumban kell lennie, hogy megfelelően működjön. Ez azt jelenti, hogy a első lépésekhez létre kell hoznia egy VHD- vagy VHDX-csomagot.

>[!NOTE]
>Ha még nem, engedélyezze a Hyper-V-t a [Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)telepítése az Windows 10.

VHD- vagy VHDX-csomag létrehozása az MSIX-hez:

1. Először nyissa meg a PowerShellt.
2. Ezután futtassa a következő parancsmagot a virtuális merevlemez létrehozásához:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Győződjön meg arról, hogy a VHD elég nagy a kibontott MSIX-csomaghoz.

3. Futtassa a következő parancsmagot az előbb létrehozott virtuális merevlemez csatlakoztatáshoz:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Ezután futtassa ezt a parancsmagot a csatlakoztatott VHD inicializáláshoz:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Ezután futtassa ezt a parancsmagot az inicializált VIRTUÁLIS merevlemez új partíciója létrehozásához:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Futtassa ezt a parancsmagot a partíció formázására:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Végül hozzon létre egy szülőmappát a csatlakoztatott VHD-en. Erre a lépésre azért van szükség, mert az MSIX-csomagnak szülőmappának kell lennie a megfelelő munkához. A szülőmappa elnevezése nem számít, amíg a szülőmappa létezik.

## <a name="expand-msix"></a>Az MSIX kibontása

Ezután ki kell bontsa az MSIX-rendszerképet úgy, hogy "kicsomagolja" a fájljait a VHD-be.

Az MSIX-rendszerkép kibontása:

1. [Töltse le az msixmgr eszközt,](https://aka.ms/msixmgr) és mentse a .zip mappát egy munkamenetgazda virtuális gép egyik mappájába.

2. Csomagolja ki az msixmgr eszköz .zip mappáját.

3. Helyezze a forrás MSIX-csomagot ugyanoda a mappába, ahol kicsomagolta az msixmgr eszközt.

4. Nyisson meg egy parancssort rendszergazdaként, és keresse meg azt a mappát, ahová letöltötte és kicsomagolta az msixmgr eszközt.

5. A következő parancsmag futtatásával csomagolja ki az MSIX-et az előző szakaszban létrehozott VHD-be.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A kicsomagolás után a következő üzenetnek kell megjelennie:

    > Sikeresen kicsomagolta és alkalmazta az ACL-eket a következő csomaghoz: <package name> .msix

    >[!NOTE]
    > Ha az Microsoft Store Vállalatoknak vagy az Education csomagjait használja a hálózatán vagy az internethez nem csatlakozó eszközökön, az alkalmazások futtatásához le kell töltenie és telepítenie kell a csomaglicenceket a Microsoft Store-ból. A licencek lekért lásd: [Csomagok offline használata.](app-attach.md#use-packages-offline)

6. Nyissa meg a csatlakoztatott VHD-t, és nyissa meg az alkalmazásmappát, és ellenőrizze, hogy a csomag tartalma ott van-e.

7. Válassza le a virtuális merevlemezt.

## <a name="upload-msix-image-to-share"></a>MSIX-rendszerkép feltöltése megosztáshoz

Az MSIX-csomag létrehozása után fel kell töltenie az eredményül kapott VHD-, VHDX- vagy CIM-fájlt egy olyan megosztásra, ahol a felhasználók virtuális gépei hozzáférhetnek.

## <a name="next-steps"></a>Következő lépések

A funkcióval kapcsolatos kérdéseit a [TechCo Windows Virtual Desktop fel.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

Visszajelzést is küldhet a Windows Virtual Desktop a [Windows Virtual Desktop visszajelzési központban.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)

Íme néhány további cikk, amelyek hasznosak lehetnek:

- [MSIX-alkalmazás csatolási szószedete](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)