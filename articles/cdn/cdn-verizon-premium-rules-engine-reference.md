---
title: A Azure CDN Rules Engine referenciája | Microsoft Docs
description: A Azure CDN szabályok motorjának a feltételekkel és szolgáltatásokkal kapcsolatos dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362580"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN a Verizon Premium Rules motor referenciája

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)elérhető egyeztetési feltételeinek és funkcióinak részletes leírását tartalmazza.

A szabályok motor úgy lett kialakítva, hogy az adott típusú kérelmeknek a CDN általi feldolgozásának végső hatósága legyen.

**Gyakori felhasználások**:

- Egyéni gyorsítótár-szabályzat felülbírálása vagy definiálása.
- Bizalmas tartalomra vonatkozó kérések biztonságos vagy megtagadása.
- Átirányítási kérelmek.
- Egyéni naplófájlok tárolása.
## <a name="key-concepts"></a>Fő fogalmak
A szabályok motorjának beállításával kapcsolatos alapfogalmak alább olvashatók.
### <a name="draft"></a>Draft
A szabályzatok vázlata egy vagy több olyan szabályból áll, amely a kérelmek azonosítását és a rájuk vonatkozó műveletek készletét jelenti. A draft egy olyan folyamatban lévő folyamat, amely a gyakori konfigurációs frissítéseket a hely forgalmának befolyásolása nélkül teszi lehetővé. Miután a vázlat készen áll a véglegesítésre, át kell alakítani egy írásvédett szabályzatba.

### <a name="rule"></a>Szabály
Egy szabály egy vagy több típusú kérést azonosít, valamint azokat a műveleteket, amelyek rájuk lesznek alkalmazva.

A következőkből áll: 

- Feltételes kifejezések halmaza, amely meghatározza a kérelmek azonosítására szolgáló logikát.
- Egyeztetési feltételek halmaza, amely meghatározza a kérelmek azonosításához használt feltételeket.
- Olyan funkciók összessége, amelyek meghatározzák, hogy a CDN hogyan fogja kezelni a fenti kérelmeket.
Ezeket az elemeket a következő ábra azonosítja.

