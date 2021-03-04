---
title: Azure-beli Kinect ismert problémák és hibaelhárítás
description: Ismerkedjen meg az ismert problémákkal és a hibaelhárítási tippekkel, amikor az Azure Kinect DK-mel használja az Sensor SDK-t.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: hibaelhárítás, frissítés, hiba, Kinect, visszajelzés, helyreállítás, naplózás, tippek
ms.openlocfilehash: 5f13815b8f8b26f6a08da28181a4a6164b7b89a3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038820"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure-beli Kinect ismert problémák és hibaelhárítás

Ez az oldal ismert problémákat és hibaelhárítási tippeket tartalmaz a Sensor SDK és az Azure Kinect DK használata esetén. Lásd még a terméktámogatási [lapokat](./index.yml) a termék hardver-specifikus problémáira.

## <a name="known-issues"></a>Ismert problémák

- Kompatibilitási problémák a ASMedia USB-gazdagép-vezérlőkkel (például ASM1142 lapkakészlet)
  - Bizonyos esetekben a Microsoft USB-illesztőprogram feloldja a blokkolást
  - Számos számítógép rendelkezik alternatív gazdagép-vezérlőkkel, és a USB3-port módosítása is segíthet

Az SDK-val kapcsolatos további problémákért forduljon a [GitHub-problémákhoz](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues) .

## <a name="collecting-logs"></a>Naplók összegyűjtése

A K4A.dll naplózása környezeti változókon keresztül engedélyezett. Alapértelmezés szerint a rendszer elküldi az stdout-ra, és csak a hibákat és a kritikus üzeneteket hozza létre. Ezek a beállítások módosíthatók úgy, hogy a naplózás egy fájlba váltson. A részletesség is igény szerint módosítható. Az alábbiakban egy példát láthat a Windowsra, amely lehetővé teszi a naplózást egy k4a. log nevű fájlba, és rögzíti a figyelmeztetési és a magasabb szintű üzeneteket.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Forgatókönyv futtatása a parancssorból (például indítás megjelenítője)
4. Navigáljon a k4a. log fájlhoz, és ossza meg a fájlt.

További információ: az alábbi klip a fejlécből:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

A Body Tracking SDK K4ABT.dll naplózása hasonló, kivéve, hogy a felhasználóknak módosítaniuk kell a környezeti változók különböző neveit:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Az eszköz nem sorolja fel az Eszközkezelőben

