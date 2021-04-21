---
title: Érzékelők kezelése a Defender for IoT portálon
description: Megtudhatja, hogyan lehet érzékelőket bevetni, megtekinteni és kezelni a Defender for IoT portálon.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f407a65f60a1b969f17ebe00be39a888a09ec83d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752713"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>Érzékelők kezelése a Defender for IoT portálon

Ez a cikk azt ismerteti, hogyan lehet érzékelőket bevetni, megtekinteni és kezelni a [Defender for IoT portálon.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

## <a name="onboard-sensors"></a>Érzékelők előkészítése

Az érzékelő regisztrálásával regisztrálja az érzékelőt a Azure Defender for IoT és letölt egy érzékelőaktiválási fájlt.

### <a name="register-the-sensor"></a>Az érzékelő regisztrálása

Regisztráció:

1. A Defender for IoT **portálon** kattintson a [Kezdőlapra.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)
1. Válassza **az Érzékelő alaplapi beállítását.**
1. Hozzon létre egy érzékelőnevet. Javasoljuk, hogy a név részeként foglalja bele a telepített érzékelő IP-címét, vagy használjon könnyen azonosítható nevet. Így könnyebb nyomon követni és konzisztensen elnevezni a regisztrációs nevet az Azure [Defender for IoT](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) portálon és az érzékelő konzolján megjelenített üzembe helyezett érzékelő IP-címe között.
1. Társítsa az érzékelőt egy Azure-előfizetéshez.
1. Válasszon egy érzékelőkapcsolati módot a **Felhőhöz** csatlakoztatott váltógomb használatával. Ha a váltógomb be van kapcsolva, az érzékelő a felhőhöz csatlakozik. Ha a váltógomb ki van kapcsolva, az érzékelő helyileg lesz felügyelt.

   - **Felhőhöz csatlakoztatott érzékelők:** Az érzékelő által észlelt információk megjelennek az érzékelő konzolján. A riasztási információk egy IoT Hubon keresztül kézbesítve adatokat osztják meg más Azure-szolgáltatásokkal, például az Azure Sentinel. Emellett a fenyegetésintelligencia-csomagok a portálról Azure Defender for IoT az érzékelőkre. Ezzel szemben, ha az érzékelő nincs csatlakoztatva a felhőhöz, le kell töltenie a fenyegetésintelligencia-csomagokat, majd fel kell töltenie azokat a vállalati érzékelőkbe. Ha engedélyezni szeretné, hogy a Defender for IoT csomagokat toljon le az érzékelőknek, engedélyezze az **Automatikus** fenyegetésintelligencia-frissítések kapcsolót. További információ: [Fenyegetésintelligencia-kutatások és -csomagok.](how-to-work-with-threat-intelligence-packages.md)
   Válasszon egy IoT Hubot, amely átjáróként szolgál az érzékelő és a Azure Defender for IoT között. Adja meg a hely nevét és zónáját. Leíró címkéket is hozzáadhat. A webhely neve, zónája és címkéi leíró bejegyzések a Helyek és érzékelők [oldalon.](#view-onboarded-sensors)

   - **Helyileg felügyelt érzékelők:** Az érzékelők által észlelt információk megjelennek az érzékelő konzolján. Ha egy air-gaped hálózatban dolgozik, és szeretné egységes nézetben megtekinteni a több helyileg felügyelt érzékelő által észlelt összes információt, működjön együtt a helyszíni felügyeleti konzollal.

   A felhőhöz csatlakoztatott érzékelők számára az be- és bekapcsolás során meghatározott név az érzékelő konzolján megjelenő név. Ezt a nevet nem módosíthatja közvetlenül a konzolon. A helyileg felügyelt érzékelők esetén az üzembe ásás során alkalmazott nevet a rendszer az Azure-ban tárolja, de frissíthető az érzékelő konzolján.

### <a name="download-the-sensor-activation-file"></a>Az érzékelőaktiválási fájl letöltése

Az érzékelőaktiválási fájl az érzékelő felügyeleti módjára vonatkozó utasításokat tartalmaz. Minden üzembe helyezett érzékelőhöz le kell töltenie egy egyedi aktiválási fájlt. Egy felhasználó, aki először jelentkezik be az érzékelő konzoljára, feltölti az aktiválási fájlt az érzékelőbe.

Aktiválási fájl letöltése:

1. Az **Onboard Sensor lapon** válassza az **aktiválási fájl letöltése lehetőséget.**
1. Tegye elérhetővé a fájlt az érzékelő konzoljára először bejelentkező felhasználó számára.

## <a name="view-onboarded-sensors"></a>Az alaplapi érzékelők megtekintése

A [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)megtekintheti az érzékelőkkel kapcsolatos fontos működési információkat.

1. Válassza **a Helyek és érzékelők lehetőséget.** Az oldalon látható, hogy hány érzékelő van bevetve, hány érzékelő csatlakozik a felhőhöz, és hány helyileg van felügyelt, valamint:

- az érzékelőnek az onboarding során hozzárendelt neve.
- a kapcsolat típusa (felhőhöz csatlakoztatott vagy helyileg felügyelt)
- az érzékelőhöz társított zóna.
- Az érzékelő telepített verziója
- Az érzékelő és a felhő kapcsolati állapota.
- Az utolsó alkalom, amikor a rendszer észlelte, hogy az érzékelő csatlakozott a felhőhöz.

## <a name="manage-onboarded-sensors"></a>Az alaplapi érzékelők kezelése

Az [érzékelőkhöz kapcsolódó](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) felügyeleti feladatokhoz használja a Defender for IoT portált.

Az alaplapi érzékelők a Helyek és érzékelők oldalon **megtekinthetők.** Ezen az oldalon az érzékelők adatait is szerkesztheti.

### <a name="export-sensor-details"></a>Érzékelő részleteinek exportálása

Az érzékelőkre vonatkozó adatok exportáláshoz válassza az **Exportálás** ikont a Helyek és érzékelők **oldal** tetején.

### <a name="edit-sensor-zone-details"></a>Érzékelőzóna részleteinek szerkesztése

A Helyek **és érzékelők szerkesztési lehetőséggel** szerkesztheti az érzékelő nevét és zónáját.

A szerkesztéshez:

1. Válassza **a szerkeszteni kívánt** érzékelőhöz a három pont **(...**) lehetőséget.
1. Válassza a **Szerkesztés** elemet.
1. Frissítse az érzékelőzónát, vagy hozzon létre egy új zónát.

### <a name="delete-a-sensor"></a>Érzékelő törlése

Ha töröl egy felhőhöz csatlakoztatott érzékelőt, a rendszer nem küld adatokat az IoT Hubnak. Törölje a helyileg csatlakoztatott érzékelőket, ha már nem dolgozik velük.

Érzékelő törlése:

1. Válassza a törölni kívánt érzékelőhöz a három pont **(...)** lehetőséget.
1. Erősítse meg a törlési szándékát.

### <a name="reactivate-a-sensor"></a>Érzékelő újraaktiválása 

Előfordulhat, hogy újra kell aktiválni az érzékelőt, mert a következőt szeretné:

- **Helyileg felügyelt** mód helyett felhőhöz csatlakoztatott módban dolgozhat: Az újraaktiválás után az érzékelők észlelése megjelenik az érzékelőben, és az újonnan észlelt riasztási információk az IoT Hubon keresztül jelennek meg. Ezek az információk megoszthatóak más Azure-szolgáltatásokkal, például a Azure Sentinel.

- **Helyileg felügyelt módban dolgozhat** a felhőhöz csatlakoztatott mód helyett: Az újraaktiválás után az érzékelők észlelésére vonatkozó információk csak az érzékelőn jelennek meg.

- **Az érzékelő társítása egy új IoT Hubhoz:** Ehhez regisztrálja újra az érzékelőt egy új hubon, majd töltsön le egy új aktiválási fájlt.

Az érzékelő újraaktiválható:

1. A Defender for IoT **portálon kattintson** a Helyek és érzékelők [lapra.](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje az érzékelőt.

4. Az érzékelőt az Első lépések oldalon az **Érzékelő** létrehozása lehetőség kiválasztásával újból be kell kezdeni az új módban vagy egy új IoT Hubbal.

5. Töltse le az aktiválási fájlt.

1. Jelentkezzen be a Defender for IoT érzékelő konzoljára.

7. Az érzékelő konzolján válassza a **Rendszerbeállítások,** majd az **Újraaktiválás lehetőséget.**

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Töltse fel az aktiválási fájlt az érzékelő újraaktiválához.":::

8. Válassza **a Feltöltés** lehetőséget, és válassza ki a mentett fájlt az Érzékelő feltöltése lapon.

9. Válassza az **Aktiválás lehetőséget.**

## <a name="next-steps"></a>Következő lépések

[Az érzékelő aktiválása és beállítása](how-to-activate-and-set-up-your-sensor.md)
