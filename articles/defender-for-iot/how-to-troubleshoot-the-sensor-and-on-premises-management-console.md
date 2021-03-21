---
title: Az érzékelő és a helyszíni felügyeleti konzol hibaelhárítása
description: Az érzékelő és a helyszíni felügyeleti konzol hibaelhárításával elháríthatja az esetlegesen felmerülő problémákat.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/14/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: ba68bc3eee94689236792f0270d779357dffde9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465776"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Az érzékelő és a helyszíni felügyeleti konzol hibaelhárítása

Ez a cikk az érzékelő és a helyszíni felügyeleti konzol alapszintű hibaelhárítási eszközeit ismerteti. Az itt ismertetett elemek mellett a következő módokon is megtekintheti a rendszere állapotát:

**Riasztások**: a rendszer riasztást hoz létre, amikor a forgalmat figyelő érzékelő felülete nem áll le. 

**SNMP**: az érzékelő állapotának FIGYELÉSE SNMP protokollon keresztül történik. Az Azure Defender for IoT válaszol az egy jogosult figyelő kiszolgálótól küldött SNMP-lekérdezésekre. 

**Rendszerértesítések**: Ha egy felügyeleti konzol az érzékelőt vezérli, továbbíthatja a riasztásokat a nem megfelelő érzékelők biztonsági mentésével és a leválasztott érzékelőkkel kapcsolatban.

## <a name="sensor-troubleshooting-tools"></a>Érzékelők hibaelhárítási eszközei

### <a name="investigate-password-failure-at-initial-sign-in"></a>Jelszó-meghibásodás vizsgálata a kezdeti bejelentkezéskor

Amikor először jelentkezik be egy előre konfigurált nyíl-érzékelőbe, el kell végeznie a jelszó-helyreállítást.

A jelszó helyreállítása:

1. A IoT-beli Defender-bejelentkezési képernyőn válassza a  **jelszó-helyreállítás** lehetőséget. Megnyílik a **Jelszó-helyreállítási** képernyő.

1. Válassza a **CyberX** vagy a **támogatás** lehetőséget, és másolja ki az egyedi azonosítót.

1. Navigáljon a Azure Portal, és válassza a **helyek és érzékelők** lehetőséget.  

1. Jelölje be a helyszíni **felügyeleti konzol jelszavának helyreállítása** lapot.

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="A helyreállítási fájl letöltéséhez kattintson a helyszíni felügyelet helyreállítása gombra.":::

1. Adja meg a **Jelszó-helyreállítási** képernyőn kapott egyedi azonosítót, és kattintson a **helyreállítás** elemre. A `password_recovery.zip` Fájl letöltése megtörténik.

    > [!NOTE]
    > Ne változtassa meg a jelszó-helyreállítási fájlt. Ez egy aláírt fájl, és nem fog működni, ha Ön nem módosítja.

1. A **Jelszó-helyreállítási** képernyőn válassza a **feltöltés** lehetőséget. Ekkor megnyílik **a jelszó feltöltése helyreállítási fájl** ablak.

1. Válassza a **Tallózás** lehetőséget a fájl megkereséséhez `password_recovery.zip` , vagy húzza a fájlt az `password_recovery.zip` ablakra.

