---
title: Eszközadatok importálása
description: A Defender for IoT érzékelők figyelik és elemzik a tükrözött forgalmat. Ezekben az esetekben előfordulhat, hogy adatokat szeretne importálni a már észlelt eszközökre vonatkozó gazdagító információkra.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: eb82a3e5d360913acfead06adc03194d80f7fccb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785630"
---
# <a name="import-device-information-to-a-sensor"></a>Eszköz adatainak importálása az érzékelőbe

Egy Azure Defender for IoT érzékelő figyeli és elemzi a tükrözött forgalmat. Bizonyos esetekben a szervezetre jellemző hálózati konfigurációs házirendek miatt előfordulhat, hogy egyes információk nem lesznek továbbítva.

Ezekben az esetekben előfordulhat, hogy adatokat szeretne importálni a már észlelt eszközökre vonatkozó gazdagító információkra. Két lehetőség érhető el az adatok érzékelőkbe történő importálásához:

- **Importálás a térképről**: frissítse az eszköz nevét, típusát, csoportját vagy a Purdue réteget a térképre.

- **Importálás importálási beállításokból**: eszköz operációs rendszerének, IP-címének, javítási szintjének vagy engedélyezési állapotának importálása.

## <a name="import-from-the-map"></a>Importálás a térképről

Ez a szakasz azt ismerteti, hogyan importálhatja az eszközök nevét, típusait, csoportjait vagy Purdue-rétegeit az eszköz térképére. Ezt a térképen teheti meg.

Az importálási követelmények az alábbiak:

- **Nevek**: legfeljebb 30 karakter hosszúságú lehet.

- **Típus** vagy **Purdue-réteg**: használja az **eszköz tulajdonságai** párbeszédpanelen megjelenő beállításokat. (Kattintson a jobb gombbal az eszközre, és válassza a **tulajdonságok megtekintése** lehetőséget.)

- **Eszközcsoport**: hozzon létre egy új, legfeljebb 30 karakterből álló csoportot. 

> [!NOTE]
> Az ütközések elkerülése érdekében ne importálja az egyik érzékelőből egy másik érzékelőbe exportált adatokból.

Importálás:

1. Az oldalsó menüben válassza az **eszközök** lehetőséget.

2. Az **eszközök** ablak jobb felső sarkában válassza a elemet :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Képernyőfelvétel az eszköz ablakáról.":::

3. Válassza az **eszközök exportálása** lehetőséget. Az exportált fájlban az információk széles köre jelenik meg. Ezek az információk az eszköz által használt protokollok, valamint az eszköz engedélyezési állapotát tartalmazzák.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Az exportált fájlban található információk.":::

4. A CSV-fájlban módosítsa a csak az eszköz nevét, típusát, csoportját és a Purdue réteget. Ezután mentse a fájlt. 

   Az exportált fájlban látható tőkésítő szabványok használata. A Purdue réteg esetében például az összes első betűs kezdőbetű használata.

5. Az **eszköz** ablak **Importálás/exportálás** legördülő menüjében válassza az **eszközök importálása** lehetőséget.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Eszközök importálása az eszköz ablakán keresztül.":::

6. Válassza az **eszközök importálása** lehetőséget, és válassza ki az IMPORTÁLNI kívánt CSV-fájlt. Az importálási állapotüzenetek mindaddig megjelennek a képernyőn, amíg az **eszközök importálása** párbeszédpanel be nem zárul.

## <a name="import-from-import-settings"></a>Importálás importálási beállításokból

Ez a szakasz azt ismerteti, hogyan importálható az eszköz IP-címe, az operációs rendszer, a javítási szint vagy az engedélyezési állapot az eszköz térképére. Ezt a **Beállítások importálása** párbeszédpanelen teheti meg.

Az IP-cím, az operációs rendszer és a javítás szintjének importálása:

