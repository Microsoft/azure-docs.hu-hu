---
title: Üzenetsorok Azure Service Bus Használata Javával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre Java-alkalmazásokat, amelyek üzeneteket küldenek egy üzenetsorba, és üzeneteket Azure Service Bus az üzenetsorból.
ms.date: 06/23/2020
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 38657f333fea4ca18fb76eb5832649067bfd01b4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538250"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Rövid útmutató: Üzenetsorok Azure Service Bus használata Javával üzenetek küldése és fogadása

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Ez az oktatóanyag bemutatja, hogyan hozhat létre Java-alkalmazásokat, amelyek üzeneteket küldenek egy üzenetsorba, és üzeneteket Azure Service Bus az üzenetsorból. 

> [!WARNING]
>  Ez a rövid útmutató a régi `azure-servicebus` csomagokat használja. A legújabb csomagot használó rövid útmutatóért `azure-messaging-servicebus` lásd: [Üzenetek küldése és fogadása a `azure-messaging-servicebus` használatával. ](service-bus-java-how-to-use-queues.md) 


## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [MSDN-előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Ha nincs használható üzenetsora, kövesse a Use [Azure Portal to create a Service Bus queue](service-bus-quickstart-portal.md) (Üzenetsor létrehozása üzenetsor létrehozásához) cikk lépéseit az üzenetsor létrehozásához.
    1. Olvassa el a következő **üzenetsorok** Service Bus **áttekintését:**. 
    2. Hozzon létre egy Service Bus **névteret.** 
    3. Szerezze be **a kapcsolati sztringet.**
    4. Hozzon létre egy Service Bus **üzenetsort.**
3. Telepítse a [Javához készült Azure SDK-t.][Azure SDK for Java] 


## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus
A minta kiépítése előtt győződjön meg arról, hogy telepítette a [Javához][Azure SDK for Java] készült Azure SDK-t. 

Ha Eclipse-et használ, telepítheti a javához készült Azure [SDK Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] tartalmazó alkalmazást. Ezután hozzáadhatja a **Microsoft Azure Kódtárakat a** projekthez. IntelliJ használata esetén lásd: [Install the Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 

![Java Microsoft Azure kódtárak hozzáadása az Eclipse-projekthez](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Adja hozzá a következő `import` utasításokat a Java-fájl tetejéhez:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ha üzeneteket küld egy Service Bus-üzenetsorba, az alkalmazás példányosítani fog egy **QueueClient** objektumot, és aszinkron módon küld üzeneteket. Az alábbi kód bemutatja, hogyan küldhet üzenetet a portálon keresztül létrehozott üzenetsorhoz.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Az üzenetsorba küldött és onnan fogadott Service Bus üzenetsorok az [Üzenet osztály példányai.](/java/api/com.microsoft.azure.servicebus.message) Az üzenetobjektumok szabványos tulajdonságokkal (például Label és TimeToLive) rendelkeznek, egy egyéni alkalmazásspecifikus tulajdonságokat és tetszőleges alkalmazásadatok törzsét tárak. Az alkalmazás úgy állíthatja be az üzenet törzsét, hogy bármilyen szerializálható objektumot ad át az üzenet konstruktorának, majd a megfelelő szerializálóval szerializálja az objektumot. Másik lehetőségként java-t is **meg lehet adni. Io. InputStream** objektum.


A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása üzenetsorból
Az üzenetek üzenetsorból való fogadásának elsődleges módja egy **ServiceBusContract objektum** használata. A fogadott üzenetek két különböző módban működnek: **ReceiveAndDelete** és **PeekLock**.

A **ReceiveAndDelete** módban a fogadás egy egyszeri művelet – azaz amikor a Service Bus olvasási kérést kap egy üzenetsorban lévő üzenetre, az üzenetet felhasználtként jelöli meg, és visszaadja az alkalmazásnak. **A ReceiveAndDelete** mód (ez az alapértelmezett mód) a legegyszerűbb modell, és az olyan forgatókönyvekhez működik a legjobban, amelyekben az alkalmazás hiba esetén nem képes feldolgozni az üzeneteket. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel Service Bus az üzenetet felhasználtként jelölte meg, akkor amikor az alkalmazás újraindul, és ismét elkezdi az üzenetek fogadását, kihagyta az összeomlás előtt felhasznált üzenetet.

**PeekLock módban** a fogadás kétszakaszos művelet lesz, ami lehetővé teszi a hiányzó üzeneteket nem tűrő alkalmazások támogatását. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a **complete()** hívásával a fogadott üzeneten. Amikor Service Bus **complete()** hívást lát, az felhasználtként jelöli meg az üzenetet, és eltávolítja az üzenetsorból. 

Az alábbi példa bemutatja, hogyan lehet üzeneteket fogadni és feldolgozni **PeekLock** módban (nem az alapértelmezett módban). Az alábbi példa a visszahívási modellt használja egy regisztrált üzenetkezelővel, és feldolgozza az üzeneteket, amint megérkeznek `TestQueue` a-be. Ez a mód automatikusan hívja meg **a complete()** függvényt, amikor a visszahívás a szokásos módon tér vissza, és az **abandon()** függvényt hívja meg, ha a visszahívás kivételt ad vissza. 

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
            // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadóalkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meg tudja hívni az **abandon()** metódust az ügyfélobjektumon a kapott üzenet **getLockToken()** használatával kapott zárolási jogkivonatával. Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett az üzenetsorban zárolt üzenethez időtúllépés is tartozik, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időkorlát lejárta előtt (például ha az alkalmazás összeomlik), akkor az Service Bus automatikusan feloldja az üzenet zárolását, és elérhetővé teszi azt, hogy ismét megkapható legyen.

Ha az alkalmazás összeomlik az üzenet feldolgozása után, de még a **complete()** kérés kibocsátása előtt, az üzenet újra kézbesítve lesz az alkalmazásnak az újraindításkor. Ezt gyakran legalább *egyszeres feldolgozásnak nevezik;* ez azt jelenti, hogy a rendszer minden üzenetet legalább egyszer feldolgoz, de bizonyos esetekben előfordulhat, hogy ugyanazt az üzenetet újraküldi a rendszer. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet **getMessageId** metódusával érhető el, amely a kézbesítési kísérletek során állandó marad.

> [!NOTE]
> Az erőforrás-Service Bus a [Service Bus Explorer.](https://github.com/paolosalvatori/ServiceBusExplorer/) A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy egy Service Bus névtérhez csatlakozzon, és könnyen felügyelik az üzenetküldési entitásokat. Az eszköz olyan speciális funkciókat biztosít, mint az importálási/exportálási funkciók, vagy a témakör, az üzenetsorok, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelési képessége. 

## <a name="next-steps"></a>Következő lépések
A Java-mintákat a GitHubon, a [ `azure-service-bus` adattárban találja.](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
