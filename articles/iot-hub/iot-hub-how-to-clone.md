---
title: Azure IoT hub klónozása
description: Azure IoT hub klónozása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 7f5553cc51927d878487b0875e72873451a3de3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059581"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Azure IoT hub klónozása egy másik régióba

Ez a cikk a IoT Hub klónozásának módjait mutatja be, és a Kezdés előtt válaszol a szükséges kérdésekre. Többek között az IoT hub klónozására van szükség:
 
* Áthelyezi a vállalatot az egyik régióból a másikba, például Európából Észak-Amerika (vagy fordítva), és azt szeretné, hogy az erőforrások és az adatok földrajzilag közel legyenek az új helyhez, ezért át kell helyeznie a hubot.

* Egy központot állít be fejlesztési és éles környezetben.

* A több-hub magas rendelkezésre állásának egyéni megvalósítását szeretné elvégezni. További információkért tekintse meg a [IoT hub magas rendelkezésre állású és vész-helyreállítási szakaszát](iot-hub-ha-dr.md#achieve-cross-region-ha).

* Meg szeretné emelni a hubhoz konfigurált [partíciók](iot-hub-scaling.md#partitions) számát. Ez a hub első létrehozásakor van beállítva, és nem módosítható. A cikkben található információk segítségével klónozott a hubot, és a klón létrehozásakor növelheti a partíciók számát.

A hub klónozásához rendszergazdai hozzáféréssel rendelkező előfizetésre van szükség az eredeti hubhoz. Az új hubot egy új erőforráscsoport és régióba helyezheti, ugyanabban az előfizetésben, mint az eredeti hubot, vagy akár egy új előfizetésben is. Csak nem használhatja ugyanazt a nevet, mert a hub nevének globálisan egyedinek kell lennie.

> [!NOTE]
> Jelenleg nincs lehetőség az IoT hub automatikus klónozására. Ez elsősorban egy manuális folyamat, ezért meglehetősen hibákra hajlamos. A hub klónozásának összetettsége közvetlenül a központ összetettségével arányos. Például az IoT hub és az üzenet-útválasztás nélküli klónozás meglehetősen egyszerű. Ha csak egy összetettséggel adja hozzá az üzenet-útválasztást, akkor a hub klónozása legalább egy nagyságrenddel bonyolultabb lesz. Ha az útválasztási végpontokhoz használt erőforrásokat is át szeretné helyezni, akkor a magniture még összetettebb. 

## <a name="things-to-consider"></a>Megfontolandó dolgok

Az IoT hub klónozása előtt több dolgot is figyelembe kell venni.

* Győződjön meg arról, hogy az eredeti helyen elérhető összes funkció az új helyen is elérhető. Egyes szolgáltatások előzetes verzióban érhetők el, és nem minden szolgáltatás érhető el mindenhol.

* Ne távolítsa el az eredeti erőforrásokat a klónozott verzió létrehozása és ellenőrzése előtt. Ha eltávolít egy központot, az örökre megszűnik, és nem lehet helyreállítani a beállításokat vagy adatokat annak ellenőrzéséhez, hogy a központ megfelelően van-e replikálva.

* Számos erőforráshoz globálisan egyedi nevek szükségesek, ezért különböző neveket kell használnia a klónozott verziókhoz. Más nevet is kell használnia ahhoz az erőforrás-csoporthoz, amelyhez a klónozott hub tartozik. 

* Az eredeti IoT hub-adatközpont nem települ át. Ide tartozik a telemetria üzenetek, a felhőből az eszközre irányuló (C2D) parancsok, valamint a feladattal kapcsolatos információk, például az ütemtervek és a előzmények. A metrikák és a naplózási eredmények szintén nem települnek át. 

* Az Azure Storage-ba továbbított adatok vagy üzenetek esetében az eredeti Storage-fiókban hagyhatja el az adatok átvitelét, átviheti azokat az új régióban lévő új Storage-fiókba, vagy meghagyhatja a régi adatok helyét, és új Storage-fiókot hozhat létre az új adatokhoz. További információ a blob Storage-beli adatok áthelyezéséről: Ismerkedés [a AzCopy](../storage/common/storage-use-azcopy-v10.md).

* Event Hubs és Service Bus témakörök és várólisták esetében nem lehet áttelepíteni az adatátvitelt. Ez az adott időponthoz tartozó, az üzenetek feldolgozása után nem tárolt adategység.

* Az áttelepítéshez ütemeznie kell az állásidőt. Az eszközök az új hubhoz való klónozása időt vesz igénybe. Ha az importálási/exportálási módszert használja, a teljesítményteszt tesztelése azt mutatta, hogy az 500 000-es eszközök áthelyezését és négy órát vesz igénybe egy millió eszköz áthelyezéséhez. 

* Az eszközöket az új hubhoz másolhatja az eszközök leállítása vagy módosítása nélkül. 

    * Ha az eszközök eredetileg a DPS használatával lettek kiépítve, akkor az újbóli kiépítés után az egyes eszközökön tárolt kapcsolatok adatai frissülnek. 
    
    * Ellenkező esetben az importálási/exportálási módszert kell használnia az eszközök áthelyezéséhez, majd az eszközöket úgy kell módosítani, hogy az új hubot használják. Beállíthatja például, hogy az eszköz a IoT Hub állomásnév felhasználására a kívánt Twin-tulajdonságok közül. Az eszköz elvégzi a IoT Hub az állomásnév megkeresését, az eszköz leválasztását a régi központból, és újra csatlakoztatja az újat.
    
* Frissítenie kell a használt tanúsítványokat, hogy azok az új erőforrásokkal is használhatók legyenek. Azt is megteheti, hogy valahol egy DNS-táblában definiálta a hubot – ezt a DNS-információt frissítenie kell.

## <a name="methodology"></a>Módszertan

Ez az általános módszer, amelyet az IoT hub egyik régióból a másikba való áthelyezéséhez ajánlunk. Az üzenet-útválasztás esetében ez azt feltételezi, hogy az erőforrások nem kerülnek át az új régióba. További információ: az üzenet- [Útválasztás szakasza](#how-to-handle-message-routing).

   1. Exportálja a hubot és a hozzá tartozó beállításokat egy Resource Manager-sablonba. 
   
   1. Végezze el a szükséges módosításokat a sablonon, például frissítse a név összes előfordulását és a klónozott központ helyét. Az üzenet-útválasztási végpontokhoz használt sablonban lévő összes erőforrás esetében frissítse az adott erőforrás sablonjának kulcsát.
   
   1. Importálja a sablont egy új erőforráscsoporthoz az új helyen. Ez létrehozza a klónt.

   1. Hibakeresés igény szerint. 
   
   1. Adja hozzá a sablonba nem exportált bármit. 
   
       A fogyasztói csoportok például nem exportálhatók a sablonba. Manuálisan kell hozzáadnia a fogyasztói csoportokat a sablonhoz, vagy a [Azure Portalt](https://portal.azure.com) a hub létrehozása után kell használnia. Egy felhasználói csoport egy sablonhoz való hozzáadására példa van egy [Azure Resource Manager sablon használata IoT hub üzenet-útválasztás konfigurálásához](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Másolja az eszközöket az eredeti hubhoz a klónba. Ez az IoT hub-ban [regisztrált eszközök kezelése](#managing-the-devices-registered-to-the-iot-hub)című szakaszban található.

## <a name="how-to-handle-message-routing"></a>Az üzenetek útválasztásának kezelése

Ha a hub [Egyéni útválasztást](iot-hub-devguide-messages-read-custom.md)használ, a hub sablonjának exportálása magában foglalja az útválasztási konfigurációt, de maguk nem tartalmazzák az erőforrásokat. Ki kell választania, hogy áthelyezi-e az útválasztási erőforrásokat az új helyre, vagy hagyja őket a helyükre, és továbbra is használja őket. 

Tegyük fel például, hogy az USA nyugati régiójában van egy olyan központ, amely az üzeneteket egy Storage-fiókba (az USA nyugati régiójában is) irányítja, és a hubot át szeretné helyezni az USA keleti régiójába. Áthelyezheti a hubot, és továbbra is továbbíthatja az üzeneteket a Storage-fiókba az USA nyugati régiójában, vagy áthelyezheti a hubot, és áthelyezheti a Storage-fiókot is. Előfordulhat, hogy egy kisebb teljesítmény éri el az útválasztási üzeneteket egy másik régióban lévő végponti erőforrásoknak.

Ha nem helyezi át az útválasztási végpontokhoz használt erőforrásokat, áthelyezheti az üzenetsor-útválasztást használó központot is. 

Ha a hub üzenet-útválasztást használ, két lehetőség közül választhat. 

1. Helyezze át az útválasztási végpontokhoz használt erőforrásokat az új helyre.

    * Az új erőforrásokat manuálisan kell létrehoznia a [Azure Portal](https://portal.azure.com) vagy Resource Manager-sablonok használatával. 

    * Az összes erőforrást az új helyen való létrehozásakor kell átnevezni, mivel azok globálisan egyedi névvel rendelkeznek. 
     
    * Az új központ létrehozása előtt frissítenie kell az új hub sablonjának erőforrás-nevét és az erőforrás kulcsait. Az erőforrásoknak jelen kell lennie az új központ létrehozásakor.

1. Ne helyezze át az útválasztási végpontokhoz használt erőforrásokat. Használja őket "helyben".

   * A sablon szerkesztésének lépésében le kell kérnie az egyes útválasztási erőforrások kulcsait, és az új központ létrehozása előtt el kell helyeznie azokat a sablonban. 

   * A hub továbbra is az eredeti útválasztási erőforrásokra hivatkozik, és az üzeneteket konfiguráltként irányítja.

   * Egy kis teljesítményű találat lesz, mivel a hub és az útválasztási végpont erőforrásai nem ugyanazon a helyen találhatók.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>Felkészülés a központ áttelepíteni egy másik régióba

Ez a szakasz részletes útmutatást nyújt a központ áttelepítéséhez.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Keresse meg az eredeti hubot, és exportálja egy erőforrás-sablonba.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Lépjen az **erőforráscsoportok** elemre, és válassza ki azt az erőforráscsoportot, amely az áthelyezni kívánt hubot tartalmazza. Az **erőforrásokra** is rákereshet, és megkeresheti a hubot. Válassza ki a hubot.

1. Válassza a **sablon exportálása** lehetőséget a központ tulajdonságainak és beállításainak listájából. 

   ![Képernyőfelvétel: a IoT Hub sablonjának exportálására szolgáló parancs.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. A sablon letöltéséhez kattintson a **Letöltés** gombra. Mentse újra a fájlt, és keresse meg újra. 

   ![Képernyőfelvétel: a IoT Hub sablonjának letöltésére szolgáló parancs.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>A sablon megtekintése 

1. Nyissa meg a letöltések mappát (vagy a sablon exportálásakor használt mappát), és keresse meg a zip-fájlt. Nyissa meg a zip-fájlt, és keresse meg a nevű fájlt `template.json` . Jelölje ki, majd válassza a CTRL + C billentyűkombinációt a sablon másolásához. Lépjen egy másik mappába, amely nem szerepel a zip-fájlban, és illessze be a fájlt (CTRL + V). Most már szerkesztheti is.
 
    Az alábbi példa egy általános, útválasztási konfiguráció nélküli hubhoz mutat. Ez egy, a **ContosoTestHub29358** nevű S1 szintű hub (1 egység) a **westus** régióban. Itt látható az exportált sablon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>A sablon szerkesztése 

Néhány módosítást el kell végeznie ahhoz, hogy a sablon használatával létrehozza az új hubot az új régióban. A [vs Code](https://code.visualstudio.com) vagy egy szövegszerkesztő használatával szerkessze a sablont.

#### <a name="edit-the-hub-name-and-location"></a>A hub nevének és helyének szerkesztése

1. Távolítsa el a paraméterek szakaszt felül – sokkal egyszerűbb, hogy csak a hub nevét használja, mert nem fogunk több paraméterrel rendelkezni. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Módosítsa úgy a nevet, hogy a tényleges (új) nevet használja ahelyett, hogy egy paraméterből kellene beolvasnia (amelyet az előző lépésben eltávolított). 

    Az új hub esetében az új név létrehozásához használja az eredeti hub nevét és a karakterlánc- *klónozást* . Először a hub nevét és helyét kell megtisztítani.
    
    Régi verzió:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Új verzió: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Ezután megtudhatja, hogy az **elérési út** értékei tartalmazzák a régi hub nevét. Módosítsa őket úgy, hogy az újat használják. Ezek a **eventHubEndpoints** alatti elérésiút-értékek az **Events** és a **OperationsMonitoringEvents**.

    Ha elkészült, az Event hub-végpontok szakasznak így kell kinéznie:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>A nem áthelyezett útválasztási erőforrások kulcsainak frissítése

Ha olyan hubhoz exportálja a Resource Manager-sablont, amely rendelkezik konfigurált útválasztással, látni fogja, hogy az erőforrásokhoz tartozó kulcsok nincsenek megadva az exportált sablonban – az elhelyezést csillagokkal jelöli a rendszer. Az új központ sablonjának importálása és a hub létrehozása **előtt** ki kell töltenie őket a portálon, majd be kell olvasnia a kulcsokat. 

1. Kérje le az útválasztási erőforrásokhoz szükséges kulcsokat, és helyezze azokat a sablonba. A kulcs (oka) t a [Azure Portal](https://portal.azure.com)lévő erőforrásból kérheti le. 

   Ha például egy Storage-tárolóba irányítja az üzeneteket, keresse meg a Storage-fiókot a portálon. A beállítások szakaszban válassza a **hozzáférési kulcsok** elemet, majd másolja ki az egyik kulcsot. A kulcs a sablon első exportálásakor a következőhöz hasonlít:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Miután beolvasta a Storage-fiókhoz tartozó fiók kulcsát, a csillagokban lévő záradékban helyezze el azt a sablonban `AccountKey=****` . 

1. A Service Bus-várólisták esetében szerezze be a SharedAccessKeyName megfelelő megosztott elérési kulcsot. Itt látható a kulcs és a a `SharedAccessKeyName` JSON-ban:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Ugyanez vonatkozik a Service Bus témakörökre és az Event hub-kapcsolatokra is.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Új útválasztási erőforrások létrehozása az új helyen

Ez a szakasz csak akkor érvényes, ha a hub által az útválasztási végpontok számára használt erőforrásokat helyezi át.

Ha át szeretné helyezni az útválasztási erőforrásokat, manuálisan kell beállítania az erőforrásokat az új helyen. Az útválasztási erőforrásokat a [Azure Portal](https://portal.azure.com)használatával, vagy a Resource Manager-sablon exportálásával is létrehozhatja az üzenet-útválasztás által használt összes erőforráshoz, és szerkesztheti őket, és importálhatja őket. Az erőforrások beállítása után importálhatja a hub sablonját (amely magában foglalja az útválasztási konfigurációt is).

1. Hozza létre az Útválasztás által használt összes erőforrást. Ezt manuálisan is megteheti a [Azure Portal](https://portal.azure.com)használatával, vagy a Resource Manager-sablonok segítségével hozhatja létre az erőforrásokat. Ha sablonokat szeretne használni, kövesse az alábbi lépéseket:

    1. Az Útválasztás által használt minden erőforráshoz exportálja azt egy Resource Manager-sablonba.
    
    1. Frissítse az erőforrás nevét és helyét. 

    1. Frissítse az erőforrások közötti kereszthivatkozásokat. Ha például létrehoz egy sablont egy új Storage-fiókhoz, akkor frissítenie kell a Storage-fiók nevét a sablonban, és minden más sablont, amely hivatkozik rá. A legtöbb esetben a hub sablonjának útválasztási szakasza az egyetlen másik sablon, amely az erőforrásra hivatkozik. 

    1. Importálja az egyes erőforrásokat telepítő sablonokat.

    Miután beállította és futtatta az Útválasztás által használt erőforrásokat, folytathatja a szolgáltatást.

1. Az IoT hub sablonjában módosítsa az egyes útválasztási erőforrások nevét az új névre, és szükség esetén frissítse a helyet. 

Most már rendelkezik egy olyan sablonnal, amely egy olyan új hubot hoz létre, amely majdnem pontosan a régi hubhoz hasonlít, attól függően, hogy hogyan kezeli az útválasztást.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Áthelyezés – a sablon betöltésével hozza létre az új hubot az új régióban.

Hozza létre az új hubot az új helyen a sablon használatával. Ha útválasztási erőforrásokkal rendelkezik, amelyeket át szeretne helyezni, az erőforrásokat az új helyen kell beállítani, és a sablonban szereplő hivatkozások a megfelelő értékre frissülnek. Ha nem helyezi át az útválasztási erőforrásokat, a frissített kulcsokkal rendelkező sablonban kell lenniük.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** lehetőséget. 

1. A keresőmezőbe írja be a "sablon központi telepítése" kifejezést, és válassza az ENTER billentyűt.

1. Válassza **a sablon központi telepítése (üzembe helyezés egyéni sablonok használatával)** lehetőséget. Ekkor megjelenik a Template deployment képernyője. Válassza a **Létrehozás** lehetőséget. Ekkor az alábbi képernyő jelenik meg:

   ![A saját sablon létrehozásához szükséges parancsot ábrázoló képernyőkép](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben**, amely lehetővé teszi a sablon feltöltését egy fájlból. 

1. Válassza a **fájl betöltése** lehetőséget. 

   ![A sablonfájl feltöltésére szolgáló parancsot megjelenítő képernyőkép](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Keresse meg a szerkesztett új sablont, és válassza ki, majd kattintson a **Megnyitás** gombra. A sablon betöltődik a szerkesztési ablakba. Kattintson a **Mentés** gombra. 

   ![A sablon betöltését bemutató képernyőkép](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Töltse ki az alábbi mezőket.

   **Előfizetés**: válassza ki a használni kívánt előfizetést.

   **Erőforráscsoport**: hozzon létre egy új erőforráscsoportot egy új helyen. Ha már rendelkezik egy új beállítással, kiválaszthatja egy új létrehozása helyett.

   **Hely**: Ha kiválasztott egy meglévő erőforráscsoportot, a rendszer kitölti azt, hogy egyezzen az erőforráscsoport helyével. Ha létrehozott egy új erőforráscsoportot, ez lesz a helye.

   **Elfogadom ezt a jelölőnégyzetet**: Ez lényegében azt mondja, hogy Ön vállalja, hogy a létrehozott erőforrás (oka) t kell fizetnie.

1. Kattintson a **vásárlás** gombra.

A portál most ellenőrzi a sablont, és üzembe helyezi a klónozott hubot. Ha útválasztási konfigurációs adattal rendelkezik, azt a rendszer az új központban fogja tartalmazni, de az előző helyen található erőforrásokra mutat.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>Az IoT hub-ban regisztrált eszközök kezelése

Most, hogy már telepítette a klónozást, az összes eszközt át kell másolnia az eredeti hubhoz a klónba. 

Ezt többféle módon is elvégezheti. Eredetileg a [Device kiépítési szolgáltatás (DPS)](../iot-dps/about-iot-dps.md)használatával helyezi üzembe az eszközöket, vagy nem. Ha igen, ez nem nehéz. Ha nem, akkor ez nagyon bonyolult lehet. 

Ha nem használta a DPS-t az eszközök kiépítéséhez, ugorja át a következő szakaszt, és kezdje az [Importálás/exportálás használatával az eszközök az új hubhoz való áthelyezéséhez](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>Az új központban lévő eszközök újbóli kiépítése a DPS használatával

Ha a DPS használatával szeretné áthelyezni az eszközöket az új helyre, olvassa el az [eszközök újbóli kiépítése](../iot-dps/how-to-reprovision.md)című témakört. Ha elkészült, megtekintheti az eszközöket a [Azure Portalban](https://portal.azure.com) , és ellenőrizheti, hogy azok az új helyen találhatók-e.

Nyissa meg az új hubot a [Azure Portal](https://portal.azure.com)használatával. Válassza ki a hubot, majd válassza az **IoT-eszközök** lehetőséget. Megtekintheti a klónozott hubhoz újra kiépített eszközöket. Megtekintheti a klónozott központ tulajdonságait is. 

Ha implementálta az útválasztást, ellenőrizze, hogy az üzenetek megfelelően vannak-e irányítva az erőforrásokhoz.

### <a name="committing-the-changes-after-using-dps"></a>A módosítások véglegesítése a DPS használata után

Ezt a változást a DPS szolgáltatás véglegesítette.

### <a name="rolling-back-the-changes-after-using-dps"></a>A változtatások visszaállítása a DPS használata után. 

Ha vissza szeretné állítani a módosításokat, az eszközöket az új központból a régire kell állítania.

Ezzel befejezte a központ és az eszközei áttelepítését. A [tisztítás](#clean-up)lehetőségre ugorhat.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Az eszközök átmozgatása az új hubhoz a Import-Export használatával

Az alkalmazás a .NET Core-t célozza meg, így azt Windows vagy Linux rendszeren is futtathatja. Letöltheti a mintát, lekérheti a kapcsolódási karakterláncokat, beállíthatja a futtatni kívánt BITS-jelzőket, és futtathatja azt. Ezt a kód megnyitása nélkül is megteheti.

### <a name="downloading-the-sample"></a>A minta letöltése

1. Használja a IoT C#-mintákat a következő lapról: [Azure IoT-minták c#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)-hoz. Töltse le a zip-fájlt, és csomagolja ki a számítógépére. 

1. A kapcsolódó kód a./iot-hub/Samples/service/ImportExportDevicesSample. Az alkalmazás futtatásához nem kell megtekintenie vagy szerkesztenie a kódot.

1. Az alkalmazás futtatásához három kapcsolódási karakterláncot és öt beállítást kell megadni. Ezeket az adatsorokat parancssori argumentumként adja át, vagy használja a környezeti változókat, vagy használja a kettő kombinációját. A paramétereket parancssori argumentumként adjuk át, a kapcsolódási karakterláncokat pedig környezeti változókként. 

   Ennek oka az, hogy a kapcsolódási karakterláncok hosszúak és nem túl nagyok, és nem valószínű, hogy módosítani szeretnék, de előfordulhat, hogy módosítania kell a beállításokat, és többször is futtatnia kell az alkalmazást. Egy környezeti változó értékének módosításához be kell állítani a parancssorablakot és a Visual studiót vagy a VS Code-ot, attól függően, hogy melyik van használatban. 

### <a name="options"></a>Beállítások

Az alábbi öt lehetőség az alkalmazás futtatásakor adható meg. Ezeket a parancssorba helyezi egy percen belül.

*   **addDevices** (1. argumentum) – ezt állítsa igaz értékre, ha az Ön számára létrehozott virtuális eszközöket szeretne hozzáadni. Ezek hozzáadódnak a forrás hubhoz. A **numToAdd** (2. argumentum) beállítással megadhatja, hogy hány eszközt szeretne hozzáadni. A központba regisztrálni kívánt eszközök maximális száma 1 000 000. Ennek a lehetőségnek a célja a tesztelés: bizonyos számú eszközt létrehozhat, majd átmásolhatja őket egy másik hubhoz.

*   **copyDevices** (3. érv) – ezt állítsa igaz értékre az eszközök egyik központból a másikba való másolásához. 

*   **deleteSourceDevices** (4. argumentum) – ezt állítsa igaz értékre a forrásoldali hubhoz regisztrált összes eszköz törléséhez. Azt javasoljuk, hogy várjon, amíg az összes eszközt át nem adta, mielőtt futtatná. Miután törölte az eszközöket, nem kérheti vissza őket.

*   **deleteDestDevices** (5. argumentum) – ezt állítsa igaz értékre a célként megadott hubhoz (klón) regisztrált összes eszköz törléséhez. Erre akkor lehet szükség, ha többször szeretné átmásolni az eszközöket. 

Az alapszintű parancs a *DotNet futtatása* lesz – ez azt jelzi, hogy a .net létrehozza a helyi csproj fájlt, majd futtatja azt. A Futtatás előtt adja hozzá a parancssori argumentumokat a befejezéshez. 

A parancssor a következő példához hasonlóan fog kinézni:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Környezeti változók használata a kapcsolatok karakterláncai esetében

1. A minta futtatásához szüksége lesz a régi és az új IoT-hubok kapcsolódási karakterláncára, valamint egy olyan Storage-fiókra, amelyet az ideiglenes munkafájlok számára használhat. Ezek az értékek a környezeti változókban lesznek tárolva.

1. A kapcsolódási karakterlánc értékeinek beszerzéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com). 

1. Helyezze el a kapcsolatok karakterláncait valahol, például a jegyzettömböt. Ha a következőt másolja, illessze be a kapcsolatok karakterláncait közvetlenül oda, ahol elmennek. Ne adjon szóközt az egyenlőségjel körül, vagy módosítsa a változó nevét. Emellett nincs szükség dupla idézőjelre a kapcsolatok karakterláncok köré. Ha idézőjelek közé helyezi a Storage-fiókhoz tartozó kapcsolatok karakterláncát, az nem fog működni.

   Windows esetén a környezeti változók beállítása:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   A Linux esetében a környezeti változók meghatározása a következő:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Az IoT hub kapcsolódási karakterláncai esetében lépjen a portál minden központjának helyére. Kereshet a hub **erőforrásai** között. Ha ismeri az erőforráscsoportot, lépjen az **erőforráscsoportok** elemre, válassza ki az erőforráscsoportot, majd válassza ki a hubot az adott erőforráscsoport eszközeinek listájából. 

1. A hub beállításai közül válassza a **megosztott elérési szabályzatok** lehetőséget, majd válassza a **iothubowner** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. Tegye ugyanezt a célként megadott hubhoz. Adja hozzá őket a megfelelő SET parancsokhoz.

1. A Storage-fiók kapcsolati karakterláncához keresse meg a Storage-fiókot az **erőforrásokban** vagy az **erőforráscsoport** alatt, és nyissa meg. 
   
1. A beállítások szakaszban válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. A megfelelő SET parancshoz helyezze a szöveges fájlba a kapcsolatok karakterláncát. 

Most már rendelkezik a környezeti változókkal a SET parancsokkal rendelkező fájlban, és tudja, milyen parancssori argumentumok vannak. Futtassa a mintát.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>A minta alkalmazás futtatása és parancssori argumentumok használata

1. Nyisson meg egy parancsablakot. Válassza a Windows lehetőséget, és írja be a `command prompt` parancsot a parancssori ablak beszerzéséhez.

1. Másolja a környezeti változókat beállító parancsokat egy időben, és illessze be őket a parancssorablakba, és válassza az ENTER billentyűt. Ha elkészült, írja be `SET` a parancssori ablakot a környezeti változók és azok értékeinek megtekintéséhez. Miután bemásolta ezeket a parancssori ablakba, nem kell újból átmásolnia, hacsak nem nyit meg egy új parancssori ablakot.

1. A parancssori ablakban módosítsa a könyvtárakat, amíg be nem fejeződik a./ImportExportDevicesSample (ahol a ImportExportDevicesSample. csproj fájl létezik). Ezután írja be a következőt, és adja meg a parancssori argumentumokat.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    A DotNet-parancs létrehozza és futtatja az alkalmazást. Mivel az alkalmazás futtatásakor átadja a beállításokat, az alkalmazás minden egyes futtatásakor módosíthatja az értékeket. Előfordulhat például, hogy egyszer szeretné futtatni, és új eszközöket szeretne létrehozni, majd újra futtatja, majd átmásolja az eszközöket egy új hubhoz, és így tovább. Az összes lépést is elvégezheti ugyanabban a futtatásban, de javasoljuk, hogy ne töröljön semmilyen eszközt, amíg nem biztos benne, hogy elkészült a klónozással. Íme egy példa, amely 1000 eszközt hoz létre, majd átmásolja őket a másik hubhoz.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Miután meggyőződött róla, hogy az eszközök másolása sikeresen megtörtént, az alábbihoz hasonló módon távolíthatja el az eszközöket a forrás-hubhoz:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>A minta alkalmazás futtatása a Visual Studióval

1. Ha az alkalmazást a Visual Studióban szeretné futtatni, módosítsa az aktuális könyvtárat arra a mappára, ahol a IoTHubServiceSamples. SLN fájl található. Ezután futtassa ezt a parancsot a parancssori ablakban a megoldás megnyitásához a Visual Studióban. Ezt ugyanabban a parancsablakban kell megtennie, ahol a környezeti változókat is megadhatja, így ezek a változók ismertek.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Kattintson a jobb gombbal a projekt *ImportExportDevicesSample* , és válassza a **beállítás indítási projektként** lehetőséget.    
    
1. Állítsa be a változókat a program. cs felső részén a ImportExportDevicesSample mappában az öt lehetőséghez.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Az alkalmazás futtatásához válassza az F5 billentyűt. A futás befejezése után megtekintheti az eredményeket.

### <a name="view-the-results"></a>Az eredmények megtekintése 

Megtekintheti az eszközöket a [Azure Portalban](https://portal.azure.com) , és ellenőrizheti, hogy azok az új helyen találhatók-e.

1. Nyissa meg az új hubot a [Azure Portal](https://portal.azure.com)használatával. Válassza ki a hubot, majd válassza az **IoT-eszközök** lehetőséget. A régi központból a klónozott hubhoz másolt eszközök láthatók. Megtekintheti a klónozott központ tulajdonságait is. 

1. Az importálási/exportálási hibák ellenőrzéséhez lépjen a [Azure Portal](https://portal.azure.com) Azure Storage-fiókjába, és tekintse meg a `devicefiles` tárolóját `ImportErrors.log` . Ha a fájl üres (a méret 0), nem történt hiba. Ha ugyanazt az eszközt többször próbálja meg importálni, a rendszer visszautasítja az eszközt a második alkalommal, és hibaüzenetet küld a naplófájlba.

### <a name="committing-the-changes"></a>A módosítások véglegesítése 

Ekkor átmásolta a hubot az új helyre, és áttelepítette az eszközöket az új klónba. Most meg kell változtatnia a módosításokat, hogy az eszközök működjenek a klónozott hubhoz.

A módosítások elvégzéséhez a következő lépéseket kell végrehajtania: 

* Frissítse az egyes eszközöket a IoT Hub állomásnév módosítására, hogy az IoT Hub állomásneve az új hubhoz mutasson. Ezt ugyanazt a módszert használja, amelyet az eszköz kiépítés használt.

* Módosítsa a régi hubhoz hivatkozó összes alkalmazást, hogy az új hubhoz mutasson.

* Ha elkészült, az új hubhoz kell futnia. A régi hubhoz nem tartozhat aktív eszköz, és leválasztott állapotban kell lennie. 

### <a name="rolling-back-the-changes"></a>A módosítások visszaállítása

Ha úgy dönt, hogy visszaállítja a módosításokat, a következő lépéseket kell végrehajtania:

* Frissítse az egyes eszközöket úgy, hogy az IoT Hub hostname-t úgy módosítsa, hogy az a régi központ IoT Hub állomásnevét mutasson. Ezt ugyanazt a módszert használja, amelyet az eszköz kiépítés használt.

* Módosítsa az új hubhoz hivatkozó összes alkalmazást, hogy az a régi hubhoz mutasson. Ha például az Azure Analytics szolgáltatást használja, előfordulhat, hogy újra kell konfigurálnia a [Azure stream Analytics bemenetét](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).

* Törölje az új hubot. 

* Ha útválasztási erőforrásokkal rendelkezik, a régi hub konfigurációjának továbbra is a megfelelő útválasztási konfigurációra kell mutatnia, és a hub újraindítása után működnie kell ezekkel az erőforrásokkal.

### <a name="checking-the-results"></a>Az eredmények ellenőrzése 

Az eredmények vizsgálatához módosítsa a IoT-megoldását úgy, hogy az új helyen mutasson a hubhoz, majd futtassa azt. Más szóval hajtsa végre ugyanezeket a műveleteket az előző központtal végrehajtott új hubhoz, és ellenőrizze, hogy megfelelően működnek-e. 

Ha implementálta az útválasztást, ellenőrizze, hogy az üzenetek megfelelően vannak-e irányítva az erőforrásokhoz.

## <a name="clean-up"></a>A feleslegessé vált elemek eltávolítása

Ne törölje a tisztítást, amíg nem biztos benne, hogy az új hub működik, és az eszközök megfelelően működnek. Ha ezt a funkciót használja, ügyeljen arra, hogy tesztelje az útválasztást is. Ha elkészült, törölje a régi erőforrásokat a következő lépések végrehajtásával:

* Ha még nem tette meg, törölje a régi hubot. Ezzel eltávolítja az összes aktív eszközt a központból.

* Ha rendelkezik olyan útválasztási erőforrásokkal, amelyeket az új helyre helyezett át, akkor törölheti a régi útválasztási erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Az IoT hub egy új régióban található új hubhoz klónozott, és az eszközön is elkészült. További információ a IoT Hub található Identity registryben végzett tömeges műveletek végrehajtásáról: [IoT hub eszköz-identitások tömeges importálása és exportálása](iot-hub-bulk-identity-mgmt.md).

A központ IoT Hubával és fejlesztésével kapcsolatos további információkért tekintse meg a következő cikkeket.

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)

* [IoT Hub útválasztási oktatóanyag](tutorial-routing.md)

* [IoT Hub eszközkezelés – áttekintés](iot-hub-device-management-overview.md)

* Ha telepíteni szeretné a minta alkalmazást, tekintse meg a [.net Core-alkalmazás üzembe helyezése](/dotnet/core/deploying/index)című témakört.