---
title: A feltételes hozzáférés What If eszköz – Azure Active Directory
description: Ismerje meg, hogy a feltételes hozzáférési szabályzatok milyen hatással lehetnek a környezetre.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5846934a8ad8455ca375b4bc54fc46d45aba1cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379980"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Hibakeresés a What If eszköz használatával a feltételes hozzáférésben

A [feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) Azure Active Directory (Azure ad) egyik funkciója, amely lehetővé teszi, hogy a jogosult felhasználók hozzáférjenek a felhőalapú alkalmazásokhoz. Honnan tudhatja, mire számíthat a környezet feltételes hozzáférési házirendjeiből? A kérdés megválaszolásához használhatja a **feltételes hozzáférési What if eszközt**.

Ez a cikk bemutatja, hogyan használható az eszköz a feltételes hozzáférési szabályzatok teszteléséhez.

## <a name="what-it-is"></a>Mi ez?

A **feltételes hozzáférés What if házirend eszköz** lehetővé teszi a feltételes hozzáférési szabályzatok környezetre gyakorolt hatásának megismerését. Nem szükséges manuálisan végrehajtott bejelentkezésekkel tesztelni a szabályzatokat, az eszköz használatával egy szimulált felhasználói bejelentkezést értékelhet ki. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést. A jelentés nem csak az alkalmazott feltételes hozzáférési szabályzatokat sorolja fel, hanem a [klasszikus szabályzatokat](policy-migration.md#classic-policies) is, ha vannak ilyenek.    

Az **What if** eszköz segítségével gyorsan meghatározhatja az adott felhasználóra vonatkozó házirendeket. Az információkat például akkor használhatja, ha problémát kell elhárítani.    

## <a name="how-it-works"></a>Működés

A **feltételes hozzáférés What if eszközben**először konfigurálnia kell a szimulálni kívánt bejelentkezési forgatókönyv beállításait. Ezek a beállítások a következők:

- A tesztelni kívánt felhasználó 
- Azok a felhőalapú alkalmazások, amelyeket a felhasználó megkísérel elérni
- A felhőalapú alkalmazások konfigurálásának feltételei
     
A következő lépésként elindíthat egy szimulációs futtatást, amely kiértékeli a beállításokat. Csak az engedélyezett szabályzatok tartoznak egy próbaverziós futtatásba.

Ha a kiértékelés befejeződött, az eszköz létrehoz egy jelentést az érintett házirendekről.

## <a name="running-the-tool"></a>Az eszköz futtatása

Az **What if** eszközt a Azure Portal **[feltételes hozzáférési szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** oldalán találja.

Az eszköz elindításához a házirendek listájának tetején található eszköztáron kattintson a **What if**elemre.

![What If](./media/what-if-tool/01.png)

A próbaverzió futtatása előtt konfigurálnia kell a beállításokat.

## <a name="settings"></a>Beállítások

Ez a szakasz a szimulációs futtatási beállításokkal kapcsolatos információkat tartalmaz.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Felhasználó

Csak egy felhasználót választhat ki. Ez az egyetlen kötelező mező.

### <a name="cloud-apps"></a>Felhőalapú alkalmazások

A beállítás alapértelmezett értéke **minden felhőalapú alkalmazás**. Az alapértelmezett beállítás az összes elérhető házirend kiértékelését végzi a környezetben. A hatókört leszűkítheti bizonyos felhőalapú alkalmazásokat érintő szabályzatokra.

### <a name="ip-address"></a>IP-cím

Az IP-cím egyetlen IPv4-cím, amely a [hely feltételét](location-condition.md)utánozza. A címe a felhasználó által a bejelentkezéshez használt eszköz internet felé irányuló címe. Az eszközök IP-címét ellenőrizheti például, hogy [Mi az az IP-cím](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Eszköz platformok

Ez a beállítás az [eszköz platformjának feltételeit](conditions.md#device-platforms) utánozza, és az **összes platform (beleértve a nem támogatott)** megfelelőjét jelöli. 

### <a name="client-apps"></a>Ügyfélalkalmazások

Ez a beállítás a [Client apps-feltételt](conditions.md#client-apps)utánozza.
Alapértelmezés szerint ez a beállítás az összes olyan házirend kiértékelését okozza, amelyeken a **böngésző** vagy a **Mobile apps, illetve az asztali ügyfelek** egyenként vagy mindkettőben vannak kiválasztva. Emellett észleli azokat a házirendeket, amelyek kikényszerítik az **Exchange ActiveSync (EAS)** szolgáltatást. Ezt a beállítást a következő lehetőség kiválasztásával szűkítheti le:

- **Böngésző** az összes olyan házirend kiértékeléséhez, amelynél legalább **böngésző** van kiválasztva. 
- **Mobile apps és asztali ügyfelek** az összes olyan házirend kiértékeléséhez, amelyeken legalább **Mobile apps és asztali ügyfél** van kiválasztva. 

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

Ez a beállítás a [bejelentkezési kockázat feltételét](conditions.md#sign-in-risk)utánozza.   

## <a name="evaluation"></a>Értékelési 

A kiértékelés elindításához kattintson a **What if**gombra. A kiértékelés eredménye a következőkből álló jelentést tartalmazza: 

![What If](./media/what-if-tool/03.png)

- Annak jelzése, hogy a klasszikus szabályzatok léteznek-e a környezetben
- A felhasználóra érvényes házirendek
- Azok a házirendek, amelyek nem érvényesek a felhasználóra

Ha a kiválasztott felhőalapú alkalmazásokhoz [klasszikus szabályzatok](policy-migration.md#classic-policies) vannak megadva, a rendszer megjelenít egy kijelzőt. A kijelzőre kattintva a rendszer átirányítja a klasszikus házirendek lapra. A klasszikus szabályzatok oldalon áttelepítheti a klasszikus házirendeket, vagy letilthatja azt. Az oldal bezárásával visszatérhet a kiértékelési eredményhez.

A kiválasztott felhasználóra vonatkozó szabályzatok listáján megtalálhatja az [engedélyezési vezérlők](controls.md#grant-controls) és [munkamenet](controls.md#session-controls) -vezérlők listáját, amelyet a felhasználónak teljesítenie kell.

Azon házirendek listáján, amelyek nem érvényesek a felhasználóra, megtalálhatja azokat az okokat is, amelyek miatt ezek a szabályzatok nem érvényesek. Az egyes felsorolt szabályzatok esetében az ok az első olyan feltételt jelenti, amely nem teljesült. Egy nem alkalmazott házirend lehetséges oka a letiltott házirend, mivel azokat a rendszer nem értékeli ki.   

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan konfigurálhat egy feltételes hozzáférési szabályzatot, tekintse meg a többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](app-based-mfa.md)című témakört.
- Ha készen áll a környezet feltételes hozzáférési házirendjeinek konfigurálására, tekintse meg az [ajánlott eljárásokat a feltételes hozzáféréshez Azure Active Directory](best-practices.md). 
- Ha klasszikus szabályzatokat kíván áttelepíteni, tekintse [meg a klasszikus szabályzatok áttelepítését a Azure Portal](policy-migration.md)  
