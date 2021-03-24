---
title: Oktatóanyag – mérőszámok és naplók beállítása és használata Azure IoT hub használatával
description: 'Oktatóanyag: mérőszámok és naplók beállítása és használata az Azure IoT hub használatával. Ez biztosítja az elemzéshez szükséges, a hub által esetlegesen felmerülő problémák diagnosztizálását.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 1e2983fb50ce3ad1482db85025677cba5fee0c2e
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889593"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Oktatóanyag: mérőszámok és naplók beállítása és használata IoT hub használatával

A Azure Monitor használatával összegyűjtheti az IoT hub mérőszámait és naplóit, amelyek segítségével figyelheti a megoldás működését, és elháríthatja a problémákat, amikor azok bekövetkeznek. Ebből a cikkből megtudhatja, hogyan hozhat létre diagramokat mérőszámok alapján, hogyan hozhat létre mérőszámokat kiváltó riasztásokat, hogyan küldheti el IoT Hub műveleteit és hibáit Azure Monitor naplókba, és hogyan ellenőrizze a hibákat a naplókban.

Ez az oktatóanyag az Azure-mintát használja a [.net send telemetria](quickstart-send-telemetry-dotnet.md) rövid útmutatójában, hogy üzeneteket küldjön az IoT hub-nak. Az üzenetek küldéséhez mindig használhat egy eszközt vagy egy másik mintát, de előfordulhat, hogy módosítania kell néhány lépést ennek megfelelően.

Az oktatóanyag megkezdése előtt néhány ismerettel Azure Monitor fogalmak hasznosak lehetnek. További információért lásd: [IoT hub figyelése](monitor-iot-hub.md). Ha többet szeretne megtudni a IoT Hub által kibocsátott mérőszámokról és erőforrás-naplókról, tekintse meg a [figyelési adatok referenciáját](monitor-iot-hub-reference.md).

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
>
> * Az Azure CLI használatával hozzon létre egy IoT hubot, regisztráljon egy szimulált eszközt, és hozzon létre egy Log Analytics munkaterületet.  
> * IoT Hub kapcsolatok és az eszköz telemetria erőforrás-naplófájljainak küldése a Log Analytics munkaterület Azure Monitor naplóiba.
> * A metrika-kezelővel létrehozhat egy diagramot a kiválasztott mérőszámok alapján, és rögzítheti azt az irányítópulton.
> * Hozzon létre metrikus riasztásokat, így e-mailben értesítheti, ha fontos feltételek történnek.
> * Töltsön le és futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az IoT hub-nak.
> * A riasztások megtekintése a feltételek bekövetkezésekor.
> * Tekintse meg a metrikák diagramot az irányítópulton.
> * IoT Hub hibák és műveletek megtekintése Azure Monitor naplókban.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- A fejlesztői gépen a .NET Core SDK 2,1-es vagy újabb szükséges. A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

  A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

  ```cmd/sh
  dotnet --version
  ```

- E-mail fiók, amely képes e-mailek fogadására.

- Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Erőforrások beállítása

Ebben az oktatóanyagban szüksége lesz egy IoT hubhoz, egy Log Analytics munkaterületre és egy szimulált IoT-eszközre. Ezek az erőforrások létrehozhatók az Azure CLI vagy az Azure PowerShell használatával. Ugyanazt az erőforráscsoportot és helyet használja minden erőforráshoz. Ezután az oktatóanyag elvégzése után eltávolíthatja az összes lépést az erőforráscsoport törlésével.

Itt láthatók a szükséges lépések.

1. Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md).

2. Hozzon létre egy IoT hubot.

3. Egy Log Analytics-munkaterület létrehozása.

4. Regisztrálja a szimulált eszköz identitását, amely üzeneteket küld az IoT hubhoz. Mentse a szimulált eszköz konfigurálásához használandó eszköz-kapcsolódási karakterláncot.

### <a name="set-up-resources-using-azure-cli"></a>Erőforrások beállítása az Azure CLI-vel

Másolja és illessze be az alábbi szkriptet a Cloud Shellbe. A szolgáltatás azt feltételezi, hogy már bejelentkezett, és soronként futtatja a szkriptet. Néhány parancs végrehajtása hosszabb időt is igénybe vehet. Az új erőforrások az erőforráscsoport *ContosoResources* jönnek létre.

