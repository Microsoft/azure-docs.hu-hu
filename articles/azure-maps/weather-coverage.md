---
title: Microsoft Azure Maps időjárási szolgáltatások (előzetes verzió) lefedettsége
description: További információ a Microsoft Azure Maps időjárási szolgáltatások (előzetes verzió) lefedettségéről
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905485"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Azure Maps időjárási szolgáltatások (előzetes verzió) lefedettsége

> [!IMPORTANT]
> Azure Maps időjárási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ez a cikk a Azure Maps [időjárási szolgáltatásokra](/rest/api/maps/weather)vonatkozó lefedettségi információkat tartalmaz. Azure Maps időjárási adatszolgáltatások olyan részleteket adnak vissza, mint például a radar csempéi, a jelenlegi időjárási feltételek, az időjárás-előrejelzés és az időjárás az útvonal mentén.

Azure Maps az összes ország és régió esetében nem rendelkezik azonos szintű információval és pontossággal.

Az alábbi táblázat az egyes országokból/régióktól kérhető időjárási információkról nyújt információt.

| Szimbólum | Értelmezés |
|--------|---------|
|*       |A jelenlegi feltételekkel, óradíjas előrejelzéssel, negyedéves előrejelzéssel, napi előrejelzéssel, időjárással és napi indexekkel foglalkozik. |


## <a name="americas"></a>Észak-, Dél- és Közép-Amerika

| Ország/régió              |  Műholdas csempék | Perc előrejelzés, radar csempék | Súlyos időjárási riasztások | Más |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antarktisz                               | ✓ |   | |✓|
| Antigua és Barbuda                      | ✓ |   | |✓| 
| Argentína                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahama-szigetek                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermuda                                  | ✓ |   | |✓| 
| Bolívia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brazília                                   | ✓ |   | ✓ |✓| 
| Brit Virgin-szigetek                   | ✓ |   | |✓| 
| Kanada                                   | ✓ | ✓ | ✓ | ✓| 
| Kajmán-szigetek                           | ✓ |   | |✓| 
| Chile                                    | ✓ |   | |✓| 
| Kolumbia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Kuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominika                                 | ✓ |   | |✓| 
| Dominikai Köztársaság                       | ✓ |   | |✓| 
| Ecuador                                  | ✓ |   | |✓| 
| Salvador                              | ✓ |   | |✓| 
| Falkland-szigetek                         | ✓ |   | |✓| 
| Francia Guyana                            | ✓ |   | |✓| 
| Grönland                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Guadeloupe                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guyana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Jamaica                                  | ✓ |   | |✓| 
| Martinique                               | ✓ |   | |✓| 
| Mexikó                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paraguay                                 | ✓ |   | |✓| 
| Peru                                     | ✓ |   | |✓| 
| Puerto Rico                              | ✓ |   | ✓ |✓| 
| Saint Barthélemy                         | ✓ |   | |✓| 
| Saint Kitts és Nevis                    | ✓ |   | |✓| 
| Saint Lucia                              | ✓ |   | |✓| 
| Saint Martin                             | ✓ |   | |✓| 
| Saint-Pierre és Miquelon                | ✓ |   | |✓| 
| Saint Vincent és Grenadine-szigetek         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Dél-Georgia és Déli-Sandwich-szigetek | ✓ |   | |✓| 
| Suriname                                 | ✓ |   | |✓| 
| Trinidad és Tobago                      | ✓ |   | |✓| 
| Turks- és Caicos-szigetek                 | ✓ |   | |✓| 
| Amerikai Egyesült Államok lakatlan külbirtokai                    | ✓ |   | |✓| 
| Amerikai Virgin-szigetek                      | ✓ |   | ✓|✓| 
| Egyesült Államok                            | ✓ | ✓ | ✓| ✓| 
| Uruguay                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Közel-Kelet és Afrika

