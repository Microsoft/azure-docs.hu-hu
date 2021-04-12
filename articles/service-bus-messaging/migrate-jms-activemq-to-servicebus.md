---
title: Java Message Service-(JMS-) alkalmazások migrálása az Apache ActiveMQ-ből a Azure Service Busba | Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíthet át olyan meglévő JMS-alkalmazásokat, amelyek az Apache ActiveMQ-mel működnek együtt a Azure Service Busával.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 4160a9ab4edbac8584eab2d4e5b9bf1ba11a9aec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568783"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Meglévő Java Message Service (JMS) 2,0-alkalmazások migrálása Apache ActiveMQ-ből Azure Service Bus

Ez a cikk azt ismerteti, hogyan lehet módosítani egy meglévő Java Message Service (JMS) 2,0-alkalmazást, amely együttműködik a JMS-közvetítővel a Azure Service Bus való interakcióhoz. A cikkből megtudhatja, hogy az Apache ActiveMQ-ból vagy az Amazon MQ-ból való Migrálás.

A Azure Service Bus támogatja a Java 2 platform, a Enterprise Edition és a Spring számítási feladatokat, amelyek a JMS 2,0 API-t használják a speciális üzenetsor-kezelési protokollon (AMQP).

## <a name="before-you-start"></a>Előkészületek

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure Service Bus és Apache ActiveMQ közötti különbségek

A Azure Service Bus és az Apache ActiveMQ mindkét üzenet-közvetítő, amely JMS-szolgáltatóként működik az ügyfélalkalmazások számára üzenetek küldésére és fogadására. Mindkettő lehetővé teszi a pont-pont típusú szemantika és a várólisták, valamint a közzétételi és előfizetési szemantikai témák és előfizetések együttes használatának engedélyezése. 

Még így is vannak különbségek a kettő között, ahogy az alábbi táblázat mutatja:

| Kategória | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Alkalmazás-rétegek | Fürtözött monolit | Kétszintű <br> (átjáró + háttérrendszer) |
| Protokolltámogatás | <ul> <li>AMQP</li> <li> Trapp </li> <li> OpenWire </li> </ul> | AMQP |
| Kiépítési mód | <ul> <li> Infrastruktúra-szolgáltatás (IaaS), helyszíni </li> <li> Amazon MQ (felügyelt platform – szolgáltatás) </li> | Felügyelt platform szolgáltatásként (Péter) |
| Üzenet mérete | Ügyfél által konfigurálható | 1 MB (prémium szint) |
| Magas rendelkezésre állás | Ügyfél által felügyelt | Platform által felügyelt |
| Vészhelyreállítás | Ügyfél által felügyelt | Platform által felügyelt | 

### <a name="current-supported-and-unsupported-features"></a>Jelenleg támogatott és nem támogatott funkciók

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Megfontolandó szempontok

A Azure Service Bus kétrétegű természete különböző üzletmenet-folytonossági képességeket biztosít (magas rendelkezésre állás és vész-helyreállítás). A JMS funkcióinak használatakor azonban néhány szempontot figyelembe kell venni.

#### <a name="service-upgrades"></a>Szolgáltatások frissítése

A Service Bus frissítései és újraindítása esetén a rendszer törli az ideiglenes várólistákat vagy témaköröket. Ha az alkalmazás érzékeny az ideiglenes várólisták vagy témakörök adatvesztésére, ne használjon ideiglenes várólistákat vagy témaköröket. Használjon tartós várólistákat, témaköröket és előfizetéseket.

#### <a name="data-migration"></a>Adatok migrálása

Az ügyfélalkalmazások áttelepítésének és módosításának részeként Azure Service Bus a ActiveMQ tárolt adatai nem települnek át Service Busba. Előfordulhat, hogy egyéni alkalmazásra van szüksége a ActiveMQ-várólisták,-témakörök és-előfizetések kiürítéséhez, majd az üzenetek visszajátszásához a várólisták, témakörök és előfizetések Service Bus.

