---
title: A Azure Service Bus Queues használata PHP-vel
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre PHP-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85341125"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Gyors útmutató: Service Bus Queues használata PHP-vel
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre PHP-alkalmazásokat egy Service Bus üzenetsor üzeneteinek küldéséhez és fogadásához. 

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket.
    1. Olvassa el Service Bus **várólisták** gyors **áttekintését** . 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolatok karakterláncának** beolvasása. 

        > [!NOTE]
        > Ebben az oktatóanyagban a PHP használatával fog létrehozni egy **várólistát** a Service Bus névtérben. 
3. [PHP-hoz készült Azure SDK](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Az Azure Blob servicehoz hozzáférő PHP-alkalmazások létrehozásának egyetlen követelménye, hogy az [Azure SDK for php](https://github.com/Azure/azure-sdk-for-php) osztályait a programkódon belülre hivatkozzon. Az alkalmazás vagy a Jegyzettömb létrehozásához bármilyen fejlesztői eszközt használhat.

> [!NOTE]
> A PHP-telepítésnek is telepítve és engedélyezve kell lennie az [OpenSSL bővítménnyel](https://php.net/openssl) .

Ebben az útmutatóban a szolgáltatás funkcióit fogja használni, amelyek a PHP-alkalmazásokból helyileg, vagy egy Azure-beli webes szerepkörben, feldolgozói szerepkörben vagy webhelyen futó kódban hívhatók.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél kódtárainak beszerzése
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Az Service Bus üzenetsor API-k használatához tegye a következőket:

1. Hivatkozzon az automatikus betöltő fájlra a [require_once][require_once] utasítás használatával.
2. Hivatkozzon az esetlegesen használt osztályokra.

Az alábbi példa azt mutatja be, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozni az `ServicesBuilder` osztályra.

> [!NOTE]
> Ez a példa (és a cikkben szereplő további példák) feltételezi, hogy telepítette az Azure-hoz készült PHP ügyféloldali kódtárakat a zeneszerző használatával. Ha manuálisan vagy PEAR-csomagként telepítette a kódtárakat, a **WindowsAzure. php** automatikus betöltő fájlra kell hivatkoznia.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban az `require_once` utasítás mindig látható lesz, de csak a végrehajtáshoz szükséges osztályok vannak hivatkozva.

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolatok beállítása
Service Bus-ügyfél létrehozásához a következő formátumban kell érvényes kapcsolódási karakterláncot tartalmaznia:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` általában a formátuma `[yourNamespace].servicebus.windows.net` .

Bármely Azure-szolgáltatásbeli ügyfél létrehozásához a osztályt kell használnia `ServicesBuilder` . A következőket teheti:

* Adja át közvetlenül a kapcsolódási karakterláncot.
* A **CloudConfigurationManager (CCM)** segítségével több külső forrást is megvizsgálhat a kapcsolódási karakterlánchoz:
  * Alapértelmezés szerint egy külső forrás – környezeti változók támogatásával jár.
  * Új forrásokat az osztály kibővítésével adhat hozzá `ConnectionStringSource`

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
Service Bus várólistákhoz a osztályon keresztül végezhet felügyeleti műveleteket `ServiceBusRestProxy` . Egy `ServiceBusRestProxy` objektum a `ServicesBuilder::createServiceBusService` gyári metódussal lett létrehozva egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a jogkivonat-engedélyeket a kezeléséhez.

Az alábbi példa bemutatja, hogyan `ServiceBusRestProxy` hozhat létre és hívhat meg egy, a `ServiceBusRestProxy->createQueue` `myqueue` szolgáltatási névtéren belül elnevezett üzenetsor létrehozását `MySBNamespace` :

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Az `listQueues` objektumok metódusával ellenőrizhető, `ServiceBusRestProxy` hogy egy adott névvel rendelkező várólista már létezik-e egy névtéren belül.
> 
> 

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzenetet szeretne küldeni egy Service Bus üzenetsor számára, az alkalmazás meghívja a `ServiceBusRestProxy->sendQueueMessage` metódust. A következő kód bemutatja, hogyan küldhet üzenetet a `myqueue` korábban a `MySBNamespace` szolgáltatási névtéren belül létrehozott várólistára.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Az Service Bus Queues a [BrokeredMessage][BrokeredMessage] osztály példányai. A [BrokeredMessage][BrokeredMessage] -objektumok szabványos metódusokkal és tulajdonságokkal rendelkeznek, amelyek az egyéni alkalmazásspecifikus tulajdonságok tárolására szolgálnak, valamint egy tetszőleges alkalmazásadatok törzsét.

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. A várólista méretének felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

Az üzenetek várólistából való fogadásának legjobb módja egy `ServiceBusRestProxy->receiveQueueMessage` metódus használata. Az üzenetek két különböző módban is fogadhatók: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) és [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatakor a fogadás egy egylépéses művelet; Ez azt jelenti, hogy amikor a Service Bus olvasási kérést kap egy várólistában lévő üzenethez, az üzenetet felhasználva jelöli meg, és visszaadja az alkalmazásnak. A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel a Service Bus a rendszer felhasználja az üzenetet, amikor az alkalmazás újraindul, és újrakezdi az üzenetek felhasználását, az összeomlás előtt kimaradt az üzenetből.

Az alapértelmezett [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) módban egy üzenet fogadása kétlépcsős művelet lesz, amely lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor Service Bus kap egy kérést, a rendszer megkeresi a következő felhasználandó üzenetet, zárolja, hogy megakadályozza a többi fogyasztótól a fogadást, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja azt a későbbi feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a kapott üzenet átadásával `ServiceBusRestProxy->deleteMessage` . Ha Service Bus látja a `deleteMessage` hívást, az üzenetet a rendszer felhasználja, és eltávolítja a várólistából.

Az alábbi példa bemutatja, hogyan fogadhat és dolgozhat fel egy üzenetet a [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) mód használatával (az alapértelmezett mód).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése

A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor a metódust meghívhatja a `unlockMessage` kapott üzeneten (a `deleteMessage` metódus helyett). Ez azt eredményezi, hogy Service Bus a várólistán belüli üzenet zárolását, és elérhetővé teszi azt újra, akár egyazon alkalmazás, akár egy másik alkalmazás.

A várólistán lévő üzenethez is van egy időkorlát társítva, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenetet, és elérhetővé teszi azt újra.

Abban az esetben, ha az alkalmazás az üzenet feldolgozását követően összeomlik, de a `deleteMessage` kérelem kiadása előtt, akkor az üzenet az újraindításkor visszakerül az alkalmazásba. Ezt gyakran nevezik *legalább egyszer* feldolgozásra; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyv nem tudja elviselni a duplikált feldolgozást, akkor további logikát adhat hozzá az alkalmazásokhoz az ismétlődő üzenetek kézbesítésének kezeléséhez. Ez gyakran az `getMessageId` üzenet metódusának használatával érhető el, amely állandó marad a kézbesítési kísérletek között.

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte Service Bus várólisták alapjait, további információt a [várólisták, témakörök és előfizetések][Queues, topics, and subscriptions] című témakörben talál.

További információért látogasson el a [php fejlesztői központba](https://azure.microsoft.com/develop/php/)is.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


