---
title: A Bing Search API-k használatának és megjelenítésének követelményei
titleSuffix: Azure Cognitive Services
description: Az alkalmazásokban a Bing Search API-k keresési eredményeinek megjelenítéséhez szükséges követelmények.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 4300860039b76bd076f19acfc252f59ef10d462b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351844"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Bing Search API – követelmények használata és megjelenítése

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ezek a használati és megjelenítési követelmények érvényesek a tartalom és a kapcsolódó információk a következő Bing Search API-kból történő megvalósítására, beleértve a kapcsolatokat, a metaadatokat és az egyéb jeleket.

- Bing – Egyéni keresés
- Bing – Entitáskeresés
- Bing – Képkeresés
- Bing – Hírkeresés
- Bing – Videokeresés
- Bing vizuális keresés
- Bing Web Search
- Bing – Helyesírás-ellenőrzés
- Bing – Automatikus kiegészítés

## <a name="definitions"></a>Definíciók


|Időszak  |Leírás  |
|---------|---------|
|Válasz     | A válaszban visszaadott eredmények kategóriája. A Bing Web Search API válasza például tartalmazhat válaszokat a weblapok eredményei, a képek, a videók, a vizualizáció és a hírek kategóriába. |
|Reagálás     | Minden, a keresési API-ra irányuló hívásra válaszként kapott válasz és kapcsolódó érték. |
|Eredmény    | Egy adott válaszban található információ. Az egyetlen újságcikkhez kapcsolódó adathalmaz például a hírek válaszának eredménye. |
|Keresési API-k    | együttesen a Bing Custom Search, Entity Search, Image Search, News Search, Video Search, Visual Search, helyi üzleti keresés és Web Search API-k. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Bing Spell Check és Bing Autosuggest API korlátozások

nem:

- A Bing Spell Check vagy Bing Autosuggest API-k által fogadott adatok másolása, tárolása vagy gyorsítótárazása.
- Az Bing Spell Check vagy Bing Autosuggest API-kkal kapott adatok bármilyen gépi tanulási vagy hasonló algoritmikus tevékenység részeként használhatók. Ne használja ezeket az adatmennyiséget olyan új vagy meglévő szolgáltatások betanítására, kiértékelésére vagy javítására, amelyeket Ön vagy harmadik fél is felkínálhat.

## <a name="bing-search-apis"></a>Bing Search API-k

> [!NOTE]
> Az ebben a szakaszban szereplő követelmények csak a keresési API-kra vonatkoznak, amelyek nem foglalják magukban Bing Spell Check vagy Bing Autosuggest. 

### <a name="internet-search-experience-requirements"></a>Internetes keresési élményre vonatkozó követelmények

A válaszokban visszaadott összes adat csak internetes keresési élményekben használható. Az internetes keresési élmény a megjelenő tartalmat jelenti: 

- Fontos és reagál a végfelhasználó közvetlen lekérdezésére, vagy a keresési érdek és a szándék (például egy felhasználó által jelzett keresési lekérdezés) egyéb jelzésére. 

- Segít a felhasználóknak megkeresni és navigálni a válasz adatforrásaihoz. Például a válaszban a hiperhivatkozásokból kattintható hivatkozások jelennek meg.

- Több eredményt is tartalmaz a felhasználó számára a kiválasztáshoz. 

- Olyan elhelyezést használ, amely lehetővé teszi a felhasználók számára a keresést.

- Egy látható jelzést tartalmaz arra vonatkozóan, hogy a tartalom internetes keresési eredmény. Például egy nyilatkozat arról, hogy a tartalom "a webről".

- Minden más megfelelő intézkedést tartalmaz annak biztosítására, hogy a Bing Search API-adatai ne sértsék semmilyen alkalmazandó törvényt vagy harmadik féltől származó jogot. A jogi tanácsadókkal megtekintheti, hogy milyen mértékekre lehet szükség.

Az internetes keresési élményre vonatkozó követelmények alól egyetlen kivétel az URL-felderítés, a cikk későbbi részében leírtak szerint. 

### <a name="restrictions"></a>Korlátozások

nem:

- A válaszokból származó adatok másolása, tárolása vagy gyorsítótárazása (kivéve a [szolgáltatás folytonossága](#continuity-of-service)által megengedett mértékig történő megőrzést. 

- A keresési API-kon keresztül fogadott adatok használata bármely gépi tanulási vagy hasonló algoritmikus tevékenység részeként. Ne használja ezeket az adatmennyiséget olyan új vagy meglévő szolgáltatások betanítására, kiértékelésére vagy javítására, amelyeket Ön vagy harmadik fél is felkínálhat.

- Módosítsa az eredmények tartalmát (kivéve, ha olyan módon formázza őket, amely nem sérti a többi követelményt), hacsak a törvény nem írja elő vagy nem fogadja el a Microsoft. 

- Kihagyhatja az eredmény tartalmához társított kiosztási adatokat és URL-címeket.

- Átrendezés, beleértve a kihagyást is, a válaszban megjelenő eredmények a megrendelés vagy rangsorolás megadásakor, hacsak a törvény nem kötelezi, vagy a Microsoft nem fogadja el. 

    > [!NOTE]
    > Ez a követelmény nem vonatkozik a Bing Custom Search API-portálon keresztül megvalósított átrendezésre.

- A válasz bármely részén belül más tartalmakat jeleníthet meg úgy, hogy a felhasználó azt feltételezi, hogy a másik tartalom a válasz részét képezi. 

- Jelenítse meg a Microsoft által nem biztosított hirdetéseket bármely olyan oldalon, amely a válasz bármely részét megjeleníti. 

- Jelenítse meg a válaszokat a lapokon:
    - A Bing-rendszerkép, News Search, Video Search vagy Visual Search API-k
    - Amelyek szűrése vagy korlátozása elsődlegesen (vagy kizárólag) a képekre, hírekre és/vagy videó-vagy vizuális keresési eredményekre.

### <a name="notices-and-branding"></a>Megjegyzések és védjegyezés 
Nem

- Kiemelten szerepel a [Microsoft adatvédelmi nyilatkozatához](https://go.microsoft.com/fwlink/?LinkId=521839)tartozó funkcionális hivatkozás is, amely a felhasználói élmény (UX) minden olyan pontján elérhető, amely lehetővé teszi a felhasználó számára a keresési lekérdezés bevitelét. Címkézze fel a **Microsoft adatvédelmi nyilatkozatát**.

- Kiemelten jeleníti meg a Bing branding- [használati irányelvekkel](https://go.microsoft.com/fwlink/?linkid=833278)összhangban lévő Bing branding útmutatót, amely a felhasználó számára lehetővé teszi a keresési lekérdezés bevitelét. Az ilyen védjegyezésnek egyértelműen meg kell felelnie a felhasználónak, hogy a Microsoft az internetes keresési élményt kikapcsolja.

- A Microsoft számára a Bing Web Search, Image Search, News Search, Video Search és Visual Search API-k által megjelenő egyes válaszokat (vagy válaszokat) is hozzárendelheti a Microsoftnak, hacsak a Microsoft nem adja meg a használathoz szükséges egyéb írást. Ez a Bing- [védjeggyel kapcsolatos használati útmutatóban](https://go.microsoft.com/fwlink/?linkid=833278)olvasható. 

nem:

- A Bing Custom Search APIból a Microsoftnak megjelenő attribútum-válaszok (vagy válaszok részei), kivéve, ha a Microsoft másképpen ír az adott használatra.

### <a name="transferring-responses"></a>Válaszok átvitele

Ha engedélyezi a felhasználó számára, hogy egy keresési API-ról egy másik felhasználóra vigye át a választ, például egy üzenetküldési alkalmazás vagy egy közösségi média-közzététel segítségével, a következők érvényesek: 

- Az átvitt válaszoknak a következőket kell tartalmazniuk:
  - Olyan tartalomból állhat, amely nem módosult az átadó felhasználó számára megjelenített válaszok tartalmából. A formázás módosításai megengedettek.
  - Nem tartalmaz adatokat a metaadatok űrlapján.
  - A Bing web, a kép, a hírek, a videó és a vizualizáció API-k válaszai esetén a válasz megjelenítési nyelve a Bing által működtetett internetes keresési élményen keresztül érhető el. Megjelenítheti például a "Bing" vagy a "További információ a Bingről" vagy a Bing emblémát is.
  - A Bing Custom Search APIra adott válaszok esetében a válasz megjelenítési nyelve az internetes keresési élményen keresztül érhető el. Megjelenítheti például a "További információ a keresési eredményekről" kifejezést.
  - Kiemelten megjeleníti a válasz létrehozásához használt teljes lekérdezést.
  - Vegyen fel egy kiemelt hivatkozást, vagy hasonlítson a válasz alapjául szolgáló forráshoz, közvetlenül vagy a keresőmotoron keresztül (bing.com, m.bing.com vagy az egyéni keresési szolgáltatáson keresztül).
- Előfordulhat, hogy nem automatizálja a válaszok átvitelét. Az átvitelt egy felhasználói műveletnek kell kezdeményeznie, amely egyértelműen igazolja a válaszok átvitelének szándékát.
- Csak akkor engedélyezheti a felhasználók számára, hogy átvigye az átadó felhasználó lekérdezésére adott válaszban megjelenített válaszokat.

### <a name="continuity-of-service"></a>A szolgáltatás folytonossága 

Ne másolja, tárolja vagy gyorsítótárazza a keresési API-válaszok adatait. A szolgáltatás-hozzáférés és az adatmegjelenítés folytonosságának lehetővé tételéhez azonban az eredményeket kizárólag az alábbi feltételek teljesülése esetén lehet megőrizni:

#### <a name="device"></a>Eszköz

Lehetővé teheti, hogy a felhasználó a lekérdezés időpontjától számított 24 órán belül megőrizze az eredményeket az eszközön, vagy (II), amíg a felhasználó egy másik lekérdezést nem küld a frissített eredményekre, amennyiben a megőrzött eredmények csak a következőket használhatják:

- Annak engedélyezéséhez, hogy a felhasználó hozzáférjen az adott eszközhöz korábban visszaadott eredményekhez (például szolgáltatás megszakítása esetén).
- Ha az előjelzéses lekérdezés eredményét szeretné tárolni a felhasználó igényei szerint, a felhasználó által megadott jelek alapján (például az előre jelzett szolgáltatás megszakítása esetén).

#### <a name="server"></a>Kiszolgáló

Az egyes felhasználókra vonatkozó eredményeket biztonságosan megtarthatja az Ön által felügyelt kiszolgálón, és csak a megőrzött eredményeket jelenítheti meg:

- Annak engedélyezéséhez, hogy a felhasználó hozzáférhessen az adott felhasználóhoz korábban visszaadott eredmények korábbi jelentéseihez a megoldásban. Előfordulhat, hogy az eredmények a végfelhasználó kezdeti lekérdezésének idejétől számítva több mint 21 napnál nem maradnak meg (i), és (II) a felhasználó új vagy ismétlődő lekérdezésére adott válaszban.
- Az előjelzéses lekérdezés eredményeinek tárolásához a felhasználó igényei alapján, az adott felhasználó jelei alapján. Ezeket az eredményeket a lekérdezés időpontjától számított 24 órán belül tárolhatja, vagy (II) egészen addig, amíg egy felhasználó nem küld újabb lekérdezést a frissített eredményekhez.

Ha megtartja, az adott felhasználóhoz tartozó eredmények nem commingled egy másik felhasználó eredményeivel. Ez azt eredményezi, hogy az egyes felhasználók eredményeit külön kell megőrizni és teljesíteni.

### <a name="general"></a>Általános kérdések 

A megőrzött eredmények összes megjelenítéséhez:

- Vegyen fel egy egyértelmű, látható értesítést a lekérdezés elküldésekor.
- A felhasználónak egy gomb vagy hasonló módon kell bemutatnia az ismételt lekérdezéshez és a frissített eredmények beszerzéséhez. 
- Tartsa meg a Bing arculatát az eredmények megjelenítésében.
- Törölje (és szükség esetén frissítsen egy új lekérdezéssel) a tárolt eredményeket a megadott időkereten belül.

### <a name="non-display-url-discovery"></a>Nem megjelenített URL-felderítés 

A keresési válaszokat csak a nem internetes keresési élményekben használhatja arra, hogy a felhasználó vagy az ügyfél által küldött lekérdezésre válaszoló információforrások URL-címeinek felfedésére legyen lehetőség. Az ilyen URL-címeket egy jelentésben vagy az Ön által megadott hasonló válaszban is másolhatja:

- Csak az adott felhasználó vagy ügyfél számára, a lekérdezésre válaszul.
- Csak akkor, ha a lekérdezéshez kapcsolódóan jelentős, további értékes tartalmat tartalmaz.

A keresési API-k és a megjelenítési követelmények korábbi fejezetei nem vonatkoznak erre a nem megjelenített használatra, a következők kivételével: 

- Ne gyorsítótárazza, másolja vagy ne tárolja a keresési válaszból származó adatokat vagy tartalmakat, illetve a nem a korábban leírt korlátozott URL-cím másolását.
- Győződjön meg arról, hogy a keresési API-k által fogadott adatok (beleértve a URL-címeket is) nem sértik semmilyen alkalmazandó törvényt vagy harmadik féltől származó jogot.
- Ne használja a keresési API-k által fogadott adatok (beleértve a URL-címeket) a keresési index, a gépi tanulás vagy a hasonló algoritmikus tevékenység részeként. Ne használja ezeket az adatkészleteket olyan szolgáltatások létrehozásához, kiértékeléséhez vagy fejlesztéséhez, amelyeket Ön vagy harmadik fél is felkínálhat.

## <a name="gdpr-compliance"></a>GDPR-megfelelőség  

Az Európai Unió Általános adatvédelmi rendelet (GDPR) és a keresési API-k, Bing Spell Check API vagy Bing Autosuggest API meghívásával kapcsolatban feldolgozott személyes adat tekintetében tudomásul kell vennie, hogy Ön és a Microsoft független adatkezelők a GDPR. Ön függetlenül felelős a GDPR való megfelelőségért.