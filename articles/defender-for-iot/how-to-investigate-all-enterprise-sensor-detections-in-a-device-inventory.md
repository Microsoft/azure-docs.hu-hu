---
title: Az összes vállalati érzékelők által felderített eszközök megismerése
description: A helyszíni felügyeleti konzolon az eszközök leltárát használva átfogó képet kaphat az eszközről a csatlakoztatott érzékelőkről. Az adatok kezeléséhez használjon importálási, exportálási és szűrési eszközöket.
ms.date: 12/02/2020
ms.topic: how-to
ms.openlocfilehash: 0ae59123b59cfb54cba2a2ee9bdeefb411c8793b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782180"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Az eszközök leltárában szereplő összes vállalati érzékelő észlelésének vizsgálata

A csatlakoztatott érzékelőkről a helyszíni felügyeleti konzolon található *eszközök leltározásával* tekinthet meg információkat. Ez a funkció átfogó áttekintést nyújt az összes hálózati információról. Az adatok kezeléséhez használjon importálási, exportálási és szűrési eszközöket. Megjelenik a csatlakoztatott érzékelő verziójára vonatkozó állapotadatok is.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Képernyőfelvétel az eszköz leltározási táblázatáról.":::

Az alábbi táblázat az eszközök leltárában szereplő táblázat oszlopokat ismerteti.

| Paraméter | Leírás |
|--|--|
| **Nem nyugtázott riasztások** | Az eszközhöz társított nem kezelt riasztások száma. |
| **Üzleti egység** | Az eszközt tartalmazó üzleti egység. |
| **Régió** | Az eszközt tartalmazó régió. |
| **Hely** | Az eszközt tartalmazó hely. |
| **Zóna** | Az eszközt tartalmazó zóna. |
| **Berendezés** | Az eszközt védő Azure Defender for IoT-érzékelő. |
| **Név** | Az eszköz neve, mint Defender a IoT felderített. |
| **Típus** | Az eszköz típusa, például PLC vagy HMI. |
| **Szállító** | Az eszköz szállítójának neve a MAC-címben megadott módon. |
| **Operációs rendszer** | Az eszköz operációs rendszere. |
| **Belső vezérlőprogram** | Az eszköz belső vezérlőprogram. |
| **IP-cím** | Az eszköz IP-címe. |
| **VLAN** | Az eszköz VLAN-a. |
| **MAC-címe** | Az eszköz MAC-címe. |
| **Protokollok** | Az eszköz által használt protokollok. |
| **Nem nyugtázott riasztások** | Az eszközhöz társított nem kezelt riasztások száma. |
| **Engedélyezve van** | Az eszköz engedélyezési állapota:<br />- **True (igaz**): az eszköz engedélyezve van.<br />- **Hamis**: az eszköz nincs engedélyezve. |
| **Ismert képolvasó** | Azt határozza meg, hogy az eszköz végez-e vizsgálathoz hasonló tevékenységeket a hálózaton. |
| **Programozási eszköz** | Legyen szó programozási eszközről:<br />- **True (igaz**): az eszköz programozási tevékenységeket végez a PLC, a RTUs és a vezérlőkön, amelyek a mérnöki állomásokra vonatkoznak.<br />- **Hamis**: az eszköz nem programozási eszköz. |
| **Csoportok** | Azok a csoportok, amelyekben ez az eszköz részt vesz. |
| **Legutóbbi tevékenység** | Az eszköz által végrehajtott utolsó tevékenység. |
| **Felderített** | Amikor az eszköz először a hálózatban volt látható. |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Az adategységek integrálása a vállalati eszközök leltárba

Az adatintegrációs funkciók lehetővé teszik, hogy az eszközök leltárában lévő adatokat más vállalati erőforrásokból származó adatokkal fejlessze. Ezek a források többek között a CMDBs, a DNS, a tűzfalak és a webes API-k.

Ezt az információt a következő témakörben olvashatja:. Például:

- Az eszközök vásárlási dátumai és a jótállás végének dátuma

- Az egyes eszközökért felelős felhasználók

- Nyitott jegyek az eszközökhöz

- A belső vezérlőprogram frissítésének utolsó dátuma

- Engedélyezett eszközök hozzáférése az internethez

- Aktív víruskereső alkalmazásokat futtató eszközök

- Az eszközökre bejelentkezett felhasználók

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Adattábla az eszköz leltározási képernyőjén.":::

Az adatintegrációt az alábbiak szerint végezheti el:

- Manuális Hozzáadás

- A IoT Defender által biztosított testreszabott parancsfájlok futtatása

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="A vállalati adatintegrátor diagramja.":::

A Defender for IoT technikai támogatási szolgálatának használatával a rendszer beállíthatja a webes API-lekérdezések fogadását.

Az adatfelvétel kézzel:

1. Az oldalsó menüben válassza az **eszközök leltározása** lehetőséget, majd válassza a lehetőséget :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Szerkessze az eszköz leltározási beállításait.":::

