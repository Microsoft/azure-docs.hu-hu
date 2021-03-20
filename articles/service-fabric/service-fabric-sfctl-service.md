---
title: Azure Service Fabric CLI – sfctl szolgáltatás
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A szolgáltatások, a szolgáltatások típusai és a szolgáltatási csomagok kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245602"
---
# <a name="sfctl-service"></a>sfctl service
Szolgáltatás, szolgáltatások típusai és szolgáltatási csomagok létrehozása, törlése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| alkalmazás neve | Lekéri a szolgáltatás Service Fabric alkalmazásának nevét. |
| kód – csomag listája | Lekérdezi a Service Fabric csomóponton telepített programkódok listáját. |
| létrehozás | Létrehozza a megadott Service Fabric szolgáltatást. |
| delete | Egy meglévő Service Fabric-szolgáltatás törlése. |
| központilag telepített – típus | A Service Fabric-fürt csomópontjain üzembe helyezett alkalmazás megadott szolgáltatástípus információinak beolvasása. |
| telepített típusok listája | Beolvassa a Service Fabric-fürt csomópontjain központilag telepített alkalmazások szolgáltatás típusaira vonatkozó információkat tartalmazó listát. |
| leírás | Lekéri egy meglévő Service Fabric szolgáltatás leírását. |
| tárolók lekérése – naplók | Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait. |
| állapotfigyelő | Lekéri a megadott Service Fabric szolgáltatás állapotát. |
| információ | A Service Fabric alkalmazáshoz tartozó adott szolgáltatás információinak beolvasása. |
| list | Az alkalmazás-azonosító által megadott alkalmazáshoz tartozó összes szolgáltatás információinak beolvasása. |
| manifest | Lekéri a szolgáltatás típusát leíró jegyzékfájlt. |
| csomag üzembe helyezése | A megadott szolgáltatási jegyzékfájlhoz társított csomagok letöltése a megadott csomóponton lévő rendszerkép-gyorsítótárba. |
| csomag – állapot | Egy Service Fabric csomóponthoz és alkalmazáshoz telepített adott alkalmazáshoz tartozó szervizcsomag állapotával kapcsolatos információk beolvasása. |
| csomag – információ | Egy Service Fabric csomóponton telepített szervizcsomagok listájának beolvasása pontosan a megadott névvel egyezően. |
| csomagok listája | Lekéri a Service Fabric csomóponton telepített szervizcsomagok listáját. |
| Visszaszerez | Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálnia a megadott szolgáltatás helyreállítását, amely jelenleg a kvórum elvesztése miatt megakad. |
| jelentés – állapot | Állapotjelentést küld a Service Fabric szolgáltatásról. |
| megoldásához | Service Fabric partíció feloldása. |
| típus – lista | Beolvassa a Service Fabric-fürtben egy kiépített alkalmazás által támogatott szolgáltatási típusokkal kapcsolatos információkat tartalmazó listát. |
| update | Frissíti a megadott szolgáltatást az adott frissítés leírása alapján. |

## <a name="sfctl-service-app-name"></a>sfctl szolgáltatás-alkalmazás neve
Lekéri a szolgáltatás Service Fabric alkalmazásának nevét.

Lekéri az alkalmazás nevét a megadott szolgáltatáshoz. 404 FABRIC_E_SERVICE_DOES_NOT_EXIST hiba történik, ha a megadott AZONOSÍTÓJÚ szolgáltatás nem létezik.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-code-package-list"></a>sfctl szolgáltatás kódja – csomag-lista
Lekérdezi a Service Fabric csomóponton telepített programkódok listáját.

