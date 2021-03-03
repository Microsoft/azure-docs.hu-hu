---
title: Microsoft Office FSLogix telepítése a Windows rendszerű virtuális asztalon – Azure
description: Az FSLogix létrehozása az Office-alkalmazásokkal a Windows virtuális asztalon az alkalmazási szabálygyűjtemény használatával.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746040"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Microsoft Office telepítése a FSLogix alkalmazás-tárolók használatával

A Microsoft Office gyorsan és hatékonyan telepítheti a FSLogix lévő más virtuális gépek (VM-EK) sablonként való használatával.

A FSLogix alkalmazás-tárolók használata a következő okból nyújt segítséget a telepítés gyorsabb elvégzéséhez:

- Az Office-alkalmazások alkalmazás-tárolóba való kiszervezése csökkenti a C meghajtó méretének követelményeit.
- A virtuális gép pillanatképei vagy biztonsági mentései kevesebb erőforrást igényelnek.
- Ha egy automatikus folyamattal rendelkezik, egy adott rendszerkép frissítésével egyszerűbbé válik a virtuális gépek frissítése.
- Csak egy rendszerképre van szükség az Office (és más alkalmazások) telepítéséhez a Windows rendszerű virtuális asztali környezet összes virtuális gépére.

Ez a cikk bemutatja, hogyan állíthat be egy FSLogix az Office-ban.

## <a name="requirements"></a>Követelmények

A következő dolgokra lesz szüksége a szabálygyűjtemény beállításához:

- Windows rendszerű virtuális gép, amelyen nincs telepítve az Office
- az Office egy példánya
- az üzembe helyezésre telepített FSLogix másolata
- egy hálózati megosztás, amelyet a gazdagép összes virtuális gépe csak olvasási hozzáféréssel rendelkezik

## <a name="install-office"></a>Az Office telepítése

Ha az Office-t virtuális merevlemezre vagy VHDX szeretné telepíteni, engedélyezze a RDP protokollt a virtuális gépen, majd kövesse az [Office telepítése VHD-főrendszerképben](install-office-on-wvd-master-image.md)című témakör utasításait. A telepítésekor ellenőrizze, hogy [a megfelelő licenceket](overview.md#requirements)használja-e.

>[!NOTE]
>A Windows virtuális asztal használatához meg kell osztani a számítógép-aktiválást (SCA).

## <a name="install-fslogix"></a>A FSLogix telepítése

A FSLogix és a szabály szerkesztőjének telepítéséhez kövesse a [FSLogix letöltése és telepítése](/fslogix/install-ht)című témakör útmutatását.

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Virtuális merevlemez létrehozása és előkészítése az Office tárolásához

Ezután létre kell hoznia és elő kell készítenie egy VHD-rendszerképet a Kódszerkesztő használatához a következőn:

1. Nyisson meg egy parancssort rendszergazdaként. és futtassa a következő parancsot:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Ügyeljen arra, hogy a parancsban látható üres szóközöket ne használja.

2. Ezután futtassa a következő parancsot:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Ha megtalálta a szolgáltatást, indítsa újra a virtuális gépet, mielőtt folytatná a 3. lépést.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Ezután nyissa meg a **Program Files**  >  **FSLogix**  >  **apps alkalmazást** , és futtassa a következő parancsot a cél VHD létrehozásához:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    A paranccsal létrehozott VHD-fájlnak tartalmaznia kell a C: \\ Program Files \\ Microsoft Office mappát.

    >[!NOTE]
    >Ha bármilyen hibát tapasztal, távolítsa el az Office-t, és kezdje el az 1. lépéssel.

## <a name="configure-the-rule-editor"></a>A szabály-szerkesztő konfigurálása

Most, hogy előkészítette a rendszerképet, konfigurálnia kell a szabály-szerkesztőt, és létre kell hoznia egy fájlt a szabályok tárolásához a alkalmazásban.

1. Nyissa meg a **Program Files**  >  **FSLogix**  >  **alkalmazásokat** , és futtassa a **RuleEditor.exe**.

2. Válassza a **fájl**  >  **új**  >  **Létrehozás** lehetőséget, hogy új szabálykészlet legyen létrehozva, majd mentse a szabályt egy helyi mappába.

3. Válassza az **üres szabálykészlet** lehetőséget, majd kattintson **az OK gombra**.

4. Válassza az **+** gombot. Ekkor megnyílik a **szabály hozzáadása** ablak. Ez megváltoztatja a beállításokat a **szabály hozzáadása** párbeszédpanelen.

5. A legördülő menüben válassza az alkalmazás- **tároló (VHD) szabályt**.

6. Írja be a **C: \\ Program Files \\ Microsoft Office** **mappát a mappa** mezőbe.

7. A **lemez fájl** mezőjénél válassza az **\<path\> \\ Office. vhd** elemet a **cél VHD létrehozása** szakaszban.

8. Válassza az **OK** lehetőséget.

9. Nyissa meg a munkamappát a **C: \\ Users \\ \<username\> \\ Document \\ FSLogix** , és keresse meg a. FRx és a. fxa fájlokat. Ezeket a fájlokat át kell helyeznie a szabályok mappába a **C: \\ Program Files \\ FSLogix \\ alkalmazási \\ szabályokban** , hogy a szabályok megkezdődjön.

10. Válassza a **szabályok alkalmazása a rendszerre** lehetőséget a szabályok érvénybe léptetéséhez.

     >[!NOTE]
     > Az alkalmazás-szabály fájljait az összes munkamenet-gazdagépre alkalmaznia kell.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a FSLogix-ről, tekintse meg a [FSLogix dokumentációját](/fslogix/).