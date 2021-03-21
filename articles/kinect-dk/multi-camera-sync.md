---
title: Több Azure Kinect DK-eszköz szinkronizálása
description: Ez a cikk a több eszköz szinkronizálásának előnyeit, valamint a szinkronizálandó eszközök beállítását ismerteti.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, specs, Hardware, DK, képességek, mélység, szín, RGB, IMU, tömb, mélység, több, szinkronizálás
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039954"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Több Azure Kinect DK-eszköz szinkronizálása

Minden egyes Azure Kinect DK-eszköz 3,5 – mm szinkronizációs portot tartalmaz (**szinkronizálja** és **szinkronizálja**), amelyek segítségével több eszközt is összekapcsolhat. Az eszközök csatlakoztatása után a szoftver képes a közöttük lévő triggerek időzítésének koordinálására. 

Ez a cikk az eszközök csatlakoztatását és szinkronizálását ismerteti.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Több Azure Kinect DK-eszköz használatának előnyei

Számos oka lehet annak, hogy több Azure Kinect DK-eszközt használ, többek között az alábbiakat:

- Adja meg az elzáródásokat. Habár az Azure Kinect DK-adatátalakítások egyetlen képet hoznak létre, a két kamera (mélység és RGB) valójában egy kis távolságra esik. Az eltolás lehetővé teszi a elzáródást. Elzáródás akkor következik be, amikor egy előtér-objektum egy adott eszközön lévő két kamera valamelyikének egy részének nézetét blokkolja. Az eredményül kapott színes képen az előtér-objektum árnyékot dob a háttérben lévő objektumon.  
   Az alábbi ábrán például a bal oldali kamera látja a "P2" szürke képpontot. A fehér előtér-objektum azonban blokkolja a jobb oldali kamera IR-fényét. A jobb oldali kamera nem rendelkezik a "P2" adattal.  
   ![A diagram két olyan kamerát mutat be, amelyek ugyanazon a ponton vannak letiltva.](./media/occlusion.png)  
   A további szinkronizált eszközök biztosíthatják a bezárt adatokat.
- Objektumok vizsgálata három dimenzióban.
- Növelje a tényleges képkockák sebességét olyan értékre, amely nagyobb, mint 30 képkocka másodpercenként (FPS).
- Az azonos jelenetben található több 4K-os színes lemezkép rögzítése, amely a kitettség középpontjának 100-es másodpercenkénti részén van igazítva &mu; .
- Növelje a kamera lefedettségét a tárhelyen belül.

## <a name="plan-your-multi-device-configuration"></a>A többszörös eszköz konfigurációjának megtervezése

Mielőtt elkezdené, győződjön meg róla, hogy áttekinti az [Azure Kinect DK hardveres specifikációit](hardware-specification.md) és az [Azure Kinect DK részletes kameráját](depth-camera.md).

> [!NOTE]  
> Távolítsa el a külső műanyag burkolatot, hogy elérhető legyen a szinkronizálás, és szinkronizálja a csatlakozókat.

### <a name="select-a-device-configuration"></a>Eszköz konfigurációjának kiválasztása

Az eszköz konfigurálásához a következő módszerek egyikét használhatja:

- **Daisy-Chain konfiguráció**. Egy fő eszköz és legfeljebb nyolc alárendelt eszköz szinkronizálása.  
   ![Diagram, amely bemutatja, hogyan csatlakoztathatók az Azure Kinect DK-eszközök egy Daisy-lánc konfigurációjában.](./media/multicam-sync-daisychain.png)
