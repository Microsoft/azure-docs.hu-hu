---
title: 'Útmutató: egyéni parancsok alkalmazásának fejlesztése – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan fejlesztheti és testreszabhatja az egyéni parancsok alkalmazásait. Ezek a hangvezérelt alkalmazások a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbbek.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935818"
---
# <a name="develop-custom-commands-applications"></a>Egyéni parancsok alkalmazásának fejlesztése

Ebben a útmutatóban megtudhatja, hogyan fejlesztheti és konfigurálhatja az egyéni parancsok alkalmazásait. Az egyéni parancsok funkció lehetővé teszi, hogy a hang-első interakciós élményhez optimalizált, sokoldalú, hang-parancssori alkalmazásokat építsen ki. A szolgáltatás a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbb. Különösen jól alkalmazható a eszközök internetes hálózata (IoT) eszközökhöz, valamint a környezeti és a fej nélküli eszközökhöz.

Ebben a cikkben egy olyan alkalmazást hoz létre, amely be-és kikapcsolhatja a TV-t, beállíthatja a hőmérsékletet, és beállíthatja a riasztást. Az alapszintű parancsok létrehozása után megismerheti az alábbi beállításokat a parancsok testreszabásához:

* Paraméterek hozzáadása parancsokhoz
* Konfigurációk hozzáadása a parancs paramétereinek
* Interakciós szabályok kiépítése
* Nyelvi létrehozási sablonok létrehozása a beszédfelismerési válaszokhoz
* Egyéni hangvezérelt eszközök használata

## <a name="create-an-application-by-using-simple-commands"></a>Alkalmazás létrehozása egyszerű parancsok használatával

Első lépésként hozzon létre egy üres egyéni parancsok alkalmazást. További részletekért tekintse meg a rövid [útmutatót.](quickstart-custom-commands-application.md) Ebben az alkalmazásban egy projekt importálása helyett egy üres projektet kell létrehoznia.

