---
title: Notification Hubs biztonsági modell
description: Ismerje meg az Azure Notification Hubs biztonsági modelljét.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 07600b1fe0cb7420989fbbfbe55c2f1a4197d2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548250"
---
# <a name="notification-hubs-security"></a>Notification Hubs biztonság

## <a name="overview"></a>Áttekintés

Ez a témakör az Azure Notification Hubs biztonsági modelljét ismerteti.

## <a name="shared-access-signature-security"></a>Közös hozzáférésű aláírás biztonsága

Notification Hubs megvalósítja a *közös hozzáférésű aláírás* (SAS) nevű entitás szintű biztonsági sémát. Minden szabály tartalmaz egy nevet, egy kulcs értéket (közös titkos kulcsot) és a jogosultságok egy készletét, ahogy azt a [biztonsági jogcímek](#security-claims)később ismertették. 

Egy központ létrehozásakor a rendszer automatikusan két szabályt hoz létre: egyet a **figyelési** jogokkal (az ügyfélalkalmazás által használt), és egyet az **összes** jogosultsággal (amelyet az alkalmazás-háttér használ):

- **DefaultListenSharedAccessSignature**: csak a **figyelési** engedélyt engedélyezi.
- **DefaultFullSharedAccessSignature**: a **figyelési**, **kezelési** és **küldési** engedélyeket biztosít. Ezt a szabályzatot csak az alkalmazási háttérrendszer használja. Ne használja az ügyfélalkalmazások számára; olyan házirendet használjon, amely csak a **figyelési** hozzáféréssel rendelkezik. Az új SAS-tokent tartalmazó új egyéni hozzáférési házirend létrehozásához tekintse meg a jelen cikk későbbi, a [hozzáférési szabályzatok sas-jogkivonatait](#sas-tokens-for-access-policies) ismertető cikket.

Ha az ügyfélalkalmazások regisztrációs felügyeletet végeznek, ha az értesítéseken keresztül küldött információk nem érzékenyek (például időjárás-frissítések), az értesítési központ elérésének közös módja, hogy a szabály kulcsfontosságú értéke csak az ügyfélalkalmazás számára legyen elérhető, és hogy a szabály kulcs értéke teljes hozzáférést biztosítson az alkalmazás-háttérhöz.

Az alkalmazások nem ágyazzák be a kulcs értékét a Windows áruházbeli ügyfélalkalmazásokba; Ehelyett az ügyfélalkalmazás az indításkor lekéri az alkalmazás-háttérből.

A **figyelési** hozzáféréssel rendelkező kulcs lehetővé teszi, hogy az ügyfélalkalmazás bármely címkére regisztrálhat. Ha az alkalmazásnak meghatározott felhasználóknak kell korlátoznia a regisztrációkat adott címkékre (például ha a címkék felhasználói azonosítókat jelölnek), az alkalmazás-háttérnek el kell végeznie a regisztrációt. További információ: [regisztráció kezelése](notification-hubs-push-notification-registration-management.md). Vegye figyelembe, hogy az ügyfélalkalmazás így nem lesz közvetlen hozzáférése Notification Hubshoz.

## <a name="security-claims"></a>Biztonsági jogcímek

A többi entitáshoz hasonlóan a Notification hub műveletei három biztonsági jogcím esetében is engedélyezettek: **figyelés**, **Küldés** és **felügyelet**.

| Jogcím   | Leírás                                          | Engedélyezett műveletek |
| ------- | ---------------------------------------------------- | ------------------ |
| Figyelés  | Egyszeri regisztrációk létrehozása/frissítése, olvasása és törlése | Regisztráció létrehozása/frissítése<br><br>Regisztráció beolvasása<br><br>Egy leíró összes regisztrációjának olvasása<br><br>Regisztráció törlése |
| Küldés    | Üzenetek küldése az értesítési központnak                | Üzenet küldése |
| Kezelés  | Notification Hubs (beleértve a PNS hitelesítő adatainak frissítését és a biztonsági kulcsokat), valamint a címkék alapján történő regisztrációk beolvasása |Hubok létrehozása/frissítése/olvasása/törlése<br><br>Regisztrációk beolvasása címke szerint |

A Notification Hubs fogadja a közvetlenül a központban konfigurált megosztott kulcsokkal generált SAS-jogkivonatokat.

Nem lehet értesítést küldeni több névtérnek. A névterek a Notification Hubs logikai tárolói, és nem vesznek részt az értesítések küldésében.

Névtér szintű műveletekhez használja a névtér szintű hozzáférési házirendeket (hitelesítő adatokat); például: csomópontok listázása, hubok létrehozása vagy törlése stb. Csak a hub szintű hozzáférési házirendek teszik lehetővé az értesítések küldését.

### <a name="sas-tokens-for-access-policies"></a>Hozzáférési szabályzatok SAS-jogkivonatai

Új biztonsági jogcím létrehozásához vagy a meglévő SAS-kulcsok megtekintéséhez tegye a következőket:

1. Jelentkezzen be az Azure Portalra.
2. Válassza a **Minden erőforrás** elemet.
3. Válassza ki az értesítési központ nevét, amelyhez létre kívánja hozni a jogcímet, vagy tekintse meg az SAS-kulcsot.
4. A bal oldali menüben válassza a **hozzáférési szabályzatok** lehetőséget.
5. Új biztonsági jogcím létrehozásához válassza az **új szabályzat** lehetőséget. Adja meg a szabályzat nevét, és válassza ki a megadni kívánt engedélyeket. Ez után válassza az **OK** gombot.
6. A hozzáférési házirendek ablakban megjelenik a teljes kapcsolati karakterlánc (az új SAS-kulccsal együtt). Ezt a karakterláncot a vágólapra másolhatja későbbi használatra.

Ha az SAS-kulcsot egy adott szabályzatból szeretné kibontani, kattintson a kívánt SAS-kulcsot tartalmazó házirend melletti **Másolás** gombra. Illessze be ezt az értéket egy ideiglenes helyre, majd másolja a kapcsolatok karakterlánc SAS-kulcs részét. Ez a példa egy **mytestnamespace1** nevű Notification Hubs névteret és egy **policy2** nevű szabályzatot használ. Az SAS-kulcs a karakterlánc végén található, a **SharedAccessKey** által megadott érték:

```shell
Endpoint=sb://mytestnamespace1.servicebus.windows.net/;SharedAccessKeyName=policy2;SharedAccessKey=<SAS key value here>
```

![SAS-kulcsok beszerzése](media/notification-hubs-push-notification-security/access1.png)

## <a name="next-steps"></a>Következő lépések

- [Notification Hubs áttekintése](notification-hubs-push-notification-overview.md)
