---
title: Identitásmodell
titleSuffix: An Azure Communication Services concept
description: Az identitások és a hozzáférési jogkivonatok megismerése
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5e53f9e89dd9718111da4f8016bc3e5a7f791627
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496575"
---
# <a name="identity-model"></a>Identitásmodell

Az Azure kommunikációs szolgáltatások identitás-agnosztikus szolgáltatás. Ez a kialakítás több előnyt kínál:

- A meglévő identitások újrafelhasználása az Identity Management rendszerből
- Rugalmasságot biztosít az integrációs forgatókönyvekhez
- Identitások magánjellegűvé tartása az Azure kommunikációs szolgáltatásokban

Az információk a rendszerbe való másolása helyett meg kell őriznie az üzleti esethez szükséges leképezési kapcsolatot. Például leképezheti az identitásokat 1:1, 1: N, N:1, N:M. Az Azure kommunikációs szolgáltatásokban nem használhatók olyan külső azonosítók, mint a telefonszámok, a felhasználók, az eszközök, az alkalmazások és a GUID azonosítók. Az Azure kommunikációs szolgáltatások identitásához létrehozott hozzáférési jogkivonatok olyan primitívek elérésére szolgálnak, mint a csevegés vagy a hívás.

## <a name="identity"></a>Identitás

Az identitásokat az Azure kommunikációs szolgáltatások Identity Library használatával hozhatja létre. Az identitás azonosítóként szolgál a beszélgetésekben. Hozzáférési tokenek létrehozásához használatos. Ugyanaz az identitás több egyidejű munkamenetben is részt vehet több eszközön. Egy identitás egyszerre több aktív hozzáférési jogkivonattal rendelkezhet.

Egy identitás, erőforrás vagy előfizetés törlése érvényteleníti az összes hozzáférési jogkivonatát. Ezzel a művelettel az identitáshoz tárolt összes adattal is törlődik. A törölt identitások nem hozhatnak létre új hozzáférési jogkivonatokat, és nem férhetnek hozzá a korábban tárolt adatokhoz (például csevegési üzenetekhez).

Az Ön által használt identitások száma nem számítható fel. Ehelyett a primitívek használatára számítunk fel díjat. Az identitások számának nem kell korlátoznia az alkalmazás identitásának az Azure kommunikációs szolgáltatásbeli identitásokra való leképezését.

A feltérképezés szabadsága adatvédelmi felelősséggel jár. Ha a felhasználót törölni szeretné a rendszerből, akkor törölnie kell az adott felhasználóhoz társított összes identitást.

Az Azure kommunikációs szolgáltatások nem biztosítanak speciális identitást a névtelen felhasználók számára. Nem tartja meg a felhasználók és az identitások közötti leképezést, és nem tudja megállapítani, hogy az identitás névtelen-e. Megtervezheti az identitási koncepciót, hogy megfeleljen az igényeinek. Javasoljuk, hogy minden egyes alkalmazásnál hozzon létre egy új identitást minden egyes névtelen felhasználó számára.

