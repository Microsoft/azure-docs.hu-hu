---
title: A MSIXMGR eszköz használata – Azure
description: A Windows rendszerű virtuális asztali MSIXMGR eszköz használata.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b2cab56fb99bda7da361cc2068396e53e794501d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448338"
---
# <a name="using-the-msixmgr-tool"></a>A MSIXMGR eszköz használata

A MSIXMGR eszköz a MSIX által csomagolt alkalmazások MSIX-lemezképekre való kiterjesztésére szolgál. Az eszköz egy MSIX által csomagolt alkalmazást (. MSIX), és kibontja egy VHD-, VHDx-vagy CIM-fájlba. Az eredményül kapott MSIX-rendszerkép az Azure Storage-fiókban tárolódik, amelyet a Windows rendszerű virtuális asztal üzembe helyezése használ. Ez a cikk bemutatja, hogyan használhatja a MSIXMGR eszközt.

>[!NOTE]
>A kompatibilitás biztosításához győződjön meg arról, hogy a Windows rendszerű virtuális asztali CIMs futó operációsrendszer-verzióban a MSIX-lemezképek tárolása történik. A MSIXMGR képes CIM-fájlokat létrehozni, de ezeket a fájlokat csak a Windows 10 20H2 futtató gazdagépekkel lehet használni.

## <a name="requirements"></a>Követelmények

A cikkben szereplő utasítások követéséhez a következőket kell tennie:

- [A MSIXMGR eszköz letöltése](https://aka.ms/msixmgr)
- MSIX-csomagban lévő alkalmazás beszerzése (. MSIX-fájl)
- Rendszergazdai engedélyek beszerzése azon a gépen, amelyen létre kívánja hozni a MSIX-rendszerképet

## <a name="create-an-msix-image"></a>MSIX-rendszerkép létrehozása

A bővítés a MSIX csomagolt alkalmazások (. MSIX), és kicsomagolása egy MSIX-rendszerképbe (. VHD (x) vagy. CIM-fájl).

MSIX-fájl kibontása:

1. Ha még nem tette meg, [töltse le a MSIXMGR eszközt](https://aka.ms/msixmgr) .

2. MSIXMGR.zip kicsomagolása helyi mappába.

3. Nyisson meg egy parancssort emelt szintű módban.

4. Keresse meg a helyi mappát a 2. lépésben.

5. Futtassa az alábbi parancsot a parancssorban egy MSIX-rendszerkép létrehozásához.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Ne felejtse el lecserélni a helyőrző értékeket a megfelelő értékekre. Például:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Most, hogy létrehozta a rendszerképet, nyissa meg a célmappát, és győződjön meg arról, hogy sikeresen létrehozta a MSIX-rendszerképet (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>MSIX-rendszerkép létrehozása CIM-fájlban

A CIM-és VHDX-fájlok létrehozásához az [5. lépésben](#create-an-msix-image) szereplő parancsot is használhatja a fájl típusának és a célhely elérési útjának lecserélésével.

Tegyük fel például, hogy ezt a parancsot használja egy CIM-fájl létrehozásához:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

A következő parancs használatával VHDX teheti a parancsot:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Következő lépések

További információ a MSIX-alkalmazáshoz a [MSIX-alkalmazás csatolása](what-is-app-attach.md) című témakörben található?

Az alkalmazások csatolásának beállításával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [MSIX-alkalmazás csatolásának beállítása az Azure Portal használatával](app-attach-azure-portal.md)
- [MSIX-alkalmazás csatolásának beállítása a PowerShell használatával](app-attach-powershell.md)
- [PowerShell-parancsfájlok létrehozása MSIX-alkalmazás csatolásához](app-attach.md)
- [MSIX-rendszerkép előkészítése a Windows rendszerű virtuális asztalhoz](app-attach-image-prep.md)
- [Fájlmegosztás beállítása a MSIX-alkalmazás csatolásához](app-attach-file-share.md)

Ha kérdése van a MSIX-alkalmazás csatolásával kapcsolatban, tekintse meg az [alkalmazás csatolása GYIK](app-attach-faq.md) és az [alkalmazás csatolása szószedetet](app-attach-glossary.md).
