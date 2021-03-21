---
title: Az Azure IoT Hub-problémákkal kapcsolatos gyakori eszközök frissítésének hibaelhárítása | Microsoft Docs
description: Ez a dokumentum felsorolja azokat a tippeket és trükköket, amelyek segítenek a IoT Hub eszköz frissítésével kapcsolatos lehetséges problémák megoldásában.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030630"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Eszköz frissítése IoT Hub hibaelhárítási útmutató

Ez a dokumentum felsorolja a gyakori kérdéseket és az eszközök frissített felhasználói által jelentett problémákat. Mivel az eszköz frissítése a nyilvános előzetes verzióban halad át, ez a hibaelhárítási útmutató rendszeresen frissül az új kérdésekkel és megoldásokkal. Ha olyan problémába ütközik, amely nem jelenik meg ebben a hibaelhárítási útmutatóban, tekintse meg a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakaszt a helyzet dokumentálására.

## <a name="importing-updates"></a><a name="import"></a>Frissítések importálása

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>K: nem sikerül csatlakoztatni az eszköz frissítési példányát a IoT Hub-példányhoz.
_Győződjön meg arról, hogy a IoT Hub üzenet-útvonalak megfelelően vannak konfigurálva, az [eszköz frissítési erőforrásainak](./device-update-resources.md) dokumentációja szerint._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>K: szerepkörrel kapcsolatos hiba történt (Azure Portal vagy egy 403 API-hiba esetén hibaüzenet jelenik meg).
_Előfordulhat, hogy nem rendelkezik megfelelően konfigurált hozzáférési engedélyekkel. Győződjön meg arról, hogy a hozzáférési engedélyeket megfelelően konfigurálta az [eszköz frissítése hozzáférés-vezérlési](./device-update-control-access.md) dokumentációjában._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>K: 500 típusú hiba történt a tartalom eszköz-frissítési szolgáltatásba való importálásakor.
_Az 500-tartomány hibakódja az eszköz frissítési szolgáltatásával kapcsolatos problémát jelezhet. Várjon 5 percet, majd próbálkozzon újra. Ha ugyanez a hiba továbbra is fennáll, kövesse a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakasz utasításait a támogatási kérelem a Microsofttal való beírásához._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>K: a rendszer hibát észlelt a tartalom importálásakor, és elemezni szeretné.
_A hibakódok elemzésével kapcsolatos információkért tekintse meg az [eszköz Update-hibakódok](./device-update-error-codes.md) dokumentációját._

## <a name="device-failures"></a><a name="device-failure"></a>Eszköz meghibásodása

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>K: Hogyan biztosíthatom, hogy az eszköz csatlakoztatva legyen a IoT Hub eszköz frissítéséhez?
_Annak ellenőrzéséhez, hogy az eszköz csatlakoztatva van-e az eszköz frissítéséhez, ellenőrizze, hogy megjelenik-e a "nem csoportosított eszközök" szakasz a Azure Portal megfelelőség nézetében._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>K: egy vagy több eszköz frissítése sikertelen.
_Az eszköz frissítési hibájának számos kiváltó oka lehet. Ellenőrizze, hogy az eszköz: 1) csatlakoztatva van-e a IoT Hub-példányhoz, 2) az eszköz frissítési példányához, és 3) a kézbesítési optimalizálás (DO) szolgáltatás fut-e. Ha az eszköz mindhárom értéke igaz, kövesse a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakaszban található utasításokat a Microsofttal kapcsolatos támogatási kérelem beírásához._

## <a name="deploying-an-update"></a><a name="deploy"></a> Frissítés telepítése

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>K: Telepítettem az eszköz (ek) frissítését, de a megfelelőségi állapot azt mondja, hogy nem a legújabb frissítés. Mit tegyek?
_Az eszköz megfelelőségi állapota akár 5 percet is igénybe vehet. Várjon, majd ellenőrizze újra._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>K: az eszköz üzembe helyezési állapota nem kompatibilis, mi a teendő?
_Előfordulhat, hogy a célként megadott eszköz gyártója és modellje módosítva lett az eszköz IoT Hub való csatlakoztatása után, így az eszköz már nem kompatibilis az aktuális üzemelő példány frissítési tartalmával._

