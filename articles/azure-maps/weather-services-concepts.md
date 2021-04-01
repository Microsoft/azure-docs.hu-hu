---
title: Időjárási szolgáltatások (előzetes verzió) – fogalmak a Microsoft Azure Maps-ben
description: Ismerje meg a Microsoft Azure Maps időjárási szolgáltatások (előzetes verzió) alkalmazásával kapcsolatos fogalmakat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 657cb6a86122d267b86e82b6f02eb58e968cddd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904737"
---
# <a name="weather-services-preview-in-azure-maps"></a>Időjárási szolgáltatások (előzetes verzió) Azure Maps

> [!IMPORTANT]
> Azure Maps időjárási szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Azure Maps [időjárási szolgáltatásokra](/rest/api/maps/weather)alkalmazandó fogalmakat ismerteti. Javasoljuk, hogy az időjárási API-k megkezdése előtt végezze el ezt a cikket.

## <a name="unit-types"></a>Egységek típusai

A Weather Service (előzetes verzió) API-k némelyike lehetővé teszi a felhasználó számára, hogy az adatokat metrikus vagy angolszász egységekben adja vissza. Az API-k által visszaadott válaszok közé tartozik a unitType és egy numerikus érték, amely az egység fordításához használható. Az értékek értelmezéséhez tekintse meg az alábbi táblázatot.

|unitType|Leírás         |
|--------|--------------------|
|0       |méter                |
|1       |hüvelyk              |
|2       |mérföld               |
|3       |milliméter          |
|4       |centiméter          |
|5       |mérő               |
|6       |kilométer           |
|7       |kilometersPerHour   |
|8       |csomó               |
|9       |milesPerHour        |
|10      |metersPerSecond     |
|11      |hectoPascals        |
|12      |inchesOfMercury     |
|13      |kilopascalban         |
|14      |millibars           |
|15      |millimetersOfMercury|
|16      |poundsPerSquareInch |
|17      |Celsius             |
|18      |Fahrenheit          |
|19      |Kelvin              |
|20      |százalék             |
|21      |float               |
|22      |egész szám             |


## <a name="weather-icons"></a>Időjárási ikonok

Néhány időjárási szolgáltatás (előzetes verzió) API a válaszban visszaküldi a következőt `iconCode` :. A az `iconCode` ikon definiálásához használt numerikus érték. Ne hivatkozzon közvetlenül az alkalmazásokból származó képekre, az URL-címek és a módosítások is megváltoznak.

| Ikon száma |Ikon| Nap | Éjszaka | Szöveg |
|-------------|:----:|-----|-------|------|
| 1           | :::image type="icon" source="./media/weather-services-concepts/sunny-i.png"::: | Igen |  Nem    | Sunny|
| 2           | :::image type="icon" source="./media/weather-services-concepts/mostly-sunny.png"::: | Igen |  Nem    | Többnyire napos|
| 3           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny.png"::: | Igen |  Nem    | Részben Sunny|
| 4           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds.png"::: | Igen |  Nem    | Időszakos felhők|
| 5           | :::image type="icon" source="./media/weather-services-concepts/hazy-sunshine.png"::: | Igen |  Nem    | Ködös napsütés |
| 6           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy.png"::: | Igen |  Nem    | Többnyire Felhőbeli|
| 7           | :::image type="icon" source="./media/weather-services-concepts/cloudy-i.png"::: | Igen |  Yes   | Felhős |
| 8           | :::image type="icon" source="./media/weather-services-concepts/dreary-overcast.png"::: | Igen |  Yes   | Sivár (borús)|
| 11           | :::image type="icon" source="./media/weather-services-concepts/fog-i.png"::: | Igen |  Yes   | Köd|
| 12           | :::image type="icon" source="./media/weather-services-concepts/showers-i.png"::: | Igen |  Yes   | Zuhanyzók|
| 13           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers.png"::: | Igen |  Nem    | Jórészt Felhőbeli|
| 14           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-showers.png"::: | Igen |  Nem    | Részben napos és zuhanyozós|
| 15           | :::image type="icon" source="./media/weather-services-concepts/tstorms-i.png"::: | Igen |  Yes   | Zivatarok|
| 16           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms.png"::: | Igen |  Nem    | Jórészt Felhőbeli|
| 17           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-tstorms.png"::: | Igen |  Nem    | Részben napos, zivataros|
| 18           | :::image type="icon" source="./media/weather-services-concepts/rain-i.png"::: | Igen |  Yes   | Eső|
| 19           | :::image type="icon" source="./media/weather-services-concepts/flurries-i.png"::: | Igen |  Yes   | Flurries|
| 20           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries.png"::: | Igen |  Nem    | Többnyire Felhőbeli Flurries|
| 21           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-flurries.png"::: | Igen |  Nem    | Részben Sunny a Flurries|
| 22           | :::image type="icon" source="./media/weather-services-concepts/snow-i.png"::: | Igen |  Yes   | Vízisízni|
| 23           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | Igen |  Nem    | Jórészt Felhőbeli|     
| 24           | :::image type="icon" source="./media/weather-services-concepts/ice-i.png"::: | Igen |  Yes   | Ice |
| 25           | :::image type="icon" source="./media/weather-services-concepts/sleet-i.png"::: | Igen |  Yes   | Ónos eső|
| 26           | :::image type="icon" source="./media/weather-services-concepts/freezing-rain.png"::: | Igen |  Yes   | Fagyasztási eső|
| 29           | :::image type="icon" source="./media/weather-services-concepts/rain-snow.png"::: | Igen |  Yes   | Eső és hó|
| 30           | :::image type="icon" source="./media/weather-services-concepts/hot-i.png"::: | Igen |  Yes   | Gyakori|
| 31           | :::image type="icon" source="./media/weather-services-concepts/cold-i.png"::: | Igen |  Yes   | Ritka elérésű|
| 32           | :::image type="icon" source="./media/weather-services-concepts/windy-i.png"::: | Igen |  Yes   | Szeles|
| 33           | :::image type="icon" source="./media/weather-services-concepts/clear-night.png"::: | Nem  |  Igen   | Törlés|
| 34           | :::image type="icon" source="./media/weather-services-concepts/mostly-clear-night.png"::: | Nem  |  Igen   | Többnyire egyértelmű|
| 35           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-night.png"::: | Nem  |  Igen   | Részben felhőbe|
| 36           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds-Night.png"::: | Nem  |  Igen   | Időszakos felhők|
| 37           | :::image type="icon" source="./media/weather-services-concepts/hazymoon-light.png"::: | Nem  |  Igen   | Ködös Holdfény|
| 38           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-night.png"::: | Nem  |  Igen   | Többnyire Felhőbeli|
| 39           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-showers-night.png"::: | Nem  |  Igen   | Záporok részben felhőben|
| 40           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers-night.png"::: | Nem  |  Igen   | Jórészt Felhőbeli|
| 41           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-tstorms-night.png"::: | Nem  |  Igen   | Jórészt Felhőbeli|
| 42           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms-night.png"::: | Nem  |  Igen   | Jórészt Felhőbeli|
| 43           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries-night.png"::: | Nem  |  Igen   | Többnyire Felhőbeli Flurries|
| 44           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | Nem  |  Igen   | Jórészt Felhőbeli|


