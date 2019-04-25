---
title: Az Azure Event Grid eseménykezelők
description: Támogatott eseménykezelők ismerteti az Azure Event Gridhez
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 33604a16f5895e20d4475d1dd8b27c34184feb72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345481"
---
# <a name="event-handlers-in-azure-event-grid"></a>Az Azure Event Grid eseménykezelők

Eseménykezelőt az a hely, ahol a rendszer. A kezelő néhány további művelet feldolgozni az eseményt vesz igénybe. Több Azure-szolgáltatások automatikusan megtörténik az események kezeléséhez. Bármilyen WebHook események kezeléséhez is használható. A WebHook nem kell-e üzemeltetni, arra zpracovávat události. Event Grid csak a HTTPS WebHook-végpontokat támogatja.

Ez a cikk tartalmakra mutató hivatkozásokat biztosít minden eseménykezelő.

## <a name="azure-automation"></a>Azure Automation

Azure Automation segítségével a runbookok automatizált eseményeinek feldolgozása.

|Cím  |Leírás  |
|---------|---------|
|[Oktatóanyag: Az Event Grid és a Microsoft Teams az Azure Automation](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény aktiválása a címkéket a virtuális gép, és a egy Microsoft Teams-csatornához küldött üzenet aktivál egy Automation-runbook. |

## <a name="azure-functions"></a>Azure Functions

Az Azure Functions használata kiszolgáló nélküli által kiváltott kódfuttatáshoz.

Ha az Azure Functionst használja kezelőként, használja az Event Grid-triggereket az általános HTTP-triggerek helyett. Az Event Grid automatikusan érvényesíti az Event Grid függvény eseményindítóit. Általános HTTP-eseményindítók esetén meg kell valósítani az [érvényesítési választ](security-authentication.md#webhook-event-delivery).

|Cím  |Leírás  |
|---------|---------|
| [Event Grid-trigger az Azure Functions szolgáltatáshoz](../azure-functions/functions-bindings-event-grid.md) | Az Event Grid eseményindító használata a funkciók áttekintése. |
| [Oktatóanyag: az Event Grid használatával feltöltött képek átméretezésének automatizálása](resize-images-on-storage-blob-upload-event.md) | Felhasználók webalkalmazás keresztül képek feltöltéséhez a storage-fiókot. Amikor létrejön egy storage-blobból, az Event Grid eseményt küld az a függvényalkalmazást, amely átméretezi a feltöltött kép. |
| [Oktatóanyag: big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md) | Az Event Hubs rögzítési fájlt hoz létre, amikor az Event Grid eseményt küld egy függvényalkalmazáshoz. Az alkalmazás lekéri a rögzítési fájlt, és egy data warehouse-bA migrálja az adatokat. |
| [Oktatóanyag: Azure Service Bus – Azure Event Grid integráció példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld a Service Bus-témakörbe, alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="event-hubs"></a>Event Hubs

Az Event Hubs akkor használja, ha a megoldás gyorsabb ütemben fel tud dolgozni az események beolvassa az eseményeket. Az alkalmazás dolgozza fel az Event hubsból, saját ütemezés. A bejövő események kezeléséhez a Eseményfeldolgozási skálázhatja.

Az Event Hubs egy eseményforrás vagy egy eseménykezelő működhet. A következő cikk az Event Hubs használata a kezelő jeleníti meg.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események átirányítása az Azure Event Hubs az Azure CLI-vel és az Event Griddel](custom-event-to-eventhub.md) | Egyéni eseményt küld egy event hubot, az alkalmazások feldolgozását. |
| [Resource Manager-sablon: egyéni témakör és az Event Hubs-végpont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Resource Manager-sablonnal, amely egy egyéni témakör-előfizetést hoz létre. Az Azure Event Hubs küld eseményeket. |

Példák az Event hubs forrásként: [az Event Hubs-forrás](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

Azure Relay hibrid kapcsolatok használatával az események küldése az alkalmazásokat, amelyek egy vállalati hálózaton belül, és nem rendelkezik egy nyilvánosan elérhető végpontot.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: a hibrid kapcsolat események küldése](custom-event-to-hybrid-connection.md) | Egyéni eseményt küld egy meglévő, a hibrid kapcsolat feldolgozása egy figyelő alkalmazás. |

## <a name="logic-apps"></a>Logic Apps

A Logic Apps segítségével automatizálhatja üzleti folyamatait eseményekre reagálni.

|Cím  |Leírás  |
|---------|---------|
| [Oktatóanyag: az Azure Event Grid és a Logic Apps a virtuális gépek módosításainak monitorozása](monitor-virtual-machine-changes-event-grid-logic-app.md) | Egy logikai alkalmazást egy virtuális gép módosításainak figyeli, és ezeket a módosításokat kapcsolatos e-mailt küld. |
| [Oktatóanyag: küldése e-mailes értesítést az Azure IoT Hub-események Logic Apps használatával](publish-iot-hub-events-to-logic-apps.md) | Egy logikai alkalmazás küld értesítő e-mailt, minden alkalommal, amikor egy eszköz az IoT hubhoz való hozzáadása. |
| [Oktatóanyag: Azure Service Bus – Azure Event Grid integráció példák](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid üzeneteket küld a Service Bus-témakörbe, alkalmazás és a logikai alkalmazás működéséhez. |

## <a name="queue-storage"></a>Queue Storage

A Queue storage használatával kapja, le kell kérnie. A Queue storage használatával, ha egy hosszú ideig futó folyamatot, amely a túl sok ideig nem válaszolt. Eseményeket küld a Queue storage, az alkalmazás kérheti le és dolgozza fel a saját ütemezés szerint.

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események átirányítása az Azure CLI és az Event Grid Azure Queue storage](custom-event-to-queue-storage.md) | Ismerteti, hogyan lehet egyéni eseményeket küldeni egy Queue Storage. |

## <a name="webhooks"></a>Webhookok

Webhookok használata a eseményekre reagáló, testre szabható végpontok.

|Cím  |Leírás  |
|---------|---------|
| Gyors útmutató: hozzon létre, és az egyéni események – útvonal [Azure CLI-vel](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md), és [portál](custom-event-quickstart-portal.md). | Bemutatja, hogyan küldhet egyéni eseményeket egy Webhookot. |
| Gyors útmutató: Blob storage-események átirányítása egyéni webes végpontra az - [Azure CLI-vel](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json), és [portál](blob-event-quickstart-portal.md). | Bemutatja, hogyan blob storage-események küldéséhez egy Webhookot. |
| [Gyors útmutató: a tároló beállításjegyzék események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja a tároló-beállításjegyzék események küldése az Azure CLI használatával. |
| [Áttekintés: a HTTP-végponton események fogadása](receive-events.md) | Események fogadása az esemény-előfizetés, és fogadni, és deszerializálni az eseményeket egy HTTP-végpontot ellenőrzését ismerteti. |

## <a name="next-steps"></a>További lépések

* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
