---
title: Az ingyenes vagy próbaverziós SaaS-ajánlat kezdőlapjának összeállítása a kereskedelmi piactéren
description: Megtudhatja, hogyan hozhat létre egy kezdőlapot az ingyenes vagy próbaverziós SaaS-ajánlathoz.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: e7cee47e90e6484a4258ba82e47af03725c41d34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559290"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Az ingyenes vagy próbaverziós SaaS-ajánlat kezdőlapjának összeállítása a kereskedelmi piactéren

Ez a cikk végigvezeti egy, a Microsoft kereskedelmi piactéren árusított ingyenes vagy próbaverziós SaaS-alkalmazás kezdőlapjának létrehozásán.

## <a name="overview"></a>Áttekintés

Azt is megteheti, hogy a kezdőlapot "lobbyként" látja el a szolgáltatott szoftverként (SaaS). Miután az ügyfél úgy dönt, hogy beolvassa az alkalmazást, a kereskedelmi piactér átirányítja őket a kezdőlapra, hogy aktiválja és konfigurálja az előfizetését az SaaS-alkalmazásra. Amikor létrehoz egy szolgáltatott szoftver (SaaS) ajánlatot, a partner Centerben megadhatja, hogy a [Microsofton keresztül](plan-saas-offer.md#listing-options)kíván-e eladni. Ha csak a Microsoft kereskedelmi piactéren kívánja felsorolni ajánlatát, és nem a Microsofton keresztül értékesít, megadhatja, hogy a potenciális ügyfelek hogyan használhatják az ajánlatot. Ha engedélyezi a **Letöltés most (ingyenes)** vagy az **ingyenes próbaverzió** listázása beállítást, meg kell adnia egy Kezdőlap URL-címét, amelyhez a felhasználó hozzáférhet az ingyenes előfizetéshez vagy próbaverzióhoz.

A Kezdőlap célja egyszerűen a felhasználó fogadása, így aktiválhatja az ingyenes próbaverziót vagy az ingyenes előfizetést. A Azure Active Directory (Azure AD) és a Microsoft Graph használatával engedélyezheti az egyszeri bejelentkezést (SSO) a felhasználó számára, és fontos információkat kaphat arról, hogy miként aktiválhatja az ingyenes próbaverziót vagy az ingyenes előfizetést, beleértve a nevét, e-mail-címét és a szervezetét.

Mivel az előfizetés aktiválásához szükséges információk korlátozottak, és az Azure AD és a Microsoft Graph biztosítanak, nem kell olyan információt kérnie, amely több mint alapszintű beleegyezett. Ha olyan felhasználói adatokra van szüksége, amelyek további beleegyezett az alkalmazáshoz, akkor az előfizetés aktiválása után kérnie kell ezt az információt. Ez lehetővé teszi a felhasználó számára a zökkenőmentes előfizetés-aktiválást, és csökkenti a megszüntetés kockázatát.

A Kezdőlap általában a következő információkat és listaelem-beállításokat tartalmazza:

- Adja meg az ingyenes próbaverzió vagy az ingyenes előfizetés nevét és részleteit. Itt adhatja meg például a próbaverzió használati korlátait vagy időtartamát.
- Adja meg a felhasználó fiókjának adatait, beleértve a vezetéknevét és a vezetéknevét, a céget és az e-maileket.
- Kérje meg a felhasználót, hogy erősítse meg vagy cserélje le a különböző fiók adatait.
- Az aktiválás után kövesse a felhasználót a következő lépésekben. Például fogadjon egy üdvözlő e-mailt, kezelje az előfizetést, kérjen támogatást vagy olvassa el a dokumentációt.

A cikk következő részei végigvezetik a Kezdőlap létrehozásának folyamatán:

1. [Hozzon létre egy Azure ad-alkalmazás regisztrációját](#create-an-azure-ad-app-registration) a kezdőlapon.
2. Az alkalmazás [kiindulási pontként használja a kód mintát](#use-a-code-sample-as-a-starting-point) .
3. Az [azonosító jogkivonatban kódolt jogcímek adatainak beolvasása](#read-information-from-claims-encoded-in-the-id-token)az Azure ad-től a bejelentkezés után kapott, a kérelemmel elküldött információ.
4. [A Microsoft Graph API](#use-the-microsoft-graph-api) -val további információkat gyűjthet, igény szerint.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrálásának létrehozása

A kereskedelmi piactér teljes mértékben integrálva van az Azure AD-vel. A felhasználók egy [Azure ad-fiókkal vagy Microsoft-fiók (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)hitelesítve érkeznek meg a piactéren. Miután beszerezte az ingyenes vagy ingyenes próbaverziós előfizetést a listáról, a felhasználó a kereskedelmi piactérről a Kezdőlap URL-címére jut, hogy aktiválja és felügyelje az előfizetését az SaaS-alkalmazásra. Engedélyeznie kell, hogy a felhasználó bejelentkezzen az alkalmazásba az Azure AD SSO segítségével. (A Kezdőlap URL-címe az ajánlat [technikai konfiguráció](plan-saas-offer.md#technical-information) lapján van megadva.

Az identitás használatának első lépése annak biztosítása, hogy a Kezdőlap regisztrálva legyen Azure AD-alkalmazásként. Az alkalmazás regisztrálása lehetővé teszi, hogy az Azure AD használatával hitelesítse a felhasználókat, és hozzáférést Kérjen a felhasználói erőforrásokhoz. Ez az alkalmazás definíciójának tekinthető, amely lehetővé teszi, hogy a szolgáltatás tudja, hogyan kell jogkivonatokat kibocsátani az alkalmazásnak az alkalmazás beállításai alapján.

### <a name="register-a-new-application-using-the-azure-portal"></a>Új alkalmazás regisztrálása az Azure Portal használatával

Az első lépésekhez kövesse az [új alkalmazások regisztrálásának](../active-directory/develop/quickstart-register-app.md)utasításait. Ahhoz, hogy a többi vállalattól származó felhasználók meglátogassák az alkalmazást, a **fiókokat a szervezeti címtárban (bármely Azure ad-címtár – több-bérlős) és személyes Microsoft-fiókkal (például Skype vagy Xbox)** kell választania, amikor megkérdezi, ki használhatja az alkalmazást

Ha le szeretné kérdezni a Microsoft Graph API- [t, konfigurálja az új alkalmazást a webes API-k eléréséhez](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Ha kijelöli az alkalmazás API-engedélyeit, a felhasználó alapértelmezett értéke **. az olvasás** elég ahhoz, hogy alapvető információkat gyűjtsön a felhasználóról, hogy a bevezetési folyamat zökkenőmentes és automatikus legyen. Ne igényeljen **rendszergazdai** jogosultsággal jelölt API-engedélyeket, mivel ez letiltja a nem rendszergazda felhasználók számára a Kezdőlap meglátogatását.

Ha emelt szintű engedélyekre van szüksége a bevezetési vagy kiépítési folyamat részeként, érdemes lehet az Azure AD [növekményes hozzájárulási](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) funkcióit használni, hogy a piactéren eljuttatott összes felhasználó képes legyen a kezdeti kommunikációra a kezdőlapon.

## <a name="use-a-code-sample-as-a-starting-point"></a>Mintakód használata kiindulási pontként

A Microsoft számos olyan minta alkalmazást adott meg, amelyek egy egyszerű webhelyet implementálnak az Azure AD-bejelentkezéssel. Miután az alkalmazás regisztrálva lett az Azure AD-ben, a gyors üzembe helyezési **panel az általános** alkalmazási típusok és a fejlesztői verem (1. ábra) listáját kínálja. Válassza ki a környezetének megfelelőt, és kövesse a letöltésre és telepítésre vonatkozó utasításokat.

***1. ábra: rövid útmutató a Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="A Azure Portal rövid útmutató paneljének bemutatása.":::

A kód letöltése és a fejlesztési környezet beállítása után módosítsa az alkalmazás konfigurációs beállításait úgy, hogy az tükrözze az előző eljárásban rögzített alkalmazás-azonosítót, a bérlő AZONOSÍTÓját és az ügyfél titkos kulcsát. Vegye figyelembe, hogy a pontos lépések eltérhetnek attól függően, hogy melyik mintát használja.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Az azonosító jogkivonatban kódolt jogcímek adatainak beolvasása

Az [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) folyamat részeként az Azure ad egy [azonosító jogkivonatot](../active-directory/develop/id-tokens.md) ad hozzá a kéréshez, amikor a felhasználót a kezdőlapra küldi. Ez a jogkivonat több alapvető információt tartalmaz, amelyek hasznosak lehetnek az aktiválási folyamat során, beleértve az ebben a táblázatban látható információkat is.

| Érték | Leírás |
| ------------ | ------------- |
| aud | A token célközönsége. Ebben az esetben meg kell egyeznie az alkalmazás-AZONOSÍTÓval, és ellenőrizni kell. |
| preferred_username | A látogató felhasználó elsődleges felhasználóneve. Ez lehet egy e-mail-cím, telefonszám vagy más azonosító. |
| e-mail | A felhasználó e-mail-címe. Vegye figyelembe, hogy ez a mező üres lehet. |
| name | Az ember által olvasható érték, amely azonosítja a jogkivonat tárgyát. Ebben az esetben ez lesz a felhasználó neve. |
| OID | Azonosító a Microsoft Identity rendszerben, amely egyedileg azonosítja a felhasználót az alkalmazások között. A Microsoft Graph egy adott felhasználói fiók azonosító tulajdonsága adja vissza ezt az értéket. |
| TID | A felhasználót az Azure AD-bérlőt jelölő azonosító. MSA-identitás esetén ez mindig a következő lesz: `9188040d-6c67-4c5b-b112-36a304b66dad` . További információt a következő szakaszban található megjegyzésekben talál: Microsoft Graph API használata. |
| Sub | Azonosító, amely egyedileg azonosítja a felhasználót ebben az adott alkalmazásban. |
|||

## <a name="use-the-microsoft-graph-api"></a>A Microsoft Graph API használata

Az azonosító jogkivonat alapinformációkat tartalmaz a felhasználó azonosításához, de az aktiválási folyamat további részleteket igényelhet – például a felhasználó vállalata – a bevezetési folyamat befejezéséhez. A [Microsoft Graph API](/graph/use-the-api) -val kérheti ezeket az adatokat, hogy ne kényszerítse a felhasználót, hogy adja meg újra ezeket a részleteket. Az általános jogú **felhasználó. az olvasási** engedélyek alapértelmezés szerint a következő információkat tartalmazzák:

| Érték | Leírás |
| ------------ | ------------- |
| displayName | A felhasználó címjegyzékében megjelenő név. |
| givenName | A felhasználó vezetékneve. |
| jobTitle | A felhasználó beosztása. |
| Levelezés | A felhasználó SMTP-címe. |
| Mobiltelefon | A felhasználó elsődleges mobil telefonszáma. |
| preferredLanguage | ISO 639-1-kód a felhasználó által előnyben részesített nyelvhez. |
| surname | A felhasználó vezetékneve. |
|||

További tulajdonságok – például a felhasználó vállalatának neve vagy a felhasználó helye (ország) – kiválasztható a kérelembe való felvételhez. További részletekért lásd: [a felhasználói erőforrástípus tulajdonságai](/graph/api/resources/user#properties).

Az Azure AD-ben regisztrált alkalmazások többsége delegált engedélyekkel rendelkezik, hogy beolvassa a felhasználó adatait a vállalat Azure AD-bérlője számára. Az adott információhoz Microsoft Graph összes kérelemhez hozzáférési jogkivonatot kell csatolni a hitelesítéshez. A hozzáférési jogkivonat létrehozásával kapcsolatos konkrét lépések a használt technológiai veremtől függenek, de a mintakód egy példát is tartalmaz majd. További információ: [hozzáférés beszerzése egy felhasználó nevében](/graph/auth-v2-user).

> [!NOTE]
> Az MSA-bérlőről (a bérlői AZONOSÍTÓval) származó fiókok `9188040d-6c67-4c5b-b112-36a304b66dad` nem adnak vissza több információt, mint amelyet már gyűjtöttek az azonosító jogkivonattal. Így kihagyhatja ezt a hívást a fiókok Graph API.

## <a name="next-steps"></a>Következő lépések
- [SaaS-ajánlat létrehozása a kereskedelmi piactéren](create-new-saas-offer.md)