#### <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Azure Active Directory által támogatott hitelesítési mechanizmus a Service Bus számára. Mivel az Azure RBAC vagy a jogcím-alapú hitelesítés jelenleg nem támogatott az Apache csontos JMS, azonban SAS-kulcsokat kell használnia a hitelesítéshez.

## <a name="pre-migration"></a>A migrálás előtt

### <a name="version-check"></a>Verzió-ellenőrzési

A JMS-alkalmazások írásakor a következő összetevőket és verziókat használja: 

| Összetevő | Verzió |
|---|---|
| Java Message Service-(JMS-) API | 1,1 vagy újabb |
| AMQP protokoll | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Győződjön meg arról, hogy a AMQP-portok nyitva vannak

A Service Bus támogatja a AMQP protokollon keresztüli kommunikációt. Erre a célra engedélyezze a kommunikációt a 5671 (AMQP) és a 443 (TCP) porton keresztül. Attól függően, hogy hol találhatók az ügyfélalkalmazások, szükség lehet egy támogatási jegyre, amely lehetővé teszi az ilyen portokon keresztüli kommunikációt.

> [!IMPORTANT]
> A Service Bus csak a AMQP 1,0 protokollt támogatja.

### <a name="set-up-enterprise-configurations"></a>Vállalati konfigurációk beállítása

