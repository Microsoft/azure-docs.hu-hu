---
title: Az alkalmazások típusai a v 1.0-ban | Azure
description: Az Azure Active Directory v 2.0 végpont által támogatott alkalmazások és forgatókönyvek típusait ismerteti.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5ff2858dd8b91ba036c517cbff07be96a729ef8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88116444"
---
# <a name="application-types-in-v10"></a>Alkalmazások típusai a v 1.0-ban

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) támogatja a különböző modern alkalmazás-architektúrák hitelesítését, mind az iparági szabványnak megfelelő protokollok, OAuth 2,0 vagy OpenID Connect alapján.

Az alábbi ábrán a forgatókönyvek és az alkalmazások típusai láthatók, valamint a különböző összetevők hozzáadásának módja:

![Alkalmazástípusok és forgatókönyvek](./media/authentication-scenarios/application-types-scenarios.png)

Az Azure AD az alábbi öt elsődleges alkalmazási forgatókönyvet támogatja:

- **[Egyoldalas alkalmazás (Spa)](single-page-application.md)**: A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure ad biztosít.
- Webböngésző webes **[alkalmazásba](web-app.md)**: a felhasználónak be kell jelentkeznie egy Azure ad által védett webalkalmazásba.
- **[Natív alkalmazás webes API-](native-app.md)** hoz: a telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure ad által védett webes API erőforrásainak lekéréséhez.
- **[Webalkalmazás webes API-hoz](web-api.md)**: egy webalkalmazásnak az Azure ad által védett webes API-erőforrásokból kell lekérnie az erőforrásokat.
- **[Daemon vagy Server Application to web API](service-to-service.md)**: egy webfelhasználói felület nélküli démon-alkalmazás vagy kiszolgálóalkalmazás számára szükséges az Azure ad által védett webes API erőforrásainak lekérése.

Kövesse a hivatkozásokat, és ismerkedjen meg az egyes alkalmazási típusokkal, és ismerkedjen meg a magas szintű forgatókönyvekkel a kód használatának megkezdése előtt. Azt is megismerheti, hogy milyen különbségek szükségesek a 1.0-s vagy a v 2.0-s végponttal működő adott alkalmazás írásakor.

> [!NOTE]
> A v 2.0-végpont nem támogatja az összes Azure AD-forgatókönyvet és-funkciót. Annak megállapításához, hogy a 2.0-s végpontot kell-e használni, olvassa el a 2.0-s [verzióra vonatkozó korlátozásokat](./azure-ad-endpoint-comparison.md?bc=%2fazure%2factive-directory%2fazuread-dev%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fazuread-dev%2ftoc.json).

