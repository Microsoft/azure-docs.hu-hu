---
title: Betekintést nyerhet egy adott érzékelő által felderített eszközökbe
description: Az eszköz leltára az eszköz által észlelt attribútumok széles körét jeleníti meg.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 9dc6849c1b2ad5daa6142e894e36f351663ff9bd
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523991"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>Egy eszközkészlet érzékelői általi észlelések vizsgálata

Az eszköz leltára az eszköz által észlelt attribútumok széles körét jeleníti meg. A következő lehetőségek érhetők el:

 - Egyszerűen szűrheti az adatokat.

 - Adatok exportálása CSV-fájlba.

 - Importálja a Windows beállításjegyzékének részleteit.

 - Csoportok létrehozása az eszköz térképének megjelenítéséhez.

## <a name="view-device-attributes-in-the-device-inventory"></a>Eszköz attribútumainak megtekintése az eszköz leltárában

A következő attribútumok jelennek meg az eszköz leltározási táblájában.

| Paraméter | Leírás |
|--|--|
| Név | Az eszköz neve, amelyet az érzékelő észlelt, vagy a felhasználó által megadott módon. |
| Típus | Az eszköz típusa az érzékelő által meghatározott módon, vagy a felhasználó által megadott módon. |
| Szállító | Az eszköz szállítójának neve a MAC-címben megadott módon. |
| Operációs rendszer | Az eszköz operációs rendszere, ha észlelve van. |
| Belső vezérlőprogram verziója | Az eszköz belső vezérlőprogramja, ha észlelt. |
| IP-cím | Annak az eszköznek az IP-címe, ahol a definiálva van. |
| VLAN | Az eszköz VLAN-a. A VLAN-ok felderítésével kapcsolatos utasításokért lásd: [VLAN-nevek meghatározása](how-to-manage-the-on-premises-management-console.md#define-vlan-names). (útmutató – define-Management-Console-Network-Settings. MD # define-VLAN-Names). |
| MAC-cím | Az eszköz MAC-címe. |
| Protokollok | Az eszköz által használt protokollok. |
| Nem nyugtázott riasztások | Az eszközhöz társított nem visszaigazolt riasztások száma. |
| Engedélyezve van | A felhasználó által meghatározott engedélyezési állapot:<br />- **True (igaz**): az eszköz engedélyezve van.<br />- **Hamis**: az eszköz nincs engedélyezve. |
| Ismert képolvasó | A felhasználó által hálózati ellenőrzési eszközként van meghatározva. |
| Programozási eszköz | A felhasználó által jóváhagyott programozási eszközként van meghatározva. <br />- **True (igaz**): az eszköz programozási tevékenységeket végez a PLC, a RTUs és a vezérlőkön, amelyek a mérnöki állomásokra vonatkoznak. <br />- **Hamis**: az eszköz nem programozási eszköz. |
| Csoportok | Azok a csoportok, amelyekben ez az eszköz részt vesz. |
| Legutóbbi tevékenység | Az eszköz által végrehajtott utolsó tevékenység. |
| Discovered | Amikor az eszköz először a hálózatban volt látható. |

Az eszköz leltározásának megtekintése:

1. A bal oldali panelen válassza az **eszközök** elemet. Az **eszközök** panel a jobb oldalon nyílik meg.

2. Az **eszközök** ablaktáblán válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false"::: .

Az oszlopok elrejtéséhez és megjelenítéséhez szabja testre az eszköz leltározási tábláját:

1. Az eszközök leltárának jobb felső menüjében válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="Eszköz leltározási beállításai képernyő.":::

2. Az **eszközbeállítások beállításai** ablakban válassza ki azokat az oszlopokat, amelyeket meg szeretne jeleníteni az eszköz leltározási táblájában.

3. Módosítsa a tábla oszlopainak helyét a nyilak használatával.

4. Kattintson a **Mentés** gombra. Az **eszköz leltározási beállításai** ablak bezárul, és az új beállítások megjelennek a táblázatban.

### <a name="create-temporary-device-inventory-filters"></a>Ideiglenes eszközök leltározási szűrőinek létrehozása

Beállíthat egy szűrőt, amely meghatározza a tábla által megjelenített információkat. Dönthet például úgy, hogy csak a PLC-eszköz adatait szeretné megtekinteni.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="Eszközök tanulása.":::

A rendszer nem menti a szűrőt, amikor elhagyja a leltárt.

### <a name="save-device-inventory-filters"></a>Eszköz leltározási szűrőinek mentése

Mentheti a szükséges szűrőket vagy szűrők kombinációját, és újraalkalmazhatja őket az eszköz leltározásakor. Hozzon létre szélesebb szűrőket egy adott eszköz típusától, vagy egy adott típuson és egy adott protokollon alapuló szűkebb szűrők alapján.

A mentett szűrők az eszköz-hozzárendelési csoportokként is mentve lesznek. Ez a funkció további részletességi szintet biztosít a hálózati eszközök megjelenítéséhez a térképen.

Szűrők létrehozása:

1. A szűrni kívánt oszlopban válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false"::: .

2. A **Filter (szűrő** ) párbeszédpanelen válassza ki a szűrő típusát:

   - **Equals (egyenlő**): az a pontos érték, amelynek alapján szűrni kívánja az oszlopot. Ha például a protokoll oszlopot az **egyenlő** és a érték alapján szűri `value=ICMP` , akkor az oszlop csak az ICMP protokollt használó eszközöket fogja bemutatni.

   - **Tartalmazza**: az oszlop más értékei között található érték. Ha például a protokoll oszlopot **a és a érték alapján** szűri `value=ICMP` , akkor az oszlopban az ICMP protokollt használó eszközök jelennek meg az eszköz által használt protokollok listájának részeként.

3. Az oszlop adatainak betűrendbe rendezéséhez válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Rendezze a sorrendet a és a nyilak kiválasztásával :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Új szűrő mentéséhez adja meg a szűrőt, és válassza a **Mentés másként** lehetőséget.

5. A szűrési definíciók módosításához módosítsa a definíciókat, majd válassza a **módosítások mentése** lehetőséget.

Szűrők megtekintése:

- Nyissa meg a bal oldali ablaktáblát, és tekintse meg a mentett szűrőket:

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="A szűrőket a bal oldali ablaktáblán tekintheti meg.":::

### <a name="view-filtered-information-as-a-map-group"></a>Szűrt adatok megtekintése térképi csoportként

Amikor átvált a Térkép nézetre, a szűrt eszközök ki vannak emelve és szűrhetők. Az **eszköz leltározási** csoportjának oldalsó menüjében a mentett szűrő csoport jelenik meg.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="Szűrők megtekintése a Térkép nézetben.":::

## <a name="learn-windows-registry-details"></a>A Windows beállításjegyzék részleteinek megismerése

Az eszközök eszközön kívül a Microsoft Windows-munkaállomásokat és-kiszolgálókat is felderítheti. Ezek az eszközök az eszközök leltárában is megjelennek. Az eszközök megismerése után a részletes Windows-információkkal bővítheti az eszközök leltárát, például:

- Windows-verzió telepítve

- Telepített alkalmazások

- Javítási szintű információk

- Portok megnyitása

- Robusztusabb információk az operációs rendszer verziójáról

Az adatok lekéréséhez két lehetőség közül választhat:

- Aktív lekérdezés ütemezett WMI-vizsgálatok használatával. 

- Helyi felmérés egy parancsfájlnak az eszközön való elosztásával és futtatásával. A helyi parancsfájlok használata megkerüli a WMI-lekérdezések végponton való futtatásának kockázatait. Emellett a vízesésekkel és egyirányú elemekkel rendelkező szabályozott hálózatok esetében is hasznos.

Ez a cikk azt ismerteti, hogyan lehet helyileg megvizsgálni a Windows-végpont beállításjegyzékét egy parancsfájl használatával. Ezek az adatok a riasztások, értesítések, adatbányászati jelentések, kockázatértékelések és a támadási vektoros jelentések generálására szolgálnak.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="Adatbányászati képernyőkép.":::

A következő Windows operációs rendszereket végezheti el:

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>Előkészületek

A szkripttel való együttműködéshez a következő követelményeknek kell megfelelnie:

- A parancsfájlnak az eszközön való futtatásához rendszergazdai jogosultságok szükségesek.

- Az érzékelőnek már megtanulta a Windows-eszközt. Ez azt jelenti, hogy ha az eszköz már létezik, a parancsfájl lekéri az adatokat.

- Az érzékelő figyeli azt a hálózatot, amelyhez a Windows rendszerű számítógép csatlakozik.

### <a name="acquire-the-script"></a>A parancsfájl megszerzése

A parancsfájl fogadásához [forduljon az ügyfélszolgálathoz](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>A parancsfájl üzembe helyezése

A parancsfájlt telepítheti egyszer, vagy ütemezhet a folyamatban lévő lekérdezéseket szabványos automatikus telepítési módszerek és eszközök használatával.

### <a name="about-the-script"></a>Tudnivalók a parancsfájlról

- A parancsfájl segédprogramként fut, és nem telepített program. A parancsfájl futtatása nem befolyásolja a végpontot.

- A parancsfájl által létrehozott fájlok addig maradnak a helyi meghajtón, amíg nem törli őket.

- A parancsfájl által generált fájlok egymás mellett találhatók. Ne válassza el őket.

- Ha ugyanezen a helyen futtatja újra a parancsfájlt, a rendszer felülírja ezeket a fájlokat.

A szkript futtatása:  

1. Másolja a szkriptet egy helyi meghajtóra, és csomagolja ki. A következő fájlok jelennek meg:

    - start.bat

    - settings.jsbekapcsolva

    - adattároló

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="A Fájlkezelőben található fájlok megtekintése.":::

2. Futtassa a `run.bat` fájlt.

3. A beállításjegyzék kivizsgálása után a CX-Snapshot fájl jelenik meg a beállításjegyzék adataival.

4. A fájl neve a pillanatkép rendszernevét és dátumát és időpontját jelzi. Példa fájlnév `CX-snaphot_SystemName_Month_Year_Time` .

### <a name="import-device-details"></a>Eszköz adatainak importálása

Az egyes végpontokon megszerzett információkat az érzékelőbe kell importálni.

A lekérdezésekből létrehozott fájlok egy olyan mappába helyezhetők, amelyet az érzékelőkből lehet elérni. Szabványos, automatizált metódusok és eszközök használatával helyezze át a fájlokat az egyes Windows-végpontokról arra a helyre, ahová az érzékelőbe importálja őket.

Ne frissítse a fájlneveket.

Importálás:

1. Válassza a **Beállítások importálása** lehetőséget a **Windows-konfiguráció importálása** párbeszédpanelen.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Importálja a Windows-konfigurációkat.":::

2. Válassza a **Hozzáadás** lehetőséget, majd jelölje ki az összes fájlt (CTRL + A).

3. Válassza a **Bezárás** gombot. Az eszköz beállításjegyzék-információit a rendszer importálja. Ha probléma merül fel az egyik fájl feltöltésekor, a rendszer tájékoztat arról, hogy a fájlfeltöltés nem sikerült.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="A hozzáadott fájlok feltöltése sikerült.":::

## <a name="export-device-inventory-information"></a>Eszköz leltározási adatainak exportálása

Az eszköz leltározási adatait Excel-fájlba is exportálhatja.

CSV-fájl exportálása:

- Az eszközök leltárának jobb felső menüjében válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false"::: . A CSV-jelentés létrehozása és letöltése megtörténik.

## <a name="next-steps"></a>Következő lépések

[Egy eszközkészlet összes vállalati érzékelője általi észlelések vizsgálata](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[Webhelyszerkezet-nézetek használata](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
