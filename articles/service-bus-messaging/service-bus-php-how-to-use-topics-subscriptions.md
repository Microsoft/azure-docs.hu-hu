---
title: A PHP-vel Azure Service Bus témák használata
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatók Azure Service Bus témakörök és előfizetések egy PHP-alkalmazásból.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88066202"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Gyors útmutató: Service Bus témakörök és előfizetések használata PHP-vel

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk bemutatja, hogyan használhatja Service Bus témaköröket és előfizetéseket. A minták PHP-ben íródnak, és a [PHP-hez készült Azure SDK](https://github.com/Azure/azure-sdk-for-php)-t használják. A tárgyalt forgatókönyvek a következők:

- Témakörök és előfizetések létrehozása 
- Előfizetési szűrők létrehozása 
- Üzenetek küldése egy témakörnek 
- Üzenetek fogadása egy előfizetésből
- Témakörök és előfizetések törlése

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [Visual Studio-vagy MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Kövesse a rövid útmutató lépéseit [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörhöz](service-bus-quickstart-topics-subscriptions-portal.md) , és hozzon létre egy Service Bus **névteret** , és szerezze be a **kapcsolódási karakterláncot**.

    > [!NOTE]
    > Ebben a rövid útmutatóban egy **témakört** és egy **előfizetést** hoz létre a témakörhöz a **php** használatával. 

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
Az Azure Blob servicehoz hozzáférő PHP-alkalmazások létrehozásának egyetlen követelménye, hogy a programkódon belül a [PHP-hez készült Azure SDK](https://github.com/Azure/azure-sdk-for-php) -ban található osztályokra hivatkozzon. Az alkalmazás vagy a Jegyzettömb létrehozásához bármilyen fejlesztői eszközt használhat.

> [!NOTE]
> A PHP-telepítésnek is telepítve és engedélyezve kell lennie az [OpenSSL bővítménnyel](https://php.net/openssl) .
> 
> 

Ez a cikk azt ismerteti, hogyan használhatók olyan szolgáltatási funkciók, amelyek a PHP-alkalmazásokban helyileg, vagy egy Azure-beli webes szerepkörön, feldolgozói szerepkörben vagy webhelyen futó kódban is meghívhatók.

## <a name="get-the-azure-client-libraries"></a>Az Azure-ügyfél kódtárainak beszerzése

### <a name="install-via-composer"></a>Telepítés a Zeneszerzőn keresztül
1. Hozzon létre egy **composer.js** nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Töltse le a **[zeneszerző. farmakovigilancia] [zeneszerző-farmakovigilancia]** részt a projekt gyökerében.
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökerében
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
A Service Bus API-k használata:

1. Hivatkozzon az automatikus betöltő fájlra a [require_once][require-once] utasítás használatával.
2. Hivatkozzon az esetlegesen használt osztályokra.

Az alábbi példa azt mutatja be, hogyan lehet felvenni az automatikus betöltő fájlt, és hivatkozni a **ServiceBusService** osztályra.

> [!NOTE]
> Ez a példa (és a cikkben szereplő további példák) feltételezi, hogy telepítette az Azure-hoz készült PHP ügyféloldali kódtárakat a zeneszerző használatával. Ha manuálisan vagy PEAR-csomagként telepítette a kódtárakat, a **WindowsAzure. php** automatikus betöltő fájlra kell hivatkoznia.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban az `require_once` utasítás mindig látható, de csak a végrehajtáshoz szükséges osztályok vannak hivatkozva.

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolatok beállítása
Service Bus-ügyfél létrehozásához a következő formátumban kell érvényes kapcsolódási karakterláncot tartalmaznia:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Ahol `Endpoint` általában a formátuma `https://[yourNamespace].servicebus.windows.net` .

Bármely Azure-szolgáltatásbeli ügyfél létrehozásához a osztályt kell használnia `ServicesBuilder` . A következőket teheti:

* Adja át közvetlenül a kapcsolódási karakterláncot.
* A **CloudConfigurationManager (CCM)** segítségével több külső forrást is megvizsgálhat a kapcsolódási karakterlánchoz:
  * Alapértelmezés szerint a szolgáltatás egy külső forrás-környezeti változó támogatásával jár.
  * Új forrásokat a `ConnectionStringSource` osztály kiterjesztésével adhat hozzá.

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Service Bus témakörökhöz a osztályon keresztül végezhet felügyeleti műveleteket `ServiceBusRestProxy` . Egy `ServiceBusRestProxy` objektum a `ServicesBuilder::createServiceBusService` gyári metódussal lett létrehozva egy megfelelő kapcsolati karakterlánccal, amely magában foglalja a jogkivonat-engedélyeket a kezeléséhez.

Az alábbi példa bemutatja, hogyan `ServiceBusRestProxy` `ServiceBusRestProxy->createTopic` hozhat létre és hívhat meg egy `mytopic` névtéren belüli témakört `MySBNamespace` :

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Az `listTopics` objektumok metódusával ellenőrizhető, `ServiceBusRestProxy` hogy már létezik-e egy adott nevű témakör a szolgáltatási névtérben.
> 
> 

## <a name="create-a-subscription"></a>Előfizetés létrehozása
Témakör-előfizetések is létrejönnek a `ServiceBusRestProxy->createSubscription` metódussal. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs megadva szűrő az új előfizetés létrehozásakor, a rendszer a **MatchAll** szűrőt (alapértelmezett) használja. A **MatchAll** szűrő használatakor a témakörben közzétett összes üzenet az előfizetés virtuális várólistáján lesz elhelyezve. A következő példa létrehoz egy nevű előfizetést `mysubscription` , és az alapértelmezett **MatchAll** szűrőt használja.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Beállíthat szűrőket, amelyek lehetővé teszik annak meghatározását, hogy mely üzenetek jelenjenek meg egy adott üzenettémakör-előfizetésben. Az előfizetések által támogatott legrugalmasabb típusú szűrő a [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), amely a SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. További információ a SqlFilters: [SqlFilter. SqlExpression tulajdonság][sqlfilter].

> [!NOTE]
> Az előfizetéshez tartozó összes szabály egymástól függetlenül dolgozza fel a bejövő üzeneteket, az eredményül kapott üzeneteket hozzáadja az előfizetéshez. Emellett minden új előfizetéshez tartozik egy alapértelmezett **szabálykészlet** , amely egy olyan szűrőt tartalmaz, amely az összes üzenetet hozzáadja a témakörből az előfizetéshez. Ha csak a szűrőnek megfelelő üzeneteket szeretne fogadni, el kell távolítania az alapértelmezett szabályt. Az alapértelmezett szabályt a metódus használatával távolíthatja el `ServiceBusRestProxy->deleteRule` .
> 
> 

Az alábbi példa egy nevű előfizetést hoz létre `HighMessages` egy **SqlFilter** , amely csak a 3- `MessageNumber` nál nagyobb egyéni tulajdonságú üzeneteket jelöli ki. Az üzenetek küldésével kapcsolatos további információkért lásd: [üzenetek küldése egy témakörbe](#send-messages-to-a-topic) .

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

A kódnak további névteret kell használnia: `WindowsAzure\ServiceBus\Models\SubscriptionInfo` .

Hasonlóképpen, a következő példa létrehoz egy nevű előfizetést, `LowMessages` `SqlFilter` amely csak olyan üzeneteket jelöl ki, amelyek `MessageNumber` tulajdonsága kisebb vagy egyenlő, mint 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Most, amikor elküld egy üzenetet a `mytopic` témakörnek, a rendszer mindig kézbesíti az előfizetésre előfizetett fogadóknak `mysubscription` , és szelektíven kézbesíti azokat a fogadóknak, akik előfizetettek a `HighMessages` és az `LowMessages` előfizetésekre (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Ha üzenetet szeretne küldeni egy Service Bus témakörnek, az alkalmazás meghívja a `ServiceBusRestProxy->sendTopicMessage` metódust. A következő kód bemutatja, hogyan küldhet üzenetet a `mytopic` korábban a `MySBNamespace` szolgáltatási névtéren belül létrehozott témakörbe.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

A Service Bus-üzenettémakörbe küldött üzenetek a [BrokeredMessage][BrokeredMessage] osztály példányai. A [BrokeredMessage][BrokeredMessage] objektumok szabványos tulajdonságokkal és metódusokkal, valamint az egyedi alkalmazásspecifikus tulajdonságok tárolására használható tulajdonságokkal rendelkeznek. Az alábbi példa bemutatja, hogyan küldhet öt tesztüzenet a `mytopic` korábban létrehozott témakörbe. A `setProperty` metódus használatával egyéni tulajdonságot () adhat hozzá az `MessageNumber` egyes üzenetekhez. A `MessageNumber` tulajdonság értéke minden üzenetnél változó lehet (ezt az értéket használhatja az [előfizetés létrehozása](#create-a-subscription) szakaszban látható módon megállapítani, hogy mely előfizetések kapják meg):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör méretének felső korlátja 5 GB. További információ a kvótákkal kapcsolatban: [Service Bus kvóták][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Az előfizetésből érkező üzenetek fogadásának legjobb módja egy metódus használata `ServiceBusRestProxy->receiveSubscriptionMessage` . Az üzenetek két különböző módban is fogadhatók: [ *ReceiveAndDelete* és *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). A **PeekLock** az alapértelmezett érték.

A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy előfizetésben lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. A [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) * mód a legegyszerűbb modell, és a legjobban olyan helyzetekben működik, amikor egy alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel Service Bus az üzenetet felhasználva jelölte meg, akkor az alkalmazás újraindításakor és az üzenetek újbóli használatának megkezdése után a rendszer kihagyta az összeomlás előtt felhasznált üzenetet.

Az alapértelmezett [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban egy üzenet fogadása kétlépcsős művelet lesz, amely lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja azt a későbbi feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a kapott üzenet átadásával `ServiceBusRestProxy->deleteMessage` . Ha Service Bus látja a `deleteMessage` hívást, az üzenetet a rendszer felhasználja, és eltávolítja a várólistából.

Az alábbi példa bemutatja, hogyan fogadhat és dolgozhat fel egy üzenetet a [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód használatával (az alapértelmezett mód). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Útmutató: az alkalmazások összeomlásának és nem olvasható üzeneteinek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor a metódust meghívhatja a `unlockMessage` kapott üzeneten (a `deleteMessage` metódus helyett). Ennek hatására Service Bus az üzenet zárolásának feloldására, és elérhetővé tételére, hogy ugyanazt az alkalmazást vagy egy másik alkalmazást használó alkalmazás is megkapja.

A várólistán lévő üzenethez is tartozik időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi azt újra.

Abban az esetben, ha az alkalmazás az üzenet feldolgozása után összeomlik, de a `deleteMessage` kérelem kiadása előtt, akkor a rendszer az üzenetet az újraindításkor visszaküldi az alkalmazásnak. Ezt a fajta feldolgozást gyakran *legalább egyszer* kell meghívni a feldolgozásra; Ez azt eredményezi, hogy minden üzenet legalább egyszer fel van dolgozva, de bizonyos helyzetekben előfordulhat, hogy az üzenet újbóli kézbesítésre kerül. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, akkor az alkalmazások fejlesztőinek további logikát kell hozzáadniuk az alkalmazásokhoz, hogy kezelni tudják az üzenetek ismétlődő kézbesítését. Ez gyakran az `getMessageId` üzenet metódusának használatával érhető el, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Témakör vagy előfizetés törléséhez használja a `ServiceBusRestProxy->deleteTopic` vagy a `ServiceBusRestProxy->deleteSubscripton` metódust. Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli.

Az alábbi példa bemutatja, hogyan törölhet egy nevű témakört `mytopic` és a regisztrált előfizetéseit.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

A metódus használatával `deleteSubscription` egymástól függetlenül törölheti az előfizetéseket:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer/)kezelheti Service Bus erőforrásait. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez kapcsolódjanak, és egyszerű módon felügyelhetik az üzenetkezelési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkció, illetve a témakör, a várólisták, az előfizetések, a Relay-szolgáltatások, az értesítési központok és az események hubok. 

## <a name="next-steps"></a>Következő lépések
További információ: [várólisták, témakörök és előfizetések][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md