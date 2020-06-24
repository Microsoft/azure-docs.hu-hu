---
title: A üzenetsor-tárolás használata a PHP-Azure Storage-ból
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue Storage szolgáltatással, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket. A minták PHP-ben íródnak.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: aefd03b9d0ce726e086dff96a648e5f3a6b28e6e
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809201"
---
# <a name="how-to-use-queue-storage-from-php"></a>How to use Queue storage from PHP (A Queue Storage használata PHP-val)

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure üzenetsor-tárolási szolgáltatás használatával. A mintákat a [PHP-hez készült Azure Storage ügyféloldali kódtár][download]osztályain keresztül írták. A kezelt forgatókönyvek közé tartozik például a várólista-üzenetek beszúrása, bepillantása, beolvasása és törlése, valamint a várólisták létrehozása és törlése.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása

Az Azure üzenetsor-tárolóhoz hozzáférő PHP-alkalmazások létrehozásának egyetlen követelménye az, hogy az [Azure Storage ügyféloldali kódtáraban][download] található osztályokra hivatkozik a PHP-n belül a kódból. Az alkalmazás létrehozásához bármilyen fejlesztői eszközt (pl. Jegyzettömböt) használhat.

Ebben az útmutatóban a üzenetsor-tárolási szolgáltatás olyan funkcióit használja, amelyek a PHP-alkalmazásokban helyileg, vagy egy Azure-beli webalkalmazásban futó kódban hívhatók.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél kódtárainak beszerzése

### <a name="install-via-composer"></a>Telepítés a Zeneszerzőn keresztül

1. Hozzon létre egy **composer.js** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```
2. A **[zeneszerző. farmakovigilancia][composer-phar]** letöltése a projekt gyökerében.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökerében
   
    ```
    php composer.phar install
    ```

Alternatív megoldásként nyissa meg az [Azure Storage php ügyféloldali kódtár][download] a githubon a forráskód klónozásához.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez

Az Azure üzenetsor-tároláshoz szükséges API-k használatához a következőket kell tennie:

1. Hivatkozzon az automatikus betöltő fájlra a [require_once] utasítás használatával.
2. Hivatkozzon az esetlegesen használt osztályokra.

Az alábbi példa azt mutatja be, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozni a **QueueRestProxy** osztályra.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Az alábbi példákban az `require_once` utasítás mindig látható, de csak a végrehajtáshoz szükséges osztályok vannak hivatkozva.

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage-beli kapcsolatok beállítása

Azure üzenetsor-tárolási ügyfél létrehozásához először érvényes kapcsolódási karakterláncot kell megadni. A várólista-szolgáltatás összekapcsolási karakterláncának formátuma a következő.

Élő szolgáltatás eléréséhez:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Az Emulator-tároló eléréséhez:

```php
UseDevelopmentStorage=true
```

Azure Queue szolgáltatás-ügyfél létrehozásához a **QueueRestProxy** osztályt kell használnia. A következő módszerek egyikét használhatja:

* Adja át közvetlenül a kapcsolódási karakterláncot.
* A webalkalmazásban környezeti változók használatával tárolhatja a kapcsolati karakterláncot. A kapcsolati karakterláncok konfigurálásához tekintse meg az [Azure-webalkalmazás konfigurációs beállításainak](../../app-service/configure-common.md) dokumentumát.
Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A **QueueRestProxy** objektum lehetővé teszi, hogy a **createQueue** metódus használatával hozzon létre egy várólistát. Várólista létrehozásakor megadhatja a várólista beállításait, de erre nincs szükség. (Az alábbi példa bemutatja, hogyan állíthatja be a metaadatokat egy várólistán.)

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
> 
> 

## <a name="add-a-message-to-a-queue"></a>Üzenet hozzáadása egy várólistához

Ha üzenetet szeretne hozzáadni egy várólistához, használja a **QueueRestProxy->createMessage**. A metódus az üzenetsor nevét, az üzenet szövegét és az üzenet beállításait (amelyek nem kötelezőek).

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
    $queueClient->createMessage("myqueue", "Hello World!");
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

A várólista elején megtekintheti az üzenetet (vagy üzeneteket) anélkül, hogy el kellene távolítania azt a sorból a **QueueRestProxy->peekMessages**meghívásával. Alapértelmezés szerint a **peekMessage** metódus egyetlen üzenetet ad vissza, de a **PeekMessagesOptions->setNumberOfMessages** metódus használatával módosíthatja ezt az értéket.

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

A kód két lépésben eltávolít egy üzenetet egy várólistából. Először hívja meg a **QueueRestProxy->listMessages**, amely az üzenetet a várólistából beolvasott bármely más kód számára láthatatlanná teszi. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. (Ha az üzenet nem törlődik ebben az időszakban, ismét láthatóvá válik a várólistán.) Az üzenet várólistából való eltávolításának befejezéséhez hívnia kell a **QueueRestProxy->deleteMessage**. Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód a **deleteMessage** közvetlenül az üzenet feldolgozása után hívja meg.

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

A várólistán található üzenet tartalmát a **QueueRestProxy->updateMessage**meghívásával módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. A következő kód új tartalommal frissíti az üzenetsor-üzenetet, és beállítja a láthatósági időtúllépést, hogy kiterjesszen egy újabb 60 másodpercet. Ezzel elmenti az üzenethez társított munkahelyi állapotot, és egy percet ad az ügyfélnek, hogy folytassa a munkát az üzeneten. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

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

## <a name="additional-options-for-de-queuing-messages"></a>További beállítások az üzenetsor-kezelő üzenetekhez

Az üzenetek várólistából való lekérését kétféleképpen lehet testreszabni. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. Másodszor beállíthatja a hosszabb vagy rövidebb láthatósági időtúllépést, így a kód több vagy kevesebb időt vehet igénybe az egyes üzenetek teljes feldolgozásához. A következő kódrészlet a **getMessages** metódus használatával 16 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza a **for** hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

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

Megbecsülheti egy üzenetsorban található üzenetek számát. A **QueueRestProxy->getQueueMetadata** metódus arra kéri a várólista-szolgáltatást, hogy a várólistára vonatkozó metaadatokat ad vissza. A visszaadott objektum **getApproximateMessageCount** metódusának meghívása megadja, hogy hány üzenet van egy várólistában. A darabszám csak hozzávetőleges, mert az üzenetsor-szolgáltatás a kérésre való reagálás után üzeneteket adhat hozzá vagy távolíthat el.

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

Ha törölni szeretne egy várólistát és a benne lévő összes üzenetet, hívja meg a **QueueRestProxy->deleteQueue** metódust.

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

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure üzenetsor-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat:

* Az [Azure Storage php ügyféloldali kódtár API-referenciájának](https://azure.github.io/azure-storage-php/) felkeresése
* Lásd a [speciális üzenetsor példáját](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

További információ: a [php fejlesztői központ](https://azure.microsoft.com/develop/php/)is.

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
