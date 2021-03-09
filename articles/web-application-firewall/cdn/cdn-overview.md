---
title: Mi az Azure webalkalmazási tűzfal Azure CDN?
description: Ismerje meg, hogy az Azure webalkalmazás-tűzfal Hogyan védi a Azure CDN-szolgáltatást a webalkalmazások rosszindulatú támadások elleni védelmében.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: cd1f21f150094094a6bf856b04a64a02963f35a8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521713"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network-from-microsoft"></a>Azure webalkalmazási tűzfal az Azure Content Delivery Network a Microsofttól

Az Azure webalkalmazási tűzfal (WAF) a Microsoft Azure Content Delivery Network (CDN) szolgáltatásában központosított védelmet biztosít a webes tartalmak számára. A WAF megvédi webszolgáltatásait a gyakori biztonsági rések és sebezhetőségek ellen. A szolgáltatás elérhetővé válik a felhasználók számára, és segít a megfelelőségi követelmények teljesítésében.

> [!IMPORTANT]
> A Microsoft Azure CDN WAF jelenleg nyilvános előzetes verzióban érhető el, és előzetes verziójú szolgáltatói szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A WAF on Azure CDN globális és központosított megoldás. Üzembe helyezése az Azure-beli hálózati peremhálózati telephelyeken a világon. A WAF leállítja a kártékony támadásokat a támadási források közelébe, mielőtt azok elérnék a forrást. A globális védelmet a teljesítmény feláldozása nélkül érheti el. 

A WAF szabályzat egyszerűen hivatkozik az előfizetésében található bármely CDN-végpontra. Az új szabályok percek alatt üzembe helyezhetők, így gyorsan reagálhat a veszélyforrások változására.