1. Töltse le a [devices_info_2.2.8 és up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) fájlt a [Súgó központjából](https://cyberx-labs.zendesk.com/hc/en-us) , és adja meg az adatokat a következő módon:

   - **IP-cím**: adja meg az eszköz IP-címét.

   - **Operációs rendszer**: válasszon a legördülő listából.

   - **Utolsó frissítés**: az ÉÉÉÉ-HH-NN formátumot használja.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="A beállítások képernyő.":::

2. Az oldalsó menüben válassza a **Beállítások importálása** lehetőséget.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Importálja a beállításokat.":::

3. A szükséges konfiguráció feltöltéséhez az **eszköz adatai** szakaszban válassza a **Hozzáadás** lehetőséget, és töltse fel az előkészített CSV-fájlt.

Az engedélyezési állapot importálása:

1. Töltse le és mentse a [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) fájlt a Defender for IoT súgójában. Ellenőrizze, hogy mentette-e a fájlt CSV-fájlként.

2. Adja meg az adatokat a következőképpen:

   - **IP-cím**: az eszköz IP-címe.

   - **Name (név**): a hitelesítő eszköz neve. Győződjön meg arról, hogy a nevek pontosak. Az importált listában szereplő eszközökhöz megadott nevek az eszköz térképén látható felülírási nevek.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Az importált eszközök listáját tartalmazó Excel-fájlok.":::

3. Az oldalsó menüben válassza a **Beállítások importálása** lehetőséget.

4. A **jogosultsággal rendelkező eszközök** szakaszban válassza a **Hozzáadás** lehetőséget, és töltse fel a mentett CSV-fájlt.

Az adatok importálásakor a rendszer riasztást küld az összes olyan eszközről, amely nem szerepel ezen a listán.

## <a name="import-device-information-to-the-sensor"></a>Eszköz adatainak importálása az érzékelőbe

Az érzékelő figyeli és elemzi a tükrözött forgalmat. Bizonyos esetekben a szervezetre jellemző hálózati konfigurációs házirendek miatt előfordulhat, hogy egyes információk nem lesznek továbbítva.

Ezekben az esetekben előfordulhat, hogy a már észlelt eszközökön adatokat szeretne importálni az eszközre. Két lehetőség érhető el az adatok érzékelőkbe történő importálásához:

- **Importálás a térképről**: frissítse az eszköz nevét, típusát, csoportját vagy a Purdue réteget a térképre.

- **Importálás importálási beállításokból**: eszköz operációs rendszerének, IP-címének, javítási szintjének vagy engedélyezési állapotának importálása.

### <a name="import-from-the-map"></a>Importálás a térképről

Ez a szakasz azt ismerteti, hogyan importálhatja az eszközök nevét, típusait, csoportjait vagy Purdue-rétegeit az eszköz térképére. Ezt a térképen teheti meg.

Az importálási követelmények az alábbiak:

- **Nevek**: legfeljebb 30 karakter hosszúságú lehet.

- **Típus** vagy **Purdue-réteg**: használja az **eszköz tulajdonságai** párbeszédpanelen megjelenő beállításokat. (Kattintson a jobb gombbal az eszközre, és válassza a **tulajdonságok megtekintése** lehetőséget.)

- **Eszközcsoport**: hozzon létre egy új, legfeljebb 30 karakterből álló csoportot. 

> [!NOTE]
> Az ütközések elkerülése érdekében ne importálja az egyik érzékelőből egy másik érzékelőbe exportált adatokból.

Importálás:

1. Az oldalsó menüben válassza az **eszközök** lehetőséget.

2. Az **eszközök** ablak jobb felső sarkában válassza a elemet :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="Az az ablak, amelyből ki szeretné választani az eszközt.":::

3. Válassza az **eszközök exportálása** lehetőséget. Az exportált fájlban az információk széles köre jelenik meg. Ilyenek például az eszköz által használt protokollok és az eszköz engedélyezési állapota.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="Az exportált fájlban található információk.":::

4. A CSV-fájlban csak az eszköz nevét, típusát, csoportját és a Purdue réteget kell módosítania. Ezután mentse a fájlt. 

   Az exportált fájlban látható tőkésítő szabványok használata. A Purdue réteg esetében például az összes első betűs kezdőbetű használata.

5. Az **eszköz** ablak **Importálás/exportálás** legördülő menüjében válassza az **eszközök importálása** lehetőséget.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="Importálja az eszközöket.":::

6. Válassza az **eszközök importálása** lehetőséget, és válassza ki az IMPORTÁLNI kívánt CSV-fájlt. Az importálási állapotüzenetek mindaddig megjelennek a képernyőn, amíg az **eszközök importálása** párbeszédpanel be nem zárul.

### <a name="import-from-import-settings"></a>Importálás importálási beállításokból 

Ez a szakasz azt ismerteti, hogyan importálható az eszköz IP-címe, az operációs rendszer, a javítási szint vagy az engedélyezési állapot az eszköz térképére. Ezt a **Beállítások importálása** párbeszédpanelen teheti meg.

Az IP-cím, az operációs rendszer és a javítás szintjének importálása:

1. Töltse le a [devices_info_2.2.8 és up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) fájlt a [Súgó központjából](https://cyberx-labs.zendesk.com/hc/en-us) , és adja meg az adatokat a következő módon:

   - **IP-cím**: az eszköz IP-címe.

   - **Operációs rendszer**: válasszon a legördülő listából.

   - **Utolsó frissítés dátuma**: az ÉÉÉÉ-HH-NN formátumot használja.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="A képernyőn megjelenő tartalom.":::

2. Az oldalsó menüben válassza a **Beállítások importálása** lehetőséget.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="Töltse ki az importálási beállítások képernyőt.":::

3. A szükséges konfiguráció feltöltéséhez az **eszköz adatai** szakaszban válassza a **Hozzáadás** lehetőséget, és töltse fel az előkészített CSV-fájlt.

Az engedélyezési állapot importálása:

1. Töltse le és mentse a [authorized_devices-examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) fájlt a Defender for IoT súgójában. Ellenőrizze, hogy mentette-e a fájlt CSV-fájlként.

2. Adja meg az adatokat a következőképpen:

   - **IP-cím**: az eszköz IP-címe.

   - **Name (név**): a hitelesítő eszköz neve. Ellenőrizze, hogy a nevek pontosak-e. Az importált listában szereplő eszközökhöz megadott nevek az eszköz térképén látható felülírási nevek.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="Az importálási lista az eszköz térképéhez.":::

3. Az oldalsó menüben válassza a **Beállítások importálása** lehetőséget.

4. A **jogosultsággal rendelkező eszközök** szakaszban válassza a **Hozzáadás** lehetőséget, és töltse fel a mentett CSV-fájlt.

Az adatok importálásakor a rendszer riasztást küld az összes olyan eszközről, amely nem szerepel ezen a listán.

## <a name="see-also"></a>Lásd még

[A monitorozandó forgalom szabályozása](how-to-control-what-traffic-is-monitored.md)

[Egy eszközkészlet érzékelői általi észlelések vizsgálata](how-to-investigate-sensor-detections-in-a-device-inventory.md)
