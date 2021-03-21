---
title: Honosítási támogatás Microsoft Azure térképekkel
description: Megtudhatja, hogy mely régiókat Azure Maps támogatja olyan szolgáltatásokkal, mint a Maps, a Search, az routing, az időjárási és a forgalmi incidensek. Megtudhatja, hogyan állíthatja be a View paramétert.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904516"
---
# <a name="localization-support-in-azure-maps"></a>Honosítási támogatás Azure Maps

A Azure Maps ország/régió alapján támogatja a különböző nyelveket és nézeteket. Ez a cikk a támogatott nyelveket és nézeteket ismerteti, amelyek segítenek a Azure Maps megvalósításában.


## <a name="azure-maps-supported-languages"></a>Azure Maps támogatott nyelvek

Azure Maps a különböző nyelveken honosítva vannak a szolgáltatásai között. Az alábbi táblázat az egyes szolgáltatásokhoz támogatott nyelvi kódokat tartalmazza.  
  

| ID (Azonosító)         | Name                   |  Maps | Keresés | Útválasztás | Időjárás | Forgalmi incidensek | JS Térkép vezérlőelem |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| AF-ZA      | búr              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arab                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN – BD      | Bangla (Banglades)    |       |       |         |     ✓    |                   |                |
| BN – a      | Bangla (India)         |       |       |         |     ✓    |                   |                |
| BS-BA      | boszniai                 |       |       |         |     ✓    |                   |                |
| EU – ES      | Baszk                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bolgár              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalán                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Kínai (egyszerűsített)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kínai (Hongkong KKT)  |  |   |    |    ZH-HK   |                   |           |
| zh-HanT    | Kínai (Tajvan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Horvát               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Cseh                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dán                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holland (Belgium)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Holland (Hollandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-AU      | Angol (Ausztrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| EN-NZ      | Angol (Új-Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angol (Nagy-Britannia) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angol (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Észt               |       |    ✓   |         |      ✓    |         ✓         |                |
| Fil – PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | finn                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francia                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr – CA      | Francia (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Gallego               |       |    ✓   |         |         |                   |                |
| de-DE      | Német                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Görög                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – a      | gudzsaráti                |       |       |         |     ✓    |                   |                |
| he-IL      | héber                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Magyar              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| a ()      | Izlandi              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonéz             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Olasz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japán               |       |        |         |     ✓    |                   |                |
| KN – a      | kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazak                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreai                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latin-amerikai spanyol |       |    ✓   |         |         |                   |                |
| lv-LV      | Lett                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litván             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Macedón             |       |       |         |     ✓    |                   |                |
| ms-MY      | Maláj (latin betűs)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norvég (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Semleges földelési igazság – hivatalos nyelvek a helyi parancsfájlok minden régiója számára, ha elérhető |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Semleges földelési igazság – latin exonyms. Ha elérhető, a latin szkriptet fogja használni |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Lengyel                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugál (Brazília)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugál (Portugália)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | pandzsábi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Román               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Orosz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Szerb (cirill betűs)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Szerb (latin betűs)        |       |       |         |     SR-Latn    |                   |                |
| sk-SK      | Szlovák             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| SL-SL      | Szlovén              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanyol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanyol (Mexikó)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | svéd                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-be      | Tamil (India)                 |       |       |         |     ✓    |                   |                |
| te-a      | Telugu (India)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Török                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrán               |       |    ✓   |         |     ✓    |                   |                |
| az Ön-PK      | urdu                 |       |       |         |     ✓    |                   |                |
| Uz-Latn-UZ | Üzbég                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnámi             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps támogatott nézetek

> [!Note]
> 2019 augusztus 1-jén adták ki Azure Maps a következő országokban/régiókban:
>  * Argentína
>  * India
>  * Marokkó
>  * Pakisztán
>
> 2019. augusztus 1-től a **View** paraméter meghatározza a visszaadott leképezési tartalmat a fent felsorolt új régiókban/országokban. Azure Maps **View** paraméter (más néven "felhasználói régió paramétere") egy kétbetűs ISO-3166 országkód, amely az adott országhoz/régióhoz tartozó megfelelő térképeket jeleníti meg, amelyek meghatározzák, hogy a geopolitikai módon vitatott tartalom mely készletét adja vissza Azure Maps szolgáltatásokon keresztül, beleértve a térképen megjelenített szegélyeket és címkéket. 

Ügyeljen arra, hogy a **View** paramétert a REST API-k és a szolgáltatások által használt SDK-k számára szükséges módon állítsa be.
  

### <a name="rest-apis"></a>REST API-k
  
Győződjön meg arról, hogy szükség szerint beállította a View paramétert. A View paraméter azt határozza meg, hogy a rendszer mely geopolitikai jogvitás tartalmat adja vissza Azure Maps szolgáltatásokon keresztül. 

Érintett Azure Maps REST-szolgáltatások:
    
 * Térkép lekérése csempe
 * Térkép-rendszerkép lekérése 
 * Keresés – fuzzy
 * Keresési célobjektum beolvasása
 * Keresési célobjektum kategóriájának beolvasása
 * Keresés a közelben
 * Keresési címek beolvasása
 * Felépített keresési címek beolvasása
 * Fordított keresési címek lekérése
 * A keresési címek fordított kereszt utcájának beolvasása
 * Keresés közzététele a geometrián belül
 * Keresési csoport utáni batch
 * Keresési címen fordított köteg közzététele
 * Keresés közzététele az útvonal mentén
 * Keresés a zavaros kötegben

 
### <a name="sdks"></a>SDK-k

Győződjön meg arról, hogy szükség szerint állította be a **View** paramétert, és a web SDK és az Android SDK legújabb verziója van telepítve. Érintett SDK-k:

 * Azure Maps web SDK
 * Azure Maps Android SDK

Alapértelmezés szerint a View paraméter **egyesített** értékre van állítva, még akkor is, ha még nem adta meg a kérésben. Határozza meg a felhasználók helyét. Ezután állítsa be helyesen a **View** paramétert az adott helyen. Másik lehetőségként megadhatja a "View = automatikus" értéket, amely a kérés IP-címe alapján visszaadja a térképi adatmennyiséget.  Azure Mapsban a **View** paramétert a vonatkozó törvényeknek megfelelően kell használni, beleértve azokat az országokat/régiókat, amelyekben a Maps, a images és más adatokat, valamint a harmadik féltől származó, a Azure Maps-on keresztül való hozzáférésre jogosult külső tartalmat is elérhetővé teszi.


A következő táblázat a támogatott nézeteket tartalmazza.

| Nézet         | Description                            |  Maps | Keresés | JS térképkezelés |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Egyesült Arab Emírségek (arab nézet)    |   ✓   |        |     ✓          |
| AR           | Argentína (argentin nézet)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (arab nézet)                 |   ✓   |        |     ✓          |
| IN           | India (indiai nézet)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arab nézet)                    |   ✓   |        |     ✓          |
| JO           | Jordánia (arab nézet)                  |   ✓   |        |     ✓          |
| KW           | Kuvait (arab nézet)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arab nézet)                 |   ✓   |        |     ✓          |
| MA           | Marokkó (marokkói nézet)                |   ✓   |   ✓     |     ✓          |
| OM           | Omán (arab nézet)                    |   ✓   |        |     ✓          |
| PK           | Pakisztán (pakisztáni nézet)              |   ✓   |    ✓    |     ✓          |
| PS           | Palesztin hatóság (arab nézet)    |   ✓   |        |     ✓          |
| QA           | Katar (arab nézet)                   |   ✓   |        |     ✓          |
| SA           | Szaúd-Arábia (arab nézet)            |   ✓   |        |     ✓          |
| SY           | Szíria (arab nézet)                   |   ✓   |        |     ✓          |
| TI           | Jemeni (arab nézet)                   |   ✓   |        |     ✓          |
| Automatikus         | A leképezési adatmennyiséget a kérés IP-címe alapján küldi vissza.|   ✓   |    ✓   |     ✓          |
| Egyesített      | Egyesített nézet (egyéb)                  |   ✓   |   ✓     |     ✓          |
