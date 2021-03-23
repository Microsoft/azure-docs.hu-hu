---
title: Horizon SDK
description: A Horizon SDK lehetővé teszi, hogy az Azure Defender IoT-fejlesztőknek tervezzen olyan, a hálózati forgalmat dekódolást nem támogató beépülő modulokat, amelyeket a IoT Network Analysis Programs automatizált Defender feldolgozhat.
ms.date: 1/13/2021
ms.topic: article
ms.openlocfilehash: 500f1d78a6a2ab91284c993a5c56ef8e679db83f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782486"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Horizont-tulajdonosi protokollok

A Horizon egy nyílt fejlesztési környezet (ODE), amely a saját protokollokat futtató IoT és ICS-eszközök védelmére szolgál.

Ez a környezet a következő megoldásokat kínálja az ügyfeleknek és a technológiai partnereknek:

- Korlátlan, teljes körű támogatás az olyan általános, tulajdonosi, egyéni protokollok vagy protokollok esetében, amelyek bármely standardtól eltérnek. 

- A DPI-fejlesztés új rugalmassági szintje és hatóköre.

- Egy eszköz, amely exponenciálisan kibővíti az OT láthatóságát és szabályozását anélkül, hogy a Defendert a IoT platform verziójára kellene frissítenie.

- A védett fejlesztés engedélyezése a bizalmas adatok nyilvánosságra hozatala nélkül.

A Horizon SDK lehetővé teszi, hogy az Azure Defender IoT-fejlesztőknek tervezzen olyan, a hálózati forgalmat dekódolást nem támogató beépülő modulokat, amelyeket a IoT Network Analysis Programs automatizált Defender feldolgozhat.

A protokollok nem szektorai külső beépülő modulokként lettek kifejlesztve, és a Defender számos IoT-szolgáltatással vannak integrálva. Például a figyelést, riasztást és jelentéskészítési képességeket biztosító szolgáltatások.

## <a name="secure-development-environment"></a>Biztonságos fejlesztési környezet 

A Horizon ODE lehetővé teszi olyan egyéni vagy saját protokollok fejlesztését, amelyek nem oszthatók meg a szervezeten kívül. Például jogi szabályozások vagy vállalati szabályzatok miatt.

A nem ágazati beépülő modulok fejlesztése: 

- a protokollok definiálásával kapcsolatos bármilyen tulajdonosi információ felfedése.

- a bizalmas PCAPs megosztása.

- a megfelelőségi rendeletek megsértése.

## <a name="customization-and-localization"></a>Testreszabás és honosítás  

