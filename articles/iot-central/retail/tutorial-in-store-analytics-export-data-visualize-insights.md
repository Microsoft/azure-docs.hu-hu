---
title: Oktatóanyag – az adatexportálás és az elemzések megjelenítése az Azure-ban IoT Central
description: Ebből az oktatóanyagból megtudhatja, hogyan exportálhat adatok IoT Centralból, és hogyan jelenítheti meg az elemzéseket egy Power BI irányítópulton.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 2695b34745ff02d55f18cebbe87a468f807ca77a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831688"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Oktatóanyag: adatok exportálása az Azure IoT Central és az elemzések megjelenítése Power BI


A két korábbi oktatóanyagban létrehozta és testreszabta a IoT Central alkalmazást a **Store Analytics-Checkout** alkalmazás sablonjának használatával. Ebben az oktatóanyagban konfigurálja a IoT Central alkalmazást az eszközökről összegyűjtött telemetria exportálásához. Ezután a Power BI használatával létrehozhat egy egyéni irányítópultot az áruház kezelőjének, hogy megjelenítse a telemetria származtatott bepillantást.

Az oktatóanyag során a következőket fogja elsajátítani:
> [!div class="checklist"]
> * Konfiguráljon egy IoT Central alkalmazást a telemetria exportálásához egy Event hubhoz.
> * A Logic Apps használatával adatokat küldhet az Event hub-ból egy Power BI streaming-adatkészletbe.
> * Hozzon létre egy Power BI irányítópultot, amely megjeleníti az adatokat a folyamatos átviteli adatkészletben.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Az előző két oktatóanyag elvégzéséhez [hozzon létre egy áruházbeli elemzési alkalmazást az azure IoT Centralban](./tutorial-in-store-analytics-create-app.md) , és [szabja testre az operátor irányítópultját, és kezelje az eszközöket az Azure IoT Centralban](./tutorial-in-store-analytics-customize-dashboard.md).
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Power BI-fiók. Ha nincs Power BI fiókja, regisztráljon az [ingyenes Power bi Pro próbaverzióra](https://app.powerbi.com/signupredirect?pbi_source=web) , mielőtt elkezdené.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Event hub és a Logic app létrehozása előtt létre kell hoznia egy erőforráscsoportot a kezeléséhez. Az erőforráscsoport ugyanazon a helyen kell lennie, mint az **áruházbeli elemzési-pénztár** IoT Central alkalmazás. Erőforráscsoport létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs sávon válassza az **erőforráscsoportok** lehetőséget. Ezután válassza a **Hozzáadás** elemet.
1. Az **előfizetés** mezőben válassza ki a IoT Central alkalmazás létrehozásához használt Azure-előfizetés nevét.
1. Az **erőforráscsoport** neve mezőbe írja be a következőt: _Retail-Store-Analysis_*.
1. A **régió** esetében válassza ki ugyanazt a régiót, amelyet a IoT Central alkalmazáshoz választott.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

Most már rendelkezik egy **Retail-Store-Analysis** nevű erőforráscsoporthoz az előfizetésében.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ahhoz, hogy konfigurálni tudja a kiskereskedelmi figyelési alkalmazást a telemetria exportálásához, létre kell hoznia egy Event hub-t az exportált adatforgalom fogadásához. Az alábbi lépések bemutatják, hogyan hozhatja létre az Event hub-t:

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget a képernyő bal felső részén.
1. A **Keresés a piactéren** mezőbe írja be a _Event Hubs_ kifejezést, majd nyomja le az **ENTER** billentyűt.
1. A **Event Hubs** lapon válassza a **Létrehozás** lehetőséget.
1. A **névtér létrehozása** oldalon hajtsa végre a következő lépéseket:
    * Adja meg a névtér egyedi nevét, például: _sajátneve-Retail-Store-Analysis_. A rendszer ellenőrzi, hogy elérhető-e a név.
    * Válassza ki az **alapszintű** díjszabási szintet.
    * Válassza ki ugyanazt az **előfizetést** , amelyet a IoT Central alkalmazás létrehozásához használt.
    * Válassza ki a **kiskereskedelmi tároló-elemzési** erőforráscsoportot.
    * Válassza ki ugyanazt a helyet, amelyet a IoT Central alkalmazáshoz használt.
    * Válassza a **Létrehozás** lehetőséget. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiépíti az erőforrásokat.
1. A portálon navigáljon a **Retail-Store-Analysis** erőforráscsoport elemre. Várjon, amíg az üzembe helyezés befejeződik. Előfordulhat, hogy a **frissítés** gombra kell kattintania a telepítés állapotának frissítéséhez. Az Event hub-névtér létrehozásának állapotát az **értesítésekben** is megtekintheti.
1. A **Retail-Store-Analysis** Resource csoportban válassza ki a **Event Hubs névteret**. Ekkor megjelenik a **Event Hubs névtér** kezdőlapja a portálon.

Most már rendelkezik egy **Event Hubs névtérrel**, létrehozhat egy **Event hub** -t, amelyet a IoT Central alkalmazással használhat:

1. A portálon a **Event Hubs névtér** kezdőlapján válassza a **+ Event hub** elemet.
1. Az **Event hub létrehozása** lapon adja meg a _Store-telemetria_ nevet a név mezőben, majd válassza a **Létrehozás** lehetőséget.

Most már rendelkezik egy Event hub-val, amely a IoT Central alkalmazásból történő adatexportálás konfigurálására használható:

![Eseményközpont](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Az adatexportálás konfigurálása

Most már rendelkezik egy Event hub-vel, beállíthatja az **áruházbeli elemzési-pénztári** alkalmazást, hogy telemetria exportáljon a csatlakoztatott eszközökről. A következő lépések bemutatják, hogyan konfigurálhatja az exportálást:

1. Jelentkezzen be az **áruházbeli elemzés-** IoT Central alkalmazásba.
1. A bal oldali ablaktáblán válassza az **adatexportálás** lehetőséget.
1. Válassza az **új > Azure Event Hubs** lehetőséget.
1. A **megjelenítendő név** mezőben adja meg a _telemetria exportálását_ .
1. Válassza ki a **Event Hubs névteret**.
1. Válassza ki a **Store-telemetria** Event hubot.
1. Kapcsolja ki az **eszközöket** és az eszközök **sablonjait** az **exportálandó adatexportálás** szakaszban.
1. Kattintson a **Mentés** gombra.

Az adatexportálás eltarthat néhány percig, hogy el lehessen küldeni a telemetria az Event hub-ra. Az Exportálás állapota az **adatexportálás** oldalon látható:

![Folyamatos adatexportálási konfiguráció](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>A Power BI adatkészletek létrehozása

A Power BI irányítópulton megjelennek a kiskereskedelmi monitorozási alkalmazásból származó adatok. Ebben a megoldásban Power BI adatfolyam-adatkészleteket használja az Power BI irányítópult adatforrásaként. Ebben a szakaszban megadhatja a folyamatos átviteli adatkészletek sémáját, hogy a logikai alkalmazás továbbítsa az adatokat az Event hub-ból. A következő lépések bemutatják, hogyan hozhat létre két folyamatos átviteli adatkészletet a környezeti érzékelőkhöz és egy folyamatos átviteli adatkészlethez a foglaltság érzékelőhöz:

1. Jelentkezzen be a **Power BI**-fiókjába.
1. Válassza a **munkaterületek** lehetőséget, majd válassza **a munkaterület létrehozása** lehetőséget.
1. A **Munkaterület létrehozása** lapon a **munkaterület neve** mezőbe írja be _a következőt: Store Analytics-Checkout_ .
1. Görgessen az **üdvözli a helyszíni elemzés – pénztár munkaterület** oldal aljára, és válassza a **kihagyás** lehetőséget.
1. A munkaterület lapon válassza a **> folyamatos átviteli adatkészlet létrehozása** lehetőséget.
1. Az **új folyamatos átviteli adatkészlet** lapon válassza az **API** lehetőséget, majd kattintson a **tovább** gombra.
1. Adja meg _1. zóna érzékelőt_ az **adatkészlet neveként**.
1. Adja meg a következő táblázatban található **stream három értékét** :

    | Érték neve  | Érték típusa |
    | ----------- | ---------- |
    | Időbélyeg   | DateTime   |
    | Páratartalom    | Szám     |
    | Hőmérséklet | Szám     |

1. Állítsa át a **korábbi adatelemzést** a következőre:.
1. Válassza a **Létrehozás** , majd a **kész** lehetőséget.
1. Hozzon létre egy **2. zóna Sensor** nevű adatfolyam-adatkészletet ugyanazzal a sémával és beállításokkal, mint az **1. zóna Sensor** streaming adatkészlettel.

Most már két folyamatos adatkészletet tartalmaz. A logikai alkalmazás átirányítja a telemetria **a következő két** adatkészlethez kapcsolódó két környezeti érzékelőből:

![Zónák adatkészletei](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Ez a megoldás egy adatfolyam-adatkészletet használ az egyes érzékelőkhöz, mert nem lehet szűrőket alkalmazni Power BI adatfolyam-adatátvitelre.

Szüksége lesz egy folyamatos átviteli adatkészletre is a foglaltság telemetria:

1. A munkaterület lapon válassza a **> folyamatos átviteli adatkészlet létrehozása** lehetőséget.
1. Az **új folyamatos átviteli adatkészlet** lapon válassza az **API** lehetőséget, majd kattintson a **tovább** gombra.
1. Adja meg a _kihasználtsági érzékelőt_ az **adatkészlet neveként**.
1. Adja meg a **stream öt értékét** a következő táblázatban:

    | Érték neve     | Érték típusa |
    | -------------- | ---------- |
    | Időbélyeg      | DateTime   |
    | Várólista hossza 1 | Szám     |
    | Várólista hossza 2 | Szám     |
    | 1. tartózkodási idő   | Szám     |
    | 2. tartózkodási idő   | Szám     |

1. Állítsa át a **korábbi adatelemzést** a következőre:.
1. Válassza a **Létrehozás** , majd a **kész** lehetőséget.

Most már rendelkezik egy harmadik adatfolyam-adatkészlettel, amely az értékeket a szimulált Foglaltság érzékelőből tárolja. Ez az érzékelő jelentést készít a várólista hosszáról a tároló két pénztárában, valamint azt, hogy mennyi ideig várakozik az ügyfelek a következő várólistákban:

![Kihasználtsági adatkészlet](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a megoldásban a logikai alkalmazás beolvassa az telemetria az Event hub-ból, elemzi az adatokat, majd elküldi a létrehozott Power BI streaming adatkészleteknek.

A logikai alkalmazás létrehozása előtt szüksége lesz az IoT Central-alkalmazáshoz csatlakoztatott két RuuviTag-érzékelő eszköz-azonosítójának [használatára az Azure IoT Central](./tutorial-in-store-analytics-create-app.md) oktatóanyag:

1. Jelentkezzen be az **áruházbeli elemzés-** IoT Central alkalmazásba.
1. Válassza az **eszközök** lehetőséget a bal oldali ablaktáblán. Ezután válassza a **RuuviTag** lehetőséget.
1. Jegyezze fel az **eszköz azonosítóit**. A következő képernyőképen az azonosítók a **f5dcf4ac32e8** és a **e29ffc8d5326**:

    ![Eszközök azonosítói](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

A következő lépések bemutatják, hogyan hozhatja létre a logikai alkalmazást a Azure Portalban:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza az **erőforrás létrehozása** lehetőséget a képernyő bal felső részén.
1. A **Keresés a piactéren** mezőbe írja be a _Logic app_ kifejezést, majd nyomja le az **ENTER** billentyűt.
1. A **logikai alkalmazás** lapon válassza a **Létrehozás** lehetőséget.
1. A **logikai alkalmazás** létrehozása lapon:
    * Adjon egyedi nevet a logikai alkalmazásnak, például _sajátneve-Retail-Store-Analysis_.
    * Válassza ki ugyanazt az **előfizetést** , amelyet a IoT Central alkalmazás létrehozásához használt.
    * Válassza ki a **kiskereskedelmi tároló-elemzési** erőforráscsoportot.
    * Válassza ki ugyanazt a helyet, amelyet a IoT Central alkalmazáshoz használt.
    * Válassza a **Létrehozás** lehetőséget. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiépíti az erőforrásokat.
1. A Azure Portal navigáljon az új logikai alkalmazáshoz.
1. A **Logic apps Designer** lapon görgessen le, és válassza az **üres logikai alkalmazás** lehetőséget.
1. Az **Összekötők és eseményindítók keresése** területen adja meg a _Event Hubs_.
1. Az **Eseményindítók** területen válassza ki, hogy **mikor érhetők el események az Event hub-ban**.
1. Adja meg a _telemetria tárolási_ **nevét**, és válassza ki a **Event Hubs névteret**.
1. Válassza ki a **RootManageSharedAccess** szabályzatot, majd válassza a **Létrehozás** lehetőséget.
1. Az Event hub-műveletben **elérhető események** esetén:
    * Az **Event hub neveként** válassza a **Store-telemetria** elemet.
    * A **tartalom típusa** területen válassza az **alkalmazás/JSON** elemet.
    * Az **intervallumot** állítsa három értékre, a **gyakoriságot** pedig másodpercre.
1. Kattintson a **Mentés** gombra a logikai alkalmazás mentéséhez.

A Logic app-kialakításhoz való hozzáadáshoz válassza a **kód nézetet**:

1. Cserélje le `"actions": {},` a t a következő JSON-ra. Cserélje le a két helyőrzőt `[YOUR RUUVITAG DEVICE ID 1]` és `[YOUR RUUVITAG DEVICE ID 2]` a két RuuviTag-eszközön megjegyzett azonosítókat:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Válassza a **Mentés** lehetőséget, majd válassza a **tervező** lehetőséget a hozzáadott logika vizuális verziójának megtekintéséhez:

    ![Logic app-kialakítás](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. A művelet kibontásához válassza a **Váltás az DeviceID alapján** lehetőséget. Ezután válassza ki **1. zóna környezetet**, és válassza **a művelet hozzáadása** lehetőséget.
1. A **Keresés az összekötők és műveletek** területen írja be **Power bi**, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **sorok hozzáadása adatkészlethez (előzetes verzió)** műveletet.
1. Válassza a **Bejelentkezés** lehetőséget, és az utasításokat követve jelentkezzen be Power bi-fiókjába.
1. A bejelentkezési folyamat befejezése után a **sorok hozzáadása egy adatkészlethez** művelet:
    * Válassza a **tárolt elemzés – pénztár** lehetőséget a munkaterület területen.
    * Válassza ki **1. zóna érzékelőt** adatkészletként.
    * Válassza a **RealTimeData** táblát.
    * Válassza az **új paraméter hozzáadása** lehetőséget, majd válassza ki az **időbélyeg**, a **páratartalom** és a **hőmérséklet** mezőket.
    * Válassza ki az **időbélyeg** mezőt, majd válassza az **x-opt-Enqueuedtime** lehetőséget a **dinamikus tartalmak** listájából.
    * Válassza ki a **páratartalom** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **páratartalom** lehetőséget.
    * Válassza ki a **hőmérséklet** mezőt, majd válassza a **továbbiak** lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **hőmérséklet** elemet.
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **1. zóna környezeti** művelet a következő képernyőképre hasonlít: ![ 1. zóna-környezet](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Válassza ki a **2. zóna környezeti** műveletet, és válassza a **művelet hozzáadása** lehetőséget.
1. A **Keresés az összekötők és műveletek** területen írja be **Power bi**, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **sorok hozzáadása adatkészlethez (előzetes verzió)** műveletet.
1. A **sorok hozzáadása egy adatkészlethez 2** művelet:
    * Válassza a **tárolt elemzés – pénztár** lehetőséget a munkaterület területen.
    * Válassza ki **2. zóna érzékelőt** adatkészletként.
    * Válassza a **RealTimeData** táblát.
    * Válassza az **új paraméter hozzáadása** lehetőséget, majd válassza ki az **időbélyeg**, a **páratartalom** és a **hőmérséklet** mezőket.
    * Válassza ki az **időbélyeg** mezőt, majd válassza az **x-opt-Enqueuedtime** lehetőséget a **dinamikus tartalmak** listájából.
    * Válassza ki a **páratartalom** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **páratartalom** lehetőséget.
    * Válassza ki a **hőmérséklet** mezőt, majd válassza a **továbbiak** lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **hőmérséklet** elemet.
    A módosítások mentéséhez válassza a **Mentés** gombot.  A **2. zóna környezeti** művelet a következő képernyőképre hasonlít: ![ 2. zóna-környezet](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Válassza ki a **kihasználtsági** műveletet, majd válassza ki a **kapcsolót illesztőfelület-azonosító** művelettel.
1. Válassza ki a **tartózkodási idő illesztőfelülete** műveletet, és válassza a **művelet hozzáadása** lehetőséget.
1. A **Keresés az összekötők és műveletek** területen írja be **Power bi**, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **sorok hozzáadása adatkészlethez (előzetes verzió)** műveletet.
1. A **sorok hozzáadása adatkészlet** -művelethez:
    * Válassza a **tárolt elemzés – pénztár** lehetőséget a munkaterület területen.
    * Válassza ki a **kihasználtsági érzékelőt** adatkészletként.
    * Válassza a **RealTimeData** táblát.
    * Válassza az **új paraméter hozzáadása** lehetőséget, majd válassza ki az **időbélyeget**, az **1. tartózkodási időt** és a **tartózkodási idő 2** mezőt.
    * Válassza ki az **időbélyeg** mezőt, majd válassza az **x-opt-Enqueuedtime** lehetőséget a **dinamikus tartalmak** listájából.
    * Válassza ki a **tartózkodási idő 1** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **DwellTime1** lehetőséget.
    * Válassza ki a **tartózkodási idő 2** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **DwellTime2** lehetőséget.
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **tartózkodási idő illesztőfelületének** művelete a következő képernyőképhez hasonlóan néz ki: ![ képernyőkép, amely a "tartózkodási idő illesztőfelülete" műveletet mutatja.](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Válassza ki a **személyek száma illesztőfelület** műveletet, és válassza a **művelet hozzáadása** lehetőséget.
1. A **Keresés az összekötők és műveletek** területen írja be **Power bi**, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **sorok hozzáadása adatkészlethez (előzetes verzió)** műveletet.
1. A **sorok hozzáadása adatkészlet** -művelethez:
    * Válassza a **tárolt elemzés – pénztár** lehetőséget a munkaterület területen.
    * Válassza ki a **kihasználtsági érzékelőt** adatkészletként.
    * Válassza a **RealTimeData** táblát.
    * Válassza az **új paraméter hozzáadása** lehetőséget, majd válassza ki az **időbélyeg**, a **várólista hossza 1** és a **várólista hossza 2** mezőt.
    * Válassza ki az **időbélyeg** mezőt, majd válassza az **x-opt-Enqueuedtime** lehetőséget a **dinamikus tartalmak** listájából.
    * Válassza ki a **várólista hossza 1** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **count1** lehetőséget.
    * Válassza ki a **várólista hossza 2** mezőt, majd válassza a **továbbiak** tovább lehetőséget a **telemetria elemzéséhez**. Ezután válassza a **count2** lehetőséget.
    * A módosítások mentéséhez válassza a **Mentés** gombot. A **személyek száma Interface** művelet a következő képernyőképhez hasonlóan néz ki: ![ Foglaltság művelet](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

A logikai alkalmazás automatikusan fut. Az egyes futtatások állapotának megtekintéséhez navigáljon a logikai alkalmazás **Áttekintés** lapjára a Azure Portalban:

## <a name="create-a-power-bi-dashboard"></a>Power BI-irányítópult létrehozása

Most már telemetria a IoT Central alkalmazásból az Event hub-on keresztül. Ezután a logikai alkalmazás elemzi az Event hub-üzeneteket, és hozzáadja őket egy Power BI streaming-adatkészlethez. Most létrehozhat egy Power BI irányítópultot a telemetria megjelenítéséhez:

1. Jelentkezzen be a **Power BI**-fiókjába.
1. Válassza ki **a Munkaterületek > áruházbeli elemzés – pénztár** lehetőséget.
1. Válassza a **létrehozás > irányítópultot**.
1. Adja meg a **Store Analytics** nevet az irányítópult neveként, majd válassza a **Létrehozás** lehetőséget.

### <a name="add-line-charts"></a>Soros diagramok hozzáadása

Adja hozzá a négy vonalas diagramot a hőmérséklet és a páratartalom megjelenítéséhez a két környezeti érzékelőből. A csempék létrehozásához használja a következő táblázatban található információkat. Az egyes csempék hozzáadásához először válassza a **... lehetőséget. (További beállítások) > csempe hozzáadása**. Válassza az **Egyéni adatfolyam-továbbítás** lehetőséget, majd válassza a **Next (tovább**) gombot:

| Beállítás | Diagram #1 | Diagram #2 | Diagram #3 | Diagram #4 |
| ------- | -------- | -------- | -------- | -------- |
| Adathalmaz | 1. zóna érzékelő | 1. zóna érzékelő | 2. zóna érzékelő | 2. zóna érzékelő |
| Vizualizáció típusa | Vonaldiagram | Vonaldiagram | Vonaldiagram | Vonaldiagram |
| Tengely | Időbélyeg | Időbélyeg | Időbélyeg | Időbélyeg |
| Értékek | Hőmérséklet | Páratartalom | Hőmérséklet | Páratartalom |
| Időablak | 60 perc | 60 perc | 60 perc | 60 perc |
| Cím | Hőmérséklet (1 óra) | Páratartalom (1 óra) | Hőmérséklet (1 óra) | Páratartalom (1 óra) |
| Alcím | 1\. zóna | 1\. zóna | 2\. zóna | 2\. zóna |

Az alábbi képernyőképen az első diagram beállításai láthatók:

![Vonaldiagram beállításai](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Kártyák hozzáadása a környezeti adatai megjelenítéséhez

A két környezeti érzékelő közül a legutóbbi hőmérséklet és a páratartalom értékének megjelenítéséhez vegyen fel négy kártyát a csempék közül. A csempék létrehozásához használja a következő táblázatban található információkat. Az egyes csempék hozzáadásához először válassza a **... lehetőséget. (További beállítások) > csempe hozzáadása**. Válassza az **Egyéni adatfolyam-továbbítás** lehetőséget, majd válassza a **Next (tovább**) gombot:

| Beállítás | Kártya #1 | Kártya #2 | Kártya #3 | Kártya #4 |
| ------- | ------- | ------- | ------- | ------- |
| Adathalmaz | 1. zóna érzékelő | 1. zóna érzékelő | 2. zóna érzékelő | 2. zóna érzékelő |
| Vizualizáció típusa | Kártya | Kártya | Kártya | Kártya |
| Mezők | Hőmérséklet | Páratartalom | Hőmérséklet | Páratartalom |
| Cím | Hőmérséklet (F) | Páratartalom (%) | Hőmérséklet (F) | Páratartalom (%) |
| Alcím | 1\. zóna | 1\. zóna | 2\. zóna | 2\. zóna |

Az alábbi képernyőképen az első kártya beállításai láthatók:

![Kártya beállításai](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Csempék hozzáadása a pénztári kihasználtsági adatai megjelenítéséhez

Vegyen fel négy kártya csempét, hogy megjelenjen a várólista hossza és a tartózkodási idő a két pénztár számára a tárolóban. A csempék létrehozásához használja a következő táblázatban található információkat. Az egyes csempék hozzáadásához először válassza a **... lehetőséget. (További beállítások) > csempe hozzáadása**. Válassza az **Egyéni adatfolyam-továbbítás** lehetőséget, majd válassza a **Next (tovább**) gombot:

| Beállítás | Kártya #1 | Kártya #2 | Kártya #3 | Kártya #4 |
| ------- | ------- | ------- | ------- | ------- |
| Adathalmaz | Foglaltság érzékelője | Foglaltság érzékelője | Foglaltság érzékelője | Foglaltság érzékelője |
| Vizualizáció típusa | Fürtözött oszlopdiagram | Fürtözött oszlopdiagram | Kijelző | Kijelző |
| Tengely    | Időbélyeg | Időbélyeg | N.A. | N.A. |
| Érték | 1. tartózkodási idő | 2. tartózkodási idő | Várólista hossza 1 | Várólista hossza 2 |
| Időablak | 60 perc | 60 perc |  N.A. | N.A. |
| Cím | Tartózkodási idő | Tartózkodási idő | Processzor-várólista hossza | Processzor-várólista hossza |
| Alcím | 1. pénztár | 2. pénztár | 1. pénztár | 2. pénztár |

Méretezze át és rendezze át a csempéket az irányítópulton úgy, hogy az a következő képernyőképhez hasonlítson:

![Képernyőfelvétel: a Power B I irányítópult átméretezett és átrendezett csempéket jelenít meg.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Hozzáadhat további grafikus erőforrásokat is az irányítópult további testreszabásához:

![Power BI-irányítópult](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a IoT Central alkalmazással, törölheti azt az alkalmazásba való bejelentkezéssel, és megtekintheti a **felügyelet** szakasz **Alkalmazásbeállítások** lapját.

Ha szeretné megtartani az alkalmazást, de csökkenti az ahhoz kapcsolódó költségeket, tiltsa le a telemetria küldő adatexportálást az Event hub-ra.

Az Event hub és a Logic alkalmazást törölheti a Azure Portal a **Retail-Store-Analysis** nevű erőforráscsoport törlésével.

Power BI adatkészleteket és irányítópultokat a munkaterület Power BI beállítások lapján lévő munkaterület törlésével törölheti.

## <a name="next-steps"></a>Következő lépések

Ez a három oktatóanyag olyan teljes körű megoldást mutat be, amely a **Store Analytics-checkout** IoT Central alkalmazás sablonját használja. Csatlakoztatta az eszközöket az alkalmazáshoz, a IoT Central használta az eszközök figyelésére, és Power BI, hogy irányítópultot hozzon létre az eszköz telemetria való betekintés céljából. A következő lépés egy másik IoT Central alkalmazás-sablon egyikének megismerése:

> [!div class="nextstepaction"]
> [Energiaipari megoldások létrehozása az IoT Centrallal](../energy/overview-iot-central-energy.md)
