---
title: Érzékelők kezelése a helyszíni felügyeleti konzolról
description: Megtudhatja, hogyan kezelheti az érzékelőket a felügyeleti konzolról, beleértve az érzékelők verzióinak frissítését, a rendszerbeállítások az érzékelőkre való leküldését, valamint az érzékelőkön futó motorok engedélyezését
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 10051bfdf7a93a8d6c83d649a7b0ad008b5e2caf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523957"
---
# <a name="manage-sensors-from-the-management-console"></a>Érzékelők kezelése a felügyeleti konzolról

Ez a cikk bemutatja, hogyan kezelhetők az érzékelők a felügyeleti konzolról, beleértve a következőket:

- Rendszerbeállítások leküldése érzékelőkre

- Motorok engedélyezése és letiltása érzékelőkön

- Az érzékelő verzióinak frissítése

## <a name="push-configurations"></a>Leküldéses konfigurációk

Megadhat különböző rendszerbeállításokat, és automatikusan alkalmazhatja azokat a felügyeleti konzolhoz csatlakozó érzékelőkre. Ezzel időt takaríthat meg, és segít a nagyvállalati érzékelőkben lévő, korszerű beállítások biztosításában.

A következő szenzorrendszer-beállításokat adhatja meg a felügyeleti konzolon:

- Levelezési kiszolgáló

- SNMP MIB-figyelés

- Active Directory

- DNS-beállítások

- Alhálózatok

- Port aliasok

A rendszerbeállítások alkalmazása:

1. A konzol bal oldali ablaktábláján válassza a **Rendszerbeállítások** elemet.

2. Az **érzékelők konfigurálása** panelen válassza ki az egyik lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Egy érzékelő rendszerbeállítási beállításai.":::

   Az alábbi példa leírja, hogyan határozhatja meg a levelezési kiszolgáló paramétereit a vállalati érzékelőkhöz.

3. Válassza a **levelezési kiszolgáló** lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Válassza ki a levelezési kiszolgálót a rendszerbeállítások képernyőn.":::

4. Válasszon ki egy érzékelőt a bal oldalon.

5. Állítsa be a levelezési kiszolgáló paramétereit, és válassza az **Ismétlődés** lehetőséget. Az érzékelő faszerkezetének minden eleme megjelenik egy jelölőnégyzet melletti jelölőnégyzettel.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Győződjön meg arról, hogy a jelölőnégyzetek ki vannak választva az érzékelőkhöz.":::

6. Az érzékelő fán válassza ki azokat az elemeket, amelyekre alkalmazni kívánja a konfigurációt.

7. Kattintson a **Mentés** gombra.

## <a name="update-versions"></a>Frissítési verziók

A helyszíni felügyeleti konzolról egyszerre több érzékelőt is frissíthet.

Több érzékelő frissítése:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

2. Nyissa meg az Azure Defender IoT.

3. Nyissa meg a **frissítések** lapot.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Képernyőkép a frissítések irányítópult nézetről.":::

4. Válassza a **Letöltés** lehetőséget az **érzékelők** szakaszban, és mentse a fájlt.

5. Jelentkezzen be a felügyeleti konzolra, és válassza a **Rendszerbeállítások** lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Képernyőkép az adminisztráció menüjéről a rendszerbeállítások kiválasztásához.":::

6. Jelölje meg a frissíteni kívánt érzékelőket az **érzékelő motorjának konfigurációja** szakaszban, majd válassza az **Automatikus frissítések** lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Két érzékelő, amely a tanulási módot és az automatikus frissítéseket mutatja.":::

7. Válassza a **módosítások mentése** lehetőséget.

8. Az **érzékelők** verziófrissítése panelen válassza a elemet :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Az érzékelő verziófrissítési képernyője a fájlok megjelenítéséhez.":::

9. Megnyílik a **fájl feltöltése** párbeszédpanel. Töltse fel a letöltött fájlt a **frissítések** lapról.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="A fájl feltöltéséhez kattintson a Tallózás gombra.":::

10. A frissítési folyamat során az egyes érzékelők frissítési állapota a **hely kezelése** ablakban jelenik meg.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Figyelje meg a frissítés folyamatát.":::

## <a name="update-threat-intelligence-packages"></a>Veszélyforrások elleni intelligencia-csomagok frissítése 

A fenyegetések felderítésére szolgáló adatcsomag minden új Defender számára elérhető a IoT-verzióhoz, vagy szükség esetén a kiadások között. A csomag aláírásokat (beleértve a kártevő-aláírásokat is), a CVEs és más biztonsági tartalmakat tartalmaz. 

Ezt a fájlt manuálisan is feltöltheti a Defender for IoT portál **frissítések** lapjára, és automatikusan frissítheti az érzékelőkre. 

