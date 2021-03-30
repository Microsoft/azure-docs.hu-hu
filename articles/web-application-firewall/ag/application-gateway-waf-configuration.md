---
title: Webalkalmazási tűzfalra vonatkozó kérelmek mérete és kizárási listája az Azure Application Gatewayban – Azure Portal
description: Ez a cikk a webalkalmazási tűzfalakra vonatkozó kérelmek méretére vonatkozó korlátozásokkal és a kizárási listával kapcsolatos információkat tartalmaz Application Gateway a Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93040262"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Webalkalmazási tűzfal kérelmek méretének korlátai és kizárási listája

Az Azure Application Gateway webalkalmazási tűzfal (WAF) védelmet biztosít a webalkalmazások számára. Ez a cikk a WAF kérelmek méretére és a kizárási listára vonatkozó konfigurációt ismerteti. Ezek a beállítások a Application Gatewayhoz társított WAF szabályzatban találhatók. Az WAF-szabályzatokkal kapcsolatos további tudnivalókért tekintse meg az Azure [webalkalmazási tűzfal az azure Application Gateway](ag-overview.md) és a [webalkalmazási tűzfal házirendjeinek létrehozása a Application Gateway](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF kizárási listája

![Kérelmek méretének korlátai](../media/application-gateway-waf-configuration/waf-policy.png)

A WAF kizárási listája lehetővé teszi bizonyos WAF kiértékelését. Gyakori példa Active Directory a hitelesítési vagy a jelszó mezőkhöz használt beszúrt tokenek. Ezek az attribútumok olyan speciális karaktereket tartalmaznak, amelyek a WAF-szabályokból hamis pozitív riasztást indíthatnak. Miután hozzáadta az attribútumot a WAF kizárási listájához, nem számít semmilyen konfigurált és aktív WAF szabály. A kizárási listák globálisak a hatókörben.

A következő attribútumok adhatók hozzá a kizárási listához név szerint. A kiválasztott mező értékeit a rendszer nem értékeli ki a WAF-szabályok alapján, de a nevük továbbra is fennáll (lásd az alábbi 1. példát, a User-Agent fejléc értéke ki van zárva a WAF kiértékelése alól). A kizárási listán el kell távolítani a mező értékének ellenőrzését.

* Kérések fejlécei
* Cookie-k kérése
* A kérelem attribútumának neve (ARG) kizárási elemként vehető fel, például:

   * Űrlapmező neve
   * JSON-entitás
   * URL-lekérdezési karakterlánc argumentumai

Megadhat egy pontos kérelem fejlécét, törzsét, cookie-t vagy lekérdezési karakterlánc-attribútumát.  Másik lehetőségként megadhatja a részleges egyezéseket is. A kizárási szabályok globálisak a hatókörben, és minden oldalra és minden szabályra érvényesek.

A támogatott egyeztetési feltételek operátorai a következők:

- **Egyenlő**: ez az operátor pontos egyezést használ. Például a **bearerToken** nevű fejléc kiválasztásához használja az Equals operátort a választó **bearerToken**.
- A következővel **kezdődik**: ez az operátor megegyezik a megadott választó értékkel kezdődő összes mezővel.
- **Végződik**: ez az operátor a megadott választó értékkel végződő összes kérelem mezőre illeszkedik.
- **Tartalmazza**: ez az operátor megfelel az összes olyan kérelem mezőnek, amely tartalmazza a megadott választó értéket.
- **Egyenlő**: ez az operátor megfelel az összes kérelem mezőnek. * a választók értéke lesz.

Minden esetben a nem megkülönbözteti a kis-és nagybetűket, és a reguláris kifejezés nem engedélyezett választóként.

> [!NOTE]
> További információ és hibaelhárítási segítség: [WAF hibaelhárítás](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Példák

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az alábbi példák a kizárások használatát mutatják be.

#### <a name="example-1"></a>1\. példa

Ebben a példában a felhasználói ügynök fejlécét szeretné kizárni. A felhasználói ügynök kérelmének fejléce egy jellemző karakterláncot tartalmaz, amely lehetővé teszi a hálózati protokoll társai számára, hogy azonosítsák a kérelmező szoftver felhasználói ügynökének alkalmazás típusát, operációs rendszerét, szoftveres gyártóját vagy szoftveres verzióját. További információ: [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

A fejléc kiértékelésének letiltása tetszőleges számú oka lehet. Lehetséges, hogy a WAF egy karakterláncot lát, és feltételezi, hogy rosszindulatú. Például a "x = x" klasszikus SQL-támadás egy karakterláncban. Bizonyos esetekben ez legitim forgalom lehet. Ezért előfordulhat, hogy ki kell zárnia ezt a fejlécet a WAF kiértékelése alól.

A következő Azure PowerShell parancsmag kizárja a User-Agent fejlécet a kiértékelésből:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>2\. példa

Ez a példa az URL-cím használatával kizárja a kérelemben átadott *felhasználói* paraméter értékét. Tegyük fel például, hogy az Ön környezetében gyakran előfordul, hogy a felhasználói mező olyan karakterláncot tartalmaz, amelyet a WAF kártékony tartalomként tekint meg, ezért blokkolja azt.  Ebben az esetben kizárhatja a felhasználói paramétereket, így a WAF nem értékel semmit a mezőben.

A következő Azure PowerShell parancsmag kizárja a felhasználói paramétert a kiértékelésből:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Így ha az URL-címet átadja `http://www.contoso.com/?user%281%29=fdafdasfda` a WAF, nem értékeli ki a **fdafdasfda** karakterláncot, de továbbra is kiértékeli a **(z) %281 %29** nevű paramétert. 

## <a name="waf-request-size-limits"></a>WAF kérelmek méretének korlátai



A webalkalmazási tűzfal lehetővé teszi, hogy az alsó és felső határokon belül konfigurálja a kérelmek méretére vonatkozó korlátozásokat. A következő két méretkorlát-konfiguráció érhető el:

- A kérelem törzsének maximális mérete mezője kilobájtban van megadva, és a kérelmek teljes méretére vonatkozó korlátozásokat a fájlok feltöltése nélkül szabályozza. Ebben a mezőben a minimális érték 1 KB, a maximális érték 128 KB. Az alapértelmezett érték a kérelem törzsének mérete 128 KB.
- A fájlfeltöltés korlátozása mező a MB-ban van megadva, és a maximálisan megengedett fájlfeltöltés-méretet szabályozza. Ennek a mezőnek legalább 1 MB-nyi értéke lehet, a következő maximális értékekkel:

   - 100 MB v1 közepes WAF-átjárók esetén
   - 500 MB a v1 nagyméretű WAF-átjárók esetében
   - 750 MB a v2 WAF-átjárók számára 

 A fájl feltöltési korlátjának alapértelmezett értéke 100 MB.

A WAF egy konfigurálható gombot is kínál a kérelem törzsének be-és kikapcsolásához. Alapértelmezés szerint a kérelem törzsének ellenőrzése engedélyezve van. Ha a kérelem törzsének ellenőrzése ki van kapcsolva, a WAF nem értékeli ki a HTTP-üzenet törzsének tartalmát. Ilyen esetekben a WAF folytatja a WAF-szabályok betartatását a fejléceken, a cookie-kon és az URI-n. Ha a kérelem törzsének ellenőrzése ki van kapcsolva, a kérelem törzsének maximális mérete mezője nem alkalmazható, és nem állítható be. A kérelem törzsének kikapcsolásával a 128 KB-nál nagyobb üzenetek küldhetők a WAF, de az üzenettörzs nem kerül ellenőrzésre a biztonsági rések esetében.

## <a name="next-steps"></a>Következő lépések

A WAF beállításainak konfigurálása után megtudhatja, hogyan tekintheti meg a WAF-naplókat. További információ: [Application Gateway diagnosztika](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
