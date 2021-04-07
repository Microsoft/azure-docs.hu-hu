---
title: Nem érhető el ez a vállalati alkalmazáshiba az App proxy alkalmazással
description: Az Azure AD Application Proxy-alkalmazások gyakori hozzáférési problémáinak elhárítása.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9fb68643c48c685194fa7ba1f1e5050d2d3cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99254931"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>"Nem lehet hozzáférni a vállalati alkalmazáshoz" hibaüzenet alkalmazásproxy-alkalmazás használatakor

Ez a cikk segítséget nyújt a "Ez a vállalati alkalmazás nem érhető el" hibaüzenet gyakori hibáinak elhárításában egy Azure AD Application Proxy-alkalmazásnál.

## <a name="overview"></a>Áttekintés

Ha ezt a hibát látja, keresse meg az állapotkódot a hiba oldalon. A kód valószínűleg a következő állapotkódok egyike:

- **Átjáró időtúllépése**: az alkalmazásproxy szolgáltatás nem tudja elérni az összekötőt. Ez a hiba általában az összekötő-hozzárendeléssel, az összekötővel vagy az összekötőn található hálózatkezelési szabályokkal kapcsolatos problémát jelez.
- **Hibás átjáró**: az összekötő nem tudja elérni a háttérbeli alkalmazást. Ez a hiba az alkalmazás helytelen konfigurációját jelezheti.
- **Tiltott**: a felhasználó nem jogosult az alkalmazás elérésére. Ez a hiba akkor fordulhat elő, ha a felhasználó nincs hozzárendelve az alkalmazáshoz Azure Active Directoryban, vagy ha a háttérben a felhasználónak nincs engedélye az alkalmazás elérésére.

A kód megkereséséhez tekintse meg a "állapotkód" mezőhöz tartozó hibaüzenet bal alsó sarkában található szöveget. Tekintse meg az oldal alján található további tippeket is.

