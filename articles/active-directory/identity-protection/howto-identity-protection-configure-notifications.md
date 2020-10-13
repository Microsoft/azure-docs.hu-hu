---
title: Értesítések Azure Active Directory Identity Protection
description: Ismerje meg, hogy az értesítések hogyan támogatják a vizsgálati tevékenységeket.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c786aaecd3ab2f18f242cea2f5c45838f9ecf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839347"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Értesítések Azure Active Directory Identity Protection

Azure AD Identity Protection kétféle automatizált értesítő e-mailt küld a felhasználói kockázatok és a kockázati észlelések kezeléséhez:

- Veszélyeztetett felhasználók észlelt e-mail-címe
- Heti kivonatoló e-mail

Ez a cikk az értesítő e-mailek áttekintését tartalmazza.

## <a name="users-at-risk-detected-email"></a>Veszélyeztetett felhasználók észlelt e-mail-címe

A veszélyeztetett észlelt fiókra adott válaszként Azure AD Identity Protection e-mail-riasztást hoz létre a **veszélyeztetett felhasználókkal** kapcsolatban. Az e-mail tartalmazza a kockázati jelentésre **[megjelölt felhasználókra](./overview-identity-protection.md)** mutató hivatkozást. Ajánlott eljárásként azonnal vizsgálja meg a veszélyeztetett felhasználókat.

Ennek a riasztásnak a konfigurációja lehetővé teszi, hogy meghatározza, milyen felhasználói kockázati szinten szeretné létrehozni a riasztást. A rendszer akkor hozza létre az e-mailt, ha a felhasználó kockázati szintje eléri a megadott értéket. Ha például úgy állítja be a házirendet, hogy az a közepes felhasználói kockázatra figyelmeztessen, és a felhasználó János felhasználói kockázati pontszáma közepes kockázatra vált a valós idejű bejelentkezési kockázat miatt, akkor a veszélyeztetett felhasználók számára észlelt e-mail üzenet jelenik meg. Ha a felhasználónál olyan kockázati észlelések következnek be, amelyek következtében a felhasználói kockázati szint kiszámítása a megadott kockázati szintre (vagy magasabbra) történik, akkor a kockázatnak kitett, a felhasználói kockázati pontszám újraszámítása esetén további veszélyeztetett felhasználókat is kapni fog. Ha például egy felhasználó január 1-től közepes kockázatra vált, e-mailben értesítést fog kapni, ha a beállítások közepes kockázatú riasztásra vannak beállítva. Ha ugyanezen a felhasználónál ezt követően egy másik, a január 5. januári kockázati észlelési kockázattal is jár, és a felhasználói kockázati pontszám újra ki van számítva, és még közepes, egy másik e-mail-értesítést fog kapni. 

Egy további e-mail-értesítés azonban csak akkor lesz elküldve, ha a kockázat észlelésének időpontja (ami a felhasználói kockázati szint változását okozta) újabb, mint az utolsó e-mail elküldésekor. Például egy felhasználói bejelentkezés január 1-től 5 ÓRAKOR, és nincs valós idejű kockázat (ami azt jelenti, hogy a bejelentkezés miatt nem jön létre e-mail-cím). Tíz perccel később, a 5:10 ÓRAKOR ugyanaz a felhasználó jelentkezik újra, és magas a valós idejű kockázat, így a felhasználói kockázati szint magasra vált, és elküldhető e-mailben. Ezután a 5:15 ÓRAKOR az offline kockázati pontszám az eredeti bejelentkezéshez 5 ÓRAKOR változik, és ez a kockázat a kapcsolat nélküli kockázatok feldolgozása miatt magas kockázatot jelent. A rendszer nem küld további felhasználót a kockázati e-mailekhez, mert az első bejelentkezés időpontja a második olyan bejelentkezés előtt volt, amely már elindította az e-mailes értesítést.

Az e-mailek túlterhelésének megakadályozása érdekében egy 5 másodperces időszakon belül csak egyetlen felhasználót fog észlelni a veszélyeztetett e-mailen. Ez azt jelenti, hogy ha több felhasználó a megadott kockázati szintre vált ugyanazon az 5 másodperces időszakban, akkor összesítjük, majd egy e-mailt küldünk, hogy az összes ilyen kockázati szint változását képviseljék.

![Veszélyeztetett felhasználók észlelt e-mail-címe](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Veszélyeztetett felhasználók konfigurálása észlelt riasztásokkal

Rendszergazdaként a következőket állíthatja be:

- **Az e-mailek generációját kiváltó felhasználói kockázati szint** alapértelmezés szerint a kockázati szint "magas" kockázatra van állítva.
- A rendszer automatikusan hozzáadja az e- **mailek címzettjeit** a globális rendszergazda, a biztonsági rendszergazda vagy a biztonsági olvasó szerepkör felhasználói számára a listához. Megpróbáljuk elküldeni az e-maileket az egyes szerepkörök első 20 tagjának. Ha egy felhasználó regisztrálva van a PIM-ban, hogy az egyik ilyen szerepkört igény szerint emeljen, akkor csak akkor kapják meg az e **-maileket, ha azok az e-mail elküldésekor megemelve lesznek**.
   - Opcionálisan **hozzáadhat egyéni e-maileket itt** definiált felhasználóknak a megfelelő engedélyekkel kell rendelkezniük a csatolt jelentések megtekintéséhez a Azure Portal.

Konfigurálja a veszélyeztetett felhasználókat a **Azure Portal** **Azure Active Directory**  >  **biztonsági**  >  **identitások védelme**a  >  **veszélyeztetett felhasználók által észlelt riasztások**területen.

## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail

A heti kivonatoló e-mail tartalmazza az új kockázati észlelések összegzését.  
A következőket tartalmazza:

- Új kockázatos felhasználók észlelhetők
- Új kockázatos bejelentkezések észlelhetők (valós időben)
- Az Identity Protection kapcsolódó jelentéseire mutató hivatkozások

![Heti kivonatoló e-mail](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

A globális rendszergazda, a biztonsági rendszergazda vagy a biztonsági olvasó szerepkör felhasználói automatikusan hozzáadódnak a listához. Megpróbáljuk elküldeni az e-maileket az egyes szerepkörök első 20 tagjának. Ha egy felhasználó regisztrálva van a PIM-ben, hogy az egyik ilyen szerepkört igény szerint emeljen, akkor csak akkor kapják meg az e **-maileket, ha az e-mailek elküldésekor megemelve lesznek** .

### <a name="configure-weekly-digest-email"></a>Heti kivonatoló e-mail konfigurálása

Rendszergazdaként a heti kivonatoló e-mailek küldését vagy kikapcsolását is elvégezheti, és kiválaszthatja az e-mailek fogadásához hozzárendelt felhasználókat.

Konfigurálja a heti kivonatoló e-mailt **Azure portal** a Azure Portal **Azure Active Directory**  >  **biztonsági**  >  **Identity Protection**  >  **heti kivonatoló**területen.

## <a name="see-also"></a>Lásd még

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
