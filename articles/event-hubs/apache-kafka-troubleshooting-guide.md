---
title: Az Azure Event Hubs Apache Kafka kapcsolatos hibáinak elhárítása
description: Ez a cikk bemutatja, hogyan lehet elhárítani a problémákat az Azure Event Hubs for Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e32e02947b9f004755381d562fd3f3c897b70674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061427"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Event Hubs Apache Kafka hibaelhárítási útmutatója
Ez a cikk hibaelhárítási tippeket nyújt a Event Hubs Apache Kafka való használatakor futtatott problémákhoz. 

## <a name="server-busy-exception"></a>Kiszolgáló foglalt kivétele
A Kafka-szabályozás miatt előfordulhat, hogy a kiszolgáló foglalt kivételt okoz. A AMQP-ügyfelekkel a Event Hubs azonnal visszaadja a **kiszolgáló számára foglalt** kivételt a szolgáltatás szabályozása során. Ez egyenértékű a "Try Again később" üzenettel. A Kafka-ben az üzenetek a befejezés előtt késleltetve lesznek. A késleltetési hossz ezredmásodpercben, `throttle_time_ms` a termék/beolvasási válaszban is visszaadja. A legtöbb esetben ezek a késleltetett kérelmek nem kerülnek be kiszolgálóként foglalt kivételként Event Hubs irányítópultokon. Ehelyett a válasz értékét olyan `throttle_time_ms` jelzésként kell használni, amely túllépte a kiosztott kvótát.

Ha a forgalom túlzott, a szolgáltatás a következő viselkedéssel rendelkezik:

- Ha a előállítási kérelem késleltetése meghaladja a kérelmek időtúllépését, Event Hubs visszaadja a **szabályzat megsértésének** hibakódját.
- Ha a kérelem késleltetése meghaladja a kérelmek időtúllépését, Event Hubs naplózza a kérést, és üres rekordokkal válaszol, és nem tartalmaz hibakódot.

A [dedikált fürtök](event-hubs-dedicated-overview.md) nem rendelkeznek szabályozási mechanizmusokkal. Ingyenesen használhatja a fürt összes erőforrását.

## <a name="no-records-received"></a>Nem érkezett rekord
Láthatja, hogy a felhasználók nem kapnak semmilyen rekordot, és folyamatosan kiegyensúlyozzák azokat. Ebben a forgatókönyvben a fogyasztók nem kapnak semmilyen rekordot és folyamatosan kiegyensúlyozzák azokat. Nincs kivétel vagy hiba, ha ez megtörténik, de a kafka-naplók azt mutatják, hogy a felhasználók elragadják a csoport újracsatlakoztatását és a partíciók hozzárendelését. Néhány lehetséges ok:

- Győződjön meg arról, hogy `request.timeout.ms` legalább a 60000 javasolt értéke, és az értéke legalább `session.timeout.ms` a 30000 ajánlott. Ha ezek a beállítások túl alacsonyak, a fogyasztói időtúllépéseket okozhatnak, ami után a rendszer kiegyensúlyozza az újraelosztást (ami több időtúllépést okoz, ami nagyobb kiegyensúlyozást eredményezhet stb.) 
- Ha a konfiguráció megfelel az ajánlott értékeknek, és továbbra is állandó kiegyensúlyozásra van szüksége, nyugodtan megnyithatja a problémát (a probléma megoldásához vegye fel a teljes konfigurációt, hogy segítsen a hibakeresésben)!

## <a name="compressionmessage-format-version-issue"></a>Tömörítés/üzenet formátumának verziószáma
A Kafka támogatja a tömörítést, és a Kafka-Event Hubs jelenleg nem. Az üzenet formátumát (például) megemlítő hibák oka az, hogy `The message format version on the broker does not support the request.` az ügyfél tömörített Kafka-üzeneteket próbál küldeni a brókereknek.