| Ország/régió              |  Műholdas csempék | Perc előrejelzés, radar csempék | Súlyos időjárási riasztások | Más | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algéria                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrein                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botswana                    | ✓               |                              |     |  ✓| 
| Bouvet-sziget               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Kamerun                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Közép-afrikai Köztársaság    | ✓               |                              |     |  ✓| 
| Csád                        | ✓               |                              |     |  ✓| 
| Comore-szigetek                     | ✓               |                              |     |  ✓| 
| Kongó (KDK)                 | ✓               |                              |     |  ✓|
| Côte d'Ivoire               | ✓               |                              |     |  ✓| 
| Dzsibuti                    | ✓               |                              |     |  ✓| 
| Egyiptom                       | ✓               |                              |     |  ✓| 
| Egyenlítői-Guinea           | ✓               |                              |     |  ✓| 
| Eritrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiópia                    | ✓               |                              |     |  ✓| 
| Francia Déli Területek | ✓               |                              |     |  ✓| 
| Gabon                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghána                       | ✓               |                              |     |  ✓| 
| Guinea                      | ✓               |                              |     |  ✓| 
| Bissau-Guinea               | ✓               |                              |     |  ✓| 
| Irán                        | ✓               |                              |     |  ✓| 
| Irak                        | ✓               |                              |     |  ✓| 
| Izrael                      | ✓               |                              |   ✓   |  ✓| 
| Jordánia                      | ✓               |                              |     |  ✓| 
| Kenya                       | ✓               |                              |     |  ✓| 
| Kuvait                      | ✓               |                              |     |  ✓| 
| Libanon                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Libéria                     | ✓               |                              |     |  ✓| 
| Líbia                       | ✓               |                              |     |  ✓| 
| Madagaszkár                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauritánia                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marokkó                     | ✓               |                              |     |  ✓| 
| Mozambik                  | ✓               |                              |     |  ✓| 
| Namíbia                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigéria                     | ✓               |                              |     |  ✓| 
| Omán                        | ✓               |                              |     |  ✓| 
| Palesztin Hatóság       | ✓               |                              |     |  ✓| 
| Katar                       | ✓               |                              |     |  ✓| 
| Réunion                     | ✓               |                              |     |  ✓| 
| Ruanda                      | ✓               |                              |     |  ✓| 
| Szent Ilona, Ascension, Tristan da Cunha        | ✓               |            |     |  ✓| 
| São Tomé és Príncipe       | ✓               |                              |     |  ✓| 
| Szaúd-Arábia                | ✓               |                              |     |  ✓| 
| Szenegál                     | ✓               |                              |     |  ✓| 
| Seychelle-szigetek                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Szomália                     | ✓               |                              |     |  ✓| 
| Dél-afrikai Köztársaság                | ✓               |                              |     |  ✓| 
| Dél-Szudán                 | ✓               |                              |     |  ✓| 
| Szudán                       | ✓               |                              |     |  ✓| 
| Szíria                       | ✓               |                              |     |  ✓| 
| Tanzánia                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunézia                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Egyesült Arab Emírségek        | ✓               |                              |     |  ✓| 
| Jemen                       | ✓               |                              |     |  ✓| 
| Zambia                      | ✓               |                              |     |  ✓| 
| Zimbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió              |  Műholdas csempék | Perc előrejelzés, radar csempék | Súlyos időjárási riasztások | Más |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afganisztán                       | ✓ |   | | ✓| 
| Amerikai Szamoa                    | ✓ |   |  ✓| ✓| 
| Ausztrália                         | ✓ | ✓ |  ✓ | ✓| 
| Banglades                        | ✓ |   | | ✓| 
| Bhután                            | ✓ |   | | ✓| 
| Brit indiai-óceáni terület    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Kambodzsa                          | ✓ |   | | ✓| 
| Kína                             | ✓ | ✓ |  ✓ | ✓| 
| Karácsony-sziget                  | ✓ |   | | ✓| 
| Cocos (Keeling)-szigetek           | ✓ |   | | ✓| 
| Cook-szigetek                      | ✓ |   | | ✓| 
| Fidzsi                              | ✓ |   | | ✓| 
| Francia Polinézia                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Heard-sziget és McDonald-szigetek | ✓ |   | | ✓| 
| Hongkong (KKT)                     | ✓ |   | | ✓| 
| India                             | ✓ |   | | ✓| 
| Indonézia                         | ✓ |   | | ✓| 
| Japán                             | ✓ | ✓ |  ✓| ✓| 
| Kazahsztán                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Dél-Korea                             | ✓ | ✓ | ✓ |  ✓| 
| Kirgizisztán                        | ✓ |   | | ✓| 
| Laosz                              | ✓ |   | | ✓| 
| Makaó (KKT)                         | ✓ |   | | ✓| 
| Malajzia                          | ✓ |   | | ✓| 
| Maldív-szigetek                          | ✓ |   | | ✓| 
| Marshall-szigetek                  | ✓ |   | ✓ | ✓| 
| Mikronézia                        | ✓ |   | ✓ | ✓| 
| Mongólia                          | ✓ |   | | ✓| 
| Mianmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepál                             | ✓ |   | | ✓| 
| Új-Kaledónia                     | ✓ |   | | ✓| 
| Új-Zéland                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Norfolk-sziget                    | ✓ |   | | ✓| 
| Észak-Korea                       | ✓ |   | | ✓| 
| Északi Mariana-szigetek          | ✓ |   | ✓ | ✓| 
| Pakisztán                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Pápua Új-Guinea                  | ✓ |   | | ✓| 
| Fülöp-szigetek                       | ✓ |   | ✓ | ✓| 
| Pitcairn-szigetek                  | ✓ |   | | ✓| 
| Szamoa                             | ✓ |   | | ✓| 
| Szingapúr                         | ✓ |   | | ✓| 
| Salamon-szigetek                   | ✓ |   | | ✓| 
| Srí Lanka                         | ✓ |   | | ✓| 
| Tajvan                            | ✓ |   | | ✓| 
| Tádzsikisztán                        | ✓ |   | | ✓| 
| Thaiföld                          | ✓ |   | | ✓| 
| Timor-Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Türkmenisztán                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Üzbegisztán                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnam                           | ✓ |   | | ✓| 
| Wallis és Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Európa

