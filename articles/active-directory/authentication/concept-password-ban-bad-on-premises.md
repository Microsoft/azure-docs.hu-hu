---
title: Az Azure AD jelszóvédelem – Azure Active Directory
description: Az Azure AD Jelszóvédelem letiltása a gyenge jelszavakat a helyszíni Active Directoryban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 644054960e12979c231bbf50a5979bc12d343f89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694767"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Az Azure AD jelszóvédelem a Windows Server Active Directory kényszerítése

Az Azure AD jelszóvédelem funkciója, amely növeli a szervezeten belüli jelszóházirendeket. A helyszíni üzembe helyezés a jelszavas védelem is a globális és egyéni le van tiltva – jelszó listák az Azure ad-ben tárolt használ. Az azonos ellenőrzi a helyszíni, az Azure AD a felhőalapú módosításokat végez el.

## <a name="design-principles"></a>Tervezési alapelvek

Az Azure AD jelszóvédelem szem előtt ezeket az alapelveket a célja:

* Tartományvezérlők soha nem kell közvetlenül kommunikáljon az internettel.
* Nincs új hálózati portok nyitva vannak azokon a tartományvezérlőkön.
* Az Active Directory-séma módosítása nélkül szükség. A szoftver használja a meglévő Active Directory **tároló** és **serviceConnectionPoint** adatbázisséma objektumaiban.
* Nincs minimális Active Directory tartomány vagy erdő működési szintje (DFL/FFL) nem szükséges.
* A szoftver nem hozzon létre vagy fiókok az Active Directory-tartományok, amely megvédi a szükséges.
* Felhasználói tiszta szöveges jelszavak soha ne hagyja a tartományvezérlő, a jelszó érvényesítése működés közben, vagy a tetszőleges időpontban.
* A szoftver nem függ más Azure AD-funkciók; például az Azure AD Jelszókivonat-szinkronizálás nem kapcsolódik, és nem szükséges ahhoz, hogy az Azure AD jelszóvédelem függvény.
* Támogatott növekményes üzembe helyezést, azonban a jelszóházirend csak kényszerítve van, ahol a Domain Controller ügynök (DC ügynök) telepítve van. További részleteket a következő témakörben talál.

## <a name="incremental-deployment"></a>Növekményes üzembe helyezést

Az Azure AD jelszóvédelem támogatja a növekményes üzembe helyezést az Active Directory-tartományban lévő tartományvezérlők között, de fontos tudni, Mi ez igazán azt jelenti, és a kompromisszumot kínál a rendszer.

Az Azure AD jelszó DC védelmi ügynök nevű szoftvert csak ellenőrizheti jelszavak, ha telepítve van a tartományvezérlőn, és csak a jelszó módosítására, az adott tartományvezérlőhöz küldött. Nincs lehetőség, hogy mely tartományvezérlők a Windows ügyfél gépek feldolgozásának felhasználói jelszó módosítására közül választ. Annak érdekében, hogy garantálja a következetes viselkedését és univerzális jelszó védelmi biztonság kényszerítése, a tartományvezérlő ügynök szoftvert telepítenie kell egy tartomány összes tartományvezérlőjére.

Számos szervezetben érdemes az Azure AD jelszóvédelem részhalmazán a tartományvezérlők teljes körű telepítésére végrehajtásakor.%n előtt gondosan tesztelés. Azure AD jelszóvédelem támogatja a részleges központi telepítési, például a tartományvezérlő ügynök nevű szoftvert egy adott tartományvezérlő aktívan ellenőrzi a jelszavak akkor is, ha a tartomány más tartományvezérlők nem rendelkeznek telepített DC ügynökszoftver. Az ilyen típusú részleges központi telepítések nincsenek biztonságos, és más, nem ajánlott tesztelési célokra.

## <a name="architectural-diagram"></a>Architekturális diagramja

Fontos, az alapul szolgáló tervezési és a függvény fogalmak megértéséhez, az Azure AD jelszavas védelem az Active Directory a helyszíni környezetben üzembe helyezése előtt. Az alábbi ábrán látható, hogyan működik a jelszavas védelem összetevői együtt:

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Az Azure AD-jelszó védelme Proxy szolgáltatás minden olyan tartományhoz csatlakoztatott gép az aktuális Active Directory-erdő futtat. Az elsődleges célja tartományvezérlők az Azure AD-jelszó házirend letöltési kérelmek továbbításához. Majd visszaadja a válaszok az Azure ad-ből a tartományvezérlőre.
* A jelszószűrő DLL, a tartományvezérlő-ügynök a felhasználói jelszó-érvényesítési kéréseket fogad az operációs rendszer. Azt továbbítja őket a tartományvezérlő ügynök szolgáltatás, amely helyben fut a tartományvezérlő.
* A tartományvezérlő Agent szolgáltatást a jelszavas védelem jelszó-érvényesítési kéréseket fogad a jelszószűrő DLL, a tartományvezérlő-ügynök. Azt az aktuális (helyben érhető el) jelszóházirend használatával dolgozza fel, és visszaadja az eredményt: *átadni* vagy *sikertelen*.

## <a name="how-password-protection-works"></a>Jelszavas védelem működése

