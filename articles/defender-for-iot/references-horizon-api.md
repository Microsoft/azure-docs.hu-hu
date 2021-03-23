---
title: Horizon API
description: Ez az útmutató a gyakran használt horizont módszereit ismerteti.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786787"
---
# <a name="horizon-api"></a>Horizon API 

Ez az útmutató a gyakran használt horizont módszereit ismerteti.

### <a name="getting-more-information"></a>További információk

A Horizon és a Defender for IoT platform használatáról további információt a következő információkban talál:

- A Horizon Open Development Environment (ODE) SDK esetében forduljon a Defender for IoT képviselőjéhez.
- A támogatási és hibaelhárítási információkért forduljon a következőhöz: <support@cyberx-labs.com> .

- A Defender for IoT felhasználói útmutatójának a Defender for IoT-konzolról való eléréséhez válassza a :::image type="icon" source="media/references-horizon-api/profile.png"::: **felhasználói útmutató letöltése** lehetőséget.


## `horizon::protocol::BaseParser`

Absztrakt minden beépülő modulhoz. Ez két módszerből áll:

- A fentiekben megadott plugin-szűrők feldolgozásához. Így a horizonton tudja, hogyan kommunikálhat az elemzővel.
- A tényleges adatok feldolgozásához.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

A beépülő modulhoz hívott első függvény létrehozza az elemző egy példányát a horizonton, hogy felismerje és regisztrálja azt.

### <a name="parameters"></a>Paraméterek 

Nincsenek.

### <a name="return-value"></a>Visszatérési érték

shared_ptr az elemző példányra.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Ezt a függvényt a rendszer minden fent regisztrált beépülő modulhoz meghívja. 

A legtöbb esetben ez üres lesz. Kivételt jelez a horizonton, hogy valami rossz történt.

### <a name="parameters"></a>Paraméterek 

- Dissect_as struktúráját tartalmazó Térkép, amely egy másik beépülő modul config.jsján van meghatározva, amelyet szeretne regisztrálni.

### <a name="return-value"></a>Visszatérési érték 

Uint64_t egy tömbje, amely a regisztrációt egy uint64_tba dolgozza fel. Ez azt jelenti, hogy a térképen a portok listája jelenik meg, amelyek értéke a uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

A fő függvény. Pontosabban, a beépülő modul logikája, valahányszor egy új csomag eléri az elemzőt. Ezt a függvényt a rendszer a csomagok feldolgozásával kapcsolatos összes adat meghívása után itt végezheti el.

### <a name="considerations"></a>Megfontolandó szempontok

A beépülő modulnak biztonságos szálnak kell lennie, mivel ez a függvény különböző szálakból hívható meg. A megfelelő módszer a veremben lévő összes érték meghatározása.

### <a name="parameters"></a>Paraméterek

- Az adat tárolására és az SDK-val kapcsolatos objektumok (például ILayer és mezők) létrehozásához felelős SDK-vezérlő egység.
- A nyers csomag adatolvasására szolgáló segítő. Már be van állítva a config.jsban megadott bájtos sorrendtel.

### <a name="return-value"></a>Visszatérési érték 

A feldolgozás eredménye. Ez lehet *sikeres*, *helytelen formátumú* vagy *józan ész*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Kijelöli a feldolgozást egészségügyi hibaként, ami azt jelenti, hogy az aktuális protokoll nem ismeri fel a csomagot

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Paraméterek 

- Meghatározza a horizont által a naplózáshoz használt hibakódot, ahogy azt a config.jsbe van állítva.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Helytelenül formázott eredmény, jelezve, hogy a csomagot már felismertük a protokollként, de néhány érvényesítési hiba történt (a fenntartott bitek be vannak kapcsolva, vagy hiányzik egy mező).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Paraméterek  

- Hibakód a config.json megadottak szerint.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

A sikeres feldolgozás horizontjának értesítése. Ha a művelet sikeres, a csomag el lett fogadva, az adategységek a hozzánk tartoznak, és az összes adattal kivonták.

## `horizon::protocol::SuccessResult()`

Konstruktor. Egy alapszintű sikeres eredmény lett létrehozva. Ez azt jelenti, hogy a csomagra vonatkozó irány vagy egyéb metaadatok nem.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Konstruktor.

### <a name="parameters"></a>Paraméterek 

- A csomag iránya, ha meg van határozva. Az értékek lehetnek *kérések* vagy *válaszok*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Konstruktor.

### <a name="parameters"></a>Paraméterek

- A csomag iránya, ha azonosította, *kérhető*, *választ* kaphat.
- Figyelmeztetések. Ezek az események nem lesznek sikertelenek, de a rendszer a horizontot értesíti.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Konstruktor.

### <a name="parameters"></a>Paraméterek 

-  Figyelmeztetések. Ezek az események nem lesznek sikertelenek, de a rendszer a horizontot értesíti.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Karakterlánc-alapú hivatkozást alakít át egy mezőnév (például function_code) HorizonID.

### <a name="parameters"></a>Paraméterek 

- Az átalakítandó karakterlánc.

### <a name="return-value"></a>Visszatérési érték

