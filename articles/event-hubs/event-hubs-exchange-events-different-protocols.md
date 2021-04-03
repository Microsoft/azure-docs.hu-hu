---
title: Azure Event Hubs – különböző protokollokat használó Exchange-események
description: Ez a cikk bemutatja, hogy a különböző protokollokat (AMQP, Apache Kafka és HTTPS) használó felhasználók és gyártók Hogyan válthatnak be eseményeket az Azure Event Hubs használatakor.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: cbc6999e3ede73b948ce034769966922b4b0f282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010319"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>A különböző protokollokat használó fogyasztók és gyártók közötti Exchange-események: AMQP, Kafka és HTTPS
Az Azure Event Hubs három protokollt támogat a felhasználók és a termelők számára: AMQP, Kafka és HTTPS. A protokollok mindegyike saját módon jelképezi az üzenetet, így természetesen a következő kérdés merül fel: Ha egy alkalmazás egy adott protokollon keresztül küld eseményeket egy Event hub-nak, és egy másik protokollal használja őket, mi az esemény különböző részeinek és értékeinek a fogyasztóhoz való érkezésekor? Ez a cikk a gyártó és a fogyasztó ajánlott eljárásait ismerteti, így biztosítva, hogy az adott eseményen belüli értékeket a fogyasztó alkalmazás helyesen értelmezze.

A cikkben szereplő tanácsok kifejezetten ismertetik ezeket az ügyfeleket a kódrészletek fejlesztéséhez használt felsorolt verziókkal:

