---
title: Az Azure Data Catalog gyakori forgatókönyvei
description: A Azure Data Catalog gyakori forgatókönyvei – többek között a nagy értékű adatforrások regisztrálása és felderítése, az önkiszolgáló üzleti intelligencia engedélyezése, valamint az adatforrásokkal és folyamatokkal kapcsolatos meglévő ismeretek rögzítése.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4f76a9696d613a4a974c9ae7c7b2578e56f2fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674614"
---
# <a name="azure-data-catalog-common-scenarios"></a>Az Azure Data Catalog gyakori forgatókönyvei

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ez a cikk olyan gyakori forgatókönyveket mutat be, amelyekben a Azure Data Catalog segíthetnek a szervezetnek a meglévő adatforrásokból származó további értékek megszerzésében.

## <a name="scenario-1-registration-of-central-data-sources"></a>1. forgatókönyv: központi adatforrások regisztrálása
A szervezeteknek gyakran sok nagy értékű adatforrásuk van. Ezek az adatforrások az üzletági, az online tranzakció-feldolgozási (OLTP) rendszerek, az adattárházak és az üzleti intelligencia/elemzési adatbázisok közé tartoznak. A rendszerek száma és a köztük lévő átfedések általában az üzleti igényeknek megfelelően növekednek, és maga a vállalat is fejlődik, például az egyesülések és a beszerzések.

Nehéz lehet a szervezet tagjainak tudnia, hogy hol találhatók az adatforrásokban lévő információk. Többek között az alábbiakhoz hasonló kérdések:

* A vállalaton belül használt három HR-rendszerből, amelyet az ilyen típusú jelentések létrehozásához használok?
* Hol érdemes beolvasni a pénzügyi év minősített értékesítési számát, amely éppen véget ért?
* Kinek kell megkérdezni, vagy mi az a folyamat, amelyet az adattárházhoz való hozzáféréshez használok?
* Nem tudom, hogy ezek a számok megfelelőek-e. Ki Kérhetek betekintést arról, hogyan használják ezeket az információkat az irányítópultnak a csapattal való megosztása előtt?

Ezekhez és egyéb kérdésekhez Azure Data Catalog válaszokat is biztosíthat. A szervezetek között használt központi, nagy értékű, felügyelt adatforrások gyakran a katalógus feltöltésének logikai kiindulási pontja. Habár bármely felhasználó regisztrálhat egy adatforrást, a katalógus elindítását az adatforrásokkal kezdi meg, amelyek a legnagyobb valószínűséggel biztosítják a felhasználók számára a rendszer bevezetését és használatát. 

Ha a Azure Data Catalog használatának első lépéseiben bemutatjuk, hogy az adatforrások számos különböző csapata által használt fő adatforrások azonosítása és regisztrálása az első lépés a siker érdekében.

Ez a forgatókönyv arra is lehetőséget ad, hogy a nagy értékű adatforrásokat is jegyezze fel, hogy könnyebben megértsék és hozzáférjenek. Ennek a tevékenységnek az egyik fő szempontja, hogy a felhasználók hogyan igényelhetnek hozzáférést az adatforráshoz. A Azure Data Catalog segítségével megadhatja annak a felhasználónak vagy csoportnak az e-mail-címét, amely az adatforrás-hozzáférés szabályozására, a meglévő eszközökre vagy dokumentációra mutató hivatkozásokat, illetve a hozzáférési kérelmek folyamatát ismertető ingyenes szöveget tartalmazza. Ez az információ segít a regisztrált adatforrásokat felderítő tagoknak, de még nem rendelkezik engedéllyel az adatok eléréséhez, hogy az adatforrások tulajdonosai által meghatározott és vezérelt folyamatokkal könnyen hozzáférhessenek a hozzáféréshez.

## <a name="scenario-2-self-service-business-intelligence"></a>2. példa: önkiszolgáló üzleti intelligencia
Bár a hagyományos vállalati üzleti intelligencia-megoldások továbbra is a számos szervezet adatterületének értékes részét képezik, az üzleti élet változó üteme egyre több és még fontosabb. Az önkiszolgáló BI használatával az információkkal dolgozó szakemberek és elemzők saját jelentéseket, munkafüzeteket és irányítópultokat hozhatnak létre anélkül, hogy egy központi informatikai csapatra támaszkodnak, vagy az IT-csoport ütemezése és rendelkezésre állása korlátozza őket.