1. A **név** mezőbe írja be a *Smart-Room-Lite* (vagy más néven választott név) projekt nevét.
1. A **nyelv** listában válassza az **angol (Egyesült Államok)** lehetőséget.
1. Válasszon ki vagy hozzon létre egy LUIS-erőforrást.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel: az "új projekt" ablak.](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti az **új projekt** ablakban kiválasztott szerzői erőforrást. Megadhat egy előrejelzési erőforrást is. 

Az egyéni parancsok alkalmazásának közzétételekor a rendszer előrejelzési erőforrást használ a felismeréshez. A fejlesztési és tesztelési fázisokban nincs szükség előrejelzési erőforrásra.

### <a name="add-a-turnon-command"></a>TurnOn-parancs hozzáadása

A létrehozott üres Smart-Room-Lite egyéni parancsok alkalmazásban adjon hozzá egy parancsot. A parancs feldolgozza a teljes műveletet `Turn on the tv` . Az üzenettel válaszol `Ok, turning the tv on` .

1. Hozzon létre egy új parancsot a bal oldali ablaktábla tetején található **új parancs** kiválasztásával. Megnyílik az **új parancssori** ablak.
1. A **Name (név** ) mezőben adja meg az értéket `TurnOn` .
1. Válassza a **Létrehozás** lehetőséget.

A középső ablaktábla a parancs tulajdonságait listázza. 

A következő táblázat a parancs konfigurációs tulajdonságait ismerteti. További információ: [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md).

| Konfiguráció            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Példa mondatokra | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó elindíthatja ezt a parancsot.                                                                 |
| Paraméterek       | A parancs végrehajtásához szükséges információk.                                                                                |
| Befejezési szabályok | A parancs teljesítéséhez végrehajtandó műveletek. Példák: válaszadás a felhasználóra vagy webszolgáltatással folytatott kommunikációra. |
| Interakciós szabályok   | Egyéb szabályok konkrétabb vagy összetett helyzetek kezelésére.                                                              |


> [!div class="mx-imgBorder"]
> ![A parancs létrehozásának helyét bemutató képernyőkép.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Példa mondatok hozzáadása

A **példa mondatok** szakaszban megtudhatja, hogy mit tehet a felhasználó.

1. A középső ablaktáblán válassza a **példák mondatok** lehetőséget.
1. A jobb oldali ablaktáblán vegyen fel példákat:

    ```
    Turn on the tv
    ```

1.  A panel tetején kattintson a **Mentés** gombra.

Még nem rendelkezik paraméterekkel, ezért átléphet a **befejezési szabályok** szakaszra.

#### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Ezután a parancsnak egy befejezési szabályra van szüksége. Ez a szabály azt jelzi, hogy a felhasználó teljesíti a teljesítési műveletet. 

További információ a szabályokról és a befejezési szabályokról: [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md).

1. Válassza ki az alapértelmezett befejezési **szabályt.** Ezután szerkessze a következő módon:

    
    | Beállítás    | Ajánlott érték                          | Leírás                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Név**       | `ConfirmationResponse`                  | A szabály célját leíró név          |
    | **Feltételek** | Nincsenek                                     | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
    | **Műveletek**    | **Beszédfelismerési válasz küldése**  >  **Egyszerű szerkesztő**  >  **Első változat** > `Ok, turning the tv on` | A szabály feltételének teljesülésekor végrehajtandó művelet |

   > [!div class="mx-imgBorder"]
   > ![A beszédfelismerési válasz létrehozásának helyét bemutató képernyőkép.](media/custom-commands/create-speech-response-action.png)

1. A művelet mentéséhez válassza a **Mentés** lehetőséget.
1. A **befejezési szabályok** szakaszban kattintson a **Mentés** gombra a módosítások mentéséhez. 

    > [!NOTE]
    > A parancshoz tartozó alapértelmezett befejezési szabályt nem kell használnia. Törölheti az alapértelmezett befejezési szabályt, és hozzáadhatja a saját szabályát is.

### <a name="add-a-settemperature-command"></a>SetTemperature-parancs hozzáadása

Most adjon hozzá még egy parancsot `SetTemperature` . Ez a parancs egyetlen Kimondás után `Set the temperature to 40 degrees` válaszol az üzenetre `Ok, setting temperature to 40 degrees` .

Az új parancs létrehozásához kövesse a `TurnOn` parancshoz használt lépéseket, de használja a példa mondatot `Set the temperature to 40 degrees` .

Ezután szerkessze a **meglévő befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| **Név**  | `ConfirmationResponse`                  |
| **Feltételek** | Nincsenek                                     |
| **Műveletek**    | **Beszédfelismerési válasz küldése**  >  **Egyszerű szerkesztő**  >  **Első változat** > `Ok, setting temperature to 40 degrees` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

### <a name="add-a-setalarm-command"></a>SetAlarm-parancs hozzáadása

Hozzon létre egy új `SetAlarm` parancsot. Használja a példa mondatot `Set an alarm for 9 am tomorrow` . Ezután szerkessze a **meglévő befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| **Név**  | `ConfirmationResponse`                  |
| **Feltételek** | Nincsenek                                     |
| **Műveletek**    | **Beszédfelismerési válasz küldése**  >  **Egyszerű szerkesztő**  >  **Első változat** > `Ok, setting an alarm for 9 am tomorrow` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

### <a name="try-it-out"></a>Próbálja ki

Tesztelje az alkalmazás viselkedését a teszt panel használatával: 

1. A panel jobb felső sarkában válassza a **vonat** ikont. 
1. A képzés befejeződése után válassza a **teszt** lehetőséget. 

Próbálja ki a következő kifejezési példákat hang vagy szöveg használatával:

- Gépelje be a következőt: *a hőmérséklet 40 fok* .
- Várt válasz: ok, hőmérséklet beállítása 40 fok
- Írja be a következőt: *TV bekapcsolása*
- Várt válasz: ok, a TV bekapcsolása
- Gépelje be a következőt: *riasztás beállítása 9 holnap*
- Várt válasz: ok, riasztás beállítása 9. holnap

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a webes csevegési felületen látható tesztről.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> A teszt ablaktáblán kiválaszthatja, hogy **a rendszer hogyan** dolgozza fel a hangbemenetet vagy a szövegbeviteli adatokat.

## <a name="add-parameters-to-commands"></a>Paraméterek hozzáadása parancsokhoz

Ebből a szakaszból megtudhatja, hogyan adhat hozzá paramétereket a parancsokhoz. A parancsokhoz paramétereket kell végrehajtani a feladatok végrehajtásához. Összetett forgatókönyvekben paraméterek használhatók az egyéni műveleteket kiváltó feltételek definiálásához.

### <a name="configure-parameters-for-a-turnon-command"></a>TurnOn-parancs paramétereinek konfigurálása

Először szerkessze a meglévő `TurnOn` parancsot a több eszköz bekapcsolásához és kikapcsolásához.

1. Most, hogy a parancs a be-és kikapcsolási forgatókönyveket is kezeli, nevezze át a parancsot *TurnOnOff*-ként.
   1. A bal oldali ablaktáblán válassza a **TurnOn** parancsot. Ezután a panel tetején található **új parancs** mellett kattintson a három pont (**..**.) gombra.
   
   1. Válassza az **Átnevezés** lehetőséget. Az **átnevezési parancs** ablakban módosítsa a nevet a *TurnOnOff* értékre.

1. Adjon hozzá egy új paramétert a parancshoz. A paraméter azt jelöli, hogy a felhasználó be-vagy kikapcsolja az eszközt.
   1. A középső ablaktábla tetején válassza a  **Hozzáadás** lehetőséget. A legördülő menüben válassza a **paraméter** lehetőséget.
   1. A jobb oldali ablaktáblán, a **Paraméterek** szakaszban, a **név** mezőben adja hozzá a következőt: `OnOff` .
   1. Válassza a **kötelező** lehetőséget. A **válasz hozzáadása a kötelező paraméterhez** ablakban válassza az **egyszerű szerkesztő** lehetőséget. Az **első változat** mezőben adja hozzá *vagy ki a* következőt:.
   1. Válassza a **Frissítés** lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![Képernyőkép: a "Simple Editor" (egyszerű szerkesztő) lapon megjelenő "válasz hozzáadása egy szükséges paraméterhez" szakasz.](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Konfigurálja a paraméter tulajdonságait az alábbi táblázat segítségével. További információ a parancsok összes konfigurációs tulajdonságáról: [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md).
      

       | Konfiguráció      | Ajánlott érték     | Leírás                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Név**               | `OnOff`           | A paraméter leíró neve                                                                           |
       | **Globális**          | Ki nem jelölt       | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e.|
       | **Kötelező**           | Bejelölve         | Jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs befejeződése előtt. |
       | **A kötelező paraméterre adott válasz**      |**Egyszerű szerkesztő** > `On or Off?`      | A paraméter értékét kérő üzenet, ha az nem ismert. |
       | **Típus**               | **Sztring**          | Paraméter típusa, például szám, karakterlánc, dátum és idő vagy földrajz.   |
       | **Konfigurálás**      | **Előre megadott bemeneti értékek elfogadása belső katalógusból** | A karakterláncok esetében ez a beállítás a bemeneteket a lehetséges értékek halmazára korlátozza. |
       | **Előre megadott bemeneti értékek**     | `on`, `off`           | A lehetséges értékek és az aliasok halmaza.         |
       
        
   1. Előre definiált bemeneti értékek hozzáadásához válassza az **előre definiált bemenet hozzáadása** lehetőséget. Az **új elem**  ablakban írja be a *nevet* az előző táblázatban látható módon. Ebben az esetben nem használ aliasokat, így üresen hagyhatja ezt a mezőt.
   
      > [!div class="mx-imgBorder"]
      > ![A paraméter létrehozását bemutató képernyőkép.](media/custom-commands/create-on-off-parameter.png)

   1. A paraméter összes konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.
 
#### <a name="add-a-subjectdevice-parameter"></a>SubjectDevice paraméter hozzáadása

1. Ha hozzá szeretne adni egy második paramétert, amely a parancs használatával ellenőrizhető eszközök nevét jelöli, válassza a **Hozzáadás** lehetőséget. Használja a következő konfigurációt.


    | Beállítás            | Ajánlott érték       |
    | ------------------ | --------------------- |
    | **Név**               | `SubjectDevice`         |
    | **Globális**          | Ki nem jelölt             |
    | **Kötelező**           | Bejelölve               |
    | **A kötelező paraméterre adott válasz**     | **Egyszerű szerkesztő** > `Which device do you want to control?`    | 
    | **Típus**               | **Sztring**                |          |
    | **Konfigurálás**      | **Előre megadott bemeneti értékek elfogadása belső katalógusból** | 
    | **Előre megadott bemeneti értékek** | `tv`, `fan`               |
    | **Aliasok** ( `tv` )      | `television`, `telly`     |

1. Kattintson a **Mentés** gombra.

#### <a name="modify-example-sentences"></a>Példa mondatok módosítása

A paramétereket használó parancsok esetében hasznos lehet olyan példákat felvenni, amelyek az összes lehetséges kombinációra kiterjednek. Például:

* Paraméterek teljes körű adatai: `turn {OnOff} the {SubjectDevice}`
* Részleges paraméterek adatai: `turn it {OnOff}`
* Nincs paraméter-információ: `turn something`

A különböző mértékű adatokat használó mondatok például lehetővé teszik, hogy az egyéni parancsok alkalmazás a részleges információ használatával oldja meg az egylépéses és a többszörös feloldási felbontást.

Ezeket az információkat szem előtt tartva szerkessze a példa mondatokat a javasolt paraméterek használatára:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Kattintson a **Mentés** gombra.

> [!TIP]
> A példa – mondatok szerkesztőben kapcsos zárójelek használatával hivatkozhat a paraméterekre. Például: `turn {OnOff} the {SubjectDevice}`.
> A korábban létrehozott paraméterek által támogatott automatikus kiegészítéshez használjon fület.

#### <a name="modify-completion-rules-to-include-parameters"></a>A befejezési szabályok módosítása a paraméterek belefoglalásához

Módosítsa a meglévő befejezési szabályt `ConfirmationResponse` .

1. A **feltételek** szakaszban válassza a **feltétel hozzáadása** lehetőséget.
1. Az **új feltétel** ablak **típus** listájában válassza a **kötelező paraméterek** elemet. Az alábbi listában válassza a **javítás OnOff** és a **SubjectDevice** lehetőséget.
1. Hagyja kiválasztva a **IsGlobal** .
1. Válassza a **Létrehozás** lehetőséget.
1. A **műveletek** szakaszban szerkessze a **beszédfelismerési válasz küldése** műveletet úgy, hogy fölé viszi, majd kiválasztja a Szerkesztés gombot. Ezúttal használja az újonnan létrehozott `OnOff` és `SubjectDevice` paramétereket:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Kattintson a **Mentés** gombra.

Próbálja ki a módosításokat úgy, hogy kiválasztja a jobb oldali ablaktábla tetején található **vonat** ikont. 

A képzés befejeződése után válassza a **teszt** lehetőséget. Megjelenik **az alkalmazás tesztelése** ablak. Próbálja ki a következő interakciókat:

- Bemenet: *a TV kikapcsolása*
- Kimenet: ok, a TV kikapcsolása
- Bemenet: *a televízió kikapcsolása*
- Kimenet: ok, a TV kikapcsolása
- Bemenet: *kikapcsolás*
- Kimenet: melyik eszközt szeretné vezérelni?
- Bemenet: *a TV*
- Kimenet: ok, a TV kikapcsolása

### <a name="configure-parameters-for-a-settemperature-command"></a>SetTemperature-parancs paramétereinek konfigurálása

Módosítsa a parancsot úgy, hogy `SetTemperature` lehetővé tegye a hőmérséklet beállítását a felhasználó számára.

Adjon hozzá egy `Temperature` paramétert. Használja a következő konfigurációt:

| Konfiguráció      | Ajánlott érték     |
| ------------------ | ----------------|
| **Név**               | `Temperature`           |
| **Kötelező**           | Bejelölve         |
| **A kötelező paraméterre adott válasz**      | **Egyszerű szerkesztő** > `What temperature would you like?`
| **Típus**               | `Number`          |


Szerkessze a példában szereplő hosszúságú kimondott szöveg a következő értékek használatára.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

A meglévő befejezési szabályok szerkesztése. Használja a következő konfigurációt.

| Konfiguráció      | Ajánlott érték     |
| ------------------ | ----------------|
| **Feltételek**         | **Kötelező paraméter**  >  **Hőmérséklet**           |
| **Műveletek**           | **Beszédfelismerési válasz küldése** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>SetAlarm-parancs paramétereinek konfigurálása

Adjon hozzá egy nevű paramétert `DateTime` . Használja a következő konfigurációt.

   | Beállítás                           | Ajánlott érték                     | 
   | --------------------------------- | ----------------------------------------|
   | **Név**                              | `DateTime`                               |
   | **Kötelező**                          | Bejelölve                                 |
   | **A kötelező paraméterre adott válasz**   | **Egyszerű szerkesztő** > `For what time?`            | 
   | **Típus**                              | **Dátum/idő**                                |
   | **Dátum alapértelmezett értékei**                     | Ha a dátum hiányzik, használja a mai napot.            |
   | **Idő alapértelmezett értékei**                     | Ha az idő hiányzik, használja a nap kezdetét.     |


> [!NOTE]
> Ez a cikk elsősorban karakterláncot, számot és DateTime típusú paramétereket használ. Az összes támogatott paraméter típusának és tulajdonságainak listáját lásd: [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md).


Szerkessze a példa hosszúságú kimondott szöveg. Használja a következő értékeket.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

A meglévő befejezési szabályok szerkesztése. Használja a következő konfigurációt.

   | Beállítás    | Ajánlott érték                               |
   | ---------- | ------------------------------------------------------- |
   | **Műveletek**    | **Beszédfelismerési válasz küldése** > `Ok, alarm set for {DateTime}`  |

Tesztelje a három parancsot együtt a különböző parancsokhoz kapcsolódó hosszúságú kimondott szöveg használatával. (Válthat a különböző parancsok között.)

- Bemenet: *riasztás beállítása*
- Kimenet: milyen időpontra?
- Bemenet: *kapcsolja be a TV* -t
- Kimenet: ok, a TV bekapcsolása
- Bemenet: *riasztás beállítása*
- Kimenet: milyen időpontra?
- Bemenet: *5 PM*
- Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Konfigurációk hozzáadása a parancs paramétereinek

Ebben a szakaszban további tudnivalókat talál a speciális paraméterek konfigurálásáról, beleértve a következőket:

 - A paraméterek értékei az egyéni parancsok alkalmazáson kívül definiált készletekhez tartozhatnak.
 - Érvényesítési záradékok hozzáadása a paraméterek értékeihez.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Paraméter konfigurálása külső katalógus entitásként

Az egyéni parancsok funkció lehetővé teszi a karakterlánc típusú paraméterek konfigurálását a webes végponton futó külső katalógusokra való hivatkozáshoz. Így a külső katalógust az egyéni parancsok alkalmazás szerkesztése nélkül is frissítheti egymástól függetlenül. Ez a megközelítés olyan esetekben hasznos, amikor a katalógus bejegyzései többek között vannak.

Használja újra a `SubjectDevice` paramétert a `TurnOnOff` parancsból. A paraméter aktuális konfigurációja a **belső katalógus előre megadott bemeneteit fogadja el**. Ez a konfiguráció a paraméterek konfigurációjában lévő eszközök statikus listáját jelöli. Helyezze át ezt a tartalmat egy külső adatforrásra, amely egymástól függetlenül frissíthető.

A tartalom áthelyezéséhez kezdje egy új webes végpont hozzáadásával. A bal oldali ablaktáblán lépjen a **webes végpontok** szakaszra. Ott adjon hozzá egy új webes végpontot. Használja a következő konfigurációt.

| Beállítás | Ajánlott érték |
|----|----|
| **Név** | `getDevices` |
| **URL-cím** | `https://aka.ms/speech/cc-sampledevices` |
| **Metódus** | **GET** |


Ha az URL-cím javasolt értéke nem működik, konfiguráljon és működtessen egy olyan webes végpontot, amely az ellenőrzött eszközök listájából álló JSON-fájlt ad vissza. A webes végpontnak a következő módon formázott JSON-fájlt kell visszaadnia:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Ezután nyissa meg a **SubjectDevice** paraméter-beállítások lapot. Állítsa be az alábbi tulajdonságokat.

| Beállítás | Ajánlott érték |
| ----| ---- |
| **Konfigurálás** | **Előre megadott bemenetek elfogadása a külső katalógusból** |                               
| **Katalógus végpontja** | `getDevices` |
| **Metódus** | **GET** |

Kattintson a **Mentés** gombra.

> [!IMPORTANT]
> Nem jelenik meg olyan paraméter, amely úgy konfigurálja a paramétereket, hogy fogadja a külső katalógus bemeneteit, kivéve, ha a bal oldali ablaktábla **web Endpoint (webes** végpont) szakaszában be van állítva a webes végpont.

Próbálja ki a **betanítás** lehetőséget. A betanítás befejezése után válassza a **test (tesztelés** ) lehetőséget, és próbálkozzon néhány interakcióval.

* Bemenet: *bekapcsolás*
* Kimenet: melyik eszközt szeretné vezérelni?
* Bemenet: *fények*
* Kimenet: ok, a fények bekapcsolása

> [!NOTE]
> Mostantól szabályozhatja a webes végponton tárolt összes eszközt. Azonban továbbra is be kell tanítania az alkalmazást az új módosítások teszteléséhez, majd újra közzé kell tennie az alkalmazást.

### <a name="add-validation-to-parameters"></a>Érvényesítés hozzáadása a paraméterekhez

Az *érvényesítések* olyan szerkezetek, amelyek bizonyos paraméterekre érvényesek, amelyek lehetővé teszik a korlátozásoknak a paraméter értékére való konfigurálását. Ha az értékek nem tartoznak a megkötések között, a rendszer rákérdez, hogy vannak-e kijavítani. Az ellenőrzési összeállítást kiterjesztő paraméterek típusairól az [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md)című cikk nyújt útmutatást.

A parancs használatával kipróbálhatja az érvényességeket `SetTemperature` . A következő lépésekkel adhat hozzá egy érvényesítést a `Temperature` paraméterhez.

1. A bal oldali ablaktáblán válassza a **SetTemperature** parancsot.
1. A középső ablaktáblán válassza a **hőmérséklet** elemet.
1. A jobb oldali ablaktáblán válassza az **Érvényesítés hozzáadása** elemet.
1. Az **új érvényesítési** ablakban konfigurálja az érvényesítést az alábbi táblázatban látható módon. Ezután kattintson a **Létrehozás** elemre.


    | Paraméter konfigurálása | Ajánlott érték | Leírás |
    | ---- | ---- | ---- |
    | **Minimális érték** | `60` | A szám paraméternél a paraméter által feltételezhető minimális érték |
    | **Maximális érték** | `80` | A szám paraméternél a paraméter által feltételezhető maximális érték |
    | **Sikertelen válasz** |  **Egyszerű szerkesztő**  >  **Első változat** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Új érték kérése, ha az ellenőrzés sikertelen |

    > [!div class="mx-imgBorder"]
    > ![A tartomány-ellenőrzés hozzáadását bemutató képernyőkép.](media/custom-commands/add-validations-temperature.png)

Próbálja ki a jobb oldali ablaktábla tetején, a **vonat** ikonra kattintva. A betanítás befejezése után válassza a **teszt** lehetőséget. Próbáljon ki néhány interakciót:

- Bemenet: *a hőmérséklet beállítása 72 fokos értékre*
- Kimenet: ok, hőmérséklet beállítása 72 fok
- Bemenet: *a hőmérséklet beállítása 45 fokos értékre*
- Kimenet: sajnos csak 60 és 80 fok közötti hőmérsékletet állíthatok be
- Bemenet: *tegyük 72 fok helyett*
- Kimenet: ok, hőmérséklet beállítása 72 fok

## <a name="add-interaction-rules"></a>Használati szabályok hozzáadása

A kapcsolati szabályok olyan *további* szabályok, amelyek konkrét vagy összetett helyzeteket kezelnek. Bár Ön szabadon létrehozhatja saját interakciós szabályait, ebben a példában a következő helyzetekben használja az interakciós szabályokat:

* Parancsok megerősítése
* Egy lépésből álló korrekció hozzáadása parancsokhoz

További információ az interakciós szabályokról: [egyéni parancsok – fogalmak és definíciók](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Megerősítések hozzáadása parancshoz

Megerősítés hozzáadásához használja az `SetTemperature` parancsot. A megerősítéshez hozzon létre interakciós szabályokat a következő lépésekkel:

1. A bal oldali ablaktáblán válassza a **SetTemperature** parancsot.
1. A középső ablaktáblán a **Hozzáadás gombra** kattintva vegyen fel kapcsolatitevékenység-szabályokat. Ezután válassza a **kapcsolati szabályok**  >  **megerősítése parancsot**.

    Ez a művelet három interakciós szabályt hoz létre. A szabályok megkérik a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját. A következő körök megerősítését (igen vagy nem) várnak.

    1. Módosítsa a **parancs** -interakció megerősítése szabályt a következő konfiguráció használatával:
        1. Módosítsa a nevet a **hőmérséklet megerősítéséhez**.
        1. Új feltétel hozzáadása: **kötelező paraméterek**  >  **hőmérséklete**.
        1. Új művelet hozzáadása: a   >  **beszédfelismerési válasz** típusának  >  **megadásakor a hőmérsékletet {hőmérséklet} fok értékre szeretné beállítani?**
        1. Az **elvárások** szakaszban hagyja meg az alapértelmezett értéket, ha a **felhasználó megerősíti a jóváhagyást**.
      
         > [!div class="mx-imgBorder"]
         > ![A kötelező paraméter válaszának létrehozását bemutató képernyőkép.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Módosítsa a **megerősítő** sikeres interakciós szabályt a sikeres megerősítés kezelésére (a felhasználó azt mondta, hogy igen).
      
          1. A név módosítása a **megerősítési hőmérsékletre sikeres** volt.
          1. A meglévő **megerősítés sikeres** feltétele.
          1. Új feltétel hozzáadása: **írja be** a  >  **kötelező paraméterek**  >  **hőmérsékletét**.
          1. Az alapértelmezett **végrehajtás utáni állapot** értékeként **hajtsa végre a végrehajtás befejezésének szabályait**.

    1. Módosítsa a **megerősítő megtagadási** kapcsolati szabályt úgy, hogy kezelni tudja a megerősítés megtagadásakor jelentkező forgatókönyveket (a felhasználó nem mondta).

          1. Módosítsa a nevet **visszaigazoló hőmérséklet megtagadásával**.
          1. Hagyja meg a meglévő **megerősítés** feltételét.
          1. Új feltétel hozzáadása: **írja be** a  >  **kötelező paraméterek**  >  **hőmérsékletét**.
          1. Új művelet **hozzáadása: a**  >  **beszédfelismerési válasz küldése**  >  **nem jelent problémát. Milyen hőmérsékletet?**
          1. A **felhasználó bemenetére való várakozáshoz** módosítsa az alapértelmezett **végrehajtás utáni állapot** értéket.

> [!IMPORTANT]
> Ebben a cikkben a beépített megerősítő funkciót használja. A kapcsolatitevékenység-szabályokat manuálisan is hozzáadhatja egyenként.
   
Próbálja ki a módosításokat a **betanítás** lehetőség kiválasztásával. A képzés befejeződése után válassza a **teszt** lehetőséget.

- **Bemenet**: *hőmérséklet beállítása 80 fokos értékre*
- **Kimenet**: biztos, hogy 80 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: *nem*
- **Kimenet**: nincs probléma. Milyen hőmérsékletet?
- **Bemenet**: *72 fok*
- **Kimenet**: biztos, hogy 72 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: *Igen*
- **Kimenet**: ok, hőmérséklet beállítása 72 fok

### <a name="implement-corrections-in-a-command"></a>Javítások implementálása egy parancsban

Ebben a szakaszban egy egylépéses korrekciót fog konfigurálni. A rendszer ezt a javítást használja a teljesítési művelet futtatása után. Azt is láthatja, hogyan engedélyezhető a javítás alapértelmezés szerint, ha a parancs még nem teljesül. Ha a parancs végrehajtása nem fejeződött be, adja hozzá az új paramétert a javításhoz `AlarmTone` .

A bal oldali ablaktáblán válassza a **SetAlarm** parancsot. Ezután adja hozzá az új **AlarmTone** paramétert.
        
- **név** > `AlarmTone`
- **Típus**  >  **Karakterlánc**
- **Alapértelmezett érték**  >  **Harangjáték**
- **Konfiguráció**  >  **Előre megadott bemeneti értékek elfogadása a belső katalógusból**
- **Előre megadott bemeneti értékek**  >  **Harangjáték**, **Jingle** és **echo** (ezek az értékek egyéni előre megadott bemenetek.)


Ezután frissítse a **datetime** paraméterre adott választ úgy, hogy a **riasztást a (z) {AlarmTone} hangjelzéssel állítsa be. Milyen időpontra?** Ezután módosítsa a befejezési szabályt a következőképpen:

1. Válassza ki a meglévő befejezési szabályt **ConfirmationResponse**.
1. A jobb oldali ablaktáblán vigye a kurzort a meglévő művelet fölé, és válassza a **Szerkesztés** lehetőséget.
1. A beszédre adott válasz frissítése a következőre: `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> A riasztási hang a folyamatban lévő parancs explicit konfigurációja nélkül is változhat. Például megváltoztathatja, ha a parancs még nem fejeződött be. Ha a parancs még nem teljesül, a rendszer *alapértelmezés* szerint az összes parancs-paraméterre vonatkozóan engedélyez egy javítást.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Korrekció implementálása egy parancs befejezésekor

Az egyéni parancsok platform lehetővé teszi az egylépéses javítás használatát akkor is, ha a parancs befejeződött. Ez a funkció alapértelmezés szerint nincs engedélyezve. Explicit módon kell konfigurálni. 

Az egylépéses javítás konfigurálásához kövesse az alábbi lépéseket:

1. A **SetAlarm** parancsban vegyen fel egy interakciós szabályt az **előző verzió frissítése paranccsal** a korábban beállított riasztás frissítéséhez. Nevezze át az interakciós szabályt az **előző riasztás frissítéseként**.
1. Hagyja meg az alapértelmezett feltételt: a **korábbi parancsot frissíteni kell**.
1. Új feltétel hozzáadása: **írja be** a  >  **kötelező paramétert** a  >  **datetime** értékre.
1. Új művelet hozzáadása: **írja be**  >    >    >  **az előző riasztási idő frissítése a következőre: {datetime}**.
1. Hagyja meg az alapértelmezett **végrehajtás utáni állapot** értéket a **parancs befejezésekor**.

Próbálja ki a módosításokat a **betanítás** lehetőség kiválasztásával. Várjon, amíg befejeződik a betanítás, majd válassza a **teszt** lehetőséget.

- **Bemenet**: *riasztás beállítása.*
- **Kimenet**: készen áll a riasztás hangjelzéssel való beállítására. Mennyi ideig?
- **Bemenet**: *állítson be egy riasztást a csengőhangként a 9. holnapnál.*
- **Kimenet**: ok, riasztás beállítva a 2020-05-21 09:00:00-hez. Az ébresztési hang a Jingle.
- **Bemenet**: *nem, 8.*
- **Kimenet**: az előző riasztási idő frissítése 2020-05-29 08:00-re.

> [!NOTE]
> Egy valós alkalmazásban a helyesbítő szabály **műveletek** szakaszában is el kell küldenie egy tevékenységet az ügyfélnek, vagy egy http-végpontot kell meghívnia, hogy frissítse a riasztási időt a rendszeren. Ez a művelet kizárólag a riasztási idő frissítéséhez felelős. A parancs más attribútuma nem vonható felelősségre. Ebben az esetben ez az attribútum az ébresztési hang lenne.

## <a name="add-language-generation-templates-for-speech-responses"></a>Nyelvi létrehozási sablonok hozzáadása a beszédfelismerési válaszokhoz

A Language-Generation (LG) sablonok segítségével testre szabhatja az ügyfélnek küldött válaszokat. A válaszokban eltéréseket vezetnek be. A nyelvi generációt a használatával érheti el:

* Nyelv – létrehozási sablonok.
* Adaptív kifejezések.

Az egyéni parancsok sablonjai a bot Framework [LG-sablonjain](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)alapulnak. Mivel az egyéni parancsok funkció szükség esetén új LG-sablont hoz létre (a paraméterekben vagy műveletekben a beszédfelismerési válaszokhoz), nem kell megadnia az LG-sablon nevét. 

Így nem kell megadnia a sablont, például a következőt:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Ehelyett a sablon törzsét a név nélkül is meghatározhatja, például:

> [!div class="mx-imgBorder"]
> ![A sablon-szerkesztő példáját ábrázoló képernyőfelvétel.](./media/custom-commands/template-editor-example.png)


Ez a változás bevezeti az ügyfélnek küldött beszédfelismerési válaszokat. A kilépéshez a megfelelő beszéd válasza véletlenszerűen van kiválasztva a megadott beállítások közül.

Az LG-sablonok előnyeit kihasználva az adaptív kifejezések használatával összetett beszédi válaszokat is megadhat a parancsokhoz. További információ: [LG templates Format](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Alapértelmezés szerint az egyéni parancsok funkció a következő kisebb eltérésekkel támogatja az összes képességet:

* Az LG-sablonokban az entitások a következőképpen jelennek meg: `${entityName}` . Az egyéni parancsok funkció nem használ entitásokat. A paramétereket azonban változóként is használhatja a `${parameterName}` megjelenítéssel vagy a `{parameterName}` megjelenítéssel.
* Az egyéni parancsok funkció nem támogatja a sablonok összeállítását és bővítését, mert nem módosítja közvetlenül az *. LG* fájlt. Csak az automatikusan létrehozott sablonok válaszait szerkeszti.
* Az egyéni parancsok funkció nem támogatja az LG által Beinjektált egyéni függvényeket. Az előre definiált függvények támogatottak.
* Az egyéni parancsok funkció nem támogatja a beállításokat, például,, `strict` `replaceNull` és `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Sablonra adott válaszok hozzáadása egy TurnOnOff-parancshoz

Módosítsa a `TurnOnOff` parancsot egy új paraméter hozzáadásához. Használja a következő konfigurációt.

| Beállítás            | Ajánlott érték       | 
| ------------------ | --------------------- | 
| **Név**               | `SubjectContext`         | 
| **Globális**          | Ki nem jelölt             | 
| **Kötelező**           | Ki nem jelölt               | 
| **Típus**               | **Sztring**                |
| **Alapértelmezett érték**      | `all` |
| **Konfigurálás**      | **Előre megadott bemeneti értékek elfogadása a belső katalógusból** | 
| **Előre megadott bemeneti értékek** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Befejezési szabály módosítása

Szerkessze a meglévő befejezési szabály **ConfirmationResponse** **műveletek** szakaszát. A **művelet szerkesztése** ablakban váltson a sablon- **szerkesztőre**. Ezután cserélje le a szöveget a következő példára.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Az alkalmazás betanítása és tesztelése a következő bemeneti és kimeneti adatok használatával. Figyelje meg a válaszok variációját. A változatot a sablon értékének több alternatívája hozza létre, és az adaptív kifejezések használatával is.

* Bemenet: *kapcsolja be a TV* -t
* Kimenet: ok, a TV bekapcsolása
* Bemenet: *kapcsolja be a TV* -t
* Kimenet: kész, bekapcsolva a tévén
* Bemenet: *a fények kikapcsolása*
* Kimenet: ok, az összes jelzőfény kikapcsolása
* Bemenet: a *helyiségek fényének* kikapcsolása
* Kimenet: ok, a helyiség fényének kikapcsolása

## <a name="use-a-custom-voice"></a>Egyéni hang használata

Az egyéni parancsok válaszának egy másik módja a kimeneti hang kiválasztása. Az alábbi lépéseket követve válthat az alapértelmezett hang egyéni hangra:

1. Az egyéni parancsok alkalmazás bal oldali paneljén válassza a **Beállítások** lehetőséget.
1. A középső ablaktáblán válassza az **egyéni hang** lehetőséget.
1. A táblázatban válasszon ki egy egyéni hangot vagy egy nyilvános hangot.
1. Kattintson a **Mentés** gombra.

> [!div class="mx-imgBorder"]
> ![A minta mondatokat és paramétereket bemutató képernyőkép.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> A nyilvános hangok esetében az neurális típusok csak bizonyos régiókban érhetők el. További információ: [beszédfelismerési szolgáltatás által támogatott régiók](./regions.md#standard-and-neural-voices).
>
> Egyéni hangokat hozhat létre az **Egyéni** hangprojekt oldalon. További információ: [Bevezetés az egyéni hang használatába](./how-to-custom-voice.md).

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [integrálható az egyéni parancsok alkalmazása](how-to-custom-commands-setup-speech-sdk.md) egy ügyfélalkalmazás segítségével a Speech SDK használatával.
* [Állítsa be a folyamatos üzembe helyezést](how-to-custom-commands-deploy-cicd.md) az egyéni parancsok alkalmazáshoz az Azure DevOps használatával. 
                      