Lekéri az adott alkalmazás Service Fabric csomópontján telepített programkódok listáját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Code-Package-Name | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Service-manifest-Name | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-create"></a>sfctl szolgáltatás létrehozása
Létrehozza a megadott Service Fabric szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziókban a 6.0 + és a "SajátPr/App1" értéknél. |
| --Name [kötelező] | A szolgáltatás neve. Az alkalmazás AZONOSÍTÓjának gyermekének kell lennie. Ez a teljes név, beleértve az `fabric\:` URI-t. Például a szolgáltatás `fabric\:/A/B` az alkalmazás gyermeke `fabric\:/A` . |
| --Service-Type [kötelező] | A szolgáltatás típusának neve. |
| – aktiválási mód | A szolgáltatási csomag aktiválási módja. |
| – megkötések | Az elhelyezési megkötések karakterláncként jelennek meg. Az elhelyezési megkötések logikai kifejezések a csomópont tulajdonságaiban, és lehetővé teszik a szolgáltatások adott csomópontokra való korlátozását a szolgáltatási követelmények alapján. Ha például egy szolgáltatást olyan csomópontokon kíván elhelyezni, ahol a NodeType kék, a következőt kell megadnia: \: "NodeColor = = Blue". |
| --korrelált-szolgáltatás | Annak a célként megadott szolgáltatásnak a neve, amely a következővel összefügg:. |
| – korreláció | Társítsa a szolgáltatást egy meglévő szolgáltatáshoz egy igazítási affinitás használatával. |
| --DNS-Name | A létrehozandó szolgáltatás DNS-neve. Ehhez a beállításhoz engedélyezni kell a Service Fabric DNS rendszerszolgáltatást. |
| – példányok száma | A példányok száma. Ez csak az állapot nélküli szolgáltatásokra vonatkozik. |
| --int-Scheme | Azt jelzi, hogy a szolgáltatást egységesen kell particionálni egy előjel nélküli egész számon belül. |
| --int-Scheme-Count | A létrehozandó egész kulcs tartományán belüli partíciók száma, ha egységes egész partíciós sémát használ. |
| --int-Scheme – magas | A kulcs egész tartományának vége, ha egységes egész partíciós sémát használ. |
| --int-Scheme – alacsony | A kulcs egész tartományának kezdete, ha egységes egész partíciós sémát használ. |
| --Load-metrikák | A csomópontok közötti terheléselosztási szolgáltatásokban használt metrikák JSON-kódolású listája. |
| --min-replika-készlet-méret | A replikakészlet minimális mérete számként. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --Move-Cost | Meghatározza a szolgáltatás áthelyezési költségeit. A lehetséges értékek: \: "Zero", "Low", "medium", "High", "VeryHigh". |
| --named-Scheme | Azt jelzi, hogy a szolgáltatásnak több elnevezett partícióval kell rendelkeznie. |
| --named-Scheme-List | A JSON-kódolású nevek listája a szolgáltatás közötti particionáláshoz a nevesített partíciós séma használata esetén. |
| --nem tartott-állapot | Ha az értéke igaz, ez azt jelzi, hogy a szolgáltatás nem rendelkezik állandó állapottal a helyi lemezen, vagy csak az állapotot tárolja a memóriában. |
| --elhelyezés-szabályzat-lista | A szolgáltatáshoz tartozó elhelyezési házirendek, valamint a hozzájuk tartozó tartománynevek JSON-kódolású listája. A szabályzatok lehetnek:,, \: `NonPartiallyPlaceService` `PreferPrimaryDomain` `RequireDomain` `RequireDomainDistribution` . |
| --kvórum – veszteség – várakozás | Az a maximális időtartam (másodpercben), ameddig a partíciók kvórum elvesztésének állapota engedélyezett. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --replika-újraindítás-várakozás | Az az időtartam (másodpercben), amikor egy replika leáll, és új replika jön létre. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --skálázás – házirendek | A szolgáltatás skálázási házirendjeinek JSON-kódolású listája. |
| --szolgáltatás – elhelyezési idő | Az az időtartam, ameddig a replikák inépítve maradhatnak a Build beragadása előtt. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --egyedi-séma | Azt jelzi, hogy a szolgáltatásnak egyetlen partícióval kell rendelkeznie, vagy nem particionált szolgáltatásnak kell lennie. |
| --stand-by-replika-Keep | Az a maximális időtartam (másodpercben), ameddig a rendszer az Eltávolítás előtt megőrzi a készenléti replikákat. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| – állapot-nyilvántartó | Azt jelzi, hogy a szolgáltatás állapot-nyilvántartó szolgáltatás. |
| – állapot nélküli | Azt jelzi, hogy a szolgáltatás állapot nélküli szolgáltatás. |
| --cél – replika – beállított méret | A célként megadott replika mérete számként. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-delete"></a>sfctl szolgáltatás törlése
Egy meglévő Service Fabric-szolgáltatás törlése.

