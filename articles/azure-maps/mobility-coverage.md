---
title: Mobilitási (tranzit) lefedettség Microsoft Azure Maps mobilitási szolgáltatásokban (előzetes verzió)
description: Ismerje meg, hogy a Azure Maps mobilitási szolgáltatások (előzetes verzió) milyen szintű lefedettséggel rendelkezik, mely régiókban érhetők el a nyilvános árutovábbítási funkciók, például az Útválasztás és a szolgáltatás riasztásai.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e902f313edf22d75f6b183575c3dc8d0dd94bc1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904754"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Azure Maps mobilitási szolgáltatások (előzetes verzió) lefedettsége

> [!IMPORTANT]
> Azure Maps mobilitási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


A Azure Maps [mobilitási szolgáltatás](/rest/api/maps/mobility) javítja a nyilvános átviteli funkciókkal rendelkező alkalmazások fejlesztési idejét, például a tranzit útválasztást és a közeli nyilvános átvitelek keresését. A felhasználók részletes információkat kérhetnek a továbbítási leállásokról, a sorokról és az ütemtervekről. A mobilitási szolgáltatások azt is lehetővé teszik a felhasználóknak, hogy leállítsák a leállítási és a vonalas geometriákat, a leállási, a soros és a szolgáltatási területekre vonatkozó riasztásokat, valamint a valós idejű, nyilvános tranzit Emellett a mobilitási szolgáltatások útválasztási lehetőségeket biztosítanak a multimodális kirándulások megtervezéséhez. A multimodális utazás megtervezése magában foglalja a gyaloglást, a kerékpározást és a nyilvános továbbítási lehetőségeket, mindezt egyetlen útra. A felhasználók a részletes multimodális lépésenkénti útvonalakat is használhatják.

Azure Maps nem biztosít azonos szintű információt és pontosságot az összes város és ország/régió számára. A nyilvános árutovábbítási adatok meghívásának lehetősége a Metro területtől függ. Emellett előfordulhat, hogy a térképi adatok nem tartalmazzák a metró területét kiszolgáló összes nyilvános átviteli lehetőséget és Ügynökséget.

Az alábbi táblázat a Azure Maps mobilitási szolgáltatások lefedettségi információit tartalmazza.

| Szimbólum | Értelmezés |
|--------|---------|
| *      |Szinte teljes lefedettség az ország/régió számára.|

## <a name="americas"></a>Észak-, Dél- és Közép-Amerika

