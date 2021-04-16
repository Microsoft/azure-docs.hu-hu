---
title: Eszközök automatikus kezelése a Device Provisioning Service használatával
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan állíthat be automatikus folyamatot az IoT-eszközök üzembe Azure Digital Twins a Device Provisioning Service (DPS) használatával.
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2ee2aad290c03743d8a2627922446b8167f3ffee
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480418"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Eszközök automatikus kezelése a Azure Digital Twins Device Provisioning Service (DPS) használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja a Azure Digital Twins [a Device Provisioning Service (DPS) szolgáltatással.](../iot-dps/about-iot-dps.md)

A cikkben ismertetett megoldással automatizálhatja a **** IoT Hub-eszközök üzembe Azure Digital Twins a Device Provisioning Service használatával. **** 

A kiépítési és  a kivezetési fázisokkal kapcsolatos további információkért, valamint az összes vállalati [  ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) IoT-projekt általános eszközkezelési szakaszának jobb érthetőségért tekintse meg az IoT Hub eszközkezelési dokumentációJának Eszközök életciklusa szakaszát. 

## <a name="prerequisites"></a>Előfeltételek

A kiépítés beállítása előtt be kell állítania a következőket:
* egy **Azure Digital Twins példány.** Kövesse a [*How-to: Set up an instance and authentication*](how-to-set-up-instance-portal.md) to create a Azure Digital Twins instance (Útmutató: Példány és hitelesítés beállítása) útmutatót egy Azure Digital Twins-példány létrehozásához. Gyűjtse össze a példány **_gazdagépnevét_** a Azure Portal ([utasítások).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)
* egy **IoT Hub.** Útmutatásért tekintse meg *a* IoT Hub útmutató IoT Hub [szakaszát.](../iot-hub/quickstart-send-telemetry-cli.md)
* egy [**Azure-függvény,**](../azure-functions/functions-overview.md) amely frissíti a digitális ikerinformációkat a IoT Hub alapján. Az Azure-függvény létrehozásához kövesse a [*How to: Ingest IoT Hub data (Útmutató: IoT Hub-adatok*](how-to-ingest-iot-hub-data.md) bemenő adatai) útmutatását. Gyűjtse össze **_a függvény nevét_** a cikkben való használathoz.

Ez a minta egy eszközszimulátort **is használ,** amely magában foglalja a Device Provisioning Service használatával való kiépítést. Az eszközszimulátor itt található: [Azure Digital Twins és IoT Hub integrációs minta.](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) A mintaprojektet a mintahivatkozásra navigálva, majd a cím alatti **Kód** tallózása gombra kattintva szerezze be a gépre. Ezzel a minta GitHub-adattárába fog vinni, amelyet letölthet a következőként: *. A* ZIP-fájlhoz válassza a **Kód gombot,** majd **a ZIP letöltése gombot.** 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Képernyőkép a digital-twins-iothub-integration adattárról a GitHubon. A Kód gomb van kiválasztva, amely egy kis párbeszédpanelt hoz, ahol a ZIP letöltése gomb ki van emelve." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Csomagolja ki a letöltött mappát.