![Példa: átjáró időtúllépési hibája](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

A hibák kiváltó okának elhárításával és a javasolt javításokkal kapcsolatos további részletekért tekintse meg az alábbi megfelelő szakaszt.

## <a name="gateway-timeout-errors"></a>Átjáró időtúllépési hibái

Az átjáró időtúllépése akkor történik meg, amikor a szolgáltatás megpróbál elérni az összekötőt, és nem képes az időkorlát-időszakon belül. Ezt a hibát általában egy, a munkaösszekötők nélküli összekötő-csoporthoz rendelt alkalmazás okozza, vagy az összekötőhöz szükséges egyes portok nincsenek megnyitva.

## <a name="bad-gateway-errors"></a>Hibás átjáróval kapcsolatos hibák

A hibás átjáró hibája azt jelzi, hogy az összekötő nem tudja elérni a háttérbeli alkalmazást. Győződjön meg arról, hogy a megfelelő alkalmazást tette közzé. A hibát okozó gyakori hibák a következők:

- A belső URL-cím elírása vagy hibája
- Nem teszi közzé az alkalmazás gyökerét. Például közzététel, `http://expenses/reimbursement` de a hozzáférés kipróbálása `http://expenses`
- Problémák a Kerberos által korlátozott delegálás (KCD) konfigurációjával
- Problémák a háttérbeli alkalmazással

## <a name="forbidden-errors"></a>Tiltott hibák

Ha tiltott hiba jelenik meg, a felhasználó nem lett hozzárendelve az alkalmazáshoz. Ez a hiba lehet Azure Active Directory vagy a háttérbeli alkalmazásban.

Ha szeretné megtudni, hogyan rendelhet hozzá felhasználókat az alkalmazáshoz az Azure-ban, tekintse meg a [konfigurációs dokumentációt](application-proxy-add-on-premises-application.md#test-the-application).

Ha megerősíti, hogy a felhasználó hozzá van rendelve az alkalmazáshoz az Azure-ban, ellenőrizze a felhasználói konfigurációt a háttérbeli alkalmazásban. Ha Kerberos által korlátozott delegálást vagy integrált Windows-hitelesítést használ, tekintse meg az irányelvek KCD-hibakeresési oldalát.

## <a name="check-the-applications-internal-url"></a>Az alkalmazás belső URL-címének megtekintése

Első lépésként nyissa meg a belső URL-címet úgy, hogy megnyitja az alkalmazást a **vállalati alkalmazásokkal**, majd kiválasztja az **alkalmazásproxy menüt.** Ellenőrizze, hogy a belső URL-cím a helyszíni hálózatból az alkalmazás eléréséhez használt-e.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Győződjön meg arról, hogy az alkalmazás egy működő összekötő-csoporthoz van rendelve

Annak ellenőrzése, hogy az alkalmazás hozzá van-e rendelve egy működő összekötő-csoporthoz:

1. Nyissa meg az alkalmazást a portálon a **Azure Active Directory**, majd a **vállalati alkalmazások**, majd az **összes alkalmazás** lehetőségre kattintva. Nyissa meg az alkalmazást, majd válassza az **alkalmazásproxy** elemet a bal oldali menüben.
1. Tekintse meg az összekötő csoport mezőt. Ha nincsenek aktív összekötők a csoportban, a rendszer figyelmeztetést jelenít meg. Ha nem jelennek meg figyelmeztetések, a bekapcsolásával ellenőrizze, hogy az összes [szükséges port](application-proxy-add-on-premises-application.md) engedélyezve van-e.
1. Ha nem megfelelő összekötő-csoport jelenik meg, a legördülő menüben válassza ki a megfelelő csoportot, és erősítse meg, hogy többé nem jelenik meg figyelmeztetés. Ha a kívánt összekötő csoport látható, kattintson a figyelmeztető üzenetre az oldal összekötő-kezeléssel való megnyitásához.
1. Íme néhány módszer a további részletezéshez:

   - Aktív összekötő áthelyezése a csoportba: Ha rendelkezik olyan aktív összekötővel, amelynek a csoporthoz kell tartoznia, és a cél háttérbeli alkalmazáshoz tartozik, akkor áthelyezheti az összekötőt a hozzárendelt csoportba. Ehhez kattintson az összekötőre. Az "összekötő csoport" mezőben a legördülő listából válassza ki a megfelelő csoportot, majd kattintson a Mentés gombra.
   - Új összekötő letöltése a csoport számára: ezen a lapon megtekintheti az [új összekötő letöltésére](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)mutató hivatkozást. Telepítse az összekötőt egy olyan gépre, amelyen közvetlen a háttérbeli alkalmazás található. Az összekötő általában ugyanarra a kiszolgálóra van telepítve, mint az alkalmazás. A Connector letöltése hivatkozásra kattintva letöltheti az összekötőt a célszámítógép számára. Ezután kattintson az összekötőre, és az "összekötő csoport" legördülő lista használatával győződjön meg arról, hogy a megfelelő csoporthoz tartozik.
   - Inaktív összekötő vizsgálata: Ha egy összekötő inaktívként jelenik meg, nem tudja elérni a szolgáltatást. Ez a hiba általában a blokkolt szükséges portok miatt fordul elő. A probléma megoldásához a következő lépésekkel ellenőrizheti, hogy az összes szükséges port engedélyezve van-e.

A lépések végrehajtása után ellenőrizze, hogy az alkalmazás hozzá van-e rendelve munkaösszekötővel rendelkező csoporthoz, majd tesztelje újra az alkalmazást. Ha továbbra sem működik, folytassa a következő szakasszal.

## <a name="check-all-required-ports-are-open"></a>Győződjön meg arról, hogy az összes szükséges port meg van nyitva

Győződjön meg arról, hogy az összes szükséges port meg van nyitva. A szükséges portokért tekintse meg a portok megnyitása szakaszt az [oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az Application proxyn keresztül Azure Active Directory](application-proxy-add-on-premises-application.md). Ha az összes szükséges port meg van nyitva, lépjen a következő szakaszra.

## <a name="check-for-other-connector-errors"></a>Más összekötők hibáinak keresése

Ha a fentiek egyike sem oldja meg a problémát, a következő lépés az összekötővel kapcsolatos problémák vagy hibák megkeresése. A [hibaelhárítási dokumentumban](./application-proxy-troubleshoot.md#connector-errors)néhány gyakori hibát láthat.

A hibák azonosításához közvetlenül az összekötő naplófájljaiban is kereshet. A hibaüzenetek számos konkrét javaslattal rendelkeznek a javítások megosztva. A naplók megtekintéséhez tekintse meg az [Összekötők dokumentációját](application-proxy-connectors.md#under-the-hood).

## <a name="additional-resolutions"></a>További megoldások

Ha a fentiek nem oldották meg a problémát, néhány különböző lehetséges ok van. A probléma azonosítása:

Ha az alkalmazás az integrált Windows-hitelesítés (IWA) használatára van konfigurálva, tesztelje az alkalmazást egyszeri bejelentkezés nélkül. Ha nem, lépjen a következő bekezdésre. Ha az alkalmazást egyszeri bejelentkezés nélkül szeretné megnézni, nyissa meg az alkalmazást a **vállalati alkalmazásokkal,** és lépjen az **egyszeri bejelentkezési** menüre. Módosítsa az "integrált Windows-hitelesítés" legördülő menüből az "Azure AD egyszeri bejelentkezés letiltva" lehetőséget.

Most nyisson meg egy böngészőt, és próbálja meg újból elérni az alkalmazást. Meg kell adnia a hitelesítést, és be kell szereznie az alkalmazást. Ha hitelesítésre van lehetősége, a probléma a Kerberos által korlátozott delegálás (KCD) konfigurációja, amely lehetővé teszi az egyszeri bejelentkezést. További információ: KCD-hibakeresési oldal.

Ha a hiba továbbra is fennáll, lépjen arra a gépre, amelyen az összekötő telepítve van, nyisson meg egy böngészőt, és próbálja meg elérni az alkalmazáshoz használt belső URL-címet. Az összekötő úgy viselkedik, mint egy másik ügyfél ugyanarról a gépről. Ha nem sikerül elérnie az alkalmazást, vizsgálja meg, hogy miért nem tudja elérni az alkalmazást, vagy használjon olyan kiszolgálón lévő összekötőt, amely képes hozzáférni az alkalmazáshoz.

Ha az adott gépről elérheti az alkalmazást, akkor az összekötővel kapcsolatos problémákat és hibákat kereshet. A [hibaelhárítási dokumentumban](application-proxy-troubleshoot.md#connector-errors)néhány gyakori hibát láthat. A hibák azonosításához közvetlenül az összekötő naplófájljaiban is kereshet. A hibaüzenetek nagy része a javításokra vonatkozó konkrétabb javaslatok megosztását is lehetővé teheti. A naplók megtekintésének megismeréséhez tekintse meg az [Összekötők dokumentációját](application-proxy-connectors.md#under-the-hood).

## <a name="next-steps"></a>Következő lépések

[Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)