Egyes erőforrások nevének egyedinek kell lennie az Azure-ban. A szkript létrehoz egy véletlenszerű értéket a `$RANDOM` függvénnyel, és egy változóban tárolja. Ezekhez az erőforrásokhoz a parancsfájl hozzáfűzi ezt a véletlenszerű értéket az erőforrás alapneveként, így egyedivé teszi az erőforrás nevét.

Előfizetés esetén csak egy ingyenes IoT hub engedélyezett. Ha már rendelkezik egy ingyenes IoT-hubhoz az előfizetésében, törölje a parancsfájl futtatása előtt, vagy módosítsa a szkriptet az ingyenes IoT hub vagy a standard vagy alapszintű IoT Hub használatára.

A szkript kinyomtatja az IoT hub nevét, a Log Analytics munkaterület nevét és az általa regisztrált eszközhöz tartozó kapcsolatok karakterláncát. Ügyeljen rá, hogy ezeket a cikk későbbi szakaszában jegyezze fel.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Az eszköz identitásának létrehozásakor a következő hibaüzenet jelenhet meg: *IoT hub ContosoTestHub házirend Iothubowner nem találhatók kulcsok*. A hiba elhárításához frissítse az Azure CLI IoT bővítményét, majd futtassa újra a parancsfájl utolsó két parancsát. 
>
>Itt látható a bővítmény frissítésére szolgáló parancs. Futtassa ezt a parancsot a Cloud Shell-példányban.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Naplók összegyűjtése a kapcsolatok és az eszközök telemetria