A gépre [**Node.js**](https://nodejs.org/download) kell telepítenie. Az eszközszimulátorNode.js **** 10.0.x vagy újabb verzión alapul.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi ábra ennek a megoldásnak az architektúráját mutatja be Azure Digital Twins Device Provisioning Service-Azure Digital Twins használatával. Az eszközbeépítési és -kiépítési folyamatot is megjeleníti.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Az eszköz és számos Azure-szolgáltatás diagramja egy végpontok között. Az adatok oda-vissza áramlnak a termosztátos eszköz és a DPS között. Az adatok a DPS-től a IoT Hub felé áramlnak, és Azure Digital Twins &quot;Allocation&quot; (Foglalás) címkével ellátott Azure-függvényen keresztül. A manuális &quot;Eszköz törlése&quot; műveletből származó adatok a IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Ez a cikk két szakaszra oszlik:
* [*Eszköz automatikus kiépítése a Device Provisioning Service használatával*](#auto-provision-device-using-device-provisioning-service)
* [*Eszközök automatikus kivezetése IoT Hub életciklus-eseményekkel*](#auto-retire-device-using-iot-hub-lifecycle-events)

Az architektúra egyes lépései részletes magyarázatát a cikk későbbi részében, az egyes szakaszokban olvashatja el.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Eszköz automatikus kiépítése a Device Provisioning Service használatával

Ebben a szakaszban a Device Provisioning Service-t fogja csatolni az Azure Digital Twins automatikus üzembehelyezéséhez az alábbi útvonalon. Ez a korábban bemutatott teljes architektúra [kivonata.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="A kiépítési folyamat ábrája – a megoldásarchitektúra diagramja a folyamat számcímkézési szakaszaival. Az adatok oda-vissza áramlnak a termosztátos eszköz és a DPS között (1 az eszköz > DPS és 5 a DPS > eszközhöz). Az adatok a DPS-IoT Hub (4) és a Azure Digital Twins (3) felé is áramlnak egy &quot;Allocation&quot; (2) címkével ellátott Azure-függvényen keresztül." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

A folyamat leírása:
1. Az eszköz kapcsolatba lép a DPS-végponttal, és azonosító adatokat ad át az identitása bizonyítására.
2. A DPS úgy ellenőrzi az eszközidentitást, hogy érvényesíti a [](../azure-functions/functions-overview.md) regisztrációs azonosítót és a kulcsot a regisztrációs listában, és egy Azure-függvényt hív meg a lefoglaláshoz.
3. Az Azure-függvény [](concepts-twins-graph.md) létrehoz egy új ikereszközt Azure Digital Twins az eszközhöz. Az ikereszköz neve megegyezik az eszköz regisztrációs **azonosítójával.**
4. A DPS regisztrálja az eszközt egy IoT Hubon, és feltölti az eszköz kívánt ikerállapotát.
5. Az IoT Hub visszaadja az eszközazonosító adatait és az IoT Hub kapcsolati adatait az eszköznek. Az eszköz most már képes csatlakozni az IoT Hubhoz.

A következő szakaszok az automatikus eszközátépítési folyamat beállításának lépéseit ismertetik.

### <a name="create-a-device-provisioning-service"></a>Device Provisioning Service létrehozása

Amikor új eszközt létesít a Device Provisioning Service használatával, az eszközhöz egy új ikereszköz is Azure Digital Twins a regisztrációs azonosítóval azonos néven.

Hozzon létre egy Device Provisioning Service-példányt, amely ioT-eszközök létrehozására lesz használva. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: Rövid útmutató: A virtuális IoT Hub Device Provisioning Service beállítása a [*Azure Portal.*](../iot-dps/quick-setup-auto-provision.md)

A következő Azure CLI-parancs létrehoz egy Device Provisioning Service-szolgáltatást. Meg kell adnia a Device Provisioning Service nevét, az erőforráscsoportot és a régiót. A Device Provisioning Service-t támogató régiókat a régiónként elérhető [*Azure-termékek között tudja megnézni.*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)
A parancs futtatható a következő [Cloud Shell,](https://shell.azure.com)vagy helyileg, ha az Azure CLI telepítve [van a gépen.](/cli/azure/install-azure-cli)

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>A Device Provisioning Service-sel használható függvény hozzáadása

Az előfeltételek szakaszban létrehozott függvényalkalmazás-projekten belül létre fog hozni egy új függvényt a Device Provisioning Service-hez. [](#prerequisites) Ezt a függvényt a Device Provisioning Service egy egyéni kiosztási szabályzatban [egy](../iot-dps/how-to-use-custom-allocation-policies.md) új eszköz kiosztására fogja használni.

Első lépésként indítsa el a függvényalkalmazás-projektet Visual Studio a gépen, és kövesse az alábbi lépéseket.

#### <a name="step-1-add-a-new-function"></a>1. lépés: Új függvény hozzáadása 

Adjon hozzá egy *HTTP-trigger* típusú új függvényt a függvényalkalmazás projekthez a Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="Képernyőkép a Visual Studio nézetről, amely http trigger típusú Azure-függvényt ad hozzá a függvényalkalmazás projektjéhez." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>2. lépés: A függvénykód kitöltése

Adjon hozzá egy új NuGet-csomagot a projekthez: [Microsoft.Azure.Devices.Provisioning.Service.](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) Előfordulhat, hogy további csomagokat is hozzá kell adnia a projekthez, ha a kódban használt csomagok még nem részei a projektnek.

Az újonnan létrehozott függvénykódfájlba illessze be a következő kódot, nevezze át a függvényt *DpsAdtAllocationFunc.cs* névre, és mentse a fájlt.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. lépés: A függvényalkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *DpsAdtAllocationFunc.cs függvény* használatával a függvényalkalmazásban az Azure-ban.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Eszközregisztrációs regisztráció létrehozása

Ezután létre kell hoznia egy regisztrációt a Device Provisioning Service-be egy egyéni kiosztási **függvény használatával.** Ehhez kövesse a Device [](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) Provisioning Service dokumentációjának Egyéni kiosztási szabályzatok létrehozása szakaszának Regisztráció szakaszát.

A folyamat közben győződjön meg arról, hogy a következő lehetőségeket választja, hogy a regisztrációt az újonnan létrehozott függvényhez csatolja.

* **Válassza ki, hogyan szeretné hozzárendelni az eszközöket a központokhoz:** Egyéni (Az Azure-függvény használata).
* Válassza ki azokat az **IoT Hubokat, amelyekhez ez a csoport hozzárendelhető:** Válassza ki az IoT Hub nevét, vagy válassza az Új IoT Hub csatolása *gombot,* és válassza ki az IoT Hubot a legördülő menüből.

Ezután válassza az *Új függvény kiválasztása gombot,* hogy a függvényalkalmazást a regisztrációs csoporthoz csatolja. Ezután töltse ki a következő értékeket:

* **Előfizetés:** Az Azure-előfizetés automatikusan ki van töltve. Győződjön meg arról, hogy ez a megfelelő előfizetés.
* **Függvényalkalmazás:** Válassza ki a függvényalkalmazás nevét.
* **Függvény:** Válassza a DpsAdtAllocationFunc lehetőséget.

Mentse a részleteket.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Képernyőkép a TAT-regisztrációs csoport részletei ablakról, amely az Egyéni kiválasztása (Azure-függvény használata) lehetőséget és az IoT Hub nevét tartalmazza a Select how you want to assign devices to hubs (Válassza ki, hogyan szeretné hozzárendelni az eszközöket a központokhoz) és Select the IoT hubs this group can be assigned to (Válassza ki azokat az IoT Hubokat, amelyekhez ez a csoport rendelhető). Emellett válassza ki az előfizetését, a függvényalkalmazást a legördülő menüből, és ügyeljen arra, hogy a DpsAdtAllocationFunc lehetőséget válassza." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

A regisztráció létrehozása után  a későbbiekben a regisztráció elsődleges kulcsát fogja használni a cikk eszközszimulátorának konfiguráláshoz.

### <a name="set-up-the-device-simulator"></a>Az eszközszimulátor beállítása

Ez a minta egy eszközszimulátort használ, amely magában foglalja a Device Provisioning Service használatával való kiépítést. Az eszközszimulátor az Előfeltételek szakaszban [letöltött](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) Azure Digital Twins IoT Hub [integrációs](#prerequisites) mintában található.

#### <a name="upload-the-model"></a>A modell feltöltése

Az eszközszimulátor egy termosztát típusú eszköz, amely a következő azonosítóval használja a modellt: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Ezt a modellt fel kell töltenie a Azure Digital Twins, mielőtt létrehoz egy ilyen típusú ikereszközt.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

A modellekkel kapcsolatos további információkért lásd: [*How-to: Manage models*](how-to-manage-model.md#upload-models)(A modellek kezelése).

#### <a name="configure-and-run-the-simulator"></a>A szimulátor konfigurálása és futtatása

A parancsablakban keresse meg a korábban kicsomagolt Azure Digital Twins és *IoT Hub integrációt,* majd lépjen az eszközszimulátor *könyvtárába.* Ezután telepítse a projekt függőségeit a következő paranccsal:

```cmd
npm install
```

Ezután az eszközszimulátor könyvtárában másolja az .env.template fájlt egy .env nevű új fájlba, és gyűjtse össze a következő értékeket a beállítások kitöltéséhez:

* PROVISIONING_IDSCOPE: Az érték lekért értékéhez lépjen az eszközátépítési szolgáltatásra a Azure Portal [területen,](https://portal.azure.com/)majd válassza az *Áttekintés* lehetőséget a menüben, és keresse meg az Azonosító hatóköre *mezőt.*

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Képernyőkép az Azure Portal áttekintési oldalának képernyőnézetéről az Azonosító hatóköre érték másolására." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: Kiválaszthatja az eszköz regisztrációs azonosítóját.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Ez a korábban beállított regisztráció elsődleges kulcsa. Az érték újra lekért értékéhez lépjen az eszközregisztrációs szolgáltatáshoz a Azure Portal, válassza a *Regisztrációk* kezelése lehetőséget, majd válassza ki a korábban létrehozott regisztrációs csoportot, és másolja ki az elsődleges *kulcsot.*

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Képernyőkép az Azure Portal eszközregisztrációs szolgáltatás regisztrációk kezelése oldalának képernyőképe az SAS elsődleges kulcs értékének másolására." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Most használja a fenti értékeket az .env fájl beállításainak frissítéséhez.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Mentse és zárja be a fájlt.

### <a name="start-running-the-device-simulator"></a>Az eszközszimulátor futtatásának elkezde

Még mindig az *eszközszimulátor könyvtárában* a parancsablakban, és indítsa el az eszközszimulátort a következő paranccsal:

```cmd
node .\adt_custom_register.js
```

Látnia kell, hogy az eszköz regisztrálva van, IoT Hub csatlakozik az eszközhöz, majd elkezd üzeneteket küldeni.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Képernyőkép a Parancsablak eszközregisztrációról és az üzenetek küldéséről" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Érvényesítés

A cikkben beállított folyamat eredményeként az eszköz automatikusan regisztrálva lesz a Azure Digital Twins. A következő [parancssori Azure Digital Twins parancssori](how-to-use-cli.md) felületi paranccsal keresse meg az eszköz ikereszközét a Azure Digital Twins példányban.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Az eszköz ikereszközének meg kell lennie a Azure Digital Twins példányban.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Képernyőkép az újonnan Parancsablak ikereszközről." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Eszközök automatikus kivezetése IoT Hub életciklus-eseményekkel

Ebben a szakaszban egy életciklus-eseményeket fog IoT Hub az eszközök automatikus Azure Digital Twins az alábbi útvonalon keresztül. Ez a korábban bemutatott teljes architektúra [kivonata.](#solution-architecture)

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Az Eszközkiürítés folyamat ábrája – a megoldásarchitektúra-diagram kivonata, a folyamat számcímkézési szakaszaival. A termosztáteszköz úgy jelenik meg a diagramon, hogy nincs kapcsolat az Azure-szolgáltatásokkal. A manuális &quot;Eszköz törlése&quot; művelet adatai a IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

A folyamat leírása:
1. Egy külső vagy manuális folyamat elindítja egy eszköz törlését a IoT Hub.
2. IoT Hub törli az eszközt, és létrehoz [](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) egy eszköz életciklus-eseményét, amely egy [eseményközponthoz lesz irányítva.](../event-hubs/event-hubs-about.md)
3. Az Azure-függvény törli az eszköz ikereszközét a Azure Digital Twins.

A következő szakaszok végigvezetik az automatikus eszköz-kiállítási folyamat beállításának lépéseit.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

A következő lépés egy Azure-eseményközpont [létrehozása](../event-hubs/event-hubs-about.md) a IoT Hub események fogadásához. 

Kövesse az Eseményközpont létrehozása [*rövid*](../event-hubs/event-hubs-create.md) útmutatóban leírt lépéseket. Nevezze el az eseményközpont *életciklusátevents néven.* Ezt az eseményközpontnevet fogja használni, amikor a IoT Hub egy Azure-függvényt a következő szakaszokban.

Az alábbi képernyőképen az eseményközpont létrehozása látható.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Képernyőkép az Azure Portal-ablakról, amely egy lifecycleevents nevű eseményközpontot hoz létre." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>SAS-szabályzat létrehozása az eseményközponthoz

Ezután létre kell hoznia egy közös hozzáférésű jogosultság jogosultsága [(SAS)](../event-hubs/authorize-access-shared-access-signature.md) szabályzatot, amely konfigurálja az eseményközpontot a függvényalkalmazással.
Ehhez:
1. Lépjen az újonnan létrehozott eseményközpontra a Azure Portal  bal oldali menüben válassza a Megosztott hozzáférési szabályzatok lehetőséget.
2. Válassza a **Hozzáadás** lehetőséget. A *megnyíló SAS-szabályzat* hozzáadása ablakban adjon meg egy választott szabályzatnevet, és jelölje be a *Figyelás* jelölőnégyzetet.
3. Válassza a **Létrehozás** lehetőséget.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Képernyőkép az eseményközpont Azure Portal SAS-szabályzat hozzáadásáról." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Eseményközpont konfigurálása függvényalkalmazással

Ezután konfigurálja az előfeltételek szakaszban beállított [](#prerequisites) Azure-függvényalkalmazást, hogy működjön az új eseményközpontbal. Ezt egy környezeti változónak az eseményközpont kapcsolati sztringje segítségével való beállításával fogja megtenni a függvényalkalmazásban.

1. Nyissa meg az előbb létrehozott szabályzatot, és másolja ki a **Kapcsolati sztring – elsődleges kulcs értékét.**

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Képernyőkép a Azure Portal-elsődleges kulcs másolásának célkulcsról." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Adja hozzá a kapcsolati sztringet változóként a függvényalkalmazás beállításaiban az alábbi Azure CLI-paranccsal. A parancs futtatható a következő [Cloud Shell,](https://shell.azure.com)vagy helyileg, ha az Azure CLI telepítve [van a gépen.](/cli/azure/install-azure-cli)

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Az életciklus-eseményeket IoT Hub függvény hozzáadása

Az előfeltételek szakaszban létrehozott függvényalkalmazás-projektben létre fog hozni egy új függvényt, amely egy meglévő eszközt fog kivezetni az IoT Hub életciklus-események használatával. [](#prerequisites)

Az életciklus-eseményekről a nem [*IoT Hub eseményeket.*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) További információ az Azure Functions Event Hubs való használatával kapcsolatban: Azure Event Hubs [*trigger for Azure Functions.*](../azure-functions/functions-bindings-event-hubs-trigger.md)

Első lépésként indítsa el a függvényalkalmazás-projektet Visual Studio a gépen, és kövesse az alábbi lépéseket.

#### <a name="step-1-add-a-new-function"></a>1. lépés: Új függvény hozzáadása
     
Adjon hozzá egy *Event Hub Trigger* típusú új függvényt a függvényalkalmazás-projekthez a Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="Képernyőkép a Visual Studio ablakról, amely egy Event Hub Trigger típusú Azure-függvényt ad hozzá a függvényalkalmazás-projekthez." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>2. lépés: A függvénykód kitöltése

Az újonnan létrehozott függvénykódfájlba illessze be a következő kódot, nevezze át a függvényt a névre, `DeleteDeviceInTwinFunc.cs` majd mentse a fájlt.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>3. lépés: A függvényalkalmazás közzététele az Azure-ban

Tegye közzé a projektet a *DeleteDeviceInTwinFunc.cs* függvény használatával az Azure-beli függvényalkalmazásban.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Útvonal IoT Hub életciklus-eseményekhez

Most be fog állítani egy útvonal-IoT Hub az eszközök életciklus-eseményeinek útválasztására. Ebben az esetben kifejezetten az által azonosított eszköz törlésének eseményeit fogja `if (opType == "deleteDeviceIdentity")` figyelni. Ez aktiválja a digitális ikerelem törlését, és véglegesen törli az eszközt és annak digitális ikereszközét.

Először létre kell hoznia egy eseményközpont-végpontot az IoT Hubban. Ezután hozzáad egy útvonalat az IoT Hubhoz, amely életciklus-eseményeket küld erre az eseményközpont-végpontra.
Eseményközpont-végpont létrehozásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)lépjen az előfeltételek szakaszban létrehozott IoT [](#prerequisites) Hubra,  és válassza az Üzenetirányítás lehetőséget a bal oldali menüben.
2. Válassza az **Egyéni végpontok** lapot.
3. Válassza **a + Hozzáadás,** majd az **Eseményközpontok lehetőséget** egy eseményközpont-típusú végpont hozzáadásához.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Képernyőkép az Visual Studio-végpont hozzáadásához." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. A megnyíló *Eseményközpont-végpont hozzáadása* ablakban válassza a következő értékeket:
    * **Végpont neve:** Válasszon egy végpontnevet.
    * **Eseményközpont-névtér:** Válassza ki az eseményközpont-névteret a legördülő listából.
    * **Eseményközpont-példány:** Válassza ki az előző lépésben létrehozott eseményközpont nevét.
5. Válassza a **Létrehozás** lehetőséget. Tartsa nyitva ezt az ablakot, és adjon hozzá egy útvonalat a következő lépésben.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Képernyőkép az Visual Studio-végpont hozzáadásához." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Ezután hozzáad egy útvonalat, amely a fenti lépésben létrehozott végponthoz csatlakozik egy útválasztási lekérdezéssel, amely elküldi a törlési eseményeket. Útvonal létrehozásához kövesse az alábbi lépéseket:

1. Lépjen az *Útvonalak lapra,* és válassza a **Hozzáadás lehetőséget** egy útvonal hozzáadásához.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Képernyőkép a Visual Studio, amely hozzáad egy útvonalat az események küldése számára." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. A *megnyíló Útvonal hozzáadása* lapon válassza a következő értékeket:

   * **Név:** Válasszon egy nevet az útvonalnak. 
   * **Végpont:** Válassza ki a korábban létrehozott Event Hubs-végpontot a legördülő menüből.
   * **Adatforrás:** Válassza az *Eszközök életciklusával kapcsolatos események lehetőséget.*
   * **Útválasztási lekérdezés:** Írja be a következőt: `opType='deleteDeviceIdentity'` . Ez a lekérdezés úgy korlátozza az eszköz életciklus-eseményeit, hogy csak a törlési eseményeket küldjék el.

3. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Képernyőkép az Azure Portal az életciklus-eseményeket küldünk elküldő útvonal hozzáadásához." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Miután végiglépett a folyamaton, minden úgy van beállítva, hogy az eszközöket végpontok között kiveszi.

### <a name="validate"></a>Érvényesítés

A kieső folyamat aktiválásához manuálisan kell törölnie az eszközt a IoT Hub.

Ezt egy Azure [CLI-paranccsal](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) vagy a Azure Portal. Kövesse az alábbi lépéseket az eszköz törléséhez a Azure Portal:

1. Lépjen az IoT Hubra, és válassza az **IoT-eszközök** lehetőséget a bal oldali menüben. 
2. A cikk első felében kiválasztott eszközregisztrációs azonosítóval fog [látni egy eszközt.](#auto-provision-device-using-device-provisioning-service) Másik lehetőségként bármely másik eszközt is törölhet, ha az ikereszköz az Azure Digital Twins-ban található, így ellenőrizheti, hogy az ikereszközt a rendszer automatikusan törli-e az eszköz törlése után.
3. Jelölje ki az eszközt, és válassza a **Törlés lehetőséget.**

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Képernyőkép az ikereszköz Azure Portal ioT-eszközökről való törlésének képernyőképe." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

A módosítások néhány percen belül megjelennek a Azure Digital Twins.

Az alábbi [parancssori Azure Digital Twins parancssori](how-to-use-cli.md) felületi paranccsal ellenőrizheti, hogy az eszköz ikereszköze törölve lett-e Azure Digital Twins példányban.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Látnia kell, hogy az ikereszköz többé nem található a Azure Digital Twins példányban.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Képernyőkép az iker Parancsablak nem található ikerről." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a cikkben létrehozott erőforrásokra, az alábbi lépésekkel törölheti őket.

A Azure Cloud Shell vagy helyi Azure CLI használatával egy erőforráscsoport összes Azure-erőforrását törölheti [az az group delete paranccsal.](/cli/azure/group#az-group-delete) Ezzel eltávolítja az erőforráscsoportot; a Azure Digital Twins példány; az IoT Hub és a hub eszközregisztrációja; az Event Grid-témakör és a társított előfizetések; az Event Hubs-névtér és Azure Functions alkalmazásokat, beleértve a társított erőforrásokat, például a tárolást.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ezután törölje a helyi gépről letöltött projektmintamappát.

## <a name="next-steps"></a>Következő lépések

Az eszközökhöz létrehozott digitális ikereszközök egyszintű hierarchiában vannak tárolva az Azure Digital Twins-ban, de modellinformációk és a szervezet többszintű hierarchiája is bővítheti őket. Ha többet szeretne megtudni erről a fogalomról, olvassa el a következőt:

* [*Fogalmak: Digitális iker és az ikergráf*](concepts-twins-graph.md)

A HTTP-kérések Azure Functions-függvényekkel való használatával kapcsolatos további információkért lásd:

* [*Azure HTTP-kérés eseményindítója Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Írhat egyéni logikát, amely automatikusan biztosítja ezt az információt a modell és a gráfadatok használatával, amelyek már a Azure Digital Twins. Az ikergráf információinak kezelésével, frissítésével és lekért értékével kapcsolatos további információkért tekintse meg a következőket:

* [*How-to: Manage a digital twin (A digitális ikereszköz kezelése)*](how-to-manage-twin.md)
* [*Hogyan lehet lekérdezni az ikergráfot?*](how-to-query-graph.md)