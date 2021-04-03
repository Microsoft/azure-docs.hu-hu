---
title: A RabbitMQ integrálása a Azure Service Bus használatával
description: Részletes útmutató a RabbitMQ és a Azure Service Bus integrálásához
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91301105"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>A RabbitMQ integrálása a Azure Service Bus használatával

Ebben az útmutatóban megtudhatja, hogyan küldhet üzeneteket a RabbitMQ a Azure Service Busba.

Íme néhány forgatókönyv, amelyekben igénybe veheti ezeket a képességeket:

- **Edge-beállítások**: van egy Edge-telepítőnk, amelyben üzeneteket küldünk a RabbitMQ-be, de ezeket az üzeneteket továbbra is [Azure Service Busjuk](./service-bus-messaging-overview.md) további feldolgozás céljából, így az [Azure Big adatszolgáltatások számos funkcióját](/azure/architecture/guide/architecture-styles/big-data)felhasználhatjuk.
- **Hibrid felhő**: a vállalat csak olyan harmadik féltől származó, amely RabbitMQ használ az üzenetkezelési igényeiknek. Más felhőben vannak. Az Azure-ba való áttérés közben már megkezdheti az adatmegosztást a RabbitMQ és a Azure Service Bus összekötésével.
- **Harmadik féltől származó integráció**: egy harmadik fél RabbitMQ használ, és szeretné elküldeni az adatainkat, de a szervezeten kívül vannak. Az SAS-kulccsal biztosítjuk számukra, hogy hozzáférést biztosítanak számukra egy korlátozott számú Azure Service Bus-várólistához, ahol az üzeneteiket továbbítják a szolgáltatásba.

A lista továbbra is fennáll, de a RabbitMQ az Azure-hoz való áthidalása révén megoldjuk a legtöbb felhasználási esetet.