1. Ezután a **tovább** gombra, a felhasználó és a rendszer által létrehozott jelszó jelenik meg a felügyeleti konzolon.

    > [!NOTE]
    > Ha első alkalommal jelentkezik be az érzékelőbe vagy a helyszíni felügyeleti konzolba, akkor ahhoz az előfizetéshez lesz csatolva, amelyhez csatlakoztatta. Ha alaphelyzetbe kell állítania a CyberX vagy a támogató felhasználó jelszavát, ki kell választania az előfizetést. A CyberX helyreállításával vagy a felhasználói jelszavak támogatásával kapcsolatos további információkért lásd: [jelszavak alaphelyzetbe állítása](how-to-create-and-manage-users.md#resetting-passwords).

### <a name="investigate-a-lack-of-traffic"></a>Forgalom hiányának vizsgálata

A konzol tetején megjelenik egy kijelző, ha az érzékelő felismeri, hogy nincs forgalom a konfigurált portok egyikén sem. Ez a mutató minden felhasználó számára látható.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Képernyőkép arról a riasztásról, amelyről nem észlelhető forgalom.":::
 
Ha megjelenik ez az üzenet, megvizsgálhatja, hogy nincs-e forgalom. Győződjön meg arról, hogy a span kábel csatlakoztatva van, és nem történt változás a span architektúrában.  

A támogatási és hibaelhárítási információkért forduljon a [Microsoft ügyfélszolgálatahoz](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>A rendszerteljesítmény ellenõrzése 

Amikor új érzékelőt telepít, vagy például az érzékelő lassan működik, vagy nem jeleníti meg a riasztásokat, megtekintheti a rendszer teljesítményét.

A rendszerteljesítmény ellenõrzése:

1. Győződjön meg arról, hogy az irányítópulton a `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Képernyőkép a minta-irányítópultról."::: 

1. Az oldalsó menüben válassza az **eszközök** lehetőséget.

1. Az **eszközök** ablakban ellenőrizze, hogy az eszközök felderítése folyamatban van-e.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Győződjön meg arról, hogy az eszközök fel vannak derítve.":::

1. Az oldalsó menüben válassza az **adatbányászat** lehetőséget.

1. Az **adatbányászat** ablakban válassza az **összes** lehetőséget, és készítsen jelentést.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Új jelentés létrehozása az adatbányászat használatával.":::

1. Győződjön meg arról, hogy a jelentés tartalmaz adatfájlokat.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Győződjön meg arról, hogy a jelentés tartalmaz-e adatfájlokat.":::

1. Az oldalsó menüben válassza a **trendek & statisztikák** lehetőséget.

1. A **trendek & statisztika** ablakban válassza a **widget hozzáadása** elemet.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="A widget hozzáadásához válassza ki azt.":::

1. Adjon hozzá egy widgetet, és győződjön meg róla, hogy az adatmegjelenítések láthatók

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Győződjön meg arról, hogy a widget megjeleníti az adatfájlokat.":::

1. Az oldalsó menüben válassza a **riasztások** lehetőséget. Megjelenik a **riasztások** ablak.

1. Győződjön meg arról, hogy a riasztások létrejöttek.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Győződjön meg arról, hogy a riasztások létrejöttek.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>A várt riasztások hiányának vizsgálata

Ha a **riasztások** ablak nem jeleníti meg a várt riasztást, ellenőrizze a következőket:

- Ellenőrizze, hogy a **riasztások** ablakban már van-e egy másik biztonsági példányra reagáló válasz. Ha igen, és ez a riasztás még nem lett kezelve, az érzékelő konzolon nem jelenik meg új riasztás.

- Győződjön meg arról, hogy nem zárja ki ezt a riasztást a **riasztás kizárási** szabályainak használatával a felügyeleti konzolon. 

### <a name="investigate-widgets-that-show-no-data"></a>Az adatmegjelenítést nem tartalmazó widgetek vizsgálata

Ha a **trendek & statisztika** ablakában lévő widgetek nem jelenítenek meg adatokat, tegye a következőket:

- A [rendszerteljesítmény ellenõrzése](#check-system-performance).

- Győződjön meg arról, hogy az idő és a régió beállításai megfelelően vannak konfigurálva, és nem a jövőbeli időpontra vannak beállítva. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Csak a szórási eszközöket megjelenítő eszköz leképezésének vizsgálata

Ha a térképen látható eszközök nem csatlakoznak egymáshoz, előfordulhat, hogy a SPAN port konfigurációjában hiba fordul elő. Ez azt jelentheti, hogy csak a szórásos eszközök és az egyedi küldési forgalom nélkül lehet látni.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Megtekintheti a műsorszolgáltatási eszközöket.":::

Ebben az esetben ellenőriznie kell, hogy csak a szórási forgalom látható. Ezután kérje meg a hálózati mérnöket, hogy javítsa ki a SPAN port konfigurációját, hogy megtekintse az egyedi küldésű forgalmat.

Annak ellenőrzése, hogy csak a szórási forgalmat látja-e:

- Az **adatbányászati** képernyőn hozzon létre egy jelentést a **minden** lehetőség használatával. Ezután ellenőrizze, hogy csak a szórás és a csoportos küldésű forgalom (és nem egyedi küldésű forgalom) jelenik-e meg a jelentésben.

Vagy

- Rögzítsen egy PCAP közvetlenül a kapcsolóból, vagy csatlakoztasson egy laptopot a Wireshark használatával.

### <a name="connect-the-sensor-to-ntp"></a>Az érzékelő összekötése az NTP-vel

Az NTP-hez való kapcsolódáshoz beállíthat egy önálló érzékelőt és egy felügyeleti konzolt az ahhoz kapcsolódó érzékelőkkel.

Önálló érzékelő összekötése az NTP-vel:

- [Segítségért forduljon a támogatási csapathoz](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

A felügyeleti konzol által vezérelt érzékelő összekötése az NTP-vel:

- Az NTP-hez való kapcsolódás a felügyeleti konzolon van konfigurálva. Az összes érzékelő, amelyet a felügyeleti konzol vezérel, automatikusan beolvassa az NTP-kapcsolatokat.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Vizsgálja meg, hogy az eszközök nem jelennek-e meg a térképen, vagy több internettel kapcsolatos riasztás van

Az INTERNETKAPCSOLATtal rendelkező eszközök néha külső IP-címekkel vannak konfigurálva. Ezek az INTERNETKAPCSOLATtal rendelkező eszközök nem jelennek meg a térképen. Az eszközök helyett egy internetes felhő jelenik meg a térképen. Ezeknek az eszközöknek az IP-címeit a Felhőbeli rendszerkép tartalmazza.

A probléma egy másik jele, ha több internettel kapcsolatos riasztás jelenik meg.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Több internettel kapcsolatos riasztás.":::

A konfiguráció kijavítása:

1. Kattintson a jobb gombbal a felhő ikonjára az eszköz térképen, és válassza az **IP-címek exportálása** lehetőséget. Másolja a magánjellegű nyilvános tartományokat, és adja hozzá őket az alhálózati listához. További információ: [alhálózatok konfigurálása](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Új adatbányászati jelentés létrehozása az internetes kapcsolatokhoz.

1. Az adatbányászati jelentésben válassza :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: a rendszergazdai mód megadását és az internetkapcsolattal rendelkező eszközök IP-címeinek törlését.

### <a name="tweak-the-sensors-quality-of-service"></a>Az érzékelő minőségi szolgáltatásának csípése

A hálózati erőforrások mentéséhez korlátozhatja azt az interfész-sávszélességet, amelyet az érzékelő használ a napi eljárásokhoz.

Az interfész sávszélességének korlátozásához használja a `cyberx-xsense-limit-interface` CLI eszközt, amelyet a sudo engedélyekkel kell futtatni. Az eszköz a következő argumentumokat kéri le:

  - `* -i`: felületek (példa: ETH0).

  - `* -l`: korlát (példa: 30 kbit/1 Mbit). A következő sávszélesség-egységeket használhatja: kbps, Mbps, kbit, Mbit vagy bps.

  - `* -c`: Clear (az interfész sávszélesség-korlátozásának törléséhez).

A szolgáltatás minőségének megcsípése:

1. Jelentkezzen be az érzékelő CLI-be Defender IoT-felhasználóként, és írja be a következőt: `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Például: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Fizikai készülék esetén használja a em1 felületet.

1. Az illesztőfelület korlátozásának törléséhez írja be a következőt: `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>A helyszíni felügyeleti konzol hibaelhárítási eszközei

### <a name="investigate-a-lack-of-expected-alerts"></a>A várt riasztások hiányának vizsgálata

Ha a **riasztások** ablakban nem jelenik meg a várt riasztás, ellenőrizze a következőket:

- Ellenőrizze, hogy a **riasztások** ablakban már van-e egy másik biztonsági példányra reagáló válasz. Ha igen, és ez a riasztás még nem lett kezelve, egy új riasztás nem jelenik meg.

- Győződjön meg arról, hogy nem zárja ki ezt a riasztást a helyi felügyeleti konzolon a **riasztás kizárási** szabályainak használatával.  

### <a name="tweak-the-quality-of-service"></a>A szolgáltatás minőségének megcsípése

A hálózati erőforrások mentéséhez korlátozhatja a külső rendszereknek (például az e-maileknek vagy a SIEM-nek) küldött riasztások számát egyetlen szinkronizálási műveletben egy berendezés és a helyszíni felügyeleti konzol között.

Az alapértelmezett érték a 50. Ez azt jelenti, hogy egy berendezés és a helyszíni felügyeleti konzol közötti egyetlen kommunikációs munkamenetben nem lesz több, mint 50 riasztás a külső rendszerekre. 

A riasztások számának korlátozásához használja a `notifications.max_number_to_report` ben elérhető tulajdonságot `/var/cyberx/properties/management.properties` . A tulajdonság módosítása után nincs szükség újraindításra.

A szolgáltatás minőségének megcsípése:

1. Jelentkezzen be Defenderként a IoT-felhasználó számára. 

1. Ellenőrizze az alapértelmezett értékeket:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   A következő alapértelmezett értékek jelennek meg:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Az alapértelmezett beállítások szerkesztése:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Szerkessze a következő sorok beállításait:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Mentse a módosításokat. Újraindításra nincs szükség.

## <a name="export-information-for-troubleshooting"></a>Információk exportálása hibaelhárításhoz

A hálózat figyelésére és elemzésére szolgáló eszközök mellett további vizsgálat céljából adatokat is küldhet a támogatási csapatnak. A naplók exportálásakor az érzékelő automatikusan létrehoz egy egyszeri jelszót (OTP), amely egyedi az exportált naplók számára, egy külön szövegfájlban. 

Naplók exportálása:

1. A bal oldali ablaktáblán válassza a **Rendszerbeállítások** elemet.

1. Válassza a **Naplók exportálása** elemet.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Napló exportálása a rendszertámogatásba.":::

1. A **fájl neve** mezőbe írja be a napló exportálásához használni kívánt fájlnevet. Az alapértelmezett érték az aktuális dátum.

1. Az exportálni kívánt adatkategóriák meghatározásához válassza ki az adatkategóriákat:  

    | Exportálási kategória | Description |
    |--|--|
    | **Operációs rendszer naplói** | Válassza ezt a lehetőséget az operációs rendszer állapotával kapcsolatos információk lekéréséhez. |
    | **Telepítési/frissítési naplók** | Válassza ezt a lehetőséget a telepítési és frissítési konfigurációs paraméterek vizsgálatához. |
    | **Rendszerjózanság kimenete** | Ezzel a beállítással ellenőrizhető a rendszerteljesítmény. |
    | **Naplók elmetszete** | Ezzel a beállítással engedélyezheti a protokollok szakaszának speciális vizsgálatát. |
    | **OPERÁCIÓSRENDSZER-kernel-memóriaképek** | Válassza ezt a lehetőséget a kernel memóriaképének exportálásához. A Kernel memóriaképe tartalmazza az összes memóriát, amelyet a kernel az adott kernelben előforduló probléma idején használ. A memóriakép mérete kisebb a teljes memóriaképnél. A memóriakép általában a rendszer fizikai memóriájának egyharmadát adja meg. |
    | **Naplók továbbítása** | Válassza ezt a lehetőséget a továbbítási szabályok vizsgálatához. |
    | **SNMP-naplók** | Válassza ezt a lehetőséget az SNMP-állapot-ellenőrzési információk fogadásához. |
    | **Alapvető alkalmazási naplók** | Válassza ezt a lehetőséget az alapvető alkalmazás-konfigurációra és a műveletre vonatkozó információk exportálásához. |
    | **Kommunikáció a CM-naplókkal** | Akkor válassza ezt a lehetőséget, ha folyamatos problémák léptek fel, vagy a Felügyeleti konzollal fennáll a kapcsolódás megszakítása. |
    | **Webalkalmazás-naplók** | Ezzel a beállítással információkat kérhet le az alkalmazás webes felületéről küldött összes kérelemről. |
    | **Rendszerbiztonsági mentés** | Ezzel a beállítással exportálhatja az összes Rendszerinformáció biztonsági másolatát, hogy a rendszer pontos állapotát vizsgálja. |
    | **Szakaszok statisztikája** | Ezzel a beállítással engedélyezheti a protokollok statisztikájának speciális vizsgálatát. |
    | **Adatbázis-naplók** | Válassza ezt a lehetőséget a naplók a Rendszeradatbázisból való exportálásához. A rendszernaplók kivizsgálása segít a rendszerproblémák azonosításában. |
    | **Konfigurálás** | Ezzel a beállítással exportálhatja az összes konfigurálható paraméter adatait, így meggyőződhet arról, hogy minden megfelelően van-e konfigurálva. |

1. Az összes beállítás kiválasztásához válassza az **összes kiválasztása** lehetőséget a **Kategóriák kiválasztása** lehetőségnél.

1. Válassza a **Naplók exportálása** elemet.

Az exportált naplók bekerülnek az **archivált naplók** listájára. Küldje el az egyszeri jelszavas támogatást a támogatási csapatnak egy külön üzenetben, és közepes az exportált naplókból. A támogatási csapat csak a naplók titkosításához használt egyedi egyszeri jelszavas hitelesítéssel képes kinyerni az exportált naplókat.

Az archivált naplók listája legfeljebb öt elemet tartalmazhat. Ha a listában szereplő elemek száma túllépi ezt a számot, a rendszer a legkorábbi elemet törli.

## <a name="see-also"></a>Lásd még

- [Riasztások megtekintése](how-to-view-alerts.md)

- [Az SNMP MIB-monitorozás beállítása](how-to-set-up-snmp-mib-monitoring.md)

- [Az érzékelő leválasztási eseményeinek ismertetése](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