- Ellenőrizze az eszköz mögötti állapotot, ha a számítógép villog, és az USB-kapcsolat problémája van, és nem kap elég teljesítményt. A tápegység kábelét csatlakoztatni kell a megadott hálózati adapterhez. Amíg a tápkábel USB-csatlakozóval rendelkezik, az eszközön nagyobb teljesítményre van szükség, mint a számítógép USB-portjához. Tehát ne kapcsolódjon hozzá egy PC-porthoz vagy USB-hubhoz.
- Győződjön meg arról, hogy van csatlakoztatva az USB3 port, és használja az adatkapcsolatot.
- Próbálja meg módosítani az adatkapcsolat USB3-portját (javasolt, hogy az alaplaphoz közelebbi USB-portot használjon, például a számítógép hátulján).
- Győződjön meg arról, hogy a kábel, a sérült vagy az alacsonyabb minőségű kábelek megbízhatatlan enumerálást okoznak (az eszköz az Eszközkezelőben tartja a "villogást").
- Ha csatlakoztatva van a laptophoz, és akkumulátorról fut, akkor lehet, hogy a porton keresztül szabályozható a tápellátás.
- Indítsa újra a gazdagépet.
- Ha a probléma továbbra is fennáll, kompatibilitási probléma merülhet fel.
- Ha a hiba a belső vezérlőprogram frissítése során történt, és az eszköz önmagában nem lett helyreállítva, hajtsa végre a [gyári beállítások visszaállítását](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Az Azure Kinect Viewer nem nyitható meg

- Először győződjön meg arról, hogy az eszköz enumerálása a Windows Eszközkezelőban.

    ![Azure Kinect-kamerák a Windows Device Managerben](./media/resources/viewer-fails.png)

- Ellenőrizze, hogy van-e más alkalmazás az eszköz használatával (például Windows-kamera alkalmazás). Egyszerre csak egy alkalmazás férhet hozzá az eszközhöz.
- Olvassa el a k4aviewer. err naplófájlt a hibaüzenetek esetében.
- Nyissa meg a Windows Camera alkalmazást, és vizsgálja meg, hogy működik-e.
- A Power Cycle-eszközön az eszköz használata előtt várjon, amíg a streaming bekapcsolta a teljesítményt.
- Indítsa újra a gazdagépet.
- Győződjön meg arról, hogy a legújabb grafikus illesztőprogramokat használja a számítógépen.
- Ha saját SDK-buildet használ, próbálja meg a hivatalosan kiadott verziót használni, ha a probléma kijavítja a problémát.
- Ha a probléma továbbra is fennáll, [Gyűjtse össze a naplókat](troubleshooting.md#collecting-logs) és a fájlok visszajelzéseit.

## <a name="cannot-find-microphone"></a>Nem található a mikrofon

- Először győződjön meg arról, hogy a mikrofon-tömb enumerálása Eszközkezelőban történik.
- Ha egy eszköz enumerálása és másképpen működik megfelelően a Windowsban, a probléma az lehet, hogy a belső vezérlőprogram-frissítés után a Windows különböző tároló-azonosítót rendelt a részletes kamerához.
- Próbálja meg alaphelyzetbe állítani a Eszközkezelő, kattintson a jobb gombbal az "Azure Kinect mikrofon Array" elemre, és válassza az "eszköz eltávolítása" lehetőséget. Ha a művelet befejeződött, válassza le és csatlakoztassa újra az érzékelőt.

    ![Azure Kinect MIC-tömb](./media/resources/mic-not-found.png)

- Ezután indítsa újra az Azure Kinect Viewert, és próbálkozzon újra.

## <a name="device-firmware-update-issues"></a>Eszköz belső vezérlőprogram-frissítésével kapcsolatos problémák

- Ha a frissítés után nem történt meg a helyes verziószám, előfordulhat, hogy az eszközt újra kell indítania.
- Ha a belső vezérlőprogram frissítése megszakad, előfordulhat, hogy a rendszer rossz állapotba kerül, és nem tud enumerálni. Válassza le és csatlakoztassa újra az eszközt, és várjon 60 másodpercet, és ellenőrizze, hogy képes-e helyreállítani.
Ha nem, akkor hajtsa végre a [gyári beállítások visszaállítását](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Képminőséggel kapcsolatos problémák

- Indítsa el az [Azure Kinect Viewert](azure-kinect-viewer.md) , és ellenőrizze az eszköz elhelyezését interferencia esetén, vagy ha az érzékelő blokkolva van, vagy a lencse inkonzisztens.
- Ha a probléma adott módban történik, próbálja meg a különböző működési módokat leszűkíteni.
- A képminőséggel kapcsolatos problémák a csapattal való megosztásához a következőket teheti:

1) Az [Azure Kinect Viewer](azure-kinect-viewer.md) szüneteltetési nézetének elkészítése és képernyőkép készítése
2) Felvétel készítése az [Azure Kinect Recorder](azure-kinect-recorder.md)használatával, például: `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Inkonzisztens vagy váratlan eszköz időbélyegei

```k4a_device_set_color_control```A hívás átmenetileg előidézheti az eszköz időzítésének változásait, ami eltarthat néhány rögzítést a stabilizáláshoz. Kerülje az API meghívását a rendszerkép-rögzítési ciklusban, így elkerülhető a belső időzítési számítás alaphelyzetbe állítása minden új képpel. Ehelyett hívja meg az API-t a kamera elindítása előtt, vagy csak akkor, ha módosítani szeretné az értéket a rendszerkép-rögzítési hurokon belül. Különösen Kerülje a meghívást ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>USB3-állomásvezérlő kompatibilitása

Ha az eszköz nem sorolja fel az Eszközkezelőt, annak oka az lehet, hogy a rendszer nem támogatott USB3-vezérlőhöz csatlakozik. 

Az Azure Kinect DK a **Windows, az Intel**, a **Texas Instruments (ti)** és a **Renesas** az *egyetlen támogatott gazdagép-vezérlő*. Az Azure Kinect SDK a Windows-platformokon egy egyesített tároló-AZONOSÍTÓra támaszkodik, és az USB 2,0-es és 3,0-es eszközökön is át kell terjednie, hogy az SDK megtalálja az ugyanazon az eszközön fizikailag található mélység-, szín-és hangeszközöket. Linux rendszeren több gazdagép-vezérlő is támogatott, mivel ez a platform a tároló-AZONOSÍTÓnál kevesebbet, az eszköz sorozatszámait pedig többet használ. 

Az USB-állomásvezérlő témája még bonyolultabbá válik, ha egy számítógépen egynél több állomásvezérlő van telepítve. Ha a gazdagép-vezérlők vegyesen működnek, a felhasználók olyan problémákat tapasztalhatnak, amelyekben bizonyos portok nem működnek megfelelően, és más nem működik. Attól függően, hogy a portok hogyan vannak vezetékesek az esethez, az összes, az Azure Kinect szolgáltatással kapcsolatban felmerülő portot látni lehet

**Windows:** Annak megállapítása, hogy milyen állomásvezérlő van megnyitva Eszközkezelő

1. Nézet – > eszközök típus szerint 
2. Az Azure Kinect-kapcsolattal rendelkező kamerák kiválasztása – >Azure Kinect 4K-kamera
3. > eszközök megtekintése a kapcsolatok alapján

![USB-port hibaelhárítása](./media/resources/usb-troubleshooting.png)

Ha jobban meg szeretné tudni, hogy melyik USB-port csatlakozik a SZÁMÍTÓGÉPéhez, ismételje meg ezeket a lépéseket minden USB-porthoz, amikor az Azure Kinect DK-t csatlakoztatja a számítógép különböző USB-portjaihoz.

## <a name="depth-camera-auto-powers-down"></a>Részletes kamera automatikus kikapcsolási képességei

A mélységi kamera által a képmélységi adatok kiszámításához használt lézer korlátozott élettartammal rendelkezik. A lézerek élettartamának maximalizálása érdekében a részletes kamera felismeri a mélységi adatmennyiséget. A mélységi kamera leáll, ha az eszköz több percig áramlik be, de a gazdaszámítógép nem olvassa be az adatgyűjtést. Emellett hatással van a többeszközes szinkronizálásra is, ahol az alárendelt eszközök olyan állapotban kezdődnek, ahol a mélységi kamera adatfolyam-és mélységmérő Ha el szeretné kerülni ezt a problémát a többeszközes rögzítési forgatókönyvekben, győződjön meg arról, hogy a Főeszköz az első elinduláskor egy percen belül elindul. 

## <a name="using-body-tracking-sdk-with-unreal"></a>A Body Tracking SDK használata az Unreal használatával

Ha az Unreal-mel szeretné használni a Body Tracking SDK-t, győződjön meg róla, hogy hozzáadta `<SDK Installation Path>\tools` a környezeti változóhoz, és `PATH` másolja azt `dnn_model_2_0.onnx` `cudnn64_7.dll` `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Az Azure Kinect használata a fej nélküli Linux rendszeren

A Linuxon futó Azure Kinect mélységmérő-motor az OpenGL-t használja. Az OpenGL működéséhez szükség van egy Windows-példányra, amelyhez a figyelőnek csatlakoznia kell a rendszerhez. A probléma megkerülő megoldás:

1. Engedélyezze az automatikus bejelentkezést a használni kívánt felhasználói fiókhoz. Az automatikus bejelentkezés engedélyezésével kapcsolatos utasításokért tekintse meg [ezt](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) a cikket.
2. Kapcsolja le a rendszerállapotot, válassza le a figyelőt, és kapcsolja be a rendszerállapotot. Az automatikus bejelentkezés kényszeríti az x kiszolgáló munkamenetének létrehozását.
2. Csatlakozzon SSH-n keresztül, és állítsa be a DISPLAY env változót `export DISPLAY=:0`
3. Indítsa el az Azure Kinect-alkalmazást.

A [xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) segédprogrammal azonnal zárolhatja a képernyőt az automatikus bejelentkezés után. Adja hozzá a következő parancsot az indítási alkalmazáshoz vagy a rendszerszintű szolgáltatáshoz:

`bash -c “xtrlock -b”` 

## <a name="missing-c-documentation"></a>Hiányzó C#-dokumentáció

Az Sensor SDK C# dokumentációja [itt](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html)található.

A Body Tracking SDK C# dokumentációja [itt](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html)található.

## <a name="next-steps"></a>Következő lépések

[További támogatási információk](support.md)