Először létre kell hoznia egy ingyenes Azure-fiókot a regisztráláshoz [](https://azure.microsoft.com/free/)

Miután bejelentkezett a fiókjába, lépjen a [Azure Portal](https://portal.azure.com/) , és hozzon létre egy új Azure Service Bus [névteret](./service-bus-create-namespace-portal.md). A névterek azok a hatókör-tárolók, ahol az üzenetkezelési összetevők élőak, például a várólisták és a témakörök.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Új Azure Service Bus névtér hozzáadása

A Azure Portal kattintson a nagy plusz gombra egy új erőforrás hozzáadásához

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Erőforrás létrehozása":::

Ezután válassza az integráció lehetőséget, majd kattintson a Azure Service Bus elemre egy üzenetküldési névtér létrehozásához:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Azure Service Bus kiválasztása":::

A rendszer felszólítja a névtér adatainak megadására. Válassza ki a használni kívánt Azure-előfizetést. Ha nem rendelkezik [erőforráscsoporthoz](../azure-resource-manager/management/manage-resource-groups-portal.md), létrehozhat egy újat.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Névtér létrehozása":::

`rabbitmq`A for `Namespace name` , de bármire is lehet szükség. Ezután állítsa be `East US` a helyet. Válassza `Basic` az ár szintet.

Ha minden rendben volt, a következő megerősítő képernyőnek kell megjelennie:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Névtér-visszaigazolás létrehozása":::

Ezután visszatérhet a Azure Portal az új `rabbitmq` névtér látható. Kattintson rá az erőforrás eléréséhez, hogy hozzá lehessen adni egy várólistát.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Erőforráslista új névtérrel":::

## <a name="creating-our-azure-service-bus-queue"></a>Azure Service Bus üzenetsor létrehozása

Most, hogy elvégezte a Azure Service Bus névteret, kattintson a `Queues` bal oldalon található gombra, `Entities` és adja hozzá az új üzenetsor:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Várólista létrehozása":::

A várólista neve csak emlékeztetőként fog megjelenni, `from-rabbitmq` ahol az üzenetek érkeznek. Az összes többi beállítást is meghagyhatja alapértelmezettként, de az alkalmazás igényeinek megfelelően módosíthatja őket.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>A RabbitMQ lapát beépülő modul engedélyezése

Ahhoz, hogy üzeneteket RabbitMQ a Azure Service Busba, a RabbitMQ-mel csomagolt [lapát beépülő modult](https://www.rabbitmq.com/shovel.html) fogjuk használni. A beépülő modult és a hozzá tartozó vizualizációs felületet a következő paranccsal engedélyezheti:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Előfordulhat, hogy a parancsot root-ként kell futtatnia.

Itt az ideje, hogy lekérje a RabbitMQ az Azure-hoz való csatlakoztatásához szükséges hitelesítő adatokat.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>RabbitMQ csatlakoztatása Azure Service Bushoz

Létre kell hoznia egy [megosztott hozzáférési](../storage/common/storage-sas-overview.md) szabályzatot (SAS) a várólistához, így a RabbitMQ üzeneteket tehet közzé. A SAS-szabályzat segítségével megadhatja, hogy milyen külső fél számára engedélyezett az erőforrás. Az a gondolat, hogy a RabbitMQ képes üzeneteket küldeni, de nem figyeli vagy nem kezeli a várólistát.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="SAS-szabályzat hozzáadása":::

Jelölje be a `Send` jelölőnégyzetet, majd kattintson rá a `Create` sas-szabályzat érvénybe helyezéséhez.

A szabályzat létrehozása után kattintson rá az **elsődleges kapcsolódási karakterlánc** megtekintéséhez. Azt fogjuk használni, hogy RabbitMQ beszéljen a Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="SAS-szabályzat beszerzése":::

Ahhoz, hogy használhassa a kapcsolódási karakterláncot, át kell alakítania a RabbitMQ AMQP-kapcsolódási formátumára. Ezért lépjen a [kapcsolódási karakterlánc-átalakító eszközre](https://red-mushroom-0f7446a0f.azurestaticapps.net/) , és illessze be a kapcsolódási karakterláncot az űrlapon, majd kattintson a Konvertálás gombra. Ekkor megjelenik egy RabbitMQ, amely készen áll. (A webhely minden helyi böngészőt futtat, így az adatai nem lesznek továbbítva a vezetékes hálózaton keresztül). A [githubon](https://github.com/videlalvaro/connstring_to_amqp)érheti el a forráskódot.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="A kapcsolatok karakterláncának konvertálása":::

Most nyissa meg a RabbitMQ-kezelő beépülő modult a böngészőben `http://localhost:15672/#/dynamic-shovels` , és lépjen a `Admin -> Shovel Management` címre, ahol felveheti az új lapátot, amely gondoskodik arról, hogy a RabbitMQ-várólistákból üzeneteket küldjön az Azure Service Bus-várólistára.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="RabbitMQ lapát hozzáadása":::

Itt hívja meg a lapátot `azure` , és válassza ki `AMQP 0.9.1` a forrásoldali protokollt. A képernyőképen van `amqp://` , amely az alapértelmezett URI, amely összekapcsolja a kapcsolatot egy helyi RabbitMQ-kiszolgálóval. Győződjön meg arról, hogy a jelenlegi üzemelő példányához alkalmazkodik.

A műveletek üzenetsor oldalán használhatja a `azure` várólista nevét. Ha ez a várólista nem létezik, akkor a RabbitMQ létrehozza. Kiválaszthatja azt a várólista-nevet is, amely már létezik. A többi beállítás alapértelmezettként is elhagyható.

Ezután a `destination` dolgok oldalon válassza `AMQP 1.0` a protokollt. A `URI` mezőben adja meg az előző lépésben kapott összekapcsolási karakterláncot, és az Azure-kapcsolati karakterláncot a RabbitMQ formátumra alakította át. Ennek így kell kinéznie:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

A `Address` mezőben adja meg az **Azure Service Bus üzenetsor** nevét, ebben az esetben a rendszer meghívta a nevet `from-rabbitmq` . Kattintson `Add Shovel` a elemre, és a telepítőnek készen kell állnia az üzenetek fogadásának megkezdéséhez.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Üzenetek közzététele a RabbitMQ-ből a Azure Service Busba

A RabbitMQ kezelőfelületén `Queues` válassza ki a `azure` várólistát, és keresse meg a `Publish message` panelt. Egy űrlap jelenik meg, amely lehetővé teszi, hogy közvetlenül a várólistára tegye közzé az üzeneteket. A példánkban csak `fist message` a és a találatok lesznek hozzáadva `Payload` `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Első üzenet közzététele":::

Lépjen vissza az Azure-ba, és vizsgálja meg a várólistáját. Kattintson a `Service Bus Explorer` bal oldali panelre, majd kattintson a _betekintés_ gombra. Ha minden rendben volt, látni fogja, hogy a várólista már egy üzenettel rendelkezik. Yay, Gratula!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus üzenetsor":::

Azonban győződjön meg arról, hogy az üzenet a RabbitMQ-ból küldött üzenet. Válassza a `Peek` fület, és kattintson a `Peek` gombra a várólistán lévő utolsó üzenetek lekéréséhez. Kattintson az üzenetre a tartalmának vizsgálatához. Az alábbi képhez hasonlóan kell megjelennie, ahol a `first message` szerepel a listában.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Várólista bepillantása":::

## <a name="lets-recap"></a>Bedugni

Gratulálunk! Sokat elért! Ön felügyelte, hogy az üzenetek a RabbitMQ-ból Azure Service Busba kerülnek, a következő lépéseket kell megtennie:

1. Azure Service Bus névtér létrehozása
2. Várólista hozzáadása a névtérhez
3. SAS-szabályzat hozzáadása a várólistához
4. A várólista-kapcsolatok karakterláncának beolvasása
5. A RabbitMQ lapát beépülő modul engedélyezése a felügyeleti felületen &
6. A Azure Service Bus-kapcsolódási sztring átalakítása RabbitMQ AMQP formátumára
7. Vegyen fel egy új lapátot a RabbitMQ & összekapcsolásához Azure Service Bus
8. Üzenetek közzététele

Az előző lépésekkel az Ön szervezetének az Azure-on kívüli integrált területeit követheti. A lapátos beépülő modul lehetővé teszi, hogy az üzeneteket a RabbitMQ Azure Service Busba szállítsa. Ez óriási előnyökkel jár, mivel mostantól engedélyezheti a megbízható harmadik felek számára, hogy az alkalmazásaikat az Azure-beli üzembe helyezéssel csatlakozhassanak.

A végén az üzenetküldés a kapcsolatok engedélyezését, és ezzel a technikával nemrég nyitott egy újat.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Service Bus](./service-bus-messaging-overview.md)
- További információ a [AMQP 1,0-támogatásáról Service Bus](./service-bus-amqp-overview.md)
