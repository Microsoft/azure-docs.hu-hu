---
title: Kivételek Azure Relay és azok feloldása | Microsoft Docs
description: Azure Relay kivételek és javasolt műveletek listája, melyekkel megoldhatja a megoldást.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625148"
---
# <a name="azure-relay-exceptions"></a>Kivételek Azure Relay

Ez a cikk a Azure Relay API-k által esetlegesen generált kivételeket sorolja fel. Ez a hivatkozás változhat, ezért térjen vissza a frissítésekhez.

## <a name="exception-categories"></a>Kivételek kategóriái

A Relay API-k olyan kivételeket állítanak elő, amelyek a következő kategóriákba sorolhatók. A felsorolt javaslatok a kivételek megoldásához szükséges műveleteket is tartalmazzák.

*   **Felhasználói kódolási hiba**: [System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. szerializálás. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Általános művelet**: próbálkozzon a kód kijavításával a folytatás előtt.
*   **Telepítési/konfigurációs hiba**: [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Általános művelet**: Tekintse át a konfigurációt. Ha szükséges, módosítsa a konfigurációt.
*   **Átmeneti kivételek**: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Általános művelet**: Próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
*   **Egyéb kivételek**: [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. timeoutexception osztályról](/dotnet/api/system.timeoutexception). 

    **Általános művelet**: a kivétel típusára jellemző. Tekintse meg a következő szakaszban található táblázatot. 

## <a name="exception-types"></a>Kivételek típusai

Az alábbi táblázat az üzenetkezelési kivételek típusait és azok okait sorolja fel. A kivételek megoldásához szükséges javasolt műveleteket is figyelembe veszi.

| **Kivételtípus** | **Leírás** | **Javasolt művelet** | **Megjegyzés automatikus vagy azonnali újrapróbálkozás** |
| --- | --- | --- | --- |
| [Időtúllépés](/dotnet/api/system.timeoutexception) |A kiszolgáló a megadott időn belül nem válaszolt a kért műveletre, amelyet a [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)vezérel. Lehet, hogy a kiszolgáló végrehajtotta a kért műveletet. Ez hálózati vagy más infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotot a konzisztencia beállításnál, majd próbálkozzon újra, ha szükséges. Lásd: [timeoutexception osztályról](#timeoutexception). |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [Érvénytelen művelet](/dotnet/api/system.invalidoperationexception) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részletekért tekintse meg a kivételt jelző üzenetet. |Keresse meg a kódot és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvényes. |Az újrapróbálkozás nem segít. |
| [A művelet megszakítva](/dotnet/api/system.operationcanceledexception) |Kísérlet történt egy olyan objektum műveletének meghívására, amely már be van zárva, megszakadt vagy el lett távolítva. Ritka esetekben a környezeti tranzakció már el van távolítva. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem indít el műveleteket egy eldobott objektumon. |Az újrapróbálkozás nem segít. |
| [Jogosulatlan hozzáférés](/dotnet/api/system.unauthorizedaccessexception) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot beszerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. |Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel lett létrehozva. Vizsgálja meg a Access Control szolgáltatás konfigurációját. |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [Argumentum kivétel](/dotnet/api/system.argumentexception),<br /> [Argumentum null](/dotnet/api/system.argumentnullexception),<br />[Argumentum az engedélyezett tartományon kívül](/dotnet/api/system.argumentoutofrangeexception) |A következők közül egy vagy több történt:<br />A metódushoz megadott egy vagy több argumentum érvénytelen.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) megadott URI egy vagy több elérésiút-szegmenst tartalmaz.<br />A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy- [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) megadott URI-séma érvénytelen. <br />A tulajdonság értéke nagyobb, mint 32 KB. |Ellenőrizze a hívó kódját, és ellenőrizze, hogy helyesek-e az argumentumok. |Az újrapróbálkozás nem segít. |
| [Kiszolgáló foglalt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. |Az ügyfél hosszabb ideig is megvárhat, majd próbálja megismételni a műveletet. |Előfordulhat, hogy az ügyfél egy adott intervallum után újrapróbálkozik. Ha az újrapróbálkozások eltérő kivételt eredményeznek, ellenőrizze a kivétel újrapróbálkozási viselkedését. |
| [Kvóta túllépve](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Az üzenetküldési entitás elérte a maximálisan engedélyezett méretet. |Hozzon létre helyet az entitásban az entitásból vagy annak alvárólistából érkező üzenetek fogadásával. Lásd: [quotaexceededexception osztályról](#quotaexceededexception). |Az újrapróbálkozás akkor lehet hasznos, ha az üzenetek időközben el lettek távolítva. |
| [Az üzenet mérete túllépve](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Az üzenet tartalma meghaladja az 256-KB korlátot. Vegye figyelembe, hogy az 256-KB korlát a teljes üzenet mérete. Az üzenetek teljes mérete magában foglalhatja a rendszertulajdonságokat és a Microsoft .NET terhelését is. |Csökkentse az üzenet adattartalmát, majd próbálja megismételni a műveletet. |Az újrapróbálkozás nem segít. |

## <a name="quotaexceededexception"></a>Quotaexceededexception osztályról

A [quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitásra vonatkozó kvóta túllépve.

A Relay esetében ez a kivétel a [System. ServiceModel. quotaexceededexception osztályról](/dotnet/api/system.servicemodel.quotaexceededexception)takarja, amely azt jelzi, hogy a figyelő maximális száma túllépte a végpontot. Ezt a kivételt jelző üzenet **MaximumListenersPerEndpoint** értéke jelzi.

## <a name="timeoutexception"></a>Timeoutexception osztályról
A [timeoutexception osztályról](/dotnet/api/system.timeoutexception) azt jelzi, hogy a felhasználó által kezdeményezett művelet a művelet időkorlátja alatt hosszabb időt vesz igénybe. 

Keresse meg a [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) tulajdonság értékét. A korlát eléréséhez [timeoutexception osztályról](/dotnet/api/system.timeoutexception)is vezethet.

A Relay esetében időtúllépési kivételek jelenhetnek meg, amikor először nyit meg egy Relay feladói kapcsolatát. Ennek a kivételnek két gyakori oka van:

*   Lehet, hogy a [OpenTimeout](/previous-versions/) értéke túl kicsi (ha még egy másodperc töredéke is).
* Előfordulhat, hogy egy helyszíni továbbító figyelő nem válaszol (vagy a tűzfalszabályok olyan problémákba ütközik, amelyek tiltják a figyelők új ügyfélkapcsolatok fogadását), és a [OpenTimeout](/previous-versions/) értéke kisebb, mint körülbelül 20 másodperc.

Példa:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van:

*   **A konfiguráció helytelen**
    
    Lehet, hogy a művelet időtúllépése túl kicsi a működési feltételnél. Az ügyfél-SDK-ban a művelet időtúllépésének alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kódban szereplő érték értéke túl kicsi-e. Vegye figyelembe, hogy a CPU-használat és a hálózat feltétele befolyásolhatja a művelet befejezéséhez szükséges időt. Jó ötlet a művelet időtúllépésének beállítása nagyon kis értékre.
*   **Átmeneti szolgáltatás hibája**

    Időnként előfordulhat, hogy a továbbító szolgáltatás késéseket tapasztal a feldolgozási kérelmekben. Ez például a nagy forgalmú időszakok esetében fordulhat elő. Ha ez történik, próbálkozzon újra a művelettel egy késleltetés után, amíg a művelet nem sikerült. Ha ugyanaz a művelet több kísérlet után is folytatódik, ellenőrizze az [Azure szolgáltatás állapotát](https://azure.microsoft.com/status/) , és győződjön meg róla, hogy ismert szolgáltatások kimaradása történt.

## <a name="next-steps"></a>Következő lépések
* [Azure Relay GYIK](relay-faq.md)
* [Relay-névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés a Azure Relay és a .NET használatával](relay-hybrid-connections-dotnet-get-started.md)
* [A Azure Relay és a Node első lépései](relay-hybrid-connections-node-get-started.md)
