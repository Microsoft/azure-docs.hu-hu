---
title: Hibaelhárítási útmutató a Azure Service Bushoz | Microsoft Docs
description: Ez a cikk felsorolja az Azure Service Bus üzenetkezelési kivételeket és a kivétel bekövetkezésekor végrehajtandó javasolt műveleteket.
ms.topic: article
ms.date: 07/15/2020
ms.openlocfilehash: 6071aae85daa1852c9384656d7caf5e2deffd84e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071312"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>A Azure Service Bus hibaelhárítási útmutatója
Ez a cikk hibaelhárítási tippeket és javaslatokat tartalmaz a Azure Service Bus használatakor esetlegesen előforduló problémákkal kapcsolatban. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolati, tanúsítvány-vagy időtúllépési problémák
A következő lépések segítséget nyújthatnak a kapcsolat/tanúsítvány/időtúllépési problémák hibaelhárításához a *. servicebus.windows.net alatti összes szolgáltatáshoz. 

- Tallózással keresse meg a következőt: vagy a [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Segít ellenőrizni, hogy rendelkezik-e IP-szűréssel, illetve virtuális hálózati vagy tanúsítványlánc-problémákkal, amelyek a Java SDK használatakor gyakoriak.

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Egy példa a hiba hibaüzenetére:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- A következő parancs futtatásával ellenőrizze, hogy a tűzfal blokkolja-e a portokat. A használt portok a következők: 443 (HTTPS), 5671 (AMQP) és 9354 (net Messaging/SBMP). A használt könyvtártól függően más portok is használatban vannak. Itt látható a minta parancs, amely azt vizsgálja, hogy a 5671-es port blokkolva van-e. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux rendszeren:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal a szolgáltatással 1 másodpercenként 25 különböző TCP-kapcsolatot kell létrehozni. Ezt követően megtekintheti, hogy a sikeres és sikertelen volt-e a TCP-kapcsolatok késése. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Ha más eszközöket (például `tnc` , stb.) használ, használhatja az egyenértékű parancsokat `ping` . 
- Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik olyan eszközökkel, mint például a [Wireshark](https://www.wireshark.org/). Ha szükséges, forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>A szolgáltatás verziófrissítése/újraindítása esetén felmerülő problémák

### <a name="symptoms"></a>Probléma
- Előfordulhat, hogy a kérelmek egy pillanatra szabályozva vannak.
- Lehet, hogy elvesznek a bejövő üzenetek/kérelmek.
- A naplófájl hibaüzeneteket tartalmazhat.
- Előfordulhat, hogy az alkalmazások néhány másodpercig le lesznek választva a szolgáltatástól.

### <a name="cause"></a>Ok
A háttér-szolgáltatás verziófrissítése és újraindítása a problémákat okozhatja az alkalmazásokban.

### <a name="resolution"></a>Feloldás
Ha az alkalmazás kódja SDK-t használ, az újrapróbálkozási házirend már be van építve és aktív. Az alkalmazás az alkalmazás/munkafolyamat jelentős hatása nélkül újra csatlakozik.

## <a name="unauthorized-access-send-claims-are-required"></a>Jogosulatlan hozzáférés: a jogcímek küldése kötelező

### <a name="symptoms"></a>Probléma 
Ez a hiba akkor fordulhat elő, amikor a Visual studióból egy, a felhasználó által hozzárendelt, a küldési engedélyekkel rendelkező felügyelt identitás használatával próbál hozzáférni egy Service Bus témakörhöz.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Ok
Az identitás nem rendelkezik a Service Bus témakör eléréséhez szükséges engedélyekkel. 

### <a name="resolution"></a>Feloldás
A hiba elhárításához telepítse a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) könyvtárat.  További információ: [helyi fejlesztési hitelesítés](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Ha meg szeretné tudni, hogyan rendelhet hozzá engedélyeket a szerepkörökhöz, tekintse meg [a felügyelt identitás hitelesítése Azure Active Directory használatával Azure Service Bus erőforrások elérését](service-bus-managed-service-identity.md)ismertető témakört.

## <a name="service-bus-exception-put-token-failed"></a>Service Bus kivétel: a Put token nem sikerült

### <a name="symptoms"></a>Probléma
Ha több mint 1000 üzenetet próbál elküldeni ugyanazzal a Service Bus-kapcsolatban, a következő hibaüzenet jelenik meg: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Ok
Az üzenetek küldésére és fogadására használt tokenek száma korlátozott a Service Bus névtérhez való egyetlen kapcsolaton keresztül. Ez 1000. 

### <a name="resolution"></a>Feloldás
További üzenetek küldéséhez nyisson meg egy új kapcsolódást a Service Bus névtérhez.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket: 

- [Azure Resource Manager kivételek](service-bus-resource-manager-exceptions.md). A Azure Service Bus a Azure Resource Manager használatával (sablonok vagy közvetlen hívások segítségével) való interakció során keletkező kivételeket sorolja fel.
- [Üzenetküldési kivételek](service-bus-messaging-exceptions.md). A .NET-keretrendszer által Azure Service Bus által generált kivételek listáját tartalmazza. 

