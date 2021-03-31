---
title: Több webhely üzemeltetése az Azure Application Gateway
description: Ez a cikk áttekintést nyújt az Azure Application Gateway többhelyes támogatásáról.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87474325"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway – több hely üzemeltetése

Több hely üzemeltetése lehetővé teszi több webalkalmazás konfigurálását az Application Gateway ugyanazon portjára. Így hatékonyabb topológiát konfigurálhat telepítéseihez, mivel akár 100-nál is több webhelyet adhat hozzá egyetlen alkalmazásátjáróhoz. Mindegyik webhelyet a saját háttérkészletéhez lehet irányítani. Például három tartomány (contoso.com, fabrikam.com és adatum.com) mutat az alkalmazásátjáró IP címére. Létrehozhat három többhelyes figyelőt, és konfigurálhatja az egyes figyelők esetében a megfelelő port- és protokollbeállítást. 

A helyettesítő karakterrel ellátott gazdaneveket többhelyes figyelőben és figyelőként legfeljebb 5 gazdanévben is meghatározhatja. További információ: [helyettesítő karakterek nevei a figyelőben](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Többhelyes Application Gateway":::

> [!IMPORTANT]
> A szabályok feldolgozása a v1 SKU-ban található portálon megjelenő sorrendben történik. A v2 SKU esetében a pontos egyezések magasabb prioritással rendelkeznek. Alapszintű figyelő konfigurálása előtt határozottan ajánlott többhelyes figyelőket konfigurálni.  Ez biztosítja, hogy a forgalom a megfelelő háttérbe legyen irányítva. Ha előbb egy alapszintű figyelő szerepel a listában, és az megfelel egy bejövő kérésnek, a figyelő feldolgozza azt.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen ugyanazon szülőtartomány több altartományát is üzemeltetheti ugyanazon az Application Gateway-példányon. Például `http://blog.contoso.com` `http://app.contoso.com` egyetlen Application Gateway-telepítésben is üzemeltetheti a szolgáltatást.

## <a name="wildcard-host-names-in-listener-preview"></a>Helyettesítő karakterek nevei a figyelőben (előzetes verzió)

Application Gateway lehetővé teszi a gazdagép-alapú útválasztást a többhelyes HTTP (S) figyelő használatával. Mostantól használhat helyettesítő karaktereket, például a csillag (*) és a kérdőjel (?) karaktert a gazdagép nevében, és legfeljebb 5 állomásnévvel többhelyes HTTP (S) figyelővel. Például: `*.contoso.com`.

Ha helyettesítő karaktert használ az állomásnévben, több állomásnév is megegyező egyetlen figyelőben. Megadhatja például a következőt: `*.contoso.com` `ecom.contoso.com` , `b2b.contoso.com` `customer1.b2b.contoso.com` és így tovább. Az állomásnevek tömbjét használva több állomásnevet is beállíthat egy figyelőhöz, hogy átirányítsa a kéréseket egy háttérbeli készletbe. Egy figyelő például tartalmazhat, `contoso.com, fabrikam.com` amely fogadja a kérelmeket az állomásnevek esetében is.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Helyettesítő karakteres figyelő":::

>[!NOTE]
> Ez a funkció előzetes verzióban érhető el, és csak a Application Gateway Standard_v2 és WAF_v2 SKU-ban érhető el. Az előzetes verziókkal kapcsolatos további tudnivalókért tekintse meg [a használati feltételeket itt](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>Ez a funkció jelenleg csak [Azure PowerShell](tutorial-multiple-sites-powershell.md) és az [Azure CLI](tutorial-multiple-sites-cli.md)-n keresztül érhető el. A portál támogatása hamarosan elérhetővé válik.
> Vegye figyelembe, hogy mivel a portál támogatása nem teljes mértékben elérhető, ha csak a HostNames paramétert használja, a figyelő alapszintű figyelőként fog megjelenni a portálon, és a figyelő listanézet oszlopa nem jeleníti meg a konfigurált állomásnevek nevét. A helyettesítő karakteres figyelő bármilyen módosítása esetén győződjön meg arról, hogy az Azure PowerShell vagy a CLI-t használja, amíg a portálon nem támogatott.

[Azure PowerShell](tutorial-multiple-sites-powershell.md)a `-HostNames` helyett a parancsot kell használnia `-HostName` . Az állomásnevek esetében legfeljebb 5 állomásnév adható meg vesszővel tagolt értékként, és helyettesítő karaktereket is használhat. Például: `-HostNames "*.contoso.com,*.fabrikam.com"`

Az [Azure CLI](tutorial-multiple-sites-cli.md)-ben a helyett a-t kell használnia `--host-names` `--host-name` . A gazdagép-nevek használatával legfeljebb 5 állomásnév adható meg vesszővel tagolt értékként, és helyettesítő karaktereket is használhat. Például: `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Engedélyezett karakterek a gazdagép neve mezőben:

* `(A-Z,a-z,0-9)` – alfanumerikus karakterek
* `-` -kötőjel vagy mínusz
* `.` – időszak elválasztóként
*   `*` – a megengedett tartomány több karakterével is megegyező lehet
*   `?` – az engedélyezett tartományba tartozó egyetlen karakterrel is párosítható

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Helyettesítő karakterek és több állomásnév használatának feltételei egy figyelőben:

*   Egyetlen figyelőben legfeljebb 5 állomásnév lehet megemlítve
*   A csillag `*` csak egyszer szerepelhet egy tartományi stílusú név vagy állomásnév összetevőben. Például: component1 *. component2*. component3. `(*.contoso-*.com)` érvényes.
*   A gazdagép neve legfeljebb két csillaggal rendelkezhet `*` . Például érvényes, `*.contoso.*` és `*.contoso.*.*.com` érvénytelen.
*   Az állomásnév legfeljebb 4 helyettesítő karakterből állhat. A például `????.contoso.com` `w??.contoso*.edu.*` érvényes, de `????.contoso.*` érvénytelen.
*   A csillag `*` és a kérdőjel `?` együttes használata az állomásnév ( `*?` vagy) egy összetevőjében `?*` `**` érvénytelen. Például a `*?.contoso.com` és `**.contoso.com` a érvénytelen.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>A helyettesítő karakteres vagy több állomásnév használatának szempontjai és korlátai a figyelőben:

*   Az [SSL-lezárás és a végpontok közötti SSL](ssl-overview.md) használatához a protokollt HTTPS-ként kell konfigurálni, és fel kell tölteni a figyelő konfigurációjában használni kívánt tanúsítványt. Ha ez egy többhelyes figyelő, akkor az állomásnév is megadható, általában ez az SSL-tanúsítvány CN-neve. Ha több állomásnevet ad meg a figyelőben, vagy helyettesítő karaktereket használ, a következőket kell figyelembe vennie:
    *   Ha ez egy helyettesítő karakteres állomásnév, például *. contoso.com, fel kell töltenie egy helyettesítő karaktert, a következővel: CN like *. contoso.com
    *   Ha ugyanabban a figyelőben több állomásnév is szerepel, akkor fel kell töltenie egy SAN-tanúsítványt (a tulajdonos alternatív neveit), és meg kell adnia az említett állomásnévnek megfelelő CNs-t.
*   Nem használhat reguláris kifejezést az állomásnév megemlítésére. Csak helyettesítő karaktereket (például csillag (*) és kérdőjel (?)) lehet használni az állomásnév-minta létrehozásához.
*   A háttér állapotának ellenőrzését a HTTP-beállításokban nem lehet több [Egyéni](application-gateway-probe-overview.md) mintavételt rendelni. Ehelyett a háttérrendszer egyik webhelyét is kipróbálhatja, vagy a "127.0.0.1" használatával mintavételt hajthat végre a háttér-kiszolgáló localhost-ában. Ha azonban helyettesítő karaktert vagy több állomásnevet használ egy figyelőben, a rendszer a megadott tartományi mintákhoz tartozó kérelmeket a szabály típusától függően a háttér-készletre irányítja át (alap-vagy elérésiút-alapú).
*   A "hostname" tulajdonság egy karakterláncot vesz fel bemenetként, ahol csak egy nem helyettesítő tartománynevet, a "hostnames" karakterláncot pedig bemenetként fogadja, ahol legfeljebb 5 helyettesítő tartománynevet lehet megemlíteni. De a tulajdonságok nem használhatók egyszerre.
*   Olyan célzott figyelővel nem hozható létre [átirányítási](redirect-overview.md) szabály, amely helyettesítő karaktereket vagy több állomásnevet használ.

A többhelyes figyelő helyettesítő karakteres állomásneve konfigurálásával kapcsolatos részletes útmutató a [többhelyes kiszolgálók létrehozása a Azure PowerShell használatával](tutorial-multiple-sites-powershell.md) vagy az [Azure CLI használatával](tutorial-multiple-sites-cli.md) című témakörben olvasható.

## <a name="host-headers-and-server-name-indication-sni"></a>Állomásfejléc és kiszolgálónév jelzése (SNI)

Három elterjedt mechanizmus létezik az ugyanazon az infrastruktúrán történő többhelyes üzemeltetéshez.

1. Több webalkalmazás üzemeltetése mindegyikhez rendelt egyedi IP-címmel.
2. Állomásnév használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.
3. Különböző portok használata több webalkalmazás ugyanazon az IP-címen való üzemeltetésére.

A Application Gateway jelenleg egyetlen nyilvános IP-címet támogat, ahol a forgalmat figyeli. Így a saját IP-címmel rendelkező több alkalmazás használata jelenleg nem támogatott. 

Application Gateway több, különböző portokon figyelt alkalmazást támogat, de ehhez a forgatókönyvhöz az szükséges, hogy az alkalmazások a nem szabványos portokon fogadják a forgalmat. Ez gyakran nem a kívánt konfiguráció.

Az Application Gateway a HTTP 1.1-állomásfejlécek segítségével üzemeltet egynél több webhelyet ugyanarról a nyilvános IP-címről és portról. Az Application Gateway-ben üzemeltetett helyek támogatják a TLS-kiszervezést is Kiszolgálónév jelzése (SNI) TLS-bővítmény használatával. Ebben az esetben az RFC 6066 szabványban meghatározottak szerint az ügyfélböngészőnek és a háttérwebfarmnak támogatnia kell a HTTP/1.1-et és a TLS-bővítményt.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan konfigurálhat több hely üzemeltetését Application Gateway
* [Az Azure Portal használata](create-multiple-sites-portal.md)
* [Az Azure PowerShell használata](tutorial-multiple-sites-powershell.md) 
* [Az Azure parancssori felület használata](tutorial-multiple-sites-cli.md)

A teljes körű sablonalapú telepítés leírását a [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Többhelyes üzemeltetést használó Resource Manager-sablon) weboldalon találja.