| Ország/régió |  Város (metró) |
|----------------|---------|
| Antigua és Barbuda | Antigua és Barbuda * |
| Argentína       | <p>Azul, Bahía Blanca, Buenos Aires, Caleta Olivia, Catamarca, Chivilcoy, Comodoro Rivadavia, Concordia, Córdoba, Corrientes, általános Pico, Gualeguaychu, La Rioja, Mar del Plata, Mendoza, Miramar, Necochea, Neuquén, Oberá, Olavarría, Paraná, Posadas, Rafaela, Rio Tercero, Rosario, Salta, San Carlos de Bariloche, San Luis, San Miguel de Tucumán, San Pedro, Santa Fe, Tandil, Ushuaia, Victoria, Viedma, Villa María</p>|
| Barbados       |  Barbados |
| Brazília         | <p>Angra dos Reis, Anápolis, Apucarana, Aracaju, Araraquara, Araxa, Araçatuba, Atibaia, Bage, Barretos, Bauru, Bebedouro, Belém, Belo Horizonte, Blumenau, Boa Vista, Botucatu, Brasilia, Caldas Novas, Campina Grande, Campinas, Campo Belo, Campo Grande, Caraguatatuba, Caratinga, Cascavel, Cataguases, Caxias, Leopoldina e Região, Catalão, Caxias do Sul, Chapecó, Cianorte, Conselheiro Lafaiete, Corumbá, Criciúma, Cruzeiro do Sul, Cuiabá, Curitiba, Curitibanos, Curvelo, Diamantina, Divinópolis, Dourados, Estrela, Feira de Santana, Fernando de Noronha, Florianópolis, Fortaleza, Foz do Iguaçu, Franca, Garanhuns, Goiania, Governador Valadares, Guarapuava, Imperatriz, Ipatinga, Irati, Itabira, Itabuna, Itajaí, Itajuba, Ituiutaba, Jaguarao, Jaraguá do Sul, Joao Pessoa, Joinville, Juazeiro do Norte, Juiz de fórumok, Jundiaí, Lages, Lavras e Regio, Lucas do Rio Verde, Londrina, Macapa, Macaé, Maceió, Mafra e Rio Negro, Manaus, Manhuacu, Maringá, Marília, Monte Carmelo , Montes Claros, Mossoró, Natal, Osorio, Ourinhos, Ouro Preto, Palmas, Paracatu, Paranaguá, Parnaíba, Passo Fundo, Passos, Pato Branco, Patos de Minas, Patrocínio, Pelotas, Picos, Piracicaba, Pirapora, Pocos de Caldas, Ponta Grossa, Porto Alegre, Porto Ferreira, Porto Seguro, Porto Velho, Praia Grande, Recife, Ribeirão Preto, Rio, Rio Branco, Rio Verde, Rondonópolis, Salinas, Salvador, Santa Cruz do Sul, Santa Maria, Santa Rita do Sapucaí, Santarem, Santiago del Estero, Santos, Sao Gabriel do Oeste, Sao Joao del Rei, Tiradentes e Regio, Sao Jose do Rio Preto, Sao Mateus, Sao Paulo, Sorocaba, São Carlos, São Francisco do Sul, São José dos Campos, São Lourenço, São Luís, Taubaté, Telemaco borba, Teofilo Otoni, Teresina, Toledo, Três Lagon, Tucurui, Ubatuba, Uberaba, Uberlândia, Ubá, Uruguaiana, Varginha, Vicosa, Videira & Fraiburgo , Vitória, Vitória da Conquista, volta Redonda, Votuporanga </p>|
| Kanada | Banff (AB), Brandon (MB), Calgary (AB), Chatham-Kent (ON), Comox-völgy (BC), Cowichan-völgy (BC), Cranbrook (BC), Edmonton (AB), Fort St. John, Fredericton (NB), nagyobb Sudbury (ON), Greater Vancouver (BC), Halifax (NS), Kamloops (BC), Kelowna-Vernon (BC), Kingston (ON), London (ON), Moncton (NB), Montreal (QC), Nanaimo (BC), Ottawa (ON), Prince George (BC), Québec City (QC), Red Deer (AB), Regina (SK), Rimouski (QC), Saskatoon (SK), Sherbrooke (QC), Délnyugat-Brit Columbia (BC), Squamish (BC)), St. John 's (NL), Sunshine Coast, Thunder Bay (ON), Toronto (ON), Victoria (BC), West Kootenay (BC), Whistler (BC), Windsor (ON), Winnipeg (MB), Woodstock</p>|
| Chile  | <p>Antofagasta, Arica, Aysén, Chillán, Concepción, Constitución, Copiapó, Curicó, Iquique, La Serena y Coquimbo, Linares, Los Angeles (Chile), Los Lagos, Punta Arenas, Rancagua, Santiago, talkum, Temuco, Veszprémi, Valparaíso, Viña del Mar</p>|
| Kolumbia | <p>Barranquilla, Bogotá, Bucaramanga, Cali, Cartagena, Ibagué, Medellín, Pasto, Popayán, Santa Marta, Sincelejo, Valledupar</p>|  
| Costa Rica | San José|
| Dominikai Köztársaság | Santo Domingo |
| Ecuador | Cuenca, Guayaquil, Loja, Manta, MILAGRO|
| Salvador | San Salvador |
 | Guatemala | Ciudad de Guatemala (GT) |
