---
title: Windows rendszerű virtuális asztali felkészülés MSIX alkalmazás csatolása Képelőnézet – Azure
description: Rendszerkép csatolása egy Windows rendszerű virtuális asztali MSIX létrehozásához.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d909d04c38a7d6bad42020175cbbbfcfd7bf9f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448355"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>MSIX-rendszerkép előkészítése a Windows rendszerű virtuális asztalhoz

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A MSIX app Attach (előzetes verzió) egy alkalmazás-rétegbeli megoldás, amely lehetővé teszi, hogy dinamikusan csatolja az alkalmazásokat egy MSIX-csomagból egy felhasználói munkamenetbe. A MSIX Package rendszer elkülöníti az alkalmazásokat az operációs rendszertől, így könnyebben hozhat létre lemezképeket a virtuális gépek számára. A MSIX-csomagok nagyobb mértékben szabályozzák, hogy mely alkalmazások férhetnek hozzá a felhasználókhoz a virtuális gépeken. Akár külön is elkülönítheti az alkalmazásokat a fő rendszerképből, és később is megadhatja őket a felhasználóknak.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>VHD-vagy VHDX-csomag létrehozása a MSIX

A MSIX-csomagoknak VHD-vagy VHDX-formátumúnak kell lenniük ahhoz, hogy megfelelően működjenek. Ez azt jelenti, hogy a kezdéshez létre kell hoznia egy VHD-vagy VHDX-csomagot.

>[!NOTE]
>Ha még nem tette meg, győződjön meg arról, hogy engedélyezi a Hyper-V-t a [Hyper-v telepítése Windows 10 rendszeren](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)című témakör útmutatásait követve.

VHD-vagy VHDX-csomag létrehozása a MSIX-hez:

1. Először nyissa meg a PowerShellt.
2. Ezután futtassa a következő parancsmagot a virtuális merevlemez létrehozásához:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Győződjön meg arról, hogy a VHD elég nagy méretű a kibontott MSIX-csomag tárolásához.

3. Futtassa a következő parancsmagot az imént létrehozott virtuális merevlemez csatlakoztatásához:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Ezután futtassa ezt a parancsmagot a csatlakoztatott virtuális merevlemez inicializálásához:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Ezt követően futtassa ezt a parancsmagot egy új partíció létrehozásához a inicializált virtuális merevlemezhez:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Futtassa ezt a parancsmagot a partíció formázásához:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Végül hozzon létre egy szülő mappát a csatlakoztatott virtuális merevlemezen. Erre a lépésre azért van szükség, mert a MSIX-csomagnak rendelkeznie kell egy szülő-mappával, hogy megfelelően működjön. A szülőmappa nevét nem számítja ki, ha a szülő mappa létezik.

## <a name="expand-msix"></a>MSIX kibontása

Ezt követően ki kell bővíteni a MSIX-rendszerképet a fájljainak a VHD-be való kicsomagolásával.

A MSIX-rendszerkép kibontása:

1. [Töltse le a msixmgr eszközt](https://aka.ms/msixmgr) , és mentse a. zip mappát egy, a munkamenet-gazda virtuális gépen található mappába.

2. Bontsa ki a msixmgr Tool. zip mappát.

3. Helyezze a forrás MSIX-csomagot ugyanabba a mappába, ahová kicsomagolta a msixmgr eszközt.

4. Nyisson meg egy parancssort rendszergazdaként, és navigáljon ahhoz a mappához, ahová letöltötte és kicsomagolta a msixmgr eszközt.

5. A következő parancsmag futtatásával csomagolja ki a MSIX az előző szakaszban létrehozott VHD-be.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A kicsomagolás befejezése után a következő üzenetnek kell megjelennie:

    > Sikerült kicsomagolni és alkalmazni az ACL-eket a következő csomaghoz: <package name> . msix

    >[!NOTE]
    > Ha a hálózaton vagy az internetre nem csatlakozó eszközökön csomagokat használ a Microsoft Store vállalati vagy oktatási célokra, akkor az alkalmazások futtatásához le kell töltenie és telepítenie kell a csomag-licenceket a Microsoft Store. A licencek beszerzéséhez tekintse meg a [csomagok offline használata](app-attach.md#use-packages-offline)című témakört.

6. Lépjen a csatlakoztatott virtuális merevlemezre, és nyissa meg az alkalmazás mappáját, és győződjön meg arról, hogy a csomag tartalma ott van.

7. Válassza le a virtuális merevlemezt.

## <a name="upload-msix-image-to-share"></a>MSIX-rendszerkép feltöltése a megosztáshoz

Miután létrehozta a MSIX csomagot, fel kell töltenie az eredményül kapott VHD-, VHDX-vagy CIM-fájlt egy olyan megosztásba, ahol a felhasználók virtuális gépei el tudják érni.

## <a name="next-steps"></a>Következő lépések

Kérje meg a közösségi kérdéseket a szolgáltatással kapcsolatban a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.

Az alábbi cikkek hasznosnak bizonyulnak:

- [MSIX-alkalmazás szószedetének csatolása](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)