---
title: Geo-szűrés egy tartományon az Azure bejárati ajtón | Microsoft Docs
description: Ebben a cikkben megismerheti az Azure-beli bejáratra vonatkozó geo-szűrési szabályzatot
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 33ad17e3024011368c909a89e9164ca06d044bad
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612082"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Geo-szűrés egy tartományon az Azure bejárati ajtaján

Alapértelmezés szerint az Azure bejárati ajtaja válaszol az összes felhasználói kérésre, függetlenül attól, hogy a kérés honnan származik. Bizonyos esetekben előfordulhat, hogy korlátozni szeretné a webalkalmazáshoz való hozzáférést országok/régiók szerint. A webalkalmazási tűzfal (WAF) a bejárati ajtón lehetővé teszi, hogy egyéni hozzáférési szabályokkal Definiáljon egy házirendet a végpont egy adott elérési útján, hogy engedélyezze vagy tiltsa le a hozzáférést a megadott országokból/régiókból. 

A WAF szabályzat egyéni szabályokat tartalmaz. A szabály egyezési feltételekből, műveletből és prioritásból áll. Egyeztetési feltételben definiálni kell egy egyezési változót, egy operátort és egy egyezési értéket. A földrajzi szűrési szabályok esetében REMOTE_ADDR az operátor GeoMatch, és az érték egy kétbetűs ország/régió. A "ZZ" országkód vagy az "ismeretlen" ország rögzíti azokat az IP-címeket, amelyek még nincsenek leképezve az adatkészlet egyik országához sem. Az egyeztetési feltételhez hozzáadhatja a ZZ-t, hogy elkerülje a hamis pozitív értéket. Egy GeoMatch-feltételt és egy REQUEST_URI karakterlánc-egyeztetési feltételt egyesítheti elérésiút-alapú geo-szűrési szabály létrehozásához. 


Az [Azure PowerShell](front-door-tutorial-geo-filtering.md) vagy egy rövid útmutató [sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)használatával konfigurálhatja a Geo-szűrési szabályzatot az előtérben.

## <a name="countryregion-code-reference"></a>Ország/régió kódjának referenciája

|Ország/régió kódja | Ország/régió neve |
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
| MO | Makaó (KKT)|
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
| Szórás | Szudán|
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
| ZA | Dél-afrikai Köztársaság|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- Ismerje meg, hogyan [állíthat be geo-szűrési WAF szabályzatot](front-door-tutorial-geo-filtering.md).
