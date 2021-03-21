---
title: Egyéni parancsok – fogalmak és definíciók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megismerheti az egyéni parancsok alkalmazásaival kapcsolatos fogalmakat és definíciókat.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 98510132b2341736664dfafa52e9567df95652be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561097"
---
# <a name="custom-commands-concepts-and-definitions"></a>Egyéni parancsok – fogalmak és definíciók

Ez a cikk az egyéni parancsok alkalmazásaira vonatkozó fogalmakkal és definíciókkal kapcsolatos referenciául szolgál.

## <a name="commands-configuration"></a>Parancsok konfigurálása
A parancsok az egyéni parancsok alkalmazás alapszintű építőelemei. A parancsok olyan konfigurációk, amelyek a felhasználó által meghatározott feladatok végrehajtásához szükségesek.

### <a name="example-sentences"></a>Példa mondatokra
Példa a hosszúságú kimondott szöveg a felhasználók számára egy adott parancs indítására. Csak hosszúságú kimondott szöveg-mintát kell megadnia, és nem teljes listát. 

### <a name="parameters"></a>Paraméterek
A paraméterek a feladatok végrehajtásához szükséges adatok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

### <a name="completion-rules"></a>Befejezési szabályok
A befejezési szabályok olyan szabályok, amelyeket akkor kell végrehajtani, ha a parancs készen áll, például ha a szabályok összes feltétele teljesül.

### <a name="interaction-rules"></a>Interakciós szabályok
Az interakciós szabályok további szabályok, amelyek az összetettebb vagy összetett helyzetek kezelésére szolgálnak. Hozzáadhat további érvényesítéseket, vagy konfigurálhat speciális funkciókat, például a jóváhagyásokat vagy egy lépésenkénti javítást. Saját, egyéni interakciós szabályokat is létrehozhat.

## <a name="parameters-configuration"></a>Paraméterek konfigurálása

A paraméterek a feladatok végrehajtásához szükséges parancsok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

### <a name="name"></a>Name
A Name tulajdonság azonosítja a paramétereket. Mindig adjon egy leíró nevet a paraméternek. A paraméterek több szakaszban is szerepelhetnek, például a feltételek, a beszédfelismerési válaszok vagy más műveletek összeállításakor.
 
### <a name="isglobal"></a>IsGlobal
Ez a jelölőnégyzet azt jelzi, hogy a paraméter hatóköre meg van-e osztva az alkalmazás összes parancsán. Ha a paraméter globális, annak értéke bármely parancs hatókörében lehetséges. Egy érték hozzárendelése után bármely parancsból lehet hivatkozni. 

### <a name="required"></a>Kötelező
Ez a jelölőnégyzet azt jelzi, hogy a paraméter értéke kötelező-e a parancs teljesítéséhez vagy befejezéséhez. A válaszokat úgy kell konfigurálni, hogy megkérdezze a felhasználót, hogy adjon meg egy értéket, ha a paraméter kötelezőként van megjelölve.

Vegye figyelembe, hogy ha egy **kötelező paramétert** beállított **alapértelmezett értékkel**, a rendszer továbbra is explicit módon kéri a paraméter értékét.

### <a name="type"></a>Típus
Az egyéni parancsok a következő típusú paramétereket támogatják:

* DateTime
* Földrajzi hely
* Szám
* Sztring

Az összes ilyen típusparaméter, kivéve a földrajzot, támogatja az alapértelmezett érték konfigurációját, amelyet a portálról konfigurálhat.

### <a name="configuration"></a>Konfiguráció
A Configuration paraméter csak a karakterlánc típusú tulajdonsághoz van definiálva. A következő értékek támogatottak:

* **Nincs**.
* **Teljes bevitel elfogadása**: Ha engedélyezve van, a paraméter fogadja az összes bemenetet. Ez a beállítás akkor hasznos, ha a felhasználónak a teljes kilépéssel rendelkező paraméterre van szüksége. Ilyenek például a postai címek.
* **Előre megadott bemeneti értékek elfogadása külső katalógusból**: ez az érték egy olyan paraméter konfigurálására szolgál, amely az értékek széles skáláját feltételezheti. Ilyen például egy értékesítési katalógus. Ebben az esetben a katalógus egy külső webes végponton fut, és egymástól függetlenül is konfigurálható.
* **Előre megadott bemeneti értékek elfogadása belső katalógusból**: ez az érték egy olyan paraméter konfigurálására szolgál, amely néhány értéket feltételez. Ebben az esetben az értékeket a Speech Studióban kell konfigurálni.


