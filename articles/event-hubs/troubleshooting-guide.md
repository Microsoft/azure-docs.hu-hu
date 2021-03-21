---
title: Kapcsolódási problémák elhárítása – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs kapcsolódási problémáinak elhárításával kapcsolatos információkat tartalmaz.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012680"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Kapcsolódási problémák elhárítása – Azure Event Hubs
Számos oka lehet annak, hogy az ügyfélalkalmazások nem tudnak csatlakozni az Event hub-hoz. Előfordulhat, hogy az Ön által tapasztalt kapcsolódási problémák állandóak vagy átmenetiek. Ha a probléma minden alkalommal (állandó) történik, érdemes megtekinteni a kapcsolódási karakterláncot, a szervezet tűzfalbeállítások, az IP-tűzfal beállításait, a hálózati biztonsági beállításokat (szolgáltatási végpontok, privát végpontok stb.). Átmeneti problémák esetén az SDK legújabb verziójára való frissítés, az eldobott csomagok vizsgálatára szolgáló parancsok futtatása, valamint a hálózati nyomkövetés beszerzése segíthet a hibák elhárításában. 

Ez a cikk tippekkel szolgál az Azure Event Hubs kapcsolódási problémáinak elhárításához. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Állandó csatlakozási problémák elhárítása
Ha az alkalmazás egyáltalán nem tud csatlakozni az Event hub-hoz, kövesse a jelen szakaszban ismertetett lépéseket a probléma megoldásához. 

