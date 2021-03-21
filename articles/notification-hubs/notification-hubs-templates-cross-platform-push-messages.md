---
title: Azure Notification Hubs-sablonok
description: Tudnivalók az Azure Notification Hubs sablonjainak használatáról.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100635642"
---
# <a name="notification-hubs-templates"></a>Notification Hubs sablonok

A sablonok lehetővé teszik az ügyfélalkalmazás számára, hogy megadják a fogadni kívánt értesítések pontos formátumát. A sablonok használatával az alkalmazások számos különböző előnnyel járhatnak, többek között az alábbiakkal:

- Platform-agnosztikus háttérrendszer
- Személyre szabott értesítések
- Ügyfél-verzió függetlenség
- Egyszerű honosítás

Ez a szakasz két részletes példát tartalmaz arra vonatkozóan, hogyan használhatók a sablonok a platformok közötti, az összes eszközön a platformon megcélzott, és a szórásos értesítések személyre szabásához.

## <a name="using-templates-cross-platform"></a>Sablonok használata platformfüggetlen platformon

A leküldéses értesítések küldésének szabványos módja az elküldött értesítések küldése, a platform Notification Services (WNS, APNS) egy adott adattartalom. Ha például riasztást szeretne küldeni a APNS, a hasznos adat a következő űrlap JSON-objektuma:

```json
{"aps": {"alert" : "Hello!" }}
```

Ha egy Windows áruházbeli alkalmazáshoz hasonló Toast-üzenetet szeretne küldeni, az XML-tartalom a következő:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

A MPNS (Windows Phone-telefon) és az FCM (Android) platformokhoz hasonló adattartalmakat is létrehozhat.

Ez a követelmény arra kényszeríti az alkalmazási hátteret, hogy az egyes platformokon különböző adattartalmakat hozzon létre, és hatékonyan hozza létre az alkalmazás megjelenítési rétegének egy részéért felelős hátteret. Néhány probléma például a honosítás és a grafikus elrendezés (különösen a Windows áruházbeli alkalmazásokhoz, amelyek a különböző típusú csempék értesítéseit tartalmazzák).

A Notification Hubs sablon funkció lehetővé teszi, hogy az ügyfélalkalmazás olyan speciális regisztrációkat hozzon létre, amelyeket sablon-regisztrációknak neveznek, beleértve a címkék készletét is. A Notification Hubs sablon funkció lehetővé teszi, hogy az ügyfélalkalmazások sablonokkal társítsák az eszközöket, függetlenül attól, hogy a telepítések (előnyben részesített) vagy a regisztrációk használatával működnek. Az előző adattartalomra vonatkozó példák miatt az egyetlen platformtól független információ a tényleges riasztási üzenet (**Hello!**). A sablon az értesítési központ utasításait mutatja be, amely bemutatja, hogyan formázhatja a platformtól független üzenetet az adott ügyfélalkalmazás regisztrálásához. Az előző példában a platform-független üzenet egyetlen tulajdonság: `message = Hello!` .

Az alábbi ábra a folyamatot szemlélteti:

![A sablonok platformok közötti használatának folyamatát bemutató ábra](./media/notification-hubs-templates/notification-hubs-hello.png)

Az iOS-ügyfélalkalmazás regisztrációjának sablonja a következő:

```json
{"aps": {"alert": "$(message)"}}
```

A Windows áruházbeli ügyfélalkalmazás megfelelő sablonja a következő:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Figyelje meg, hogy a tényleges üzenet a kifejezés helyére kerül `$(message)` . Ez a kifejezés arra utasítja az értesítési központot, hogy ha üzenetet küld erre a regisztrációra, hozzon létre egy üzenetet, amely a következőt jeleníti meg, és beszúrja a közös értéket.

Ha a telepítési modellel dolgozik, a "templates" (Sablonok) kulcs több sablon JSON-fájlját tartalmazza. Ha a regisztrációs modellel dolgozik, az ügyfélalkalmazás több regisztrációt is létrehozhat, hogy több sablont lehessen használni; például egy sablon a riasztási üzenetekhez, valamint egy sablon a csempék frissítéseihez. Az ügyfélalkalmazások a natív regisztrációkat (sablon nélküli regisztrációkat) és a sablonok regisztrációját is összekeverik.

Az értesítési központ egy értesítést küld az egyes sablonokhoz anélkül, hogy azok ugyanahhoz az ügyfélalkalmazás ugyanahhoz az alkalmazáshoz tartozzanak. Ez a viselkedés a platformtól független értesítések további értesítésekre való fordítására használható. Például az értesítési központhoz hasonló platform-független üzenet zökkenőmentesen lefordítható egy Toast-riasztásban és egy csempe-frissítésben anélkül, hogy a háttérnek tudniuk kell róla. Egyes platformok (például iOS) több értesítést is összecsuknak ugyanarra az eszközre, ha rövid időn belül elküldik őket.

