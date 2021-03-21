---
title: Oktatóanyag – folyamatos beteg-figyelési alkalmazás létrehozása az Azure IoT Centralkal | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre folyamatos beteg-figyelési alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719100"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Oktatóanyag: folyamatos, beteg monitorozási alkalmazás sablonjának üzembe helyezése és áttekintése

Ez az oktatóanyag bemutatja, hogyan kezdheti el az első lépéseket egy IoT Central folyamatos beteg monitorozási alkalmazás sablonjának üzembe helyezésével. Megtudhatja, hogyan helyezheti üzembe és használhatja a sablont.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Alkalmazás sablonjának létrehozása
> * Az alkalmazás sablonjának átjárása

## <a name="prerequisites"></a>Előfeltételek

Az Azure-előfizetés használata ajánlott. Alternatív megoldásként ingyenes, 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-an-application-template"></a>Alkalmazás sablonjának létrehozása

Navigáljon az [Azure IoT Central Application Manager webhelyére](https://apps.azureiotcentral.com/). Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd válassza az **egészségügy** fület.

:::image type="content" source="media/app-manager-health.png" alt-text="Healthcre-alkalmazás sablonja":::

A **create app (alkalmazás létrehozása** ) gombra kattintva megkezdheti az alkalmazás létrehozását, majd bejelentkezhet a Microsoft személyes, munkahelyi vagy iskolai fiókjával. Az **új alkalmazás** lapra kerül.

![Alkalmazás-egészségügyi ellátás létrehozása](media/app-manager-health-create.png)

![Alkalmazás-egészségügyi számlázási adatok létrehozása](media/app-manager-health-create-billinginfo.png)

Az alkalmazás létrehozása:

1. Az Azure IoT Central automatikusan javaslatot tesz az alkalmazás nevére a kiválasztott sablon alapján. Elfogadhatja ezt a nevet, vagy megadhatja a saját felhasználóbarát alkalmazásának nevét, például a **folyamatos beteg monitorozását**. Az Azure IoT Central egy egyedi URL-előtagot is létrehoz az alkalmazás neve alapján. Ha szeretné, megváltoztathatja ezt az URL-előtagot valami emlékezetre.

2. Kiválaszthatja, hogy szeretné-e létrehozni az alkalmazást az *ingyenes* díjszabási csomag vagy az egyik *standard* díjszabási csomag használatával. Az ingyenes csomaggal létrehozott alkalmazások a lejáratuk előtt hét napig ingyenesen használhatók, és legfeljebb öt ingyenes eszközt tudnak használni. Az alkalmazásokat az ingyenes csomagból egy standard díjszabási csomagba helyezheti át, mielőtt lejár. Ha az ingyenes csomagot választja, meg kell adnia a kapcsolattartási adatait, és ki kell választania, hogy szeretne-e információkat és tippeket kapni a Microsofttól. A standard csomag használatával létrehozott alkalmazások akár két ingyenes eszközt is támogatnak, és az Azure-előfizetési adatok megadását igénylik a számlázáshoz.

3. Az alkalmazás üzembe helyezéséhez kattintson a lap alján található **Létrehozás** gombra.

## <a name="walk-through-the-application-template"></a>Az alkalmazás sablonjának átjárása

### <a name="dashboards"></a>Irányítópultok

Az alkalmazás sablonjának üzembe helyezése után először a **Lamna a beteg monitorozási irányítópultján** kell megszállnia. A Lamna Healthcare egy fiktív kórházi rendszer, amely két kórházat tartalmaz: a Woodgrove kórházat és a Burkville kórházat. A Woodgrove Hospital Operator irányítópultján a következőket teheti:

* Lásd: az eszköz telemetria és tulajdonságai, például az eszköz **töltöttségi szintje** vagy a **kapcsolati** állapota.

* Megtekintheti a Smart Vitals javító eszköz **alapszintű csomagját** és helyét.

* A Smart Vitals javításának **újraépítése** egy új beteg számára.

* Tekintse meg a **szolgáltatói irányítópultot** , amelyet a kórházi ápolási csapat láthat a betegek nyomon követése érdekében.

* Módosítsa az eszköz **beteg állapotát** annak jelzésére, hogy az eszköz használatban van-e egy fekvőbeteg-vagy távoli forgatókönyvben.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Fekvőbeteg-állapot":::

A Burkville Kórház-kezelő irányítópultjának megnyitásához válassza a **Ugrás a távoli beteg irányítópultra** lehetőséget. Ez az irányítópult a műveletek, a telemetria és az információk hasonló készletét tartalmazza. Több eszközt is láthat a használatban, és a **belső vezérlőprogram frissítését** is választhatja.

:::image type="content" source="media/lamna-remote.png" alt-text="Távoli operátor irányítópultja":::

### <a name="device-templates"></a>Eszközsablonok

Ha kijelöli az **eszköz sablonokat**, a sablon két típusát látja:

* **Smart vitals-javítás**: ez az eszköz egy olyan javítást jelent, amely a különböző létfontosságú jeleket méri. A kórházi és a kórházon kívüli betegek figyelésére használatos. Ha kiválasztja a sablont, láthatja, hogy a javítás az eszközön lévő adatokat, például az akkumulátor szintjét és az eszköz hőmérsékletét, valamint a beteg egészségügyi adatokat, például a légzési arányt és a vérnyomást küldi.

* **Intelligens térd zárójel**: ez az eszköz egy térd zárójelet jelöl, amelyet a páciensek a térdtől való kiváltáskor használnak. Ha ezt a sablont választja, megtekintheti az eszközök, a mozgási tartomány és a gyorsítás funkcióit.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Intelligens javítási sablon":::

### <a name="device-groups"></a>Device groups

Az erőforráscsoportok használatával logikailag csoportosíthatja az eszközök készletét, majd csoportos lekérdezéseket vagy műveleteket futtathat rajtuk.

Ha kiválasztja az eszközcsoport fület, megjelenik egy alapértelmezett eszközcsoport az alkalmazásban található minden eszköz sablonhoz. Emellett két további, **elavult belső vezérlőprogram**-eszközzel rendelkező **eszközt is** létrehozhat. Ezeket [a minta](#jobs) eszközöket bemenetként használhatja az alkalmazás egyes feladatainak futtatásához.

### <a name="rules"></a>Szabályok

Ha a **szabályok** lehetőséget választja, a sablon három szabálya látható:

* **Kapcsos zárójel hőmérséklete**: Ez a szabály akkor aktiválódik, ha az intelligens térd zárójelének hőmérséklete nagyobb, mint 95 &deg; F egy 5 perces ablakban. Ez a szabály figyelmezteti a betegeket és a gondozási csapatot, és távolról lehűti az eszközt.

* **Csökkenés észlelhető**: Ez a szabály akkor aktiválódik, ha a beteg bukása észlelhető. Ezzel a szabállyal konfigurálható egy operatív csapat üzembe helyezésére szolgáló művelet, amely segítséget nyújt az elhullott páciensek számára.

* **Alacsony javítási akkumulátor**: Ez a szabály akkor aktiválódik, ha az eszköz töltöttségi szintje 10% alá esik. Ezzel a szabállyal elindíthatja az értesítéseket a betegnek az eszközük feltöltéséhez.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Szabályok":::

### <a name="jobs"></a>Feladatok

A feladatok lehetővé teszik a tömeges műveletek futtatását egy [adott eszközön](#device-groups) , az eszközcsoport használatával bemenetként. Az alkalmazás sablonja két olyan feladattal rendelkezik, amelyeket az operátor futtathat:

* A **térd zárójel belső vezérlőprogram frissítése**: Ez a művelet **elavult belső vezérlőprogram** használatával megkeresi az eszközök csoportjának eszközeit, és futtat egy parancsot, amely frissíti ezeket az eszközöket a legújabb belső vezérlőprogram-verzióra. Ez a példa feltételezi, hogy az eszközök kezelhetik a **frissítési** parancsot, majd beolvassák a belső vezérlőprogram fájljait a felhőből.  

* **Eszközök újbóli kiépítése**: olyan eszközöket tartalmaz, amelyek nemrég visszatértek a kórházba. Ez a feladattípus megkeresi az **eszközöket, és futtat** egy parancsot, hogy újra kiépítse őket a következő csoportba.

### <a name="devices"></a>Eszközök

Válassza az **eszközök** fület, majd válassza ki a **Smart térd zárójel** egy példányát. A kiválasztott eszköz információit három nézetben tekintheti meg. Ezek a nézetek az eszköz sablonjának összeállítása során jönnek létre és jelennek meg. ezért ezek a nézetek konzisztensek az összes csatlakoztatott vagy szimulált eszközön.

Az **irányítópult** nézet áttekintést nyújt az eszköz telemetria és tulajdonságairól.

A **Tulajdonságok** lapon szerkesztheti a felhő tulajdonságait, valamint az eszköz olvasási/írási tulajdonságait.

A **parancsok** lapon parancsokat futtathat az eszközön.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Térd zárójelek irányítópultja":::

### <a name="data-export"></a>Adatexportálás

Az adatexportálás lehetővé teszi, hogy az eszköz adatait folyamatosan exportálja más Azure-szolgáltatásokba, beleértve a [FHIR készült Azure API](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)-t.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az **adminisztráció > alkalmazás beállításai** között, és kattintson a **Törlés** gombra.

:::image type="content" source="media/admin-delete.png" alt-text="Tiszta erőforrások":::

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre olyan szolgáltatói irányítópultot, amely kapcsolódik a IoT Central alkalmazáshoz.

> [!div class="nextstepaction"]
> [Szolgáltatói irányítópult létrehozása](tutorial-health-data-triage.md)