A törlés előtt létre kell hozni egy szolgáltatást. Alapértelmezés szerint a Service Fabric a szolgáltatás-replikákat egy kecses módon fogja megpróbálni, majd törölni a szolgáltatást. Ha azonban a szolgáltatás szabályosan zárja be a replikát, a törlési művelet hosszú időt vehet igénybe, vagy elakad. A választható ForceRemove jelzővel kihagyhatja a kecses bezárási sorozatot, és kényszerített módon törölheti a szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --Force-Remove | Távolítson el egy Service Fabric alkalmazást vagy szolgáltatást erőteljesen, anélkül, hogy a kecses leállítási sorozatot kellene volna átvennie. Ez a paraméter egy olyan alkalmazás vagy szolgáltatás kényszerített törlésére használható, amelynél a törlés időtúllépés miatt meggátolja a replikák kecses bezárását. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-deployed-type"></a>sfctl szolgáltatás üzembe helyezése – típus
A Service Fabric-fürt csomópontjain üzembe helyezett alkalmazás megadott szolgáltatástípus információinak beolvasása.

Egy adott szolgáltatástípus adatait tartalmazó lista beolvasása egy Service Fabric fürt egyik csomópontján telepített alkalmazásokból. A válasz tartalmazza a szolgáltatás típusának nevét, a regisztrációs állapotát, az azt regisztráló kódot, valamint a szervizcsomag aktiválási AZONOSÍTÓját. Mindegyik bejegyzés egy szolgáltatástípus egy aktiválását jelöli, amelyet az aktiválási azonosító megkülönböztet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-Type-Name [kötelező] | Egy Service Fabric szolgáltatás típusának nevét adja meg. |
| --Service-manifest-Name | A szolgáltatás jegyzékfájljának neve, amely a telepített szolgáltatástípus-információk listáját szűri. Ha meg van adva, a válasz csak a szolgáltatás jegyzékfájljában definiált szolgáltatási típusokra vonatkozó információkat fogja tartalmazni. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl szolgáltatás által telepített-típus-lista
Beolvassa a Service Fabric-fürt csomópontjain központilag telepített alkalmazások szolgáltatás típusaira vonatkozó információkat tartalmazó listát.

Beolvassa a Service Fabric-fürt csomópontjain központilag telepített alkalmazások szolgáltatás típusaira vonatkozó információkat tartalmazó listát. A válasz tartalmazza a szolgáltatás típusának nevét, a regisztrációs állapotát, az azt regisztráló kódot, valamint a szervizcsomag aktiválási AZONOSÍTÓját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name | A szolgáltatás jegyzékfájljának neve, amely a telepített szolgáltatástípus-információk listáját szűri. Ha meg van adva, a válasz csak a szolgáltatás jegyzékfájljában definiált szolgáltatási típusokra vonatkozó információkat fogja tartalmazni. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-description"></a>sfctl-szolgáltatás leírása
Lekéri egy meglévő Service Fabric szolgáltatás leírását.

Lekéri egy meglévő Service Fabric szolgáltatás leírását. A Leírás beszerzése előtt létre kell hozni egy szolgáltatást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-get-container-logs"></a>sfctl szolgáltatás – tároló – naplók
Beolvassa a Service Fabric csomóponton üzembe helyezett tároló naplófájljait.

Beolvassa a tároló naplóit a Service Fabric csomóponton üzembe helyezett tárolóhoz az adott programkódhoz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Code-Package-Name [kötelező] | A szolgáltatás jegyzékfájljában megadott, Service Fabric fürtben egy alkalmazás típusának részeként regisztrált kódlap neve. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| – előző | Megadja, hogy a rendszer beolvassa-e a tároló naplóit a Code Package-példányból kilépett/elhalt tárolóból. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-health"></a>sfctl szolgáltatás állapota
Lekéri a megadott Service Fabric szolgáltatás állapotát.