## <a name="radar-and-satellite-imagery-color-scale"></a>A radar és a műhold képének színskálája

A [Get Map csempe v2 API](/rest/api/maps/renderv2/getmaptilepreview) -felhasználók a legújabb radar-és infravörös műhold-rendszerképeket kérhetik. A radar és a műholdas csempék színeinek értelmezéséhez tekintse meg az alábbi útmutatót.

### <a name="radar-images"></a>Radar-lemezképek

Az alábbi táblázat útmutatást nyújt a radar-képek értelmezéséhez, valamint térképes jelmagyarázat létrehozásához a radar csempék számára.

| Hexadecimális színkód | Színminta | Időjárási feltétel |
|----------------|--------------|-------------------|
| #93c701        | ![Az esőt jelző szín](./media/weather-services-concepts/color-93c701.png) | Rain-Light |
| #ffd701        | ![Az enyhe csapadék színe](./media/weather-services-concepts/color-ffd701.png) | Rain-Moderate |
| #f05514        | ![Az eső-nehéz szín.](./media/weather-services-concepts/color-f05514.png) | Rain-Heavy |
| #dc250e        | ![Eső – súlyos szín](./media/weather-services-concepts/color-dc250e.png) | Rain-Severe |
| #9ec8f2        | ![A Snow-Light színének színe](./media/weather-services-concepts/color-9ec8f2.png) | Snow-Light |
| #2a8fdb        | ![A Snow-mérsékelt szín.](./media/weather-services-concepts/color-2a8fdb.png) | Snow-Moderate |
| #144bed        | ![A Snow-Heavy szín színe](./media/weather-services-concepts/color-144bed.png) | Snow-Heavy |
| #020096        | ![A Snow-súlyos szín.](./media/weather-services-concepts/color-020096.png) | Snow-Severe |
| #e6a5c8        | ![A jég színét jelző szín](./media/weather-services-concepts/color-e6a5c8.png) | Ice-Light |
| #d24fa0        | ![A jég moderálásának színe.](./media/weather-services-concepts/color-d24fa0.png) | Ice-Moderate |
| #b71691        | ![A jég súlyos színe.](./media/weather-services-concepts/color-b71691.png) | Ice-Severe |
| #7a1570        | ![A jég nagy színe.](./media/weather-services-concepts/color-7a1570.png) | Ice-Heavy |
| #c196e6        | ![A mix-Light szín](./media/weather-services-concepts/color-c196e6.png) | Mix-Light |
| #ae6ee6        | ![A mix-mérsékelt szín.](./media/weather-services-concepts/color-ae6ee6.png) | Mix-Moderate |
| #8a32d7        | ![A mix-Heavy szín](./media/weather-services-concepts/color-8a32d7.png) | Mix-Heavy |
| #6500ba        | ![A mix-súlyos szín.](./media/weather-services-concepts/color-6500ba.png) | Mix-Severe |

Alább láthatók a radar csempék részletes színpalettája hexadecimális színkódokkal és dBZ értékekkel. a dBZ a csapadék intenzitását jelöli az időjárási radarban. 

