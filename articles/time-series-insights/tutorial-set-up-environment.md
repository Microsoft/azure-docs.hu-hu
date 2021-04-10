---
title: 'Oktatóanyag: Gen2-környezet beállítása – Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Oktatóanyag: megtudhatja, hogyan állíthat be környezetet a Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464608"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Oktatóanyag: Azure Time Series Insights Gen2-környezet beállítása

Ez az oktatóanyag végigvezeti Önt egy *Azure Time Series Insights Gen2-alapú TB-környezet* létrehozásának folyamatán.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Azure Time Series Insights Gen2-környezetet.
> * Kapcsolódjon a Azure Time Series Insights Gen2-környezethez egy IoT Hubhoz.
> * Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights Gen2-környezetbe.
> * Végezze el az adatok alapszintű elemzését.
> * Definiáljon egy idősorozat-modell típusát és hierarchiáját, és társítsa azt a példányokhoz.

>[!TIP]
> A [IoT megoldás-gyorsítók](https://www.azureiotsolutions.com/Accelerators) olyan nagyvállalati szintű előre konfigurált megoldásokat biztosítanak, amelyek segítségével felgyorsíthatja az egyéni IoT-megoldások fejlesztését.

Ha még nem rendelkezik ilyennel, regisztráljon egy [ingyenes Azure-előfizetésre](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

* Legalább a **közreműködő** szerepkörrel kell rendelkeznie az Azure-előfizetéshez. További információért olvassa el [Az Azure-szerepkörök az Azure Portal használatával történő hozzárendelését](../role-based-access-control/role-assignments-portal.md)ismertető témakört.

* Hozzon létre egy környezetet a [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) vagy a [parancssori](how-to-create-environment-using-cli.md)felület használatával.

## <a name="create-a-device-simulation"></a>Eszközszimuláció létrehozása

Ebben a szakaszban három szimulált eszközt fog létrehozni, amelyek az Azure IoT Hub-példányra küldenek adatküldést.

1. Nyissa meg az [Azure IoT megoldás-gyorsítók lapot](https://www.azureiotsolutions.com/Accelerators). Jelentkezzen be az Azure-fiókjával, majd válassza az **eszköz szimuláció** lehetőséget.

   [![Az Azure IoT megoldás-gyorssegédek lapja.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Görgessen lefelé az [Áttekintés](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) és az [első lépéseket](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) ismertető szakaszban.

1. Kövesse a Első lépések szakaszban található [központi telepítési utasításokat](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) .

   A folyamat elvégzése akár 20 percet is igénybe vehet.

1. Ha a telepítés befejeződött, meg kell adni a szimuláció URL-címét. Tartsa meg ezt a lapot, mert később vissza fog térni.

   >[!IMPORTANT]
   > Még ne adja meg a megoldás-gyorssegédet! Tartsa meg ezt a weblapot, mert később vissza fog térni.

   [![Az eszköz-szimulációs megoldás üzembe helyezése befejeződött.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Most vizsgálja meg az újonnan létrehozott erőforrásokat a Azure Portal. Az **erőforráscsoportok** oldalon figyelje meg, hogy egy új erőforráscsoport lett létrehozva az `solutionName` ARM-sablon paramétereinek fájljában megadott használatával. Jegyezze fel az eszköz szimulálásához létrehozott erőforrásokat.

   [![Eszköz-szimulációs erőforrások.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2-környezet létrehozása

Ez a szakasz azt ismerteti, hogyan hozható létre Azure Time Series Insights Gen2-környezet, és hogyan csatlakoztatható a IoT-megoldás-gyorsító által létrehozott IoT hubhoz a [Azure Portal](https://portal.azure.com/)használatával.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) az Azure-előfizetési fiók használatával.
1. Válassza az **+ Erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Válassza ki a **eszközök internetes hálózata** kategóriát, majd válassza a **Time Series Insights** lehetőséget.

   [![Válassza ki a Time Series Insights környezeti erőforrást.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. Az **Time Series Insights környezet létrehozása** ablaktábla **alapok** lapján adja meg a következő paramétereket:

    | Paraméter | Művelet |
    | --- | ---|
    | **Környezet neve** | Adjon egyedi nevet a Azure Time Series Insights Gen2-környezetnek. |
    | **Előfizetés** | Adja meg azt az előfizetést, amelyben létre szeretné hozni a Azure Time Series Insights Gen2-környezetet. Az ajánlott eljárás az, hogy ugyanazt az előfizetést használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Erőforráscsoport** | Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot a Azure Time Series Insights Gen2 környezeti erőforráshoz. Az erőforráscsoport az Azure-erőforrások tárolója. Az ajánlott eljárás az, hogy ugyanazt az erőforráscsoportot használja, mint az eszköz-szimulátor által létrehozott többi IoT-erőforrást. |
    | **Hely** | Válasszon ki egy adatközpont-régiót Azure Time Series Insights Gen2-környezetéhez. A további késés elkerülése érdekében érdemes létrehozni a Azure Time Series Insights Gen2-környezetét ugyanabban a régióban, mint az eszköz-szimulátor által létrehozott IoT hub. |
    | **Szint** |  Válassza a **Gen2 (L1)** lehetőséget. Ez a Azure Time Series Insights Gen2 termék SKU-jának. |
    | **Idősorozat-azonosító tulajdonságának neve** | Adja meg az idősorozat-példányok egyedi azonosítására szolgáló értékeket tartalmazó tulajdonság nevét. A **tulajdonságnév** mezőben megadott érték idősorozat-azonosítóként való megadása később nem módosítható. Ebben az oktatóanyagban adja meg a **_iothub--kapcsolatok-Device-ID_** értéket. Ha többet szeretne megtudni az idősorozat-AZONOSÍTÓról, beleértve az összetett idősorozat-azonosítót, olvassa el az [ajánlott eljárásokat az Idősorozat-azonosító kiválasztásához](./how-to-select-tsid.md). |
    | **Tárfiók neve** | Adjon meg egy globálisan egyedi nevet egy új Storage-fiókhoz.|
    | **Storage-fiók típusa** | Válassza ki az új Storage-fiók tárolási típusát. Ajánlott StorageV2|
    | **Storage-fiók replikálása** | Válassza ki az új Storage-fiók tárolási típusát. A hely kiválasztása alapján a LRS, a GRS és a ZRS közül választhat. Ebben az oktatóanyagban választhat LRS|
    | **Hierarchikus névtér** |Ez a beállítás választható, ha kijelöli a StorageV2 Storage-típust. Alapértelmezés szerint le van tiltva. Ebben az oktatóanyagban meghagyhatja az alapértelmezett *letiltott* állapotban.|
    |**Meleg tároló engedélyezése**|Válassza az **Igen** lehetőséget a meleg tároló engedélyezéséhez. Ez a beállítás a környezet létrehozása után is letiltható és újra engedélyezhető. |
    |**Adatmegőrzés (nap)**|Válassza az alapértelmezett beállítást 7 nap. |

    [![Új Azure Time Series Insights környezeti konfiguráció.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![Új Azure Time Series Insights környezeti konfiguráció, folytatás.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. Válassza a Next (tovább) lehetőséget **: eseményforrás**.

   [![Állítsa be a környezet idősorozat-AZONOSÍTÓját.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. Az **eseményforrás** lapon adja meg a következő paramétereket:

   | Paraméter | Művelet |
   | --- | --- |
   | **Létrehoz egy eseményforrás?** | Válassza az **Igen** lehetőséget.|
   | **Forrás típusa** | Válassza a **IoT hub** lehetőséget. |
   | **Név** | Adjon meg egyedi értéket az eseményforrás neveként. |
   | **Válasszon hubot** | Válassza a **meglévő kiválasztása** lehetőséget. |
   | **Előfizetés** | Válassza ki az eszköz-szimulátorhoz használt előfizetést. |
   | **IoT Hub neve** | Válassza ki az eszköz-szimulátorhoz létrehozott IoT hub-nevet. |
   | **Hozzáférési szabályzat IoT Hub** | Válassza a **iothubowner** lehetőséget. |
   | **IoT Hub fogyasztói csoport** | Válassza az **új** lehetőséget, adjon meg egy egyedi nevet, majd válassza a **+ Hozzáadás** lehetőséget. A fogyasztói csoportnak egyedi értéknek kell lennie Azure Time Series Insights Gen2. |
   | **Timestamp tulajdonság** | Ezzel az értékkel azonosítható a bejövő telemetria adataiban található **timestamp** tulajdonság. Ebben az oktatóanyagban hagyja üresen ezt a mezőt. Ez a szimulátor a IoT Hub bejövő időbélyegét használja, amely Azure Time Series Insights Gen2 alapértelmezett értéke. |

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

   [![Konfigurálja a létrehozott IoT hubot eseményforrásként.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

    [![Tekintse át a + létrehozás oldalt a létrehozás gombbal.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Tekintse át az üzemelő példány állapotát:

    [![Az üzembe helyezési értesítés befejeződött.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Bontsa ki a központi telepítés részleteit.

## <a name="stream-data"></a>Adatok streamelése

Most, hogy üzembe helyezte a Azure Time Series Insights Gen2-környezetét, megkezdheti az adatfolyam-adattovábbítást az elemzéshez.

1. A megoldás-gyorsító üzembe helyezésének befejezése után a rendszer URL-címet kap.

1. Kattintson az URL-címre az eszköz szimulációjának elindításához.

1. Válassza az **+ új szimuláció** lehetőséget.

    1. A **szimulációs beállítások** oldal betöltése után adja meg a szükséges paramétereket.

        | Paraméter | Művelet |
        | --- | --- |
        | **Név** | Adjon egyedi nevet a szimulátornak. |
        | **Leírás** | Adjon meg egy definíciót. |
        | **Szimuláció időtartama** | **Határozatlan ideig történő futtatásra** van beállítva. |
        | **Eszközmodell** | Kattintson **az + eszköz típusának hozzáadása** elemre. <br />**Név**: adjon meg egy **liftet**. <br />**Mennyiség**: írja be a **3** értéket. <br /> Hagyja meg a fennmaradó alapértelmezett értékeket |
        | **Cél IoT Hub** | Az **előre kiépített IoT hub használatára** van beállítva. |

        [![Konfigurálja a paramétereket és az indítást.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. Válassza a **Szimuláció indítása** lehetőséget. Az eszköz szimulációjának irányítópultján megjelenik az **aktív eszközök** és az **összes üzenet** .

        [![Azure IoT szimulációs irányítópult.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Adatok elemzése

Ebben a szakaszban az idősorozat-adatokra vonatkozó alapszintű elemzéseket az [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)használatával hajtja végre.

1. A [Azure Portal](https://portal.azure.com/)erőforrás oldaláról válassza ki az URL-címet a Azure Time Series Insights Gen2 Explorerben.

    [![A Azure Time Series Insights Gen2 Explorer URL-címe.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. A Azure Time Series Insights Gen2 Explorerben megjelenik a képernyő felső részén átívelő sáv. Ez a rendelkezésre állási választó. Győződjön meg arról, hogy legalább 2 2 m van kiválasztva, és szükség esetén bontsa ki az időkeretet úgy, hogy kijelöli és húzza a választót a bal és a jobb oldalon.

1. Az **Idősorozat-példányok** a bal oldalon lesznek megjelenítve.

    [![A nem szülő példányok listája.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. Válassza ki az első idejű adatsorozat-példányt. Ezután válassza a **hőmérséklet megjelenítése** lehetőséget.

    [![A kiválasztott idősorozat-példány az átlagos hőmérséklet megjelenítéséhez a Menu paranccsal.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Ekkor megjelenik egy idősorozat-diagram. Módosítsa az **intervallumot** a **30-as** értékre.

1. Ismételje meg az előző lépést a másik két idősorozat-példánnyal, hogy mindhárom megtekinthető legyen, ahogy az a következő ábrán is látható:

    [![Diagram az összes idősorozathoz.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. Válassza ki az időtartomány-választót a jobb felső sarokban. Itt kiválaszthatja az adott kezdési és befejezési időpontot az ezredmásodpercre, vagy az előre konfigurált beállítások közül választhat, például az **elmúlt 30 percben**. Módosíthatja az alapértelmezett időzónát is.

    [![Állítsa be az időtartományt az elmúlt 30 percben.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    A megoldás-gyorsító előrehaladása az **elmúlt 30 percben** már megjelenik a Azure Time Series Insights Gen2 Explorerben.

## <a name="define-and-apply-a-model"></a>Modell meghatározása és alkalmazása

Ebben a szakaszban egy modellt alkalmaz az adatai struktúrájára. A modell elvégzéséhez a típusokat, hierarchiákat és példányokat kell meghatároznia. Ha többet szeretne megtudni az adatmodellezésről, olvassa el a [Time Series modellt](./concepts-model-overview.md).

1. A Explorerben válassza a **modell** fület:

   [![Tekintse meg a modell fület a Explorerben.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   A **típusok** lapon válassza a **+ Hozzáadás** lehetőséget.

1. Adja meg a következő paramétereket:

    | Paraméter | Művelet |
    | --- | ---|
    | **Név** | **Lift** megadása |
    | **Leírás** | Adja meg a **lift típus definícióját** |

1. Ezután válassza a **változók** lapot.

    1. Válassza a **+ változó hozzáadása** lehetőséget, és adja meg a következő értékeket a felvonó első változójának. Összesen három változót fog létrehozni.

        | Paraméter | Művelet |
        | --- | --- |
        | **Név** | Adja meg az **AVG hőmérsékletet**. |
        | **Altípus** | **Numerikus** elem kiválasztása |
        | **Érték** | Válasszon az előre beállított listából: válassza a **hőmérséklet (Double)** lehetőséget. <br /> Megjegyzés: az **értékek** automatikus kitöltését eltarthat néhány percig, miután Azure Time Series Insights Gen2 megkezdi az események fogadását.|
        | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG** elemet. |

    1. Kattintson az **Alkalmaz** gombra. Ezután adja meg ismét a **változót** , és állítsa be a következő értékeket:

        | Paraméter | Művelet |
        | --- | --- |
        | **Név** | Adja meg az **AVG Vibration** értéket. |
        | **Altípus** | **Numerikus** elem kiválasztása |
        | **Érték** | Válasszon az előre beállított listából: válassza a **vibráció (dupla)** lehetőséget. <br /> Megjegyzés: az **értékek** automatikus kitöltését eltarthat néhány percig, miután Azure Time Series Insights Gen2 megkezdi az események fogadását.|
        | **Összesítési művelet** | Bontsa ki a **Speciális beállítások elemet**. <br /> Válassza az **AVG** elemet. |

    1. Kattintson az **Alkalmaz** gombra. Ezután adja meg ismét a **változót** , és állítsa be a következő értékeket a harmadik és a végső változóhoz:

        | Paraméter | Művelet |
        | --- | --- |
        | **Név** | Adja meg a **padlót**. |
        | **Altípus** | **Kategorikus** kijelölése |
        | **Érték** | Válasszon az előre beállított lehetőség közül: válassza a **padló (Double)** lehetőséget. <br /> Megjegyzés: az **értékek** automatikus kitöltését eltarthat néhány percig, miután Azure Time Series Insights Gen2 megkezdi az események fogadását.|
        | **Kategóriák** | <span style="text-decoration: underline">Címke</span>   -  <span style="text-decoration: underline">Értékek</span> <br /> Alsó: 1, 2, 3, 4 <br /> Középső: 5, 6, 7, 8, 9 <br /> Felső: 10, 11, 12, 13, 14, 15 |
        | **Alapértelmezett kategória** | **Ismeretlen** megadása |

        [![Adja meg a típus változóit.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. Kattintson az **Alkalmaz** gombra.
    1. Kattintson a **Mentés** gombra. A rendszer három változót hoz létre és jelenít meg.

        [![A típus hozzáadását követően tekintse át a modellt a modell nézetben.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. Válassza a **hierarchiák** fület. Ezután válassza a **+ Hozzáadás** lehetőséget.

   1. A **hierarchia szerkesztése** panelen állítsa be a következő paramétereket:

        | Paraméter | Művelet |
        | --- | ---|
        | **Név** | Adja meg a **hely hierarchiáját**. |
        |**Szintek**| Adja meg az **országot** az első szint neveként<br />Válassza a **+ szint hozzáadása** lehetőséget<br />Adja meg a második szint **városát** , majd válassza a **+ szint hozzáadása** elemet.<br />A harmadik és utolsó szint neveként adja meg a **Building** nevet |

   1. Kattintson a **Mentés** gombra.

        [![Az új hierarchia megjelenítése a modell nézetben.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. Navigáljon a **példányokhoz**.

    1. A jobb szélen lévő **műveletek** területen kattintson a ceruza ikonra az első példány szerkesztéséhez a következő értékekkel:

        | Paraméter | Művelet |
        | --- | --- |
        | **Típus** | Válassza a **lift** lehetőséget. |
        | **Név** | **1. felvonó** megadása|
        | **Leírás** | Adja meg **az 1. felvonó példányát** |

    1. Lépjen a **példány mezőire** , és adja meg a következő értékeket:

        | Paraméter | Művelet |
        | --- | --- |
        | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
        | **Ország** | **USA** megadása |
        | **City** | **Seattle** megadása |
        | **Létrehozása** | Adja meg a **térközt** |

    1. Kattintson a **Mentés** gombra.

1. Ismételje meg az előző lépést a másik két példánnyal a következő értékek használatakor:

    **2. lift esetén:**

    | Paraméter | Művelet |
    | --- | --- |
    | **Típus** | Válassza a **lift** lehetőséget. |
    | **Név** | **2. felvonó** megadása|
    | **Leírás** | **A 2. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **City** | **Seattle** megadása |
    | **Létrehozása** | A **Pacific Science Center** megadása |

    **3. felvonó esetén:**

    | Paraméter | Művelet |
    | --- | --- |
    | **Típus** | Válassza a **lift** lehetőséget. |
    | **Név** | **3. felvonó** megadása|
    | **Leírás** | **A 3. felvonóhoz tartozó példány** megadása |
    | **Hierarchiák** | **Hely hierarchiájának** kiválasztása |
    | **Ország** | **USA** megadása |
    | **City** | Adja meg a **New York** -i |
    | **Létrehozása** | Adja meg a **Birodalom állapotának kiépítése** |

    [![Tekintse meg a frissített példányokat.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Váltson vissza az **elemzés** lapra a diagram ablaktábla megtekintéséhez. Az idősorozat-példányok megjelenítéséhez a **hely hierarchiája** alatt bontsa ki az összes hierarchia szintet:

    [![Az összes hierarchia megtekintése a diagram nézetben.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. A **Pacific Science Center** alatt válassza ki az idősorozat-példány **2. liftjét**, majd válassza az **átlaghőmérséklet megjelenítése** lehetőséget.

1. Ugyanannak a példánynak a **2. liftben** válassza a **padló megjelenítése** lehetőséget.

    A kategorikus változóval meghatározhatja, hogy a lift mennyi idő után kerüljön a felső, alsó és középső emeleten.

    [![A 2. felvonó és a hierarchia megjelenítése.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy elvégezte az oktatóanyagot, törölje a létrehozott erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden erőforrás** lehetőséget, keresse meg a Azure Time Series Insights Gen2 erőforráscsoportot.
1. Törölje a teljes erőforráscsoportot (és az abban található összes erőforrást) úgy, hogy kiválasztja az egyes erőforrások **törlését** vagy eltávolítását.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Eszköz-szimulációs gyorssegéd létrehozása és használata.
* Hozzon létre egy Azure Time Series Insights Gen2 TB-környezetet.
* A Azure Time Series Insights Gen2-környezet összekötése egy IOT hubhoz.
* Egy megoldás-gyorsító minta futtatásával továbbíthatja az adatátvitelt a Azure Time Series Insights Gen2-környezetbe.
* Végezze el az adatok alapszintű elemzését.
* Definiáljon egy idősorozat-modell típusát és hierarchiáját, majd társítsa azokat a példányokhoz.

Most, hogy már tudja, hogyan hozhatja létre saját Azure Time Series Insights Gen2-környezetét, ismerkedjen meg a Azure Time Series Insights Gen2 kapcsolatos főbb fogalmakkal.

További információ a Azure Time Series Insights Gen2 betöltéséről:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 adatfeldolgozás áttekintése](./concepts-ingestion-overview.md)

További információ a Azure Time Series Insights Gen2 Storage-ról:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2-adattárolás](./concepts-storage.md)

További információ a Time Series-modellekről:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 adatmodellezés](./concepts-model-overview.md)

További információ a környezet Power BIhoz való csatlakoztatásáról:

> [!div class="nextstepaction"]
> [Adatok megjelenítése Azure Time Series Insights Gen2 a Power BI](./how-to-connect-power-bi.md)
