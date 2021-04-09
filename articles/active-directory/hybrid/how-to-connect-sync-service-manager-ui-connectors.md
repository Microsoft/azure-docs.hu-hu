---
title: Az Azure AD Synchronization Service Manager felhasználói felületének összekötői | Microsoft Docs "
description: Ismerje meg a Azure AD Connect Synchronization Service Manager összekötők lapját.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319877"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Összekötők használata a Azure AD Connect Sync Service Manager

![A szinkronizálási Service Manager bemutató képernyőkép.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Az összekötők lapon minden olyan rendszer felügyelhető, amelyhez a Szinkronizáló motor csatlakozik.

## <a name="connector-actions"></a>Összekötő műveletei
| Művelet | Megjegyzés |
| --- | --- |
| Létrehozás |Ne használja. További AD-erdőkhöz való csatlakozáshoz használja a telepítővarázslót. |
| Tulajdonságok |Tartomány-és OU-szűréshez használatos. |
| [Törlés](#delete) |Az összekötő területének vagy az erdőhöz való csatlakozás törlésére szolgál. |
| [Futtatási profilok konfigurálása](#configure-run-profiles) |A tartományi szűrés kivételével itt nem kell konfigurálnia. Ezt a műveletet használhatja a már konfigurált futtatási profilok megjelenítéséhez. |
| Futtatás |Egy profil egyszeri futtatásának elindítására szolgál. |
| Leállítás |Egy profilt jelenleg futtató összekötő leállítása. |
| Összekötő exportálása |Ne használja. |
| Összekötő importálása |Ne használja. |
| Összekötő frissítése |Ne használja. |
| Séma frissítése |Frissíti a gyorsítótárazott sémát. Ehelyett inkább a telepítési varázslóban található beállítást érdemes használni, mivel ez frissíti a szinkronizálási szabályokat is. |
| [Keresési összekötő területe](#search-connector-space) |Objektumok keresésére, valamint egy objektum és annak az adott rendszeren keresztüli követésére szolgál. |

### <a name="delete"></a>Törlés
A törlési művelet két különböző dolog esetében használatos.  
![Képernyőkép: az "összekötő törlése" ablak "csak az összekötő-terület törlése" beállítással látható.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Az **összekötő törlése lehetőség csak** az összes adathalmazt távolítja el, de megtartja a konfigurációt.

Az **összekötő és összekötő terület törlése** lehetőséggel törlődik az adathalmaz és a konfiguráció. Ezt a beállítást akkor kell használni, ha már nem szeretne csatlakozni egy erdőhöz.

Mindkét lehetőség szinkronizálja az összes objektumot, és frissíti a metaverse-objektumokat. Ez a művelet hosszú ideig futó művelet.

### <a name="configure-run-profiles"></a>Futtatási profilok konfigurálása
Ez a beállítás lehetővé teszi az összekötőhöz konfigurált futtatási profilok megtekintését.

![Képernyőfelvétel: a "futtatási profilok konfigurálása" ablak a "különbözeti Importálás" beállítással.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Keresési összekötő területe
A keresési összekötő területe művelet hasznos lehet objektumok kereséséhez és az adathibák elhárításához.

![A "keresési összekötő területe" ablakot megjelenítő képernyőkép.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Először válasszon ki egy **hatókört**. Kereshet az objektum (RDN, DN, Anchor, Sub-Tree) vagy az objektum állapota alapján (az összes többi lehetőség).  
![A "hatókör" legördülő menüt megjelenítő képernyőkép.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Ha például Sub-Tree keresést végez, az összes objektum egy szervezeti egységben fog megjelenni.  
![A "Sub-Tree" keresési példát bemutató képernyőkép.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Ebből a rácsból kiválaszthat egy objektumot, kiválaszthatja a **tulajdonságokat**, és [követheti azt](tshoot-connect-object-not-syncing.md) a forrás-összekötő területéről, a metaverse használatával és a cél összekötő területéhez.

### <a name="changing-the-ad-ds-account-password"></a>Az AD DS-fiók jelszavának módosítása
Ha megváltoztatja a fiók jelszavát, a szinkronizálási szolgáltatás többé nem fogja tudni importálni/exportálni a helyszíni AD-t.   A következők jelenhetnek meg:

- Az AD-összekötő importálási/exportálási lépése a "No-Start-hitelesítő adatok" hibával meghiúsul.
- A Windows Eseménynapló alatt az alkalmazás eseménynaplója hibát tartalmaz a 6000-es azonosítójú eseménynél, és az "a felügyeleti ügynök" contoso.com "üzenetet nem sikerült futtatni, mert a hitelesítő adatok érvénytelenek voltak."

A probléma megoldásához frissítse a AD DS felhasználói fiókot a következő paranccsal:


1. Indítsa el a Synchronization Service Managert (START → szinkronizációs szolgáltatás).
</br>![Service Manager szinkronizálása](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Nyissa meg az **Összekötők** lapot.
3. Válassza ki azt az AD-összekötőt, amely a AD DS fiók használatára van konfigurálva.
4. A műveletek területen válassza a **Tulajdonságok** lehetőséget.
5. Az előugró párbeszédpanelen válassza a kapcsolódás Active Directory erdőhöz lehetőséget:
6. Az erdő neve jelzi a megfelelő helyszíni AD-t.
7. A Felhasználónév jelzi a szinkronizáláshoz használt AD DS fiókot.
8. Adja meg a AD DS fiók új jelszavát a jelszó szövegmezőben ![ Azure ad Connect Sync encryption Key segédprogram](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Az új jelszó mentéséhez és a szinkronizálási szolgáltatás újraindításához kattintson az OK gombra, hogy eltávolítsa a régi jelszót a memória-gyorsítótárból.



## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