* Kafka Java-ügyfél (1.1.1-es verzió: https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* A Java-hoz készült Event Hubs-ügyfél (1.1.0-es verzió) Microsoft Azure https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs-ügyfél a .NET-hez (2.1.0 verziója https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (a 5.0.0 verziója https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (csak a termelők támogatásával)

Más AMQP-ügyfelek némileg eltérően működhetnek. A AMQP egy jól definiált típusú rendszerrel rendelkezik, de az adott típusú rendszertől az adott típushoz tartozó nyelvspecifikus típusok a-ügyféltől függenek, ahogy azt is, hogy az ügyfél hogyan biztosít hozzáférést egy AMQP-üzenet részeihez.

## <a name="event-body"></a>Esemény törzse
Az összes Microsoft AMQP-ügyfél a nem értelmezett bájtos táskaként jelöli meg az esemény törzsét. A termelő alkalmazások bájtok sorozatát továbbítják az ügyfélnek, és egy felhasználó alkalmazás ugyanazt a sorozatot fogadja az ügyféltől. A bájtok sorrendjének értelmezése az alkalmazás kódján belül történik.

Amikor HTTPS-kapcsolaton keresztül küld egy eseményt, az esemény törzse a közzétett tartalom, amely nem értelmezhető bájtként is kezelendő. A megadott ByteArraySerializer és ByteArrayDeserializer használatával egyszerűen elérheti ugyanazt az állapotot egy Kafka-gyártóban vagy a fogyasztóban, ahogyan az a következő kódban látható:

### <a name="kafka-byte-producer"></a>Kafka byte [] gyártó

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte [] fogyasztó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Ez a kód egy transzparens bájtos folyamatot hoz létre az alkalmazás két felében, és lehetővé teszi, hogy az alkalmazás fejlesztője manuálisan szerializálja és deszerializálja a kívánt adatokat, beleértve a deszerializálási döntések futtatását futásidőben, például a típus vagy a küldő adatai alapján a felhasználó által megadott tulajdonságok között az eseményen.

Az egyetlen, rögzített eseményvezérelt törzstel rendelkező alkalmazások képesek lehetnek más Kafka-szerializálók használatára, és a deszerializálók használatával transzparens módon konvertálhatók az adatkonverzió. Vegyünk például egy olyan alkalmazást, amely JSON-t használ. A JSON-karakterlánc felépítése és értelmezése az alkalmazás szintjén történik. A Event Hubs szinten az esemény törzse mindig sztring, amely az UTF-8 kódolásban szereplő karaktereket jelképező bájtok sorozata. Ebben az esetben a Kafka-előállító vagy-fogyasztó kihasználhatja a megadott StringSerializer vagy StringDeserializer, ahogy az a következő kódban is látható:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 string gyártó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 sztring fogyasztó
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

A AMQP oldalon a Java és a .NET is biztosít beépített lehetőségeket a karakterláncok UTF-8 bájtos sorozatokra való átalakításához. A Microsoft AMQP ügyfelek a EventData nevű osztályként jelenítik meg az eseményeket. Az alábbi példák bemutatják, hogyan szerializálhat egy UTF-8 karakterláncot egy EventData-esemény törzsében egy AMQP-gyártóban, és hogyan deszerializálhatja az EventData-események törzsét UTF-8 karakterláncba egy AMQP-fogyasztóban.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 string gyártó
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 karakterlánc-fogyasztó
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 string gyártó
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 sztring fogyasztó
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Mivel a Kafka nyílt forráskódú, az alkalmazás fejlesztője ellenőrizheti a szerializáló vagy deszerializáló megvalósítását, és programkódot implementálhat, amely a AMQP oldalán létrehoz vagy felhasználja a bájtok kompatibilis sorozatát.

## <a name="event-user-properties"></a>Esemény felhasználójának tulajdonságai

A felhasználó által beállított tulajdonságok beállíthatók és beolvashatók mindkét AMQP-ügyfélről (a Microsoft AMQP-ügyfelek által ismert tulajdonságok) és a Kafka-re (ahol fejlécek néven szerepelnek). A HTTPS-küldők a felhasználói tulajdonságokat egy eseményen is megadhatják, ha HTTP-fejlécként adja meg őket a POST műveletben. Azonban a Kafka az esemény törzseit és az esemény fejlécének értékeit bájt-sorszámként kezeli. Míg a AMQP-ügyfelekben a tulajdonságértékek típusai vannak, amelyek a AMQP típusának megfelelően a tulajdonságértékek kódolásával vannak elküldve.

A HTTPS speciális eset. A küldési ponton az összes tulajdonságérték UTF-8 szöveg. A Event Hubs szolgáltatás korlátozott mértékben értelmezi a megfelelő tulajdonságértékek átalakítását a AMQP-kódolású 32-bites és 64 bites aláírt egész számok, a 64 bites lebegőpontos számok és a logikai értékek alapján. Minden olyan tulajdonságérték, amely nem felel meg az egyik típusnak, karakterláncnak minősül.

Ha ezeket a módszereket a tulajdonság beírásával keveri, azt jelenti, hogy a Kafka-fogyasztó látja a nyers AMQP-kódolt bájt sorozatot, beleértve a AMQP-típus adatait is. Míg a AMQP-fogyasztó a Kafka gyártó által eljuttatott, nem típusos bájt sorozatot látja, amelyet az alkalmazásnak kell értelmezni.

A AMQP vagy HTTPS-gyártóktól származó tulajdonságokat fogadó Kafka-fogyasztók esetében használja a AmqpDeserializer osztályt, amely a Kafka-ökoszisztémában található többi deszerializáló alapján lett modellezve. A AMQP-kódolású byte-sorozatokban szereplő információk értelmezésével deszerializálja az adatbájtokat Java-típusra.

Ajánlott eljárásként Azt javasoljuk, hogy adjon meg egy tulajdonságot a AMQP vagy HTTPS protokollon keresztül küldött üzenetekben. A Kafka-fogyasztó felhasználhatja annak meghatározására, hogy a fejlécek értékének AMQP deszerializálásra van-e szükség. A tulajdonság értéke nem fontos. Csak egy jól ismert névvel kell rendelkeznie, amelyet a Kafka-fogyasztó talál a fejlécek listájában, és ennek megfelelően módosíthatja a viselkedését.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP a Kafka 1. részéhez: esemény létrehozása és küldése C# (.NET) tulajdonságokkal
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP a Kafka 2. részében: a AmqpDeserializer használatával deszerializálhatja ezeket a tulajdonságokat egy Kafka-fogyasztóban
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Ha az alkalmazás ismeri a tulajdonság várt típusát, vannak olyan deszerializáló metódusok, amelyek nem igénylik a Cast használatát, de hibát jeleznek, ha a tulajdonság nem a várt típusú.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP a Kafka 3. részében: a AmqpDeserializer használatának különböző módja a Kafka-fogyasztók számára
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

A másik irány nagyobb szerepet játszik, mivel a Kafka-gyártó által beállított fejléceket mindig egy AMQP-fogyasztó látja el nyers bájtként (a (Microsoft Azure) Event Hubs-ügyfél, illetve `System.Byte[]` a Microsoft .net AMQP-ügyfelei számára). A legegyszerűbb elérési út az, hogy a Kafka által biztosított szerializálók egyikével létrehozza a bájtokat a Kafka-gyártó oldalán lévő fejléc értékeihez, majd egy kompatibilis deszerializálási kódot ír a AMQP fogyasztói oldalára.

Ahogy a AMQP-to-Kafka esetében is, az ajánlott eljárás az, hogy a Kafka-n keresztül küldött üzenetekben szerepeljen a tulajdonság. A AMQP-fogyasztó a tulajdonsággal határozhatja meg, hogy a fejléc értékei deszerializálásra szorulnak-e. A tulajdonság értéke nem fontos. Csak egy jól ismert névvel kell rendelkeznie, amelyet a AMQP fogyasztó talál a fejlécek listájában, és ennek megfelelően módosíthatja a viselkedését. Ha a Kafka-gyártó nem módosítható, akkor az alkalmazás megkeresi azt is, hogy a tulajdonság értéke bináris vagy bájt típusú-e, és a típus alapján próbálja meg a deszerializálást.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka a AMQP 1. rész: a Kafka-események létrehozása és küldése a következő tulajdonságokkal
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>A Kafka AMQP 2. rész: a tulajdonságok manuális deszerializálása a C#-ban (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>A Kafka a AMQP 3. rész: a tulajdonságok manuális deszerializálása Java-ban
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan végezheti el a Event Hubs továbbítását a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

* [Ismerkedés az Event Hubs szolgáltatással](./event-hubs-about.md)
* [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
* [További minták megismerése a Kafkához készült Event Hubs GitHub-oldalán](https://github.com/Azure/azure-event-hubs-for-kafka)
* A [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) használatával [továbbíthatja a helyszíni Kafka-eseményeket a felhőbe Event Hubs.](event-hubs-kafka-mirror-maker-tutorial.md)
* Megtudhatja, hogyan továbbíthatja Event Hubs a [natív Kafka-alkalmazásokkal](event-hubs-quickstart-kafka-enabled-event-hubs.md), az [Apache flink](event-hubs-kafka-flink-tutorial.md)-val vagy a beosztott [streamekkel](event-hubs-kafka-akka-streams-tutorial.md)
