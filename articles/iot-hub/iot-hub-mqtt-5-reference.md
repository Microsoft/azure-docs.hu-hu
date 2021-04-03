---
title: Azure IoT Hub MQTT 5 API-referenciája (előzetes verzió)
description: Tudnivalók a IoT Hub MQTT 5 API-referenciáról
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603331"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT Hub adatsík MQTT 5 API-referenciája

Ez a dokumentum a IoT Hub adatsík API 2,0-es verziójában (API-Version:) elérhető műveleteket határozza meg `2020-10-01-preview` .

## <a name="operations"></a>Üzemeltetés

### <a name="get-twin"></a>Get Twin

Kettős állapot lekérése

#### <a name="request"></a>Kérés

**Témakör neve:**`$iothub/twin/get`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

#### <a name="success-response"></a>Sikeres válasz

**Tulajdonságok**: nincs

**Hasznos** adatok: Twin

#### <a name="alternative-responses"></a>Alternatív válaszok

| Állapot | Név | Leírás |
| :----- | :--- | :---------- |
| 0100 |  Hibás kérés | A műveleti üzenet helytelen formátumú, ezért nem dolgozható fel. |
| 0101 |  Nincs engedélyezve | Az ügyfél nem jogosult a művelet végrehajtására. |
| 0102 |  Nem engedélyezett | A művelet nem engedélyezett. |
| 0501 |  Szabályozott | a kérelem sebessége túl magas az SKU-ban |
| 0502 |  Kvóta túllépve | az aktuális SKU-ra vonatkozó napi kvóta túllépve |
| 0601 |  Kiszolgálóhiba | belső kiszolgálóhiba |
| 0602 |  Időtúllépés | Időtúllépés történt a művelet végrehajtása előtt. |
| 0603 |  Kiszolgáló foglalt | kiszolgáló foglalt |

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Patch Twin jelentett

Patch Twin jelentett állapota

#### <a name="request"></a>Kérés

**Témakör neve:**`$iothub/twin/patch/reported`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| If-Version | u64 | nem |  |

**Hasznos** adatok: TwinState

#### <a name="success-response"></a>Sikeres válasz

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| version | u64 | igen | A jelentett állapot verziója a javítás alkalmazása után |

**Hasznos** adat: üres

#### <a name="alternative-responses"></a>Alternatív válaszok

| Állapot | Név | Leírás |
| :----- | :--- | :---------- |
| 0104 |  Sikertelen előfeltétel | az előfeltétel nem teljesült a kérelem megszakítása miatt. |
| 0100 |  Hibás kérés | A műveleti üzenet helytelen formátumú, ezért nem dolgozható fel. |
| 0101 |  Nincs engedélyezve | Az ügyfél nem jogosult a művelet végrehajtására. |
| 0102 |  Nem engedélyezett | A művelet nem engedélyezett. |
| 0501 |  Szabályozott | a kérelem sebessége túl magas az SKU-ban |
| 0502 |  Kvóta túllépve | az aktuális SKU-ra vonatkozó napi kvóta túllépve |
| 0601 |  Kiszolgálóhiba | belső kiszolgálóhiba |
| 0602 |  Időtúllépés | Időtúllépés történt a művelet végrehajtása előtt. |
| 0603 |  Kiszolgáló foglalt | kiszolgáló foglalt |

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Parancsok fogadása

Parancsok fogadása és kezelése

#### <a name="message"></a>Üzenet

**Témakör neve:**`$iothub/commands`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| sorozat – nem | u64 | igen | Az üzenet sorozatszáma |
| várólistán lévő-idő | time | igen | A Rendszerüzenet időbélyege, amikor a rendszeren beléptek |
| kézbesítések száma | u32 | igen | Az üzenet kézbesítési kísérletének száma |
| létrehozás ideje | time | nem | Az üzenet létrehozásának időbélyegzője (a küldő által biztosított) |
| üzenet-azonosító | sztring | nem | Üzenet identitása (a küldő által megadott) |
| felhasználói azonosító | sztring | nem | Felhasználói identitás (a küldő által megadott) |
| korrelációs azonosító | sztring | nem | Korrelációs identitás (a küldő által megadott) |
| Tartalom típusa | sztring | nem | meghatározza a hasznos adattartalom típusát |
| Content-Encoding | sztring | nem | meghatározza a hasznos adatok tartalmának kódolását |

