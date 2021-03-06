---
title: Service Bus a várólisták kimenetét Azure Stream Analytics
description: Ez a cikk Service Bus a várólistákat Azure Stream Analytics kimenetként ismerteti.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: ad5bb90a0df68cc6c1c230c234c1f695312da3d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451779"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Service Bus a várólisták kimenetét Azure Stream Analytics

[Service Bus várólisták](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) egy vagy több versengő fogyasztó számára biztosítanak egy FIFO-üzenet kézbesítését. Az üzeneteket általában a fogadók a várólistához való felvételének időbeli sorrendjében fogadják és dolgozzák fel. Minden üzenet fogadása és feldolgozása csak egy üzenet felhasználója által történik.

A [1,2-es kompatibilitási szinten](stream-analytics-compatibility-level.md)Azure stream Analytics a [speciális üzenetsor-kezelési protokoll (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) üzenetkezelő protokollt használja a Service Bus várólistákba és témakörökbe való íráshoz. A AMQP lehetővé teszi többplatformos hibrid alkalmazások készítését egy nyílt szabványú protokoll használatával.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és a várólista kimenetének létrehozásához szükséges leírásokat.

| Tulajdonság neve | Description |
| --- | --- |
| Kimeneti alias |Egy rövid név, amely lekérdezésekben a lekérdezés kimenetének a Service Bus üzenetsor felé történő irányítására szolgál. |
| Service Bus névtér |Az üzenetküldési entitások készletének tárolója. |
| Üzenetsor neve |Az Service Bus üzenetsor neve. |
| Üzenetsor-házirend neve |Amikor létrehoz egy várólistát, közös hozzáférési szabályzatokat is létrehozhat a várólista **konfigurálása** lapon. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. |
| Üzenetsor-házirend kulcsa |A Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. |
| Eseményszerializációs formátum |A kimeneti adatmennyiség szerializálási formátuma. A JSON, a CSV és a Avro támogatottak. |
| Encoding |A CSV és a JSON esetében jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó |Csak a CSV-szerializáláshoz alkalmazható. Stream Analytics számos gyakori elhatárolót támogat a CSV-formátumú adatszerializáláshoz. A támogatott értékek a következők: vessző, pontosvessző, szóköz, TAB és függőleges sáv. |
| Formátum |Csak JSON-típus esetén alkalmazható. A **sor elválasztva** érték azt jelenti, hogy a kimenet formázása úgy történik, hogy minden JSON-objektum új sorral van elválasztva. Ha a **sor elválasztva** lehetőséget választja, a JSON egyszerre csak egy objektumot olvas be. A teljes tartalom önmagában nem érvényes JSON. A **Array** beállítás azt adja meg, hogy a kimenet a JSON-objektumok tömbje legyen formázva. |
| Tulajdonságok oszlopai | Választható. Vesszővel tagolt oszlopokat kell csatolni a kimenő üzenet felhasználói tulajdonságaihoz a hasznos adatok helyett. A szolgáltatással kapcsolatos további információkért lásd: [Egyéni metaadatok tulajdonságai a kimenethez](#custom-metadata-properties-for-output). |
| Rendszertulajdonság-oszlopok | Választható. A Rendszertulajdonságok és a hozzájuk tartozó oszlopnevek neve, amelyeket az adattartalom helyett a kimenő üzenethez kell csatolni.  |

A partíciók száma a [Service Bus SKU és a méret alapján](../service-bus-messaging/service-bus-partitioning.md)történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke.

## <a name="partitioning"></a>Particionálás

A particionálás automatikusan kiválasztva. A partíciók száma a [Service Bus SKU és a méret](../service-bus-messaging/service-bus-partitioning.md)alapján történik. A partíciós kulcs az egyes partíciók egyedi egész értékének értéke. A kimeneti írók száma megegyezik a kimeneti várólistában található partíciók számával.

## <a name="output-batch-size"></a>Kimeneti köteg mérete

A maximális üzenet mérete 256 KB/üzenet a standard szintű csomaghoz, a prémium szint pedig 1MB. További információ: [Service Bus korlátok](../service-bus-messaging/service-bus-quotas.md). Az optimalizáláshoz egyetlen eseményt kell használnia üzenetként.

## <a name="custom-metadata-properties-for-output"></a>A kimenet egyéni metaadat-tulajdonságai

A lekérdezési oszlopokat felhasználói tulajdonságokként csatolhatja a kimenő üzenetekhez. Ezek az oszlopok nem kerülnek bele a hasznos adatokba. A tulajdonságok a kimeneti üzenet szótárának formájában jelennek meg. A *kulcs* az oszlopnév és az *érték* az oszlop értéke a tulajdonságok szótárban. A rekord és a tömb kivételével minden Stream Analytics adattípus támogatott.

A következő példában a mezők és a `DeviceId` `DeviceStatus` metaadatokhoz lesznek adva.

1. Használja a következő lekérdezést:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Konfigurálja `DeviceId,DeviceStatus` a tulajdonság oszlopait a kimenetben.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Tulajdonságok oszlopai":::

Az alábbi ábrán a EventHub által a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)használatával megtekintett kimeneti üzenet várt tulajdonságai láthatók.

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Egyéni esemény tulajdonságai":::

## <a name="system-properties"></a>Rendszertulajdonságok

A lekérdezési oszlopokat [rendszertulajdonságként](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) csatolhatja a kimenő Service Bus-üzenetsor vagy-témakör üzeneteihez.

Ezek az oszlopok nem lépnek be a hasznos adatokba, hanem a megfelelő BrokeredMessage [rendszertulajdonságot](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) a lekérdezési oszlop értékeivel töltik fel.
Ezek a Rendszertulajdonságok támogatottak – `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Ezeknek az oszlopoknak a karakterlánc-értékeit a rendszer a megfelelő rendszertulajdonság-érték típusként értelmezi, és az elemzési hibák adathibákként lesznek kezelve.
Ez a mező JSON-objektum formátumként van megadva. A formátum részletei a következők:

* Kapcsos zárójelek között {} .
* Kulcs/érték párokba írva.
* A kulcsoknak és az értékeknek sztringeknek kell lenniük.
* A kulcs a rendszer tulajdonságának neve és értéke a lekérdezési oszlop neve.
* A kulcsok és az értékek kettősponttal vannak elválasztva.
* A kulcs/érték párok vesszővel vannak elválasztva.

Ez a tulajdonság használatát mutatja be –

* Lekérdezés `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Rendszertulajdonság oszlopai: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Ezzel beállítja a `MessageId` Service Bus üzenetsor üzeneteinek `column1` értékeit és a PartitionKey `column2` értékeit.

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)