A veszélyforrások hírszerzési adatok frissítése: 

1. Nyissa meg a Defender for IoT **Updates** oldalt. 

2. Töltse le és mentse a fájlt.

3. Jelentkezzen be a felügyeleti konzolra. 

4. Az oldalsó menüben válassza a **Rendszerbeállítások** elemet. 

5. Válassza ki azokat az érzékelőket, amelyeknek meg kell kapniuk a frissítést az **érzékelő motorjának konfigurációs** szakaszában.  

6. A **veszélyforrások felderítése adatok kiválasztása** szakaszban válassza a pluszjelet ( **+** ). 

7. Töltse fel a letöltött csomagot a Defender for IoT **Updates** oldalára.

## <a name="understand-sensor-disconnection-events"></a>Az érzékelő leválasztási eseményeinek ismertetése

A **Site Manager** ablak megjeleníti a leválasztási adatokat, ha az érzékelők le vannak húzva a hozzárendelt helyszíni felügyeleti konzolról. A következő érzékelő leválasztási információi elérhetők:

- "A helyszíni felügyeleti konzol nem tudja feldolgozni az érzékelőtől kapott adatok feldolgozását."

- "A Times drift észlelve. A helyszíni felügyeleti konzol le lett választva az érzékelőből. "

- "Az érzékelő nem kommunikál a helyszíni felügyeleti konzollal. A hálózati kapcsolat vagy a tanúsítvány érvényesítésének ellenőrzése. "

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Képernyőkép az 1. zóna nézetről.":::

A leválasztott érzékelőkkel kapcsolatos információkat tartalmazó harmadik feleknek is küldhet riasztásokat. További információ: az [érzékelők továbbításával kapcsolatos sikertelen riasztások](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts).

## <a name="enable-or-disable-sensors"></a>Érzékelők engedélyezése vagy letiltása

Az érzékelők védelmét a IoT-motorok öt védelmezője védi. Engedélyezheti vagy letilthatja a csatlakoztatott érzékelőkhöz tartozó motorokat.

| Motor | Description | Példaforgatókönyv |
|--|--|--|
| Protokoll-megsértési motor | A protokollok megsértése akkor fordul elő, ha a csomag szerkezete vagy a mező értéke nem felel meg a protokoll specifikációjának. | "Szabálytalan MODBUS művelet (kód: nulla)" riasztás. Ez a riasztás azt jelzi, hogy egy elsődleges eszköz 0 értékű kérelmet küld egy másodlagos eszköznek. Ez a protokoll specifikációja szerint nem engedélyezett, és előfordulhat, hogy a másodlagos eszköz nem kezeli helyesen a bemenetet. |
| Házirend-megsértési motor | A szabályzat megsértése a megtanult vagy konfigurált szabályzatban meghatározott alapkonfigurációi viselkedéstől való eltéréssel történik. | "Jogosulatlan HTTP-felhasználói ügynök" riasztás. Ez a riasztás azt jelzi, hogy a szabályzat által nem megtanult vagy jóváhagyott alkalmazás HTTP-ügyfélként van használatban az eszközön. Ez lehet egy új böngésző vagy alkalmazás az eszközön. |
| Kártevő motor | A kártevő motor kártékony hálózati tevékenységet észlel. | "Rosszindulatú tevékenység gyanúja (Stuxnet)" riasztás. Ez a riasztás azt jelzi, hogy az érzékelő olyan gyanús hálózati tevékenységet észlelt, amely a Stuxnet kártevő szoftverrel kapcsolatos, ami egy fejlett, az ipari szabályozást és a SCADA hálózatokat célzó állandó fenyegetés. |
| Anomália motor | A kártevő-kezelő észleli a hálózati működés rendellenességét. | "Rendszeres viselkedés a kommunikációs csatornában" Ez egy olyan összetevő, amely ellenőrzi a hálózati kapcsolatokat, és megkeresi az adatátvitel időszakos vagy ciklikus viselkedését, ami általános az ipari hálózatokban. |
| Működési motor | Ez a motor észleli a működési incidenseket vagy az üzemzavart okozó entitásokat. | `Device is Suspected to be Disconnected (Unresponsive)` riasztás. Ez a riasztás akkor aktiválódik, ha egy eszköz nem válaszol az előre meghatározott időszakra vonatkozó kérelmekre. Előfordulhat, hogy az eszköz leállítását, leválasztását vagy meghibásodását jelzi.
|

A csatlakoztatott érzékelőkhöz tartozó motorok engedélyezése vagy letiltása:

1. A konzol bal oldali ablaktábláján válassza a **Rendszerbeállítások** elemet.

2. Az **érzékelő motor konfigurálása** szakaszban válassza a motorok **engedélyezése** vagy **letiltása** lehetőséget.
         