Minden egyes Azure AD-jelszó védelme Proxy szolgáltatáspéldány azonosítja magát a tartományvezérlőn, az erdő hozzon létre egy **serviceConnectionPoint** objektumot az Active Directory.

Minden egyes tartományvezérlő ügynökszolgáltatás jelszavas védelmet is létrehoz egy **serviceConnectionPoint** objektumot az Active Directory. Ez az objektum elsősorban a jelentéskészítés és a diagnosztika szolgál.

A tartományvezérlő-ügynökszolgáltatás elindítása az Azure AD új jelszó házirend letöltése felelős. Az első lépés az, hogy keresse meg az Azure AD-jelszó védelme Proxy szolgáltatás az erdő proxy lekérdezésével **serviceConnectionPoint** objektumokat. Egy elérhető proxy szolgáltatás található, ha a tartományvezérlő-ügynök egy jelszó házirend letöltése kérést küld a proxy szolgáltatást. A proxy szolgáltatást ezután elküldi a kérést az Azure ad-hez. A proxy szolgáltatás majd visszaadja a választ a tartományvezérlő Agent szolgáltatást.

A tartományvezérlő Agent szolgáltatást az Azure AD új jelszó szabályzat kap, miután a szolgáltatás a tartomány gyökerében egy dedikált mappában tárolja a szabályzat *sysvol* mappamegosztáshoz. A tartományvezérlő-ügynökszolgáltatás is figyeli ezt a mappát, abban az esetben, ha újabb házirendek replikálni a többi tartományvezérlő ügynökszolgáltatások a tartományban.

A tartományvezérlő-ügynökszolgáltatás mindig egy új házirendet a szolgáltatás indításakor kér. Miután a tartományvezérlő-ügynökszolgáltatás elindult, ellenőrzi az aktuális helyileg elérhető házirend óránként korát. A szabályzat a régebbi, mint egy óra, a tartományvezérlő ügynök kér egy új házirendet a proxy szolgáltatáson keresztül az Azure AD előzőekben leírtak szerint. Ha a jelenlegi házirend nincs egy óránál régebbi, a tartományvezérlő-ügynök továbbra is használja az adott házirendnek.

Minden alkalommal, amikor egy Azure AD jelszó védelmi jelszó házirend letöltődik minden, az adott házirendnek csak egy bérlőt. Más szóval jelszóházirendek mindig a Microsoft globális le van tiltva – jelszó és listája, a bérlőnkénti egyéni le van tiltva – jelszó kombinációja.

A tartományvezérlő-ügynök TCP protokollon keresztül kommunikál a proxy-szolgáltatás RPC-n keresztül. A proxy szolgáltatást figyeli a hívásokat a statikus vagy dinamikus RPC port, a konfigurációtól függően.

A tartományvezérlő ügynök soha nem egy hálózati elérhető porton figyel.

A proxy szolgáltatás soha nem hívja a tartományvezérlő Agent szolgáltatást.

A proxy szolgáltatás az állapot nélküli. Soha nem gyorsítótárazza a szabályzatok, vagy más Azure-ból letöltött.

A tartományvezérlő Agent szolgáltatást a legújabb elérhető helyi jelszóházirend mindig használja a jelszó kiértékelése. Ha nincs jelszóházirend nem érhető el a helyi tartományvezérlőn, a jelszó automatikusan elfogadja. Ha ez történik, egy üzenet figyelmezteti a felhasználót a rendszergazda rendszer naplózza.

Az Azure AD jelszóvédelem egy valós idejű alkalmazás házirendmotor nem. Amikor jelszó házirend konfigurációs módosításakor az Azure ad-ben, és amikor, amely eléri módosítása és kényszerítve van az összes olyan tartományvezérlőn közötti késleltetés is lehet.

Az Azure AD jelszóvédelem funkcionál a meglévő Active Directory jelszóházirendek, nem helyettesíti a kiegészítést. Ez magában foglalja a bármely más jelszót. 3. fél szűrő dll telepíthető. Az Active Directory mindig szükséges, hogy a jelszó érvényesítése az összes összetevő fogadja el a jelszó elfogadása előtt.

## <a name="foresttenant-binding-for-password-protection"></a>Erdő vagy a bérlőjéhez kötést a jelszavas védelem

Az Azure AD jelszavas védelem az Active Directory-erdő telepítése az adott erdőben, az Azure AD-regisztrációs igényel. Minden üzembe helyezett proxy szolgáltatás is regisztrálni kell az Azure ad-ben. Egy adott erdő és a proxy regisztrációk tartoznak az Azure AD-bérlőjéhez, amely implicit módon azonosíthatók a regisztráció során használt hitelesítő adatokkal.

Az Active Directory-erdő és a egy erdőn belül minden üzembe helyezett alkalmazásproxy-szolgáltatásokat a ugyanazt bérlőhöz regisztrálva kell lennie. Szeretné, hogy az Active Directory-erdőben vagy bármely alkalmazásproxy-szolgáltatásokat, hogy az erdő regisztrálja a másik Azure AD-bérlők nem támogatott. Az ilyen egy rosszul konfigurált telepítési tünetei a jelszóházirendek letöltése nem.

## <a name="download"></a>Letöltés

A két szükséges ügynök telepítőcsomagjai az Azure AD jelszóvédelem érhetők el a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="next-steps"></a>További lépések
[Azure AD jelszóvédelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
