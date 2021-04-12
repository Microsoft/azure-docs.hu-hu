---
title: Azure Firewall Premium előzetes verziójának funkciói
description: Az Azure Firewall Premium egy felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure Virtual Network-erőforrásait.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: fdf0113d73b0b2e82ec889caf2a82c77bb7a040c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226701"
---
# <a name="azure-firewall-premium-preview-features"></a>Azure Firewall Premium előzetes verziójának funkciói

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA-tanúsítvány emblémája" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI minősítési emblémája" border="false":::


> [!IMPORTANT]
> A Azure Firewall Premium szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 A Azure Firewall Premium Preview egy új generációs tűzfal, amely a szigorúan bizalmas és szabályozott környezetekhez szükséges képességekkel rendelkezik.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure Firewall Premium – áttekintés diagram":::

A Azure Firewall Premium Preview egy olyan globális erőforrást használ, amely a tűzfalak központi felügyeletére használható a Azure Firewall Manager használatával. Ennek a kiadásnak a megkezdése után az összes új funkció csak a tűzfal házirendjének használatával konfigurálható. A tűzfalszabályok (klasszikus) továbbra is támogatottak, és használhatók a meglévő szabványos tűzfal-funkciók konfigurálására.  A tűzfalszabályok egymástól függetlenül, vagy a Azure Firewall Managerrel is kezelhetők. Az egyetlen tűzfallal társított tűzfalszabályok díjmentesek.

> [!IMPORTANT]
> A biztonságos központi telepítések és a kényszerített bújtatási konfigurációk jelenleg nem támogatják a Firewall Premium SKU-t. 

A Premium Preview Azure Firewall a következő funkciókat tartalmazza:

- **TLS-vizsgálat** – visszafejti a kimenő forgalmat, feldolgozza az adatokat, majd titkosítja az adatokat, és elküldi azt a célhelyre.
- **IDP** – a hálózati behatolás-észlelési és-megelőzési rendszer (IDP) lehetővé teszi a kártékony tevékenységek hálózati tevékenységének figyelését, a tevékenység adatainak naplózását, a jelentés megadását, és opcionálisan megkísérli a blokkolását.
- **URL-szűrés** – kiterjeszti Azure Firewall FQDN-szűrési képességét egy teljes URL-cím megfontolni. Például a `www.contoso.com/a/c` helyett `www.contoso.com` .
- **Webes kategóriák** – a rendszergazdák engedélyezhetik vagy megtagadhatják a webhely-kategóriákhoz való hozzáférést, például a szerencsejáték-webhelyeket, a közösségi média webhelyeket és egyebeket.


## <a name="tls-inspection"></a>TLS-ellenőrzés

A Azure Firewall Premium megszakítja a kimenő és a kelet-nyugati TLS-kapcsolatokat. A bejövő TLS-vizsgálat az [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) támogatja a végpontok közötti titkosítást. Azure Firewall a szükséges értékkel bővített biztonsági funkciókat, majd újratitkosítja az eredeti célhelyre eljuttatott forgalmat.

> [!TIP]
> A TLS 1,0 és a 1,1 elavult, és nem támogatott. A TLS 1,0 és a TLS/SSL (SSL) 1,1-es verziója sebezhetőnek bizonyult, és noha jelenleg is működik a visszamenőleges kompatibilitás lehetővé tételéhez, nem ajánlottak. A lehető leghamarabb telepítse át a TLS 1,2-es verzióra.

Ha többet szeretne megtudni az Azure Firewall Premium Preview köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra vonatkozó követelményekről, tekintse meg a [prémium szintű előzetes verziójú tanúsítványokat Azure Firewall](premium-certificates.md).

## <a name="idps"></a>IDP

A hálózati behatolás-észlelési és-megelőzési rendszerek (IDP-EK) lehetővé teszik a hálózat figyelését a kártékony tevékenységekhez, a tevékenység adatainak naplózását, jelentését, és opcionálisan megkísérli a blokkolását. 

A Azure Firewall Premium Preview az aláírás-alapú IDP lehetővé teszi a támadások gyors észlelését azáltal, hogy konkrét mintákat keres, például a hálózati forgalomban lévő bájtok sorozatait vagy a kártevők által használt ismert kártékony utasítások sorozatait. A IDP aláírása teljes mértékben felügyelt és folyamatosan frissül.

A Azure Firewall aláírások/szabályrendszerek a következők:
- Hangsúlyt fektetve a tényleges kártevők, a parancsok és szabályozások, a készletek kiaknázása, valamint a hagyományos megelőzési módszerek által kimaradt vad kártékony tevékenységek ujjlenyomatára.
- Több mint 35 000 szabály van több mint 50 kategóriában.
    - A kategóriák közé tartoznak a kártevők parancs és vezérlés, a DoS-támadások, a botnetek, a tájékoztató események, a biztonsági rések, a SCADA hálózati protokollok, a Kit-tevékenységek kiaknázása és egyebek.