A megadott szolgáltatás állapotára vonatkozó információk beolvasása. A EventsHealthStateFilter használatával szűrheti a szolgáltatáson jelentett állapotú események gyűjteményét az állapot alapján. A PartitionsHealthStateFilter használatával szűrheti a visszaadott partíciók gyűjteményét. Ha olyan szolgáltatást ad meg, amely nem szerepel az állapotfigyelő tárolóban, a kérelem hibát jelez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --kizárás-Health-Statistics | Azt jelzi, hogy az állapot statisztikáját a lekérdezés eredményének részeként kell-e visszaadni. Alapértelmezés szerint hamis. A statisztikákban a gyermek entitások számának állapota ok, figyelmeztetés és hiba látható. |
| --Partitions-Health-State-Filter | Lehetővé teszi a partíciók állapot-lekérdezés eredményében visszaadott partíciók állapotának szűrését az állapotuk alapján. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. A rendszer csak a szűrőnek megfelelő partíciókat adja vissza. A rendszer minden partíciót felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott érték kombinációja lehet. Ha például a megadott érték 6, akkor a partíciók állapota az OK (2) HealthState értékével, a figyelmeztetés pedig (4) lesz visszaadva.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-info"></a>sfctl szolgáltatás adatai
A Service Fabric alkalmazáshoz tartozó adott szolgáltatás információinak beolvasása.

A megadott Service Fabric alkalmazáshoz tartozó megadott szolgáltatás információit adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-list"></a>sfctl-szolgáltatások listája
Az alkalmazás-azonosító által megadott alkalmazáshoz tartozó összes szolgáltatás információinak beolvasása.

Az alkalmazás-azonosító által megadott alkalmazáshoz tartozó összes szolgáltatás információit adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --szolgáltatás-típus-név | A szolgáltatás lekérdezéséhez használt szolgáltatástípus neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-manifest"></a>sfctl szolgáltatás jegyzékfájlja
Lekéri a szolgáltatás típusát leíró jegyzékfájlt.

Lekéri a szolgáltatás típusát leíró jegyzékfájlt. A válasz karakterláncként tartalmazza a szolgáltatás jegyzékfájljának XML-fájlját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Name [kötelező] | Az alkalmazás típusának neve. |
| --Application-Type-Version [kötelező] | Az alkalmazás típusának verziója. |
| --Service-manifest-Name [kötelező] | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-deploy"></a>sfctl-szolgáltatáscsomag – üzembe helyezés
A megadott szolgáltatási jegyzékfájlhoz társított csomagok letöltése a megadott csomóponton lévő rendszerkép-gyorsítótárba.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Type-Name [kötelező] | A megfelelő kért szolgáltatás jegyzékfájljának alkalmazási jegyzékfájljának neve. |
| --App-Type-Version [kötelező] | Az alkalmazás jegyzékfájljának verziója a megfelelő kért szolgáltatási jegyzékfájlhoz. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-manifest-Name [kötelező] | A letölteni kívánt csomagokhoz társított szolgáltatás jegyzékfájljának neve. |
| --Share-Policy | A megosztási házirendek JSON-kódolású listája. Minden megosztási házirend elem "Name" és "scope" elemekből áll. A név megegyezik a megosztani kívánt kód, konfiguráció vagy adatcsomag nevével. A hatókör a következők egyike lehet: "None", "all", "code", "config" vagy "az". |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-health"></a>sfctl-szolgáltatáscsomag – állapot
Egy Service Fabric csomóponthoz és alkalmazáshoz telepített adott alkalmazáshoz tartozó szervizcsomag állapotával kapcsolatos információk beolvasása.

Egy Service Fabric csomóponton üzembe helyezett adott alkalmazáshoz tartozó szervizcsomag állapotával kapcsolatos információk beolvasása. A EventsHealthStateFilter használatával opcionálisan szűrheti a telepített szervizcsomagban jelentett HealthEvent-objektumok állapotát az állapot alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-Package-Name [kötelező] | A szolgáltatási csomag neve. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-info"></a>sfctl-szolgáltatáscsomag – információ
Egy Service Fabric csomóponton telepített szervizcsomagok listájának beolvasása pontosan a megadott névvel egyezően.