- A sztringből létrehozott HorizonID.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Létrehoz egy új réteget, így a horizont tudni fogja, hogy a beépülő modul szeretné tárolni az egyes adatfájlokat. Ez az alapszintű tárolási egység, amelyet használnia kell.

### <a name="return-value"></a>Visszatérési érték

Egy létrehozott rétegre mutató hivatkozás, amellyel hozzá lehet adni az adatkészlethez.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Beolvassa a mező felügyeleti objektumát, amely a mezők különböző objektumokon, például a ILayer való létrehozásához felelős.

### <a name="return-value"></a>Visszatérési érték

A felettesre mutató hivatkozás.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Létrehoz egy 64 bites új numerikus mezőt a rétegen a kért AZONOSÍTÓval.

### <a name="parameters"></a>Paraméterek 

- A korábban létrehozott réteg.
- A **HORIZON_FIELD** makró által létrehozott HorizonID.
- A tárolni kívánt nyers érték.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Egy új karakterlánc-mezőt hoz létre a rétegen a kért AZONOSÍTÓval. A rendszer áthelyezi a memóriát, ezért ügyeljen rá. Ezt az értéket nem fogja tudni újra használni.

### <a name="parameters"></a>Paraméterek  

- A korábban létrehozott réteg.
- A **HORIZON_FIELD** makró által létrehozott HorizonID.
- A tárolni kívánt nyers érték.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Létrehoz egy új nyers értéket (bájtok tömbjét) a rétegen, a kért AZONOSÍTÓval. A memória mozgásba kerül, ezért legyen körültekintő, nem fogja tudni használni ezt az értéket.

### <a name="parameters"></a>Paraméterek

- A korábban létrehozott réteg.
- A **HORIZON_FIELD** makró által létrehozott HorizonID.
- A tárolni kívánt nyers érték.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Létrehoz egy Array értéket (Array) a megadott típusú rétegen a kért AZONOSÍTÓval.

### <a name="parameters"></a>Paraméterek

- A korábban létrehozott réteg.
- A **HORIZON_FIELD** makró által létrehozott HorizonID.
- A tömbön belül tárolt értékek típusa

### <a name="return-value"></a>Visszatérési érték

Hivatkozás egy olyan tömbre, amelynek értékeket kell hozzáfűzni.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Hozzáfűz egy új egész értéket a korábban létrehozott tömbhöz.

### <a name="parameters"></a>Paraméterek

- A korábban létrehozott tömb.
- A tömbben tárolni kívánt nyers érték.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Hozzáfűz egy új karakterlánc-értéket a korábban létrehozott tömbhöz. A memória mozgásba kerül, ezért legyen körültekintő, nem fogja tudni használni ezt az értéket.

### <a name="parameters"></a>Paraméterek

- A korábban létrehozott tömb.
- A tömbben tárolni kívánt nyers érték.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Hozzáfűz egy új nyers értéket a korábban létrehozott tömbhöz. A memória mozgásba kerül, ezért legyen körültekintő, nem fogja tudni használni ezt az értéket.

### <a name="parameters"></a>Paraméterek

- A korábban létrehozott tömb.
- A tömbben tárolni kívánt nyers érték.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Ellenőrzi, hogy a puffer legalább X bájtot tartalmaz-e.

### <a name="parameters"></a>Paraméterek

A létező bájtok száma.

### <a name="return-value"></a>Visszatérési érték

Értéke true, ha a puffer legalább X bájtot tartalmaz. Ellenkező esetben ez a `False` .

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

A uint8 értékének (1 bájt) beolvasása a pufferből a bájtok sorrendjének megfelelően.

### <a name="return-value"></a>Visszatérési érték

A pufferből beolvasott érték

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

A UInt16 értékének (2 bájt) beolvasása a pufferből a bájtok sorrendjének megfelelően.

### <a name="return-value"></a>Visszatérési érték

A pufferből beolvasott érték

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

A UInt32 értékének (4 bájt) beolvasása a pufferből a bájt sorrend alapján.

### <a name="return-value"></a>Visszatérési érték

A pufferből beolvasott érték

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

A UInt64 értékének (8 bájt) beolvasása a pufferből a bájtok sorrendjének megfelelően.

### <a name="return-value"></a>Visszatérési érték

A pufferből beolvasott érték

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

A megadott méretű, előre lefoglalt memóriába való beolvasás ténylegesen az adatokat másolja a memória régiójába.

### <a name="parameters"></a>Paraméterek 

- Az a memóriaterület, amelybe át kell másolni az adatterületet.
- A memória régiójának mérete, ez a paraméter azt is meghatározza, hogy hány bájtot másol a rendszer.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Beolvas egy karakterláncot a pufferből.

### <a name="parameters"></a>Paraméterek 

- Az olvasni kívánt bájtok száma.

### <a name="return-value"></a>Visszatérési érték

A karakterlánc memória régiójára mutató hivatkozás.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Megadja, hogy hány bájt marad a pufferben.

### <a name="return-value"></a>Visszatérési érték

A puffer fennmaradó mérete

## `void horizon::general::IDataBuffer::skip(size_t)`

Kihagyja az X bájtot a pufferben.

### <a name="parameters"></a>Paraméterek

- A kihagyni kívánt bájtok száma.