- minden nap 20 – 40 + új szabályt szabadítanak fel.
- Alacsony hamis pozitív minősítés a State-of-the-art malware sandbox és a Global szenzor Network feedback loop használatával.

A IDP lehetővé teszi a nem titkosított forgalomhoz tartozó összes port és protokoll támadásának észlelését. Ha azonban a HTTPS-forgalmat meg kell vizsgálni, a Azure Firewall a TLS-ellenőrzési funkció használatával visszafejtheti a forgalmat, és hatékonyabban észlelheti a kártékony tevékenységeket.  

A IDP megkerülése lista lehetővé teszi, hogy a megkerülési listán megadott IP-címekre, tartományokra és alhálózatokra ne szűrje a forgalmat. 

## <a name="url-filtering"></a>URL-szűrés

Az URL-szűrés kibővíti a Azure Firewall FQDN-szűrési képességét egy teljes URL-cím megfontolni. Például a `www.contoso.com/a/c` helyett `www.contoso.com` .  

Az URL-szűrés a HTTP-és a HTTPS-forgalomon is alkalmazható. A HTTPS-forgalom vizsgálata során Azure Firewall Premium Preview a TLS-ellenőrzési funkcióval visszafejtheti a forgalmat, és kinyerheti a célként megadott URL-címet annak ellenőrzéséhez, hogy a hozzáférés engedélyezett-e. A TLS-ellenőrzéshez az alkalmazás szabályának szintjén kell választania. Ha engedélyezve van, használhatja az URL-címeket a HTTPS-szűréshez. 

## <a name="web-categories"></a>Webes kategóriák

A webes kategóriák lehetővé teszik a rendszergazdák számára, hogy engedélyezik vagy megtagadják a webhelyhez való hozzáférést, például a szerencsejáték-webhelyeket, a közösségi média webhelyeket A webes kategóriák a Azure Firewall standard részét képezik, de a Azure Firewall Premium Preview-ban részletesebben is elérhetővé válik. A standard SKU-ban található, a teljes tartománynév alapján a kategóriának megfelelő webes kategóriák lehetőséggel ellentétben a prémium SKU a HTTP-és a HTTPS-forgalom teljes URL-címének megfelelően a kategóriának felel meg. 

Ha például Azure Firewall elfogja a HTTPS `www.google.com/news` -kérelmet, a következő kategorizálás várható: 

- Firewall standard – a rendszer csak a teljes tartománynevet fogja megvizsgálni, ezért a `www.google.com` *keresőmotorként* lesz kategorizálva. 

- Tűzfal Premium – a teljes URL-cím meg lesz vizsgálva, ezért `www.google.com/news` *hírekként* lesz kategorizálva.

A kategóriákat a **felelősség**, a **nagy sávszélesség**, az **üzleti használat**, a **termelékenység elvesztése**, az **általános szörfözés** és a **Kategorizálatlan** kategóriák alapján rendezi a rendszer.

### <a name="category-exceptions"></a>Kategória kivételei

Kivételeket hozhat létre a webes kategória szabályaihoz. Hozzon létre egy külön engedélyezési vagy megtagadási szabályt a szabály gyűjtési csoportjában magasabb prioritással. Beállíthat például egy olyan szabálygyűjtemény-gyűjteményt `www.linkedin.com` , amely az 100-es prioritással rendelkezik, és olyan szabálygyűjtemény használatát teszi lehetővé, amely megtagadja a **közösségi hálózatkezelést** a 200-es prioritással. Ez létrehozza a kivételt az előre definiált **közösségi hálózat** webes kategóriához.

### <a name="categorization-change"></a>Kategorizálási változás

A kategorizálási változást a következő lehetőségekkel kérheti le:

 - úgy gondolja, hogy egy teljes tartománynevet vagy URL-címet más kategóriába kell tartoznia 
 
vagy 

- egy Kategorizálatlan FQDN vagy URL-cím javasolt kategóriája

