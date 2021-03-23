---
title: Biztonsági ügynökök konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Defender for IoT biztonsági ügynököket a Defender for IoT Security szolgáltatással való használatra.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 5ac9a5cdb4cc13ed91c0e5e29447b9cdbe71bcce
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784407"
---
# <a name="tutorial-configure-security-agents"></a>Oktatóanyag: biztonsági ügynökök konfigurálása

Ez a cikk a IoT biztonsági ügynökeit ismerteti, és részletezi, hogyan módosíthatja és konfigurálhatja azokat.

> [!div class="checklist"]
> * Biztonsági ügynökök konfigurálása
> * Az ügynök viselkedésének módosítása a Twin tulajdonságok szerkesztésével
> * Alapértelmezett konfiguráció felderítése

## <a name="agents"></a>Ügynökök

A Defender for IoT biztonsági ügynökök adatokat gyűjtenek a IoT-eszközökről, és biztonsági műveleteket hajtanak végre az észlelt biztonsági rések enyhítése érdekében. A biztonsági ügynök konfigurációja a testre szabható modulok különálló tulajdonságainak együttes használatával vezérelhető. Általánosságban elmondható, hogy a tulajdonságok másodlagos frissítései nem ritkák.

A Defender for IoT biztonsági ügynökének Twin konfigurációs objektuma egy JSON formátumú objektum. A konfigurációs objektum olyan ellenőrizhető tulajdonságok halmaza, amelyek segítségével szabályozhatja az ügynök viselkedését.

Ezek a konfigurációk segítenek testreszabni az ügynököt minden szükséges forgatókönyvhöz. Például automatikusan kizárhat néhány eseményt, vagy megtarthatja az energiafogyasztást egy minimális szintre. Ehhez konfigurálja ezeket a tulajdonságokat.

