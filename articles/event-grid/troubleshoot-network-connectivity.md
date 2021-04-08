---
title: Hálózati kapcsolattal kapcsolatos problémák elhárítása – Azure Event Grid | Microsoft Docs
description: Ez a cikk a Azure Event Gridával kapcsolatos hálózati kapcsolódási problémák elhárításával kapcsolatos információkat tartalmaz.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96339745"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Kapcsolódási problémák elhárítása – Azure Event Grid

Számos oka lehet annak, hogy az ügyfélalkalmazások nem tudnak csatlakozni egy Event Grid témakörhöz/tartományhoz. Előfordulhat, hogy az Ön által tapasztalt kapcsolódási problémák állandóak vagy átmenetiek. Ha a probléma bármikor (állandó) történik, érdemes lehet megtekinteni a szervezet tűzfalbeállítások beállításait, az IP-tűzfal beállításait, a szolgáltatás címkéit, a privát végpontokat és egyebeket. Átmeneti problémák esetén a parancsok futtatásával ellenőrizhetők az eldobott csomagok, és a hálózati nyomkövetés beszerzése segíthet a hibák elhárításában.

Ez a cikk tippekkel szolgál a Azure Event Grid kapcsolódási problémáinak elhárításához.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Állandó csatlakozási problémák elhárítása

Ha az alkalmazás egyáltalán nem tud csatlakozni az Event gridhez, kövesse a jelen szakaszban ismertetett lépéseket a probléma megoldásához.

### <a name="check-if-theres-a-service-outage"></a>Ellenőrizze, hogy van-e leállás a szolgáltatásban