Ha tömörített adatokra van szükség, tömörítse az adatokat, mielőtt elküldi azt a brókereknek, és a rendszer kibontja a befogadást követően érvényes megkerülő megoldást. Az üzenettörzs csak egy bájtos tömb a szolgáltatás számára, így az ügyféloldali tömörítés/kitömörítés nem okoz problémát.

## <a name="unknownserverexception"></a>UnknownServerException
A Kafka-UnknownServerException az alábbi példához hasonló módon jelenhetnek meg: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Nyisson meg egy jegyet a Microsoft ügyfélszolgálatával.  A hibakeresési szintű naplózás és a kivétel időbélyege az UTC-ben hasznos a probléma hibakereséséhez. 

## <a name="other-issues"></a>Egyéb problémák
Ha a Kafka Event Hubs-on való használata során problémák merülnek fel, tekintse meg a következő elemeket.

- **Tűzfal blokkolja a forgalmat** – ellenőrizze, hogy a tűzfal nem blokkolja-e a **9093** -es portot.
- **TopicAuthorizationException** – a kivétel leggyakoribb okai a következők:
    - Egy elírás a konfigurációs fájlban lévő kapcsolatok karakterláncában, vagy
    - A Event Hubs for Kafka alapszintű névtérben való használatának megkísérlése. A Kafka szolgáltatáshoz tartozó Event Hubs [csak a standard és a dedikált szintű névterek esetében támogatott](https://azure.microsoft.com/pricing/details/event-hubs/).
- A Kafka-verziókhoz való Event Hubs a Kafka-ökoszisztémák esetében a Kafka **1,0-es** és újabb verzióit támogatja. Néhány, a Kafka 0,10-es vagy újabb verzióját használó alkalmazás időnként működhet a Kafka protokoll visszamenőleges kompatibilitása miatt, de erősen ajánlott a régi API-verziók használata. A Kafka 0,9-es és korábbi verziói nem támogatják a szükséges SASL-protokollokat, és nem tudnak csatlakozni a Event Hubshoz.
- **Furcsa kódolások a AMQP-fejléceken a Kafka** használatakor – amikor eseményeket küld az Event hub-ra a AMQP-en keresztül, a AMQP-tartalom fejlécei AMQP kódolással vannak szerializálva. A Kafka-fogyasztók nem deszerializálják a fejléceket a AMQP. A fejléc értékeinek olvasásához manuálisan dekódolja a AMQP-fejléceket. Azt is megteheti, hogy elkerüli a AMQP-fejléceket, ha tudja, hogy a Kafka protokollon keresztül fogja használni. További információkért tekintse meg [ezt a GitHub-problémát](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **SASL-hitelesítés** – a keretrendszernek a Event Hubs által megkövetelt hitelesítési protokollal való együttműködésre való beszerzése nehezebb, mint a szemnek. Ellenőrizze, hogy a SASL-hitelesítéshez használt keretrendszer erőforrásaival hogyan lehet elhárítani a konfigurációt. 

## <a name="limits"></a>Korlátok
Apache Kafka és Event Hubs Kafka. A legtöbb esetben az Azure Event Hubs Kafka felülete ugyanazokat az alapértékeket, tulajdonságokat, hibakódokat és általános viselkedést eredményezi, mint Apache Kafka. A két explicit módon eltérő példányok (vagy ahol a Event Hubs korlátozza, hogy a Kafka nem) alább látható:

- A tulajdonság maximális hossza `group.id` 256 karakter.
- A maximális méret `offset.metadata.max.bytes` 1024 bájt
- Az eltolás véglegesíti a másodpercenkénti 4 hívás/másodperc értékre, a belső naplók maximális mérete 1 MB


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

- [Apache Kafka fejlesztői útmutató Event Hubs](apache-kafka-developer-guide.md)
- [A Event Hubs Apache Kafka áttelepítési útmutatója](apache-kafka-migration-guide.md)
- [Gyakori kérdések – Event Hubs Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Ajánlott konfigurációk](apache-kafka-configurations.md)