| Mexikó | Acapulco, Aguascalientes, Cancun, Durango, Mexico City, Guadalajara, oroszlán, Merida, Monterrey, Puebla, Puerto Vallarta, Querétaro, San Luis Potosi, Tijuana, Torreon|
| Nicaragua | Managua |
| Panama | Panama|
| Peru | Cusco, Lima |
| Puerto Rico | San Juan |
| Suriname | Paramaribo |
| Uruguay | Montevideo, Paysandu, Punta del este, Salto |
| Amerika Egyesült Államok | <p>Albany (GA), Albany (NY), Albuquerque (NM), Anchorage (AK), Ann Arbor (MI) Appleton-Oshkosh-Neenah (WI), Asheville (NC), Athén (GA), Athén (OH), Atlanta (GA), Austin (TX), Bakersfield (CA), Baltimore (MD), Bend-Redmond (vagy), Berkshire County (MA), Birmingham (AL), Bloomington (IN), Boise (ID), Boston (MA), Boulder (CO), Bowling Green (KY), Brevard County (FL), Buffalo (NY), Butte (MT), Cape Cod (MA), Centre County (PA), Champaign-Urbana (IL), Charleston (SC), Charleston (WV), Charlotte (NC), Charlottesville (VA), Chattanooga (TN), Cheyenne (WY), Chicago (IL), Cincinnati (OH), citrus County (FL), Cleveland (OH), Coachella Valley (CA), Colorado Springs (CO), Columbia (TN), Columbia (SC)), Columbus (OH), Corpus Christi (TX), Dallas/Forth Worth (TX), Dayton (OH), Delaware, Denver (CO), Des Moines (IA), Detroit (MI), Duluth (MN), el Paso (TX), Eugene (vagy), Fairbanks (AK), Fargo (ND) , Fayetteville (NC), Flagstaff (AZ), Flint (MI) Fort Collins (CO), Fort Wayne (IN), Fresno (CA), Gainesville (FL), Grand Forks (ND), Grand Rapids (MI), Green Bay (WI), Greensboro (NC), Greenville (SC), Gunnison (CO), Hampton Roads (VA), Hanford (CA), Hartford (CT), Hernando County (FL), Hinesville (GA), Honolulu (HI), Houston (TX), Humboldt County (CA), Huntsville (AL), Indianapolis (IN), Ithaca (NY), Jackson (MS), Jackson (TN), Jacksonville-St. John County (FL), Johnson City (TN), Jonesboro (AR), Joplin (MO), Juneau (AK), Kalamazoo (MI), Kalispell (MT), Kansas City (MO), a Kauai (HI), Ketchum (ID), Knoxville (TN), Lafayette (IN), Lancaster (PA), Lansing (MI), Laredo (TX), Las Vegas (NV), Lawrence (KS), Lee County (FL), Lexington (KY), Lincoln County (vagy), Little Rock (AR), Los Angeles (CA), Louisville (KY), Lubbock (TX), Madison (WI), Manchester (NH) , McAllen (TX), Memphis (TN), Miami (FL), Milwaukee-Waukesha (WI), Minneapolis-St. Paul (MN), Missoula (MT), Modesto (USA), Moline (IL), Monroe megye (PA), Montgomery (AL), Morgantown (WV), Nashville (TN), Navajo Nation), New Haven (CT), New Orleans (LA), NYC-NJ területén (NY), Ocala (FL), Okaloosa County (FL), Oklahoma City (OK), Omaha (NE), Orlando (FL), Palm Desert (CA), Panama City (FL), Pensacola (FL), Peoria (IL), Philadelphia (PA), Phoenix (AZ), Pittsburgh (PA), Portland (ME), Portland (vagy), Racine (WI), Raleigh (NC), Redding (CA), Reno & Lake Tahoe (NV), Richmond (VA), a Roanoke Valley (VA-Lynchburg), Rochester (NY), Rockford (IL), Rocky Mount (NC), Rocky Mountain National Park (CO), Vadóc-völgy (vagy), Roseburg (vagy), Roseville (CA), Sacramento (CA), Salem (vagy), Salt Lake City (UT), San Antonio (TX), San Diego (CA), San Luis Obispo (CA), Santa Barbara (CA), Santa Fe (NM), Sarasota (FL) , Savannah (GA), Seacoast Region (NH), Seattle-Tacoma-Bellevue (WA), SF Bay terület (CA), SF-San Jose terület (CA), Sioux City (IA), Sioux Falls (SD), Sitka (AK), Spokane (WA), Springfield (MA), Dél-kanyar (IN), Springfield (IL), Springfield (Mass), St. George (UT), St. Louis (MO), Stockton (CA), Syracuse-Utica (NY), Tallahassee (FL), Tampa-St. Szentpétervár (FL), Terre Haute (IN), Toledo (OH), Topeka (KS), bejárási város (MI), Tucson (AZ), Tulsa (OK), Vermont, Victorville (CA), Volusia megye (FL), Waco (TX), Washington (DC), Waterbury (CT), Wichita (KS), Wichita Falls (TX) Wilmington (NC), az Yakima (WA), Youngstown (OH), York megye (PA), Yuma County (AZ)</p>|
| + Amerikai Virgin-szigetek | Amerikai Virgin-szigetek * |
| Venezuela | Caracas |

