---
title: A feltételes hozzáférés What If eszköz – Azure Active Directory
description: Ismerje meg, hogy a feltételes hozzáférési szabályzatok milyen hatással lehetnek a környezetre.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3ac799203cade3a907acbe28dee3a8023891db2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077642"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Hibaelhárítás a feltételes hozzáférés Lehetőségelemzési eszközével

A [feltételes hozzáférés](./overview.md) Azure Active Directory (Azure ad) egyik funkciója, amely lehetővé teszi, hogy a jogosult felhasználók hozzáférjenek a felhőalapú alkalmazásokhoz. Honnan tudhatja, mire számíthat a környezet feltételes hozzáférési házirendjeiből? A kérdés megválaszolásához használhatja a **feltételes hozzáférési What if eszközt**.

Ez a cikk bemutatja, hogyan használható az eszköz a feltételes hozzáférési szabályzatok teszteléséhez.

## <a name="what-it-is"></a>Leírás

A **feltételes hozzáférés What if házirend eszköz** lehetővé teszi a feltételes hozzáférési szabályzatok környezetre gyakorolt hatásának megismerését. Ahelyett, hogy tesztelje a szabályzatokat több bejelentkezés manuális elvégzésével, ez az eszköz lehetővé teszi egy felhasználó szimulált bejelentkezésének kiértékelését. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést. A jelentés nem csak az alkalmazott feltételes hozzáférési szabályzatokat sorolja fel, hanem a [klasszikus szabályzatokat](policy-migration.md#classic-policies) is, ha vannak ilyenek.    

Az **What if** eszköz segítségével gyorsan meghatározhatja az adott felhasználóra vonatkozó házirendeket. Az információkat például akkor használhatja, ha problémát kell elhárítani.    

## <a name="how-it-works"></a>Működés

A **feltételes hozzáférés What if eszközben** először konfigurálnia kell a szimulálni kívánt bejelentkezési forgatókönyv beállításait. Ezek a beállítások többek között:

- A tesztelni kívánt felhasználó 
- Azok a felhőalapú alkalmazások, amelyeket a felhasználó megkísérel elérni
- A konfigurált felhőalapú alkalmazásokhoz való hozzáférés végrehajtásának feltételei
     
A következő lépésként elindíthat egy szimulációs futtatást, amely kiértékeli a beállításokat. Csak az engedélyezett szabályzatok tartoznak egy próbaverziós futtatásba.

Ha a kiértékelés befejeződött, az eszköz létrehoz egy jelentést az érintett házirendekről. A feltételes hozzáférési szabályzattal kapcsolatos további információk összegyűjtéséhez a [feltételes hozzáférések és a jelentéskészítési munkafüzetek](howto-conditional-access-insights-reporting.md) további részleteket biztosítanak a csak jelentési módban lévő házirendekről és a jelenleg engedélyezett házirendekről.

## <a name="running-the-tool"></a>Az eszköz futtatása

Az **What if** eszközt a Azure Portal **[feltételes hozzáférési szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** oldalán találja.

Az eszköz elindításához a házirendek listájának tetején található eszköztáron kattintson a **What if** elemre.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Képernyőkép a Azure Portal feltételes hozzáférési szabályzatok oldaláról. Az eszköztáron a mi történik, ha az elem ki van emelve." border="false":::

A próbaverzió futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a szakasz a szimulációs futtatási beállításokkal kapcsolatos információkat tartalmaz.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Képernyőfelvétel a Azure Portal What If oldalról, amelynek mezői a felhasználók, a Cloud apps, az I P-címek, az eszközök platformja, az ügyfélalkalmazás és a bejelentkezési kockázat." border="false":::

### <a name="user"></a>User

Csak egy felhasználót választhat ki. Ez az egyetlen kötelező mező.

### <a name="cloud-apps"></a>Felhőalkalmazások

A beállítás alapértelmezett értéke **minden felhőalapú alkalmazás**. Az alapértelmezett beállítás az összes elérhető házirend kiértékelését végzi a környezetben. A hatókört leszűkítheti bizonyos felhőalapú alkalmazásokat érintő szabályzatokra.

### <a name="ip-address"></a>IP-cím

Az IP-cím egyetlen IPv4-cím, amely a [hely feltételét](location-condition.md)utánozza. A címe a felhasználó által a bejelentkezéshez használt eszköz internet felé irányuló címe. Az eszközök IP-címét ellenőrizheti például, hogy [Mi az az IP-cím](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Eszközplatformok

Ez a beállítás az [eszköz platformjának feltételeit](concept-conditional-access-conditions.md#device-platforms) utánozza, és az **összes platform (beleértve a nem támogatott)** megfelelőjét jelöli. 

### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás a [Client apps-feltételt](concept-conditional-access-conditions.md#client-apps)utánozza.
Alapértelmezés szerint ez a beállítás az összes olyan házirend kiértékelését okozza, amelyeken a **böngésző** vagy a **Mobile apps, illetve az asztali ügyfelek** egyenként vagy mindkettőben vannak kiválasztva. Emellett észleli azokat a házirendeket, amelyek kikényszerítik az **Exchange ActiveSync (EAS)** szolgáltatást. Ezt a beállítást a következő lehetőség kiválasztásával szűkítheti le:

- **Böngésző** az összes olyan házirend kiértékeléséhez, amelynél legalább **böngésző** van kiválasztva. 
- **Mobile apps és asztali ügyfelek** az összes olyan házirend kiértékeléséhez, amelyeken legalább **Mobile apps és asztali ügyfél** van kiválasztva. 

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás a [bejelentkezési kockázat feltételét](concept-conditional-access-conditions.md#sign-in-risk)utánozza.   

## <a name="evaluation"></a>Értékelés 

A kiértékelés elindításához kattintson a **What if** gombra. A kiértékelés eredménye a következőkből álló jelentést tartalmazza: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Képernyőkép egy értékelési jelentésről. A szöveg azt jelzi, hogy legalább egy klasszikus házirend konfigurálva van. A szabályzatok megtekintéséhez lapok érhetők el." border="false":::

- Annak jelzése, hogy a klasszikus szabályzatok léteznek-e a környezetben
- A felhasználóra érvényes házirendek
- Azok a házirendek, amelyek nem érvényesek a felhasználóra

Ha a kiválasztott felhőalapú alkalmazásokhoz [klasszikus szabályzatok](policy-migration.md#classic-policies) vannak megadva, a rendszer megjelenít egy kijelzőt. A kijelzőre kattintva a rendszer átirányítja a klasszikus házirendek lapra. A klasszikus szabályzatok oldalon áttelepítheti a klasszikus házirendeket, vagy letilthatja azt. Az oldal bezárásával visszatérhet a kiértékelési eredményhez.

A kiválasztott felhasználóra vonatkozó szabályzatok listáján megtalálhatja az [engedélyezési vezérlők](concept-conditional-access-grant.md) és [munkamenet-vezérlők](concept-conditional-access-session.md) listáját, amelyet a felhasználónak teljesítenie kell.

Azon házirendek listáján, amelyek nem érvényesek a felhasználóra, megtalálhatja azokat az okokat is, amelyek miatt ezek a szabályzatok nem érvényesek. Az egyes felsorolt szabályzatok esetében az ok az első olyan feltételt jelenti, amely nem teljesült. Egy nem alkalmazott házirend lehetséges oka a letiltott házirend, mivel azokat a rendszer nem értékeli ki.   

## <a name="next-steps"></a>Következő lépések

- A feltételes hozzáférési szabályzat alkalmazásával kapcsolatos további információkért a [feltételes hozzáférési információk és jelentéskészítés](howto-conditional-access-insights-reporting.md)funkció használatával a házirend csak jelentés mód használatával érhető el.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg a [feltételes hozzáférés általános házirendjeit](concept-conditional-access-policy-common.md).