Az adott alkalmazás Service Fabric csomópontján telepített szervizcsomagok információit adja vissza. Ezek az eredmények olyan szolgáltatási csomagok, amelyek neve pontosan egyezik a paraméterként megadott szolgáltatási csomag nevével.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Service-Package-Name [kötelező] | A szolgáltatási csomag neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-package-list"></a>sfctl-csomag – lista
Lekéri a Service Fabric csomóponton telepített szervizcsomagok listáját.

Az adott alkalmazás Service Fabric csomópontján telepített szervizcsomagok információit adja vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-recover"></a>sfctl szolgáltatás helyreállítása
Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálnia a megadott szolgáltatás helyreállítását, amely jelenleg a kvórum elvesztése miatt megakad.

Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálnia a megadott szolgáltatás helyreállítását, amely jelenleg a kvórum elvesztése miatt megakad. Ezt a műveletet csak akkor kell végrehajtani, ha ismert, hogy a leállított replikák nem állíthatók helyre. Az API helytelen használata lehetséges adatvesztést eredményezhet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-report-health"></a>sfctl szolgáltatás jelentés – állapot
Állapotjelentést küld a Service Fabric szolgáltatásról.

A megadott Service Fabric szolgáltatás állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric Gateway szolgáltatásnak, amely továbbítja az állapot-áruháznak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés az állapotfigyelő áruházban volt-e alkalmazva, ellenőrizze, hogy a jelentés megjelenik-e a szolgáltatás állapotának eseményei között.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Health-Property [kötelező] | Az állapotadatok tulajdonsága. <br><br> Az entitások különböző tulajdonságokhoz tartozhatnak állapotjelentést. A tulajdonság egy karakterlánc, nem pedig rögzített enumerálás, amely lehetővé teszi, hogy a jelentéskészítő rugalmasan kategorizálja a jelentést kiváltó állapot feltételeit. A "LocalWatchdog" SourceId forrásazonosító rendelkező jelentéskészítő például nyomon követheti a csomópontok rendelkezésre álló lemezének állapotát, így az adott csomópont "AvailableDisk" tulajdonságát is jelentheti. Ugyanaz a riporter figyelheti a csomópontok kapcsolatát, így a "kapcsolat" tulajdonságot is jelentheti ugyanazon a csomóponton. Az állapotfigyelő szolgáltatásban ezek a jelentések különálló állapotadatokként lesznek kezelve a megadott csomópont esetében. A SourceId forrásazonosító együtt a tulajdonság egyedileg azonosítja az állapotadatok adatait. |
| --állapotadatok [kötelező] | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "OK", "figyelmeztetés", "hiba", "ismeretlen". |
| --Service-ID [kötelező] | A szolgáltatás identitása. <br><br> Ez általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása a korábbi verziókban a következő: "SajátPr \~ App1 \~ svc1", 6.0 + és "SajátPr/App1/svc1". |
| – forrás-azonosító [kötelező] | Az állapottal kapcsolatos adatokat létrehozó ügyfél/watchdog/rendszer összetevőt azonosító forrás neve. |
| – Leírás | Az állapotadatok leírása. <br><br> A jelentésből származó, emberi olvasásra alkalmas adatok hozzáadására szolgáló szabad szöveget jelöli. A Leírás maximális hossza 4096 karakter. Ha a megadott karakterlánc már nem érhető el, a rendszer automatikusan csonkolja. A csonkítás során a Leírás utolsó karakterei a "[csonkolt]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi, hogy a felhasználók csonkítva lettek. Vegye figyelembe, hogy a csonkítás során a Leírás kevesebb, mint 4096 karakterből áll az eredeti sztringből. |
| – azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e juttatni. <br><br> Egy állapotjelentés érkezik egy Service Fabric Gateway-alkalmazásba, amely továbbítja az állapot-áruháznak. Ha az azonnali beállítás értéke TRUE (igaz), a rendszer azonnal elküldi a jelentést a HTTP-átjáróról az állapotfigyelő tárolóba, függetlenül a HTTP-átjáró alkalmazás által használt háló-ügyfél beállításaitól. Ez olyan kritikus fontosságú jelentések esetében hasznos, amelyeket a lehető leghamarabb el kell juttatni. Az Időzítéstől és az egyéb feltételektől függően előfordulhat, hogy a jelentés küldése továbbra is meghiúsul, például ha a HTTP-átjáró be van zárva, vagy az üzenet nem éri el az átjárót. Ha az azonnali beállítás hamis értékre van állítva, a rendszer a HTTP-átjáró állapot-ügyfélbeállítások alapján elküldi a jelentést. Ezért a HealthReportSendInterval-konfigurációnak megfelelően kötegbe kerül. Ez az ajánlott beállítás, mivel lehetővé teszi, hogy az állapot-ügyfél optimalizálja az állapot-jelentési üzeneteket az állapotfigyelő tárolóba, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a rendszer nem küldi el azonnal a jelentéseket. |
| --Remove-when-lejárt | Az érték, amely azt jelzi, hogy a jelentés törlődik-e a Health Store-ból, amikor lejár. <br><br> Ha az értéke TRUE (igaz), a rendszer eltávolítja a jelentést az állapot-áruházból a lejárat után. Ha hamis értékre van állítva, a jelentés a lejártkor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeresen jelentést küldenek, a Eltávolításlejáratkor false (alapértelmezett) értéket kell beállítania. Így a riporter problémákba ütközik (például holtpont), és nem tud jelentést készíteni, az entitást a rendszer hiba esetén kiértékeli, amikor az állapotjelentés lejár. Ez az entitás a hiba állapotának megfelelően jelenik meg. |
| --Sequence-Number | Az állapotjelentés sorszáma numerikus karakterláncként. <br><br> A jelentés sorszámát a Health Store használja az elavult jelentések észlelésére. Ha nincs megadva, a rendszer automatikusan létrehozza a sorszámot, amikor egy jelentés hozzáadása történik. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| --TTL | Az az időtartam, ameddig ez az állapotjelentés érvényes. Ez a mező ISO8601 formátumot használ az időtartam megadásához. <br><br> Amikor az ügyfelek rendszeresen jelentést küldenek, a jelentéseknek az élettartamuk során nagyobb gyakorisággal kell elküldeniük a jelentéseket. Ha az ügyfelek áttérnek a váltásra, beállíthatja, hogy az idő a végtelen értékre legyen állítva. Ha a lejárati idő lejár, az állapottal kapcsolatos információkat tartalmazó állapotot a rendszer eltávolítja az állapotfigyelő tárolóból, ha a Eltávolításlejáratkor értéke TRUE (igaz), vagy hiba esetén kiértékelt, ha a Eltávolításlejáratkor hamis. Ha nincs megadva, a rendszer az alapértelmezett élettartamot a végtelen értékre adja. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-resolve"></a>sfctl szolgáltatás feloldása
Service Fabric partíció feloldása.