## <a name="asia-pacific"></a>Ázsia és a Csendes-óceáni térség

| Ország/régió |  Város (metró) |
|--------|---------|
| Ausztrália | <p>Adelaide, Alice Springs, Bowen, Brisbane, Bundaberg QLD, Burnie, Cairns, Canberra, Darwin, Gladstone, Hobart, Innisfail, Launceston, Mackay, Magnetic Island, Maryborough-Hervey Bay, Melbourne, New South Wales, Perth, RockHampton, Dél-Kelet Queensland, Sydney, Toowoomba, Townsville, Victoria, Warwick, Yeppoon</p> |
| Brunei | Bandar Seri Begawan |
| Kína | <p> Changchun, Changsha, Chengdu, Chongqing, Dalian, Datong, Dongguan, Hangzhou, Harbin, Jiangyin, Jinan, Nanjing, Nantong, Ningbo, Pingdingshan, Qingdao, Shenyang, Suzhou, Tangshan, Tianjin, Weifang, Wuhan, Wuxi, Yantai, yixing, Zhuhai, Shanghai, Peking, Guangzhou, Shenzhen, Zhengzhou</P>|
| Hongkong (KKT) | Hongkong KKT *|
| Makaó (KKT) | Makaó KKT *|
| Maldív-szigetek | Férfi |
| India | Ahmedabad, Bengaluru, Delhi, Hyderabad, Mumbai, Mysuru, Pune|
| Indonézia | Bandung, Banjarmasin, Banyuwangi, Batam, Denpasar, Jakarta, Kediri, Malang, Palembang, Semarang, Surabaya, Surakarta, Yogyakarta |
| Japán | Hokkaido, Shizuoka prefektúra, Tokió, Wakkanai, Yamanashi Prefektúra |
| Malajzia | Ipoh, Johar Bahru, Kuala Lumpur, Kuantan, Penang |
| Új-Zéland | Auckland, Christchurch, Dunedin, Queenstown, Timaru, Wellington|
| Fülöp-szigetek | Manila |
| Szingapúr | Szingapúr |
| Dél-Korea | Busan, Szöul |
| Tajvan | Changhua megye, Taipei |
| Thaiföld | Bangkok, Chiang Mai |
| Üzbegisztán | Szamarkand |
| Vietnam | Hanoi, Ho Chi Minh város |

## <a name="europe"></a>Európa