Keresse meg az Azure Event Grid szolgáltatás kimaradását az [Azure szolgáltatás állapota helyen](https://azure.microsoft.com/status/).

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Ellenőrizze, hogy a szervezet tűzfala nem blokkolja-e a Event Grid való kommunikációhoz szükséges portokat

Ellenőrizze, hogy a Azure Event Grid való kommunikáció során használt portok nincsenek-e letiltva a szervezet tűzfalán. Tekintse meg a következő táblázatot a kimenő portokhoz, amelyeket meg kell nyitni a Azure Event Gridsal való kommunikációhoz.

| Protokoll | Portok |
| -------- | ----- |
| HTTPS    | 443   |

Itt látható egy minta parancs, amely ellenőrzi, hogy az 443-es port blokkolva van-e.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Linux rendszeren:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Ellenőrizze, hogy az IP-címek engedélyezve vannak-e a vállalati tűzfalban

Ha az Azure-t használja, időnként engedélyeznie kell bizonyos IP-címtartományok vagy URL-címek használatát a vállalati tűzfalon vagy proxyn a használt összes Azure-szolgáltatás eléréséhez. Ellenőrizze, hogy engedélyezett-e a forgalom a Event Grid által használt IP-címeken. Az Azure Event Grid által használt IP-címek esetében lásd: az [Azure IP-címtartományok és a szolgáltatás címkéi – nyilvános felhő](https://www.microsoft.com/download/details.aspx?id=56519) és [szolgáltatás AzureEventGrid](network-security.md#service-tags).

Az [Azure IP-címtartományok és szolgáltatás-címkék – a nyilvános Felhőbeli](https://www.microsoft.com/download/details.aspx?id=56519) dokumentum az IP-címeket is felsorolja **régiónként**. A **témakör régiója** és a **párosított régió** a vállalati tűzfalon vagy proxyn is engedélyezheti a címtartományt. Egy adott régióhoz tartozó párosított régió esetében lásd [: Üzletmenet-folytonosság és vész-helyreállítási (BCDR): Azure párosított régiók](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Az új IP-címek hozzáadhatók a AzureEventGrid szolgáltatás címkéjéhez, bár ez nem szokásos. Ezért érdemes hetente megnézni a szolgáltatás címkéit.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Annak ellenőrzése, hogy a AzureEventGrid szolgáltatás címkéje engedélyezve van-e a hálózati biztonsági csoportokban

Ha az alkalmazás egy alhálózaton belül fut, és ha van hálózati biztonsági csoport, akkor ellenőrizze, hogy engedélyezett-e az internetes kimenő forgalom vagy a AzureEventGrid szolgáltatás címkéje. Lásd a [szolgáltatás címkéit](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>A témakör/tartomány IP-tűzfal beállításainak megtekintése

Győződjön meg arról, hogy az EventGrid-témakör/tartomány IP-tűzfala nem blokkolja az alkalmazást futtató számítógép nyilvános IP-címét.

Alapértelmezés szerint a Event Grid témakörök/tartományok az internetről érhetők el, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal a [CIDR (osztály nélküli Inter-Domain útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel tovább korlátozhatja az IPv4-címek vagy az IPv4-címtartományok körét.

Az IP-tűzfalszabályok a Event Grid témakör/tartomány szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. Minden olyan IP-címről érkező kapcsolódási kísérlet, amely nem felel meg egy engedélyezett IP-szabálynak a Event Grid témakör/tartományon, tiltottként van visszautasítva. A válasz nem említi az IP-szabályt.

További információ: az [IP-tűzfalszabályok konfigurálása Azure Event Grid témakörhöz/tartományhoz](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Az IP-tűzfal által blokkolt IP-címek keresése

Diagnosztikai naplók engedélyezése Event Grid témakörhöz/tartományhoz a [diagnosztikai naplók engedélyezése](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Ekkor megjelenik a megtagadott kapcsolatok IP-címe.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Ellenőrizze, hogy a EventGrid témakör/tartomány csak privát végponttal érhető-e el

Ha a Event Grid témakör/tartomány úgy van konfigurálva, hogy csak privát végponton keresztül legyen elérhető, ellenőrizze, hogy az ügyfélalkalmazás hozzáfér-e a témakörhöz/tartományhoz a privát végponton. A megerősítéshez ellenőrizze, hogy az ügyfélalkalmazás egy alhálózaton belül fut-e, és van-e privát végpont az adott alhálózat Event Grid témaköréhez/tartományához.

Az [Azure Private link Service](../private-link/private-link-overview.md) lehetővé teszi Azure Event Grid elérését a virtuális hálózat **privát végpontján** keresztül. A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [privát végpontok konfigurálása](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Átmeneti kapcsolódási problémák elhárítása

Ha időnkénti kapcsolódási problémákat tapasztal, a hibaelhárítási tippekért tekintse át a következő szakaszokat.

### <a name="run-the-command-to-check-dropped-packets"></a>Futtassa a parancsot az eldobott csomagok vizsgálatához

Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal a szolgáltatással 1 másodpercenként 25 különböző TCP-kapcsolatot kell létrehozni. Ezt követően megtekintheti, hogy a sikeres és sikertelen volt-e a TCP-kapcsolatok késése. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Ha más eszközöket (példáultcpping.exe) használ, egyenértékű parancsokat is használhat `tcpping` [ ](https://www.elifulkerson.com/projects/tcping.php).

Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik olyan eszközökkel, mint például a [Wireshark](https://www.wireshark.org/). Ha szükséges, forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) .

### <a name="service-upgradesrestarts"></a>Szolgáltatás-frissítések/újraindítások

Az átmeneti kapcsolódási problémák a háttérrendszer frissítése és újraindítása miatt merülhetnek fel. Ha ezek történnek, a következő tünetek jelenhetnek meg:

- Lehet, hogy elvesznek a bejövő üzenetek/kérelmek.
- A naplófájl hibaüzeneteket tartalmazhat.
- Előfordulhat, hogy az alkalmazások néhány másodpercig le lesznek választva a szolgáltatástól.
- Előfordulhat, hogy a kérelmek egy pillanatra szabályozva vannak.

Ezeknek az átmeneti hibáknak a kifogása, a biztonsági mentés és a hívás újbóli kipróbálása biztosítja, hogy a kód rugalmasan kezelje ezeket az átmeneti problémákat.

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).