2. Az **eszköz leltározási beállításai** párbeszédpanelen válassza az **Egyéni oszlop hozzáadása** lehetőséget.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Egyéni oszlop hozzáadása a leltárhoz.":::

3. Az **Egyéni oszlop hozzáadása** párbeszédpanelen adja hozzá az új oszlop nevét (legfeljebb 250 karakter UTF-ban), válassza a **manuális** lehetőséget, majd kattintson a **Mentés** gombra. Az új elem megjelenik az **eszköz leltározási beállításai** párbeszédpanelen.

4. Az **eszközök leltára** ablak jobb felső sarkában válassza ki :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: és válassza a **minden eszköz leltár exportálása** lehetőséget. Létrejön a CSV-fájl.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Az exportált CSV-fájl.":::

5. Manuálisan adja hozzá az adatokat az új oszlophoz, és mentse a fájlt.

6. Az **eszközök leltára** ablak jobb felső sarkában válassza a lehetőséget :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: , válassza a **manuális beviteli oszlopok importálása** lehetőséget, és keresse meg a CSV-fájlt. Az új adattábla megjelenik az **eszköz leltározási** táblájában.

Más vállalati entitásokból származó adatok integrálása:

1. Az **eszközök leltára** ablak jobb felső sarkában válassza ki :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false"::: és válassza a **minden eszköz leltár exportálása** lehetőséget.

2. Az **eszköz leltározási beállításai** párbeszédpanelen válassza az **Egyéni oszlop hozzáadása** lehetőséget.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Egyéni oszlop hozzáadása a leltárhoz.":::

3. Az **Egyéni oszlop hozzáadása** párbeszédpanelen adja meg az új oszlop nevét (legfeljebb 250 karakter UTF-ként), majd válassza az **automatikus** lehetőséget. Megjelenik a **parancsfájl feltöltése** és a **tesztelési parancsfájl** beállításai.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Egyéni oszlopok automatikus hozzáadása.":::

4. Töltse fel és tesztelje a [Microsoft ügyfélszolgálatatól](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)kapott parancsfájlt.

## <a name="retrieve-information-from-the-device-inventory"></a>Adatok lekérése az eszköz leltározásáról

Lekérheti a felügyelt érzékelők által észlelt eszközök széles körét, és integrálhatja ezeket az információkat a partneri rendszerekkel. Lekérheti például az érzékelőt, a zónát, a hely AZONOSÍTÓját, az IP-címet, a MAC-címet, a belső vezérlőprogram, a protokoll és a szállító adatait. A lekért információk szűrése a következő alapján:

- Engedélyezett és jogosulatlan eszközök.

- Adott helyekhez társított eszközök.

- Adott zónákhoz társított eszközök.

- Adott érzékelőkhöz társított eszközök.

A Defender használata a IoT API-parancsokkal az információk lekéréséhez és integrálásához. További információ: [Defender for IOT API-érzékelő és felügyeleti konzol API](references-work-with-defender-for-iot-apis.md)-k.

## <a name="filter-the-device-inventory"></a>Az eszköz leltározásának szűrése

Az eszközök leltározását szűrheti a fontos oszlopok megjelenítéséhez. Például megtekintheti a PLC-eszközök adatait.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Képernyőfelvétel az eszközök leltáráról.":::

Ha elhagyja az ablakot, a szűrő törlődik.

Ha többször is ugyanazt a szűrőt szeretné használni, mentheti a szűrőket vagy a szükséges szűrők kombinációját. Megnyithatja a bal oldali ablaktáblát, és megtekintheti a mentett szűrőket:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Eszközök leltározása képernyő.":::

Az eszköz leltározásának szűrése:

1. A szűrni kívánt oszlopban válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: .

2. A **Filter (szűrő** ) párbeszédpanelen válassza ki a szűrő típusát:

   - **Equals (egyenlő**): az a pontos érték, amelynek alapján szűrni kívánja az oszlopot. Ha például a protokoll oszlopot az **egyenlő** és a érték alapján szűri `value=ICMP` , akkor az oszlop csak az ICMP protokollt használó eszközöket fogja bemutatni.

   - **Tartalmazza**: az oszlop más értékei között található érték. Ha például a protokoll oszlopot **a és a érték alapján** szűri `value=ICMP` , akkor az oszlopban az ICMP protokollt használó eszközök jelennek meg az eszköz által használt protokollok listájának részeként.

3. Az oszlop adatainak betűrendbe rendezéséhez válassza a elemet :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: . Rendezze a sorrendet a és a nyilak kiválasztásával :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: .

4. Új szűrő mentéséhez adja meg a szűrőt, és válassza a **Mentés másként** lehetőséget.

5. A szűrési definíciók módosításához módosítsa a definíciókat, majd válassza a **módosítások mentése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

[Egy eszközkészlet érzékelői általi észlelések vizsgálata](how-to-investigate-sensor-detections-in-a-device-inventory.md)