Az SDK különböző testreszabási lehetőségeket támogat, többek között a következőket:

  - A függvények kódjának szövege. 

  - A riasztások, események és protokollok paramétereinek teljes honosítási szövege. További információ: [Mapping Files (JSON) létrehozása](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Teljes honosított riasztások megtekintése.":::

## <a name="horizon-architecture"></a>Horizont architektúra

Az építészeti modell három termék réteget tartalmaz.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender a IoT platform réteghez

Lehetővé teszi az egyéni nem ágazati beépülő modulok azonnali integrációját és valós idejű figyelését a Defender for IoT platformon, anélkül, hogy frissítenie kellene a Defender for IoT platform verzióját.

## <a name="defender-for-iot-services-layer"></a>Defender a IoT Services réteghez

Minden szolgáltatás egy folyamatként van kialakítva, amely egy adott protokolltól leválasztva hatékonyabb, független fejlesztést tesz lehetővé.

Minden szolgáltatás egy folyamatként van kialakítva, amely egy adott protokolltól van leválasztva. A szolgáltatások figyelik a folyamat forgalmát. A beépülő modulok adataival és az érzékelők által rögzített forgalommal működnek együtt az üzembe helyezett protokollok indexelésével és a forgalmi adatok elemzésével, valamint hatékonyabb és független fejlesztést tesznek lehetővé.

## <a name="custom-dissector-layer"></a>Egyéni nem ágazati réteg 

Lehetővé teszi a bővítmények létrehozását a Defender használatával a IoT tulajdonosi SDK-val (beleértve a C++ implementációt és a JSON-konfigurációt) a következőkre: 

- A protokoll azonosításának meghatározása

- Itt adhatja meg, hogy miként képezhető le a forgalomból kinyerni kívánt mezők, és kinyerheti őket 

- A Defender for IoT Services integrálásának meghatározása

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="A beépített rétegek.":::

A Defender for IoT alapszintű, általános protokollokat tartalmaz. Ezeket a protokollokat felépítheti a nem szektorokra.

## <a name="before-you-begin"></a>Előkészületek

## <a name="what-this-sdk-contains"></a>Az SDK tartalma 

Ez a csomag a fejlesztéshez szükséges fejléc-fájlokat tartalmazza. A fejlesztési folyamathoz az SDK-ban leírt alapszintű lépések és választható speciális lépések szükségesek.

A <support@cyberx-labs.com> fejléc-fájlok és egyéb erőforrások fogadásával kapcsolatos információkért forduljon a kapcsolathoz.

## <a name="about-the-environment-and-setup"></a>Tudnivalók a környezetről és a telepítésről 

### <a name="requirements"></a>Követelmények 

- Az előnyben részesített fejlesztői környezet a Linux. Ha Windows-környezetben fejleszt, érdemes lehet Linux rendszerű virtuális gépet használni.

- A fordítási folyamathoz használja a GCC 7.4.0 vagy újabb verzióját. Használjon a C++ 17 által támogatott szabványos osztályokat a stdlib.

- Defender a IoT 3,0-es vagy újabb verziójára.

### <a name="process"></a>Folyamat

1. [Töltse le](https://www.eclipse.org/) a C/C++ fejlesztőknek készült Eclipse ide-t. Bármilyen más, Ön által előnyben részesített IDE-t használhat. Ez a dokumentum végigvezeti Önt az Eclipse IDE használatával történő konfiguráláson.

1. Az Eclipse IDE elindítása és a munkaterület (ahol a projektek lesznek tárolva) beállítása után nyomja le a **Ctrl + n** billentyűkombinációt, és hozzon létre egy C++ projektként.

1. A következő képernyőn állítsa be a létrehozni kívánt protokoll nevét, és válassza ki a projekt típusát a és a értékre `Shared Library` `AND Linux GCC` .

1. Szerkessze a projekt tulajdonságait a **C/C++ Build**  >  **Settings**  >  **eszköz beállítások**  >  **GCC C++ fordítóprogram**  >  **egyéb**  >  **kullancs pozíciójának független kódjában**.

1. Illessze be az SDK-val kapott példa kódokat, és fordítsa le.

1. Adja hozzá az összetevőket (függvénytár, config.jsés metaadatok) egy tar. gz fájlhoz, és módosítsa a fájlkiterjesztést a \<XXX> . HDP értékre, ahol a a \<XXX> bővítmény neve.

### <a name="research"></a>Kutatási 

Mielőtt elkezdené, ellenőrizze, hogy:

- Ha elérhető, olvassa el a protokoll specifikációját.

- A kiolvasni kívánt protokollok mezőinek ismerete.

- Megtervezte a leképezési célkitűzéseket.

## <a name="about-plugin-files"></a>A bővítmények fájljai 

A fejlesztési folyamat során három fájl van definiálva.

### <a name="json-configuration-file-required"></a>JSON-konfigurációs fájl (kötelező) 

Ennek a fájlnak meg kell határoznia a nem ágazati azonosítót, a deklarációkat, a függőségeket, az integrációs követelményeket, az érvényesítési paramétereket és a leképezési definíciókat, hogy az értékeket a nevekre, a szövegekre További információkért tekintse meg az alábbi hivatkozásokat:

- [A konfigurációs fájl (JSON) előkészítése](#prepare-the-configuration-file-json)

- [Implementációs kód érvényességének előkészítése](#prepare-implementation-code-validations)

- [Eszköz metaadatainak kinyerése](#extract-device-metadata)

- [Kapcsolódás indexelő szolgáltatáshoz (alapterv)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Implementációs kód: C++ (kötelező)

A megvalósítási kód (CPP) elemzi a nyers forgalmat, és azokat olyan értékekre képezi, mint például a szolgáltatások, az osztályok és a függvények kódja. Kibontja a réteg mezőit, és leképezi őket a JSON-konfigurációs fájlokban lévő index nevükre. A CPP-ből kinyerni kívánt mezők a konfigurációs fájlban vannak meghatározva. További információ: [a implementációs kód előkészítése (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Fájlok leképezése (nem kötelező)

A beépülő modul kimeneti szövege testreszabható úgy, hogy megfeleljen a vállalati környezet igényeinek.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="áttelepítési":::

Megadhatja és frissítheti a leképezési fájlokat úgy, hogy a kód módosítása nélkül frissítse a szöveget. Minden fájl egy vagy több mezőt is leképez:

  - Mezőértékek leképezése nevekre, például 1: alaphelyzetbe állítás, 2: indítás, 3: leállítás.

  - Szöveg leképezése több nyelv támogatásához.

További információ: [Mapping Files (JSON) létrehozása](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Nem ágazati beépülő modul létrehozása (áttekintés)

1. Tekintse át a [környezetet és a telepítést](#about-the-environment-and-setup) ismertető szakaszt.

2.  [Készítse elő a megvalósítási kódot (C++)](#prepare-the-implementation-code-c). Másolja a **template.js** fájlt, és szerkessze az igényeinek megfelelően. Ne módosítsa a kulcsokat. 

3. [Készítse elő a konfigurációs fájlt (JSON)](#prepare-the-configuration-file-json). Másolja a **template. cpp** fájlt, és hajtson végre egy felülbírálási metódust. További információkért lásd [: horizont::p rotocol:: BaseParser](#horizonprotocolbaseparser) .

4. [Készítse elő a implementációs kód érvényességét](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>A megvalósítási kód (C++) előkészítése

A CPP-fájl a következőkért felelős elemző:

- A csomag fejlécének és hasznos adatainak (például a fejléc hosszának vagy a hasznos adatok struktúrájának) ellenőrzése.

- Adatok kinyerése a fejlécből és a hasznos adatokat definiált mezőkbe.

- A konfigurált mezők végrehajtása a JSON-fájl alapján.

### <a name="what-to-do"></a>Teendő

Másolja a Template **. cpp** fájlt, és hajtson végre egy felülbírálási metódust. További információkért lásd [: horizont::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Alapszintű C++ sablon minta 

Ez a szakasz az alapszintű protokoll sablonját tartalmazza, a IoT Horizon Protocol-hoz készült minta Defender standard funkcióival.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Alapszintű C++ sablon leírása  

Ez a szakasz az alapszintű protokoll sablonját tartalmazza, amely a IoT Horizon protokollhoz tartozó minta Defender standard függvényeit ismerteti. 

### <a name="include-pluginpluginh"></a>#include "beépülő modul/plugin. h"

A beépülő modul által használt definíció. A fejlécben minden szükséges a fejlesztés befejezéséhez.

### <a name="horizonprotocolbaseparser"></a>horizont::p rotocol:: BaseParser

A horizont infrastruktúra és a beépülő modul rétege közötti kommunikációs interfész. További információ: a [horizont architektúrája](#horizon-architecture) a rétegek áttekintéséhez.

A processLayer az adatfeldolgozáshoz használt metódus.

- A függvény kódjának első paramétere a korábban feldolgozott adatok beolvasására szolgáló feldolgozó segédprogram, új mezők és rétegek létrehozása.

- A függvény kódjának második paramétere az előző elemző által átadott aktuális adat.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

A használatával hozza létre az elemző példányát.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Protokoll-függvény kódja minta 

Ez a szakasz a kód számának (2 bájt) és az üzenetek hosszának (4 bájt) kibontását mutatja be.

Ez a JSON konfigurációs fájlban megadott endianness szerint történik, ami azt jelenti, hogy ha a protokoll *kis endianness*, és az érzékelő kis endianness rendelkező gépen fut, akkor a rendszer átalakítja.

A rendszer létrehoz egy réteget is az adattároláshoz. Új mezők létrehozásához használja a Processing utils *fieldsManager* . Egy mezőhöz a következő típusok tartozhatnak: *karakterlánc*, *szám*, *nyers érték*, *tömb* (adott típus) vagy *összetett*. Ez a réteg tartalmazhatja az AZONOSÍTÓval rendelkező számot, nyers vagy karakterláncot.

Az alábbi példában a következő két mező van kibontva:

- `function_code_number`

- `headerLength`

Létrejön egy új réteg, és a rendszer átmásolja a kibontott mezőt.

Az alábbi minta egy adott függvényt ír le, amely a beépülő modul feldolgozásának fő logikája.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Kapcsolódó JSON-mező 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="A kapcsolódó JSON-mező.":::

## <a name="prepare-the-configuration-file-json"></a>A konfigurációs fájl (JSON) előkészítése

A Horizon SDK a standard JavaScript Object Notation (JSON), az adattárolásra és-átvitelre szolgáló, egyszerű formátumot használ, és nem igényli a saját programozási nyelv használatát.

Ez a szakasz a JSON-konfiguráció minimális listáját, valamint a kapcsolódó struktúrát ismerteti, és egy olyan minta konfigurációs fájlt biztosít, amely meghatározza a protokollt. Ez a protokoll automatikusan integrálva van az eszköz felderítési szolgáltatásával.

## <a name="file-structure"></a>Fájlstruktúra

Az alábbi minta a fájl szerkezetét írja le.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="A fájl struktúrájának mintája.":::

### <a name="what-to-do"></a>Teendő

Másolja a sablonfájlt `config.json` , és szerkessze, hogy megfeleljen az igényeinek. Ne változtassa meg a kulcsot. A kulcsok piros színnel vannak megjelölve a [minta JSON konfigurációs fájlban](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Fájl elnevezési követelményei 

A JSON-konfigurációs fájlt menteni kell `config.json` .

### <a name="json-configuration-file-fields"></a>JSON konfigurációs fájl mezői

Ez a szakasz a definiált JSON-konfigurációs mezőket ismerteti. Ne módosítsa a mezők *feliratait*.

### <a name="basic-parameters"></a>Alapszintű paraméterek

Ez a szakasz az alapszintű paramétereket ismerteti.

| Paraméter felirata | Leírás | Típus |
|--|--|--|
| **ID (Azonosító)** | A protokoll neve. Törölje az alapértelmezett értéket, és adja hozzá a protokoll nevét, ahogy az megjelenik. | Sztring |
| **endianess** | Meghatározza a több bájtos adatkódolás módját. Csak a "kis" vagy "nagy" kifejezést használja. A protokoll specifikációja vagy a forgalom rögzítése alapján történt | Sztring |
| **sanity_failure_codes** | Ezek az elemző által visszaadott kódok, ha a kód identitásával kapcsolatos józanság ütközik. Lásd a Magic Number Validation című szakaszt a C++ szakaszban. | Sztring |
| **malformed_codes** | Ezek a kódok megfelelően vannak azonosítva, de a rendszer hibát észlelt. Ha például a mező hossza túl rövid vagy hosszú, vagy érvénytelen az érték. | Sztring |
| **dissect_as** | Egy tömb, amely meghatározza, hogy az adott protokoll forgalmát Mikor kell megérkezni. | TCP/UDP, Port stb. |
| **mezők** | A forgalomból kinyert mezők deklarációja. Minden mező saját AZONOSÍTÓval (névvel) és típussal (numerikus, karakterlánc, nyers, tömb, összetett) rendelkezik. Például a kinyert mező [függvény](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) a megvalósítási elemző fájlban. A konfigurációs fájlban írt mezők az egyetlenek, amelyek hozzáadhatók a réteghez. |  |

### <a name="other-advanced-fields"></a>Egyéb speciális mezők 

Ez a szakasz a többi mezőt ismerteti.

| Paraméter felirata | Leírás |
|-----------------|--------|
| **címlisták engedélyezése** | Indexelheti a protokollok értékeit, és megjelenítheti őket az adatbányászati jelentésekben. Ezek a jelentések a hálózati alapkonfigurációt tükrözik. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Az adatbányászati nézet mintája."::: <br /> További információ: [Kapcsolódás egy indexelő szolgáltatáshoz (alapterv)](#connect-to-an-indexing-service-baseline) a részletekért. |
| **belső vezérlőprogram** | Kinyerheti a belső vezérlőprogram adatait, megadhatja az indexelési értékeket, és aktiválhatja a belső vezérlőprogram riasztásait a beépülő modulhoz. További információ: a [belső vezérlőprogram adatainak kinyerése](#extract-firmware-data) . |
| **value_mapping** | A beépülő modul kimeneti szövege testreszabható úgy, hogy a leképezési fájlok definiálásával és frissítésével megfeleljen a vállalati környezet igényeinek. Például leképezheti a nyelvi fájlokat. A módosítások a kód módosítása vagy hatása nélkül is könnyen végrehajthatók a szövegre. További információ: a [leképezési fájlok (JSON) létrehozása](#create-mapping-files-json) a részletekért. |

## <a name="sample-json-configuration-file"></a>Példa JSON-konfigurációs fájlra

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Implementációs kód érvényességének előkészítése

Ez a szakasz ismerteti a C++ kód-ellenőrzési függvények megvalósítását, és mintakód használatát teszi lehetővé. Két ellenőrzési réteg érhető el:

- Józanság.

- Helytelen formátumú kód.

Egy működő beépülő modul létrehozásához nem kell ellenőrző kódot létrehoznia. Ha nem készíti elő az érvényesítési kódot, az érzékelő adatbányászati jelentéseit a sikeres feldolgozás jelzése tekintheti át.

A mezőértékek leképezhetők a fájlok leképezésére szolgáló szövegbe, és zökkenőmentesen frissíthetők a feldolgozás befolyásolása nélkül.

## <a name="sanity-code-validations"></a>Az épelméjűség kódjának érvényessége 

Ez ellenőrzi, hogy a továbbított csomag megfelel-e a protokoll érvényesítési paramétereinek, amely segít azonosítani a protokollt a forgalomon belül.

Használja például az első 8 bájtot a *bűvös számként*. Ha a józan ész nem sikerül, a rendszer egy épelméjűség-hibát ad vissza.

Például:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Ha más kapcsolódó beépülő modulok is telepítve lettek, a rendszer érvényesíti a csomagot.

## <a name="malformed-code-validations"></a>Helytelen formátumú kód érvényessége 

A rendszer helytelenül formázott érvényesítéseket használ a protokoll pozitív ellenőrzése után.

Ha a protokollok alapján nem sikerül feldolgozni a csomagokat, a rendszer hibaüzenetet ad vissza.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="helytelen formátumú kód":::

## <a name="c-sample-with-validations"></a>C++ minta érvényesítéssel

A függvénynek megfelelően a rendszer végrehajtja a folyamatot, ahogy az alábbi példában is látható.

### <a name="function-20"></a>20. függvény 

- A rendszer belső vezérlőprogramként dolgozza fel.

- A mezők a függvény szerint vannak beolvasva.

- A mezők hozzáadódnak a réteghez.

### <a name="function-10"></a>10. függvény 

- A függvény egy másik alfüggvényt tartalmaz, amely egy konkrétabb művelet.

- A rendszer beolvassa a alfüggvényt, és hozzáadja a réteghez.

Ha ez megtörtént, a feldolgozás befejeződött. A visszatérési érték azt jelzi, hogy a program sikeresen feldolgozta-e a nem ágazati réteget. Ha igen, a réteg felhasználható lesz.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Eszköz metaadatainak kinyerése

A következő metaadatokat is kinyerheti az eszközökön:

  - `Is_distributed_control_system` – Azt jelzi, hogy a protokoll az elosztott vezérlő rendszer részét képezi-e. (például: a SCADA protokollnak false értékűnek kell lennie)

  - `Has_protocol_address` – Megadja, hogy van-e protokoll címe; az aktuális protokoll megadott címe, például MODBUS egység azonosítója

  - `Is_scada_protocol` – Azt jelzi, hogy a protokoll az OT-hálózatokra vonatkozik-e

  - `Is_router_potential` – Azt jelzi, hogy a protokollt főleg útválasztók használják-e. Például: LLDP, CDP vagy STP.

Ennek eléréséhez a JSON konfigurációs fájlt a metadata tulajdonsággal kell frissíteni.

## <a name="json-sample-with-metadata"></a>A metaadatokat tartalmazó JSON-minta 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Programozási kód kibontása 

A programozási esemény bekövetkeztekor kinyerheti a kód tartalmát. A kinyert tartalom a következőket teszi lehetővé:

- A programkód tartalmának összehasonlítása különböző programozási eseményekben.

- Riasztás elindítása a jogosulatlan programozással kapcsolatban.  

- Esemény elindítása a programozási kód fájljának fogadásához.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="A programozási változási napló.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="A gombra kattintva megtekintheti a programozást.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="A nem engedélyezett PLC programozási riasztás.":::

Ennek eléréséhez a JSON konfigurációs fájlt a tulajdonság használatával kell frissíteni `code_extraction` . 

### <a name="json-configuration-fields"></a>JSON-konfigurációs mezők 

Ez a szakasz a JSON-konfigurációs mezőket ismerteti. 

- **metódus**

  Azt jelzi, hogy a program hogyan fogadja a programozási esemény fájljait. 

  MINDEN (minden programozási művelet esetén a rendszer minden fájl fogadását okozhatja, még akkor is, ha a fájlok módosítása nélkül van).

- **file_type**  

  Megadja a kód tartalomtípusát.  

  Szöveg (minden szövegfájl tartalmaz szöveges adatokat).

- **code_data_field**
  
  Megadja a kód tartalmának megadásához használandó megvalósítási mezőt.

  Mező.

- **code_name_field**

  A kódolási fájl nevének megadásához használandó megvalósítási mezőt jelzi.

  Mező.

- **size_limit**

  Megadja az egyes kódolási fájlok tartalmának méretét BÁJTban kifejezve, ha egy programkód túllépi a beállított korlátot. Ha ez a mező nincs megadva, az alapértelmezett érték 15 MB lesz 15 000 000.

  Száma.

- **metaadatok**

  A kódlap további információit jelzi.

  Két tulajdonsággal rendelkező objektumokat tartalmazó tömb:

    - név (karakterlánc) – azt jelzi, hogy a XSense metaadat-kulcs jelenleg csak a Felhasználónév kulcsát támogatja.
 
    - Value (mező) – a metaadat-adatok biztosításához használandó megvalósítási mezőt jelzi.

## <a name="json-sample-with-programming-code"></a>JSON-minta programozási kóddal

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Egyéni horizontú riasztások

Bizonyos protokollok funkció kódja hibát jelezhet. Ha például a protokoll egy adott vegyszerrel rendelkező tárolót vezérel, amelyet mindig egy adott hőmérsékleten kell tárolni. Ebben az esetben előfordulhat, hogy a függvény kódja hibát jelez a hőmérőben. Ha például a függvény kódja 25, aktiválhat egy riasztást a webkonzolon, amely azt jelzi, hogy probléma van a tárolóval. Ilyen esetben megadhatja a részletes csomagok riasztásait.

Adja hozzá a **riasztások** paramétert a `config.json` beépülő modulhoz.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON-konfigurációs mezők

Ez a szakasz a JSON-konfigurációs mezőket ismerteti. 

| Mező neve | Leírás | Lehetséges értékek |
|--|--|--|
| **ID (Azonosító)** | Egyetlen riasztási azonosítót jelöl. Ebben a kontextusban egyedinek kell lennie. | 0-10000 numerikus érték |
| **message** | A felhasználó számára megjelenő információk. Ez a mező különböző mezők használatát teszi lehetővé. | A protokoll bármelyik mezőjét vagy bármely alsóbb rétegbeli protokollt használhat. |
| **Cím** | A riasztás címe |  |
| **expression** | Ha azt szeretné, hogy ez a riasztás felugorjon. | Az alsó rétegekben vagy az aktuális rétegben található numerikus mezők használata.</br></br> Minden mezőnek burkolóval kell rendelkeznie `{}` , hogy az SDK-t mezőként lehessen felderíteni, a támogatott logikai operátorok</br> = =-EQUAL</br> <= – kisebb vagy egyenlő</br> >= – nagyobb vagy egyenlő</br> > – több, mint</br> < – kisebb, mint</br> ~ =-Nem egyenlő |

## <a name="more-about-expressions"></a>További információ a kifejezésekről

Minden alkalommal, amikor a Horizon SDK meghívja a kifejezést, és az *igaz* értékre vált, a rendszer riasztást küld az érzékelőben.

Ugyanabban a riasztásban több kifejezés is szerepelhet. Például:

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Ez a kifejezés csak akkor érvényesíti a függvény kódját, ha a csomag IPv4-src 10.10.10.10. A numerikus ábrázolásban az IP-cím nyers ábrázolása.

Használhatja a `and` vagy `or` a kifejezést a kifejezések összekapcsolásához.

## <a name="json-sample-custom-horizon-alerts"></a>JSON-minta – egyéni horizontú riasztások

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Kapcsolódás indexelő szolgáltatáshoz (alapterv)

Indexelheti a protokollok értékeit, és megjelenítheti őket az adatbányászati jelentésekben.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Az adatbányászati lehetőség nézete.":::

Ezek az értékek később meghatározott szövegekre képezhetők le, például a számok szövegként való leképezése vagy információ hozzáadása bármilyen nyelven.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="áttelepítési":::

További információ: a [leképezési fájlok (JSON) létrehozása](#create-mapping-files-json) a részletekért.

A korábban elemzett protokollok értékeit is használhatja további információk kinyeréséhez.

Például a TCP protokollon alapuló érték esetén az IPv4-rétegből származó értékeket használhatja. Ebből a rétegből kinyerheti az értékeket, például a csomag forrását és a célhelyet.

Ennek eléréséhez a JSON konfigurációs fájlt a tulajdonság használatával kell frissíteni `whitelists` .

## <a name="allow-list-data-mining-fields"></a>Engedélyezési lista (adatbányászat) mezői

Az engedélyezési lista következő mezői érhetők el:

- Name (név) – az indexeléshez használt név.

- alert_title – egy rövid, egyedi címet, amely leírja az eseményt.

- alert_text – további információk

Több engedélyezési listát is hozzáadhat, ami lehetővé teszi az indexelés teljes rugalmasságát.

## <a name="json-sample-with-indexing"></a>JSON-minta indexeléssel 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Belső vezérlőprogram-adatok kinyerése

Kinyerheti a belső vezérlőprogram adatait, megadhatja az indexelési értékeket, és aktiválhatja a belső vezérlőprogram riasztásait a beépülő modulhoz. Példa:

- Bontsa ki a belső vezérlőprogram modelljét vagy verzióját. Ezeket az információkat tovább használhatja a CVEs azonosításához.

- Riasztás elindítása új belső vezérlőprogram-verzió észlelésekor.

Ennek eléréséhez a JSON konfigurációs fájlt a belső vezérlőprogram tulajdonsággal kell frissíteni.

## <a name="firmware-fields"></a>Belső vezérlőprogram mezői

Ez a szakasz a JSON belső vezérlőprogram konfigurációs mezőit ismerteti.

- **név**
  
  Azt jelzi, hogyan jelennek meg a mező az érzékelő konzolon.

- **value**

 Megadja az adateléréshez használandó megvalósítási mezőt. 

- **firmware_index:**

  Válassza az alábbi lehetőségek egyikét:  
  - **modell**: az eszköz modellje. Engedélyezi a CVEs észlelését.
  - **soros**: az eszköz sorozatszáma. A sorozatszám nem mindig érhető el minden protokollhoz. Ez az érték egyedi eszközönként.
  - **rack**: az állvány azonosítóját jelzi, ha az eszköz egy állvány része.
  - **tárolóhely**: a tárolóhely azonosítója, ha az eszköz egy rack részét képezi.  
  - **module_address**: Ha a modult egy másik eszköz mögött szeretné megjeleníteni, használja a-hierarchiát. Ehelyett akkor alkalmazható, ha egy rack tárolóhely kombinációja, amely egy egyszerűbb bemutató.  
  - **firmware_version**: az eszköz verziószámát jelzi. Engedélyezi a CVEs észlelését.
  - **alert_text**: a belső vezérlőprogram eltéréseit leíró szöveget jelöl, például a verzió módosításait.  
  - **index_by**: az eszköz azonosítására és indexelésére szolgáló mezőket jelzi. Az alábbi példában az eszközt az IP-címe azonosítja. Bizonyos protokollokban összetettebb indexet lehet használni. Ha például egy másik eszköz csatlakozik, és tudja, hogyan kell kibontani a belső elérési utat. A MODBUS egység azonosítója például az index részeként használható, az IP-cím és az egység azonosítójának eltérő kombinációja alapján.
  - **firmware_fields**: Megadja, hogy mely mezők legyenek az eszköz metaadatainak mezői. Ebben a példában a következők használatosak: modell, változat és név. Minden protokoll meghatározhat saját belső vezérlőprogram-adatforrásokat.

## <a name="json-sample-with-firmware"></a>JSON-minta belső vezérlőprogram 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Eszköz attribútumainak kinyerése 

Az eszközön a leltárban, az adatbányászatban és más jelentésekben elérhető információk is javíthatók.

- Név

- Típus

- Szállító

- Operációs rendszer

Ennek eléréséhez a JSON-konfigurációs fájlt frissíteni kell a **Properties (Tulajdonságok** ) tulajdonság használatával. 

Ezt az alapszintű beépülő modul és a szükséges mezők kinyerése után teheti meg.

## <a name="properties-fields"></a>Tulajdonságok mezői

Ez a szakasz a JSON-tulajdonságok konfigurációs mezőit ismerteti. 

**config_file** 

Annak a fájlnak a nevét tartalmazza, amely meghatározza, hogyan kell feldolgozni az egyes kulcsokat a `key` mezőkben. A konfigurációs fájlnak JSON formátumúnak kell lennie, és a beépülő modul mappájának részeként kell szerepelnie.

A JSON minden kulcsa meghatározza azt a műveletet, amelyet a kulcs csomagból való kibontásakor kell elvégezni.

Az egyes kulcsok a következőket tartalmazhatják:

- **Csomagkapcsolt adatok** – a csomagból kinyert adatok alapján frissítendő tulajdonságokat jelzi (az adatok biztosításához használt megvalósítási mező).

- **Statikus** adatmennyiség – előre definiált `property-value` műveleteket jelez, amelyeket frissíteni kell.

A fájlban konfigurálható tulajdonságok a következők: 

- **Név** – az eszköz nevét jelzi.

- **Type (típus** ) – az eszköz típusát jelzi.

- **Szállító** – az eszköz gyártóját jelzi.

- **Leírás** – az eszköz belső vezérlőprogram-modelljét jelzi (alacsonyabb prioritás, mint a "modell").

- **OperatingSystem** – az eszköz operációs rendszerét jelzi.

### <a name="fields"></a>Mezők

| Mező | Leírás |
|--|--|
| kulcs | Megadja a kulcsot. |
| érték | Megadja az adateléréshez használandó megvalósítási mezőt. |
| is_static_key | Azt jelzi, hogy a `key` mező származtatott érték-e a csomagból, vagy egy előre definiált érték-e. |

### <a name="working-with-static-keys-only"></a>Csak a statikus kulcsok használata

Ha statikus kulcsokkal dolgozik, akkor nem kell konfigurálnia a következőt: `config.file` . Csak a JSON-fájlt állíthatja be.

## <a name="json-sample-with-properties"></a>JSON-minta tulajdonságaival 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>JSON CONFIG_FILE_EXAPMLE 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Leképezési fájlok létrehozása (JSON)

Testreszabhatja a beépülő modul kimeneti szövegét, hogy az megfeleljen a vállalati környezet igényeinek a fájlok definiálásával és frissítésével. A módosítások a kód módosítása vagy hatása nélkül is könnyen végrehajthatók a szövegre. Az egyes fájlok egy vagy több mezőt is leképezhetők.

- Mezőértékek leképezése nevekre, például 1: alaphelyzetbe állítás, 2: indítás, 3: leállítás.

- Szöveg leképezése több nyelv támogatásához.

Két típusú leképezési fájl is definiálható.

  - [Egyszerű leképezési fájl](#simple-mapping-file).

  - [Függőségi leképezési fájl](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="éteri háló":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="A nem kezelt riasztások nézete.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Az ismert szabályzatok megsértésének listája.":::

## <a name="file-naming-and-storage-requirements"></a>A fájlok elnevezési és tárolási követelményei 

A leképezési fájlokat a metaadatok mappában kell menteni.

A fájl nevének meg kell egyeznie a JSON-konfigurációs fájl azonosítójával.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Egy JSON-konfigurációs fájl mintája.":::

## <a name="simple-mapping-file"></a>Egyszerű leképezési fájl 

Az alábbi minta egy alapszintű JSON-fájlt mutat be kulcs értékként.

Engedélyezési lista létrehozásakor a leképezett mezők közül egyet vagy többet tartalmaz. Az érték egy számból, sztringből vagy bármilyen típusból lesz átalakítva a leképezési fájlban bemutatott formázott szövegbe.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Függőség-leképezési fájl 

Ha azt szeretné jelezni, hogy a fájl egy függőségi fájl, adja hozzá a kulcsszót `dependency` a leképezési konfigurációhoz.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

A fájl a függőség mező és a függvény mező közötti leképezést tartalmaz. Például a függvény és az sub függvény között. A sub függvény a megadott függvénynek megfelelően változik.

A korábban konfigurált engedélyezési listán nincs függőségi konfiguráció, az alább látható módon.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

A függőség lehet egy adott érték vagy mező alapján. Az alábbi példában egy mezőn alapul. Ha értéket ad meg, adja meg a leképezési fájlból beolvasni kívánt kinyerési értéket.

Az alábbi példában szereplő függőséget a mező azonos értékének megfelelően hajtsa végre.

Például az alárendelt függvényben a jelentés a függvény alapján módosul.

  - Ha az olvasási függvény, az öt érték azt jelenti, hogy beolvassa a memóriát.

  - Ha ez egy írási függvény, a rendszer ugyanazt az értéket használja egy fájlból való olvasáshoz.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Mintafájl

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>JSON-minta leképezéssel

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>A beépülő modul becsomagolása, feltöltése és figyelése 

Ez a szakasz ismerteti, hogyan

  - Csomagolja a beépülő modult.

  - Töltse fel a beépülő modult.

  - A beépülő modul figyelése és hibakeresése annak kiértékeléséhez, hogy milyen jól van végrehajtva.

A beépülő modul becsomagolása:

1. Adja hozzá az összetevőt **(lehet** , könyvtár, config.jsvagy metaadatok) egy `tar.gz` fájlhoz.

1. Módosítsa a fájlkiterjesztést a értékre \<XXX.hdp> , ahol a a \<XXX> bővítmény neve.

Bejelentkezés a Horizon-konzolra:

1.  Jelentkezzen be az érzékelő CLI-be rendszergazdaként, CyberX vagy támogató felhasználóként.

2.  A fájlban: `/var/cyberx/properties/horizon.properties` módosítsa a **felhasználói felület. enabled** tulajdonságot **true** ( `horizon.properties:ui.enabled=true` ) értékre.

3.  Jelentkezzen be az érzékelő konzolra.

4.  A főmenüben válassza a **horizont** lehetőséget.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Válassza a horizont lehetőséget a bal oldali ablaktáblán.":::

    Megnyílik a Horizon-konzol.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="A Horizon-konzol és annak összes beépülő moduljának nézete.":::

## <a name="plugins-pane"></a>Beépülő modulok ablaktábla

A beépülő modul ablaktábla a következőket sorolja fel:

  - Infrastruktúra-bővítmények: alapértelmezés szerint a Defender for IoT telepíti az infrastruktúra beépülő modulokat.

  - Alkalmazás-bővítmények: az alkalmazás plugins alapértelmezés szerint a Defender for IoT és más, a Defender által a IoT vagy külső fejlesztők által fejlesztett beépülő modulok esetében települnek.
    
A váltógomb használatával feltöltött beépülő modulok engedélyezése és letiltása.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="A CIP-váltás.":::

### <a name="uploading-a-plugin"></a>Beépülő modul feltöltése

A beépülő modul létrehozása és csomagolása után feltöltheti azt a Defender for IoT-érzékelőbe. A hálózat teljes lefedettségének eléréséhez fel kell töltenie a beépülő modult a szervezet összes érzékelője számára.

Feltöltés:

1.  Jelentkezzen be az érzékelőbe.


2. Válassza a **Feltöltés** lehetőséget.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Töltse fel a beépülő modult.":::

3. Tallózással keresse meg a beépülő modult, és húzza a beépülő modul párbeszédpanelre. Ellenőrizze, hogy az előtag értéke `.hdp` . A beépülő modul betöltődik.

## <a name="plugin-status-overview"></a>A beépülő modul állapotának áttekintése 

A Horizon-konzol **Áttekintés** ablaka információt nyújt a feltöltött beépülő modulról, és lehetővé teszi azok letiltását és engedélyezését.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="A Horizon-konzol áttekintése.":::

| Mező | Leírás |
|--|--|
| Alkalmazás | A feltöltött beépülő modul neve. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="A be-és kikapcsoló kapcsoló."::: | Kapcsolja **be** vagy **ki** a beépülő modult. A IoT Defender nem fogja kezelni a beépülő modulban definiált protokoll-forgalmat, ha kikapcsolja a beépülő modult. |
| Idő | Az adatelemzési idő utolsó elemzése. 5 másodpercenként frissül. |
| PPS | A csomagok másodpercenkénti száma. |
| Sávszélesség | Az utolsó 5 másodpercben észlelt átlagos sávszélesség. |
| Malforms | A rendszer helytelenül formázott érvényesítéseket használ a protokoll pozitív ellenőrzése után. Ha a protokollok alapján nem sikerül feldolgozni a csomagokat, a rendszer hibaüzenetet ad vissza.   <br><br>Ez az oszlop a malform hibák számát jelzi az elmúlt 5 másodpercben. További információ: [helytelenül formázott kód érvényességi](#malformed-code-validations) adatai. |
| Figyelmeztetések | A csomagok megfelelnek a struktúrának és a specifikációnak, de nem várt viselkedés a beépülő modul figyelmeztetési konfigurációja alapján. |
| Hibák | Azon csomagok száma, amelyek sikertelenek voltak az alapszintű protokollok érvényessége miatt. Ellenőrzi, hogy a csomag megfelel-e a protokoll-definícióknak. Az itt megjelenő szám azt jelzi, hogy az elmúlt 5 másodpercben észlelt hibák száma. További információ: az [épelméjűség kódjának érvényességi](#sanity-code-validations) adatai. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="A figyelő ikonja"::: | Tekintse át a beépülő modulhoz észlelt malform és figyelmeztetések részleteit. |

## <a name="plugin-details"></a>Beépülő modul részletei

A beépülő modul valós idejű viselkedését a beépülő modulban észlelt *Malform* és *figyelmeztetések* számának elemzésével ellenőrizheti. Egy lehetőség áll rendelkezésre a képernyő befagyasztásához és az exportáláshoz a további vizsgálathoz

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Az SNMP-figyelő képernyője.":::

Figyelés:

Kattintson a beépülő modulhoz tartozó figyelő gombra az áttekintésben.

Következő lépések

A [Horizon API](references-horizon-api.md) beállítása