![A címkékkel ellátott képernyőképen látható egy szabály feltételes kifejezése, a egyezése és a szolgáltatás funkciói.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Szabályzat
A csak olvasható szabályokból álló szabályzatok az alábbiakat biztosítják:

- A szabályok több változatának létrehozása, tárolása és kezelése.
- Visszalépés egy korábban központilag telepített verzióra.
- Az eseményvezérelt szabályok előre való előkészítése (például egy olyan szabály, amely egy ügyfél-forrás karbantartás miatt átirányítja a forgalmat.)

> [!NOTE]
> Bár a környezetek csak egyetlen házirendet tartalmazhatnak, a házirendek igény szerint üzembe helyezhetők.

### <a name="deploy-request"></a>Kérelem üzembe helyezése
Az üzembe helyezési kérelem egyszerű és egyszerűsített eljárást biztosít, amellyel a szabályzat gyorsan alkalmazható az átmeneti vagy az éles környezetben. Az üzembe helyezési kérelmek előzményei megtalálhatók az adott környezetekben alkalmazott módosítások nyomon követése érdekében.

> [!NOTE]
> A manuális ellenőrzés és jóváhagyás megkövetelése csak azokra a kérésekre, amelyek nem adják át az automatikus érvényesítési és a hibakeresési rendszerét.

### <a name="rule-precedence"></a>Szabály prioritása
A házirendben szereplő szabályokat általában a listában szereplő sorrendben dolgozzák fel (azaz felülről lefelé). Ha a kérés megegyezik az ütköző szabályokkal, akkor a feldolgozandó utolsó szabály elsőbbséget élvez.

### <a name="policy-deployment-workflow"></a>Házirend-telepítési munkafolyamat
Az alábbi ábrán látható, hogy milyen munkafolyamatot alkalmazhat a rendszer az éles vagy átmeneti környezetre.

![Házirend-telepítési munkafolyamat](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Lépés |Description |
|---------|---------|
|[Piszkozat létrehozása](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    A Piszkozatok olyan szabályokból állnak, amelyek meghatározzák, hogy a CDN hogyan kezelje a tartalomra vonatkozó kéréseket.     |
|Piszkozat zárolása   |     A Piszkozat véglegesítése után zárolni kell és át kell alakítani egy írásvédett szabályzatot.    |
|[Üzembe helyezési kérelem beküldése](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Az üzembe helyezési kérelem lehetővé teszi, hogy a szabályzatot a tesztelési vagy a termelési forgalomra alkalmazza.</br> <br>Küldje el a telepítési kérelmet az átmeneti vagy az éles környezetbe.</br>     |
|Kérelmek felülvizsgálatának üzembe helyezése   |    <br>Egy üzembe helyezési kérelem automatikusan ellenőrzi és észleli a hibát.</br><br>Bár az üzembe helyezési kérelmek többsége automatikusan jóvá van hagyva, az összetettebb házirendek esetében manuális felülvizsgálatra van szükség.</br>   |
|Házirend üzembe helyezése ([előkészítés](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Az átmeneti környezetbe való központi telepítési kérelem jóváhagyása után a rendszer egy házirendet alkalmaz az átmeneti környezetre. Ez a környezet lehetővé teszi a szabályzatok tesztelését az ál-hely forgalmán.</br><br>Ha a házirend készen áll az élő hely forgalmára, egy új üzembe helyezési kérelmet kell elküldenie az éles környezetben.</br>      |
|Házirend-telepítés ([éles környezet](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Az éles környezetbe történő központi telepítésre vonatkozó kérés jóváhagyása után a rendszer egy szabályzatot alkalmaz az éles környezetre. Ez a környezet lehetővé teszi, hogy a szabályzat végső hatóságként működjön, hogy meghatározza, hogyan kezelje a CDN az élő forgalmat.     |
## <a name="syntax"></a>Syntax

A speciális karakterek kezelésének módja attól függően változik, hogy a egyezési feltétel vagy szolgáltatás milyen módon kezeli a szöveges értékeket. Az egyeztetési feltétel vagy szolgáltatás a következő módokon értelmezheti a szöveget:

- [**Literális értékek**](#literal-values)
- [**Helyettesítő karakteres értékek**](#wildcard-values)
- [**Reguláris kifejezések**](#regular-expressions)

### <a name="literal-values"></a>Literális értékek

A literál értékként értelmezett szöveg az összes speciális karaktert kezeli, a (z)% Symbol kivételével, az értéknek megfelelő részeként. Más szóval a konstans egyezési feltétel `\'*'\` csak akkor teljesül, ha a pontos érték (azaz `\'*'\` ) megtalálható.

A százalékos szimbólum az URL-kódolás (például:) jelölésére szolgál `%20` .

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg a speciális karakterek további jelentését rendeli hozzá. A következő táblázat a következő karakterkészletek értelmezését ismerteti:

Karakter | Leírás
----------|------------
\ | A program fordított perjelet használ a táblázatban megadott karakterek bármelyikének megmeneküléséhez. Meg kell adni egy fordított perjelet közvetlenül a kikerülő különleges karakter előtt.<br/>Az alábbi szintaxis például megmenekül egy csillaggal: `\*`
% | A százalékos szimbólum az URL-kódolás (például:) jelölésére szolgál `%20` .
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
Space (Szóköz) | A szóköz karakter azt jelzi, hogy egy egyezési feltétel teljesül a megadott értékek vagy minták valamelyikével.
érték | Egyetlen árajánlat nem rendelkezik speciális jelentéssel. Az aposztrófok készlete azonban azt jelzi, hogy egy értéket literál értékként kell kezelni. A szolgáltatás a következő módokon használható:<br><br/>– Lehetővé teszi az egyeztetési feltétel teljesülését, ha a megadott érték megegyezik az összehasonlítási érték bármely részével.  Például a `'ma'` következő sztringek bármelyike megfelel: <br/><br/>/Business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template. **ma** p<br /><br />– Lehetővé teszi, hogy egy speciális karakter literál karakterként legyen megadva. Megadhat például egy literál szóköz karaktert úgy, hogy az idézőjelek közé helyezi a szóköz karaktert (azaz `' '` vagy `'sample value'` ).<br/>– Lehetővé teszi, hogy a rendszer üres értéket ad meg. Adja meg az üres értéket egy idézőjelek készletének megadásával (azaz: "").<br /><br/>**Fontos:**<br/>– Ha a megadott érték nem tartalmaz helyettesítő karaktert, a rendszer automatikusan literál értékként veszi figyelembe, ami azt jelenti, hogy nem szükséges egyetlen idézőjelet megadni.<br/>– Ha egy fordított perjel nem kerül egy másik karakterbe a táblázatban, figyelmen kívül hagyja a rendszer, ha egy idézőjelek készletében van megadva.<br/>– Egy speciális karakter literál karakterként való megadásának másik módja, ha egy fordított perjelet (azaz) használ `\` .

### <a name="regular-expressions"></a>Reguláris kifejezések

A reguláris kifejezések határozzák meg a szöveges értéken belül keresendő mintát. A reguláris kifejezések jelölése meghatározott kifejezéseket definiál a különböző szimbólumokhoz. A következő táblázat azt mutatja be, hogyan kezeli a speciális karaktereket a reguláris kifejezéseket támogató feltételek és funkciók egyeztetésével.

Speciális karakter | Description
------------------|------------
\ | Egy fordított perjel elmenekül a következő karakterrel, ami azt eredményezi, hogy a karaktert literál értékként kell kezelni, ahelyett, hogy a reguláris kifejezés jelentését kellene használnia. Az alábbi szintaxis például megmenekül egy csillaggal: `\*`
% | A százalékos szimbólum jelentése a használattól függ.<br/><br/> `%{HTTPVariable}`: Ez a szintaxis egy HTTP-változót azonosít.<br/>`%{HTTPVariable%Pattern}`: Ez a szintaxis egy százalékos szimbólummal azonosítja a HTTP-változót és elválasztóként.<br />`\%`: A százalékos szimbólum megmenekülése lehetővé teszi, hogy literál értékként, vagy URL-kódolást jelezzen (például: `\%20` ).
\* | A csillag lehetővé teszi, hogy az előző karakter nulla vagy több alkalommal legyen összepárosítva.
Space (Szóköz) | A szóköz karaktert általában literál karakterként kezeli a rendszer.
érték | Az aposztrófok literál karakterekként lesznek kezelve. Az idézőjelek halmaza nem rendelkezik speciális jelentéssel.

A reguláris kifejezéseket támogató feltételek és funkciók egyeztetése a Perl-kompatibilis reguláris kifejezésekkel (PCRE) meghatározott mintákat fogad el.



## <a name="next-steps"></a>Következő lépések

- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