### <a name="validation"></a>Érvényesítés
Az érvényesítés olyan paraméterekre vonatkozik, amelyek lehetővé teszik a paraméterek értékére vonatkozó korlátozások konfigurálását. Az egyéni parancsok jelenleg a következő típusparaméter-típusok érvényességét támogatják:

* DateTime
* Szám

## <a name="rules-configuration"></a>Szabályok konfigurálása
Az egyéni parancsok szabályait olyan *feltételek* határozzák meg, amelyek teljesülése esetén végrehajtják a *műveletek* egy csoportját. A szabályok Emellett lehetővé teszik a *végrehajtás utáni állapot* konfigurálását és a *várakozást* a következő bekapcsoláshoz.

### <a name="types"></a>Típusok
Az egyéni parancsok a következő szabályok kategóriáit támogatják:

* **Befejezési szabályok**: ezeket a szabályokat a parancs teljesítése után kell végrehajtani. Az ebben a szakaszban konfigurált összes szabályt, amely esetében a feltételek teljesülnek, végrehajtja a rendszer. 
* **Interakciós szabályok**: ezekkel a szabályokkal további egyéni érvényesítéseket, megerősítéseket és egylépéses korrekciókat konfigurálhat, vagy bármilyen más egyéni párbeszédpanel-logikát is elvégezheti. A rendszer kiértékeli az interakciós szabályokat a feldolgozás minden egyes bekapcsolásakor, és felhasználható a befejezési szabályok elindítására.

A szabály részeként konfigurált különböző műveleteket a szerzői portálon megjelenő sorrendben hajtja végre a rendszer.

### <a name="conditions"></a>Feltételek
A feltételek azon követelmények, amelyeknek teljesülnie kell egy szabály végrehajtásához. A szabályok feltételei a következők lehetnek:

* A **paraméter értéke egyenlő**: a konfigurált paraméter értéke egy adott értékkel egyenlő.
* **Nincs paraméter értéke**: a konfigurált paraméterek nem rendelkezhetnek értékkel.
* **Szükséges paraméterek**: a konfigurált paraméter értékkel rendelkezik.
* Az **összes kötelező paraméter**: a kötelezőként megjelölt összes paraméternek értékkel kell rendelkeznie.
* **Frissített paraméterek**: a rendszer egy vagy több paraméter értékét frissítette az aktuális bevitel (teljes vagy tevékenység) feldolgozásának eredményeképpen.
* A **megerősítés sikeres volt**: a bemeneti vagy a tevékenység sikeres megerősítést kapott (igen).
* A **megerősítés megtagadva**: a bemenő adat vagy tevékenység nem volt sikeres megerősítés (nem).
* A **korábbi parancsot frissíteni kell**: ezt a feltételt akkor használja a rendszer, ha egy adott frissítéssel együtt el szeretné érni a megtagadást. A színfalak mögött ez az állapot akkor van konfigurálva, amikor a párbeszédpanel-motor negatív megerősítést észlel, ha a cél ugyanaz, mint az előző kanyar, és a felhasználó egy frissítéssel válaszolt.

### <a name="actions"></a>Műveletek
* **Beszédfelismerési válasz küldése**: beszédfelismerési válasz küldése az ügyfélnek.
* **Frissítési paraméter értéke**: a parancs paramétereinek értékét egy megadott értékre frissíti.
* **Paraméter értékének törlése**: törölje a parancs paraméterének értékét.
* **Webes végpont hívása**: Hívás kezdeményezése webes végpontra.
* **Tevékenység küldése az ügyfélnek**: egyéni tevékenység küldése az ügyfélnek.

### <a name="expectations"></a>Elvárások
Az elvárások a következő felhasználói bevitel feldolgozására vonatkozó tippeket konfigurálják. A következő típusok támogatottak:

* A **felhasználó jóváhagyását várta**: Ez a várakozás azt adja meg, hogy az alkalmazás megerősítést vár (igen/nem) a következő felhasználói bevitelhez.
* A **rendszer paraméter (ek) et vár a felhasználótól**: Ez a várakozás egy vagy több olyan parancssori paramétert határoz meg, amelyet az alkalmazás a felhasználói beviteltől várt.

### <a name="post-execution-state"></a>Végrehajtás utáni állapot
A végrehajtás utáni állapot az aktuális bevitel (teljes vagy tevékenység) feldolgozását követő párbeszédpanel-állapot. A következő típusokból áll:

* **Jelenlegi állapot megőrzése**: csak az aktuális állapot tartása.
* **Fejezze be a parancsot**: fejezze be a parancsot, és a rendszer nem dolgozza fel a parancs további szabályait.
* **Végrehajtás befejezési szabályai**: hajtsa végre az összes érvényes befejezési szabályt.
* **Várja meg a felhasználó bemenetét**: várjon a következő felhasználói bevitelre.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)