## <a name="using-templates-for-personalization"></a>Sablonok használata személyre szabáshoz

A sablonok használatának másik előnye, hogy a Notification Hubs használatával végezheti el az értesítések regisztrációjának személyre szabását. Vegyünk például egy olyan időjárási alkalmazást, amely egy adott helyen lévő időjárási feltételekkel jeleníti meg a csempét. A felhasználók a Celsius-vagy Fahrenheit-fok, valamint egy vagy öt napos előrejelzés közül választhatnak. A sablonok használatával minden ügyfélalkalmazás telepítése regisztrálhat a szükséges formátumra (1 napos Celsius, 1 napos Fahrenheit, 5 napos Celsius-fok, 5 napos Fahrenheit), és a háttérrendszer egyetlen üzenetet küld, amely tartalmazza a sablonok kitöltéséhez szükséges összes információt (például egy ötéves, Celsius-és Fahrenheit-fok szerinti előrejelzést).

A Celsius hőmérséklettel rendelkező egynapos előrejelzés sablonja a következő:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Az értesítési központnak küldött üzenet a következő tulajdonságokat tartalmazza:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Ennek a mintának a használatával a háttérrendszer csak egyetlen üzenetet küld anélkül, hogy az alkalmazás felhasználói számára speciális személyre szabott beállításokat kellene tárolnia. A következő ábra ezt a forgatókönyvet szemlélteti:

![Ábra, amely azt mutatja, hogy a háttérrendszer csak egyetlen üzenetet küld az egyes platformoknak.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Sablonok regisztrálása

Ha regisztrálni szeretne a sablonokkal a telepítési modell (előnyben részesített) vagy a regisztrációs modell használatával, tekintse meg a [regisztráció kezelése](notification-hubs-push-notification-registration-management.md)című témakört.

## <a name="template-expression-language"></a>Sablon kifejezésének nyelve

A sablonok XML-vagy JSON-dokumentumok formátumára korlátozódnak. Emellett a kifejezéseket csak adott helyeken helyezheti el; például: csomópont-attribútumok vagy XML-értékek, karakterlánc-tulajdonságértékek a JSON-hoz.

A következő táblázat a sablonokban engedélyezett nyelvet mutatja be:

| Kifejezés       | Leírás |
| ---------------- | --- |
| $ (prop)          | Hivatkozás a megadott nevű Event tulajdonságra. A tulajdonságok neve nem megkülönbözteti a kis-és nagybetűket. Ez a kifejezés a tulajdonság szöveges értékére vagy üres karakterláncba kerül, ha a tulajdonság nincs jelen. |
|$ (prop, n)       | A fentiek szerint azonban a szöveg kifejezetten n karakterből van kivágva, például $ (title, 20) – a title tulajdonság tartalma 20 karakternél. |
| . (prop, n)      | A fentiek szerint azonban a szöveg három ponttal van ellátva, ahogy azt levágja. A kivágott karakterlánc és az utótag teljes mérete nem haladja meg az n karaktert. (title, 20) a "this a title line" (cím) mezőben a következő cím jelenik meg: **.** .. |
| % (prop)          | Hasonló a $ (Name) értékhez, kivéve, ha a kimenet URI-kódolású. |
| # (prop)          | JSON-sablonokban használatos (például iOS és Android rendszerű sablonok esetén).<br><br>Ez a függvény pontosan ugyanaz, mint a korábban megadott "$ (prop)", kivéve, ha JSON-sablonokban (például Apple-sablonok) használatosak. Ebben az esetben, ha ez a függvény nem a "{", "}" (például "myJsonProperty": "# (név)"), és egy JavaScript formátumú számot ad vissza, például regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, majd a kimenet JSON egy szám.<br><br>Például a "BADGE:" # (név) "lesz" jelvény ": 40 (és nem" 40 "). |
| "text" vagy "text" | Egy szövegkonstans. A literálek egyetlen vagy idézőjelek közé foglalt tetszőleges szöveget tartalmaznak. |
| Kif1 + Kif2    | Az összefűzési operátor két kifejezést egyesít egyetlen karakterláncban. |

A kifejezések az előző űrlapok bármelyike lehet.

Az Összefűzés használatakor a teljes kifejezést körül kell lennie `{}` . Például: `{$(prop) + ‘ - ’ + $(prop2)}`.

A következő sablon például nem érvényes XML-sablon:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Ahogy korábban már említettük, az összefűzéskor a kifejezéseket kapcsos zárójelbe kell becsomagolni. Például:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Következő lépések

[További tudnivalók az Azure Notification Hubs](notification-hubs-push-notification-overview.md)