**Hasznos** adat: bármely bájtos sorozatot

#### <a name="success-acknowledgment"></a>Sikeres visszaigazolás

Azt jelzi, hogy a parancs elfogadta az ügyfél általi kezelést.

**Tulajdonságok**: nincs

**Hasznos** adat: üres

#### <a name="alternative-acknowledgments"></a>Alternatív nyugták

| Okkód | Állapot | Név | Leírás |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Megszakítás | Azt jelzi, hogy a parancs jelenleg nem lesz feldolgozva, és a későbbiekben újra kell kézbesítésre kerül. |
| 131 | 0100 | Elutasítás | Azt jelzi, hogy az ügyfél elutasította a parancsot, és nem próbálkozzon újra. |

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Közvetlen metódusok fogadása

Közvetlen metódusok fogadása és kezelése

#### <a name="request"></a>Kérés

**Témakör neve:**`$iothub/methods/{name}`

**Tulajdonságok**: nincs

**Hasznos** adat: bármely bájtos sorozatot

#### <a name="success-response"></a>Sikeres válasz

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| Válasz kódja | u32 | igen |  |

**Hasznos** adat: bármely bájtos sorozatot

#### <a name="alternative-responses"></a>Alternatív válaszok

| Állapot | Név | Leírás |
| :----- | :--- | :---------- |
| 06A0 |  Nem érhető el | Azt jelzi, hogy az ügyfél nem érhető el ezen a kapcsolaton keresztül. |

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>A következő két kívánt állapotú változás fogadása

Frissítések fogadása a Twin kívánt állapothoz

#### <a name="message"></a>Üzenet

**Témakör neve:**`$iothub/twin/patch/desired`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| version | u64 | igen | A frissítéshez illő kívánt állapot verziója |

**Hasznos** adatok: TwinState

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Telemetria küldése

Üzenet küldése a telemetria Channel-EventHubs alapértelmezés szerint vagy más végpont útválasztási konfiguráción keresztül.

#### <a name="message"></a>Üzenet

**Témakör neve:**`$iothub/telemetry`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| Tartalom típusa | sztring | nem | fordítás a `content-type` System (rendszer) tulajdonságra a közzétett üzenetekben |
| Content-Encoding | sztring | nem | fordítás a `content-encoding` System (rendszer) tulajdonságra a közzétett üzenetekben |
| üzenet-azonosító | sztring | nem | fordítás a `message-id` System (rendszer) tulajdonságra a közzétett üzenetekben |
| felhasználói azonosító | sztring | nem | fordítás a `user-id` System (rendszer) tulajdonságra a közzétett üzenetekben |
| korrelációs azonosító | sztring | nem | fordítás a `correlation-id` System (rendszer) tulajdonságra a közzétett üzenetekben |
| létrehozás ideje | time | nem | a tulajdonság fordítása `iothub-creation-time-utc` a közzétett üzenetekben |

**Hasznos** adat: bármely bájtos sorozatot

#### <a name="success-acknowledgment"></a>Sikeres visszaigazolás

Az üzenet sikeresen közzétéve a telemetria csatornán

**Tulajdonságok**: nincs

**Hasznos** adat: üres

#### <a name="alternative-acknowledgments"></a>Alternatív nyugták