A Service Bus különböző vállalati biztonsági és magas rendelkezésre állású funkciókat tesz lehetővé. További információkért lásd: 

  * [Virtuális hálózati szolgáltatásvégpontok](service-bus-service-endpoints.md)
  * [Tűzfal](service-bus-ip-filtering.md)
  * [Szolgáltatás oldali titkosítás az ügyfél által felügyelt kulccsal (BYOK)](configure-customer-managed-key.md)
  * [Privát végpontok](private-link-service.md)
  * [Hitelesítés és engedélyezés](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Figyelés, riasztások és nyomkövetés

Az egyes Service Bus névterek esetében a metrikák a Azure Monitorra tehetők közzé. Ezek a metrikák a névtérhez lefoglalt erőforrások riasztására és dinamikus skálázására használhatók.

A különböző metrikákkal és a riasztások beállításával kapcsolatos további információkért tekintse meg a [Azure Monitor Service Bus metrikákat](service-bus-metrics-azure-monitor.md). További információ az [ügyféloldali nyomkövetésről az adatműveletek](service-bus-end-to-end-tracing.md) és az [operatív/diagnosztikai naplózás felügyeleti műveletekhez](service-bus-diagnostic-logs.md)című részében található.

### <a name="metrics---new-relic"></a>Metrikák – új ereklye

A ActiveMQ-leképezésből származó metrikákat összekapcsolhatja, amelyek metrikái a Azure Service Bus. Tekintse meg a következőt az új ereklye webhelyről:

  * [ActiveMQ/Amazon MQ új ereklye mérőszámok](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Új ereklye mérőszámok Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Az új ereklye jelenleg nem rendelkezik közvetlen, zökkenőmentes integrációval a ActiveMQ, de az Amazon MQ-hoz elérhető metrikák is rendelkezésre állnak. Mivel az Amazon MQ a ActiveMQ-ből származik, az alábbi táblázat az Amazon MQ új ereklye mérőszámait az Azure Service Busba képezi le.
>

|Metrika csoportosítása| Amazon MQ/ActiveMQ metrika | Azure Service Bus metrika |
|------------|---------------------------|--------------------------|
|Bróker|`CpuUtilization`|`CPUXNS`|
|Bróker|`MemoryUsage`|`WSXNS`|
|Bróker|`CurrentConnectionsCount`|`activeConnections`|
|Bróker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Bróker|`InactiveDurableTopicSubscribersCount`|Előfizetési metrikák használata|
|Bróker|`TotalMessageCount`|Üzenetsor/témakör/előfizetés szintjének használata `activeMessages`|
|Üzenetsor/témakör|`EnqueueCount`|`incomingMessages`|
|Üzenetsor/témakör|`DequeueCount`|`outgoingMessages`|
|Üzenetsor|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migrálás

Ha át szeretné telepíteni a meglévő JMS 2,0 alkalmazást a Service Bus való interakcióra, kövesse a következő szakasz lépéseit.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Exportálja a topológiát a ActiveMQ-ből, és hozza létre az entitásokat a Service Busban (nem kötelező)

Annak biztosítása érdekében, hogy az ügyfélalkalmazások zökkenőmentesen csatlakozhassanak a Service Bushoz, telepítse át a topológiát (beleértve a várólistákat, témaköröket és előfizetéseket) az Apache ActiveMQ-ből Service Busre.

> [!NOTE]
> JMS-alkalmazások esetén a várólisták, témakörök és előfizetések futtatókörnyezeti műveletként hozhatók létre. A legtöbb JMS-szolgáltató (üzenet-közvetítő) lehetővé teszi, hogy futásidőben hozza létre ezeket. Ezt az exportálási lépést nem kötelező figyelembe venni. Annak biztosítása érdekében, hogy az alkalmazás jogosult legyen a topológia létrehozására futásidőben, használja a kapcsolódási karakterláncot SAS- `Manage` engedélyekkel.

Ehhez tegye a következőket:

1. A topológia exportálásához használja a [ActiveMQ parancssori eszközöket](https://activemq.apache.org/activemq-command-line-tools-reference) .
1. Hozza létre újra ugyanazt a topológiát egy [Azure Resource Manager sablon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)használatával.
1. Futtassa a Azure Resource Manager sablont.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>A Maven-függőség importálása Service Bus JMS megvalósításához

A Service Bustel való zökkenőmentes kapcsolódás biztosításához adja hozzá a `azure-servicebus-jms` csomagot a Maven- `pom.xml` fájlhoz a következő módon:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Az alkalmazáskiszolgáló konfigurációjának módosításai

Ez a rész a ActiveMQ-hez csatlakozó ügyfélalkalmazások futtatására szolgáló alkalmazáskiszolgáló számára lett testreszabva.

#### <a name="spring-applications"></a>Rugós alkalmazások

##### <a name="update-the-applicationproperties-file"></a>A `application.properties` fájl frissítése

Ha Spring boot-alkalmazást használ a ActiveMQ-hez való kapcsolódáshoz, el szeretné távolítani a ActiveMQ jellemző tulajdonságokat a `application.properties` fájlból.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Ezután adja hozzá a Service Bus-specifikus tulajdonságokat a `application.properties` fájlhoz.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Csere `ActiveMQConnectionFactory` erre `ServiceBusJmsConnectionFactory`

A következő lépés a példányának cseréje a alkalmazással `ActiveMQConnectionFactory` `ServiceBusJmsConnectionFactory` .

> [!NOTE] 
> A kód tényleges módosításai az alkalmazásra és a függőségek felügyeletére vonatkoznak, az alábbi példa pedig útmutatást nyújt a módosításra vonatkozóan.
>

Korábban előfordulhat, hogy a következőt hozta létre egy objektum létrehozásával `ActiveMQConnectionFactory` :

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Most ezt a következőképpen változtathatja meg egy objektum létrehozásához `ServiceBusJmsConnectionFactory` :

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>A migrálás után

Most, hogy módosította az alkalmazást, hogy megkezdje az üzenetek küldését és fogadását Service Busről, ellenőrizze, hogy a várt módon működik-e. Ha elkészült, folytathatja az alkalmazás-verem pontosítását és korszerűsítését.

## <a name="next-steps"></a>Következő lépések

A [Azure Service Bus JMS készült Spring boot Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) segítségével zökkenőmentesen integrálhatja Service Bus.

Az Service Bus üzenetkezeléssel és JMS kapcsolatos további tudnivalókért tekintse meg a következőt:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