A szolgáltatás-replikák végpontjának lekéréséhez oldja fel Service Fabric-szolgáltatás partícióját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --Partition-Key-Type | A partícióhoz tartozó kulcs típusa. Ezt a paramétert akkor kell megadni, ha a szolgáltatás Int64Range vagy névvel rendelkezik. A lehetséges értékek a következők. -None (1) – azt jelzi, hogy nincs megadva a PartitionKeyValue paraméter. Ez a particionálási sémát tartalmazó partíciók esetében érvényes. Ez az alapértelmezett érték. Az érték 1. -Int64Range (2) – azt jelzi, hogy a PartitionKeyValue paraméter egy Int64 partíciós kulcs. Ez érvényes a particionálási sémával rendelkező partíciók Int64Range. Az érték 2. -Named (3) – azt jelzi, hogy a PartitionKeyValue paraméter a partíció neve. Ez a particionálási sémával ellátott partíciók esetében érvényes. Az érték 3. |
| --Partition-Key-Value | Partíciós kulcs. Erre akkor van szükség, ha a szolgáltatás partíciós sémája Int64Range vagy nevesített. Ez nem a partíció azonosítója, hanem az egész szám kulcs értéke vagy a partíció-azonosító neve. Ha például a szolgáltatás 0 és 10 közötti tartományon belüli partíciót használ, akkor a PartitionKeyValue egész számnak kell lennie az adott tartományban. A lekérdezési szolgáltatás leírása a tartomány vagy a név megtekintéséhez. |
| --korábbi-RSP-Version | A korábban kapott válasz verzió mezőjében szereplő érték. Erre akkor van szükség, ha a felhasználó tudja, hogy a korábban felütött eredmény elavult. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-type-list"></a>sfctl szolgáltatás típusa – lista
Beolvassa a Service Fabric-fürtben egy kiépített alkalmazás által támogatott szolgáltatási típusokkal kapcsolatos információkat tartalmazó listát.