### <a name="check-if-there-is-a-service-outage"></a>Ellenőrizze, hogy van-e leállás a szolgáltatásban
Keresse meg az Azure Event Hubs szolgáltatás [leállását az Azure szolgáltatás állapota helyen](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>A kapcsolatok karakterláncának ellenőrzése 
Ellenőrizze, hogy helyes-e a használt kapcsolatok karakterlánca. A kapcsolódási karakterlánc lekérése a Azure Portal, a CLI vagy a PowerShell használatával című témakör a kapcsolódási karakterlánc [beolvasása](event-hubs-get-connection-string.md) című szakaszban található. 

A Kafka-ügyfelek esetében ellenőrizze, hogy a producer.config vagy consumer.config fájlok megfelelően vannak-e konfigurálva. További információ: [üzenetek küldése és fogadása a Kafka-vel Event Hubsban](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Annak ellenőrzése, hogy a AzureEventGrid szolgáltatás címkéje engedélyezve van-e a hálózati biztonsági csoportokban
Ha az alkalmazás egy alhálózaton belül fut, és van egy társított hálózati biztonsági csoport, ellenőrizze, hogy engedélyezett-e az internetes kimenő hozzáférés vagy a AzureEventGrid szolgáltatás címkéje. Lásd: [virtuális hálózati szolgáltatás címkék](../virtual-network/service-tags-overview.md) és keresés `EventHub` .

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Ellenőrizze, hogy az alkalmazásnak egy vnet egy adott alhálózatán kell-e futnia
Győződjön meg arról, hogy az alkalmazás egy olyan virtuális hálózati alhálózaton fut, amely hozzáfér a névtérhez. Ha nem, akkor futtassa az alkalmazást abban az alhálózatban, amely hozzáfér a névtérhez, vagy adja meg annak a számítógépnek az IP-címét, amelyen az alkalmazás fut az [IP-tűzfalon](event-hubs-ip-filtering.md). 

Ha egy Event hub-névtérhez hoz létre virtuális hálózati szolgáltatási végpontot, a névtér csak a szolgáltatási végponthoz kötött alhálózatról fogad forgalmat. Ez a viselkedés kivételt jelent. Adott IP-címeket adhat hozzá az IP-tűzfalon az Event hub nyilvános végponthoz való hozzáférés engedélyezéséhez. További információ: [hálózati szolgáltatási végpontok](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>A névtér IP-tűzfal beállításainak megtekintése
Győződjön meg arról, hogy az IP-tűzfal nem tiltja le annak a számítógépnek a nyilvános IP-címét, amelyen az alkalmazást futtatja.  

Alapértelmezés szerint a Event Hubs névterek az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Az IP-tűzfalszabályok a Event Hubs névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Olyan IP-címről érkező csatlakozási kísérletek, amely nem felel meg a Event Hubs névtérben lévő engedélyezett IP-szabálynak, a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

További információ: az [IP-tűzfalszabályok konfigurálása Azure Event Hubs-névtérhez](event-hubs-ip-filtering.md). A [hálózattal kapcsolatos problémák elhárításával](#troubleshoot-network-related-issues)ellenőrizhető, hogy rendelkezik-e IP-szűréssel, virtuális hálózattal vagy tanúsítványláncot kapcsolatos problémákkal.

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Ellenőrizze, hogy a névtér csak privát végpont használatával érhető-e el
Ha a Event Hubs névtér csak privát végponton keresztül érhető el, győződjön meg arról, hogy az ügyfélalkalmazás a magánhálózati végponton keresztül éri el a névteret. 

Az [Azure Private link Service](../private-link/private-link-overview.md) lehetővé teszi az Azure Event Hubs elérését a virtuális hálózat **privát végpontján** keresztül. A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a virtuális hálózatról, amely hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [privát végpontok konfigurálása](private-link-service.md). A privát végpontok használatának ellenőrzéséhez tekintse meg a titkos végponti **kapcsolatok működését** ismertető szakaszt. 

### <a name="troubleshoot-network-related-issues"></a>A hálózattal kapcsolatos problémák elhárítása
A Event Hubs hálózati problémáinak elhárításához kövesse az alábbi lépéseket: 

Tallózással keresse meg a következőt: vagy a [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Segít ellenőrizni, hogy rendelkezik-e IP-szűréssel, illetve virtuális hálózati vagy tanúsítványlánc-problémákkal (a Java SDK használatakor leggyakrabban).

Példa a **sikeres üzenetre**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Egy példa a hiba **hibaüzenetére**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Átmeneti kapcsolódási problémák elhárítása
Ha időnkénti kapcsolódási problémákat tapasztal, a hibaelhárítási tippekért tekintse át a következő szakaszokat. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Az ügyfél-SDK legújabb verziójának használata
Előfordulhat, hogy néhány átmeneti kapcsolódási probléma kijavítva lett az SDK újabb verzióiban, mint amit használ. Győződjön meg arról, hogy az alkalmazásokban az ügyféloldali SDK-k legújabb verzióját használja. Az SDK-kat folyamatosan fejlesztjük új/frissített funkciókkal és hibajavításokkal, ezért mindig tesztelje a legújabb csomagot. Győződjön meg a kibocsátási megjegyzésekben rögzített és a hozzáadott/frissített szolgáltatásokkal kapcsolatos problémákról. 

További információ az ügyféloldali SDK-k használatáról: [Azure Event Hubs-Client SDK-](sdks.md) k. 

### <a name="run-the-command-to-check-dropped-packets"></a>Futtassa a parancsot az eldobott csomagok vizsgálatához
Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal a szolgáltatással 1 másodpercenként 25 különböző TCP-kapcsolatot kell létrehozni. Ezt követően megtekintheti, hogy a sikeres és sikertelen volt-e a TCP-kapcsolatok késése. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Ha más eszközöket (például `tnc` , stb.) használ, használhatja az egyenértékű parancsokat `ping` . 

Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik olyan eszközökkel, mint például a [Wireshark](https://www.wireshark.org/). Ha szükséges, forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) . 

### <a name="service-upgradesrestarts"></a>Szolgáltatás-frissítések/újraindítások
Az átmeneti kapcsolódási problémák a háttérrendszer frissítése és újraindítása miatt merülhetnek fel. Ha ezek történnek, a következő tünetek jelenhetnek meg: 

- Lehet, hogy elvesznek a bejövő üzenetek/kérelmek.
- A naplófájl hibaüzeneteket tartalmazhat.
- Előfordulhat, hogy az alkalmazások néhány másodpercig le lesznek választva a szolgáltatástól.
- Előfordulhat, hogy a kérelmek egy pillanatra szabályozva vannak.

Ha az alkalmazás kódja az SDK-t használja, az újrapróbálkozási házirend már be van építve és aktív. Az alkalmazás az alkalmazás/munkafolyamat jelentős hatása nélkül újra csatlakozik. Ezeknek az átmeneti hibáknak a kifogása, a biztonsági mentés és a hívás újbóli kipróbálása biztosítja, hogy a kód rugalmasan kezelje ezeket az átmeneti problémákat.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

* [Hitelesítési és engedélyezési hibák elhárítása](troubleshoot-authentication-authorization.md)
