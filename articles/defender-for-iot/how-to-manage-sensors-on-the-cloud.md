---
title: Érzékelők és előfizetések előkészítése és felügyelete a Defender for IoT portálon
description: Ismerje meg, hogyan végezheti el az érzékelők bevezetését, megtekintését és kezelését a Defender for IoT portálon.
ms.date: 2/18/2021
ms.topic: how-to
ms.openlocfilehash: 5c529a5679e8b3d4879b003fa3d168e911f9b518
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781823"
---
# <a name="onboard-and-manage-sensors-and-subscriptions-in-the-defender-for-iot-portal"></a>Érzékelők és előfizetések előkészítése és felügyelete a Defender for IoT portálon

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
1. A szűrő-és keresési eszközökkel megkeresheti az érzékelő és a fenyegetések felderítéséhez szükséges információkat.

- Hány érzékelők lettek bevezetve
- A felhőhöz csatlakoztatott és helyileg felügyelt érzékelők száma
- A beépített érzékelőhöz társított hub
- Az érzékelővel kapcsolatos részletek, például az érzékelőhöz a bevezetéskor hozzárendelt név, az érzékelőhöz társított zóna, illetve a címkékkel hozzáadott egyéb leíró információk.

## <a name="manage-onboarded-sensors"></a>Beépített érzékelők kezelése

Az érzékelőkkel kapcsolatos felügyeleti feladatokhoz használja a [Defender for IoT portált](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) .

A beépített érzékelők a **helyek és érzékelők** lapon tekinthetők meg. Ezen az oldalon szerkesztheti az érzékelő adatait is.

### <a name="export-sensor-details"></a>Az érzékelő adatainak exportálása

A beépített érzékelő adatainak exportálásához válassza az **Exportálás** ikont a **helyek és érzékelők** oldal tetején.

### <a name="edit-sensor-zone-details"></a>Az érzékelő zóna részleteinek szerkesztése

A **helyek és érzékelők** szerkesztési lehetőségeivel szerkesztheti az érzékelő nevét és zónáját.

Szerkesztés:

1. Válassza ki a szerkeszteni kívánt érzékelő **három** pontot (**..**.).
1. Válassza a **Szerkesztés** elemet.
1. Frissítse az érzékelő zónát, vagy hozzon létre egy új zónát.

### <a name="delete-a-sensor"></a>Érzékelő törlése

Ha töröl egy felhőhöz csatlakoztatott érzékelőt, a rendszer nem továbbítja az adatokat az IoT hubhoz. Törölje a helyileg csatlakoztatott érzékelőket, ha már nem dolgozik velük.

Érzékelő törlése:

1. Válassza ki a törölni kívánt érzékelő három pontot (**..**.).
1. Erősítse meg a törlési szándékát.

### <a name="reactivate-a-sensor"></a>Érzékelő újraaktiválása 

Előfordulhat, hogy újra kell aktiválnia az érzékelőt, mert a következőket kívánja végezni:

- **Helyileg felügyelt mód helyett felhőalapú kapcsolódási mód használata**: az újraaktiválást követően az érzékelők észlelései az érzékelőben jelennek meg, az újonnan észlelt riasztási információk pedig az IoT hub-on keresztül érkeznek. Ezek az információk megoszthatók más Azure-szolgáltatásokkal, például az Azure Sentinel használatával.

- **Helyileg felügyelt módban dolgozhat a felhőalapú kapcsolat helyett**: az újraaktiválást követően az érzékelő észlelési adatai csak az érzékelőben jelennek meg.

- **Az érzékelő társítása egy új IoT hubhoz**: ehhez regisztrálja újra az érzékelőt egy új hubhoz, majd töltsön le egy új aktiválási fájlt.

Érzékelő újraaktiválása:

1. Lépjen a **Sites and érzékelők** lapra a [Defender for IoT portálon](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started).

2. Válassza ki azt az érzékelőt, amelyhez új aktiválási fájlt szeretne feltölteni.

3. Törölje az érzékelőt.

4. Az érzékelőt az új módban, vagy egy új IoT hub-ban, az Első lépések lapról válassza ki az **érzékelőt** .

5. Töltse le az aktiválási fájlt.

1. Jelentkezzen be a Defender for IoT szenzor-konzolra.

7. Az érzékelő konzolon válassza a **Rendszerbeállítások** , majd az **újraaktiválás** lehetőséget.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="Töltse fel az aktiválási fájlt az érzékelő újraaktiválásához.":::

8. Válassza a **feltöltés** lehetőséget, és válassza ki az előkészítési érzékelő lapról mentett fájlt.

9. Válassza az **aktiválás** lehetőséget.

## <a name="offboard-a-subscription"></a>Előfizetés regisztrációjának megszüntetésére szolgáló

Az előfizetések havi rendszerességgel kezelhetők. Ha regisztrációjának megszüntetésére szolgáló egy előfizetést, az előfizetés után a hónap végéig számlázunk. 

Távolítsa el az előfizetéshez társított összes érzékelőt, mielőtt offboarding az előfizetést. Az érzékelők törlésével kapcsolatos további információkért lásd: [szenzorok törlése](#delete-a-sensor). 

Előfizetés regisztrációjának megszüntetésére szolgáló:

1. Navigáljon a **díjszabás** oldalra.
1. Válassza ki az előfizetést, majd kattintson a **Törlés** ikonra :::image type="icon" source="media/how-to-manage-sensors-on-the-cloud/delete-icon.png" border="false"::: .
1. A megerősítő felugró ablakban jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy törölte az előfizetéshez társított összes érzékelőt.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="Jelölje be a jelölőnégyzetet, és válassza ki a regisztrációjának megszüntetésére szolgáló, hogy regisztrációjának megszüntetésére szolgáló az érzékelőt.":::

1. Kattintson a **regisztrációjának megszüntetésére szolgáló** gombra. 

A helyszíni környezet nincs hatással, de az érzékelőt el kell távolítani a helyszíni környezetből, vagy újra hozzá kell rendelni az érzékelőt egy másik előfizetéshez, hogy a kapcsolódó adatok ne legyenek a helyszíni felügyeleti konzolra áramlanak. 

## <a name="see-also"></a>Lásd még

[Az érzékelő aktiválása és beállítása](how-to-activate-and-set-up-your-sensor.md)