Beolvassa a Service Fabric-fürtben egy kiépített alkalmazás által támogatott szolgáltatási típusokkal kapcsolatos információkat tartalmazó listát. A megadott alkalmazás típusának léteznie kell. Ellenkező esetben a rendszer 404 állapotot ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Name [kötelező] | Az alkalmazás típusának neve. |
| --Application-Type-Version [kötelező] | Az alkalmazás típusának verziója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-service-update"></a>sfctl szolgáltatás frissítése
Frissíti a megadott szolgáltatást az adott frissítés leírása alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása a korábbi verziókban a következő: "SajátPr \~ App1 \~ svc1", 6.0 + és "SajátPr/App1/svc1". |
| – megkötések | Az elhelyezési megkötések karakterláncként jelennek meg. Az elhelyezési megkötések logikai kifejezések a csomópont tulajdonságaiban, és lehetővé teszik a szolgáltatások adott csomópontokra való korlátozását a szolgáltatási követelmények alapján. Ha például egy szolgáltatást olyan csomópontokon kíván elhelyezni, ahol a NodeType kék, a következőt kell megadnia: \: "NodeColor = = Blue". |
| --korrelált-szolgáltatás | Annak a célként megadott szolgáltatásnak a neve, amely a következővel összefügg:. |
| – korreláció | Társítsa a szolgáltatást egy meglévő szolgáltatáshoz egy igazítási affinitás használatával. |
| – példányok száma | A példányok száma. Ez csak az állapot nélküli szolgáltatásokra vonatkozik. |
| --Load-metrikák | A csomópontok közötti terheléselosztás során használt metrikák JSON-kódolású listája. |
| --min-replika-készlet-méret | A replikakészlet minimális mérete számként. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --Move-Cost | Meghatározza a szolgáltatás áthelyezési költségeit. A lehetséges értékek: \: "Zero", "Low", "medium", "High", "VeryHigh". |
| --elhelyezés-szabályzat-lista | A szolgáltatáshoz tartozó elhelyezési házirendek, valamint a hozzájuk tartozó tartománynevek JSON-kódolású listája. A szabályzatok lehetnek:,, \: `NonPartiallyPlaceService` `PreferPrimaryDomain` `RequireDomain` `RequireDomainDistribution` . |
| --kvórum – veszteség – várakozás | Az a maximális időtartam (másodpercben), ameddig a partíciók kvórum elvesztésének állapota engedélyezett. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --replika-újraindítás-várakozás | Az az időtartam (másodpercben), amikor egy replika leáll, és új replika jön létre. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --skálázás – házirendek | A szolgáltatás skálázási házirendjeinek JSON-kódolású listája. |
| --szolgáltatás – elhelyezési idő | Az az időtartam, ameddig a replikák inépítve maradhatnak a Build beragadása előtt. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --stand-by-replika-Keep | Az a maximális időtartam (másodpercben), ameddig a rendszer az Eltávolítás előtt megőrzi a készenléti replikákat. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| – állapot-nyilvántartó | Azt jelzi, hogy a célként megadott szolgáltatás állapot-nyilvántartó szolgáltatás. |
| – állapot nélküli | Azt jelzi, hogy a célként megadott szolgáltatás állapot nélküli szolgáltatás. |
| --cél – replika – beállított méret | A célként megadott replika mérete számként. Ez csak az állapot-nyilvántartó szolgáltatásokra vonatkozik. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