| Ország/régió |  Város (metró) |
|----------------|---------|
| Andorra        | Andorra la Vella |
| Ausztria        | Bécs |
| Belarusz        | Gomel, Grodno, Polotsk & Novopolotsk, Zhlobin, Vileyka, Maladziečna, Minszk, Rechytsa |
| Belgium        | Belgium |
| Bolívia        | La Paz, Santa Cruz de La Sierra |
| Bosznia-Hercegovina | Szarajevó |
| Bulgária       | <p>Balchik, Blagoevgrad, Burgas, Dobrich, Gabrovo, Haskovo, Kardzhali, Lovech, Nessebar, Pazardzhik, Pernik, Pleven, Plovdiv, Ruse, Shumen, Szliven, Stara Zagora, Vratsa, Jambol, Várna, Veliko, Szófia</P> |
| Horvátország | Crikvenica, Dubrovnik, Rijeka, Slovanski Brod, Zágráb |
| Ciprus | Larnaca, Limassol, Nicosia |
| Cseh Köztársaság | Brno, Jablonec, Karlovy Vary, Liberec, Ostrava, Prága |
| Dánia   | Dánia |
| Észtország   | Észtország |
| Finnország   | Hämeenlinna, Helsinki, Joensuu, Jyväskylä, kaja, Kouvola-Kotka, Kuopio, Lappeenranta, Mikkeli, Oulu, Pori, Rovaniemi, Seinäjoki, Tampere, Turku, Vaasa|
| Franciaország    | <p>Amberieu-en-Bugey, Amiens, Angers, Annecy, Annonay, Arras, Aubenas, Bayonne, Besançon, Blois, Bordeaux, Boulogne sur Mer, Brest, Briançon, Cannes, Châlons-en-Champagne, Chartres, Clermont-Ferrand, Colmar, Côte d ', Dax, Dijon, Grenoble, Haguenau, La Rochelle, Le Mans, Lens, Lille, Lorient, Lyon, Mac, Marseille & Provence, Metz, Millau, Mont-de-Marsan, Montpellier, Mulhouse, Nancy, Nantes, Nizza, Nice Côte d ', Nimes, Normandia, Nyons, Párizs, Poitiers, Privas, Quimper, Rennes, Saint Malo, Saint-Étienne, Saint-Nazaire, Saintes, Sarrebourg, Sete, Strasbourg, Tarbes, Toulouse, Tours</P> |
| + Francia Guyana | Cayenne |
| + Új-Kaledónia | Nouméa  |
| Grúzia | Tbiliszi |
| Németország | <p>Berlin, Brandenburg, Bréma & Niedersachsen, Köln, Eisenach, Frankfurt, Hamburg, Karlsruhe, Mainz, München-München, Rhein-Neckar régió, Rhein-Ruhr régió, Stuttgart, Titisee-Neustadt, Ulm</P> |
| Görögország | <p>Agrinio, Aigio, Athén, Arta, Amorgos, Chania, Korfu, Chios Kos, Heraklion, Ioannina, Kavala, Kalamata, Khios, Komotini, Kos, Larissa, Meganisi, Milos, Mykonos, Patra, Rethimno, Rhodes, Santorini, Serre, Syros, Tinos, Thesszaloniki, Veria, Volos, Xanthi </P> |
| Magyarország | Budapest, Győr, Miskolc, nagyvárosi megye, pesc, Szeged, Székesfehérvár |
| Izland | Ísland – Izland * |
| Írország | Írország |
| Olaszország   | <p>Agrigento, Alessandria, Ancona, Bari, Bologna-Bologne, Cagliari – Szardínia, Campobasso, Catania e Messina, Cosenza, Crema, Cremona, Crotone, Cuneo, Firenze-Firenze, Foggia, Genova-Genova, Iglesias, La Spezia, Lecce, Matera, Milano-Milánó, Nápoly-Nápoly, Padova, Palermo, Parma, Perugia, Pescara, Pisa, Potenza, Roma-Róma, Siena e Grosseto, Siracusa-Siracusa, Taranto, Torino-Torinó, Trento, Trieszt, Udine, Venezia-Róma </p> |
| Lettország | Rīga |
| Liechtenstein | Liechtenstein |
| Litvánia | Druskininkai, Kauno, Klaipėda, Panevėžys, Vilnius |
| Luxemburg | Luxemburg |
| Moldova | Chisinau |
| Montenegró | Podgorica |
| Hollandia | Hollandia |
| Norvégia | Norvégia |
| Lengyelország | <p>Wrocław, Białystok, Bydgoszcz, Elbląg, Elk, Gorzow, Kętrzyna, Krakkó, Leszno, Lodz, Lublin, Mrągowo, Olsztyn, Poznan, Rzeszów, Sanok, Starachowice, Świonujście, Szczecin, Tricity, Varsó, Wodzisław Śląski, Wrocław, Zakopane</p> |
| Portugália | Bragança, Coimbra, Funchal, Leiria, Lisboa, Portimao, Porto|
| Málta | Málta |
| Románia | <p>Alba Iulia, Arad, Beszterce, Brăila, Brassó, Bukarest, Buzau, Cluj Napoca, Constanța, Craiova, Deva, Focșani, Galati, Jászvásár, Csíkszereda, Nagyvárad, Piatra Neamt, Pitești, Ploieşti, Reșița, Szatmárnémeti, Nagyszeben, Suceava, Marosvásárhely, Temesvár, Tulcea, Zalau</p> | 
| Oroszország  | Kaliningrad, Rostov-on-Don, Volgográd, Jekatyerinburg, Kazan, Kirov, Krasnodar, Moszkva, Makhachkala, Nyizsnij Novgorod, Novoszibirszk, Noyabrsk, Omszki, Oryol, Perm, Szentpétervár, Pyatigorsk, Tverri, Tomsk |
| Szerbia  | Belgrád, Kragujevac, NIS, Novi Sad, Valjevo, Szabadka | 
| Szlovákia | Banská Bystrica, Pozsony, Kosice, Presov, Prievidza, Ruzomberok a Liptovsky Mikulas, Stará ľubovňa, Trencin |
| Szlovénia | Koper, Ljubljana |
| Spanyolország    | <p>ALBACETE, Corunna, Alicante, Almería, Asturias, Avila, Badajoz, Cádizi öböl, Barcelona, Bilbao, Burgos, Csomor, Campo de Gibraltár, Castellon de La Plant, Ceuta, Ciudad Real, Cordoba, Cuenca, El Hierro, Ferrol, Fuerteventura, Gran Canaria, Granada, Huelva, Huesca, Ibiza, Jaén-Úbeda, La Gomera, La Palma, Lanzarote, León, Lleida, Logroño, Lugo, Madrid, Malaga, Mallorca-Mallorca, Melilla, Menorca, Merida, Murcia, Ourense, Palencia, Pamplona, a San Sebastian , Santander, Santiago de Compostela, Segovia, Sevilla, Soria, Tarragona-Reus, Tenerife, Toledo, Valencia, Valladolid, Vigo, Vitoria-Gasteiz, Zaragoza-zaragozai</p> |
| Svédország | Goteborg/Göteborg/Jönköping, Malmö kommun-Malmö, Norrköping och Linköping, Stockholm, Sundsvall |
| Svájc | Basel, Genf, Yverdon-Les-Bains, Zürich | 
| Törökország | Adana-Mersin, Ankara, Antalya, Balıkesir, Bilecik, Bolu, Bursa, Çorum, Denizli, Duzce, Edirne, Elazig, Eskisehir, Isztambul, Izmir-Aydin, Kahramanmaras, Kayseri, Konya, Malatya, Muğla, Samsun, Şanlıurfa, Trabzon |
| Egyesült Királyság | Kelet-Anglia, Kelet-Midlands, London és Dél-Kelet, Észak-Kelet, Észak-Nyugat, Észak-Írország, Skócia, Dél-Nyugat, Wales, West Midlands, Yorkshire |
| Ukrajna | Kharkiv, Zhytomyr, Kijev, Lviv, Chernivtsi |

