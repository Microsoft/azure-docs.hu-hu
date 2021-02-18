---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652605"
---
A következő táblázat felsorolja a Java Message Service (JMS) által jelenleg támogatott funkciókat Azure Service Bus. Emellett a nem támogatott funkciókat is megjeleníti.


| Szolgáltatás | API |Állapot |
|---|---|---|
| Üzenetsorok   | <ul> <li> JMSContext. createQueue (karakterlánc queueName) </li> </ul>| **Támogatott** |
| Témakörök   | <ul> <li> JMSContext. createTopic (karakterlánc topicName) </li> </ul>| **Támogatott** |
| Ideiglenes várólisták |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Támogatott** |
| Ideiglenes témakörök |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Támogatott** |
| Üzenet gyártója/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Támogatott** |
| Üzenetsor-tallózók |<ul> <li> JMSContext. createBrowser (Üzenetsor-várólista) </li> <li> JMSContext. createBrowser (Üzenetsor-várólista, karakterlánc messageSelector) </li> </ul> | **Támogatott** |
| Üzenet fogyasztó/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (cél célhelye) </li> <li> JMSContext. createConsumer (cél cél, karakterlánc messageSelector) </li> <li> JMSContext. createConsumer (cél cél, karakterlánc messageSelector, logikai nem helyi)</li> </ul>  <br/> a helyi hálózat jelenleg nem támogatott | **Támogatott** |
| Közös tartós előfizetések | <ul> <li> JMSContext. createSharedDurableConsumer (témakör témakör, karakterlánc neve) </li> <li> JMSContext. createSharedDurableConsumer (témakör témakör, karakterlánc neve, karakterlánc messageSelector) </li> </ul>| **Támogatott**|
| Nem megosztott tartós előfizetések | <ul> <li> JMSContext. createDurableConsumer (témakör témakör, karakterlánc neve) </li> <li> createDurableConsumer (témakör, karakterlánc neve, karakterlánc messageSelector, logikai nem helyi) </li> </ul> <br/> a nem helyi érték jelenleg nem támogatott, és hamis értékre kell állítani. | **Támogatott** |
| Megosztott nem tartós előfizetések |<ul> <li> JMSContext. createSharedConsumer (témakör témakör, karakterlánc sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (témakör témakör, karakterlánc sharedSubscriptionName, karakterlánc messageSelector) </li> </ul> | **Támogatott** |
| Nem megosztva nem tartós előfizetések |<ul> <li> JMSContext. createConsumer (cél célhelye) </li> <li> JMSContext. createConsumer (cél cél, karakterlánc messageSelector) </li> <li> JMSContext. createConsumer (cél cél, karakterlánc messageSelector, logikai nem helyi) </li> </ul> <br/> a nem helyi érték jelenleg nem támogatott, és hamis értékre kell állítani. | **Támogatott** |
| Üzenetek választói | a létrehozott fogyasztótól függ | **Támogatott** |
| Kézbesítési késleltetés (ütemezett üzenetek) | <ul> <li> JMSProducer. setDeliveryDelay (hosszú deliveryDelay) </li> </ul>|**Támogatott**|
| Üzenet létrehozva |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (szerializálható objektum) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (karakterlánc szövege) </li> </ul>| **Támogatott** |
| Entitások közötti tranzakciók |<ul> <li> Kapcsolatok. createSession (igaz, Session.SESSION_TRANSACTED) </li> </ul> | **Támogatott** |
| Elosztott tranzakciók || Nem támogatott |