Az itt ismertetett alkalmazások és forgatókönyvek az egyes nyelveken és platformokon is kidolgozhatók. Mindegyiket a Code Samples útmutatóban elérhető, a kód mintái: [v 1.0 Code Samples by forgatókönyv](sample-v1-code.md) és a [v 2.0 Code Samples by forgatókönyv szerint](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)támogatja. A kód mintáit közvetlenül a megfelelő [GitHub-minta tárházból](https://github.com/Azure-Samples?q=active-directory)is letöltheti.

Továbbá, ha az alkalmazásnak egy végpontok közötti forgatókönyv egy adott darabját vagy szegmensét kell használnia, a legtöbb esetben a funkciók egymástól függetlenül is hozzáadhatók. Ha például egy olyan natív alkalmazással rendelkezik, amely webes API-t hív meg, egyszerűen hozzáadhat egy webalkalmazást, amely a webes API-t is meghívja.

## <a name="app-registration"></a>Alkalmazásregisztráció

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Az Azure AD v 1.0-s végpontot használó alkalmazás regisztrálása

Minden olyan alkalmazást, amely kiszervezi az Azure AD-hitelesítést, regisztrálni kell egy címtárban. Ez a lépés magában foglalja az alkalmazással kapcsolatos Azure AD-t, beleértve a helyét tartalmazó URL-címet, a hitelesítés utáni válaszokat, az URI-t az alkalmazás azonosítására, és így tovább. Ezek az információk néhány fő okból szükségesek:

* Az Azure AD-nak kommunikálnia kell az alkalmazással a bejelentkezés vagy a tokenek cseréje során. Az Azure AD és az alkalmazás között átadott információk a következőket tartalmazzák:
  
  * **Application ID URI** – az alkalmazás azonosítója. Ezt az értéket a rendszer a hitelesítés során elküldi az Azure AD-nek, amely jelzi, hogy a hívó melyik alkalmazáshoz kér tokent. Emellett ez az érték szerepel a jogkivonatban, hogy az alkalmazás tudja, hogy a kívánt cél volt.
  * **Válasz URL-cím** és **átirányítási URI** – webes API-hoz vagy webalkalmazáshoz a válasz URL-címe az a hely, ahol az Azure ad elküldi a hitelesítési választ, beleértve a tokent, ha a hitelesítés sikeres volt. Natív alkalmazások esetében az átirányítási URI egy egyedi azonosító, amely számára az Azure AD átirányítja a felhasználói ügynököt egy OAuth 2,0-kérelemben.
  * **Alkalmazás azonosítója** – az alkalmazás azonosítója, amelyet az Azure ad generál az alkalmazás regisztrálásakor. Ha hitelesítési kódot vagy tokent kér, a hitelesítés során az alkalmazás AZONOSÍTÓját és kulcsát a rendszer elküldi az Azure AD-nek.
  * **Kulcs** – a kulcs, amelyet a rendszer az alkalmazás-azonosítóval együtt továbbít az Azure ad-ben a webes API meghívásához való hitelesítéskor.
* Az Azure AD-nek biztosítania kell, hogy az alkalmazás rendelkezik a címtáradatok eléréséhez szükséges engedélyekkel, a szervezet más alkalmazásaival és így tovább.

Részletekért olvassa el az [alkalmazások regisztrálását](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)ismertető témakört.

## <a name="single-tenant-and-multi-tenant-apps"></a>Egyetlen bérlős és több-bérlős alkalmazások

A kiépítés világossá válik, ha tudomásul veszi, hogy az alkalmazások két kategóriája van, amelyeket az Azure AD-vel lehet fejleszteni és integrálni:

* **Egybérlős alkalmazás** – egyetlen bérlői alkalmazás az egyik szervezet számára készült. Ezek általában vállalati fejlesztő által írt üzletági (LoB) alkalmazások. Egyetlen bérlői alkalmazást csak a felhasználóknak kell elérniük egy címtárban, és ennek eredményeképpen csak egy címtárban kell kiépíteni őket. Ezeket az alkalmazásokat általában egy fejlesztő regisztrálja a szervezeten belül.
* **Több-bérlős alkalmazás** – a több-bérlős alkalmazások számos szervezet számára készültek, nem csupán egyetlen szervezet számára. Ezek jellemzően egy független szoftvergyártó (ISV) által írt, szolgáltatott szoftveres (SaaS) alkalmazások. Több-bérlős alkalmazásokat kell kiépíteni minden olyan címtárban, ahol azokat használni fogják, ami felhasználói vagy rendszergazdai jogosultságot igényel a regisztráláshoz. Ez a engedélyezési folyamat akkor indul el, amikor egy alkalmazás regisztrálva van a címtárban, és hozzáférést kap a Graph APIhoz vagy esetleg egy másik webes API-hoz. Ha egy másik szervezet felhasználói vagy rendszergazdái feliratkozik az alkalmazás használatára, a rendszer egy párbeszédpanelt jelenít meg, amely megjeleníti az alkalmazás által igényelt engedélyeket. A felhasználó vagy a rendszergazda megadhatja az alkalmazásnak, amely hozzáférést biztosít az alkalmazás számára a megadott értékekhez, és végül regisztrálja az alkalmazást a címtárában. További információ: [az engedélyezési keretrendszer áttekintése](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>További szempontok egyetlen bérlő vagy több-bérlős alkalmazások fejlesztéséhez

További szempontokat is figyelembe kell venni, amikor több-bérlős alkalmazást fejlesztenek egyetlen bérlős alkalmazás helyett. Ha például az alkalmazást több címtárban is elérhetővé teszi a felhasználók számára, szüksége lesz egy olyan mechanizmusra, amely meghatározza, hogy melyik bérlőn van. Egyetlen bérlői alkalmazásnak csak a saját címtárában kell megkeresnie egy felhasználó számára, míg a több-bérlős alkalmazásnak egy adott felhasználót kell azonosítania az Azure AD összes könyvtárából. A feladat elvégzéséhez az Azure AD egy közös hitelesítési végpontot biztosít, ahol a több-bérlős alkalmazások a bérlői végpontok helyett a bejelentkezési kéréseket is megadhatják. Ez a végpont `https://login.microsoftonline.com/common` Az Azure ad összes könyvtárára vonatkozik, míg a bérlő-specifikus végpont lehet `https://login.microsoftonline.com/contoso.onmicrosoft.com` . A közös végpontot különösen fontos figyelembe venni az alkalmazás fejlesztésekor, mert a bejelentkezés, a kijelentkezés és a jogkivonat-érvényesítés során több bérlő kezeléséhez szükséges logikára van szükség.

Ha jelenleg egy bérlői alkalmazást fejleszt, de számos szervezet számára elérhetővé szeretné tenni azt, egyszerűen módosíthatja az alkalmazást és annak konfigurációját az Azure AD-ben, hogy az informatikai részleg képes legyen a több-bérlős működésre. Emellett az Azure AD ugyanazt az aláíró kulcsot használja az összes címtárban található összes tokenhez, függetlenül attól, hogy egyetlen bérlő vagy több-bérlős alkalmazásban biztosít hitelesítést.

A dokumentumban felsorolt minden forgatókönyv tartalmaz egy alszakaszt, amely leírja a létesítési követelményeit. Az alkalmazások Azure AD-ben való kiépítésével és az önálló és több-bérlős alkalmazások közötti különbségekkel kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directoryval](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) további információ. Folytassa az olvasást a gyakori alkalmazási forgatókönyvek megismeréséhez az Azure AD-ben.

## <a name="next-steps"></a>Következő lépések

- További információ az Azure AD- [alapú hitelesítés egyéb alapjairól](v1-authentication-scenarios.md)