## <a name="middle-east-and-africa"></a>Közel-Kelet és Afrika

| Ország/régió |  Város (metró) |
|---------|---------|
| Bahrein | Bahrein |
| Burkina Faso | Ouagadougou |
| Kongó | Kinshasa |
| Egyiptom | Cairo    |
| Izrael| Izrael  |
| Kenya | Nairobi  |
| Madagaszkár | Antananarivo |
| Marokkó | Casablanca, Essaouira, Khouribga, Tétouan|
| Katar| Doha|
| Szaúd-Arábia | Thuwal |
| Szenegál | Dakar |
| Dél-afrikai Köztársaság | Fokváros |
| Tunézia | Kairouan |
| Egyesült Arab Emírségek  | Abu Dhabi, Dubaj |


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kérhet továbbítási információkat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Adatátviteli adatkérés](how-to-request-transit-data.md)

Ismerje meg, hogyan kérhet valós idejű adatszolgáltatásokat a mobilitási szolgáltatások (előzetes verzió) használatával:

> [!div class="nextstepaction"]
> [Valós idejű adatkérések igénylése](how-to-request-real-time-data.md)

A Azure Maps mobilitási szolgáltatások (előzetes verzió) API dokumentációjának megismerése

> [!div class="nextstepaction"]
> [A mobilitási szolgáltatások API dokumentációja](/rest/api/maps/mobility)
