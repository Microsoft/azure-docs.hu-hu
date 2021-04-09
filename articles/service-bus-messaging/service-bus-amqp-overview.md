---
title: A AMQP 1,0-es verziójának áttekintése Azure Service Bus
description: Ismerje meg, hogyan támogatja a Azure Service Bus a Advanced Message Queueing Protocol (AMQP), egy nyílt szabványú protokollt.
ms.topic: article
ms.date: 02/17/2021
ms.openlocfilehash: b2ca126312f5fc3da2a7ff6e20a9ade252f489f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653883"
---
# <a name="amqp-10-support-in-service-bus"></a>AMQP 1,0-támogatás Service Bus
A Azure Service Bus Cloud Service a kommunikáció elsődleges kommunikációs eszközeként a [1,0 Advanced Message Queuing protokollt (AMQP)](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) használja. A Microsoft az iparágban, az ügyfelekkel és a versengő üzenetkezelési közvetítőket használó partnerekkel együttműködve fejleszti és fejleszti a AMQP az elmúlt évtizedben, és új bővítményeket fejlesztett ki az [Oasis AMQP technikai bizottságban](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). A AMQP 1,0 egy ISO és IEC szabvány ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

A AMQP lehetővé teszi a többplatformos hibrid alkalmazások kiépítését a szállító-semleges és implementációs-semleges, nyílt szabványú protokoll használatával. Az alkalmazásokat a különböző nyelvekkel és keretrendszerekkel, valamint a különböző operációs rendszereken futó összetevőkkel építheti fel. Ezek az összetevők kapcsolódhatnak a Service Bushoz, és zökkenőmentesen cserélhetik fel a strukturált üzleti üzeneteket hatékonyan és teljes hűséggel.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Bevezetés: mi a AMQP 1,0, és miért fontos?
A hagyományosan az üzenetsor-orientált middleware-termékek saját protokollokat használnak az ügyfélalkalmazások és a brókerek közötti kommunikációhoz. Ez azt jelenti, hogy ha kiválasztotta egy adott szállító üzenetkezelési közvetítőjét, akkor a gyártó kódtárait kell használnia az ügyfélalkalmazások ehhez a közvetítőhöz való összekapcsolásához. Ez az adott gyártótól való függőséget eredményez, mivel az alkalmazások egy másik termékhez való portolása minden csatlakoztatott alkalmazásban kód módosítását igényli. A Java-közösségekben a nyelvspecifikus API-szabványok, például a Java Message Service (JMS) és a Spring Framework absztrakciói némileg csökkentik a fájdalmat, de nagyon szűk funkciókkal rendelkeznek, és kizárják a fejlesztőket más nyelveken.