Bárki, aki rendelkezik érvényes hozzáférési jogkivonattal, elérheti az aktuális identitás tartalmát. Például a felhasználók hozzáférhetnek az elküldött csevegési üzenetekhez. A hozzáférés csak a hozzáférési jogkivonat részét képező hatókörökre korlátozódik. További információ a jelen cikk [hozzáférési jogkivonatok](#access-tokens) című szakaszában található.

### <a name="identity-mapping"></a>Identitás-hozzárendelés

Az Azure kommunikációs szolgáltatás nem replikálja az Azure Identity Management rendszer funkcióit. Nem biztosít lehetőséget az ügyfelek számára az ügyfél-specifikus identitások használatára. Az ügyfelek például nem használhatnak telefonszámot vagy e-mail-címet. Ehelyett az Azure kommunikációs szolgáltatások egyedi azonosítókat biztosítanak. Ezeket az egyedi azonosítókat hozzárendelheti az alkalmazás identitásához. Az Azure kommunikációs szolgáltatások semmilyen olyan információt nem tárolnak, amely felfedi a felhasználók valódi identitását.

Ha el szeretné kerülni, hogy a rendszer hogyan duplikálja az adatokat, tervezze meg, hogyan képezhető le a felhasználók az identitás-tartományból az Azure kommunikációs szolgáltatások identitására. Bármilyen típusú mintát követheti. Használhatja például a 1:1, 1: N, N:1 vagy M:N. Döntse el, hogy egyetlen felhasználó egyetlen identitáshoz vagy több identitáshoz van-e rendelve.

Új identitás létrehozásakor a hozzárendelését az alkalmazás felhasználója vagy felhasználói számára tárolja. Mivel az identitásoknak hozzáférési jogkivonatokra van szükségük, hogy primitívek legyenek, az identitásnak ismertnek kell lennie az alkalmazás felhasználója vagy felhasználói számára.

Ha egy kapcsolódó adatbázist használ a felhasználói adatok tárolásához, a leképezési forgatókönyv alapján módosíthatja a kialakítást. Az 1:1-es vagy N:1-ot leképező forgatókönyvek esetében érdemes lehet `CommunicationServicesId` oszlopokat felvenni a táblába az Azure kommunikációs szolgáltatások identitásának tárolásához. Az 1: N vagy N:M kapcsolatot használó forgatókönyvekben érdemes lehet külön táblát létrehozni a relációs adatbázisban.

## <a name="access-tokens"></a>Hozzáférési jogkivonatok

A hozzáférési jogkivonat egy JSON Web Token (JWT), amely az Azure kommunikációs szolgáltatásbeli primitívek elérésére használható. A kibocsátott hozzáférési jogkivonat integritási védelemmel rendelkezik. Ez azt a jogcímeket nem lehet módosítani a kibocsátása után. Így a tulajdonságok, például az identitás, a lejárat vagy a hatókörök manuális módosítása érvényteleníti a hozzáférési jogkivonatot. Ha a primitívek inellenőrzött jogkivonatokkal vannak ellátva, akkor a hozzáférés megtagadva lesz a primitívek számára.

A hozzáférési token tulajdonságai a következők:
* Identitás.
* Lejárati.
* Hatókörök.

A hozzáférési token mindig 24 órán át érvényes. A lejárat után a hozzáférési jogkivonat érvénytelenítve lett, és nem használható a primitívek eléréséhez.

Az identitásnak új hozzáférési tokent kell kérnie egy kiszolgálóoldali szolgáltatástól. A *hatókör* -paraméter olyan primitívek nem üres készletét határozza meg, amelyek használhatók. Az Azure kommunikációs szolgáltatások a következő hatóköröket támogatják a hozzáférési tokenek esetében.

|Név|Leírás|
|---|---|
|Csevegés|  Lehetőséget biztosít csevegésben való részvételre|
|VoIP|  Identitások és telefonszámok meghívásának lehetősége|


A hozzáférési token lejárati ideje előtti visszavonásához használja az Azure kommunikációs szolgáltatások identitás-függvénytárát. A jogkivonat-visszavonás nem azonnali. A propagálás akár 15 percet is igénybe vehet. Egy identitás, erőforrás vagy előfizetés eltávolítása visszavonja az összes hozzáférési jogkivonatot.

Ha el szeretné távolítani egy felhasználó hozzáférését bizonyos funkciókhoz, vonja vissza az összes hozzáférési jogkivonatot. Ezután adjon ki egy új hozzáférési jogkivonatot, amely szűkebb hatókörrel rendelkezik.

Az Azure kommunikációs szolgáltatásokban a hozzáférési kulcsok rotációja visszavonja az összes olyan aktív hozzáférési jogkivonatot, amelyet egy korábbi hozzáférési kulcs használatával hoztak létre. Az összes identitás elveszíti az Azure kommunikációs szolgáltatásokhoz való hozzáférést, és új hozzáférési jogkivonatokat kell kiadnia.

Javasoljuk, hogy a hozzáférési jogkivonatokat a kiszolgálóoldali szolgáltatásban, nem pedig az ügyfél alkalmazásában adja meg. Ennek az az oka, hogy a kiállításhoz hozzáférési kulcs vagy felügyelt identitás szükséges. Biztonsági okokból a hozzáférési kulcsok megosztása az ügyfél alkalmazásával nem ajánlott.

Az ügyfélalkalmazás olyan megbízható szolgáltatási végpontot használjon, amely képes hitelesíteni az ügyfeleket. A végpontnak hozzáférési jogkivonatokat kell kiadnia a nevükben. További információ: [ügyfél-és kiszolgáló architektúrája](./client-and-server-architecture.md).

Ha a hozzáférési jogkivonatokat egy tárolóba gyorsítótárazza, javasoljuk, hogy használja a titkosítást. A hozzáférési token érzékeny adatok. Rosszindulatú tevékenységekhez használható, ha nem védett. A hozzáférési jogkivonattal rendelkező személy elindíthatja az SDK-t, és elérheti az API-t. Az elérhető API csak a hozzáférési jogkivonat hatóköre alapján korlátozott. Javasoljuk, hogy csak a szükséges hatókörökkel rendelkező hozzáférési jogkivonatokat bocsásson ki.

## <a name="next-steps"></a>Következő lépések

* A hozzáférési tokenek kezelésének bemutatása: [hozzáférési tokenek létrehozása és kezelése](../quickstarts/access-tokens.md).
* A hitelesítés bevezetését lásd: [hitelesítés az Azure kommunikációs szolgáltatásokban](./authentication.md).
* Az adattárolással és az adatvédelemmel kapcsolatos bevezetésért lásd: a [régió rendelkezésre állása és az adatok tartózkodási](./privacy.md)helye.