IoT Hub az erőforrás-naplókat a művelet számos kategóriájára kibocsátja; a naplók megtekintéséhez azonban létre kell hoznia egy diagnosztikai beállítást, amely a célhelyre küldi őket. Az egyik ilyen cél Azure Monitor naplók, amelyeket a rendszer egy Log Analytics munkaterületen gyűjt. IoT Hub az erőforrás-naplók különböző kategóriákba vannak csoportosítva. Kiválaszthatja, hogy mely kategóriákat szeretné elküldeni a diagnosztikai beállításban Azure Monitor naplókba. Ebben a cikkben a kapcsolatokkal és az eszközök telemetria kapcsolatban felmerülő műveletekkel és hibákkal kapcsolatos naplókat gyűjtjük. A IoT Hub által támogatott kategóriák teljes listáját lásd: [IoT hub erőforrás-naplók](monitor-iot-hub-reference.md#resource-logs).

A következő lépésekkel hozhat létre diagnosztikai beállítást IoT Hub erőforrás-naplók küldéséhez Azure Monitor naplókba:

1. Először is, ha a portálon még nem szerepel a központban, válassza az **erőforráscsoportok** lehetőséget, és válassza ki az erőforráscsoport ContosoResources. Válassza ki az IoT hubot a megjelenő erőforrások listájából.

1. Keresse meg a **figyelés** szakaszt a IoT hub panelen. Válassza a **diagnosztikai beállítások** lehetőséget. Ezután válassza a **diagnosztikai beállítás hozzáadása** lehetőséget.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Képernyőfelvétel: a figyelés szakasz diagnosztikai beállításainak kiemelése.":::

1. A **diagnosztika beállítása** panelen adjon meg egy leíró nevet, például a "kapcsolatok küldése és a telemetria a naplókhoz" beállítást.

1. A **Kategória részletei** területen válassza a **kapcsolatok** és az **eszköz telemetria** elemet.

1. A **célhely részletei** területen válassza a **Küldés log Analyticsba** lehetőséget, majd a log Analytics munkaterület-választó használatával válassza ki a korábban feljegyzett munkaterületet. Ha elkészült, a diagnosztikai beállításnak az alábbi képernyőképhez hasonlóan kell kinéznie:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="A diagnosztikai napló utolsó beállításait bemutató képernyőkép.":::

1. A beállítások mentéséhez válassza a **Mentés** lehetőséget. A **diagnosztikai beállítások** panel bezárásához. Az új beállításokat a diagnosztikai beállítások listájában tekintheti meg.

## <a name="set-up-metrics"></a>Mérőszámok beállítása

Most a metrikák Explorerrel hozzunk létre egy diagramot, amely a nyomon követni kívánt metrikákat jeleníti meg. Ezt a diagramot a Azure Portal alapértelmezett irányítópultján fogja rögzíteni.

1. Az IoT hub bal oldali paneljén válassza a **metrikák** lehetőséget a **figyelés** szakaszban.

1. A képernyő felső részén válassza az **elmúlt 24 óra (automatikus)** lehetőséget. A megjelenő legördülő menüben válassza az **utolsó 4 óra** az **időtartományhoz** lehetőséget, állítsa az **időrészletességet** **1 percre**, majd válassza a **helyi** lehetőséget az **idő megjelenítéséhez**. A beállítások mentéséhez kattintson az **alkalmaz** gombra. A beállításnak most a helyi idő szerint kell megjelennie **: az elmúlt 4 óra (1 perc)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="A metrikák időbeállítását bemutató képernyőkép.":::

1. A diagramon egy részleges metrikai beállítás jelenik meg, amely az IoT hub hatókörén alapul. Hagyja meg a **hatókör** és a **metrika névterének** értékeit az alapértelmezett értékeken. Válassza ki a **mérőszám** beállítását, írja be a "telemetria" kifejezést, majd válassza ki a legördülő menüből **küldött telemetria-üzeneteket** . Az **Összesítés** automatikusan a **Sum** értékre lesz beállítva. Figyelje meg, hogy a diagram címe is megváltozik.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Képernyőkép, amely megjeleníti a metrika a diagramba küldött telemetria-üzenetek hozzáadását.":::

1. Most válassza a **metrika hozzáadása** lehetőséget, ha egy másik mérőszámot szeretne hozzáadni a diagramhoz. A **metrika** területen válassza ki **a felhasznált üzenetek teljes számát**. Az **Összesítés** automatikusan **AVG** értékre lesz állítva. Figyelje meg, hogy a diagram címe módosult, hogy tartalmazza ezt a metrikát.

   Mostantól a képernyőn látható az *elküldött telemetria-üzenetek* kisméretű mérőszáma, valamint a *felhasznált üzenetek teljes számának* új mérőszáma.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Képernyőfelvétel: a diagramhoz felhasznált üzenetek teljes számának hozzáadása.":::

1. A diagram jobb felső sarkában válassza a **rögzítés az irányítópulton** lehetőséget.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Képernyőkép, amely kiemeli a rögzítés az irányítópulton gombot.":::

1. A **rögzítés az irányítópulton** ablaktáblán válassza a **meglévő** lapot. Válassza a **magánjellegű** lehetőséget, majd az irányítópult legördülő listából válassza az **irányítópult** lehetőséget. Végül a **rögzítés** gombra kattintva rögzítheti a diagramot az alapértelmezett irányítópulton Azure Portalban. Ha nem rögzít a diagramot egy irányítópulton, a beállítások nem maradnak meg a metrika Explorer bezárásakor.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Az irányítópulton a rögzítés beállításait megjelenítő képernyőkép.":::

## <a name="set-up-metric-alerts"></a>Metrikai riasztások beállítása

Most be kell állítania a riasztásokat, hogy a rendszer *elküldje két metrika telemetria-üzenetét* és a *felhasznált üzenetek teljes számát*.

Az *elküldött telemetria-üzenetek* jó mérőszámot biztosítanak az üzenetek átviteli sebességének nyomon követéséhez és a szabályozás elkerüléséhez. Az ingyenes szinten lévő IoT Hub esetén a szabályozási korlát 100 üzenet/mp. Egyetlen eszköz esetében nem tudjuk elérni ezt az átviteli sebességet, ezért a riasztást úgy állítjuk be, hogy aktiválja, ha az üzenetek száma meghaladja az 1000-et egy 5 perces időszakban. Éles környezetben a jelzést a IoT hub szintjei, kiadása és egységeinek száma alapján sokkal jelentősebb értékre állíthatja.

A *felhasznált üzenetek teljes száma* nyomon követi a felhasznált üzenetek napi számát. Ez a metrika minden nap 00:00 UTC időpontban alaphelyzetbe áll. Ha túllépi a napi kvótát egy bizonyos küszöbértéknél, akkor a IoT Hub többé nem fogad üzeneteket. Az ingyenes szintű IoT Hub a napi üzenet kvótája 8000. Beállítjuk a riasztást, amely akkor aktiválódik, ha az üzenetek teljes száma meghaladja a kvóta 4000, 50%-át. A gyakorlatban valószínűleg magasabb értékre állítja ezt a százalékot. A napi kvóta értéke az IoT hub szintjétől, kiadástól és egységeinek számától függ.

A kvóta-és szabályozási korlátozásokkal kapcsolatos további információkért lásd: [kvóták és sávszélesség-szabályozás](iot-hub-devguide-quotas-throttling.md)IoT hub.

Metrikai riasztások beállítása:

1. Nyissa meg Azure Portal a IoT hubot.

1. A **figyelés** területen válassza a **riasztások** lehetőséget. Ezután válassza az **új riasztási szabály** lehetőséget.  Megnyílik a **riasztási szabály létrehozása** panel.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="A riasztási szabály létrehozása panelt ábrázoló képernyőfelvétel":::

    A **riasztási szabály létrehozása** panelen négy szakaszt talál:

    * A **hatókör** már be van állítva az IoT hub-ra, ezért ezt a szakaszt külön fogjuk hagyni.
    * A **feltétel** megadja a riasztást kiváltó jeleket és feltételeket.
    * A **műveletek** azt konfigurálják, hogy mi történjen a riasztás indításakor.
    * A **riasztási szabály részletei** lehetővé teszi a riasztás nevének és leírásának megadását.

1. Először konfigurálja azt a feltételt, amelyet a riasztás aktivál majd.

    1. A **feltétel** területen válassza a **feltétel hozzáadása** lehetőséget. A **jel logikájának konfigurálása** panelen írja be a "telemetria" kifejezést a keresőmezőbe, és válassza ki a **telemetria küldött üzeneteket**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="A metrika kijelölését bemutató képernyőkép.":::

    1. A **jel logikájának konfigurálása** panelen állítsa be vagy erősítse meg a következő mezőket a **riasztási logika** alatt (figyelmen kívül hagyhatja a diagramot):

       **Küszöbérték**:  *statikus*.

       **Operátor**: *nagyobb, mint*.

       **Összesítés típusa**: *összesen*.

       **Küszöbérték**: 1000.

       **Összesítés részletessége (időszak)**: *5 perc*.

       **Értékelés gyakorisága**: *1 percenként*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="A riasztási feltételek beállításait megjelenítő képernyőkép.":::

       Ezek a beállítások úgy állítja be a jelet, hogy az üzenetek száma összesen 5 percen belül megtörténjen. Ez az összeg percenként lesz kiértékelve, és ha az előző 5 percben megadott összeg meghaladja az 1000-as üzenetet, a riasztás elindul.

       A jel logikájának mentéséhez válassza a **kész** lehetőséget.

1. Most konfigurálja a riasztás műveletét.

    1. Vissza a **riasztási szabály létrehozása** panel **műveletek** területén válassza a **műveleti csoportok hozzáadása** elemet. A **riasztási szabályhoz csatolni kívánt műveleti csoport kiválasztása** panelen válassza a **műveleti csoport létrehozása** lehetőséget.

    1. A **műveleti csoport létrehozása** panel **alapismeretek** lapján adja meg a műveleti csoport nevét és a megjelenítendő nevet.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Képernyőfelvétel: a műveleti csoport létrehozása ablaktábla alapok lapja.":::

    1. Válassza az **értesítések** lapot. Az **értesítés típusa** beállításnál válassza a legördülő listából az **e-mail/SMS-üzenet/leküldés/hang** lehetőséget. Megnyílik az **e-mail/SMS-üzenet/leküldéses/hang** panel.

    1. Az **e-mail/SMS-üzenet/leküldés/hang** panelen válassza az e-mail lehetőséget, és adja meg az e-mail-címét, majd kattintson **az OK gombra**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Az e-mail cím beállítását megjelenítő képernyőfelvétel":::

    1. Az **értesítések** ablaktáblán lépjen vissza az értesítés nevére.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="A befejezett értesítések panelt bemutató képernyőkép.":::

    1. Választható Ha a **műveletek** lapot választja, majd kiválasztja a **művelet típusa** legördülő menüt, láthatja, hogy milyen típusú műveleteket indíthat el riasztással. Ebben a cikkben csak az értesítéseket fogjuk használni, így figyelmen kívül hagyhatja az ezen a lapon lévő beállításokat.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Képernyőfelvétel a Műveletek ablaktáblán elérhető tevékenységtípusok megjelenítéséhez.":::

    1. Válassza a **felülvizsgálat és létrehozás** lapot, ellenőrizze a beállításokat, majd válassza a **Létrehozás** lehetőséget.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="A felülvizsgálat és létrehozás ablaktáblát ábrázoló képernyőkép.":::

    1. Vissza a **riasztási szabály létrehozása** panelen, figyelje meg, hogy az új műveleti csoport hozzá lett adva a riasztás műveleteihez.  

1. Végül konfigurálja a riasztási szabály részleteit, és mentse a riasztási szabályt.

    1. A riasztási **szabály létrehozása** ablaktábla riasztási szabály részletei területén adja meg a riasztás nevét és leírását; például: "riasztás, ha több mint 1000 üzenet 5 perc alatt". Győződjön meg arról, hogy a **létrehozáskor a riasztási szabály engedélyezése** jelölőnégyzet be van jelölve. A befejezett riasztási szabály ehhez a képernyőképhez hasonlóan fog kinézni.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Képernyőfelvétel: a befejezési riasztási szabály létrehozása panel.":::

    1. Az új szabály mentéséhez válassza a **riasztási szabály létrehozása** lehetőséget.

1. Most állítson be egy újabb riasztást a *felhasznált üzenetek teljes számára* vonatkozóan. Ez a metrika akkor lehet hasznos, ha riasztást szeretne küldeni, ha a felhasznált üzenetek száma közeledik az IoT hub napi kvótájának eléréséhez, ekkor az IoT hub elutasítja az üzenetek elutasítását. Kövesse a korábban végrehajtott lépéseket a következő eltérésekkel.

    * A **jel logikai beállítása** ablaktáblán válassza ki a **használt üzenetek teljes számát**.

    * A **jel logikájának konfigurálása** panelen állítsa be vagy erősítse meg a következő mezőket (a diagram figyelmen kívül hagyható):

       **Küszöbérték**:  *statikus*.

       **Operátor**: *nagyobb, mint*.

       **Összesítés típusa**: *maximum*.

       **Küszöbérték**: 4000.

       **Összesítés részletessége (időszak)**: *1 perc*.

       **Értékelés gyakorisága**: *1 percenként*

       Ezekkel a beállításokkal állítható be a jel, ha az üzenetek száma eléri a 4000 értéket. A metrika percenként lesz kiértékelve.

    * Amikor megadja a riasztási szabályhoz tartozó műveletet, egyszerűen válassza ki a korábban létrehozott műveleti csoportot.

    * A riasztás részleteinek megtekintéséhez válasszon egy másik nevet és leírást, mint korábban.

1. Válassza a **riasztások** lehetőséget a **figyelés** elemnél az IoT hub bal oldali paneljén. Most válassza a **riasztási szabályok kezelése** elemet a **riasztások** ablaktábla tetején található menüben. Megnyílik a **szabályok** ablaktábla. Ekkor megjelenik a két riasztás:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Képernyőfelvétel: a szabályok panel az új riasztási szabályokkal.":::

1. A **szabályok** ablaktábla bezárásához.

Ezekkel a beállításokkal a rendszer riasztást küld, és e-mailben értesítést fog kapni, ha több mint 1000 üzenetet küld egy 5 perces időtartamon belül, valamint azt is, hogy a felhasznált üzenetek teljes száma meghaladja a 4000 (az ingyenes szinten lévő IoT hub napi kvótájának 50%-ában).

## <a name="run-the-simulated-device-app"></a>A szimulált eszköz alkalmazásának futtatása

Az [erőforrások beállítása](#set-up-resources) szakaszban regisztrálta a IoT-eszköz használatával történő szimulálás során használt eszköz identitását. Ebben a szakaszban olyan .NET-konzol alkalmazást tölt le, amely egy eszközről a felhőbe irányuló üzeneteket küldő eszközt szimulál egy IoT Hubba, konfigurálja úgy, hogy elküldi ezeket az üzeneteket az IoT hubhoz, majd futtassa azt.

> [!IMPORTANT]
>
> A riasztások akár 10 percet is igénybe vehetnek, hogy IoT Hub. Várjon legalább 10 percet a legutóbbi riasztás konfigurálása és a szimulált eszköz alkalmazás futtatása között.

Az [IoT-eszköz szimulációjára](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) szolgáló megoldás letöltése. Ez a hivatkozás egy tárházat tölt le több alkalmazással; a keresett IOT-hub/rövid útmutató/szimulált eszköz/.

1. A helyi terminál ablakban navigáljon a megoldás gyökérkönyvtárához. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.cs** fájlt egy Ön által választott szövegszerkesztőben.

    1. Cserélje le a változó értékét `s_connectionString` arra az eszköz-kapcsolódási sztringre, amelyet a parancsfájlnak az erőforrások beállításához való futtatásakor feljegyzett.

    1. A `SendDeviceToCloudMessagesAsync` metódusban módosítsa a `Task.Delay` 1000 – 1 értéket, amely csökkenti az üzenetek 1 másodperc és 0,001 másodperc közötti elküldésének időtartamát. A késleltetés lerövidítése növeli az elküldött üzenetek számát. (Előfordulhat, hogy a másodpercenkénti 100-as üzenet-arányt nem fogja kapni.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Mentse a módosításokat a **SimulatedDevice. cs** fájlba.

1. A helyi terminál ablakban futtassa a következő parancsot a szükséges csomagok telepítéséhez a szimulált eszköz alkalmazáshoz:

    ```cmd/sh
    dotnet restore
    ```

1. Futtassa az alábbi parancsot a helyi terminálablakban a szimulálteszköz-alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="A szimulált eszköz kimenetét ábrázoló képernyőkép.":::

Hagyja, hogy az alkalmazás legalább 10-15 percig fusson. Ideális esetben futtassa a futtatást, amíg leállítja az üzenetek küldését (körülbelül 20-30 perc). Ez akkor fordulhat elő, ha túllépte az IoT hub napi üzenetének kvótáját, és leállt a további üzenetek fogadása.

> [!NOTE]
> Ha az alkalmazás nem az üzenetek küldésének leállítását követően hosszabb ideig nem fut az eszközön, kivételt tapasztalhat. Ezt a kivételt nyugodtan figyelmen kívül hagyhatja, és az alkalmazás ablakát is lezárhatja.

## <a name="view-metrics-chart-on-your-dashboard"></a>Metrikai diagram megtekintése az irányítópulton

1. A Azure Portal bal felső sarkában nyissa meg a portál menüt, majd válassza az **irányítópult** lehetőséget.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Képernyőkép az irányítópult kiválasztásáról.":::

1. Keresse meg a korábban rögzített diagramot, és kattintson a diagramon a csempén kívül bárhová a kibontáshoz. Megjeleníti az elküldött telemetria-üzeneteket, valamint a diagramon használt üzenetek teljes számát. A legfrissebb számok a diagram alján jelennek meg. A kurzort áthelyezheti a diagramon a megadott időpontok metrikai értékeinek megjelenítéséhez. Az időértéket és a részletességet a diagram tetején is megváltoztathatja, hogy szűkítse vagy bővítse az adatokat egy adott időpontra.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="A metrikák diagramját ábrázoló képernyőfelvétel":::

   Ebben a forgatókönyvben a szimulált eszköz üzeneteinek átviteli sebessége nem elég nagy ahhoz, hogy IoT Hub az üzeneteinek szabályozásához. Az olyan forgatókönyvekben, amelyek ténylegesen szabályozzák a szabályozást, láthatják, hogy az elküldött telemetria-üzenetek túllépik a IoT hub korlátozási korlátját korlátozott ideig. Ez a burst forgalom befogadására szolgál. Részletekért lásd: [Traffic Shaping](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Riasztások megtekintése

Ha az elküldött üzenetek száma meghaladja a riasztási szabályokban beállított korlátokat, elindíthatja az értesítő e-maileket.

Ha meg szeretné tudni, hogy vannak-e aktív riasztások, válassza a **riasztások** elemet a **figyelés** elemnél az IoT hub bal oldali paneljén. A **riasztások** ablaktábla megjeleníti a megadott időtartomány súlyossági sorrendje szerint rendezett riasztások számát.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="A riasztások összegzését bemutató képernyőkép.":::

Válassza ki a 3. súlyossági szinthez tartozó sort. Ekkor megnyílik a **minden riasztás** panel, és megjeleníti a kilőtt 3. három riasztást.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Képernyőfelvétel a minden riasztás panelről.":::

Válassza ki az egyik riasztást a riasztás részleteinek megtekintéséhez.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="A riasztás részleteit megjelenítő képernyőkép.":::

A Beérkezett üzenetek mappában található e-mailek Microsoft Azure. A Tárgy sor az aktivált riasztást írja le. Például az *Azure: aktivált súlyosság: 3 riasztás, ha több mint 1000 üzenet 5 percen belül*. A törzs a következő képhez hasonlóan fog kinézni:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="A riasztásokat bemutató e-mail képernyőképe.":::

## <a name="view-azure-monitor-logs"></a>Azure Monitor naplók megtekintése

A [kapcsolatok és eszközök telemetria gyűjtése](#collect-logs-for-connections-and-device-telemetry) című szakaszban létrehozott egy diagnosztikai beállítást, amellyel az IoT hub által kibocsátott erőforrás-naplókat küldhet a kapcsolatok és az eszközök telemetria műveleteihez a naplók Azure monitor. Ebben a szakaszban egy Kusto-lekérdezést fog futtatni Azure Monitor naplókon, hogy figyelje az esetleges hibákat.

1. Az IoT hub bal oldali ablaktáblájának **figyelés** területén Azure Portal válassza a **naplók** lehetőséget. Ha megnyílik, a kezdeti **lekérdezések** ablak bezárásához.

1. Az új lekérdezés ablaktáblán válassza a **lekérdezések** fület, majd bontsa ki **IoT hub** az alapértelmezett lekérdezések listájának megtekintéséhez.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Képernyőkép IoT Hub alapértelmezett lekérdezésekről.":::

1. Válassza ki a *hiba összegző* lekérdezését. A lekérdezés megjelenik a lekérdezés-szerkesztő ablaktáblán. Válassza a **Futtatás** lehetőséget a szerkesztő ablaktáblán, és figyelje meg a lekérdezés eredményeit. Bontsa ki az egyik sort a részletek megjelenítéséhez.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Képernyőfelvétel a hibák összegző lekérdezés által visszaadott naplókról.":::

   > [!NOTE]
   > Ha nem lát hibát, próbálja meg futtatni a *nemrégiben csatlakoztatott eszközök* lekérdezését. Ennek egy sort kell visszaadnia a szimulált eszközhöz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyagban létrehozott összes erőforrás eltávolításához törölje az erőforráscsoportot. Ez a művelet törli a csoportban lévő összes erőforrást. Ebben az esetben eltávolítja az IoT hubot, a Log Analytics munkaterületet és magát az erőforráscsoportot. Ha rögzített mérőszámokat tartalmazó diagramokat az irányítópulthoz, akkor manuálisan el kell távolítania őket a diagram jobb felső sarkában található három pontra, majd az **Eltávolítás** lehetőséget választva. A diagramok törlése után ne felejtse el menteni a módosításokat.

Az erőforráscsoport az [az group delete](/cli/azure/group#az-group-delete) paranccsal távolítható el.

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja IoT Hub metrikákat és naplókat a következő feladatok végrehajtásával:

> [!div class="checklist"]
>
> * Az Azure CLI használatával hozzon létre egy IoT hubot, regisztráljon egy szimulált eszközt, és hozzon létre egy Log Analytics munkaterületet.  
> * IoT Hub kapcsolatok és az eszköz telemetria erőforrás-naplófájljainak küldése a Log Analytics munkaterület Azure Monitor naplóiba.
> * A metrika-kezelővel létrehozhat egy diagramot a kiválasztott mérőszámok alapján, és rögzítheti azt az irányítópulton.
> * Hozzon létre metrikus riasztásokat, így e-mailben értesítheti, ha fontos feltételek történnek.
> * Töltsön le és futtasson egy alkalmazást, amely egy IoT-eszközt szimulál, amely üzeneteket küld az IoT hub-nak.
> * A riasztások megtekintése a feltételek bekövetkezésekor.
> * Tekintse meg a metrikák diagramot az irányítópulton.
> * IoT Hub hibák és műveletek megtekintése Azure Monitor naplókban.

A következő oktatóanyag az IoT-eszközök állapotának kezelését mutatja be. 

> [!div class="nextstepaction"]
> [Eszközök konfigurálása háttérszolgáltatásból](tutorial-device-twins.md)