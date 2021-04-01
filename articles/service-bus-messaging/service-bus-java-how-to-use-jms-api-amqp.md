---
title: A AMQP használata a Java Message Service API-val és Azure Service Bus
description: Használja a Java Message Service (JMS) szolgáltatást a Azure Service Bus és a Advanced Message Queueing Protocol (AMQP) 1,0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90086691"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>A Java-Üzenetkezelő szolgáltatás használata a Azure Service Bus és a AMQP 1,0

> [!WARNING]
> Ez a cikk a Java Message Service (JMS) 1,1 API *korlátozott támogatását* nyújtja, és csak a standard szintű Azure Service Bus esetében létezik.
>
> A Java Message Service 2,0 API teljes körű támogatása csak az előzetes verzióban elérhető [prémium szintű Azure Service Bus](how-to-use-java-message-service-20.md)érhető el. Javasoljuk, hogy ezt a szintet használja.
>

Ez a cikk azt ismerteti, hogyan használhatók a Java-alkalmazások Service Bus üzenetkezelési funkciói a népszerű JMS API standard használatával. Ezek az üzenetkezelési funkciók a várólistákat és a közzétételt, illetve a témakörökre való feliratkozást tartalmazzák. A [cikk](service-bus-amqp-dotnet.md) bemutatja, hogyan végezheti el ugyanezt a Azure Service Bus .NET API használatával. A két cikk együttes használatával a Advanced Message Queueing Protocol (AMQP) 1,0 használatával megismerheti a platformok közötti üzenetkezelést.

A AMQP 1,0 egy hatékony, megbízható, vezeték nélküli üzenetkezelési protokoll, amellyel robusztus, platformfüggetlen üzenetkezelési alkalmazások hozhatók létre.

A AMQP 1,0-es verziójának támogatása a Service Bus azt jelenti, hogy egy hatékony bináris protokoll használatával használhatja a Queuing szolgáltatást, és közzéteheti a felügyelt üzenetkezelési funkciókat számos platformon. Létrehozhat olyan alkalmazásokat is, amelyek a különböző nyelvek, keretrendszerek és operációs rendszerek együttes használatával készített összetevőkből állnak.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései

Ez a cikk azt feltételezi, hogy már rendelkezik egy Service Bus névtérrel, amely egy nevű várólistát tartalmaz `basicqueue` . Ha nem, akkor a [névtér és a várólista](service-bus-create-namespace-portal.md) a [Azure Portal](https://portal.azure.com)használatával hozható létre. Service Bus névterek és várólisták létrehozásával kapcsolatos további információkért lásd: [Service Bus Queues – első lépések](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> A particionált várólisták és témakörök szintén támogatják a AMQP. További információ: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [AMQP 1,0 támogatás Service Bus particionált várólistákhoz és témakörökhöz](./service-bus-amqp-protocol-guide.md).
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>Töltse le a AMQP 1,0 JMS ügyféloldali függvénytárát

Az Apache csontos JMS AMQP 1,0 ügyféloldali kódtár legújabb verziójának letöltéséről az [Apache csontos letöltési webhelyén](https://qpid.apache.org/download.html)tájékozódhat.

A következő JAR-fájlokat hozzá kell adnia az Apache csontos JMS AMQP 1,0 Distribution Archive-ből a Java OSZTÁLYÚTVONAL környezeti változóhoz, amikor JMS-alkalmazásokat hoz létre és futtat a Service Bus használatával:

* Geronimo-JMS \_ 1,1 \_ Spec-1.0. jar
* Csontos-JMS-Client-[Version]. jar

> [!NOTE]
> Lehetséges, hogy a JMS JAR-nevek és-verziók megváltoztak. További információ: [csontos JMS AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="code-java-applications"></a>Java-alkalmazások kódolása

### <a name="java-naming-and-directory-interface"></a>Java elnevezési és könyvtári felület

A JMS a Java elnevezési és könyvtári felületet (JNDI) használja a logikai nevek és a fizikai nevek közötti elkülönítés létrehozásához. A JNDI: **ConnectionFactory** és a **Destination** típusú JMS objektumok két típusát oldják fel. A JNDI egy szolgáltatói modellt használ, amelyben különböző címtárszolgáltatás-szolgáltatásokat lehet csatlakoztatni a névfeloldási feladatok kezeléséhez. Az Apache csontos JMS AMQP 1,0 Library egy egyszerű, file-alapú JNDI-szolgáltatót tartalmaz, amely a következő formátumú tulajdonságok használatával van konfigurálva:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>JNDI-környezet beállítása és a ConnectionFactory objektum konfigurálása

A hivatkozott kapcsolati sztring az **elsődleges kapcsolati sztringben** lévő [Azure Portal](https://portal.azure.com) megosztott hozzáférési házirendekben elérhető.

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>A gyártó és a fogyasztói cél várólisták konfigurálása

A csontos tulajdonságok fájljának JNDI-szolgáltatójában a célhely definiálásához használt bejegyzés formátuma a következő:.

Cél várólista létrehozása a gyártó számára:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

Célvárólista létrehozása a fogyasztó számára:
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása

A JMS és a Service Bus használatával nem szükséges különleges API-k vagy beállítások. A későbbiekben néhány korlátozás vonatkozik rájuk. Ahogy a JMS-alkalmazásokhoz hasonlóan, az első szükséges, hogy a JNDI-környezet úgy legyen feloldva, hogy fel lehessen oldani egy **ConnectionFactory** objektumot és célhelyet.

#### <a name="configure-the-jndi-initialcontext-object"></a>A JNDI InitialContext objektumának konfigurálása

A JNDI környezet úgy van konfigurálva, hogy a konfigurációs adatok kivonatoló táblázatát átadja a javax.naming.InitialContext osztály konstruktorának. A kivonatoló tábla két szükséges eleme a kezdeti környezeti gyár és a szolgáltató URL-címe. A következő kód bemutatja, hogyan konfigurálhatja a JNDI környezetet úgy, hogy a csontos Properties fájl-alapú JNDI-szolgáltatót használja a **servicebus. properties** nevű tulajdonságokkal.

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>Egy Service Bus-várólistát használó egyszerű JMS-alkalmazás

Az alábbi példában szereplő program szöveges üzeneteket küld egy Service Bus üzenetsor számára a várólista logikai JNDI, és visszafogadja az üzeneteket a JMS.

Az összes forráskódot és konfigurációs információt elérheti a [Azure Service Bus Samples JMS-várólista](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)rövid útmutatójában.

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Adja át a **kapcsolati karakterláncot** a megosztott hozzáférési szabályzatokból az alkalmazás futtatásához.
A következő kimenet az alkalmazást futtató űrlap:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-elrendezés és Service Bus művelet-hozzárendelés

A AMQP-elrendezés egy Service Bus műveletre fordítható le:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS témakörök és Service Bus témakörök

Service Bus témakörök és előfizetések használata a JMS API-n keresztül alapvető küldési és fogadási képességeket biztosít. Kényelmes választás, ha más JMS-kompatibilis API-kkal rendelkező alkalmazásokat is más-más JMS, Service Bus de a témakörök eltérőek, és csak néhány módosítást igényelnek.

Service Bus témakörök üzeneteket továbbítanak az Azure Resource Management felületen, az Azure parancssori eszközein vagy a Azure Portal felügyelt, névvel ellátott, megosztott és tartós előfizetésekre. Az egyes előfizetések legfeljebb 2 000 kiválasztási szabályt tesznek lehetővé, amelyek mindegyike tartalmaz egy szűrési feltételt, és az SQL-szűrők esetében metaadat-átalakítási műveletet is végezhet. Az egyes Szűrőfeltételek egyeztetése kiválasztja az előfizetésbe másolandó bemeneti üzenetet.  

Az előfizetésből érkező üzenetek fogadása azonos a várólistákból érkező üzenetek fogadásával. Minden előfizetéshez tartozik egy kézbesítetlen levelek várólistája, valamint az üzenetek automatikus továbbítása egy másik várólistába vagy témakörbe.

A JMS témakörök lehetővé teszik, hogy az ügyfelek dinamikusan hozzanak létre nem tartós és tartós előfizetőket, amelyek opcionálisan lehetővé teszik a Message választókkal való szűrést. A Service Bus nem támogatja ezeket a nem megosztott entitásokat. Az Service Bus SQL-szűrési szabályának szintaxisa hasonló a JMS által támogatott üzenet-választói szintaxishoz.

A JMS témakör közzétevő oldala kompatibilis a Service Busekkel, ahogy az ebben a mintában látható, de a dinamikus előfizetők nem. A következő topológia-alapú JMS API-k nem támogatottak a Service Bus.

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott funkciók és korlátozások

A következő korlátozások léteznek, ha a JMS-t használja a AMQP 1,0-nél több mint Service Bus, nevezetesen:

* Egy munkamenetben csak egy **MessageProducer** vagy **MessageConsumer** objektum engedélyezett. Ha több **MessageProducer** vagy **MessageConsumer** objektumot kell létrehoznia egy alkalmazásban, hozzon létre egy dedikált munkamenetet mindegyikhez.
* A felejtő témakör-előfizetések jelenleg nem támogatottak.
* A **MessageSelector** objektumok jelenleg nem támogatottak.
* Az elosztott tranzakciók nem támogatottak, de a tranzakciós munkamenetek támogatottak.

Service Bus feldarabolja a vezérlő síkot az adatsíkon, így nem támogatja több JMS dinamikus topológiai funkcióját.

| Nem támogatott metódus          | Csere erre                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Hozzon létre egy témakör-előfizetést, amely az üzenet választóját adja meg.                                |
| createDurableConsumer       | Hozzon létre egy témakör-előfizetést, amely az üzenet választóját adja meg.                                |
| createSharedConsumer        | Service Bus témakörök mindig megoszthatók. Tekintse meg a "JMS témakörök és Service Bus témakörök" című szakaszt.                                    |
| createSharedDurableConsumer | Service Bus témakörök mindig megoszthatók. Tekintse meg a "JMS témakörök és Service Bus témakörök" című szakaszt.                                      |
| createTemporaryTopic        | Hozzon létre egy témakört a Management API, az eszközök vagy a portál *AutoDeleteOnIdle* , amely lejárati időszakra van beállítva. |
| createTopic                 | Hozzon létre egy témakört a felügyeleti API, eszközök vagy a portál használatával.                                         |
| Leiratkozás                 | Törölje a témakör-felügyeleti API-t, eszközöket vagy portált.                                            |
| createBrowser               | Támogatott. Használja a Service Bus API betekintés () funkcióját.                         |
| createQueue                 | Hozzon létre egy várólistát a felügyeleti API, eszközök vagy a portál használatával.                                           | 
| createTemporaryQueue        | Hozzon létre egy várólistát a felügyeleti API, az eszközök vagy a portál használatával, amely lejárati időszakra van beállítva a *AutoDeleteOnIdle* . |
| receiveNoWait               | Használja a Service Bus SDK által biztosított Receive () metódust, és nagyon alacsony vagy nulla időtúllépést ad meg. |

## <a name="summary"></a>Összefoglalás

Ez a cikk bemutatja, hogyan használhatja Service Bus felügyelt üzenetkezelési funkciókat, például a várólistákat és a közzétételi vagy előfizetési témákat a Java-ból a népszerű JMS API és a AMQP 1,0 használatával.

Más nyelvekről is használhat Service Bus AMQP 1,0, például .NET, C, Python és PHP. Az ezekkel a különböző nyelvekkel létrehozott összetevők megbízhatóan és teljes hűséggel cserélhetik az üzeneteket a Service Bus AMQP 1,0-támogatásának használatával.

## <a name="next-steps"></a>Következő lépések

* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [A AMQP 1,0 használata a Service Bus .NET API-val](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)