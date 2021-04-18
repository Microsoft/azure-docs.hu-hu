---
title: Érzékelők kezelése a Defender for IoT portálon
description: Megtudhatja, hogyan lehet érzékelőket bevetni, megtekinteni és kezelni a Defender for IoT portálon.
ms.date: 4/18/2021
ms.topic: how-to
ms.openlocfilehash: 2c948aa2387552f9815ab075abb43c98307ae087
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600182"
---
# <a name="manage-sensors-ain-the-defender-for-iot-portal"></a>Érzékelők kezelése a Defender for IoT portálon

Ez a cikk az érzékelők a [Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)portálon való be- és megtekintését és kezelését ismerteti.

## <a name="onboard-sensors"></a>Érzékelők előkészítése

Az érzékelő regisztrálásával regisztrálja az érzékelőt a Azure Defender for IoT és letölt egy érzékelőaktiválási fájlt.

### <a name="register-the-sensor"></a>Az érzékelő regisztrálása

Regisztráció:

1. A Defender for IoT **portálon** kattintson a [Kezdőlapra.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Válassza **az Onboard sensor lehetőséget.**
1. Hozzon létre egy érzékelőnevet. Javasoljuk, hogy a név részeként foglalja bele a telepített érzékelő IP-címét, vagy használjon könnyen azonosítható nevet. Ez biztosítja az Azure [Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) portálon a regisztráció neve és az érzékelő konzolján megjelenített üzembe helyezett érzékelő IP-címe közötti könnyebb követést és konzisztens elnevezést.
1. Társítsa az érzékelőt egy Azure-előfizetéshez.
1. Válasszon egy érzékelőkezelési módot a **Felhőhöz** csatlakoztatott váltógomb használatával. Ha a váltógomb be van kapcsolva, az érzékelő a felhőhöz csatlakozik. Ha a váltógomb ki van kapcsolva, az érzékelő helyileg lesz felügyelt.

   - **Felhőhöz csatlakoztatott érzékelők:** Az érzékelő által észlelt információk megjelennek az érzékelő konzolján. A riasztási adatok egy IoT Hubon keresztül lesznek kézbesítve, és megoszthatóak más Azure-szolgáltatásokkal, például az Azure Sentinel.

   - **Helyileg felügyelt érzékelők:** Az érzékelők által észlelt információk megjelennek az érzékelő konzolján. Ha egy air-gaped hálózatban dolgozik, és szeretné egységes nézetben megtekinteni a több helyileg felügyelt érzékelő által észlelt összes információt, működjön együtt a helyszíni felügyeleti konzollal.

   A felhőhöz csatlakoztatott érzékelők számára az be- és bekapcsolás során meghatározott név az érzékelő konzolján megjelenő név. Ezt a nevet nem módosíthatja közvetlenül a konzolról. A helyileg felügyelt érzékelők esetén a telepítés során alkalmazott név az Azure-ban lesz tárolva, és frissíthető az érzékelő konzolján.

1. Válasszon egy IoT Hubot, amely átjáróként szolgál az érzékelő és a Azure Defender for IoT.
1. Ha az érzékelő felhőhöz csatlakozik, társítsa egy IoT Hubhoz, majd adja meg a hely nevét és zónáját. Leíró címkéket is hozzáadhat. A webhely neve, zónája és címkéi leíró bejegyzések a Helyek és érzékelők [oldalon.](#view-onboarded-sensors)

### <a name="download-the-sensor-activation-file"></a>Az érzékelőaktiválási fájl letöltése

Az érzékelőaktiválási fájl az érzékelő kezelési módjára vonatkozó utasításokat tartalmaz. Minden üzembe helyezett érzékelőhöz le kell töltenie egy egyedi aktiválási fájlt. A felhasználó, aki először jelentkezik be az érzékelő konzoljára, feltölti az aktiválási fájlt az érzékelőbe.

Aktiválási fájl letöltése:

1. Az **Onboard Sensor lapon** válassza az **aktiválási fájl letöltése lehetőséget.**
1. Tegye elérhetővé a fájlt az érzékelő konzoljára először bejelentkező felhasználó számára.

## <a name="view-onboarded-sensors"></a>Az alaplapi érzékelők megtekintése

A [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)megtekintheti az alaplapi érzékelőkkel kapcsolatos alapvető információkat.

1. Válassza **a Helyek és érzékelők lehetőséget.**
1. Szűrő- és keresési eszközökkel megkeresheti a szükséges érzékelő- és fenyegetésintelligencia-információkat.

- Hány érzékelő volt bevetve
- A felhőhöz csatlakoztatott és helyileg felügyelt érzékelők száma
- Az alaplapi érzékelőhöz társított hub
- Az érzékelővel kapcsolatos részletek, például az érzékelőhöz az berakkorzás során hozzárendelt név, az érzékelőhöz társított zóna vagy egyéb leíró információk címkékkel

## <a name="manage-onboarded-sensors"></a>Az alaplapi érzékelők kezelése

Az [érzékelőkkel kapcsolatos](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) felügyeleti feladatokhoz használja a Defender for IoT portált.

Az alaplapi érzékelőket a Helyek és érzékelők oldalon **lehet** megtekinteni. Ezen az oldalon az érzékelők adatait is szerkesztheti.

### <a name="export-sensor-details"></a>Érzékelő részleteinek exportálása

Az alaplapi érzékelő információinak exportáláshoz válassza az **Exportálás** ikont a Helyek és érzékelők **oldal** tetején.

### <a name="edit-sensor-zone-details"></a>Érzékelőzóna részleteinek szerkesztése

A Helyek **és érzékelők szerkesztési lehetőséggel** szerkesztheti az érzékelő nevét és zónáját.

A szerkesztéshez:

1. Válassza **a szerkeszteni kívánt** érzékelőhöz a három pont **(...)** lehetőséget.
1. Válassza a **Szerkesztés** elemet.
1. Frissítse az érzékelőzónát, vagy hozzon létre egy új zónát.

### <a name="delete-a-sensor"></a>Érzékelő törlése

Ha töröl egy felhőhöz csatlakoztatott érzékelőt, a rendszer nem küld adatokat az IoT Hubnak. Törölje a helyileg csatlakoztatott érzékelőket, ha már nem dolgozik velük.

Érzékelő törlése:

1. Válassza a törölni kívánt érzékelőhöz a három pont **(...)** lehetőséget.
1. Erősítse meg a törlési szándékát.

### <a name="reactivate-a-sensor"></a>Érzékelő újraaktiválása 

Előfordulhat, hogy újra kell aktiválni az érzékelőt, mert a következőt szeretné:

- **Helyileg felügyelt** mód helyett felhőhöz csatlakoztatott módban való munka: Az újraaktiválás után az érzékelő észlelései megjelennek az érzékelőben, és az újonnan észlelt riasztási információk az IoT Hubon keresztül jelennek meg. Ezek az információk megoszthatóak más Azure-szolgáltatásokkal, például a Azure Sentinel.

- **Helyileg felügyelt mód használata** felhőhöz csatlakoztatott mód helyett: Az újraaktiválás után az érzékelőészlelési információk csak az érzékelőn jelennek meg.

- **Az érzékelő társítása egy új IoT Hubhoz:** Ehhez regisztrálja újra az érzékelőt egy új központban, majd töltsön le egy új aktiválási fájlt.

Az érzékelő újraaktiválható:

1. A [Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) **portálon** válassza a Helyek és érzékelők lapot.

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje az érzékelőt.

4. Az érzékelőt az Első lépések oldalon található Érzékelő létrehozása  lehetőség kiválasztásával újból elő kell választania az új módban vagy egy új IoT Hubbal.

5. Töltse le az aktiválási fájlt.

1. Jelentkezzen be a Defender for IoT érzékelő konzoljára.

7. Az érzékelő konzolján válassza a **Rendszerbeállítások,** majd az **Újraaktiválás lehetőséget.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Töltse fel az aktiválási fájlt az érzékelő újraaktiválhoz.":::

8. Válassza **a Feltöltés** lehetőséget, és válassza ki a mentett fájlt az Érzékelő feltöltése lapon.

9. Válassza az **Aktiválás lehetőséget.**

## <a name="next-steps"></a>Következő lépések

[Az érzékelő aktiválása és beállítása](how-to-activate-and-set-up-your-sensor.md)