Az önkiszolgáló BI-forgatókönyvekben a felhasználók gyakran egyesítenek több forrásból származó adatokkal, amelyek közül sokan nem voltak korábban a BI és az Analysis szolgáltatásban. Bár előfordulhat, hogy az ilyen adatforrások némelyike már ismert, kihívást jelenthet, ha felderíti, mi a teendő egy adott feladat lehetséges adatforrásainak megkereséséhez és kiértékeléséhez.

Ez a felderítési folyamat hagyományosan az egyik kézi: az elemzők a társ hálózati kapcsolataikat használják a keresett adatmennyiséggel dolgozó mások azonosítására. Miután megtalálta és felhasználta az adatforrást, a folyamat újra megismétlődik minden további önkiszolgáló BI-tevékenységnél, és több felhasználó végzi el a felderítési folyamat egy redundáns manuális feldolgozását.

A Azure Data Catalog segítségével a szervezet megszakíthatja a munkafolyamatot. Az adatforrások hagyományos módon történő felfedezése után az elemző regisztrálhatja azt, hogy a későbbiekben könnyebben felderíthető legyen a többi felhasználó számára. Bár az elemző több értéket is hozzáadhat a regisztrált adategységek megjegyzésével, ez a jegyzet nem szükséges a regisztrációval megegyező időpontban. A felhasználók idővel hozzájárulhatnak a katalógusban regisztrált adatforrások értékének fokozatos hozzáadásához.

A katalógus tartalmának szerves növekedése természetes módon kiegészíthető a központi adatforrások előzetes regisztrálásával. A katalógus előzetes kitöltése olyan adatokkal, amelyeknek számos felhasználónak szüksége lehet a kezdeti használat és a felderítés ösztönzésére. Ha engedélyezni szeretné a felhasználók számára a további források regisztrálását és megjegyzéseit, megtarthatja azokat, és más szervezeti tagokat is felvehet.

Érdemes megjegyezni, hogy bár ez a forgatókönyv kifejezetten az önkiszolgáló BI-ra koncentrál, ugyanazok a minták és kihívások érvényesek a nagyméretű vállalati BI-projektekre is. A Data Catalog használatával a szervezete javíthatja az adatforrások felderítésének manuális folyamatát magában foglalja az összes erőfeszítést.

## <a name="scenario-3-capturing-tribal-knowledge"></a>3. forgatókönyv: a törzsi ismeretek rögzítése
Honnan tudhatja, hogy milyen adatokra van szüksége a feladatokhoz, és hol találhatja meg ezeket az adatok körét?

Ha már elvégezte a feladatot egy ideig, valószínűleg csak tudja. Egy fokozatos tanulási folyamaton esett át, és az idő múlásával megismertük a napi munkához kulcsfontosságú adatforrásokat.

Amikor egy új alkalmazott összekapcsolja a csapatot, hogyan tudja, hogy a felhasználó milyen adatokra van szüksége a feladatokhoz, és hol találja?

Az is előfordulhat, hogy az új személy felveszi Önnel a kérdést.

Ez a törzsi ismeretek folyamatos átvitele az adatforrások felderítési folyamatának része a nagy és kicsi szervezeteknél. Az évek során egyre több vezető és tapasztalt csapat tagjai készítettek ismereteket, és az újabb csapattagok megtanulták, hogy kérdéseket tegyenek fel. A legfontosabb információk gyakran csak néhány kulcsfontosságú személy feje, és ha azok a személyek nyaralnak vagy hagyják el a csapatot, a szervezet szenved.

Az adatszakértők általában erőfeszítéseket tesznek az ismereteik dokumentálására, e-mailben vagy Word-dokumentumokban való megosztására egy csoport SharePoint-webhelyén. Bár ez a megközelítés értékes lehet, egy új felderítési probléma bevezetését mutatja be: Honnan tudhatják meg, hogy milyen dokumentáció létezik, és hol találhatók?

Azure Data Catalog a szervezet egyetlen, központi helyen tárolja a törzsi ismereteket, és így könnyen felderíthetővé válik. Data Catalog az adategységek közvetlenül láthatják az adategységeket, és hivatkozásokat biztosítanak a meglévő dokumentációhoz. Ha a szervezet tagjai a katalógus használatával derítenek fel egy adatforrást, nem csupán a forrást fogják találni, hanem azt is, hogy a korábban már csak a szervezet szakértőinek tagjaiban létezett.
