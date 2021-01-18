---
title: Érzékelők beléptetése és kezelése a Defender for IoT portálon
description: Ismerje meg, hogyan végezheti el az érzékelők bevezetését, megtekintését és kezelését a Defender for IoT portálon.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/27/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 427ea3884a3db6ba33405014435cf1f962670064
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562709"
---
# <a name="onboard-and-manage-sensors-in-the-defender-for-iot-portal"></a>Érzékelők beléptetése és kezelése a Defender for IoT portálon

Ez a cikk bemutatja, hogyan végezheti el az érzékelők bevezetését, megtekintését és kezelését a [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

## <a name="onboard-sensors"></a>Érzékelők előkészítése

Az érzékelők bevezetését az Azure Defender IoT való regisztrálásával és az érzékelő aktiválási fájljának letöltésével végezheti el.

### <a name="register-the-sensor"></a>Az érzékelő regisztrálása

Regisztráció:

1. Nyissa meg **a** [IoT portál](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)kezdőlapját.
1. Válassza ki az előkészítő **érzékelőt**.
1. Hozzon létre egy érzékelő nevét. Javasoljuk, hogy adja meg a név részeként telepített érzékelő IP-címét, vagy használjon egy könnyen azonosítható nevet. Így könnyebben nyomon követhető és konzisztens az Azure [Defender for IoT portál](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) regisztrációs neve és az érzékelő konzolon megjelenő üzembe helyezett érzékelő IP-címe.
1. Az érzékelő hozzárendelése egy Azure-előfizetéshez.
1. Válassza ki az érzékelő felügyeleti módot a **felhőalapú csatlakoztatott** váltógomb használatával. Ha a váltógomb be van kapcsolva, az érzékelő felhőhöz csatlakozik. Ha a váltógomb ki van kapcsolva, a rendszer helyileg felügyeli az érzékelőt.

   - **Felhőalapú csatlakoztatott érzékelők**: az érzékelő által észlelt információk az érzékelő konzolján jelennek meg. A riasztási adatokat egy IoT hub továbbítja, és más Azure-szolgáltatásokkal, például az Azure Sentinelrel is megosztható.

   - **Helyileg felügyelt érzékelők**: az érzékelők észlelésére szolgáló információk az érzékelő konzolon jelennek meg. Ha gapped hálózaton dolgozik, és szeretné, hogy a több helyileg felügyelt érzékelők által észlelt összes információ egységes legyen, működjön együtt a helyszíni felügyeleti konzollal.

   A felhőhöz csatlakoztatott érzékelők esetében az előkészítés során megadott név az érzékelő konzolján megjelenő név. Ez a név nem módosítható közvetlenül a konzolról. A helyileg felügyelt érzékelők esetében az előkészítés során alkalmazott név az Azure-ban lesz tárolva, és az érzékelő konzolján is frissíthető.

1. Válasszon egy IoT hubot, amely átjáróként szolgál az érzékelő és az Azure Defender for IoT között.
1. Ha az érzékelő felhőhöz csatlakozik, társítsa egy IoT hubhoz, majd adja meg a hely nevét és a zónát. Emellett leíró címkéket is hozzáadhat. A hely neve, a zóna és a címkék leíró bejegyzéseket mutatnak a [helyek és érzékelők lapon](#view-onboarded-sensors).

### <a name="download-the-sensor-activation-file"></a>Az érzékelő aktiválási fájljának letöltése

Az érzékelő aktivációs fájlja az érzékelő felügyeleti módjára vonatkozó utasításokat tartalmazza. A rendszer minden egyes üzembe helyezett érzékelőhöz letölt egy egyedi aktiválási fájlt. Az a felhasználó, aki első alkalommal bejelentkezik az érzékelő konzolra, feltölti az aktiválási fájlt az érzékelőbe.

Aktiválási fájl letöltése:

1. A **fedélzeti érzékelő** lapon válassza az **aktiválási fájl letöltése** lehetőséget.
1. Tegye elérhetővé a fájlt azon felhasználó számára, aki első alkalommal jelentkezik be az érzékelő konzolba.

## <a name="view-onboarded-sensors"></a>Beépített érzékelők megtekintése

A [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)megtekintheti a beépített érzékelőkre vonatkozó alapvető információkat. 

1. Válassza **a helyek és érzékelők** lehetőséget.
1. A **helyek és érzékelők** lapon a szűrő-és keresési eszközök segítségével megkeresheti a szükséges szenzor-információkat.

Az elérhető információk a következők:

- Hány érzékelők lettek bevezetve
- A felhőhöz csatlakoztatott és helyileg felügyelt érzékelők száma
- A beépített érzékelőhöz társított hub
- Az érzékelővel kapcsolatos részletek, például az érzékelőhöz a bevezetéskor hozzárendelt név, az érzékelőhöz társított zóna, illetve a címkékkel hozzáadott egyéb leíró információk.

## <a name="manage-onboarded-sensors"></a>Beépített érzékelők kezelése

Az érzékelőkkel kapcsolatos felügyeleti feladatokhoz használja a [Defender for IoT portált](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) .

### <a name="export"></a>Exportálás

A beépített érzékelő adatainak exportálásához válassza az **Exportálás** ikont a **helyek és érzékelők** oldal tetején.

### <a name="edit"></a>Szerkesztés

A **helyek és érzékelők** szerkesztési eszközeivel adhatja hozzá és szerkesztheti a hely nevét, a zónát és a címkéket.

### <a name="delete"></a>Törlés

Ha töröl egy felhőhöz csatlakoztatott érzékelőt, a rendszer nem továbbítja az adatokat az IoT hubhoz. Törölje a helyileg csatlakoztatott érzékelőket, ha már nem dolgozik velük.

Érzékelő törlése:

1. Válassza ki a törölni kívánt érzékelő három pontot (**..**.). 
1. Erősítse meg a törlési szándékát.

### <a name="reactivate"></a>Újraaktiválás

Előfordulhat, hogy frissíteni szeretné az érzékelő által felügyelt módot. Például:

- **Helyileg felügyelt mód helyett felhőalapú csatlakozású módban dolgozhat**: ehhez frissítse a helyileg csatlakoztatott érzékelőhöz tartozó aktiválási fájlt egy felhőalapú érzékelőhöz tartozó aktiválási fájllal. Az újraaktiválást követően az érzékelő és az [IoT-portál Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)-észlelései is megjelennek. Az újraaktiválási fájl sikeres feltöltése után a rendszer az újonnan észlelt riasztási adatokat küldi el az Azure-nak.

- **Helyileg csatlakoztatott módban dolgozhat a felhőalapú kapcsolat helyett**: ehhez frissítse az aktiválási fájlt egy felhőhöz csatlakoztatott érzékelőhöz egy, a helyileg felügyelt érzékelőhöz tartozó aktiválási fájllal. Az újraaktiválást követően az érzékelő észlelési adatai csak az érzékelőben jelennek meg.

- **Az érzékelő társítása egy új IoT-hubhoz**: ehhez újra regisztrálnia kell az érzékelőt, és fel kell töltenie egy új aktiválási fájlt.

Érzékelő újraaktiválása:

1. Lépjen a **Sites and érzékelők** lapra a [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje az érzékelőt.

4. Az érzékelőt újra bevezetheti **a bevezetési oldalról** az új mód vagy egy új IoT hub között.

5. Töltse le az aktiválási fájlt az **aktiválási fájl letöltése** lapról.

6. Jelentkezzen be a Defender for IoT szenzor-konzolra.

7. Az érzékelő konzolon válassza a **Rendszerbeállítások** , majd az **újraaktiválás** lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Töltse fel az aktiválási fájlt az érzékelő újraaktiválásához.":::

8. Válassza a **feltöltés** lehetőséget, és válassza ki a mentett fájlt.

9. Válassza az **aktiválás** lehetőséget. 

## <a name="see-also"></a>Lásd még

[Az érzékelő aktiválása és beállítása](how-to-activate-and-set-up-your-sensor.md)
