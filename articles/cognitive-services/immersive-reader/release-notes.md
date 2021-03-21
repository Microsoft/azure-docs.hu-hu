---
title: A lebilincselő olvasó SDK kibocsátási megjegyzései
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a részletes olvasó JavaScript SDK-val kapcsolatos újdonságokkal.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93133594"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>A lebilincselő olvasó JavaScript SDK kibocsátási megjegyzései

## <a name="version-110"></a>1.1.0-es verzió

Ez a kiadás új funkciókat, biztonsági sebezhetőségi javításokat, hibajavításokat, kód-mintákat és konfigurációs beállításokat tartalmaz.

#### <a name="new-features"></a>Új funkciók

* Felhasználói beállítások mentésének és betöltésének engedélyezése különböző böngészők és eszközök között
* Az alapértelmezett megjelenítési beállítások konfigurálásának engedélyezése
* Lehetőség hozzáadása a fordítási nyelv beállításához, a Word fordításának engedélyezéséhez és a dokumentumok fordításának engedélyezéséhez a magától megjelenő olvasó indításakor
* A hangos olvasás konfigurálásának támogatása a beállítások használatával
* Az első futtatási élmény letiltási képességének hozzáadása
* ImmersiveReaderView hozzáadása a UWP

#### <a name="improvements"></a>Fejlesztései

* Az Android-kód mintájának HTML-kódjának frissítése a legújabb SDK-val való együttműködéshez
* Indítási válasz frissítése a feldolgozott karakterek számának visszaküldéséhez
* Mintakód-minták frissítése a v 1.1.0 használatára
* Ne engedélyezze a launchAsync meghívását, ha már betöltődik
* Érvénytelen tartalom keresése az olyan üzenetek figyelmen kívül hagyásával, amelyekben az adathalmaz nem sztring
* Az ígéretek böngésző általi támogatásának ellenőrzését az IF záradékban lévő ablakba.

#### <a name="fixes"></a>Javítások

* Javítsa ki a dependabot a fonalak eltávolításával. zárolás a gitignore
* A biztonsági rés javítása a mopsz-ról v 3.0.0-re való frissítéssel a gyors útmutatóban – NodeJS Code Sample
* Több biztonsági rést is megjavít a Jest és az írógéppel csomagok frissítésével
* Javítsa ki a biztonsági rést a Microsoft. IdentityModel. clients. ActiveDirectory és a v 5.2.0 frissítésével

<br>

## <a name="version-100"></a>A 1.0.0 verziója

Ez a kiadás a feltörési változásokat, az új funkciókat, a kód mintájának javításait és a hibajavításokat tartalmazza.

#### <a name="breaking-changes"></a>Kompatibilitástörő változások

* Azure AD-jogkivonat és altartomány megkövetelése, valamint a korábbi verziókban használt tokenek érvénytelenítése.
* Állítsa le a CookiePolicy a Letiltva értékre. A felhasználói beállítások megőrzése alapértelmezés szerint le van tiltva. Az olvasó minden alkalommal elindítja az alapértelmezett beállításokat, kivéve, ha a CookiePolicy engedélyezve értékre van állítva.

#### <a name="new-features"></a>Új funkciók

* Támogatás hozzáadása a cookie-k engedélyezéséhez vagy letiltásához
* Androidos Kotlin gyors üzembe helyezési mintájának hozzáadása
* Androidos Java-gyors kezdési kód minta hozzáadása
* Node.js gyors kezdési kód hozzáadása minta

#### <a name="improvements"></a>Fejlesztései

* Node.js speciális README.md frissítése
* Python-kód minta módosítása a speciális verzióról a gyors üzembe helyezéshez
* IOS SWIFT-kód mintájának áthelyezése js/Samples-be
* Kód mintáinak frissítése a v 1.0.0 használatához

#### <a name="fixes"></a>Javítások

* Javítás Node.js speciális kód minta
* Hiányzó fájlok hozzáadása a speciális csharp – több erőforráshoz
* En-us eltávolítása a hiperhivatkozásokból

<br>

## <a name="version-003"></a>0.0.3 verziója