| Okkód | Állapot | Név | Leírás |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Hibás kérés | A műveleti üzenet helytelen formátumú, ezért nem dolgozható fel. |
| 135 | 0101 | Nincs engedélyezve | Az ügyfél nem jogosult a művelet végrehajtására. |
| 131 | 0102 | Nem engedélyezett | A művelet nem engedélyezett. |
| 131 | 0601 | Kiszolgálóhiba | belső kiszolgálóhiba |
| 151 | 0501 | Szabályozott | a kérelem sebessége túl magas az SKU-ban |
| 151 | 0502 | Kvóta túllépve | az aktuális SKU-ra vonatkozó napi kvóta túllépve |
| 131 | 0602 | Időtúllépés | Időtúllépés történt a művelet végrehajtása előtt. |
| 131 | 0603 | Kiszolgáló foglalt | kiszolgáló foglalt |

#### <a name="pseudo-code-sample"></a>Pszeudo-kód minta

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Válaszok

### <a name="bad-request"></a>Hibás kérés

A műveleti üzenet helytelen formátumú, ezért nem dolgozható fel.

**Okkód:** `131`

**Állapot:**`0100`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="conflict"></a>Ütközés

A művelet ütközik egy másik folyamatban lévő művelettel.

**Okkód:** `131`

**Állapot:**`0103`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="not-allowed"></a>Nem engedélyezett

A művelet nem engedélyezett.

**Okkód:** `131`

**Állapot:**`0102`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="not-authorized"></a>Nincs engedélyezve

Az ügyfél nem jogosult a művelet végrehajtására.

**Okkód:** `135`

**Állapot:**`0101`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |

**Hasznos** adat: üres

### <a name="not-found"></a>Nem található

a kért erőforrás nem létezik

**Okkód:** `131`

**Állapot:**`0504`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="not-modified"></a>Nincs módosítva

Az erőforrás nem lett módosítva a megadott előfeltétel alapján.

**Okkód:** `0`

**Állapot:**`0001`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

### <a name="precondition-failed"></a>Sikertelen előfeltétel

Az előfeltétel nem teljesült a kérelem megszakítása miatt.

**Okkód:** `131`

**Állapot:**`0104`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

### <a name="quota-exceeded"></a>Kvóta túllépve

az aktuális SKU-ra vonatkozó napi kvóta túllépve

**Okkód:** `151`

**Állapot:**`0502`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

### <a name="resource-exhausted"></a>Erőforrás kimerítve

az erőforrás nem rendelkezik a művelet befejezéséhez szükséges kapacitással.

**Okkód:** `131`

**Állapot:**`0503`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="server-busy"></a>Kiszolgáló foglalt

kiszolgáló foglalt

**Okkód:** `131`

**Állapot:**`0603`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |

**Hasznos** adat: üres

### <a name="server-error"></a>Kiszolgálóhiba

belső kiszolgálóhiba

**Okkód:** `131`

**Állapot:**`0601`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |

**Hasznos** adat: üres

### <a name="target-failed"></a>A cél nem sikerült

A cél válaszolt, de a válasz érvénytelen vagy helytelen formátumú.

**Okkód:** `131`

**Állapot:**`06A2`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="target-timeout"></a>Cél időtúllépése

Időtúllépés történt a kérelem befejezésére való várakozáskor

**Okkód:** `131`

**Állapot:**`06A1`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |
| reason | sztring | nem | információt tartalmaz arról, hogy mi a konkrétan nem érvényes az üzenetre vonatkozóan |

**Hasznos** adat: üres

### <a name="target-unavailable"></a>A cél nem érhető el

A cél nem érhető el a kérelem teljesítéséhez

**Okkód:** `131`

**Állapot:**`06A0`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

### <a name="throttled"></a>Szabályozott

a kérelem sebessége túl magas az SKU-ban

**Okkód:** `151`

**Állapot:**`0501`

**Tulajdonságok**: nincs

**Hasznos** adat: üres

### <a name="timeout"></a>Időtúllépés

Időtúllépés történt a művelet végrehajtása előtt.

**Okkód:** `131`

**Állapot:**`0602`

**Tulajdonságok**:

| Név | Típus | Kötelező | Leírás |
| :--- | :--- | :------- | :---------- |
| nyomkövetési azonosító | sztring | nem | nyomkövetési azonosító a hiba további diagnosztikája esetén |

**Hasznos** adat: üres