- **Csillagos konfiguráció**. Egy fő eszköz és legfeljebb két alárendelt eszköz szinkronizálása.  
   ![Diagram, amely bemutatja, hogyan állíthat be több Azure DK-eszközt egy csillag-konfigurációban.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Külső szinkronizálási trigger használata

Mindkét konfigurációban a Főeszköz biztosítja az indító jelet az alárendelt eszközökhöz. A szinkronizálási triggerhez azonban használhat egyéni külső forrást is. Ezt a lehetőséget használhatja például a képrögzítések más eszközökkel való szinkronizálására. A külső trigger forrás a Daisy-Chain konfigurációban vagy a csillag konfigurációjában csatlakozik a főeszközhöz.

A külső trigger forrásának ugyanolyan módon kell működnie, mint a Főeszköz. A következő jellemzőkkel rendelkező szinkronizálási jelet kell biztosítania:

- Aktív magas
- Impulzus szélessége: nagyobb, mint 8 &mu; s
- 5V TTL/CMOS
- Maximális kapacitás: nem kevesebb, mint 8 milliamper (mA)
- Frekvencia-támogatás: pontosan 30 FPS, 15 FPS és 5 FPS (a színes kamera fő VSYNC-jel gyakorisága)

Az trigger forrásának az 3,5-mm-es audiokábel használatával kell továbbítania a jelet a fő eszköz **szinkronizálásához** a porton. Használhat sztereó vagy monó kábelt is. Az Azure Kinect DK rövidnadrágban az audio-kábel összekötő összes ujja és gyűrűje össze van keverve. Ahogy az az alábbi ábrán is látható, az eszköz csak a szinkronizációs jelet fogadja az összekötő hegye alapján.

![Külső trigger-jel kábeles konfigurációi](./media/resources/camera-trigger-signal.jpg)

További információ a külső berendezésekkel való [használatról: az Azure Kinect Recorder használata külső szinkronizált eszközökkel](record-external-synchronized-units.md)

> [!NOTE]  
> A szinkronizálás ki az RGB-kamera VSync-je. Az összes eszköz időbélyege nulla értékre van állítva, és megszámolható. A Microsoft nem jellemezte a szinkronizálási impulzus minimális és maximális szélességét, és azt ajánlja, hogy az Azure Kinect DK-ből való szinkronizálás által generált impulzust utánozza.

### <a name="plan-your-camera-settings-and-software-configuration"></a>A kamera beállításainak és a szoftver konfigurációjának megtervezése

További információ arról, hogyan állíthatja be a szoftvert a kamerák vezérlésére és a képadatok használatára: [Azure Kinect Sensor SDK](about-sensor-sdk.md).

Ez a szakasz a szinkronizált eszközöket (de nem egyetlen eszközt) érintő tényezőket ismerteti. A szoftvernek meg kell fontolnia ezeket a tényezőket.

#### <a name="exposure-considerations"></a>Expozíciós megfontolások
Ha az egyes eszközök pontos időzítését szeretné vezérelni, javasoljuk, hogy használjon manuális expozíciós beállítást. Az automatikus expozíciós beállítás alatt minden egyes színes kamera képes dinamikusan módosítani a tényleges expozíciót. Mivel a kitettség hatással van az időzítésre, az ilyen változások gyorsan leküldik a kamerákat a szinkronizálásból.

A rendszerkép-rögzítési hurokban ne állítsa többször ugyanazt az expozíciós beállítást. Csak egyszer hívja meg az API-t, amikor szükséges.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Több mélységű kamera közötti interferencia elkerülése

Ha több mélységi kamera is átfedésben lévő nézeteket ábrázol, minden kamerának saját társított lézert kell megadnia. Annak megakadályozása érdekében, hogy a lézerek egymással zavarják egymást, a kamera rögzítését a 160 μs vagy újabb értékkel kell ellensúlyozni.

Minden egyes részletes kamera-rögzítés esetében a lézer kilenc alkalommal fordul elő, és minden alkalommal aktív 125 s-ra &mu; . A lézer a &mu; művelet módjától függően a 14505 s vagy a 23905 s esetében üresjáratban van &mu; . Ez azt jelenti, hogy az eltolási számítás kiindulási pontja 125 &mu; s.

Emellett a kamera órája és az eszköz belső vezérlőprogram órája közötti különbségek megnövelhetik a minimális eltolást 160 s értékre &mu; . A konfiguráció pontosabb eltolásának kiszámításához jegyezze fel a használt mélységi módot, és tekintse meg a [mélységi érzékelő nyers időzítési táblázatát](hardware-specification.md#depth-sensor-raw-timing). A táblázatból származó adatok használatával kiszámíthatja a minimális eltolást (az egyes kamera expozíciós idejét) a következő egyenlet használatával:

> *Expozíciós idő* = (*IR Pulse* &times; *Pulse width*) + (*üresjárati időszakok* &times; *üresjárati ideje*)

Ha 160 s-eltolást használ &mu; , akár kilenc további részletes kamerát is beállíthat, hogy minden lézer bekapcsoljon, miközben a többi lézer tétlen marad.

A szoftverben a vagy a használatával győződjön meg arról, ```depth_delay_off_color_usec``` ```subordinate_delay_off_master_usec``` hogy az egyes IR lézeres tüzek a saját 160 &mu; s ablakban vannak, vagy más típusú nézetben vannak.

> [!NOTE]  
> A tényleges Pulse szélessége 125us, azonban 160us, hogy bizonyos mozgásteret biztosítson. Ha például NFOV UNBINNED, az egyes 125us impulzusokat a 1450us üresjárat követi. Ezek összesen – (9 x 125) + (8 x 1450) – a rendszer a 12.8 MS expozíciós idejét eredményezi. A szekrényben a 2 eszköz beléptetése is megmarad, hogy a második kamera első impulzusa az első kamera első üresjárati időszaka alá kerüljön. Az első és második kamera közötti késleltetés akár 125us is lehet (egy impulzus vastagsága), de azt javasoljuk, hogy a 160us. Megadott 160us legfeljebb 10 kamera expozíciós időszakait lehet összekapcsolni.

## <a name="prepare-your-devices-and-other-hardware"></a>Az eszközök és egyéb hardverek előkészítése

Több Azure Kinect DK-eszközön kívül előfordulhat, hogy további gazdagép-számítógépeket és egyéb hardvereket kell beszereznie, hogy támogassa a létrehozni kívánt konfigurációt. Az ebben a szakaszban található információk alapján győződjön meg arról, hogy az összes eszköz és hardver készen áll a beállítás megkezdése előtt.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK-eszközök

Minden szinkronizálni kívánt Azure Kinect DK-eszköz esetében tegye a következőket:

- Győződjön meg arról, hogy a legújabb belső vezérlőprogram telepítve van az eszközön. Az eszközök frissítésével kapcsolatos további információkért keresse fel az [Azure Kinect DK belső vezérlőprogram frissítése](update-device-firmware.md)című témakört. 
- Távolítsa el az eszköz fedelét a szinkronizálási portok megjelenítéséhez.
- Jegyezze fel az egyes eszközök sorozatszámát. Ezt a számot később fogja használni a telepítési folyamat során.

### <a name="host-computers"></a>Gazdagép számítógépek

Az egyes Azure Kinect DK-alkalmazások általában a saját gazdagépét használják. Az eszköz használatának módjától és az USB-kapcsolaton keresztüli adatátviteltől függően dedikált gazdagép-vezérlőt is használhat. 

Győződjön meg arról, hogy az Azure Kinect Sensor SDK telepítve van az egyes gazdagépeken. Az Sensor SDK telepítésével kapcsolatos további információkért nyissa meg a rövid útmutató [: Azure Kinect DK beállítása](set-up-azure-kinect-dk.md)című témakört. 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux rendszerű számítógépek: USB-memória Ubuntu rendszeren

Alapértelmezés szerint a Linux-alapú gazdagépek az USB-vezérlőt csak 16 MB kernel memóriával osztják le az USB-átvitelek kezeléséhez. Ez az összeg általában elegendő egyetlen Azure Kinect DK támogatásához. A több eszköz támogatásához azonban az USB-vezérlőnek több memóriával kell rendelkeznie. A memória növeléséhez kövesse az alábbi lépéseket:

1. Szerkesztés/**etc/default/grub**.
1. Keresse meg a következő sort:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Cserélje le a következő sor használatával:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Ezek a parancsok az USB-memóriát 32 MB-ra állítja be. Ez egy példa az alapértelmezett érték két alkalommal történő beállítására. Sokkal nagyobb értéket is beállíthat, a megoldásnak megfelelően.
1. Futtassa a **sudo update-grub** parancsot.
1. Indítsa újra a számítógépet.

### <a name="cables"></a>Kábelek

Ha az eszközöket egymáshoz és a gazdagépekhez szeretné csatlakoztatni, a 3,5 mm-es dugasz-dugasz kábelt kell használnia (más néven 3,5-PP audiokábel). A kábelek kevesebb mint 10 méter hosszúak lehetnek, és sztereó vagy monó is lehet.

A szükséges kábelek száma a használt eszközök számától és az adott eszköz konfigurációjától függ. Az Azure Kinect DK Box nem tartalmaz kábeleket. Ezeket külön kell megvásárolnia.

Ha az eszközöket a csillag konfigurációjában kapcsolja össze, akkor egy fejhallgató-elosztóval is rendelkeznie kell.

## <a name="connect-your-devices"></a>Az eszközök csatlakoztatása

**Azure Kinect DK-eszközök csatlakoztatása egy Daisy-lánc konfigurációjában**

1. Az egyes Azure Kinect DK-ket kapcsolja össze.
1. Minden eszköz csatlakoztatása a gazdagép számítógéphez. 
1. Válassza ki az egyik eszközt a főeszközként, és csatlakoztassa a 3,5-mm-es hangkábelt a **szinkronizálási** porthoz.
1. Csatlakoztassa a kábel másik végét a **szinkronizálás** az első alárendelt eszköz portjához.
1. Egy másik eszköz csatlakoztatásához csatlakoztasson egy másik kábelt az első alárendelt eszköz **szinkronizálási** portjához, és a következő **eszköz portjába** .
1. Ismételje meg az előző lépést az összes eszköz csatlakoztatása előtt. Az utolsó eszköznek csak egy csatlakozókábel-kapcsolatban kell állnia. A **szinkronizálás kimenő** portjának üresnek kell lennie.

**Azure Kinect DK-eszközök csatlakoztatása egy csillag-konfigurációban**

1. Az egyes Azure Kinect DK-ket kapcsolja össze.
1. Minden eszköz csatlakoztatása a gazdagép számítógéphez. 
1. Válassza ki az egyik eszközt a fő eszközként, és csatlakoztassa a fejhallgató-elosztó egyetlen végét a **szinkronizálási** porthoz.
1. Csatlakoztasson 3,5-mm-es hangkábeleket a fejhallgató-elosztó "Split" végéhez.
1. Csatlakoztassa az egyes kábelek másik végét az egyik alárendelt eszköz **szinkronizálási** portjához.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Ellenőrizze, hogy az eszközök csatlakoztatva vannak-e és kommunikálnak-e

Az [Azure Kinect Viewer](azure-kinect-viewer.md)használatával ellenőrizheti, hogy az eszközök megfelelően vannak-e csatlakoztatva. Ismételje meg az eljárást úgy, hogy az összes alárendelt eszközt a fő eszközzel együtt tesztelje

> [!IMPORTANT]  
> Ehhez az eljáráshoz ismernie kell az egyes Azure Kinect DK sorozatszámát.

1. Nyissa meg az Azure Kinect Viewer két példányát.
1. Az **eszköz megnyitása** alatt válassza ki a tesztelni kívánt alárendelt eszköz sorozatszámát.  
   ![Eszköz megnyitása](./media/open-devices.png)
   > [!IMPORTANT]  
   > Ha az összes eszköz között pontos képrögzítési igazítást szeretne kapni, akkor a főeszközt utoljára kell elindítania.  
1. A **külső szinkronizálás** területen válassza a **Sub** elemet.  
   ![Alárendelt kamera kezdete](./media/sub-device-start.png)
1.  Válassza az **Indítás** elemet.  
    > [!NOTE]  
    > Mivel ez egy alárendelt eszköz, az Azure Kinect Viewer nem jelenít meg rendszerképet az eszköz elindítása után. Egyetlen rendszerkép sem jelenik meg addig, amíg az alárendelt eszköz nem kap szinkronizálási jelet a főeszköztől.
1. Az alárendelt eszköz elindítása után az Azure Kinect Viewer másik példányával nyissa meg a fő eszközt.
1. A **külső szinkronizálás** területen válassza a **főkiszolgáló** lehetőséget.
1. Válassza az **Indítás** elemet.

A fő Azure Kinect-eszköz indításakor az Azure Kinect Viewer mindkét példányának képeket kell megjelenítenie.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Az eszközök kalibrálása szinkronizált készletként

Miután meggyőződött róla, hogy az eszközök megfelelően kommunikálnak, készen áll a kalibrálásra, hogy lemezképeket hozzon létre egyetlen tartományban.

Egyetlen eszközön a részletes és az RGB-kamerák a gyári kalibrálással működnek együtt. Ha azonban több eszköznek együtt kell működnie, a rendszernek hitelesítenie kell, hogy megtudja, hogyan alakíthatja át a lemezképet a kamera azon tartományában, amely a lemezképek feldolgozásához használni kívánt kamera tartományára van rögzítve.

Több lehetőség is rendelkezésre áll az eszközök többre történő kalibrálására. A Microsoft biztosítja a [GitHub Green Screen Code mintát](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), amely a OpenCV metódust használja. A kód információs fájlja további részleteket és útmutatást nyújt az eszközök kalibrálásához.

További információ a kalibrálásról: az [Azure Kinect Calibration functions használata](use-calibration-functions.md).

## <a name="next-steps"></a>Következő lépések

A szinkronizált eszközök beállítása után azt is megtudhatja, hogyan használhatja a
> [!div class="nextstepaction"]
> [Azure Kinect Sensor SDK-rekord és lejátszási API](record-playback-api.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Tudnivalók az Azure Kinect Sensor SDK-ról](about-sensor-sdk.md)
- [Az Azure Kinect DK hardveres specifikációi](hardware-specification.md) 
- [Gyors útmutató: az Azure Kinect DK beállítása](set-up-azure-kinect-dk.md) 
- [Az Azure Kinect DK belső vezérlőprogram frissítése](update-device-firmware.md) 
- [Az Azure Kinect DK alaphelyzetbe állítása](reset-azure-kinect-dk.md) 
- [Azure Kinect Viewer](azure-kinect-viewer.md) 