Ez a kiadás új funkciókat tartalmaz, javítja a kód mintáit, a biztonsági sebezhetőségi javításokat és a hibajavításokat.

#### <a name="new-features"></a>Új funkciók

* IOS SWIFT-kód hozzáadása minta
* Több erőforrás használatát bemutató C# speciális kód hozzáadása 
* Támogatás hozzáadása a teljes képernyős váltás funkció letiltásához
* Támogatás hozzáadása a magával ragadó olvasó alkalmazás kilépési gombjának elrejtéséhez
* Vegyen fel egy visszahívási függvényt, amelyet a gazda alkalmazás felhasználhat a magára az olvasóba való kilépés után
* Azure Active Directory hitelesítéshez használandó kód minták frissítése

#### <a name="improvements"></a>Fejlesztései

* C# speciális kódú minta frissítése Word-dokumentum belefoglalásához
* Mintakód-minták frissítése a v 0.0.3 használatához

#### <a name="fixes"></a>Javítások

* A lodash frissítése a 4.17.14 verzióra a biztonsági rések javítása érdekében
* A C# MSAL-könyvtár frissítése a biztonsági sebezhetőség javítására
* A biztonsági rések javítása érdekében frissítse a Mixin-Deep verzió 1.3.2 verzióját.
* Upgrade Jest, webpack és webpack-CLI, amely a set-Value és a Mixin-Deep sebezhető verzióit használta a biztonsági rések kijavításához

<br>

## <a name="version-002"></a>0.0.2 verziója

Ez a kiadás új funkciókat tartalmaz, javítja a kód mintáit, a biztonsági sebezhetőségi javításokat és a hibajavításokat.

#### <a name="new-features"></a>Új funkciók

* Python speciális kód hozzáadása minta
* Java gyors indítási kód minta hozzáadása
* Egyszerű kód hozzáadása minta

#### <a name="improvements"></a>Fejlesztései

* ResourceName átnevezése cogSvcsSubdomain
* A titkokat a kód fölé helyezheti, és környezeti változókat használhat
* Mintakód-minták frissítése a v 0.0.2 használatához

#### <a name="fixes"></a>Javítások

* A részletes olvasó gomb kisegítő lehetőségeinek javítása
* Hibás görgetés javítása
* A biztonsági rések javításához frissítse a kormány csomagját a 4.1.2-es verzióra
* Az SDK-egység tesztek hibáinak javításai
* Javítja a JavaScript Internet Explorer 11 kompatibilitási hibáit
* Az SDK URL-címeinek frissítése

<br>

## <a name="version-001"></a>0.0.1 verziója

A lebilincselő olvasó JavaScript SDK kezdeti kiadása.

* Magával ragadó olvasó JavaScript SDK hozzáadása
* Támogatás hozzáadása a felhasználói felület nyelvének megadásához
* Időkorlát hozzáadása a launchAsync függvény időtúllépési hibájának megállapításához
* Támogatás hozzáadása a lebilincselő olvasó iframe z-indexének megadásához
* Támogatás hozzáadása egy Webnézeti címke iframe helyett való használatához a Chrome-alkalmazásokkal való kompatibilitás érdekében
* SDK-egység tesztek hozzáadása
* Node.js speciális mintakód hozzáadása
* C# speciális kód hozzáadása minta
* C# gyors kezdési kód hozzáadása minta
* Csomag konfigurációjának, fonalának és egyéb Build-fájloknak a hozzáadása
* Git-konfigurációs fájlok hozzáadása
* README.md-fájlok hozzáadása a kód-mintákhoz és az SDK-hoz
* MIT licenc hozzáadása
* Közreműködői utasítások hozzáadása
* Statikus ikon hozzáadása az SVG-eszközökhöz

## <a name="next-steps"></a>Következő lépések

A Modern olvasó használatának első lépései:

* Az [olvasói ügyféloldali függvénytár-referenciájának](./reference.md) elolvasása
* Ismerje [meg az olvasói ügyféloldali kódtárat a githubon](https://github.com/microsoft/immersive-reader-sdk)