| **ESŐ**             | **JÉG**              | **VÍZISÍZNI**              | **VEGYES**             |
|----------------------|----------------------|-----------------------|-----------------------|
| **dBZ** **(szín)**  | **dBZ** **(szín)**  | **dBZ** **(szín)**   | **dBZ** **(szín)**   |
| 1,25 (#93C701) | 1,25 (#E6A5C8) | 1,25 (#9EC8F2)  | 1,25 (#C196E6) |
| 2,5 (#92C201) | 2,5 (#E6A2C6) | 2,5 (#98C5F0)  | 2,5 (#BF92E6) |
| 3,75 (#92BE01) | 3,75 (#E69FC5) | 3,75 (#93C3EF)  | 3,75 (#BD8EE6) |
| 5 (#92BA02) | 5 (#E69DC4) | 5 (#8DC1EE)  | 5 (#BB8BE6) |
| 6,25 (#92B502) | 6,25 (#E69AC2) | 6,25 (#88BFEC)  | 6,25 (#BA87E6) |
| 6,75 (#92B403) | 7,5 (#E697C1)  | 7,5 (#82BDEB)   | 7,5 (#B883E6) |
| 8 (#80AD02) | 8,75 (#E695C0) | 8,75 (#7DBAEA)  | 8,75 (#B680E6) |
| 9,25 (#6FA602) | 10 (#E692BE) | 10 (#77B8E8)  | 10 (#B47CE6) |
| 10,5 (#5EA002) | 11,25 (#E68FBD) | 11,25 (#72B6E7)  | 11,25 (#B378E6) |
| 11,75 (#4D9902) | 12,5 (#E68DBC) | 12,5 (#6CB4E6)  | 12,5 (#B175E6) |
| 12,25 (#479702) | 13,75 (#E68ABA) | 13,75 (#67B2E5)  | 13,75 (#AF71E6) |
| 13,5 (#3D9202) | 15 (#E687B9) | 15 (#61AEE4)  | 15 (#AE6EE6) |
| 14,75 (#338D02) | 16,25 (#E685B8) | 16,25 (#5BABE3)  | 16,25 (#AB6AE4) |
| 16 (#298802)  | 17,5 (#E682B6) | 17,5 (#56A8E2)  | 17,5 (#A967E3) |
| 17,25 (#1F8302) | 18,75 (#E67FB5) | 18,75 (#50A5E1)  | 18,75 (#A764E2) |
| 17,75 (#1B8103) | 20 (#E67DB4) | 20 (#4BA2E0)  | 20 (#A560E1) |
| 19 (#187102) | 21,25 (#E275B0) | 21,25 (#459EDF) | 21,25 (#A35DE0) |
| 20,25 (#166102) | 22,5 (#DF6DAD) | 22,5 (#409BDE)  | 22,5 (#A15ADF) |
| 20,75 (#165B02) | 23,75 (#DC66AA) | 23,75 (#3A98DD)  | 23,75 (#9F56DE) |
| 22 (#135001) | 25 (#D85EA6) | 25 (#3595DC)  | 25 (#9D53DD) |
| 23,25 (#114501) | 26,25 (#D556A3) | 26,25 (#2F92DB)  | 26,25 (#9B50DC) |
| 24,5 (#0F3A01) | 27,5 (#D24FA0) | 27,5 (#2A8FDB)  | 27,5 (#9648DA) |
| 25,75 (#124C01) | 28,75 (#CE479E) | 28,75 (#2581DE)  | 28,75 (#9241D9) |
| 27 (#114401) | 30 (#CB409C) | 30 (#2173E2)  | 30 (#8E39D8) |
| 28,25 (#0F3D01) | 31,25 (#C7399A) | 31,25 (#1C66E5)  | 31,25 (#8A32D7) |
| 28,75 (#0F3A01) | 32,5 (#C43298) | 32,5 (#1858E9)  | 32,5 (#862ED2) |
| 30 (#375401) | 33,75 (#C12B96) | 33,75 (#144BED)  | 33,75 (#832BCE) |
| 31,25 (#5F6E01) | 35 (#BD2494) | 35 (#1348EA)  | 35 (#7F28C9) |
| 32,5 (#878801) | 36,25 (#BA1D92) | 36,25 (#1246E7)  | 36,25 (#7C25C5) |
| 33,75 (#AFA201) | 37,5 (#B71691) | 37,5 (#1144E4)  | 37,5 (#7822C1) |
| 35 (#D7BC01) | 38,75 (#B51690) | 38,75 (#1142E1)  | 38,75 (#751FBC) |
| 36,25 (#FFD701) | 40 (#B3168F) | 40 (#1040DE)  | 40 (#711CB8) |
| 37,5 (#FEB805) | 41,25 (#B1168E) | 41,25 (#0F3EDB)  | 41,25 (#6E19B4) |
| 38,75 (#FCAB06) | 42,5 (#AF168D) | 42,5 (#0F3CD8)  | 42,5 (#6D18B4) |
| 40 (#FA9E07) | 43,75 (#AD168C) | 43,75 (#0E3AD5)  | 43,75 (#6D17B4) |
| 41,25 (#F89209) | 45 (#AB168B) | 45 (#0D38D2)  | 45 (#6D16B4) |
| 42,5 (#F05514) | 46,25 (#A9168A) | 46,25 (#0C36CF)  | 46,25 (#6C15B4) |
| 43,75 (#E74111) | 47,5 (#A81689) | 47,5 (#0C34CC)  | 47,5 (#6C14B5) |
| 45 (#DF2D0F) | 48,75 (#A61688) | 48,75 (#0B32C9)  | 48,75 (#6C13B5) |
| 45,5 (#DC250E) | 50 (#A41687) | 50 (#0A30C6)  | 50 (#6B12B5) |
| 46,75 (#D21C0C) | 51,25 (#A21686) | 51,25 (#0A2EC4)  | 51,25 (#6B11B5) |
| 48 (#C9140A) | 52,5 (#A01685) | 52,5 (#092BC1)  | 52,5 (#6B10B6) |
| 49,25 (#BF0C09) | 53,75 (#9E1684) | 53,75 (#0929BF)  | 53,75 (#6A0FB6) |
| 50 (#BA0808) | 55 (#9C1683) | 55 (#0826BC)  | 55 (#6A0EB6) |
| 56,25 (#6f031b) | 56,25 (#9B1682) | 56,25 (#0824BA)  | 56,25 (#6A0DB6) |
| 57,5 (#9f0143) | 57,5 (#981580) | 57,5 (#0721B7)  | 57,5 (#690CB6) |
| 58,75 (#c10060) | 58,75 (#96157F) | 58,75 (#071FB5)  | 58,75 (#690CB7) |
| 60 (#e70086) | 60 (#94157E) | 60 (#071DB3)  | 60 (#690BB7) |
| 61,25 (#e205a0) | 61,25 (#92157D) | 61,25 (#061AB0)  | 61,25 (#680AB7) |
| 62,5 (#cc09ac) | 62,5 (#90157C) | 62,5 (#0618AE)  | 62,5 (#6809B7) |
| 63,75 (#b50eb7) | 63,75 (#8D157A) | 63,75 (#0515AB)  | 63,75 (#6808B8) |
| 65 (#9315c8) | 65 (#8B1579) | 65 (#0513A9)  | 65 (#6707B8) |
| 66,25 (#8f21cc) | 66,25 (#891578) | 66,25 (#0410A6)  | 66,25 (#6706B8) |
| 67,5 (#983acb) | 67,5 (#871577) | 67,5 (#040EA4)  | 67,5 (#6705B8) |
| 68,75 (#9d49cb) | 68,75 (#851576) | 68,75 (#040CA2)  | 68,75 (#6604B8) |
| 70 (#a661ca) | 70 (#821574) | 70 (#03099F)  | 70 (#6603B9) |
| 71,25 (#ad72c9) | 71,25 (#801573) | 71,25 (#03079D)  | 71,25 (#6602B9) |
| 72,5 (#b78bc6) | 72,5 (#7E1572) | 72,5 (#02049A)  | 72,5 (#6501B9) |
| 73,75 (#bf9bc4) | 73,75 (#7C1571) | 73,75 (#020298)  | 73,75 (#6500B9) |
| 75 (#c9b5c2) | 75 (#7A1570) | 75 (#020096)  | 75 (#6500BA) |



### <a name="satellite-images"></a>Szatellit lemezképek

Az alábbi táblázat útmutatást nyújt az infravörös műholdképek megjelenítéséhez a felhők által a hőmérsékletük alapján, valamint a csempék térképes jelmagyarázatának létrehozásához. 

| Hexadecimális színkód | Színminta | Felhő hőmérséklete |
|----------------|--------------|-------------------|
| #b5b5b5        | ![#B5b5b5 színének csempe](./media/weather-services-concepts/color-b5b5b5.png) | Temperature-Low | 
| #d24fa0        | ![#D24fa0 színének csempe](./media/weather-services-concepts/color-d24fa0.png) |  |
| #8a32d7        | ![#8a32d7 színének csempe](./media/weather-services-concepts/color-8a32d7.png) |  |
| #144bed        | ![#144bed színének csempe](./media/weather-services-concepts/color-144bed.png) |  |
| #479702        | ![#479702 színének csempe](./media/weather-services-concepts/color-479702.png) |  |
| #72b403        | ![#72b403 színének csempe](./media/weather-services-concepts/color-72b403.png) |  |
| #93c701        | ![#93c701 színének csempe](./media/weather-services-concepts/color-93c701.png) |  |
| #ffd701        | ![#Ffd701 színének csempe](./media/weather-services-concepts/color-ffd701.png) |  |
| #f05514        | ![#F05514 színének csempe](./media/weather-services-concepts/color-f05514.png) |  |
| #dc250e        | ![#Dc250e színének csempe](./media/weather-services-concepts/color-dc250e.png) |  |
| #ba0808        | ![#Ba0808 színének csempe](./media/weather-services-concepts/color-ba0808.png) |  |
| #1f1f1f        | ![#1f1f1f színének csempe](./media/weather-services-concepts/color-1f1f1f.png) | Temperature-High |


Alább látható az infravörös műholdképek részletes palettája.

|**Temp (K)**|**Hexadecimális színkód**|
|--------|--------------|
|198     |#fe050505     |
|198,43  |#fe120505     |
|198,87  |#fc1f0505     |
|199,3   |#fc2c0606     |
|199,74  |#fa390606     |
|200,17  |#fa460606     |
|200,61  |#f8530606     |
|201,04  |#f8600707     |
|201,48  |#f66c0707     |
|201,91  |#f6790707     |
|202,35  |#f4860707     |
|202,78  |#f4930707     |
|203,22  |#f2a00808     |
|203,65  |#f2ad0808     |
|204,09  |#f0ba0808     |
|204,52  |#f0bd0a09     |
|204,96  |#eec00d09     |
|205,39  |#eec30f0a     |
|205,83  |#ecc5120a     |
|206,26  |#ecc8140b     |
|206,7   |#eacb170b     |
|207,13  |#eace190c     |
|207,57  |#e8d11b0c     |
|208     |#e8d41e0d     |
|208,43  |#e6d6200d     |
|208,87  |#e6d9230e     |
|209,3   |#e4dc250e     |
|209,74  |#e4df2c0f     |
|210,17  |#e2e23310     |
|210,61  |#e2e53a11     |
|211,04  |#e0e73a11     |
|211,48  |#e0ea4712     |
|211,91  |#deed4e13     |
|212,35  |#def05514     |
|212,78  |#dcf15e13     |
|213,22  |#dcf26811     |
|213,65  |#daf37110     |
|214,09  |#daf47a0f     |
|214,52  |#d8f5830d     |
|214,96  |#d8f68d0c     |
|215,39  |#d6f8960b     |
|215,83  |#d6f99f09     |
|216,26  |#d4faa908     |
|216,7   |#d4fbb206     |
|217,13  |#d2fcbb05     |
|217,57  |#d2fdc404     |
|218     |#d0fece02     |
|218,43  |#d0ffd701     |
|218,87  |#cef8d601     |
|219,3   |#cef2d501     |
|219,74  |#ccebd401     |
|220,17  |#cce4d301     |
|220,61  |#caddd201     |
|221,04  |#cad7d101     |
|221,48  |#c8d0d001     |
|221,91  |#c8c9cf01     |
|222,35  |#c6c2ce01     |
|222,78  |#c6bccd01     |
|223,22  |#c4b5cc01     |
|223,65  |#c4aecb01     |
|224,09  |#c2a7ca01     |
|224,52  |#c2a1c901     |
|224,96  |#c09ac801     |
|225,39  |#c093c701     |
|225,83  |#be90c501     |
|226,26  |#be8ec401     |
|226,7   |#bc8bc202     |
|227,13  |#bc88c102     |
|227,57  |#ba85bf02     |
|228     |#ba83be02     |
|228,43  |#b880bc02     |
|228,87  |#b87dba02     |
|229,3   |#b678b703     |
|229,74  |#b675b603     |
|230,17  |#b472b403     |
|230,61  |#b46eb203     |
|231,04  |#b26bb203     |
|231,48  |#b267ad03     |
|231,97  |#b064aa03     |
|232,35  |#b060a803     |
|232,78  |#ae5da603     |
|233,22  |#ae59a302     |
|233,65  |#ac55a102     |
|234,09  |#ac529e02     |
|234,52  |#aa4e9c02     |
|234,96  |#aa4b9902     |
|235,39  |#a8479702     |
|235,83  |#a845940b     |
|236,26  |#a6439115     |
|236,7   |#a6418e1e     |
|237,13  |#a43f8b28     |
|237,57  |#a43d8831     |
|238     |#a23b853a     |
|238,43  |#a2398244     |
|238,87  |#a0377f4d     |
|239,3   |#a0357c57     |
|239,74  |#9e337960     |
|240,17  |#9e317669     |
|240,61  |#9c2f7373     |
|241,04  |#9c2c6f7c     |
|241,48  |#9a2a6c86     |
|241,91  |#9a28698f     |
|242,35  |#98266698     |
|242,78  |#982463a2     |
|243,22  |#962260ab     |
|243,65  |#96205db5     |
|244,09  |#941e5abe     |
|244,52  |#941c57c7     |
|244,85  |#921a54d1     |
|245,39  |#921851da     |
|245,83  |#90164ee4     |
|246,26  |#90144bed     |
|246,7   |#8e2148eb     |
|247,13  |#8e2e45e8     |
|247,57  |#8c3b43e6     |
|248     |#8c4840e3     |
|248,43  |#8a563de1     |
|248,87  |#8a633ade     |
|249,3   |#887038dc     |
|249,74  |#887d35d9     |
|250,17  |#868a32d7     |
|250,61  |#869034d2     |
|251,04  |#849637ce     |
|251,48  |#849c39c9     |
|251,91  |#82a23cc5     |
|252,35  |#82a83ec0     |
|252,78  |#80ae41bc     |
|253,22  |#80b443b7     |
|253,65  |#7eba45b2     |
|253,09  |#7ec048ae     |
|254,52  |#7cc64aa9     |
|254,96  |#7ccc4da5     |
|255,39  |#7ad24fa0     |
|255,83  |#7ad85fac     |
|256,26  |#78dd6eb8     |
|256,7   |#78e37ec4     |
|257,13  |#76e98ed0     |
|257,57  |#76ee9ddb     |
|258     |#74f4ade7     |
|258,43  |#74f9bcf3     |
|258,87  |#72ffccff     |
|259,3   |#71ffffff     |
|259,74  |#71fcfcfc     |
|260,17  |#6ff6f6f6     |
|260,61  |#6ff6f6f6     |
|261,04  |#6df3f3f3     |
|261,48  |#6df3f3f3     |
|261,91  |#6bededed     |
|262,35  |#6bededed     |
|262,78  |#69e7e7e7     |
|263,22  |#69e7e7e7     |
|263,65  |#67e1e1e1     |
|264,09  |#67e1e1e1     |
|264,52  |#65dedede     |
|264,96  |#65dedede     |
|265,39  |#63d8d8d8     |
|265,83  |#63d8d8d8     |
|265,84  |#61d1d1d1     |
|266,26  |#61d1d1d1     |
|267,13  |#5fcecece     |
|267,57  |#5fcecece     |
|268     |#5dc8c8c8     |
|268,43  |#5dc8c8c8     |
|268,87  |#5bc2c2c2     |
|269,3   |#5bc2c2c2     |
|269,74  |#59bcbcbc     |
|270,17  |#59bcbcbc     |
|270,61  |#57b9b9b9     |
|271,04  |#57b9b9b9     |
|271,48  |#55b3b3b3     |
|271,91  |#55b3b3b3     |
|272,35  |#53adadad     |
|272,78  |#53adadad     |
|273,22  |#51aaaaaa     |
|273,65  |#51aaaaaa     |
|274,09  |#4fa4a4a4     |
|274,52  |#4fa4a4a4     |
|274,96  |#4d9e9e9e     |
|275,39  |#4d9e9e9e     |
|275,83  |#4b989898     |
|276,26  |#4b989898     |
|276,7   |#49959595     |
|277,13  |#49959595     |
|277,57  |#478f8f8f     |
|278     |#478f8f8f     |
|278,43  |#45898989     |
|278,87  |#45898989     |
|279,2   |#43868686     |
|279,74  |#43868686     |
|280,17  |#417f7f7f     |
|280,61  |#417f7f7f     |
|281,04  |#3f797979     |
|281,48  |#3f797979     |
|281,91  |#3d737373     |
|282,35  |#3d737373     |
|282,78  |#3b707070     |
|283,22  |#3b707070     |
|283,65  |#396a6a6a     |
|284,09  |#396a6a6a     |
|284,52  |#37646464     |
|284,96  |#37646464     |
|285,39  |#35616161     |
|285,83  |#35616161     |
|286,26  |#335b5b5b     |
|286,7   |#335b5b5b     |
|287,13  |#31555555     |
|287,57  |#31555555     |
|288     |#2f4f4f4f     |
|288,43  |#2f4f4f4f     |
|288,87  |#2d4c4c4c     |
|289,3   |#2d4c4c4c     |
|289,74  |#2b464646     |
|290,17  |#2b464646     |
|290,61  |#29404040     |
|291,04  |#29404040     |
|291,48  |#273d3d3d     |
|291,91  |#273d3d3d     |
|292,35  |#25373737     |
|292,78  |#25373737     |
|293,22  |#23313131     |
|293,65  |#23313131     |
|294,09  |#212a2a2a     |
|294,52  |#212a2a2a     |
|294,96  |#1f272727     |
|295,39  |#1f272727     |
|295,83  |#1d212121     |
|296,26  |#1d212121     |
|296,7   |#1b1b1b1b     |
|297,13  |#1b1b1b1b     |
|297,57  |#19181818     |
|298     |#19181818     |
|298,43  |#17121212     |
|298,87  |#17121212     |
|299,3   |#150c0c0c     |
|299,74  |#150c0c0c     |
|300,17  |#13060606     |
|300,61  |#13060606     |
|301,04  |#11000000     |
|301,48  |#11000000     |
|301,91  |#0f797979     |
|302,35  |#0f797979     |
|302,78  |#0d737373     |
|303,22  |#0d737373     |
|303,65  |#0b6d6d6d     |
|304,09  |#0b6d6d6d     |
|304,52  |#09676767     |
|304,92  |#09676767     |
|305,39  |#07616161     |
|305,83  |#07616161     |
|306,26  |#055b5b5b     |
|306,7   |#055b5b5b     |
|307,13  |#02555555     |
|307,57  |#02555555     |
|308     |#00525252     |
|308     |#00525252     |

## <a name="index-ids-and-index-groups-ids"></a>Index-azonosítók és indexelt csoportok azonosítói

A napi indexek [beolvasása](/rest/api/maps/weather) lehetővé teszi, hogy a felhasználók a visszaadott eredményeket meghatározott indexekre vagy index csoportokra korlátozzák.

Az alábbi táblázat a rendelkezésre álló index-azonosítókat, azok nevét és a tartományra mutató hivatkozást tartalmazza. A táblázat alatti táblázat a különböző indexelő csoportok listáját tartalmazza.

  Index neve |  ID (Azonosító)  | Értéktartomány
  -------------------------- |---|-----
  Ízületi fájdalom             |21 | [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Asztma                     |23|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Strand & készlet               |10| [Gyenge – kiváló 1](#poor-excellent-1)
  Kerékpározás                  |4| [Gyenge – kiváló 1](#poor-excellent-1)
  Gyakori nátha                |25|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Komposztálás                 |38| [Gyenge – kiváló 1](#poor-excellent-1)
  Építőipar               |14| [Gyenge – kiváló 1](#poor-excellent-1)
  COPD                       |44|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  A kutya kényelme        |43| [Gyenge – kiváló 1](#poor-excellent-1)
  Vezetés                    |40|  [Gyenge – kiváló 2](#poor-excellent-2)
  Por & harag              |18| [Alacsony Extreme 1](#low-extreme-1)
  Mező készültsége            |32| [Gyenge – kiváló 1](#poor-excellent-1)
  Halászat                    |13| [Gyenge – kiváló 1](#poor-excellent-1)
  Repülési késések              |-3|  [Nagyon Unlikely-Very valószínűleg 2](#very-unlikely-very-likely-2)
  Influenza                        |26|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Repülési utazási index        |31| [Kiváló – gyenge](#excellent-poor)
  Tüzelőanyag-takarékosság               |37| [Gyenge – kiváló 1](#poor-excellent-1)
  Golf időjárása               |5| [Gyenge – kiváló 1](#poor-excellent-1)
  Növekvő fű              |33| [Gyenge – kiváló 1](#poor-excellent-1)
  Haj sima                 |42| [Nem valószínű – vészhelyzet](#unlikely-emergency)
  Kifogástalan Heart fitness      |16| [Gyenge – kiváló 1](#poor-excellent-1)
  Túrázás                     |3| [Gyenge – kiváló 1](#poor-excellent-1)
  Home energiahatékonyság     |36| [Gyenge – kiváló 1](#poor-excellent-1)
  Veszélyforrás-keresés                    | 20| [Gyenge – kiváló 1](#poor-excellent-1)
  Beltéri tevékenység            | -2| [Gyenge – kiváló 1](#poor-excellent-1)
  Jogging                    |2| [Gyenge – kiváló 1](#poor-excellent-1)
  Kite repül                |9| [Gyenge – kiváló 1](#poor-excellent-1)
  Gyepes kaszálás                |28| [Gyenge – kiváló 1](#poor-excellent-1)
  Migrénes fejfájás          |27|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Délelőtti iskolabusz         |35| [Gyenge – kiváló 1](#poor-excellent-1)
  Szúnyog tevékenység          |17|[Alacsony Extreme 1](#low-extreme-1)
  Kültéri tevékenység           |29| [Gyenge – kiváló 1](#poor-excellent-1)
  Kültéri barbecue           |24| [Gyenge – kiváló 1](#poor-excellent-1)
  Kültéri koncert            |8| [Gyenge – kiváló 1](#poor-excellent-1)
  Futó                    |1|  [Gyenge – kiváló 1](#poor-excellent-1)
  Tenisz                     |6| [Gyenge – kiváló 1](#poor-excellent-1)
  Szomjúság                     |41| [Alacsony Extreme 2](#low-extreme-2)
  Vitorlázás                    |11| [Gyenge – kiváló 1](#poor-excellent-1)
  Bevásárló                   |39| [Gyenge – kiváló 1](#poor-excellent-1)
  Sinus-fejfájás             |30|  [Hasznos – szélsőséges kockázat](#beneficial-at-extreme-risk)
  Gördeszka              | 7| [Gyenge – kiváló 1](#poor-excellent-1)
  Sí Időjárás                | 15| [Gyenge – kiváló 1](#poor-excellent-1)
  Hó napjai                  | 19| [Nagyon Unlikely-Very valószínűleg](#very-unlikely-very-likely)
  Talaj nedvességtartalma              | 34| [Gyenge – kiváló 1](#poor-excellent-1)
  Stargazing                 | 12| [Gyenge – kiváló 1](#poor-excellent-1)

Alább látható az elérhető indexelt csoportok (indexGroupId) listája:

  ID (Azonosító)   | Csoportnév | Indexek ebben a csoportban |
  -------- | ------------------|------
  1       |Mind | Mind
  2       |Ízületi fájdalmak | Ízületi fájdalom (21) </br> Migrénes fejfájás (27) </br> Sinus-fejfájás (30)
  3       |Légúti | Asztma (23) </br> Gyakori megfázás (25) </br> Influenza-előrejelzés (26)
  4       |Kertészeti | Mező készültsége (32) </br> Gyepes kaszálás (28) </br> Talaj nedvességtartalma (34)</br>
  5       |Környezeti | Összeállítás (38) </br> Home energiahatékonyság (36) </br> Tüzelőanyag-takarékosság (37)
  6       |Szabadban élő | Kültéri barbecue (24) </br> Szúnyog tevékenység (17)
  7       |Strand és tengeri | Strand & készlet (10) </br> Horgászat (13) </br> Vitorlázás (11)
  8       |Sportoló | Horgászat (13) </br> Vadászat (20) </br> Kültéri tevékenység (29)</br>
  9       |Mezőgazdasági |  Mező készültsége (32) </br>  Talaj nedvességtartalma (34)
  10      |Egészségügy | Ízületi fájdalom (21) </br> Asztma (23) </br> Gyakori megfázás (25) </br> Por & harag (18) </br> Influenza (26) </br> Kifogástalan Heart fitness (16) </br> Migrénes fejfájás (27)
  11      |Szabadtéri | Kültéri barbecue (24) </br> Strand & készlet (10) </br> Biciklis (4) </br> Kültéri koncert (8) </br>  Mező készültsége (32) </br> Horgászat (13) </br> Golf időjárása (5) </br> Túrázás (3) </br> Vadászat (20) </br> Kocogás (2) </br> Kite-repülő (9) </br> Szúnyog tevékenység (17)</br> Gyepes kaszálás (28) </br> Kültéri tevékenység (29) </br> Fut (1) </br> Vitorlázás (11) </br> Gördeszka (7) </br> Sí Időjárás (15) </br>  Talaj nedvességtartalma (34)</br> Stargazing (12) </br> Tenisz (6)
  12      |Sport | Biciklis (4) </br> Golf időjárása (5) </br> Túrázás (3) </br>  Kocogás (2) </br> Fut (1) </br> Gördeszka (7) </br> Sí Időjárás (15) </br>Tenisz (6)
  13      |Kezdőlap | Home energiahatékonyság (36) </br> Tüzelőanyag-takarékosság (37) </br> Beltéri tevékenység (-2)

## <a name="daily-index-range-sets"></a>Napi indexelési tartomány készletei

A napi indexek [beolvasása API](/rest/api/maps/weather) a tartományhoz tartozó értéket és a hozzá tartozó kategórianév nevét adja vissza az egyes index-azonosítók esetében. A tartomány-készletek nem egyeznek az összes indexnél. Az alábbi táblázat az [index-azonosítók és az index-csoportok azonosítói](#index-ids-and-index-groups-ids)között felsorolt támogatott indexek által használt különböző tartomány-készleteket mutatja. A jelen dokumentum [index-azonosítók és tárgymutató-csoportok azonosítók](#index-ids-and-index-groups-ids) szakaszában találhatja meg, hogy mely indexek milyen tartományokat használnak.

### <a name="poor-excellent-1"></a>Poor-Excellent 1

  | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Rossz              |  0 |                2,99
  Elfogadható              |  3  |               4,99
  Jó              |  5  |              6,99
  nagyon jó         |  7  |               8,99
  Kiváló         |  9  |               10

### <a name="poor-excellent-2"></a>Poor-Excellent 2

 | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Rossz           |0              |  3
  Elfogadható           |3,01           |  6
  Jó           |6,01           |  7,5
  nagyon jó      |7,51           |  8,99
  Kiváló      |9              |  10

### <a name="excellent-poor"></a>Excellent-Poor

 | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Kiváló      |     0,00        |    1,00
  nagyon jó        |   1.01          |  3.00
  Jó             |   3,01          |  5,00
  Elfogadható             |   5,01          |  7,00
  Rossz             |   7,01          |  10,00

### <a name="low-extreme-1"></a>Low-Extreme 1

   | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Alacsony                |  0         |        1,99
  Mérsékelt           |  2         |        3,99
  Magas               |  4         |        5,99
  Nagyon magas          |  6         |        7,99
  Extrém            |  8         |        10

### <a name="low-extreme-2"></a>Low-Extreme 2

   | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Alacsony                |  0            |      2,99
  Mérsékelt           |  3            |      4,99
  Magas               |  5            |      6,99
  Nagyon magas          |  7            |      8,99
  Extrém            |  9            |      10

### <a name="very-unlikely-very-likely"></a>Nagyon Unlikely-Very valószínűleg

 | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Nagyon valószínűtlen      | 0     |           1,99
  Nem valószínű           | 2     |           3,99
  Esetleg           | 4     |           5,99
  Valószínűleg             | 6     |           7,99
  Nagyon valószínű        | 8     |           10

### <a name="very-unlikely-very-likely-2"></a>Nagyon Unlikely-Very valószínűleg 2

 | Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Nagyon valószínűtlen      |  0,00     |         1,00
  Nem valószínű           |  1.01     |         3.00
  Esetleg           |  3,01     |         5,00
  Valószínűleg             |  5,01     |         7,00
  Nagyon valószínű        |  7,01     |         10,00

### <a name="unlikely-emergency"></a>Unlikely-Emergency

| Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Nem valószínű         |  0     |          2,99
  Megtekintés            |  3     |          4,99
  Tanácsadás         |  5     |          6,99
  Figyelmeztetés          |  7     |          8,99
  Sürgősségi        |  9     |          10

### <a name="beneficial-at-extreme-risk"></a>Beneficial-At szélsőséges kockázat

| Kategórianév | Kezdési tartomány | Záró tartomány |
  ----------------|--------------|------------
  Előnyös        |    0        |        1,99
  Semleges           |    2        |        3,99
  Veszélyeztetett           |    4        |        5,99
  Magas kockázatú      |    6        |        7,99
  Szélsőséges kockázati   |    8        |        10