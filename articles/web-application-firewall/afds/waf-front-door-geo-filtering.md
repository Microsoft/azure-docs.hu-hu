---
title: Geo-szűrés egy tartományon az Azure bejárati ajtó szolgáltatásához
description: Ebben a cikkben megismerkedhet az Azure Front Door Service geoszűrési szabályzatával
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: eb91dfd5d774e591d3c3c3964668dcc626086181
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512573"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Mi a Geo-szűrés egy tartományon az Azure bejárati ajtón?

Alapértelmezés szerint az Azure Front Door Service a kérelmet küldő felhasználó a tartózkodási helytől függetlenül válaszol a felhasználói kérelmekre. Bizonyos esetekben azonban előfordulhat, hogy az ország/régió alapján korlátozni szeretné a webalkalmazásokhoz való hozzáférést. A webalkalmazási tűzfal (WAF) a bejárati ajtón lehetővé teszi, hogy egyéni hozzáférési szabályokkal Definiáljon egy házirendet a végpont megadott elérési útjához, hogy engedélyezze vagy tiltsa le a hozzáférést a megadott országokból/régiókból. 

A WAF szabályzat általában egyéni szabályok készletét tartalmazza. A szabályok egy egyeztetési feltételből, egy műveletből és egy prioritásból állnak. Az egyeztetési feltételben egy egyeztetési változót, egy operátort és egy egyeztetési értéket kell megadni.  A geoszűrési szabályok esetében az egyeztetési változó a REMOTE_ADDR, az operátor a GeoMatch, az érték pedig az érintett kétbetűs országkód. Egy GeoMatch feltételt és egy REQUEST_URI sztringegyeztetési feltételt kombinálva elérésiút-alapú geoszűrési szabályokat hozhat létre.

A Geo-szűrési házirendet az előtérben is konfigurálhatja [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) vagy a rövid útmutató [sablonjának](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)használatával.

## <a name="country-code-reference"></a>Országkód-hivatkozás

|Országkód | Ország neve |
| ----- | ----- |
| AD | Andorra |
| AE | Egyesült Arab Emírségek|
| AF | Afganisztán|
| AG | Antigua és Barbuda|
| AL | Albánia|
| AM | Örményország|
| AO | Angola|
| AR | Argentína|
| AS | Amerikai Szamoa|
| AT | Ausztria|
| AU | Ausztrália|
| AZ | Azerbajdzsán|
| BA | Bosznia-Hercegovina|
| BB | Barbados|
| BD | Banglades|
| BE | Belgium|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | Saint Barthélemy|
| BN | Brunei Szultanátus|
| BO | Bolívia|
| BR | Brazília|
| BS | Bahama-szigetek|
| BT | Bhután|
| BW | Botswana|
| BY | Belarusz|
| BZ | Belize|
| CA | Kanada|
| CD | Kongói Demokratikus Köztársaság|
| CF | Közép-afrikai Köztársaság|
| CH | Svájc|
| CI | Elefántcsontpart|
| CL | Chile|
| CM | Kamerun|
| CN | Kína|
| CO | Kolumbia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Ciprus|
| CZ | Cseh Köztársaság|
| DE | Németország|
| DK | Dánia|
| DO | Dominikai Köztársaság|
| DZ | Algéria|
| EC | Ecuador|
| EE | Észtország|
| EG | Egyiptom|
| ES | Spanyolország|
| ET | Etiópia|
| FI | Finnország|
| FJ | Fidzsi|
| FM | Mikronéziai Szövetséges Államok|
| JK | Franciaország|
| GB | Egyesült Királyság|
| GE | Grúzia|
| GF | Francia Guyana|
| GH | Ghána|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Görögország|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong (KKT)|
| HN | Honduras|
| HR | Horvátország|
| HT | Haiti|
| HU | Magyarország|
| ID (Azonosító) | Indonézia|
| IE | Írország|
| IL | Izrael|
| IN | India|
| IQ | Irak|
| IR | Iráni Iszlám Köztársaság|
| IS | Izland|
| IT | Olaszország|
| JM | Jamaica|
| JO | Jordánia|
| JP | Japán|
| KE | Kenya|
| KG | Kirgizisztán|
| KH | Kambodzsa|
| KI | Kiribati|
| KN | Saint Kitts és Nevis|
| KP | Koreai Népi Demokratikus Köztársaság|
| KR | Koreai Köztársaság|
| KW | Kuvait|
| KY | Kajmán-szigetek|
| KZ | Kazahsztán|
| LA | Laoszi Népi Demokratikus Köztársaság|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Srí Lanka|
| LR | Libéria|
| LS | Lesotho|
| LT | Litvánia|
| LU | Luxemburg|
| LV | Lettország|
| LY | Líbia |
| MA | Marokkó|
| MD | Moldovai Köztársaság|
| MG | Madagaszkár|
| MK | Észak-Macedónia|
| ML | Mali|
| MM | Mianmar|
| MN | Mongólia|
| MO | Makaó KKT|
| MQ | Martinique|
| MR | Mauritánia|
| MT | Málta|
| MV | Maldív-szigetek|
| MW | Malawi|
| MX | Mexikó|
| MY | Malajzia|
| MZ | Mozambik|
| NA | Namíbia|
| NE | Niger|
| NG | Nigéria|
| NI | Nicaragua|
| NL | Hollandia|
| NO | Norvégia|
| NP | Nepál|
| NR | Nauru|
| NZ | Új-Zéland|
| OM | Omán|
| PA | Panama|
| PE | Peru|
| PH | Fülöp-szigetek|
| PK | Pakisztán|
| PL | Lengyelország|
| PR | Puerto Rico|
| PT | Portugália|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Románia|
| RS | Szerbia|
| RU | Orosz Föderáció|
| RW | Ruanda|
| SA | Szaúd-Arábia|
| SD | Szudán|
| SE | Svédország|
| SG | Szingapúr|
| SI | Szlovénia|
| SK | Szlovákia|
| SN | Szenegál|
| SO | Szomália|
| SR | Suriname|
| SS | Dél-Szudán|
| SV | Salvador|
| SY | Szíriai Arab Köztársaság|
| SZ | Szváziföld|
| TC | Turks- és Caicos-szigetek|
| TG | Togo|
| TH | Thaiföld|
| TN | Tunézia|
| TR | Törökország|
| TT | Trinidad és Tobago|
| TW | Tajvan|
| TZ | Tanzániai Egyesült Köztársaság|
| UA | Ukrajna|
| UG | Uganda|
| USA | Egyesült Államok|
| UY | Uruguay|
| UZ | Üzbegisztán|
| VC | Saint Vincent és Grenadine-szigetek|
| VE | Venezuela|
| VG | Brit Virgin-szigetek|
| VI | Amerikai Virgin-szigetek|
| VN | Vietnam|
| ZA | Dél-Afrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>További lépések

- További információ az [Azure Front Door alkalmazási rétegbeli biztonságáról](../../frontdoor/front-door-application-security.md).
- [Frontdoor létrehozására](../../frontdoor/quickstart-create-front-door.md) vonatkozó információk.
