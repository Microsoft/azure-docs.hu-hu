---
title: A Queue Storage használata a PHP-ben – Azure Storage
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue Storage szolgáltatással, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket. A minták PHP-ben íródnak.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592024"
---
# <a name="how-to-use-queue-storage-from-php"></a>A Queue Storage használata PHP-ból

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Queue Storage szolgáltatás használatával. A mintákat a [PHP-hez készült Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-php)osztályain keresztül írták. A kezelt forgatókönyvek közé tartozik például a várólista-üzenetek beszúrása, bepillantása, beolvasása és törlése, valamint a várólisták létrehozása és törlése.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása

Az Azure Queue Storagehoz hozzáférő PHP-alkalmazások létrehozásának egyetlen követelménye az [Azure Storage ügyféloldali kódtára a PHP-hez](https://github.com/Azure/azure-storage-php) való hivatkozása a kódból. Az alkalmazás létrehozásához bármilyen fejlesztői eszközt (pl. Jegyzettömböt) használhat.

Ebben az útmutatóban a Queue Storage szolgáltatási funkcióit használhatja, amelyek a PHP-alkalmazásokban helyileg, vagy egy Azure-beli webalkalmazásban futó kódban hívhatók.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél kódtárainak beszerzése

### <a name="install-via-composer"></a>Telepítés a zeneszerzőn keresztül

1. Hozzon létre egy nevű fájlt `composer.json` a projekt gyökérkönyvtárában, és adja hozzá a következő kódot:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Töltse le a [`composer.phar`](https://getcomposer.org/composer.phar) projekt gyökerében.

3. Nyisson meg egy parancssort, és futtassa a következő parancsot a projekt gyökerében:

    ```console
    php composer.phar install
    ```

Alternatív megoldásként nyissa meg az [Azure Storage php ügyféloldali kódtár](https://github.com/Azure/azure-storage-php) a githubon a forráskód klónozásához.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása Queue Storage eléréséhez

Az Azure Queue Storage API-k használatához a következőket kell tennie:

1. Hivatkozzon az automatikus betöltő fájlra az utasítás használatával [`require_once`](https://www.php.net/manual/en/function.require-once.php) .
2. Hivatkozzon az esetlegesen használt osztályokra.

Az alábbi példa azt mutatja be, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozni az `QueueRestProxy` osztályra.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Az alábbi példákban az `require_once` utasítás mindig megjelenik, de csak a példa futtatásához szükséges osztályok hivatkoznak rá.

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage-beli kapcsolatok beállítása

Azure Queue Storage-ügyfél létrehozásához először érvényes kapcsolódási karakterláncot kell megadni. A Queue Storage-kapcsolatok karakterláncának formátuma a következő.

Élő szolgáltatás eléréséhez:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Az Emulator-tároló eléréséhez:

```php
UseDevelopmentStorage=true
```

Azure Queue Storage-ügyfél létrehozásához a osztályt kell használnia `QueueRestProxy` . A következő módszerek egyikét használhatja:

- Adja át közvetlenül a kapcsolódási karakterláncot.
- A webalkalmazásban környezeti változók használatával tárolhatja a kapcsolati karakterláncot. A kapcsolati karakterláncok konfigurálásához tekintse meg az [Azure-webalkalmazás konfigurációs beállításainak](../../app-service/configure-common.md) dokumentumát.

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Egy `QueueRestProxy` objektum lehetővé teszi, hogy a metódus használatával hozzon létre egy várólistát `CreateQueue` . Várólista létrehozásakor megadhatja a várólista beállításait, de erre nincs szükség. Ebből a példából megtudhatja, hogyan állíthatja be a metaadatokat egy várólistán.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> A metaadat-kulcsok esetében ne használjon kis-és nagybetűket. A rendszer minden kulcsot kisbetűsen olvas be a szolgáltatásból.

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Ha üzenetet szeretne hozzáadni egy várólistához, használja a következőt: `QueueRestProxy->createMessage` . A metódus az üzenetsor nevét, az üzenet szövegét és az üzenet beállításait (amelyek nem kötelezőek).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

A várólista elején lévő egy vagy több üzenetet betekintést nyerhet anélkül, hogy meghívja őket `QueueRestProxy->peekMessages` . Alapértelmezés szerint a `peekMessage` metódus egyetlen üzenetet ad vissza, de a metódus használatával módosíthatja ezt az értéket `PeekMessagesOptions->setNumberOfMessages` .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>A következő üzenet kivétele az üzenetsorból

A kód két lépésben eltávolít egy üzenetet egy várólistából. Először hívja meg `QueueRestProxy->listMessages` a-t, amely az üzenetet a várólistából beolvasott bármely más kód számára láthatatlanná teszi. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. (Ha az üzenet nem törlődik ebben az időszakban, ismét láthatóvá válik a várólistán.) Az üzenet várólistából való eltávolításának befejezéséhez hívnia kell a következőt: `QueueRestProxy->deleteMessage` . Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód meghívása `deleteMessage` közvetlenül az üzenet feldolgozása után történik.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

A várólistán lévő üzenet tartalmának megváltoztatásához hívja meg a következőt: `QueueRestProxy->updateMessage` . Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. A következő kód új tartalommal frissíti az üzenetsor-üzenetet, és beállítja a láthatósági időtúllépést, hogy kiterjesszen egy újabb 60 másodpercet. Ezzel elmenti az üzenethez társított munkahelyi állapotot, és egy percet ad az ügyfélnek, hogy folytassa a munkát az üzeneten. Ezzel a technikával nyomon követheti a várólista-üzenetek többlépéses munkafolyamatait anélkül, hogy az elejétől kellene kezdenie, ha a feldolgozási lépés hardverhiba vagy szoftver meghibásodása miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások a dequeuing üzenetek számára

Az üzenetek várólistából való lekérését kétféleképpen lehet testreszabni. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. Másodszor beállíthatja a hosszabb vagy rövidebb láthatósági időtúllépést, így a kód több vagy kevesebb időt vehet igénybe az egyes üzenetek teljes feldolgozásához. A következő kódrészlet a `getMessages` metódus használatával 16 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `for` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Várólista hosszának beolvasása

Megbecsülheti egy üzenetsorban található üzenetek számát. A `QueueRestProxy->getQueueMetadata` metódus a várólistához tartozó metaadatokat kérdezi le. Ha meghívja a `getApproximateMessageCount` metódust a visszaadott objektumon, a szám azt adja meg, hogy hány üzenet van egy várólistában. A darabszám csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután Queue Storage válaszol a kérelemre.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Ha törölni szeretne egy várólistát és a benne lévő összes üzenetet, hívja meg a `QueueRestProxy->deleteQueue` metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az Azure Queue Storage alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat:

- Az [Azure Storage php ügyféloldali kódtár API-referenciájának](https://azure.github.io/azure-storage-php/) felkeresése
- Lásd a [speciális üzenetsor példáját](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

További információ: [php fejlesztői központ](https://azure.microsoft.com/develop/php/).