![Azure-beli webalkalmazási tűzfal](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF szabályzat és szabályok

Beállíthat egy WAF szabályzatot, és hozzárendelheti a szabályzatot egy vagy több védett CDN-végponthoz. A WAF szabályzatok a biztonsági szabályok két típusát alkotják:

- Egyéni szabályok, amelyeket hozhat létre.

- felügyelt szabálykészlet, amely az Azure által felügyelt előre konfigurált szabályok gyűjteménye.

Ha mindkettő létezik, az egyéni szabályok feldolgozása a felügyelt szabálykészlet szabályainak feldolgozása előtt történik meg. A szabályok egyeztetési feltételből, prioritásból és műveletből állnak. Támogatott tevékenységtípusok: *Engedélyezés*, *Letiltás*, *napló* és *átirányítás*. A felügyelt és az egyéni szabályok kombinálásával teljes mértékben testre szabott szabályzatot hozhat létre, amely megfelel az adott alkalmazás védelmi követelményeinek.

A szabályzaton belüli szabályok feldolgozása prioritási sorrendben történik. A prioritás egy egyedi szám, amely meghatározza a feldolgozandó szabályok sorrendjét. A kisebb számok magasabb prioritást jelentenek, és a szabályok kiértékelése nagyobb értékű szabályok előtt történik. A szabály egyeztetése után a szabályban definiált megfelelő művelet lesz alkalmazva a kérelemre. Az ilyen egyezés feldolgozását követően az alacsonyabb prioritású szabályok nem kerülnek feldolgozásra.

Azure CDN futtatott webalkalmazásnak egyszerre csak egy WAF szabályzata lehet társítva. A CDN-végpontok azonban WAF szabályzatok nélkül is rendelkezhetnek. Ha van WAF-szabályzat, a rendszer replikálja az összes peremhálózati helyszínre, hogy biztosítsa az egységes biztonsági házirendeket az egész világon.

## <a name="waf-modes"></a>WAF módok

A WAF házirend a következő két módban való futtatásra konfigurálható:

- *Észlelési mód*: Ha az észlelési módban fut, a WAF nem hajt végre más műveleteket, mint a figyelők, és naplózza a kérést és a MEGegyező WAF-SZABÁLYT a WAF-naplókhoz. Bekapcsolhatja a CDN naplózási diagnosztikát. A portál használata esetén lépjen a **diagnosztika** szakaszra.

- *Megelőzési mód*: megelőzési módban a WAF végrehajtja a megadott műveletet, ha egy kérelem megfelel egy szabálynak. Ha talál egyezést, az alacsonyabb prioritású további szabályok kiértékelése nem történik meg. A rendszer a WAF-naplókban is naplózza a megfeleltetett kérelmeket.

## <a name="waf-actions"></a>WAF műveletek

A következő műveletek egyikét választhatja, ha egy kérelem megfelel egy szabály feltételeinek:

- *Engedélyezés*: a kérelem áthalad a WAF, és a rendszer továbbítja a háttérnek. Az alacsonyabb prioritású szabályok nem tudják blokkolni ezt a kérést.
- *Letiltás*: a kérés le van tiltva, és a WAF választ küld az ügyfélnek, és nem továbbítja a kérést a háttér felé.
- *Napló*: a rendszer naplózza a kérést a WAF-naplókban, és a WAF folytatja az alacsonyabb prioritású szabályok kiértékelését.
- *Átirányítás*: a WAF átirányítja a kérést a megadott URI-ra. A megadott URI egy házirendi szintű beállítás. A konfigurálást követően a rendszer az *átirányítási* műveletnek megfelelő összes kérelmet elküldi az URI-nak.

## <a name="waf-rules"></a>WAF-szabályok

A WAF szabályzatok két típusú biztonsági szabályt tartalmazhatnak:

- *Egyéni szabályok*: saját maga által létrehozott szabályok 
- *felügyelt szabálykészlet*: az Azure által felügyelt előre konfigurált szabályok halmaza

### <a name="custom-rules"></a>Egyéni szabályok

Az egyéni szabályoknak meg kell egyezniük a szabályokkal és a díjszabás-vezérlési szabályokkal.

A következő egyéni egyezési szabályok konfigurálhatók:

- *IP-engedélyezési lista és tiltólista*: a webalkalmazásokhoz való hozzáférést az ügyfél IP-címei vagy az IP-címtartományok listája alapján szabályozhatja. Az IPv4-és IPv6-címek egyaránt támogatottak. A lista beállítható úgy, hogy blokkolja vagy engedélyezze ezeket a kéréseket, ahol a forrás IP-cím megegyezik a listában szereplő IP-címekkel.

- *Földrajzi alapú hozzáférés-vezérlés*: a webalkalmazásokhoz való hozzáférést az ügyfél IP-címéhez társított országkód alapján szabályozhatja.

- *Http-paraméterek-alapú hozzáférés-vezérlés*: a HTTP/HTTPS-kérések paraméterei alapján a karakterlánc-egyezésekre alapozhatja a szabályokat.  Például a lekérdezési karakterláncok, a POST argumentumok, a kérelem URI-ja, a kérelem fejléce és a kérelem törzse.

- *Kérelem metódus-alapú hozzáférés-vezérlése*: a kérelem http-kérelmi módszerére vonatkozó alapszabályok. Például: GET, PUT vagy HEAD.

- *Méret megkötése*: alapszabályokat állíthat be egy kérelem adott részeinek hosszára, például a lekérdezési sztringre, az URI-ra vagy a kérelem törzsére.

A díjszabás-vezérlési szabály az ügyfél IP-címeitől származó rendellenesen nagy forgalmat korlátozza.

- *Szabály-korlátozási szabályok*: beállíthat egy küszöbértéket az ügyfél IP-címéről engedélyezett webes kérelmek számának egyperces időtartama alatt. Ez a szabály különbözik egy olyan IP-lista alapú engedélyezési/letiltási szabálytól, amely engedélyezi vagy letiltja az összes kérést az ügyfél IP-címéről. A díjszabási korlátok kombinálhatók további egyeztetési feltételekkel, például a HTTP (S) paraméterekkel, amelyek a részletes díjszabás-vezérléshez egyeznek.

### <a name="azure-managed-rule-sets"></a>Azure által felügyelt szabálykészlet

Az Azure által felügyelt szabálykészlet egyszerű módszert kínál a védelem közös biztonsági fenyegetésekkel való üzembe helyezésére. Mivel ezeket a szabályrendszerek az Azure felügyeli, a szabályok az új támadási aláírások elleni védelemhez szükséges módon frissülnek. Az Azure által felügyelt alapértelmezett szabálykészlet a következő veszélyforrás-kategóriákra vonatkozó szabályokat tartalmazza:

- Webhelyek közötti, szkriptalapú támadás
- Java-támadások
- Helyi fájl felvétele
- PHP-injektálási támadások
- Távoli parancs végrehajtása
- Távolifájl-beszúrás
- Munkamenet-rögzítés
- SQL-injektálás elleni védelem
- Protokoll-támadók

Az alapértelmezett szabálykészlet verziószáma akkor növekszik, amikor új támadási aláírásokat adnak hozzá a szabálykészlet számára.
Az alapértelmezett szabálykészlet alapértelmezés szerint engedélyezve van az *észlelési* módban a WAF-házirendekben. Az alapértelmezett szabálykészlet egyes szabályait letilthatja vagy engedélyezheti az alkalmazásra vonatkozó követelmények teljesítéséhez. Szabályként beállíthat meghatározott műveleteket is (Engedélyezés/LETILTÁS/átirányítás/napló). A felügyelt alapértelmezett szabálykészlet alapértelmezett művelete a *Letiltás*.

Az egyéni szabályokat a rendszer mindig alkalmazza az alapértelmezett szabálykészlet szabályainak kiértékelése előtt. Ha egy kérelem megfelel egy egyéni szabálynak, a rendszer alkalmazza a vonatkozó szabály műveletét. A kérést a rendszer blokkolja vagy továbbítja a háttérnek. Más egyéni szabályok vagy az alapértelmezett szabálykészlet szabályai nem lesznek feldolgozva. Eltávolíthatja a WAF szabályzatok alapértelmezett szabályát is.

## <a name="configuration"></a>Konfiguráció

Az összes WAF konfigurálhatja és telepítheti a Azure Portal, a REST API-k, a Azure Resource Manager sablonok és a Azure PowerShell használatával.

## <a name="monitoring"></a>Figyelés

A CDN-vel való WAF figyelése integrálva van Azure Monitor a riasztások nyomon követésére és a forgalmi trendek egyszerű figyelésére.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: WAF szabályzat létrehozása Azure CDN a Azure Portal használatával](waf-cdn-create-portal.md)