Szívesen küld egy kérelmet a következő címen: [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Támogatott régiók

A Premium Preview Azure Firewall a következő régiókban támogatott:

- Ausztrália középső régiója (nyilvános/ausztráliai)
- Közép-Ausztrália 2 (nyilvános/ausztráliai)
- Kelet-Ausztrália (nyilvános/ausztráliai)
- Délkelet-Ausztrália (állami/ausztráliai)
- Dél-Brazília (nyilvános/Brazília)
- Közép-Kanada (nyilvános/Kanada)
- Kelet-Kanada (nyilvános/Kanada)
- USA középső régiója (nyilvános/Egyesült Államok)
- USA középső – EUAP (Public/Canary) (USA)
- Kelet-Ázsia (nyilvános/Ázsia és a Csendes-óceáni térség)
- USA keleti régiója (nyilvános/Egyesült Államok)
- USA 2. keleti régiója (nyilvános/Egyesült Államok)
- Közép-Franciaország (nyilvános/Franciaország)
- Dél-Franciaország (nyilvános/Franciaország)
- Kelet-Japán (nyilvános/Japán)
- Nyugat-Japán (nyilvános/Japán)
- Korea középső régiója (Public/Korea)
- Dél-Korea (Public/Korea)
- USA északi középső régiója (nyilvános/Egyesült Államok)
- Észak-Európa (nyilvános/európai)
- Dél-Afrika északi régiója (nyilvános/Dél-Afrika)
- USA déli középső régiója (nyilvános/Egyesült Államok)
- Délkelet-Ázsia (nyilvános/Ázsia és a Csendes-óceáni térség)
- UAE Central (Public/UAE)
- Egyesült Királyság déli régiója (Public/Egyesült Királyság)
- Egyesült Királyság nyugati régiója (Public/Egyesült Királyság)
- USA nyugati középső régiója (nyilvános/Egyesült Államok)
- Nyugat-Európa (állami/európai)
- Nyugat-India (nyilvános/indiai)
- USA nyugati régiója (nyilvános/Egyesült Államok)
- USA 2. nyugati régiója (nyilvános/Egyesült Államok)


## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall Premium Preview a következő ismert problémákkal rendelkezik:

|Probléma  |Description  |Kockázatcsökkentés  |
|---------|---------|---------|
|A TLS-ellenőrzés csak a HTTPS standard porton támogatott|A TLS-ellenőrzés csak a HTTPS/443 protokollt támogatja. |Nincsenek. A GA-ban más portok is támogatottak lesznek.|
|ESNI-támogatás a teljes tartománynév-feloldáshoz HTTPS-ben|A titkosított SNI HTTPS-kézfogás esetén nem támogatott.|Jelenleg csak a Firefox támogatja a ESNI egyéni konfiguráción keresztül. Javasolt Áthidaló megoldás a funkció letiltása.|
|Ügyféltanúsítványok (TLS)|Az ügyféltanúsítványok kölcsönös identitási megbízhatóság létrehozására szolgálnak az ügyfél és a kiszolgáló között. Az ügyféltanúsítványok a TLS-egyeztetés során használatosak. Az Azure Firewall újraegyeztet egy kapcsolatot a kiszolgálóval, és nem fér hozzá az Ügyféltanúsítványok titkos kulcsához.|Nincsenek|
|QUIC/HTTP3|A QUIC a HTTP új fő verziója. Ez egy UDP-alapú protokoll 80 (csomag) és 443 (SSL). A teljes tartománynév/URL/TLS-ellenőrzés nem támogatott.|Az UDP 80/443 hálózati szabályokként való továbbításának beállítása.|
Nem megbízható ügyfél által aláírt tanúsítványok|Az ügyfél által aláírt tanúsítványokat a tűzfal nem bízik meg, ha az intranetes webkiszolgálóról érkezett.|Javítás ütemezése a GA-ban.
|Nem megfelelő forrás-és cél IP-címek a TLS-ellenőrzéssel végzett IDP riasztásokban.|Ha engedélyezi a TLS-ellenőrzés és a IDP új riasztást, a megjelenített forrás/cél IP-cím helytelen (a belső IP-cím az eredeti IP-cím helyett jelenik meg).|Javítás ütemezése a GA-ban.|
|Nem megfelelő forrás IP-cím a riasztásokban a HTTP-IDP (TLS-ellenőrzés nélkül).|Ha az egyszerű szöveges HTTP-forgalom használatban van, és a IDP új riasztást ad ki, és a cél nyilvános IP-cím, a megjelenített forrás IP-címe helytelen (a belső IP-cím az eredeti IP-cím helyett jelenik meg).|Javítás ütemezése a GA-ban.|
|Tanúsítvány propagálása|A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványnak a tűzfalon való alkalmazása után a tanúsítvány érvénybe léptetéséhez 5-10 percet is igénybe vehet.|Javítás ütemezése a GA-ban.|
|IDP megkerülése|A IDP megkerülése nem működik a TLS-leállított forgalom esetében, és a forrás IP-cím és a forrás IP-csoportok nem támogatottak.|Javítás ütemezése a GA-ban.|
|TLS 1,3-támogatás|A TLS 1,3 részlegesen támogatott. Az ügyfél és a tűzfal közötti TLS-alagút a TLS 1,2-alapú, a tűzfaltól a külső webkiszolgálóig a TLS 1,3-alapú.|A frissítések vizsgálata folyamatban van.|
|Kulcstartó magánhálózati végpontja|A kulcstartó támogatja a privát végpontok hozzáférését a hálózati kitettség korlátozására. A megbízható Azure-szolgáltatások figyelmen kívül hagyhatják ezt a korlátozást, ha a kulcstartó [dokumentációjában](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)leírtak szerint egy kivétel van konfigurálva. Azure Firewall jelenleg nem megbízható szolgáltatásként szerepel, és nem fér hozzá a Key Vaulthoz.|Javítás ütemezése a GA-ban.|


## <a name="next-steps"></a>Következő lépések

- [A prémium szintű tanúsítványok Azure Firewall ismertetése](premium-certificates.md)
- [A Premium Preview Azure Firewall üzembe helyezése és konfigurálása](premium-deploy.md)
- [Migrálás a Azure Firewall Premium előzetes verziójára](premium-migrate.md)
