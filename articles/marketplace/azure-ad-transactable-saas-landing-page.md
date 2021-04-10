---
title: A kereskedelmi piactéren felépítheti a transacter SaaS-ajánlat kezdőlapját
description: Megtudhatja, hogyan hozhat létre egy kezdőlapot a transacter SaaS-ajánlathoz.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549872"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>A kereskedelmi piactéren felépítheti a transacter SaaS-ajánlat kezdőlapját

Ebből a cikkből megtudhatja, hogyan hozhat létre egy, a Microsoft kereskedelmi piactéren értékesíthető, a felhasználható SaaS-alkalmazás kezdőlapját.

## <a name="overview"></a>Áttekintés

Azt is megteheti, hogy a kezdőlapot "lobbyként" látja el a szolgáltatott szoftverként (SaaS). Miután a vásárló előfizetett egy ajánlatot, a kereskedelmi piactér a kezdőlapra irányítja őket, hogy aktiválja és konfigurálja az előfizetését az SaaS-alkalmazásra. Ez egy megrendelés-visszaigazolási lépés, amely lehetővé teszi, hogy a vásárló megtekintse a megvásárolt adatokat, és erősítse meg a fiók adatait. A Azure Active Directory (Azure AD) és a Microsoft Graph használatával engedélyezheti az egyszeri bejelentkezést (SSO) a vevő számára, és fontos információkat kaphat arról, hogy az előfizetését erősítse és aktiválja, beleértve a nevét, az e-mail-címét és a szervezetét is.

Mivel az előfizetés aktiválásához szükséges információk korlátozottak, és az Azure AD és a Microsoft Graph biztosítanak, nem kell olyan információt kérnie, amely több mint alapszintű beleegyezett. Ha olyan felhasználói adatokra van szüksége, amelyek további beleegyezett az alkalmazáshoz, akkor az előfizetés aktiválása után kérnie kell ezt az információt. Ez lehetővé teszi a vevő számára a zökkenőmentes előfizetés-aktiválást, és csökkenti a megszüntetés kockázatát.

A Kezdőlap általában az alábbiakat tartalmazza:

- Adja meg az ajánlat és a megvásárolt csomag nevét, valamint a számlázási feltételeket.
- Adja meg a vásárló fiókjának adatait, beleértve a vezetéknevét, a céget és az e-maileket.
- Kérje meg a vevőt, hogy erősítse meg vagy cserélje le a különböző fiók adatait.
- Az aktiválás után a következő lépések elvégzése útmutató a vevő számára. Például fogadjon egy üdvözlő e-mailt, kezelje az előfizetést, kérjen támogatást vagy olvassa el a dokumentációt.

> [!NOTE]
> Az aktiválás után a vásárló a kezdőlapra is irányítva lesz. A vevő előfizetés aktiválása után az egyszeri bejelentkezést kell használnia ahhoz, hogy a felhasználó bejelentkezzen. Javasoljuk, hogy a felhasználót a fiók profiljához vagy a konfigurációs laphoz irányítsa.

A következő rész végigvezeti a Kezdőlap létrehozásának folyamatán:

1. [Hozzon létre egy Azure ad-alkalmazás regisztrációját](#create-an-azure-ad-app-registration) a kezdőlapon.
1. Az alkalmazás [kiindulási pontként használja a kód mintát](#use-a-code-sample-as-a-starting-point) .
1. [Két Azure ad-alkalmazással javíthatja a biztonságot az éles](#use-two-azure-ad-apps-to-improve-security-in-production)környezetben.
1. Oldja meg a piactér-beli [vásárlási azonosító tokent](#resolve-the-marketplace-purchase-identification-token) , amelyet a kereskedelmi piactér ad hozzá az URL-címhez.
1. Olvassa el a kéréssel elküldött, az Azure AD-től érkező, a bejelentkezés után kapott [azonosító jogkivonatban kódolt jogcímek információit](#read-information-from-claims-encoded-in-the-id-token).
1. [A Microsoft Graph API](#use-the-microsoft-graph-api) -val további információkat gyűjthet, igény szerint.

## <a name="create-an-azure-ad-app-registration"></a>Azure AD-alkalmazás regisztrálásának létrehozása

A kereskedelmi piactér teljes mértékben integrálva van az Azure AD-vel. A vásárlók [Azure ad-fiókkal vagy Microsoft-fiók (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology)hitelesítve érkeznek a piactéren. A vásárlást követően a vásárló a kereskedelmi piactérről a Kezdőlap URL-címére kerül, amely aktiválja és kezeli az SaaS-alkalmazás előfizetését. Engedélyeznie kell, hogy a vásárló bejelentkezzen az alkalmazásba az Azure AD SSO használatával. (A Kezdőlap URL-címe az ajánlat [technikai konfiguráció](plan-saas-offer.md#technical-information) lapján van megadva.

Az identitás használatának első lépése annak biztosítása, hogy a Kezdőlap regisztrálva legyen Azure AD-alkalmazásként. Az alkalmazás regisztrálása lehetővé teszi, hogy az Azure AD használatával hitelesítse a felhasználókat, és hozzáférést Kérjen a felhasználói erőforrásokhoz. Ez az alkalmazás definíciójának tekinthető, amely lehetővé teszi, hogy a szolgáltatás tudja, hogyan kell jogkivonatokat kibocsátani az alkalmazásnak az alkalmazás beállításai alapján.

### <a name="register-a-new-application-using-the-azure-portal"></a>Új alkalmazás regisztrálása az Azure Portal használatával

Az első lépésekhez kövesse az [új alkalmazások regisztrálásának](../active-directory/develop/quickstart-register-app.md)utasításait. Ha más cégektől származó felhasználókat szeretne meglátogatni az alkalmazásba, ki kell választania a több-bérlős lehetőségek egyikét, amikor a rendszer megkérdezi, hogy ki használhatja az alkalmazást

Ha le szeretné kérdezni a Microsoft Graph API- [t, konfigurálja az új alkalmazást a webes API-k eléréséhez](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Ha kijelöli az alkalmazás API-engedélyeit, a felhasználó alapértelmezett értéke **. az olvasás** elég ahhoz, hogy alapvető információkat gyűjtsön a vevőről, hogy a bevezetési folyamat zökkenőmentes és automatikus legyen. Ne igényeljen **rendszergazdai** jogosultsággal jelölt API-engedélyeket, mivel ez letiltja a nem rendszergazda felhasználók számára a Kezdőlap meglátogatását.

Ha emelt szintű engedélyekre van szüksége a bevezetési vagy kiépítési folyamat részeként, vegye fontolóra az Azure AD [növekményes hozzájárulási](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md) funkcióinak használatát, hogy a piactéren eljuttatott összes vevő képes legyen a kezdeti kommunikációra a kezdőlapon.

## <a name="use-a-code-sample-as-a-starting-point"></a>Mintakód használata kiindulási pontként

Számos olyan minta alkalmazást adtunk meg, amelyek az Azure AD-bejelentkezést használó egyszerű webhelyeket implementálják. Miután az alkalmazás regisztrálva lett az Azure AD-ben, a gyors üzembe helyezési **panel az 1** . ábrán látható általános alkalmazások és fejlesztői csomagok listáját kínálja. Válassza ki a környezetének megfelelőt, és kövesse a letöltésre és telepítésre vonatkozó utasításokat.

***1. ábra: rövid útmutató a Azure Portal***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="A Azure Portal rövid útmutató paneljének bemutatása.":::

A kód letöltése és a fejlesztési környezet beállítása után módosítsa az alkalmazás konfigurációs beállításait úgy, hogy az tükrözze az előző eljárásban rögzített alkalmazás-azonosítót, a bérlő AZONOSÍTÓját és az ügyfél titkos kulcsát. Vegye figyelembe, hogy a pontos lépések eltérhetnek attól függően, hogy melyik mintát használja.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Két Azure AD-alkalmazás használata az éles környezetben való biztonság javítása érdekében

Ez a cikk az architektúra egyszerűsített verzióját mutatja be a kereskedelmi Marketplace SaaS-ajánlat kezdőlapjának megvalósításához. A lap éles környezetben való futtatásakor javasoljuk, hogy a biztonság növelése érdekében csak egy másik, biztonságos alkalmazáson keresztül kommunikáljon a SaaS-teljesítési API-kkal. Ehhez két új alkalmazást kell létrehoznia:

- Először is, a több-bérlős Kezdőlap alkalmazás erre a pontra van leírva, kivéve a funkcionalitás nélkül, hogy kapcsolatba lépjen a SaaS-teljesítési API-kkal. Ez a funkció egy másik alkalmazásba kerül kiszervezésre az alább leírtak szerint.
- Másodszor, egy alkalmazás a SaaS-teljesítési API-kkal folytatott kommunikációhoz. Az alkalmazásnak egyetlen Bérlőnek kell lennie, amelyet csak a szervezet használ, és egy hozzáférés-vezérlési lista is létrehozható az API-k elérésének korlátozására kizárólag az alkalmazásból.

Ez lehetővé teszi, hogy a megoldás olyan helyzetekben működjön, amelyek figyelembe veszik a [megfontolási elv elkülönítését](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . A Kezdőlap például az első regisztrált Azure AD-alkalmazást használja a felhasználónak való bejelentkezéshez. Miután a felhasználó bejelentkezett, a Kezdőlap a második Azure AD-vel kéri a hozzáférési tokent, hogy meghívja a SaaS-teljesítési API-kat, és hívja meg a feloldási műveletet.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>A Piactéri vásárlási azonosító jogkivonat feloldása

Amikor a vevőt elküldjük a kezdőlapra, a rendszer egy tokent ad hozzá az URL-paraméterhez. Ez a jogkivonat különbözik az Azure AD-kiállított jogkivonat és a szolgáltatás-szolgáltatás hitelesítéséhez használt hozzáférési jogkivonattől, és bemenetként használja a [SaaS-teljesítési API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) -k számára, hogy lekérje az előfizetés részleteit. Ahogy a SaaS-teljesítési API-k összes hívása esetében, a szolgáltatás-szolgáltatásra irányuló kérelmet a szolgáltatás és a szolgáltatás közötti hitelesítéshez használt Azure AD-alkalmazás-azonosító felhasználótól származó hozzáférési jogkivonattal hitelesíti a rendszer.

> [!NOTE]
> A legtöbb esetben célszerű ezt a hívást egy második, egybérlős alkalmazásból elkészíteni. Lásd: [két Azure ad-alkalmazás használata a](#use-two-azure-ad-apps-to-improve-security-in-production) jelen cikk korábbi részében a biztonság javítása érdekében.

### <a name="request-an-access-token"></a>Hozzáférési jogkivonat kérése

Az alkalmazás SaaS-teljesítési API-kkal történő hitelesítéséhez hozzáférési jogkivonat szükséges, amely az Azure AD OAuth-végpont meghívásával hozható létre. Lásd: [a közzétevő engedélyezési jogkivonatának beszerzése](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>A feloldási végpont meghívása

A SaaS-teljesítési API-k implementálják a [feloldási](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) végpontot, amely meghívható a Piactéri token érvényességének megerősítéséhez és az előfizetés adatainak visszaküldéséhez.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Az azonosító jogkivonatban kódolt jogcímek adatainak beolvasása

Az [OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) folyamat részeként az Azure ad egy [azonosító jogkivonatot](../active-directory/develop/id-tokens.md) ad hozzá a kéréshez, amikor a vevőt a kezdőlapra küldi. Ez a jogkivonat több alapvető információt tartalmaz, amelyek hasznosak lehetnek az aktiválási folyamat során, beleértve az ebben a táblázatban látható információkat is.

| Érték | Leírás |
| ------------ | ------------- |
| aud | A token célközönsége. Ebben az esetben meg kell egyeznie az alkalmazás-AZONOSÍTÓval, és ellenőrizni kell. |
| preferred_username | A látogató felhasználó elsődleges felhasználóneve. Ez lehet egy e-mail-cím, telefonszám vagy más azonosító. |
| e-mail | A felhasználó e-mail-címe. Vegye figyelembe, hogy ez a mező üres lehet. |
| name | Az ember által olvasható érték, amely azonosítja a jogkivonat tárgyát. Ebben az esetben a vevő neve lesz. |
| OID | Azonosító a Microsoft Identity rendszerben, amely egyedileg azonosítja a felhasználót az alkalmazások között. A Microsoft Graph egy adott felhasználói fiók azonosító tulajdonsága adja vissza ezt az értéket. |
| TID | Az az azonosító, amely azt az Azure AD-bérlőt jelöli, amelyből a vevő származik. MSA-identitás esetén ez mindig a következő lesz: ``9188040d-6c67-4c5b-b112-36a304b66dad`` . További információt a következő szakaszban található megjegyzésekben talál: a Microsoft Graph API használata. |
| Sub | Azonosító, amely egyedileg azonosítja a felhasználót ebben az adott alkalmazásban. |
|||

## <a name="use-the-microsoft-graph-api"></a>A Microsoft Graph API használata

Az azonosító jogkivonat alapszintű információt tartalmaz a vásárló azonosításához, de az aktiválási folyamat további részleteket igényelhet – például a vevő vállalatát – a bevezetési folyamat befejezéséhez. A [Microsoft Graph API](/graph/use-the-api) -val kérheti ezeket az adatokat, hogy ne kényszerítse a felhasználót, hogy adja meg újra ezeket a részleteket. Az általános jogú **felhasználó. az olvasási** engedélyek alapértelmezés szerint a következő információkat tartalmazzák.

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

További tulajdonságok – például a felhasználó vállalatának neve vagy a felhasználó helye (ország) – kiválasztható a kérelembe való felvételhez. További részletekért tekintse meg [a felhasználói erőforrástípus tulajdonságait](/graph/api/resources/user#properties) .

Az Azure AD-ben regisztrált alkalmazások többsége delegált engedélyekkel rendelkezik, hogy beolvassa a felhasználó adatait a vállalat Azure AD-bérlője számára. Az adott információhoz Microsoft Graph kérelmekhez hozzáférési jogkivonatot kell csatolni a hitelesítéshez. A hozzáférési jogkivonat létrehozásával kapcsolatos konkrét lépések a használt technológiai veremtől függenek, de a mintakód egy példát is tartalmaz majd. További információ: [hozzáférés beszerzése egy felhasználó nevében](/graph/auth-v2-user).

> [!NOTE]
> Az MSA-bérlőről (a bérlői AZONOSÍTÓval) származó fiókok ``9188040d-6c67-4c5b-b112-36a304b66dad`` nem adnak vissza több információt, mint amelyet már gyűjtöttek az azonosító jogkivonattal. Így kihagyhatja ezt a hívást a fiókok Graph API.

## <a name="next-steps"></a>Következő lépések

- [SaaS-ajánlat létrehozása a kereskedelmi piactéren](create-new-saas-offer.md)