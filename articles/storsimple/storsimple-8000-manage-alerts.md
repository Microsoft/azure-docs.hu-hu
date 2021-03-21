---
title: Riasztások megtekintése és kezelése a StorSimple 8000 Series eszközhöz
description: Ismerteti a StorSimple riasztási feltételeit és súlyosságát, a riasztási értesítések konfigurálását, valamint a StorSimple Eszközkezelő szolgáltatás használatát a riasztások kezeléséhez.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016845"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>A StorSimple-riasztások megtekintése és kezelése a StorSimple Eszközkezelő szolgáltatással

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás **riasztások** panelje lehetővé teszi, hogy valós időben áttekintse és törölje a StorSimple-eszközökhöz kapcsolódó riasztásokat. Ebből a panelből központilag figyelheti a StorSimple-eszközök állapotát és a teljes Microsoft Azure StorSimple megoldást.

Ez az oktatóanyag a gyakori riasztási feltételeket, a riasztás súlyossági szintjeit, valamint a riasztási értesítések konfigurálását ismerteti. Emellett riasztási gyors referenciával rendelkező táblákat is tartalmaz, amelyek lehetővé teszik, hogy gyorsan megkeresse egy adott riasztást, és megfelelően reagáljon.

![Riasztások lap](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Gyakori riasztási feltételek

A StorSimple-eszköz riasztásokat hoz létre a különböző feltételekre válaszul. A riasztási feltételek leggyakoribb típusai a következők:

* **Hardverrel kapcsolatos problémák** – ezek a riasztások tájékoztatják a hardver állapotáról. Lehetővé teszik, hogy a belső vezérlőprogram frissítésére van szükség, ha a hálózati adapter problémába ütközik, vagy ha probléma van az egyik adatmeghajtóval.
* **Kapcsolódási problémák** – ezek a riasztások akkor fordulnak elő, ha az adatátviteli nehézségek merülnek fel. Kommunikációs problémák léphetnek fel az Azure Storage-fiókba irányuló és onnan érkező adatok átvitele során, vagy az eszközök és a StorSimple Eszközkezelő szolgáltatás közötti kapcsolat hiánya miatt. A kommunikációs problémák némelyike a legnehezebb megoldás, mert sok meghibásodási pont van. Mindig először ellenőrizze, hogy a hálózati kapcsolat és az Internet-hozzáférés elérhető-e, mielőtt továbblépne a fejlettebb hibaelhárításra. A hibaelhárítással kapcsolatos segítségért keresse fel a következőt: [Hibaelhárítás a Test-Connection parancsmaggal](storsimple-8000-troubleshoot-deployment.md).
* **Teljesítménnyel kapcsolatos problémák** – ezek a riasztások akkor keletkeznek, ha a rendszer nem optimális teljesítményű, például ha nagy terhelés alatt áll.

Emellett előfordulhat, hogy a biztonsággal, a frissítésekkel vagy a feladatok meghibásodásával kapcsolatos riasztások jelennek meg.

## <a name="alert-severity-levels"></a>Riasztás súlyossági szintjei

A riasztások eltérő súlyossági szinttel bírnak, attól függően, hogy milyen hatással van a riasztási helyzetre, és hogy a riasztásra adott válaszra van szükség. A súlyossági szintek a következőek:

* **Kritikus** – ez a riasztás olyan feltételre reagál, amely hatással van a rendszer sikeres teljesítményére. A művelet végrehajtásához szükséges, hogy a StorSimple szolgáltatás ne legyen megszakítva.
* **Figyelmeztetés** – ez az állapot kritikus lehet, ha nincs megoldva. Meg kell vizsgálnia a helyzetet, és el kell végeznie a probléma törléséhez szükséges lépéseket.
* **Információ** – ez a riasztás olyan információkat tartalmaz, amelyek hasznosak lehetnek a rendszer nyomon követésében és kezelésében.

## <a name="configure-alert-settings"></a>Riasztási beállítások konfigurálása

Megadhatja, hogy szeretne-e e-mailben értesítést kapni az egyes StorSimple-eszközök riasztási feltételeiről. Emellett a riasztások más címzettjeit is megadhatja, ha az e-mail-címüket a **többi e-mail címzettjei** mezőbe írja, pontosvesszővel elválasztva.

> [!NOTE]
> Eszközönként legfeljebb 20 e-mail-címet adhat meg.

Miután engedélyezte az e-mailes értesítéseket egy eszközhöz, az értesítési lista tagjai minden alkalommal kapnak egy e-mail-üzenetet, amikor kritikus riasztás történik. Az üzenetek a *storsimple-Alerts-reply \@ mail.windowsazure.com* lesznek elküldve, és leírják a riasztási feltételt. A címzettek a **leiratkozás** lehetőségre kattintva eltávolíthatják magukat az e-mail értesítések listájáról.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Riasztások e-mailes értesítésének engedélyezése az eszközön
1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök listájából válassza ki a konfigurálni kívánt eszközt, majd kattintson rá.
2. Lépjen az eszköz **Beállítások**  >  **általános** részére.

   ![Riasztások panel](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Az **általános beállítások** panelen lépjen a **riasztási beállítások** elemre, és állítsa be a következőket:
   
   1. Az **e-mail értesítés küldése** mezőben válassza az **Igen** lehetőséget.
   2. Az **e-mail szolgáltatás-rendszergazdák** mezőben válassza az **Igen** lehetőséget, ha szeretné, hogy a szolgáltatás rendszergazdája és az összes társ-rendszergazda megkapja a riasztási értesítéseket.
   3. A **többi e-mail-címzett** mezőben adja meg az összes többi címzett e-mail-címét, akiknek a riasztási értesítéseket kell kapniuk. Adja meg a neveket a *valaki \@ Somewhere.com* formátumban. Az e-mail-címeket pontosvesszővel válassza el egymástól. Eszközönként legfeljebb 20 e-mail-címet állíthat be. 
      
3. Teszt e-mail értesítés küldéséhez kattintson a **Tesztüzenet küldése** elemre. A StorSimple Eszközkezelő szolgáltatás megjeleníti az állapotüzenetek üzeneteit, mert továbbítja a teszt értesítését.

    ![Riasztási beállítások](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Ekkor megjelenik egy értesítés, amikor a teszt e-mailt elküldte. 
   
    ![Riasztások teszt értesítő e-mailek elküldve](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Ha a teszt értesítési üzenet nem küldhető el, a StorSimple Eszközkezelő szolgáltatás megfelelő hibaüzenetet jelenít meg. Várjon néhány percet, majd próbálja meg újra elküldeni a teszt értesítési üzenetet. 

5. A konfiguráció befejezése után kattintson a **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Riasztások tesztelési értesítő e-mail-címe 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Riasztások megtekintése és nyomon követése

A StorSimple Eszközkezelő szolgáltatások összegzése panel gyors áttekintést nyújt az eszközökön lévő riasztások számáról, súlyossági szint szerint rendezve.

![Riasztások irányítópult](./media/storsimple-8000-manage-alerts/device-summary4.png)

A súlyossági szintre kattintva megnyílik a **riasztások** panel. Az eredmények csak azokat a riasztásokat tartalmazzák, amelyek megfelelnek a súlyossági szintnek.

Ha a listában egy riasztásra kattint, a riasztás további részleteket tartalmaz, beleértve a riasztás legutóbbi jelentésének időpontját, az eszközön előforduló riasztások számát, valamint a riasztás feloldásához javasolt műveletet. Ha ez egy hardveres riasztás, akkor a hardver-összetevőt is azonosítja.

![Hardveres riasztás – példa](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Ha az adatokat Microsoft ügyfélszolgálata szeretné elküldeni, a riasztás részleteit szövegfájlba másolhatja. Miután végrehajtotta a javaslatot, és feloldotta a riasztási feltételt a helyszínen, törölje a riasztást az eszközről, és válassza ki  a riasztást a riasztások panelen, és kattintson a **Törlés** gombra. Több riasztás törléséhez jelölje ki az egyes riasztásokat, kattintson bármelyik oszlopra a **riasztási** oszlop kivételével, majd kattintson a **Törlés** gombra, miután kiválasztotta az összes riasztást, amelyeket törölni szeretne. Vegye figyelembe, hogy egyes riasztások automatikusan törlődnek a probléma megoldásakor, vagy ha a rendszer új információkkal frissíti a riasztást.

Ha a **Törlés** gombra kattint, lehetősége lesz a riasztással kapcsolatos megjegyzések és a probléma megoldásához szükséges lépések megadására. A rendszer néhány eseményt töröl, ha egy másik esemény új információval aktiválódik. Ebben az esetben a következő üzenet jelenik meg.

![Riasztási üzenet törlése](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Riasztások rendezése és áttekintése

Előfordulhat, hogy hatékonyabban szeretne jelentéseket futtatni a riasztásokon, hogy áttekintse és törölje őket a csoportokban. Emellett a **riasztások** panel legfeljebb 250 riasztást képes megjeleníteni. Ha túllépte a riasztások számát, nem minden riasztás jelenik meg az alapértelmezett nézetben. A következő mezőket kombinálva testreszabhatja, hogy mely riasztások jelenjenek meg:

* **Állapot** – **aktív** vagy **törölt** riasztásokat is megjeleníthet. Az aktív riasztások még mindig aktiválva vannak a rendszeren, míg a törölt riasztásokat a rendszergazda manuálisan törölte vagy programozott módon törölte, mert a rendszer frissítette a riasztási feltételt új információkkal.
* **Súlyosság** – riasztásokat jeleníthet meg az összes súlyossági szintről (kritikus, figyelmeztetés, információ), vagy csak egy bizonyos súlyosságot, például csak a kritikus riasztásokat.
* **Forrás** – megjelenítheti az összes forrásból származó riasztásokat, vagy korlátozhatja a riasztásokat a szolgáltatásból vagy az egyik vagy az összes eszközről érkezők számára.
* **Időtartomány** – a **kezdő** és **a** dátumok és időbélyegek megadásával megtekintheti a riasztásokat az Ön által érintett időszakban.

![Riasztások listája](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Riasztások – rövid útmutató

Az alábbi táblázatok felsorolják az esetlegesen felmerülő Microsoft Azure StorSimple riasztásokat, valamint a további információkat és javaslatokat, ahol elérhetők. A StorSimple-eszközökre vonatkozó riasztások a következő kategóriák valamelyikébe tartoznak:

* [Felhőalapú csatlakozási riasztások](#cloud-connectivity-alerts)
* [Fürt riasztásai](#cluster-alerts)
* [Vész-helyreállítási riasztások](#disaster-recovery-alerts)
* [Hardveres riasztások](#hardware-alerts)
* [Sikertelen feladatok riasztásai](#job-failure-alerts)
* [Helyileg rögzített mennyiségi riasztások](#locally-pinned-volume-alerts)
* [Hálózati riasztások](#networking-alerts)
* [Teljesítmény-riasztások](#performance-alerts)
* [Biztonsági riasztások](#security-alerts)
* [Támogatási csomagra vonatkozó riasztások](#support-package-alerts)
* [Bekerítési környezet riasztásai](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Felhőalapú csatlakozási riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem lehet kapcsolatot létesíteni <*Felhőbeli hitelesítő adatok nevével*>. |Nem lehet csatlakozni a Storage-fiókhoz. |Úgy tűnik, hogy az eszközhöz kapcsolódási probléma merülhet fel. A `Test-HcsmConnection` probléma azonosításához és kijavításához futtassa a parancsmagot az eszközön található StorSimple Windows PowerShell felületén. Ha a beállítások helyesek, a probléma lehet annak a Storage-fióknak a hitelesítő adataival, amelyhez a riasztás létrejött. Ebben az esetben a `Test-HcsStorageAccountCredential` parancsmaggal állapítsa meg, hogy van-e probléma a megoldással.<ul><li>Keresse meg a hálózati beállításokat.</li><li>Keresse meg a Storage-fiók hitelesítő adatait.</li></ul> |
| Nem kaptunk szívverést az eszközről az utolsó <*szám*> percben. |Nem lehet csatlakozni az eszközhöz. |Úgy tűnik, az eszköz csatlakozási problémája van. A `Test-HcsmConnection` probléma azonosításához és kijavításához, illetve a hálózati rendszergazdához való kapcsolatfelvételhez használja a parancsmagot az eszköz StorSimple Windows PowerShell-felületén. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple viselkedés a Felhőbeli kapcsolat sikertelensége esetén

Mi történik, ha nem sikerül a Felhőbeli kapcsolat az éles környezetben futó StorSimple-eszközön?

Ha a Felhőbeli kapcsolat nem sikerül a StorSimple éles eszközén, akkor az eszköz állapotától függően a következők jelenhetnek meg:

* A **helyi adatokat az eszközön**: egy ideig nem lehet megszakítani, és a rendszer továbbra is kiszolgálja az olvasást. Mivel azonban a függőben lévő IOs-növekedés és a korlát meghaladja a határértéket, az olvasási műveletek sikertelenek lesznek.

    Az eszközön lévő adatmennyiségtől függően az írás a Felhőbeli kapcsolat megszakadását követő első néhány órában is folytatódni fog. Az írások ezután lelassulnak, és végül sikertelenek lesznek, ha a Felhőbeli kapcsolat több órán keresztül megszakad. (A felhőbe leküldhető adatmennyiséghez ideiglenes tárterület van az eszközön. A rendszer kiüríti ezt a területet az adatküldés során. Ha a kapcsolat meghiúsul, akkor a rendszer nem küldi le a felhőbe az ebben a tárolóhelyen lévő adatvesztést, és az i/o-művelet sikertelen lesz.)
* A **felhőben tárolt** adatműveletek esetén a legtöbb Felhőbeli kapcsolódási hiba esetén hibaüzenetet ad vissza. A kapcsolat visszaállítása után az IOs-t a felhasználó anélkül folytatja, hogy online állapotba hozza a kötetet. Ritka esetekben előfordulhat, hogy felhasználói beavatkozásra van szükség ahhoz, hogy a kötetet online állapotba vonja a Azure Portal.
* A **Felhőbeli Pillanatképek folyamatban**: a művelet többször is próbálkozik a 4-5 órán belül, és ha a kapcsolat nem áll helyre, a Felhőbeli Pillanatképek sikertelenek lesznek.

### <a name="cluster-alerts"></a>Fürt riasztásai

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Az eszköz feladatának átadása <*eszköz neve*>. |Az eszköz karbantartási üzemmódban van. |Az eszköz feladatátvétele a karbantartási mód beírása vagy bezárása miatt meghiúsult. Ez normális, és nincs szükség beavatkozásra. Miután tudomásul vette a riasztást, törölje azt a riasztások lapról. |
| Az eszköz feladatának átadása <*eszköz neve*>. |Az eszköz belső vezérlőprogramja vagy a szoftver frissítése megtörtént. |A fürt feladatátvétele egy frissítés miatt történt. Ez normális, és nincs szükség beavatkozásra. Miután tudomásul vette a riasztást, törölje azt a riasztások lapról. |
| Az eszköz feladatának átadása <*eszköz neve*>. |A vezérlő leállítása vagy újraindítása megkezdődött. |Az eszköz feladatátvétele megtörtént, mert az aktív vezérlőt a rendszergazda leállította vagy újraindította. Nincs szükség beavatkozásra. Miután tudomásul vette a riasztást, törölje azt a riasztások lapról. |
| Az eszköz feladatának átadása <*eszköz neve*>. |Tervezett feladatátvétel. |Ellenőrizze, hogy ez tervezett feladatátvétel volt-e. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról. |
| Az eszköz feladatának átadása <*eszköz neve*>. |Nem tervezett feladatátvétel. |A StorSimple úgy van kialakítva, hogy automatikusan helyreállítsa a nem tervezett feladatátvételeket. Ha nagy számú riasztást lát, forduljon a Microsoft ügyfélszolgálatahoz. |
| Az eszköz feladatának átadása <*eszköz neve*>. |Egyéb/ismeretlen ok. |Ha nagy számú riasztást lát, forduljon a Microsoft ügyfélszolgálatahoz. A probléma megoldása után törölje ezt a riasztást a riasztások lapról. |
| A kritikus eszközbeállítások jelentéseinek állapota sikertelen. |DataPath-szolgáltatási hiba. |Segítségért forduljon Microsoft ügyfélszolgálatahoz. |
| A hálózati adapter virtuális IP-címe *<az* adat> a jelentések állapota sikertelen. |Egyéb/ismeretlen ok. |Időnként előfordulhat, hogy az ideiglenes feltételek ezeket a riasztásokat okozzák. Ebben az esetben a riasztást a rendszer némi idő elteltével automatikusan törli. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| A hálózati adapter virtuális IP-címe *<az* adat> a jelentések állapota sikertelen. |Csatoló neve *: <* adat> IP-cím `<IP address>` nem hozható online állapotba, mert a hálózaton ismétlődő IP-cím észlelhető. |Győződjön meg arról, hogy az ismétlődő IP-cím el lett távolítva a hálózatról, vagy konfigurálja újra a felületet egy másik IP-címmel. |

### <a name="disaster-recovery-alerts"></a>Vész-helyreállítási riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| A helyreállítási műveletek nem tudták visszaállítani a szolgáltatás összes beállítását. Az eszköz konfigurációs adategysége nem konzisztens állapotban van bizonyos eszközök esetében. |Az adatkonzisztencia a vész-helyreállítás után észlelhető. |A szolgáltatásban lévő titkosított adatokat nem szinkronizálja a rendszer az eszközön. A szinkronizálási folyamat elindításához engedélyezze az eszköz <*eszköz nevét*> a StorSimple Eszközkezelő. A StorSimple Windows PowerShell-felületén futtathatja az eszközön `Restore-HcsmEncryptedServiceData` <*eszköz neve*> parancsmagot, amely a régi jelszót adja meg bemenetként a parancsmagnak a biztonsági profil visszaállításához. Ezután futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmagot a szolgáltatásban tárolt adattitkosítási kulcs frissítéséhez. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról. |

### <a name="hardware-alerts"></a>Hardveres riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| A hardver-összetevő <*összetevő-azonosító*> a jelentések állapota <*állapotként*>. | |Időnként előfordulhat, hogy az ideiglenes feltételek ezeket a riasztásokat okozzák. Ha igen, akkor ez a riasztás némi idő elteltével automatikusan törlődik. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| A passzív vezérlő meghibásodik. |A passzív (másodlagos) vezérlő nem működik. |Az eszköz működőképes, de az egyik vezérlő meghibásodik. Próbálja meg újraindítani a vezérlőt. Ha a probléma nem oldódik meg, forduljon a Microsoft ügyfélszolgálatahoz. |

### <a name="job-failure-alerts"></a>Sikertelen feladatok riasztásai

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült biztonsági másolatot készíteni <*forrás mennyiségi csoportjának azonosítója*>. |A biztonsági mentési feladatok sikertelenek voltak. |A kapcsolódási problémák megakadályozhatják a biztonsági mentési művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, lehetséges, hogy elérte a biztonsági mentések maximális számát. Törölje a már nem szükséges biztonsági másolatokat, majd próbálja megismételni a műveletet. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról. |
| A <*forrás biztonsági másolati elem azonosítóinak* klónozása nem sikerült,> <*kötet sorozatszámait*> sikertelen volt. |A klónozási feladatok sikertelenek voltak. |Frissítse a biztonsági mentési listát annak ellenőrzéséhez, hogy a biztonsági másolat még érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőalapú kapcsolódási problémák akadályozzák a klónozási művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, lehetséges, hogy elérte a tárolási korlátot. Törölje a már nem szükséges biztonsági másolatokat, majd próbálja megismételni a műveletet. Miután elvégezte a probléma megoldásához szükséges lépéseket, törölje a riasztást a riasztások lapról. |
| Nem sikerült visszaállítani <*forrás biztonsági másolati elem azonosítóit*>. |A visszaállítási feladatot nem sikerült végrehajtani. |Frissítse a biztonsági mentési listát annak ellenőrzéséhez, hogy a biztonsági másolat még érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőalapú kapcsolódási problémák akadályozzák a visszaállítási művelet sikeres befejezését. Ha nincsenek kapcsolódási problémák, lehetséges, hogy elérte a tárolási korlátot. Törölje a már nem szükséges biztonsági másolatokat, majd próbálja megismételni a műveletet. Miután elvégezte a probléma megoldásához szükséges lépéseket, törölje a riasztást a riasztások lapról. |

### <a name="locally-pinned-volume-alerts"></a>Helyileg rögzített mennyiségi riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült létrehozni a helyi kötet <*kötet nevét*>. |A kötet-létrehozási feladata sikertelen volt. <*A hibás hibakódnak megfelelő hibaüzenet*>. |A kapcsolódási problémák akadályozhatják a hely létrehozási műveletének sikeres befejezését. A helyileg rögzített kötetek kiosztása sűrűn történik, a lemezterület létrehozásának folyamata pedig a lépcsőzetes kötetek felhőbe való kivonásával jár. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy kimerítette a helyi területet az eszközön. Annak megállapítása, hogy létezik-e a hely az eszközön a művelet újbóli megkísérlése előtt. |
| Nem sikerült kibővíteni a helyi kötet <a *kötet nevét*>. |A kötet-módosítási feladatot a hibás *hibakódnak megfelelő <hibaüzenet* miatt nem sikerült>. |A csatlakozási problémák megakadályozhatják a kötet bővítési műveletének sikeres végrehajtását. A helyileg rögzített kötetek kiosztása sűrűn történik, és a meglévő terület kibővítésének folyamata magában foglalja a lépcsőzetes kötetek felhőbe való kivonását. Ha nincsenek kapcsolódási problémák, előfordulhat, hogy kimerítette a helyi területet az eszközön. Annak megállapítása, hogy létezik-e a hely az eszközön a művelet újbóli megkísérlése előtt. |
| Nem sikerült a kötet <*kötetének neve*> konvertálása. |A kötet átalakítási feladata, hogy a kötet típusát a lépcsőzetesen rögzített értékre konvertálja, nem sikerült. |Nem sikerült befejezni a kötetnek a lépcsőzetesen rögzített típusról rétegűre való átalakítását. Győződjön meg arról, hogy nincsenek kapcsolódási problémák, amelyek megakadályozzák a művelet sikeres befejezését. A kapcsolódási problémák elhárításához lépjen a [Test-HcsmConnection parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)végzett hibaelhárításhoz.<br>Az eredeti helyileg rögzített kötet már meg lett jelölve egy lépcsőzetes kötetként, mert a helyileg rögzített kötetből származó egyes adatok átkerülnek a felhőbe az átalakítás során. Az eredő rétegű kötet továbbra is helyi helyet foglal az eszközön, amely nem igényelhető vissza a jövőbeli helyi kötetek esetében.<br>Javítsa ki a kapcsolódási problémákat, törölje a riasztást, és alakítsa vissza a kötetet az eredeti helyileg rögzített kötetre, hogy az összes, az összes adattal helyileg elérhetővé válik. |
| Nem sikerült a kötet <*kötetének neve*> konvertálása. |Nem sikerült a kötet átalakítási feladatainak a rétegekből a helyileg rögzítettre történő átalakítására irányuló feladata. |Nem sikerült befejezni a kötet átalakítását a lépcsőzetesről a helyileg rögzített típusra. Győződjön meg arról, hogy nincsenek kapcsolódási problémák, amelyek megakadályozzák a művelet sikeres befejezését. A kapcsolódási problémák elhárításához lépjen a [Test-HcsmConnection parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)végzett hibaelhárításhoz.<br>Az átalakítási folyamat részeként már helyileg rögzített kötetként jelölt eredeti, többkötetes kötet továbbra is a felhőben található, az eszközön lévő, sűrűn kiépített terület azonban többé nem állítható vissza a jövőbeli helyi kötetek esetében.<br>Javítsa ki a kapcsolódási problémákat, törölje a riasztást, és konvertálja vissza a kötetet az eredeti rétegű kötet típusára annak érdekében, hogy az eszközön sűrűn kiépített helyi tárterület is visszaigényelhető legyen. |
| Közeli helyi felhasználás helyi pillanatképekhez <*mennyiségi csoport neve*> |Előfordulhat, hogy a biztonsági mentési szabályzat helyi pillanatképei hamarosan elfogynak a tárhelyen, és érvénytelenítve lesznek, hogy elkerülje a gazdagép írási hibáit. |Gyakori helyi Pillanatképek a biztonsági mentési szabályzathoz társított kötetek magas adatváltozása mellett az eszközön a helyi terület gyorsan felhasználható. Törölje a már nem szükséges helyi pillanatképeket. Emellett frissítse a biztonsági mentési szabályzathoz tartozó helyi pillanatkép-ütemterveket a ritkább helyi Pillanatképek elvégzéséhez, és győződjön meg arról, hogy a Felhőbeli Pillanatképek rendszeresen zajlanak. Ha ezeket a műveleteket nem végzi el, előfordulhat, hogy a pillanatképek helyi területe hamarosan ki lesz merítve, és a rendszer automatikusan törli őket, hogy a gazdagépek írásait továbbra is sikeresen feldolgozza. |
| A <*mennyiségi csoport neve*> helyi pillanatképei érvénytelenítve lettek. |A <*kötet csoportjának neve*> helyi pillanatképei érvénytelenítve lettek, majd törölve lettek, mert túllépték a helyi területet az eszközön. |Annak biztosítása érdekében, hogy ez a jövőben ne ismétlődjön meg, tekintse át a biztonsági mentési szabályzathoz tartozó helyi pillanatkép-ütemterveket, és törölje a már nem szükséges helyi pillanatképeket. A biztonsági mentési házirend csoportba tartozó kötetek magas adatváltozása mellett gyakori helyi Pillanatképek is okozhatják az eszközön a helyi tárterület gyors felhasználását. |
| Nem sikerült visszaállítani <*forrás biztonsági másolati elem azonosítóit*>. |A visszaállítási feladatot nem sikerült végrehajtani. |Ha a biztonsági mentési szabályzatban helyileg rögzített és lépcsőzetes kötetek is vannak, akkor frissítse a biztonsági mentési listát, és ellenőrizze, hogy a biztonsági másolat még érvényes-e. Ha a biztonsági mentés érvényes, lehetséges, hogy a felhőalapú kapcsolódási problémák akadályozzák a visszaállítási művelet sikeres befejezését. A pillanatkép-csoport részeként visszaállított helyileg rögzített kötetek nem rendelkeznek az eszközre letöltött összes adattal, és ha a pillanatkép-csoportban lévő, többszintes és helyileg rögzített kötetek vegyesen vannak, nem lesznek szinkronban egymással. A visszaállítási művelet sikeres elvégzéséhez állítsa offline állapotba a csoportban lévő köteteket a gazdagépen, majd próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a visszaállítási folyamat során végrehajtott összes módosítás elvész. |

### <a name="networking-alerts"></a>Hálózati riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem lehetett elindítani a StorSimple szolgáltatás (oka) t. |DataPath hiba |Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| Ismétlődő IP-cím észlelhető a következőhöz: "Data0". | |A rendszer ütközést észlelt a (z) 10.0.0.1 IP-címhez. Az eszközön lévő "Data0" hálózati erőforrás *\<device1>* offline állapotban van. Győződjön meg arról, hogy ezt az IP-címet a hálózat egyik másik entitása sem használja. A hálózati problémák elhárításához lépjen a következőre: [Hibaelhárítás a Get-NetAdapter parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). A probléma megoldása érdekében forduljon a hálózati rendszergazdához. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| A "Data0" IPv4-(vagy IPv6-) címe offline állapotban van. | |A "Data0" hálózati erőforrás "10.0.0.1" IP-címmel. az eszközön a "22" előtag hossza *\<device1>* offline állapotban van. Győződjön meg arról, hogy az illesztőhöz csatlakozó portok működnek. A hálózati problémák elhárításához lépjen a következőre: [Hibaelhárítás a Get-NetAdapter parancsmaggal](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nem lehet csatlakozni a hitelesítési szolgáltatáshoz. |DataPath hiba |A hitelesítéshez használt URLthat nem érhető el. Győződjön meg arról, hogy a tűzfalszabályok tartalmazzák a StorSimple-eszközhöz megadott URL-mintákat. A Azure Portal URL-mintázatával kapcsolatos további információkért keresse fel a https: \/ /aka.MS/SS-8000-Network-reqs. Ha Azure Government felhőt használ, keresse fel az URL-mintákat a https: \/ /aka.MS/ss8000-gov-Network-reqs.|

### <a name="performance-alerts"></a>Teljesítmény-riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Az eszköz terhelése túllépte <*küszöbértéket*>. |A vártnál lassabb válaszidő. |Az eszköz egy nagy mennyiségű bemeneti/kimeneti terhelés alatt jelenti a kihasználtságot. Ez azt eredményezheti, hogy az eszköz nem működik, illetve. Tekintse át az eszközhöz csatolt munkaterheléseket, és állapítsa meg, hogy vannak-e olyanok, amelyek áthelyezhetők egy másik eszközre, vagy amelyekre már nincs szükség.|
| Nem lehetett elindítani a StorSimple szolgáltatás (oka) t. |DataPath hiba |Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |

### <a name="security-alerts"></a>Biztonsági riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Microsoft ügyfélszolgálata munkamenet megkezdődött. |Harmadik féltől származó, elért támogatási munkamenet. |Ellenőrizze, hogy a hozzáférés engedélyezve van-e. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról. |
| A <> *elemhez* tartozó jelszó <> *időtartamában* lejár. |A jelszó lejárata közeledik. |A jelszó módosítása a lejárat előtt. |
| Hiányzó biztonsági konfigurációs adatok a <*elem azonosítójának*>. | |A mennyiségi tárolóhoz társított kötetek nem használhatók a StorSimple-konfiguráció replikálásához. Az adatai biztonságos tárolásának biztosítása érdekében javasoljuk, hogy törölje a mennyiségi tárolót és a kötet-tárolóhoz társított köteteket. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról. |
| <> sikertelen bejelentkezési kísérletek *száma* <*elem-azonosító*>. |Több sikertelen bejelentkezési kísérlet. |Előfordulhat, hogy az eszköz támadás alatt áll, vagy a felhasználó helytelen jelszóval próbál csatlakozni.<ul><li>Forduljon a jogosult felhasználókhoz, és ellenőrizze, hogy ezek a kísérletek megbízható forrásból származnak-e. Ha továbbra is nagy számú sikertelen bejelentkezési kísérletet lát, érdemes lehet letiltani a távoli felügyeletet és kapcsolatba lépni a hálózati rendszergazdával. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról.</li><li>Győződjön meg arról, hogy a Snapshot Manager példányok a megfelelő jelszóval vannak konfigurálva. A megfelelő művelet elvégzése után törölje a riasztást a riasztások lapról.</li></ul>További információért lépjen a [lejárt eszköz jelszavának módosítása](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password)elemre. |
| Egy vagy több hiba történt a szolgáltatásban tárolt adattitkosítási kulcs módosításakor. | |Hiba történt a szolgáltatási adattitkosítási kulcs módosításakor. Miután megoldotta a hibák feltételeit, futtassa a `Invoke-HcsmServiceDataEncryptionKeyChange` parancsmagot az eszközön található StorSimple Windows PowerShell-felületéről a szolgáltatás frissítéséhez. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához. A probléma megoldása után törölje a riasztást a riasztások lapról. |

### <a name="support-package-alerts"></a>Támogatási csomagra vonatkozó riasztások

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| Nem sikerült létrehozni a támogatási csomagot. |A StorSimple nem tudta előállítani a csomagot. |Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. A probléma megoldása után törölje a riasztást a riasztások lapról. |

### <a name="enclosure-environment-alerts"></a>Bekerítési környezet riasztásai

| Riasztás szövege | Esemény | További információ/javasolt műveletek |
|:--- |:--- |:--- |
| A hardver-összetevő környezeti hőmérséklet-érzékelője nem sikerült állapotot jelez.  | Ház típusa: fő ház | Ez a riasztás akkor aktiválódik, ha a StorSimple körüli környezeti környezet egy elfogadható tartomány fölé esik. Győződjön meg róla, hogy az adatközpontban az AC-szellőzőn kívül a környezeti környezet vagy a légáram található. Ha a hőmérséklet visszatér a normál értékre, a rendszer automatikusan törli a riasztást egy kis idő elteltével. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.   |

## <a name="next-steps"></a>Következő lépések

További információ a [StorSimple hibákról és az eszközök telepítési problémáinak elhárításáról](storsimple-8000-troubleshoot-deployment.md).
