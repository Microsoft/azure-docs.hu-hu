---
title: Alkalmazások & szolgáltatásnévvel az Azure AD-| Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazás- és szolgáltatásnév-objektumok közötti kapcsolatot a Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727059"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban

Ez a cikk az alkalmazásregisztrációt, az alkalmazásobjektumokat és a szolgáltatás Azure Active Directory: mik ezek, hogyan vannak használva, és hogyan kapcsolódnak egymáshoz. Egy több-bérlős példaforgatókönyvet is bemutatunk, amely egy alkalmazás alkalmazásobjektuma és a megfelelő szolgáltatásnév-objektumok közötti kapcsolatot mutatja be.

## <a name="application-registration"></a>Alkalmazásregisztráció
Ahhoz, hogy az identitás- és hozzáférés-kezelési funkciókat az Azure AD-be delegálhatja, az alkalmazásokat regisztrálni kell egy Azure [AD-bérlőben.](developer-glossary.md#tenant) Amikor regisztrálja az alkalmazást az Azure AD-ban, olyan identitáskonfigurációt hoz létre az alkalmazáshoz, amely lehetővé teszi az Azure AD-val való integrációt. Amikor regisztrál egy alkalmazást [][AZURE-Portal]a Azure Portal-ben, kiválaszthatja, hogy az egyetlen bérlő (csak a bérlőn belül érhető el) vagy több-bérlős (más bérlőkben érhető el), és opcionálisan átirányítási URI-t is beállíthat (ahová a hozzáférési jogkivonatot küldi).

Az alkalmazások regisztrálásának részletes útmutatójért lásd az alkalmazásregisztráció rövid [útmutatóját.](quickstart-register-app.md)

Az alkalmazásregisztráció befejezése után az alkalmazás egy globálisan egyedi példányával (az alkalmazásobjektummal) [](#application-object)rendelkezik, amely az otthoni bérlőben vagy címtárban található.  Emellett rendelkezik egy globálisan egyedi azonosítóval is az alkalmazáshoz (az alkalmazás vagy az ügyfél-azonosító).  A portálon ezután titkos kulcsok, tanúsítványok és hatókörök felvételére is lehetőség van az alkalmazás megfelelő munkához, az alkalmazás arculatának testreszabásához a bejelentkezési párbeszédpanelen, és így tovább.

Ha regisztrál egy alkalmazást a portálon, a rendszer automatikusan létrehoz egy alkalmazásobjektumot és egy szolgáltatásnév-objektumot a saját bérlőben.  Ha a Microsoft Graph API-k használatával regisztrál/hoz létre egy alkalmazást, a szolgáltatásnév-objektum létrehozása külön lépés.

## <a name="application-object"></a>Alkalmazásobjektum
Az Azure AD-alkalmazásokat az egyetlen alkalmazásobjektuma határozza meg, amely abban az Azure AD-bérlőben található, ahol az alkalmazást regisztrálták (más néven az alkalmazás "otthoni" bérlője).  Az alkalmazásobjektumok sablonként vagy tervként használhatók egy vagy több szolgáltatásnév-objektum létrehozásához.  A szolgáltatásnév minden bérlőben létrejön, ahol az alkalmazást használják. Az objektumorientált programozás osztályához hasonlóan az alkalmazásobjektum statikus tulajdonságokkal rendelkezik, amelyek az összes létrehozott szolgáltatásnévre (vagy alkalmazáspéldányra) érvényesek.

Az alkalmazásobjektum az alkalmazás három aspektusát írja le: azt, hogy a szolgáltatás hogyan tud jogkivonatokat kiíratni az alkalmazás eléréséhez, milyen erőforrásokat kell elérnie az alkalmazásnak, és milyen műveleteket tud az alkalmazás.

A **Alkalmazásregisztrációk** panelen [Azure Portal][AZURE-Portal] a saját bérlőben található alkalmazásobjektumok felsorolásához és kezeléséhez.

![Alkalmazásregisztrációk panel](./media/app-objects-and-service-principals/app-registrations-blade.png)

A Microsoft Graph [Application entitás][MS-Graph-App-Entity] határozza meg az alkalmazásobjektum tulajdonságainak sémáját.

## <a name="service-principal-object"></a>Szolgáltatásnév-objektum
Az Azure AD-bérlő által védett erőforrások eléréséhez a hozzáférést igénylő entitást egy rendszerbiztonsági tagnak kell képviselnie. Ez a követelmény a felhasználókra (egyszerű felhasználó) és az alkalmazásokra (szolgáltatásnév) egyaránt igaz. A rendszerbiztonsági tag határozza meg az Azure AD-bérlőben a felhasználó/alkalmazás hozzáférési szabályzatát és engedélyeit. Ez lehetővé teszi az olyan alapvető funkciók használatát, mint a felhasználó/alkalmazás hitelesítése a bejelentkezés során, valamint az engedélyezés az erőforrás-hozzáférés során.

A szolgáltatásnévnek három típusa létezik: alkalmazás, felügyelt identitás és örökölt.

A szolgáltatásnév első típusa egy globális alkalmazásobjektum helyi reprezentációja vagy alkalmazáspéldánya egyetlen bérlőben vagy címtárban. Ebben az esetben a szolgáltatásnév egy konkrét példány, amely az alkalmazásobjektumból jött létre, és bizonyos tulajdonságokat örököl az adott alkalmazásobjektumtól. Minden bérlőben létrejön egy szolgáltatásnév, ahol az alkalmazást használják, és a globálisan egyedi alkalmazásobjektumra hivatkozik.  A szolgáltatásnév-objektum határozza meg, hogy az alkalmazás ténylegesen mit tud tenni az adott bérlőben, ki férhet hozzá az alkalmazáshoz, és milyen erőforrásokhoz férhet hozzá.

Amikor egy alkalmazás engedélyt kap a bérlőben (regisztráció vagy hozzájárulás esetén) az erőforrások [eléréséhez,](developer-glossary.md#consent)létrejön egy szolgáltatásnév-objektum. A bérlőkben szolgáltatásnév-objektumokat is létrehozhat a [Azure PowerShell,](howto-authenticate-service-principal-powershell.md) [az Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli) [a Microsoft Graph,](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http)a [Azure Portal][AZURE-Portal]és más eszközök használatával. A portál használata esetén a rendszer automatikusan létrehoz egy szolgáltatásnévt az alkalmazás regisztrálja.

A szolgáltatásnév második típusa egy felügyelt [identitást képvisel.](/azure/active-directory/managed-identities-azure-resources/overview) A felügyelt identitások szükségtelenné teszi a fejlesztők számára a hitelesítő adatok kezelését. A felügyelt identitások identitást biztosítanak az alkalmazások számára az Azure AD-hitelesítést támogató erőforrásokhoz való csatlakozáskor. Ha a felügyelt identitás engedélyezve van, a bérlőben létrejön egy szolgáltatásnév, amely azt a felügyelt identitást képviseli. A felügyelt identitásokat képviselő szolgáltatásnévhez hozzáférés és engedélyek adhatók, de közvetlenül nem frissíthetők vagy módosíthatók.

A harmadik szolgáltatásnév-típus egy örökölt alkalmazást jelent (az alkalmazásregisztrációk bevezetése előtt létrehozott vagy örökölt szolgáltatásokkal létrehozott alkalmazásokat). Az örökölt szolgáltatásnevek olyan hitelesítő adatokkal, szolgáltatásnévvel, válasz URL-címekkel és egyéb tulajdonságokkal is rendelkezni tudnak, amelyeket egy jogosult felhasználó szerkeszthet, de nem rendelkeznek társított alkalmazásregisztrációval. A szolgáltatásnév csak abban a bérlőben használható, ahol létrehozták.

A Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] entitás határozza meg a szolgáltatásnév-objektum tulajdonságainak sémáját.

A **portálOn enterprise applications (Vállalati** alkalmazások) panelje a bérlők szolgáltatásnévinek felsorolására és kezelésére használható. Itt láthatja a szolgáltatásnév engedélyét, a felhasználó által megadott engedélyeket, a felhasználók által a hozzájárulásukat, a bejelentkezési adatokat stb.

![Vállalati alkalmazások panel](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Alkalmazásobjektumok és szolgáltatásnév közötti kapcsolat

Az alkalmazásobjektum  az alkalmazás globális reprezentációja az összes bérlőn való használatra, a szolgáltatásnév pedig az adott bérlőben használt helyi reprezentáció. 

Az alkalmazásobjektum szolgál sablonként, amelyből a közös és az alapértelmezett tulajdonságok *származtatása* történik a megfelelő szolgáltatásnév-objektumok létrehozásához. Az alkalmazásobjektumok ezért 1:1-es kapcsolattal és 1:00-ás kapcsolattal vannak a megfelelő egyszerű szolgáltatásobjektum(oka)hez.

Minden bérlőben létre kell hozni egy egyszerű szolgáltatást, amely lehetővé teszi számára, hogy identitást hozzon létre a bejelentkezéshez és/vagy a bérlő által védett erőforrásokhoz való hozzáféréshez. Egybérlős alkalmazás csak egy szolgáltatásnévvel rendelkezik (a saját bérlőjében), melynek létrehozása és a használatának engedélyezése az alkalmazás regisztrációja során történik. A több-bérlős alkalmazásokban emellett egy szolgáltatásnév is létre van hozva minden bérlőben, ahol az adott bérlő felhasználója hozzájárult a használathoz.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Az alkalmazások módosításának és törlésének következményei
Az alkalmazásobjektumon végrehajtott módosítások csak az alkalmazás saját bérlőjére (a bérlőre, ahol regisztrálva vannak) is megjelennek a szolgáltatásnév-objektumában. Ez azt jelenti, hogy az alkalmazásobjektum törlése a saját bérlői szolgáltatásnév-objektumát is törli.  Az alkalmazásobjektum visszaállítása azonban nem állítja vissza a megfelelő szolgáltatásnévvel. Több-bérlős alkalmazások esetén az alkalmazásobjektum módosításai nem jelennek meg egyik fogyasztói bérlő [szolgáltatásnév-objektumában](https://myapps.microsoft.com) sem, amíg a hozzáférést el nem távolítják az application hozzáférési panel és újra meg nem adták.

## <a name="example"></a>Példa

Az alábbi ábra egy alkalmazás alkalmazásobjektuma és a megfelelő szolgáltatásnév-objektumok közötti kapcsolatot mutatja be egy HR-alkalmazás nevű több-bérlős mintaalkalmazás **kontextusában.** Ebben a példaforgatókönyvben három Azure AD-bérlő van:

- **Adatum** – A HR-alkalmazást kifejlesztett vállalat **által használt bérlő**
- **Contoso** – A Contoso szervezet által használt bérlő, amely a **HR-alkalmazás felhasználója**
- **Fabrikam** – A Fabrikam-szervezet által használt bérlő, amely a **HR-alkalmazást** is használja

![Az alkalmazásobjektum és a szolgáltatásnév-objektum közötti kapcsolat](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Ebben a példaforgatókönyvben:

| Lépés | Leírás |
|------|-------------|
| 1    | Az a folyamat, amely során az alkalmazás és a szolgáltatásnév objektumai az alkalmazás otthoni bérlőiben is létre lesz hozva. |
| 2    | Amikor a Contoso és a Fabrikam rendszergazdái befejezik a jóváhagyást, a rendszer létrehoz egy szolgáltatásnév-objektumot a vállalat Azure AD-bérlőben, és hozzárendeli a rendszergazda által megadott engedélyeket. Azt is vegye figyelembe, hogy a HR-alkalmazás konfigurálható/kialakítható úgy, hogy lehetővé tegye a felhasználók egyéni használatra vonatkozó beleegyezését. |
| 3    | A HR-alkalmazás (Contoso és Fabrikam) fogyasztói bérlői saját szolgáltatásnév-objektummal rendelkezik. Mindegyik az alkalmazás egy példányának futásidőben való használatát jelöli, amelyet a megfelelő rendszergazda által adott engedélyek irányít. |

## <a name="next-steps"></a>Következő lépések

- A Microsoft Graph [Explorerrel lekérdezheti](https://developer.microsoft.com/graph/graph-explorer) az alkalmazás- és a szolgáltatásnév-objektumokat.
- Az alkalmazás alkalmazásobjektumát a Microsoft Graph API-val, a [Azure Portal][AZURE-Portal] alkalmazásjegyzék-szerkesztőjével vagy [az Azure AD PowerShell-parancsmagokkal](/powershell/azure/)lehet elérni, ahogyan azt az OData-alkalmazás entitása [képviseli.][MS-Graph-App-Entity]
- Az alkalmazás szolgáltatásnév-objektuma a Microsoft Graph API-n vagy az [Azure AD PowerShell-parancsmagon](/powershell/azure/)keresztül érhető el, ahogyan azt az OData [ServicePrincipal entitása is képviseli.][MS-Graph-Sp-Entity]

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