A módosítások végrehajtásához használja a Defender for IoT biztonsági ügynök konfigurációs [sémáját](https://aka.ms/iot-security-github-module-schema) .

## <a name="configuration-objects"></a>Konfigurációs objektumok

A IoT biztonsági ügynök minden Defender szolgáltatásához kapcsolódó tulajdonságok az ügynök konfigurációs objektumában, a **azureiotsecurity** modul kívánt tulajdonságok szakaszában találhatók.

A konfiguráció módosításához hozza létre és módosítsa ezt az objektumot a **azureiotsecurity** modul kettős identitásán belül.

Ha az ügynök konfigurációs objektuma nem létezik a **azureiotsecurity** modulban, az összes biztonsági ügynök tulajdonság értéke alapértelmezett értékre van állítva.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurációs séma és érvényesítés

Győződjön meg arról, hogy a [séma](https://aka.ms/iot-security-github-module-schema)alapján érvényesíti az ügynök konfigurációját. Az ügynök nem indul el, ha a konfigurációs objektum nem felel meg a sémának.

Ha az ügynök futása közben a konfigurációs objektum nem érvényes konfigurációra változik (a konfiguráció nem felel meg a sémának), az ügynök figyelmen kívül hagyja az érvénytelen konfigurációt, és továbbra is az aktuális konfigurációt fogja használni.

### <a name="configuration-validation"></a>Konfiguráció ellenőrzése

A Defender for IoT biztonsági ügynök a **azureiotsecurity** modul kettős identitásának jelentett tulajdonságok szakaszának aktuális konfigurációját jelenti.
Az ügynök jelentést készít az összes elérhető tulajdonságról, ha egy tulajdonságot nem a felhasználó állított be, az ügynök az alapértelmezett konfigurációt jelenti.

A konfiguráció érvényesítéséhez hasonlítsa össze a kívánt szakaszban megadott értékeket a jelentett szakaszban bemutatott értékekkel.

Ha a kívánt és a jelentett tulajdonságok között eltérés van, akkor az ügynök nem tudta elemezni a konfigurációt.

Ellenőrizze a kívánt tulajdonságokat a [sémán](https://aka.ms/iot-security-github-module-schema), javítsa ki a hibákat, és állítsa be újra a kívánt tulajdonságokat.

> [!NOTE]
> Egy konfigurációs hiba riasztása az ügynöktől származik, ha az ügynök nem tudta elemezni a kívánt konfigurációt.
> A jelentett és a kívánt szakasz összevetése annak megismeréséhez, hogy a riasztás továbbra is érvényes-e

## <a name="editing-a-property"></a>Tulajdonság szerkesztése

Az összes egyéni tulajdonságot be kell állítani az ügynök konfigurációs objektumán belül az **azureiotsecurity** modul Twin-ben.
Az alapértelmezett tulajdonság értékének használatához távolítsa el a tulajdonságot a konfigurációs objektumból.

### <a name="setting-a-property"></a>Tulajdonság beállítása

1. A IoT Hub keresse meg és válassza ki a módosítani kívánt eszközt.

1. Kattintson az eszközre, majd a **azureiotsecurity** modulban.

1. Kattintson a **modul Identity Twin** elemre.

1. Szerkessze a Defender-IoT-Micro-agentben módosítani kívánt tulajdonságokat.

   Ha például a kapcsolódási eseményeket magas prioritással szeretné konfigurálni, és 7 percenként magas prioritású eseményeket szeretne gyűjteni, használja a következő konfigurációt.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Kattintson a **Mentés** gombra.

### <a name="using-a-default-value"></a>Alapértelmezett érték használata

Az alapértelmezett tulajdonság értékének használatához távolítsa el a tulajdonságot a konfigurációs objektumból.

## <a name="default-properties"></a>Alapértelmezett tulajdonságok

A következő táblázat a Defender által a IoT biztonsági ügynökök számára ellenőrizhető tulajdonságokat tartalmazza.

Az alapértelmezett értékek a [GitHub](https\://aka.ms/iot-security-module-default)megfelelő sémájában érhetők el.

| Name| Állapot | Érvényes értékek| Alapértelmezett értékek| Leírás |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|Kötelező: hamis |Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték: PT7M |A magas prioritású üzenetek küldése előtti maximális időtartam.|
|lowPriorityMessageFrequency |Kötelező: hamis|Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték: PT5H |Az alacsony prioritású üzenetek küldésének maximális ideje.|
|snapshotFrequency |Kötelező: false|Érvényes értékek: időtartam ISO 8601 formátumban |Alapértelmezett érték PT13H |Az Eszközállapot-Pillanatképek létrehozásának időintervalluma.|
|maxLocalCacheSizeInBytes |Kötelező: hamis |Érvényes értékek: |Alapértelmezett érték: 2560000, nagyobb, mint 8192 | Az ügynök üzenet-gyorsítótárához engedélyezett maximális tárterület (bájtban). Az üzenetek elküldése előtt az eszközön tárolt üzenetek maximális mérete engedélyezett.|
|maxMessageSizeInBytes |Kötelező: hamis |Érvényes értékek: A 8192-nál nagyobb pozitív szám, amely kisebb, mint 262144 |Alapértelmezett érték: 204800 |Az ügynök maximálisan megengedett mérete a Felhőbeli üzenetben. Ezzel a beállítással szabályozható az egyes üzenetekben elküldött maximális adatmennyiség. |
|eventPriority $ {EventName} |Kötelező: hamis |Érvényes értékek: magas, alacsony, ki |Alapértelmezett értékek: |Minden ügynök által generált esemény prioritása |

### <a name="supported-security-events"></a>Támogatott biztonsági események

|Esemény neve| PropertyName | Alapértelmezett érték| Pillanatkép-esemény| Részletek állapota  |
|----------|-|---------|----|----|
|Diagnosztikai esemény|eventPriorityDiagnostic| Ki| Hamis| Ügynökkel kapcsolatos diagnosztikai események. Ezt az eseményt részletes naplózásra használhatja.|
|Konfigurációs hiba |eventPriorityConfigurationError |Alacsony |Hamis |Az ügynök nem tudta elemezni a konfigurációt. Ellenőrizze a konfigurációt a sémán.|
|Eldobott események statisztikája |eventPriorityDroppedEventsStatistics |Alacsony |Igaz|Ügynökkel kapcsolatos események statisztikája. |
|Csatlakoztatott hardver|eventPriorityConnectedHardware |Alacsony |Igaz |Az eszközhöz csatlakoztatott összes hardver pillanatképe.|
|Portok figyelése|eventPriorityListeningPorts |Magas |Igaz |Az eszközön futó összes nyitott figyelő port pillanatképe.|
|Folyamat létrehozása |eventPriorityProcessCreate |Alacsony |Hamis |Naplózza a folyamat létrehozását az eszközön.|
|Folyamat leállítása|eventPriorityProcessTerminate |Alacsony |Hamis |Naplózza a folyamat befejezését az eszközön.|
|Rendszer-információ |eventPrioritySystemInformation |Alacsony |Igaz |A rendszerinformációk pillanatképe (például operációs rendszer vagy CPU).|
|Helyi felhasználók| eventPriorityLocalUsers |Magas |Igaz|A rendszeren belüli regisztrált helyi felhasználók pillanatképe. |
|Bejelentkezés|  eventPriorityLogin |Magas|Hamis|Naplózza a bejelentkezési eseményeket az eszközre (helyi és távoli bejelentkezések).|
|Kapcsolatok létrehozása |eventPriorityConnectionCreate|Alacsony|Hamis|Az eszközre és az eszközről létrehozott TCP-kapcsolatok naplózása. |
|Tűzfal-konfiguráció| eventPriorityFirewallConfiguration|Alacsony|Igaz|Az eszköz tűzfal-konfigurációjának pillanatképe (tűzfalszabályok). |
|OPERÁCIÓSRENDSZER-alapterv| eventPriorityOSBaseline| Alacsony|Igaz|Az eszköz operációs rendszer alapkonfigurációjának vizsgálatának pillanatképe.|
|

## <a name="next-steps"></a>Következő lépések

- [A Defender megismerése IoT-javaslatokkal](concept-recommendations.md)
- [A Defender IoT-riasztások megismerése](concept-security-alerts.md)
- [Hozzáférés a nyers biztonsági adatokhoz](how-to-security-data-access.md)