_Ellenőrizze az [adu Alapfelületét](./device-update-plug-and-play.md) , és tekintse meg, hogy az eszköz milyen gyártót és modellt jelent az eszköz frissítési szolgáltatásának, és ellenőrizze, hogy az megfelel-e az üzembe helyezett frissítési tartalom [importálási jegyzékfájljában](./import-concepts.md) megadott gyártónak és modellnek. Az adott eszköz tulajdonságait az [eszköz frissítése konfigurációs fájl](./device-update-configuration-file.md)használatával módosíthatja._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>K: látom az üzembe helyezést "aktív" fázisban, de egyik eszköz sem "folyamatban" van a frissítéssel. Mit tegyek?
_Győződjön meg arról, hogy a központi telepítés kezdő dátuma nincs beállítva a jövőben. Új központi telepítés létrehozásakor a rendszer alapértelmezés szerint a központi telepítési kezdési dátumot a következő napra védi, hacsak nem módosítja kifejezetten. Megvárhatja a központi telepítés kezdő dátumát, vagy megszakíthatja a folyamatban lévő telepítést, és létrehozhat egy új központi telepítést a kívánt kezdési dátummal._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>K: megpróbálom csoportosítani az eszközöket, de a csoport létrehozásakor nem látom a címkét a legördülő menüben.
_Győződjön meg arról, hogy megfelelően konfigurálta az üzenetek útvonalait a IoT Hub az [eszköz frissítési erőforrásainak](./device-update-resources.md) dokumentációjában. Az útvonal konfigurálása után újra fel kell címkéznie az eszközt._

_Egy másik alapvető ok lehet a címke alkalmazása, mielőtt csatlakoztatta az eszközt a IoT Hub eszköz frissítéséhez. Győződjön meg arról, hogy az eszköz már csatlakoztatva van az eszköz frissítéséhez. Annak ellenőrzéséhez, hogy az eszköz csatlakoztatva van-e az eszköz frissítéséhez IoT Hub ellenőrizze, hogy megjelenik-e a megfelelőség nézetben a "nem csoportosított" eszközök alatt. Ideiglenesen vegyen fel egy másik érték címkéjét, majd az eszköz csatlakoztatása után adja hozzá újra a kívánt címkét._

