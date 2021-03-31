---
title: Azure Notification Hubs és a Google Firebase Cloud Messaging (FCM) migrálása
description: Leírja, hogyan kezeli az Azure Notification Hubs a Google GCM az FCM áttelepítésére.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80127028"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Azure Notification Hubs és Google Firebase Cloud Messaging Migration

## <a name="current-state"></a>Aktuális állapot

Amikor a Google bejelentette a Google Cloud Messaging (GCM) rendszerből való áttelepítését a Cloud Messaging (FCM) szolgáltatásba, a leküldéses szolgáltatások (például a miénk) azt is elvégezték, hogy az Android-eszközökre küldött értesítések hogyan férjenek hozzá

Frissítettük a szolgáltatási hátteret, majd igény szerint közzétettük az API-k és SDK-k frissítéseit. A megvalósítással a meglévő GCM-értesítési sémákkal való kompatibilitás fenntartása mellett döntöttünk, hogy az ügyfelek milyen hatással lesznek a felhasználókra. Ez azt jelenti, hogy jelenleg az FCM-et használó Android-eszközökre küldünk értesítéseket az FCM örökölt módban. Végső soron az FCM-hez valódi támogatást szeretnénk adni, beleértve az új funkciókat és a hasznos adatokat is. Ez egy hosszú távú változás, és a jelenlegi áttelepítés a meglévő alkalmazásokkal és SDK-k kompatibilitásának fenntartására koncentrál. Használhatja az GCM vagy az FCM SDK-t az alkalmazásban (az SDK-val együtt), és gondoskodunk róla, hogy az értesítés megfelelően legyen elküldve.

Néhány ügyfél nemrég kapott egy e-mailt a Google figyelmeztetéssel kapcsolatban a GCM-végpontot használó alkalmazásokról az értesítésekhez. Ez csak egy figyelmeztetés volt, és semmi nem sérült – az alkalmazás androidos értesítései továbbra is a Google-ba kerülnek feldolgozásra, és a Google továbbra is feldolgozza azokat. Azok az ügyfelek, akik explicit módon adtak meg a GCM-végpontot a szolgáltatás konfigurációjában, továbbra is az elavult végpontot használják. Már azonosította ezt a rést, és dolgoztam a probléma kijavításán, amikor a Google elküldte az e-mailt.

Lecseréljük az elavult végpontot, és telepítettük a javítást.

## <a name="going-forward"></a>Továbbítás folyamatban

A Google FCM gyakori kérdései szerint nem kell semmit tennie. Az [FCM GYIK](https://developers.google.com/cloud-messaging/faq)-ban a Google azt mondta, hogy "az ügyfél SDK-k és a GCM-tokenek továbbra is határozatlan ideig fognak működni. Azonban nem fogja tudni megcélozni a Google Play-szolgáltatások legújabb verzióját az Android-alkalmazásban, hacsak nem telepít át az FCM-re. "

Ha az alkalmazás a GCM könyvtárat használja, folytassa a Google utasításait, és frissítsen az FCM-tárba az alkalmazásában. Az SDK kompatibilis az egyikével sem, így nem kell semmit frissítenie az alkalmazásában a saját oldalunkon (feltéve, hogy az SDK verziójával naprakész).

## <a name="questions-and-answers"></a>Kérdések és válaszok

Íme néhány válasz az ügyfelektől meghallott gyakori kérdésekre:

**K:** Mit kell tennem ahhoz, hogy kompatibilisek legyenek a kivágási dátummal (a Google jelenlegi levágási dátuma május 29-én és változhat)?

**A:** Nincs. A meglévő GCM-értesítési sémával való kompatibilitás fenntartása megmarad. A GCM-kulcs továbbra is a megszokott módon fog működni, mint az alkalmazás által használt GCM SDK-k és kódtárak.

Ha/ha úgy dönt, hogy az FCM SDK-kat és a kódtárakat használja az új funkciók kihasználásához, a GCM-kulcs továbbra is működni fog. Ha szeretné, átválthat az FCM-es kulcs használatára, de győződjön meg arról, hogy a meglévő GCM-projekthez Firebase ad hozzá az új Firebase-projekt létrehozásakor. Ez biztosítja a visszamenőleges kompatibilitást az alkalmazás régebbi verzióit futtató ügyfelekkel, akik továbbra is GCM SDK-kat és kódtárakat használnak.

Ha új FCM-projektet hoz létre, és nem csatlakozik a meglévő GCM-projekthez, az új FCM-titokkal való Notification Hubs frissítése után elveszíti az értesítések leküldését a jelenlegi alkalmazás-telepítésekre, mivel az új FCM-kulcs nem tartalmaz hivatkozást a régi GCM-projekthez.

**K:** Miért kapok ezt az e-mailt a használatban lévő régi GCM-végpontokról? Mit kell tennem?

**A:** Nincs. Migráljuk az új végpontokra, és hamarosan elkészült, így nincs szükség módosításra. Semmi nem sérült, az egyik kihagyott végpont egyszerűen a Google figyelmeztető üzeneteit okozta.

**K:** Hogyan válthatok az új FCM SDK-k és könyvtárak számára a meglévő felhasználók megszakítása nélkül?

A: bármikor frissíthető. A Google még nem jelentett elavultat meglévő GCM SDK-k és kódtárak esetében. Annak biztosítása érdekében, hogy a leküldéses értesítések ne legyenek megszakítva a meglévő felhasználók számára, ellenőrizze, hogy az új Firebase-projekt létrehozásakor a meglévő GCM-projekthez társítva van-e. Ezzel biztosíthatja, hogy az új Firebase-titkok az alkalmazás régebbi verzióit futtató felhasználók számára GCM SDK-k és könyvtárak, valamint az alkalmazás új felhasználói számára az FCM SDK-k és a kódtárak révén működjenek.

**K:** Mikor használhatom az új FCM-funkciókat és-sémákat az értesítések esetében?

**A:** Miután közzétettük az API-k és SDK-k frissítését, maradjunk velünk, hogy az elkövetkező hónapokban van valami.
