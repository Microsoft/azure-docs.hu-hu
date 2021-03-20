---
title: Az alkalmazások üzembe helyezésének ismert problémái az Azure AD-ben
description: Ismerje meg az Azure AD-ben végzett automatizált alkalmazások üzembe helyezésével kapcsolatos ismert problémákat.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256643"
---
# <a name="known-issues-application-provisioning"></a>Ismert problémák: az alkalmazás kiépítés
Ismert problémák, amelyek felmerülhetnek az alkalmazásba történő kiépítéskor. A UserVoice-on futó alkalmazás-kiépítési szolgáltatással kapcsolatban az [Azure ad Application UserVoice](https://aka.ms/appprovisioningfeaturerequest)című témakörben talál visszajelzést. Szorosan Figyeljük a UserVoice, így javíthatjuk a szolgáltatást. 

> [!NOTE]
> Ez nem az ismert problémák átfogó listája. Ha ismeri a nem felsorolt problémát, küldjön visszajelzést az oldal alján.

## <a name="authorization"></a>Engedélyezés 

**Nem sikerült menteni a sikeres kapcsolódási teszt után**

Ha sikeresen tesztel egy kapcsolatokat, de nem tudja menteni, akkor túllépte a hitelesítő adatok megengedett tárolási korlátját. További információ: a [rendszergazdai hitelesítő adatok mentése](./user-provisioning.md).

**Nem lehet menteni**

A mentéshez ki kell tölteni a bérlői URL-címet, a titkos jogkivonatot és az értesítő e-mailt. Csak az egyiket lehet megadnia. 

**A kiépítési mód nem módosítható a manuálisra**

Miután az első alkalommal konfigurálta az üzembe helyezést, megfigyelheti, hogy a kiépítési mód manuálisról automatikusra vált. A manuálisra nem módosítható. Az üzembe helyezést azonban a felhasználói felületen is kikapcsolhatja. A kiépítés a felhasználói felületen való kikapcsolása gyakorlatilag ugyanaz, mint a legördülő menü manuálisra állítása.  


## <a name="attribute-mappings"></a>Attribútumok hozzárendelése 

**A SamAccountName vagy userType attribútum nem érhető el forrás attribútumként**

A SamAccountName és a userType attribútumok alapértelmezés szerint nem érhetők el forrás attribútumként. Bővítse sémáját az attribútum hozzáadásához. Az attribútumokat a séma kibővítésével adhatja hozzá a rendelkezésre álló forrás attribútumainak listájához. További információ: [hiányzó forrás attribútum](user-provisioning-sync-attributes-for-mapping.md). 

**A séma-bővítményhez hiányzik a forrás attribútum legördülő listája**

A sémához tartozó bővítmények esetenként hiányozhatnak a felhasználói felületen a forrás attribútum legördülő menüből. Nyissa meg az attribútumok leképezésének speciális beállításait, és manuálisan adja hozzá az attribútumokat. További információ: [attribútumok leképezésének testreszabása](customize-application-attributes.md).

**NULL attribútum nem építhető ki**

Az Azure AD jelenleg nem tud null attribútumokat kiépíteni. Ha egy attribútum null értékű a felhasználói objektumon, a rendszer kihagyja a következőt:. 

**Attribútum-hozzárendelési kifejezések maximális száma**

Az attribútum-hozzárendelési kifejezések legfeljebb 10 000 karakterből állhatnak. 

**Nem támogatott hatóköri szűrők**

A címtárszolgáltatások, a appRoleAssignments, a userType és a accountExpires nem támogatottak hatóköri szűrőként.


## <a name="service-issues"></a>Szolgáltatásproblémák 

**Nem támogatott forgatókönyvek**

- A kiépítési jelszavak nem támogatottak. 
- A beágyazott csoportok kiépítés nem támogatott. 
- A B2C-bérlők kiépítés a bérlők mérete miatt nem támogatott.
- Nem minden alkalmazás érhető el minden felhőben. Például a Atlassian még nem érhető el a kormányzati felhőben. Dolgozunk az alkalmazás-fejlesztőknek, hogy alkalmazásaikat minden felhőbe bekészítsék.

**Az automatikus kiépítés nem érhető el az OIDC-alapú alkalmazáson**

Ha létrehoz egy alkalmazást, a vállalati alkalmazásokban a megfelelő egyszerű szolgáltatásnév nem lesz engedélyezve az automatikus felhasználó-kiépítés során. Ha több szervezet számára kívánja használni, vagy egy második, nem katalógusból álló alkalmazást szeretne kiépíteni, az alkalmazást fel kell vennie a katalógusba. 

**A kiépítési intervallum rögzített**

A kiépítési ciklusok közötti [idő](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) jelenleg nem konfigurálható. 

**A cél alkalmazásból az Azure AD-be való áttérés nem változik**

Az alkalmazás-kiépítési szolgáltatás nem ismeri a külső alkalmazásokban végrehajtott módosításokat. Így nem kerül sor a visszaállításra. Az alkalmazás-kiépítési szolgáltatás az Azure AD-ben végrehajtott módosításokra támaszkodik. 

**Váltás az összes szinkronizálásról a kijelölt nem működő szinkronizálásra**

Miután módosította a hatókört a "Sync all" értékről a "Sync Assigned" elemre, győződjön meg arról is, hogy a módosítás érvénybe léptetéséhez újraindítás szükséges. Az újraindítást a felhasználói felületen végezheti el.

**A kiépítési ciklus a befejezésig folytatódik**

A kiépítés `enabled = off` vagy a Leállítás megszakadása esetén az aktuális kiépítési ciklus a befejezésig folytatódni fog. A szolgáltatás addig nem hajtja végre a jövőbeli ciklusok végrehajtását, amíg újra be nem kapcsolja az üzembe helyezést.

**Nem kiépített csoport tagja**

Ha egy csoport hatókörben van, és egy tag kívül esik a hatókörön, a rendszer kiépíti a csoportot. A hatókörön kívüli felhasználó nem lesz kiépítve. Ha a tag visszatér a hatókörbe, a szolgáltatás nem ismeri fel azonnal a változást. A kiépítés újraindítása a probléma megoldásához vezet. Javasoljuk, hogy a szolgáltatás rendszeres újraindítása után ellenőrizze, hogy az összes felhasználó megfelelően van-e kiépítve.  

**A kezelő nincs kiépítve**

Ha a felhasználó és a felettesük is a kiépítés hatókörében van, akkor a szolgáltatás kiépíti a felhasználót, majd frissíti a kezelőt. Ha azonban a felhasználó a hatókörén belül van, és a felettes nem a hatókörön kívül esik, a felhasználót a felettesi hivatkozás nélkül fogjuk kiépíteni. Ha a felettes hatókörbe kerül, a kezelői hivatkozás addig nem frissül, amíg újra nem indítja az üzembe helyezést, és a szolgáltatás újra kiértékeli az összes felhasználót. 

## <a name="next-steps"></a>Következő lépések
- [Az üzembe helyezés menete](how-provisioning-works.md)