_Ha a Device kiépítési szolgáltatást (DPS) használja, akkor győződjön meg arról, hogy az eszközöket az üzembe helyezésük után címkézi, és nem az eszköz létrehozási folyamata során. Ha már megcímkézte az eszközt az eszköz létrehozása lépésben, akkor az eszköz kiépítés után átmenetileg fel kell címkéznie az eszközt egy másik értékkel, majd hozzá kell adnia a kívánt címkét._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>K: az üzembe helyezés sikeresen befejeződött, de néhány eszközt nem sikerült frissíteni.
_Lehetséges, hogy ezt egy ügyféloldali hiba okozta a hibás eszközökön. Tekintse meg a hibaelhárítási útmutató eszköz meghibásodások című szakaszát._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>K: hiba történt az UX-ben egy központi telepítés kezdeményezésére tett kísérlet során.
_Ezt okozhatja egy szolgáltatás/UX-hiba, vagy egy API-engedélyekkel kapcsolatos probléma. Ha támogatási kérést szeretne beküldeni a Microsofttal, kövesse a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakasz utasításait._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>K: elindítottam egy üzembe helyezést, de nem éri el a befejezési állapotot.
_Ezt okozhatta a szolgáltatás teljesítményével kapcsolatos hiba, a szolgáltatás hibája vagy az ügyfél hibája. 10 perc elteltével próbálja megismételni a telepítést. Ha ugyanezt a problémát tapasztalja, kérjük, olvassa el az eszköz naplóit, és tekintse meg a hibaelhárítási útmutató eszköz meghibásodások című szakaszát. Ha a probléma továbbra is fennáll, kövesse a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakasz utasításait a támogatási kérelem a Microsofttal való beküldéséhez._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Frissítések letöltése eszközökre

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>K: Hogyan a letöltést, ha egy eszköz újracsatlakozása egy leválasztási időszak után megszakadt?
_A letöltés önfolytatást eredményez, ha a kapcsolat 24 órás időszakon belül helyreáll. 24 óra elteltével a felhasználónak újra kell indítania a letöltést._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> A Microsoft csatlakoztatott gyorsítótár (MCC) használata

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>K: problémát tapasztalok, amikor az ÜGYFÉLKÖZPONT-modult az IoT Edge eszközön próbálja üzembe helyezni.
_Az Edge-modulok IoT Edge eszközökre való telepítéséhez tekintse meg az [IoT Edge dokumentációját]() . A alkalmazásban megtekintheti, hogy az MCC modul sikeresen fut-e a IoT Edge eszközön http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>K: az egyik IoT-eszköz egy frissítés letöltését kísérli meg az ÜGYFÉLKÖZPONTon keresztül, de sikertelen.
_Számos probléma merülhet fel, ami miatt előfordulhat, hogy egy IoT-eszköz nem tud csatlakozni az ÜGYFÉLKÖZPONThoz. A probléma diagnosztizálásához Gyűjtse össze az ügyfél-és Nginx-naplókat a hibás eszközről (lásd a [kapcsolatfelvételi Microsoft ügyfélszolgálata](#contact) szakaszt az ügyféloldali naplók összegyűjtésére vonatkozó utasításokért)._

_Előfordulhat, hogy az eszköz nem tud lekérni tartalmat az internetről, hogy átadja az ÜGYFÉLKÖZPONT-modulnak, mert a használt URL-cím nem engedélyezett. Ha meg szeretné állapítani, hogy van-e, ellenőrizze IoT Edge környezeti változóit a Azure Portalban._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Kapcsolatfelvétel Microsoft ügyfélszolgálata

Ha olyan problémákba ütközik, amelyek a fenti GYIK használatával nem oldhatók fel, az Azure Portal felületen keresztül egy támogatási kérést is megadhat Microsoft ügyfélszolgálata. Attól függően, hogy a probléma milyen kategóriába tartozik, a rendszer felkérheti a további adatok összegyűjtésére és megosztására, hogy segítsen Microsoft ügyfélszolgálata a probléma kivizsgálásában. 

Az egyes adattípusok gyűjtésével kapcsolatos információkért lásd alább. A [getDevices]() segítségével további információkat is megtudhat az API hasznos adatokkal kapcsolatos válaszában.

Emellett a következő információk is hasznosak lehetnek a probléma kiváltó okának szűkítése érdekében:
* A frissíteni kívánt eszköz típusa (Azure Percept, IoT Edge átjáró, egyéb)
* Az eszköz frissítési ügyfelének típusa (rendszerkép-alapú, csomag-alapú, szimulátor)
* Az eszközön futó operációs rendszer
* Az eszköz architektúráját érintő részletek
* Azt jelzi, hogy sikeresen használta-e az eszközt az eszköz frissítéséhez

Ha a fenti információk bármelyike elérhető, kérjük, adja meg a probléma leírását.

### <a name="collecting-client-logs"></a>Az ügyfél naplóinak összegyűjtése

* A málna PI-eszközön két napló található:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* A csomagolt ügyfél esetében a naplók itt találhatók:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* A szimulátorhoz a következő naplók találhatók:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Hibakódok
Előfordulhat, hogy a rendszer a frissítések importálásával, az eszköz meghibásodásával vagy a frissítések telepítésével kapcsolatos problémák bejelentése esetén hibakódot kér.

A hibakódok a [ADUCoreInterface](./device-update-plug-and-play.md) felülettel szerezhetők be. Az öndiagnosztika és a hibaelhárítási hibakódok elemzését az [eszköz frissítése hibakód](./device-update-error-codes.md) dokumentációjában találja.

### <a name="trace-id"></a>Nyomkövetési azonosító
Előfordulhat, hogy a rendszer egy nyomkövetési azonosítót ad meg a frissítés importálásával vagy telepítésével kapcsolatos probléma jelentéséért.

Egy adott felhasználói művelet nyomkövetési azonosítója az API-válaszban vagy az Azure Portal felhasználói felület importálási előzmények szakaszában található. 

Jelenleg a telepítési műveletek nyomkövetési azonosítói csak az API-válaszon keresztül érhetők el.

### <a name="deployment-id"></a>Központi telepítés azonosítója
Előfordulhat, hogy egy frissítés telepítésével kapcsolatos probléma bejelentéséhez meg kell adnia egy központi telepítési azonosítót.

A központi telepítési azonosítót a felhasználó hozza létre, amikor az API meghívásával kezdeményezi a központi telepítést.

Jelenleg a Azure Portal felhasználói felületről kezdeményezett központi telepítések központi telepítési azonosítói automatikusan létrejönnek, és nem kerülnek a felhasználóhoz.

### <a name="iot-hub-instance-name"></a>IoT Hub példány neve
Előfordulhat, hogy a rendszer megkéri, hogy adja meg a IoT Hub-példány nevét, amikor az eszköz meghibásodásával kapcsolatos problémát jelez, vagy egy frissítést telepít.

Az IoT Hub nevet a felhasználó választja ki az első kiépítés során.

### <a name="device-update-account-name"></a>Eszköz frissítési fiókjának neve
Előfordulhat, hogy az eszköz frissítési fiókjának nevét kell megadnia egy frissítés importálásával, az eszköz meghibásodásával vagy egy frissítés telepítésével kapcsolatos probléma bejelentéséhez.

Az eszköz frissítési fiókjának nevét a felhasználó választja ki, amikor először regisztrál a szolgáltatásra. További információ az [eszköz frissítési erőforrások](./device-update-resources.md) dokumentációjában található.

### <a name="device-update-instance-name"></a>Eszköz frissítési példányának neve
Előfordulhat, hogy a rendszer arra kéri, hogy adja meg az eszköz frissítési példányának nevét, amikor a frissítés, az eszköz meghibásodása vagy a frissítés telepítése miatti probléma bejelentését végzi.

Az eszköz frissítési példányának nevét a felhasználó választja ki az első kiépítés során. További információ az [eszköz frissítési erőforrások](./device-update-resources.md) dokumentációjában található.

### <a name="device-id"></a>Eszközazonosító
Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy eszköz-azonosítót, ha az eszköz meghibásodásával kapcsolatos problémát jelez, vagy egy frissítést telepít.

Az eszköz AZONOSÍTÓját az ügyfél határozza meg, ha az eszközt először kiépítik. Lekérhető az eszköz különálló eszközéről is.

### <a name="update-id"></a>Frissítési azonosító
Előfordulhat, hogy a frissítés telepítésével kapcsolatos probléma bejelentéséhez meg kell adnia egy frissítési azonosítót.

A frissítés AZONOSÍTÓját az ügyfél határozza meg a központi telepítés indításakor.

### <a name="nginx-logs"></a>Nginx-naplók
Előfordulhat, hogy a Microsoft által csatlakoztatott gyorsítótárral kapcsolatos probléma bejelentéséhez meg kell adnia Nginx-naplókat.

### <a name="adu-conftxt"></a>ADU-conf.txt
Előfordulhat, hogy meg kell adnia az eszköz frissítési konfigurációs fájlját ("adu-conf.txt"), amikor egy frissítés telepítésével kapcsolatos problémát jelent.

A konfigurációs fájl nem kötelező, és a felhasználó hozza létre az [eszköz frissítése konfigurációs](./device-update-configuration-file.md) dokumentáció utasításait követve.

### <a name="import-manifest"></a>Jegyzék importálása
Előfordulhat, hogy a rendszer a frissítés importálásával vagy telepítésével kapcsolatos probléma bejelentése esetén megkéri az importálási jegyzékfájl megadását.

Az importálási jegyzékfájl az ügyfél által a frissítési tartalom az eszköz frissítési szolgáltatásba történő importálásakor létrehozott fájl.

**[Következő lépés: További információ az eszköz frissítési hibakódokról](.\device-update-error-codes.md)**