3. Válassza a **módosítások mentése** lehetőséget.

   Egy piros felkiáltójel jelenik meg, ha a vállalati érzékelők egyikén nem egyeznek az engedélyezett motorok. Lehetséges, hogy a motort közvetlenül az érzékelőből letiltották.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Az engedélyezett motorok nem egyeznek."::: 

## <a name="define-sensor-backup-schedules"></a>Érzékelő biztonsági mentési ütemtervének meghatározása

Az érzékelők biztonsági mentéseit ütemezheti, és igény szerinti szenzoros biztonsági másolatokat végezhet a helyszíni felügyeleti konzolról. Ez segít a merevlemez-hibák és az adatvesztés elleni védelemben.

- A biztonsági másolat készítése: konfigurációk és adathalmazok.

- A biztonsági mentés nem támogatott: PCAP-fájlok és-naplók. A PCAPs és a naplók biztonsági mentését és visszaállítását manuálisan végezheti el.

Alapértelmezés szerint az érzékelők napi 3:00-kor automatikusan biztonsági mentés készül. Az érzékelők biztonsági mentési ütemezési funkciója lehetővé teszi a biztonsági másolatok összegyűjtését és tárolását a helyszíni felügyeleti konzolon vagy egy külső biztonsági mentési kiszolgálón. A fájlok az érzékelőkről a helyszíni felügyeleti konzolra való másolása titkosított csatornán történik.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Az érzékelő biztonsági mentési képernyőjének nézete.":::

Ha az érzékelő biztonsági mentésének alapértelmezett helye megváltozik, a helyszíni felügyeleti konzol automatikusan lekéri a fájlokat az érzékelő új helyéről vagy egy külső helyről, ha a konzol jogosult a hely elérésére. 

Ha az érzékelők nincsenek regisztrálva a helyszíni felügyeleti konzolon, az **érzékelő biztonsági mentésének ütemterve** párbeszédpanel jelzi, hogy egyetlen érzékelő sincs felügyelve.  

A visszaállítási folyamat ugyanaz, függetlenül attól, hogy hol tárolják a fájlokat.

### <a name="backup-storage-for-sensors"></a>Biztonsági mentési tár érzékelőkhöz

A helyszíni felügyeleti konzollal akár kilenc biztonsági mentést is tarthat az egyes felügyelt érzékelők számára, ha a biztonsági másolatban szereplő fájlok nem lépik túl a lefoglalt maximális lemezterületet. 

A rendelkezésre álló terület kiszámítása a felügyeleti konzol modellje alapján történik: 

- **Üzemi modell**: az alapértelmezett TÁRTERÜLET 40 GB; a korlát 100 GB. 

- **Közepes modell**: az alapértelmezett TÁRTERÜLET 20 GB; a korlát 50 GB. 

- **Laptop-modell**: az alapértelmezett TÁRTERÜLET 10 GB; a határérték 25 GB. 

- **Vékony modell**: az alapértelmezett TÁRTERÜLET 2 GB; a korlát 4 GB. 

- **Robusztus modell**: az alapértelmezett TÁRTERÜLET 10 GB; a határérték 25 GB. 

Az alapértelmezett foglalás megjelenik az **érzékelő biztonsági mentésének ütemterve** párbeszédpanelen. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="A levelezési kiszolgáló konfigurációjának szerkesztése képernyő.":::

Ha külső kiszolgálóra készít biztonsági mentést, a rendszer nem korlátozza a tárterületet. Azonban meg kell határoznia egy felső foglalási korlátot az **érzékelő biztonsági mentési ütemterv**  >  **Egyéni elérési útja** mezőben. A következő számok és karakterek támogatottak: `/, a-z, A-Z, 0-9, and _` . 

A foglalások tárolási korlátait meghaladó információk:

- Ha túllépi a lefoglalt tárolóhelyet, a rendszer nem készít biztonsági másolatot az érzékelőről. 

- Ha egynél több érzékelőről készít biztonsági másolatot, a felügyeleti konzol megkísérli beolvasni a felügyelt érzékelőkhöz tartozó érzékelő fájlokat.  

- Ha az egyik érzékelőből való lekérés meghaladja a korlátot, a felügyeleti konzol megpróbálja beolvasni a biztonsági mentési adatokat a következő érzékelőből. 

Ha túllépi a biztonsági másolatok megőrzött számát, a rendszer törli a legrégebbi biztonsági mentést tartalmazó fájlt, hogy az újat befogadja.

Az érzékelő biztonsági mentési fájljai a következő formátumban lesznek automatikusan elnevezve: `<sensor name>-backup-version-<version>-<date>.tar` . Példa: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Érzékelők biztonsági mentése:

1. A **Rendszerbeállítások** ablakban válassza az **érzékelő biztonsági mentésének ütemezett** beállítása lehetőséget. A helyszíni felügyeleti konzol által felügyelt érzékelők megjelennek az **érzékelő biztonsági mentésének ütemezett** párbeszédpaneljén.  

2. Engedélyezze a **biztonsági másolatok összegyűjtése** váltógomb bejelölését.  

3. Válassza ki a naptári intervallumot, a dátumot és az időzónát. Az időformátum 24 órás időtartamon alapul. Adja meg például a 6:00 PM értéket **18:00**. 

4. A **biztonsági mentési tár kiosztása** mezőben adja meg a biztonsági másolatok számára lefoglalni kívánt tárterületet. Ha túllépi a maximális területet, értesítést kap.

5. A **megtartás utolsó** mezőben adja meg a megőrizni kívánt érzékelők által megőrzött biztonsági másolatok számát. Ha túllépi a korlátot, a rendszer törli a legrégebbi biztonsági mentést.  

6. Válasszon biztonsági mentési helyet:  

   - A helyszíni felügyeleti konzolra történő biztonsági mentéshez tiltsa le az **Egyéni elérési út** kapcsolóját. Az alapértelmezett hely a `/var/cyberx/sensor-backups` .  

   - Egy külső kiszolgálóra történő biztonsági mentéshez engedélyezze az **Egyéni elérésiút** -kapcsolót, és adjon meg egy helyet. A következő számok és karakterek támogatottak: `/, a-z, A-Z, 0-9, and, _` . 

7. Kattintson a **Mentés** gombra. 

Azonnali biztonsági mentés: 

- Válassza a **biztonsági mentés most** lehetőséget. A helyszíni felügyeleti konzol hozza létre és gyűjti az érzékelő biztonságimásolat-fájljait. 

### <a name="receiving-backup-notifications-for-sensors"></a>Biztonsági mentési értesítések fogadása érzékelőkhöz 

Az **érzékelő biztonsági mentési ütemterve** párbeszédpanel és a biztonsági mentési napló automatikusan felsorolja a biztonsági mentés sikerességével és hibáival kapcsolatos információkat.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Megtekintheti az érzékelők listáját, valamint az azok helyét és az összes releváns információt.":::

Hibák a következő esetekben fordulnak elő:    

- Nem található biztonságimásolat-fájl. 

- Egy fájl található, de nem olvasható be.  

- Hálózati csatlakoztatási hiba történt. 

- A biztonsági mentés befejezéséhez nincs elég hely a helyi felügyeleti konzol számára.  

Hiba esetén e-mailes értesítést, syslog-frissítéseket és rendszerértesítéseket is küldhet. Ehhez hozzon létre egy továbbítási szabályt a **Rendszerértesítésekben**. 

### <a name="restoring-sensors"></a>Érzékelők visszaállítása 

A biztonsági másolatokat a helyi felügyeleti konzolról és a parancssori felületről is visszaállíthatja.  

Visszaállítás a konzolról: 

- Válassza a **rendszerkép visszaállítása** lehetőséget az **érzékelő** rendszerbeállítási ablakában.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="A rendszerkép biztonsági másolatának visszaállítása.":::

  A konzol ezután megjeleníti a visszaállítási hibákat.  

Visszaállítás a parancssori felület használatával: 

- Jelentkezzen be egy rendszergazdai fiókba, és írja be a következőt: `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Érzékelő biztonsági másolatának mentése külső SMB-kiszolgálóra

Egy SMB-kiszolgáló beállításához, hogy az érzékelő biztonsági mentését egy külső meghajtóra mentse: 

1. Hozzon létre egy megosztott mappát a külső SMB-kiszolgálón. 

2. Szerezze be az SMB-kiszolgáló eléréséhez szükséges mappa elérési útját, felhasználónevét és jelszavát. 

3. A IoT-beli Defender esetében készítse el a biztonsági másolatok könyvtárát: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Fstab-szerkesztő:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Szerkessze vagy hozza létre a megosztani kívánt hitelesítő adatokat. Az SMB-kiszolgáló hitelesítő adatai: 

   `sudo nano /etc/samba/user` 

6. Adja hozzá a következőket:  

   `username=<user name>` 

   `password=<password>` 

7. A könyvtár csatlakoztatása: 

   `sudo mount -a` 

8. Konfigurálja a biztonsági mentési könyvtárat a Defender for IoT érzékelő megosztott mappájába:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Állítsa a következőre: `Backup.shared_location` `<backup_folder_name_on_cyberx_server>` .

## <a name="next-steps"></a>Következő lépések

[Különálló érzékelők kezelése](how-to-manage-individual-sensors.md)