Emellett a különböző gyártóktól származó üzenetkezelési közvetítők összekapcsolása bonyolult. Ez általában az alkalmazás-szintű áthidalót igényli az üzenetek egyik rendszerről egy másikra való áthelyezéséhez, illetve a saját üzenetek formátumának lefordításához. Ez egy gyakori követelmény; Ha például egy új egységesített felületet kell megadnia a régebbi, különálló rendszerekhez, vagy integrálnia kell az informatikai rendszereket az egyesítést követően. A AMQP lehetővé teszi a közvetlen összekötők összekapcsolását közvetlenül, például az olyan útválasztók használatával, mint az [Apache csontos elküldési útválasztó](https://qpid.apache.org/components/dispatch-router/index.html) vagy a közvetítő natív "lapátok", mint például az egyik [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

A szoftveres iparág egy gyorsan változó üzleti tevékenység; az új programozási nyelvek és alkalmazás-keretrendszerek időnként megzavarják a lépést. Hasonlóképpen, az informatikai rendszerek követelményei idővel fejlődnek, és a fejlesztők a platform legújabb funkcióit szeretnék kihasználni. Előfordulhat azonban, hogy a kiválasztott üzenetküldési szállító nem támogatja ezeket a platformokat. Ha az üzenetkezelési protokollok védettek, nem lehetséges, hogy mások is biztosítanak könyvtárakat ezekhez az új platformokhoz. Ezért olyan megközelítéseket kell használnia, mint például az átjárók vagy hidak létrehozása, amelyek lehetővé teszik az üzenetkezelési termék használatának folytatását.

A Advanced Message Queueing Protocol (AMQP) 1,0 fejlesztését ezen problémák indokolták. A JP Morgan Chasetől származik, akik a legtöbb pénzügyi szolgáltatóhoz hasonlóan nagy mennyiségű, az üzenetsor-orientált middleware-t használó felhasználók. A cél egyszerű volt: egy nyílt szabványú üzenetküldési protokoll létrehozásához, amely a különböző nyelvekkel, keretrendszerekkel és operációs rendszerekkel létrehozott összetevők használatával teszi elérhetővé az üzenetkezelési alkalmazásokat, mindezt a legjobb összetevők széles választékával.

## <a name="amqp-10-technical-features"></a>AMQP 1,0 – technikai funkciók
A AMQP 1,0 egy hatékony, megbízható, vezetékes üzenetkezelési protokoll, amellyel robusztus, platformfüggetlen, üzenetkezelési alkalmazásokat hozhat létre. A protokoll egyszerű célja: az üzenetek biztonságos, megbízható és hatékony átvitelének mechanikája két fél között. Maguk az üzenetek olyan hordozható adatábrázolással vannak kódolva, amely lehetővé teszi a heterogén küldők és fogadók számára a strukturált üzleti üzenetek teljes megbízhatóságú cseréjét. Az alábbiakban a legfontosabb funkciók összegzését ismertetjük:

* **Hatékony**: a AMQP 1,0 egy kapcsolaton alapuló protokoll, amely bináris kódolást használ a protokoll utasításainak és a rajta továbbított üzleti üzeneteknek. Kifinomult folyamat-ellenőrzési sémákat tartalmaz a hálózat és a csatlakoztatott összetevők kihasználtságának maximalizálása érdekében. Ez azt jelenti, hogy a protokoll úgy lett kialakítva, hogy egyensúlyt teremteni a hatékonyság, a rugalmasság és az együttműködési képesség között.
* **Megbízható**: a AMQP 1,0 protokoll lehetővé teszi, hogy az üzenetek számos megbízhatósági garanciával legyenek kicserélve, a tűz és a felejtsd el a megbízható, pontosan egyszeri nyugtázott kézbesítést.
* **Rugalmas**: a AMQP 1,0 egy rugalmas protokoll, amely különböző topológiák támogatásához használható. Ugyanez a protokoll használható az ügyfél és az ügyfél, az ügyfelek és a közvetítők közötti kommunikációhoz is.
* **Közvetítő – modell független**: a AMQP 1,0 specifikáció nem tesz követelményt a közvetítő által használt üzenetkezelési modellre vonatkozóan. Ez azt jelenti, hogy könnyen hozzáadhat AMQP 1,0-támogatást a meglévő üzenetkezelési közvetítőknek.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>A AMQP 1,0 egy standard (Capital 'S)
A AMQP 1,0 egy nemzetközi szabvány, amelyet az ISO és az IEC is jóváhagy ISO/IEC 19464:2014 néven.

A AMQP 1,0 a 2008-es, több mint 20 vállalat, a technológiai beszállítók és a végfelhasználói vállalatok egyik fő csoportjának fejlesztései óta. Ebben az időszakban a felhasználói cégek járultak hozzá a valós üzleti követelményekhez, és a technológiai szolgáltatók kifejlődtek a protokollon, hogy megfeleljenek a követelményeknek. A folyamat során a szállítók részt vettek a workshopokban, amelyekben együttműködtek a megvalósításuk közötti együttműködés ellenőrzéséhez.

Október 2011-án a fejlesztési munka a szervezeten belüli technikai bizottságba került át a strukturált információs szabványok (OASIS) és az OASIS AMQP 1,0 standard 2012 kiadásában. A következő cégek részt vettek a technikai bizottságban a standard fejlesztése során:

* **Technológiai szállítók**: Axway szoftver, Huawei Technologies, IIT szoftver, INETCO-rendszerek, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, folyamatjelző szoftver, Red Hat, a, a Software AG, a vigaszrendszer, a VMware, a WSO2 és a Zenika.
* **Felhasználói cégek**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Az [Oasis AMQP műszaki bizottságának](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) aktuális elnökei a Red Hat és a Microsoft.

A nyílt szabványok általánosan idézett előnyei többek között a következők:

* A gyártó zárolásának kisebb esélye
* Együttműködési lehetőség
* Könyvtárak és eszközök széles körű rendelkezésre állása
* Az elavultság elleni védelem
* A hozzáértő munkatársak rendelkezésre állása
* Alacsonyabb és kezelhető kockázat

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 és Service Bus
A AMQP 1,0-es verziójának támogatása Azure Service Bus azt jelenti, hogy a hatékony bináris protokoll használatával kihasználhatja a Service Bus Queuing szolgáltatást, valamint a felügyelt és előfizetéseket a különböző platformokon. Emellett a különböző nyelvek, keretrendszerek és operációs rendszerek együttes használatával létrehozott összetevőkből álló alkalmazásokat is készíthet.

Az alábbi ábra egy példát mutat be egy olyan központi telepítésre, amelyben a Linux rendszeren futó Java-ügyfelek a standard Java Message Service (JMS) API-val és a Windows rendszeren futó .NET-ügyfelekkel, az AMQP 1,0-es verzióját használva Exchange-Service Bus üzeneteket küldenek.

![Diagram, amely egy Service Bus két Linux-környezettel és két Windows-környezettel rendelkező üzeneteket cserél.][0]

**1. ábra: példa a többplatformos üzenetküldést Service Bus és AMQP 1,0-et használó üzembe helyezési forgatókönyvre**

Az Azure SDK-n keresztül elérhető összes támogatott Service Bus AMQP 1,0.

- [.NET-Azure Service Bus](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Java-kódtárak Azure Service Bus](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-szolgáltató a Java JMS 2,0-hez](how-to-use-java-message-service-20.md)
- [A JavaScript és az írógéppel Azure Service Bus moduljai](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus kódtárak a Pythonhoz](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Emellett a Service Bus bármely AMQP 1,0-kompatibilis protokoll-veremből is használható:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Összefoglalás
* A AMQP 1,0 egy nyílt, megbízható üzenetkezelési protokoll, amellyel többplatformos hibrid alkalmazások hozhatók létre. A AMQP 1,0 egy oázis standard.

## <a name="next-steps"></a>Következő lépések
Készen áll a tanulásra? Látogasson el a következő hivatkozásokra:

* [Service Bus használata a .NET-ről a AMQP használatával]
* [Service Bus használata a Java és a AMQP használatával]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Service Bus használata a .NET-ről a AMQP használatával]: service-bus-amqp-dotnet.md
[Service Bus használata a Java és a AMQP használatával]: ./service-bus-java-how-to-use-jms-api-amqp.md