| Ország/régió              |  Műholdas csempék | Perc előrejelzés, radar csempék | Súlyos időjárási riasztások | Más | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albánia                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Örményország                | ✓ |   | | ✓| 
| Ausztria                | ✓ | ✓ | ✓ | ✓|
| Azerbajdzsán             | ✓ |   | | ✓| 
| Belarusz                | ✓ |   | | ✓| 
| Belgium                | ✓ | ✓ |  ✓| ✓| 
| Bosznia-Hercegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgária               | ✓ |   |  ✓| ✓| 
| Horvátország                | ✓ | ✓ |  ✓| ✓| 
| Ciprus                 | ✓ |   | ✓ | ✓| 
| Csehország                | ✓ | ✓ | ✓ | ✓| 
| Dánia                | ✓ | ✓ | ✓ | ✓| 
| Észtország                | ✓ | ✓ |  ✓| ✓| 
| Feröer szigetek          | ✓ |   | | ✓| 
| Finnország                | ✓ | ✓ | ✓ | ✓| 
| Franciaország                 | ✓ | ✓ | ✓ | ✓| 
| Grúzia                | ✓ |   | | ✓| 
| Németország                | ✓ | ✓ | ✓ | ✓| 
| Gibraltár              | ✓ | ✓ | | ✓| 
| Görögország                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Magyarország                | ✓ | ✓ |  ✓| ✓| 
| Izland                | ✓ |   | ✓ | ✓| 
| Írország                | ✓ | ✓ |  ✓| ✓| 
| Olaszország                  | ✓ |   |  ✓| ✓|
| Man-sziget            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Koszovó                 | ✓ |   |  ✓| ✓| 
| Lettország                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Litvánia              | ✓ |   | ✓ | ✓| 
| Luxemburg             | ✓ | ✓ |  ✓| ✓| 
| Észak-Macedónia        | ✓ |   | ✓ | ✓| 
| Málta                  | ✓ |   | ✓ | ✓| 
| Moldova                | ✓ | ✓ | ✓ | ✓| 
| Monaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegró             | ✓ | ✓ |  ✓| ✓| 
| Hollandia            | ✓ | ✓ |  ✓| ✓| 
| Norvégia                 | ✓ | ✓ |  ✓| ✓| 
| Lengyelország                 | ✓ | ✓ |  ✓| ✓| 
| Portugália               | ✓ | ✓ |  ✓| ✓| 
| Románia                | ✓ | ✓ |  ✓| ✓| 
| Oroszország                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Szerbia                 | ✓ | ✓ |  ✓| ✓| 
| Szlovákia               | ✓ | ✓ |  ✓| ✓| 
| Szlovénia               | ✓ | ✓ |  ✓| ✓| 
| Spanyolország                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Svédország                 | ✓ | ✓ |  ✓| ✓| 
| Svájc            | ✓ | ✓ | ✓| ✓| 
| Törökország                 | ✓ |   | | ✓| 
| Ukrajna                | ✓ |   | | ✓| 
| Egyesült Királyság         | ✓ | ✓ | ✓| ✓| 
| Vatikán           | ✓ |   |✓ | ✓|