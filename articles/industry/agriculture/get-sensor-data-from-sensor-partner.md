---
title: Érzékelők adatainak beolvasása a partnerektől
description: Ez a cikk azt ismerteti, hogyan lehet beolvasni az érzékelők adatait a partnerektől.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 474b6f8356724546105d902a15427f483f656317
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182774"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Érzékelők adatainak beolvasása az érzékelő partnereitől

Az Azure FarmBeats segítségével a IoT-eszközökről és-érzékelőkről átviheti a streamet a Datahub-be. Jelenleg a következő érzékelő-eszköz partnerek támogatottak.

  ![FarmBeats-partnerek](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Az eszköz adatainak az Azure FarmBeats való integrálásával a farmon üzembe helyezett IoT-érzékelőkből az adatközpontba helyezheti a terepi adatait. A rendelkezésre álló, a FarmBeats-gyorsító használatával megjeleníthető adatelemek. Az adatok a FarmBeats használatával adatfúziós és gépi tanulási/mesterséges intelligencia (ML/AI) modellek létrehozásához használhatók.

Az érzékelő adatfolyamának elindításához a következőket kell biztosítani:

-  Telepítette a FarmBeats az Azure Marketplace-en.
-  Ön dönti el, hogy milyen érzékelőkkel és eszközökkel kívánja telepíteni a farmon.
-  Ha a talaj nedvességtartalmának érzékelőit kívánja használni, használja a FarmBeats-szennyeződési érzékelő elhelyezési térképét, hogy javaslatot tegyen az érzékelők számára, és hogy pontosan hol kell elhelyezni azokat. További információ: Maps- [Létrehozás](generate-maps-in-azure-farmbeats.md).
- A farmján megvásárolhatja és üzembe helyezheti eszközeit és érzékelőit. Győződjön meg arról, hogy az eszköz partnereinek megoldásán keresztül éri el az érzékelő adatait.

## <a name="enable-device-integration-with-farmbeats"></a>Eszköz-integráció engedélyezése a FarmBeats

Az érzékelők adatközvetítésének megkezdése után megkezdheti az adatgyűjtési folyamat beszerzését a FarmBeats-rendszeren. Adja meg a következő információkat az eszköz szolgáltatójának az FarmBeats való integráció engedélyezéséhez:

 - API-végpont
 - Bérlőazonosító
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - EventHub-kapcsolatok karakterlánca

A fenti információk létrehozásához kövesse az alábbi lépéseket:

> [!NOTE]
> Ezeket a lépéseket az Azure-ban kell végrehajtani ahhoz, hogy hozzáférjenek az Azure-előfizetéshez, ahol a FarmBeats üzembe lett helyezve.

1. Jelentkezzen be itt: https://portal.azure.com/.

2. **Ha a FarmBeats 1.2.7 vagy újabb verzióval rendelkezik, hagyja ki az a, b és c lépést, és folytassa a 3. lépéssel.** A FarmBeats-verziót a FarmBeats felhasználói felület jobb felső sarkában található **Beállítások** ikonra kattintva tekintheti meg.

      a.  **Azure Active Directory**  >  **alkalmazás regisztrációjának** megugrása

      b. Válassza ki a FarmBeats-telepítés részeként létrehozott **alkalmazás-regisztrációt** . Ez a neve megegyezik a FarmBeats datahub.

      c. Válassza ki **az API közzététele** lehetőséget > válassza az **ügyfélalkalmazás hozzáadása** lehetőséget, és adja meg a **04b07795-8ddb-461a-bbee-02f9e1bf7b46** , és jelölje be az **Engedélyezés hatókört**. Ez hozzáférést biztosít az Azure CLI-hez (Cloud Shell) az alábbi lépések végrehajtásához:

3. Nyissa meg a Cloud Shellt. Ez a beállítás a Azure Portal jobb felső sarkában található eszköztáron érhető el.

    ![Azure Portal eszköztár](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Győződjön meg arról, hogy a környezet **PowerShell**-re van beállítva. Alapértelmezés szerint a bash értékre van beállítva.

    ![PowerShell eszköztár-beállítás](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Nyissa meg a saját címtárát.

    ```azurepowershell-interactive
    cd
    ```

6. Futtassa az alábbi parancsot. Ezzel összekapcsolja az Azure AD-kérelmekhez használt hitelesített fiókot

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. Futtassa az alábbi parancsot. Ezzel letölt egy parancsfájlt a saját könyvtárába.

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. Futtassa az alábbi parancsfájlt. A parancsfájl kéri a bérlő azonosítóját, amely **Azure Active Directory**  >  **áttekintő** oldalról kérhető le.

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. A Datahub API-végpont nevének kisbetűvel kell rendelkeznie.
> 2. Ha a Datahub API-végponthoz tartozó farmbeats webhely neve URL-címét másolja, győződjön meg róla, hogy nincs záró perjel (/).

9. A képernyőn megjelenő utasításokat követve rögzítheti az **API-végpont**, a **bérlői azonosító**, az **ügyfél-azonosító**, az **ügyfél titkos kulcsa** és a **EventHub kapcsolódási karakterláncának** értékét.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Az eszközök adatainak integrálása a generált hitelesítő adatok használatával

Most a következő információk jönnek létre az előző szakaszban.
 - API-végpont
 - EventHub-kapcsolatok karakterlánca
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs
 - Bérlőazonosító

Ezt a FarmBeats összekapcsolásához meg kell adnia az eszköz partnerének. Ha ugyanezt teszi, nyissa meg az eszköz partner portálját. Ha például a Davis Instruments, a Teralytic vagy a Pessl Instruments Instruments (Metos.at) eszközeit használja, ugorjon a megfelelő lapokra az alább említettek szerint:

1. [Davis-eszközök](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl Instruments-eszközök](https://ng.fieldclimate.com/user-api-services)

Az eszköz szolgáltatója megerősíti a sikeres integrációt. A megerősítést követően megtekintheti az összes eszközt és érzékelőt az Azure FarmBeats.

## <a name="view-devices-and-sensors"></a>Eszközök és érzékelők megtekintése

A következő szakasz segítségével megtekintheti a farm eszközeit és érzékelőit.

### <a name="view-devices"></a>Eszközök megtekintése

A FarmBeats jelenleg a következő eszközöket támogatja:

- **Csomópont**: egy eszköz, amelyhez egy vagy több érzékelő csatlakozik.
- **Átjáró**: egy eszköz, amelyhez egy vagy több csomópont csatlakozik.

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüből.
  Az **eszközök** lap megjeleníti az eszköz típusát, a modellt, az állapotot, a behelyezett farmot és a metaadatok utolsó frissítésének dátumát. Alapértelmezés szerint a farm oszlop értéke *Null*. Dönthet úgy, hogy hozzárendel egy eszközt egy farmhoz. További információ: [eszközök kiosztása](#assign-devices).
2. Válassza ki az eszközt, amely az eszközhöz csatlakoztatott eszköz tulajdonságait, telemetria és alárendelt eszközeit szeretné megtekinteni.

    ![Eszközök lap](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Érzékelők megtekintése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a menü **érzékelők** elemét.
  Az **érzékelők** lap az érzékelő típusával, a hozzá kapcsolódó farmtal, a szülő eszközzel, a port nevével, a port típusával és az utolsó frissített állapottal kapcsolatos részleteket jeleníti meg.
2. Válassza ki az érzékelő tulajdonságait, az aktív riasztásokat és a telemetria az érzékelőből.

    ![Érzékelők lap](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Eszközök kiosztása

Az érzékelők bevezetésének folyamata után hozzárendelheti azt a farmhoz, ahol az érzékelőket üzembe helyezte.

1. A kezdőlapon válassza a **farmok** lehetőséget a menüből. Megjelenik a **farmok** listája lap.
2. Válassza ki azt a farmot, amelyhez hozzá szeretné rendelni az eszközt, majd válassza az **eszközök hozzáadása** lehetőséget.
3. Megjelenik az **eszközök hozzáadása** ablak. Válassza ki azt az eszközt, amelyet hozzá szeretne rendelni a farmhoz.

    ![Eszközök hozzáadása ablak](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Válassza az **eszközök hozzáadása** lehetőséget. Másik lehetőségként lépjen az **eszközök** menüre, válassza ki a farmhoz hozzárendelni kívánt eszközöket, majd válassza az **eszközök társítása** lehetőséget.
5. Az **eszközök hozzárendelése** ablakban válassza ki a farm elemet a legördülő listából, és válassza az **összes alkalmazása** lehetőséget a farm összes kiválasztott eszközhöz való hozzárendeléséhez.

    ![Eszközök hozzárendelése ablak](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Ha az egyes eszközöket egy másik farmhoz szeretné társítani, válassza a **hozzárendelés farmhoz** oszlop legördülő nyilát, és válasszon ki egy farmot az egyes eszközök sorához.

7. Válassza a **hozzárendelés** lehetőséget az eszköz hozzárendelésének befejezéséhez.

### <a name="visualize-sensor-data"></a>Érzékelőadatok megjelenítése

Kövesse az alábbi lépéseket:

1. A Kezdőlap lapon válassza a **farmok** lehetőséget a menüből a **farmok** oldal megtekintéséhez.
2. Válassza ki azt a **farmot** , amelynek az érzékelőjét meg szeretné jeleníteni.
3. A **Farm** irányítópultján megtekintheti a telemetria-adatbázisokat. Megtekintheti az élő telemetria, vagy az **egyéni tartomány** használatával megtekintheti a kívánt dátumtartományt.

    ![Farm irányítópult](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Érzékelő törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **érzékelők** lehetőséget a menüből az **érzékelők** lap megtekintéséhez.
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** lehetőséget a megerősítő ablakban.

    ![Képernyőfelvétel: az érzékelők törlésére szolgáló oldal és a törlés gomb.](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Egy megerősítő üzenet azt mutatja, hogy az érzékelő sikeresen törölve lett.

## <a name="delete-devices"></a>Eszközök törlése

Kövesse az alábbi lépéseket:

1. A kezdőlapon válassza az **eszközök** lehetőséget a menüben az **eszközök** lap megtekintéséhez.
2. Válassza ki a törölni kívánt eszközt, és válassza a **Törlés** lehetőséget a megerősítő ablakban.

    ![Törlés gomb](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik érzékelővel az Azure FarmBeats-példányba. Most megismerheti, hogyan [hozhatja](generate-maps-in-azure-farmbeats.md#generate-maps) ki a térképeket a farmokhoz.
