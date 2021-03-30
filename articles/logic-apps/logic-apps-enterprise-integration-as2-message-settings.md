---
title: AS2-üzenetek beállításai
description: Útmutató az AS2-beli küldési és fogadási beállításokhoz Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "74793028"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Az AS2-üzenet beállításainak referenciája Azure Logic Appsban Enterprise Integration Pack

Ez a hivatkozás azokat a tulajdonságokat ismerteti, amelyekkel megadható, hogy az AS2-egyezmény hogyan kezelje a kereskedelmi partnerek között küldött és fogadott üzeneteket. Állítsa be ezeket a tulajdonságokat a partnerrel kötött szerződése alapján, amely üzeneteket cserél Önnel.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2-fogadási beállítások

![Válassza a "fogadási beállítások" lehetőséget.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Üzenet felülbírálásának tulajdonságai** | No | Felülbírálja a beérkező üzenetek tulajdonságait a tulajdonságok beállításaival. |
| **Az üzenetet alá kell írni** | No | Megadja, hogy az összes bejövő üzenetnek digitálisan aláírtnak kell-e lennie. Ha aláírásra van szüksége, a **tanúsítvány** listáról válasszon ki egy meglévő vendég partner nyilvános tanúsítványt az aláírás érvényesítéséhez az üzeneteken. Ha nem rendelkezik tanúsítvánnyal, további tudnivalók a [tanúsítványok hozzáadásáról](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Az üzenetnek titkosítottnak kell lennie** | No | Meghatározza, hogy az összes bejövő üzenetet titkosítani kell-e. A nem titkosított üzenetek el lesznek utasítva. Ha titkosításra van szüksége, a **tanúsítvány** listából válasszon ki egy meglévő, a beérkező üzenetek visszafejtésére szolgáló fogadó partner privát tanúsítványát. Ha nem rendelkezik tanúsítvánnyal, további tudnivalók a [tanúsítványok hozzáadásáról](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Az üzenetnek tömörítettnek kell lennie** | No | Meghatározza, hogy az összes bejövő üzenetnek tömörítettnek kell-e lennie. A nem tömörített üzenetek elutasítása. |
| **Üzenetek AZONOSÍTÓjának ismétlődésének tiltása** | No | Megadja, hogy a duplikált azonosítókkal rendelkező üzenetek engedélyezve legyenek-e. Ha nem engedélyezi az ismétlődő azonosítókat, válassza ki a napok számát a csekkek között. Azt is megadhatja, hogy a duplikált elemek felfüggesztése megtörténjen-e. |
| **MDN szövege** | No | Megadja az üzenet küldőjének elküldeni kívánt alapértelmezett üzenet-törlési értesítést (MDN). |
| **MDN küldése** | No | Megadja, hogy a rendszer szinkron MDNs küldjön-e a fogadott üzenetekhez.  |
| **Aláírt MDN küldése** | No | Megadja, hogy a fogadott üzenetekhez aláírt MDNs kell-e küldeni. Ha aláírásra van szüksége, a **MIC algoritmus** listából válassza ki az üzenetek aláírásához használandó algoritmust. |
| **Aszinkron MDN küldése** | No | Megadja, hogy aszinkron módon kívánja-e elküldeni a MDNs. Ha az aszinkron MDNs lehetőséget választja, az **URL-cím** mezőben adja meg azt az URL-címet, ahová el szeretné küldeni a MDNs. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2-küldési beállítások

![Válassza a "küldési beállítások" lehetőséget.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Tulajdonság | Kötelező | Leírás |
|----------|----------|-------------|
| **Üzenetek aláírásának engedélyezése** | No | Megadja, hogy az összes kimenő üzenetnek digitálisan aláírtnak kell-e lennie. Ha aláírásra van szüksége, válassza ki a következő értékeket: <p>– Az **aláírási algoritmus** listából válassza ki az üzenetek aláírásához használandó algoritmust. <br>– A **tanúsítvány** listából válasszon ki egy meglévő, a gazdagéphez tartozó privát tanúsítványt az üzenetek aláírásához. Ha nem rendelkezik tanúsítvánnyal, további tudnivalók a [tanúsítványok hozzáadásáról](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenetek titkosításának engedélyezése** | No | Meghatározza, hogy az összes kimenő üzenetet titkosítani kell-e. Ha titkosításra van szüksége, válassza ki a következő értékeket: <p>– A **titkosítási algoritmus** listából válassza ki az üzenetek titkosításához használandó vendég partner nyilvános tanúsítvány-algoritmust. <br>– A **tanúsítvány** listából válasszon ki egy meglévő vendég partner privát tanúsítványt a kimenő üzenetek titkosításához. Ha nem rendelkezik tanúsítvánnyal, további tudnivalók a [tanúsítványok hozzáadásáról](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Üzenetek tömörítésének engedélyezése** | No | Meghatározza, hogy az összes kimenő üzenetnek tömörítettnek kell-e lennie. |
| **HTTP-fejlécek kidobása** | No | A HTTP- `content-type` fejlécet egyetlen sorba helyezi. |
| **Fájlnév továbbítása a MIME-fejlécben** | No | Megadja, hogy a fájl neve szerepeljen-e a MIME-fejlécben. |
| **MDN kérése** | No | Itt adható meg, hogy az összes kimenő üzenet esetében megjelenjen-e az üzenet-törlési értesítések (MDNs). |
| **Aláírt MDN kérése** | No | Megadja, hogy a rendszer az összes kimenő üzenet esetében fogad-e aláírt MDNs. Ha aláírásra van szüksége, a **MIC algoritmus** listából válassza ki az üzenetek aláírásához használandó algoritmust. |
| **Aszinkron MDN kérése** | No | Megadja, hogy aszinkron módon kell-e fogadni a MDNs. Ha az aszinkron MDNs lehetőséget választja, az **URL-cím** mezőben adja meg azt az URL-címet, ahová el szeretné küldeni a MDNs. |
| **NRR engedélyezése** | No | Itt adható meg, hogy szükséges-e a nem megtagadási visszaigazolás (NRR). Ez a kommunikációs attribútum igazolja, hogy az adatok címzettként érkeztek meg. |
| **SHA2 algoritmus formátuma** | No | Meghatározza a kimenő AS2-üzenetek vagy MDN fejlécében való bejelentkezéshez használandó MIC-algoritmus formátumát. |
||||

## <a name="next-steps"></a>Következő lépések

[AS2-üzenetek váltása](../logic-apps/logic-apps-enterprise-integration-as2.md)
