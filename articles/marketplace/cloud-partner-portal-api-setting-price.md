---
title: A virtuálisgép-ajánlatok díjszabása – Azure Marketplace
description: Ismerteti a virtuálisgép-ajánlatok díjszabásának három módszerét.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393819"
---
# <a name="pricing-for-virtual-machine-offers"></a>Virtuálisgép-ajánlatok díjszabása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. A CPP API-kat csak olyan meglévő termékekhez szabad használni, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

A virtuális gépekre vonatkozó ajánlatok díjszabása a következő három módon adható meg: testreszabott alapszintű díjszabás, alapszintű díjszabás és a számolótábla díjszabása.

## <a name="customized-core-pricing"></a>Testreszabott alapvető díjszabás

A díjszabás az egyes régiók és a Core kombinációk esetében egyedi. Az értékesítési listán szereplő összes régiót meg kell adni a definíció **virtualMachinePricing** / **regionPrices** szakaszában.  A kérelemben szereplő minden egyes [régióhoz](#regions) használja a megfelelő pénznemkóddal.  A következő példa ezeket a követelményeket mutatja be:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>Alapszintű díjszabás

Ebben az esetben a közzétevők a SKU-ban egy árat határoznak meg USD értékben, és minden egyéb díj automatikusan létrejön. Az egységenkénti díj a kérelem **egyetlen** paraméterében van megadva.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```

## <a name="spreadsheet-pricing"></a>Táblázat díjszabása

A közzétevő a díjszabási táblázatot is feltöltheti egy ideiglenes tárolóhelyre, majd belefoglalja az URI-t a kérelembe, például a többi fájl összetevőt. A rendszer feltölti a táblázatot, és lefordítja a megadott árlista kiértékelésére, végül pedig a díjszabási információkkal frissíti az ajánlatot. Az ajánlatra vonatkozó későbbi GET-kérések a táblázat URI azonosítóját és a régió értékelt árát fogják visszaadni.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

## <a name="new-core-sizes-added-on-722019"></a>Új alapvető méretek hozzáadva a 7/2/2019

Az új Azure-beli virtuálisgép-méretek (a magok száma alapján) új díjszabást kapott a VM-közzétevők 2019. július 2-án.  Az új árak a következő alapméretekre vonatkoznak: 10, 44, 48, 60, 120, 208 és 416.  A meglévő virtuális gépek esetében a magok méretének új díjait a jelenlegi árak alapján automatikusan kiszámítjuk. A kiadók 2019 augusztus 1-ig megtekintik a további árakat, és elvégzik a kívánt módosításokat.  Ezt a dátumot követően, ha a közzétevő még nem tette közzé újra, az új alapvető méretek automatikusan kiszámított árai érvénybe lépnek.

## <a name="regions"></a>Régiók

A következő táblázat azokat a különböző régiókat mutatja be, amelyeket megadhat a testreszabott alapdíjszabáshoz, valamint a hozzájuk tartozó pénznemkóddal.

| **Régió** | **Név**             | **Pénznemkód** |
|------------|----------------------|-------------------|
| DZ         | Algéria              | DZD               |
| AR         | Argentína            | ARS               |
| AU         | Ausztrália            | AUD               |
| AT         | Ausztria              | EUR               |
| BH         | Bahrein              | BHD               |
| BY         | Belarusz              | RUB               |
| BE         | Belgium              | EUR               |
| BR         | Brazília               | USD               |
| BG         | Bulgária             | BGN               |
| CA         | Kanada               | CAD               |
| CL         | Chile                | CLP               |
| CO         | Kolumbia             | MÁSOLATTAL               |
| CR         | Costa Rica           | CRC               |
| HR         | Horvátország              | HRK               |
| CY         | Ciprus               | EUR               |
| CZ         | Csehország       | CZK               |
| DK         | Dánia              | DKK               |
| DO         | Dominikai Köztársaság   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egyiptom                | EGP               |
| SV         | Salvador          | USD               |
| EE         | Észtország              | EUR               |
| FI         | Finnország              | EUR               |
| JK         | Franciaország               | EUR               |
| DE         | Németország              | EUR               |
| GR         | Görögország               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong (KKT)        | HKD               |
| HU         | Magyarország              | Ft               |
| IS         | Izland              | ISK               |
| IN         | India                | INR               |
| ID (Azonosító)         | Indonézia            | IDR               |
| IE         | Írország              | EUR               |
| IL         | Izrael               | ILS               |
| IT         | Olaszország                | EUR               |
| JP         | Japán                | JPY               |
| JO         | Jordánia               | JOD               |
| KZ         | Kazahsztán           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Dél-Korea                | KRW               |
| KW         | Kuvait               | KWD               |
| LV         | Lettország               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litvánia            | EUR               |
| LU         | Luxemburg           | EUR               |
| MY         | Malajzia             | MYR               |
| MT         | Málta                | EUR               |
| MX         | Mexikó               | MXN               |
| ME         | Montenegró           | EUR               |
| MA         | Marokkó              | ŐRÜLT               |
| NL         | Hollandia          | EUR               |
| NZ         | Új-Zéland          | NZD               |
| NG         | Nigéria              | NGN               |
| MK         | Észak-Macedónia      | MKD               |
| NO         | Norvégia               | NOK               |
| OM         | Omán                 | OMR               |
| PK         | Pakisztán             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | TOLLAS               |
| PH         | Fülöp-szigetek          | PHP               |
| PL         | Lengyelország               | PLN               |
| PT         | Portugália             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Katar                | QAR               |
| RO         | Románia              | RON               |
| RU         | Oroszország               | RUB               |
| SA         | Szaúd-Arábia         | SAR               |
| RS         | Szerbia               | RSD               |
| SG         | Szingapúr            | SGD               |
| SK         | Szlovákia             | EUR               |
| SI         | Szlovénia             | EUR               |
| ZA         | Dél-afrikai Köztársaság         | ZAR               |
| ES         | Spanyolország                | EUR               |
| LK         | Srí Lanka            | USD               |
| SE         | Svédország               | SEK               |
| CH         | Svájc          | CHF               |
| TW         | Tajvan               | TWD               |
| TH         | Thaiföld             | THB               |
| TT         | Trinidad és Tobago  | TTD               |
| TN         | Tunézia              | TND               |
| TR         | Törökország               | TRY               |
| UA         | Ukrajna              | UAH               |
| AE         | Egyesült Arab Emírségek | EUR               |
| GB         | Egyesült Királyság       | GBP               |
| USA         | Egyesült Államok        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
