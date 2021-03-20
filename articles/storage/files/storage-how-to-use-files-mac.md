---
title: Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathat egy Azure-fájlmegosztás SMB-kapcsolaton keresztül macOS-sel a Finder vagy a Terminal használatával. Az Azure Files a Microsoft könnyen használható felhőalapú fájlrendszere.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91326065"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Azure-fájlmegosztás csatlakoztatása SMB protokoll segítségével macOS rendszeren
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztás az iparági szabványnak megfelelő SMB 3 protokollal csatlakoztatható a macOS High Sierra 10.13 + használatával. Ez a cikk két különböző módszert mutat be az Azure-fájlmegosztások macOS rendszeren történő csatlakoztatására: a Finder felhasználói felület és a Terminál használatát.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Az Azure-fájlmegosztások macOS rendszerre történő csatlakoztatásának előfeltételei
* **Storage-fiók neve**: egy Azure-fájlmegosztás csatlakoztatásához szüksége lesz a Storage-fiók nevére.

* **Storage-fiók kulcsa**: az Azure-fájlmegosztás csatlakoztatásához szüksége lesz az elsődleges (vagy másodlagos) tárolási kulcsra. Az SAS-kulcsokkal való csatlakoztatás jelenleg nem támogatott.

* **Győződjön meg arról, hogy a 445-ös port nyitva legyen**: Az SMB protokoll a 445-ös TCP porton keresztül kommunikál. Az ügyfélgépen (Mac) ellenőrizze, és győződjön meg arról, hogy a tűzfal nem blokkolja a 445-ös TCP portot.

## <a name="mount-an-azure-file-share-via-finder"></a>Azure-fájlmegosztás csatlakoztatása a Finder segítségével
1. **Nyissa meg a Findert**: A Finder alapértelmezés szerint meg van nyitva a macOS rendszereken, de biztosan megnyithatja, ha az aktuálisan kiválasztott alkalmazásban a „macOS arc ikonra” kattint a Dockban:  
    ![A macOS arc ikonja](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Válassza a "kapcsolódás a kiszolgálóhoz" lehetőséget a "Go" menüben**: az előfeltételekből származó UNC elérési út használatával alakítsa át a dupla fordított perjelet () és az összes többi fordított perjelet () a `\\` `smb://` `\` perjel () továbbításához `/` . A hivatkozásnak az alábbi módon kell kinéznie: ![A „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédablak](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Használja a tárfiók nevét és kulcsát, amikor a rendszer felkéri a felhasználónév és a jelszó megadására**: Ha a „Connect” (Kapcsolódás) elemre kattint a „Connect to Server” (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen, a rendszer felkéri a felhasználónév és a jelszó megadására (a rendszer automatikusan kitölti ezt a macOS-felhasználónevével). Lehetősége van arra, hogy hozzáadja a tárfiók nevét és kulcsát a macOS-kulcskarikához.

4. **Használja az Azure-fájlmegosztást igény szerint**: miután a felhasználónévhez és a jelszóhoz becserélte a megosztás nevét és a Storage-fiók kulcsát, a rendszer csatlakoztatja a megosztást. Ezt ugyanúgy használhatja, mint egy helyi mappát/fájlmegosztást, beleértve azt is, hogy fájlokat húzhat a fájlmegosztásba:

    ![Pillanatfelvétel egy csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Azure-fájlmegosztás csatlakoztatása a Terminál segítségével
1. Cserélje `<storage-account-name>` le `<storage-account-key>` a, a és a `<share-name>` értéket a környezetének megfelelő értékekre.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Használja az Azure-fájlmegosztást igény szerint**: az Azure-fájlmegosztás az előző parancs által megadott csatlakoztatási ponton lesz csatlakoztatva.  

    ![Pillanatfelvétel a csatlakoztatott Azure-fájlmegosztásról](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Következő lépések
* [A Mac összekötése megosztott számítógépekkel és kiszolgálókhoz – Apple-támogatás](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)