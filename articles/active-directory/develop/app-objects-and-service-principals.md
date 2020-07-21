---
title: Alkalmazások az Azure AD-ben & egyszerű szolgáltatások | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg az alkalmazás és a szolgáltatás egyszerű objektumai közötti kapcsolattal Azure Active Directoryban.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: d8ff8ced1723a3cdc969b4a951c31ea1da2d3093
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518297"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban

Ez a cikk az alkalmazások regisztrálását, az alkalmazások objektumait és az egyszerű szolgáltatásokat ismerteti Azure Active Directoryban: mit, hogyan használják, és hogyan kapcsolódnak egymáshoz. Egy több-bérlős példa is látható, amely szemlélteti az alkalmazás alkalmazás-objektuma és a hozzá tartozó egyszerű szolgáltatásnév objektumok közötti kapcsolatot.

## <a name="application-registration"></a>Alkalmazásregisztráció
Az identitás-és hozzáférés-kezelési funkciók Azure AD-be való delegálásához egy alkalmazást regisztrálni kell egy Azure AD- [bérlőben](developer-glossary.md#tenant). Ha az Azure AD-vel regisztrálja az alkalmazást, az alkalmazáshoz olyan identitás-konfigurációt hoz létre, amely lehetővé teszi, hogy integrálható legyen az Azure AD-vel. Ha a [Azure Portalban][AZURE-Portal]regisztrál egy alkalmazást, kiválaszthatja, hogy az egyetlen bérlő (csak a bérlőben érhető el) vagy több-bérlős (más bérlők számára elérhető), és opcionálisan beállíthat-e ÁTirányítási URI-t (ahol a hozzáférési tokent elküldjék).

Az alkalmazás regisztrációjának befejezése után az alkalmazás (az Application objektum) globálisan egyedi példánya van, amely a saját bérlőn vagy címtáron belül él.  Az alkalmazáshoz (az alkalmazáshoz vagy az ügyfél-AZONOSÍTÓhoz) globálisan egyedi azonosítót is használhat.  A portálon titkokat vagy tanúsítványokat és hatóköröket adhat hozzá az alkalmazás működéséhez, testreszabhatja az alkalmazás arculatát a bejelentkezési párbeszédpanelen, és így tovább.

Ha regisztrál egy alkalmazást a portálon, a rendszer automatikusan létrehoz egy Application-objektumot és egy egyszerű szolgáltatásnevet a saját bérlőben.  Ha a Microsoft Graph API-k használatával regisztrál/hoz létre alkalmazást, az egyszerű szolgáltatás létrehozása egy külön lépés.

## <a name="application-object"></a>Application objektum
Egy Azure AD-alkalmazást egy és csak egy Application objektum definiál, amely abban az Azure AD-bérlőben található, ahol az alkalmazás regisztrálva van (vagyis az alkalmazás "Home" bérlője).  Az Application Object sablonként vagy tervrajzként használható egy vagy több egyszerű szolgáltatásnév létrehozásához.  A rendszer minden olyan bérlőn létrehoz egy szolgáltatásnevet, amelyben az alkalmazás használatban van. Az objektum-orientált programozáshoz hasonlóan az Application objektumhoz is vannak olyan statikus tulajdonságok, amelyek az összes létrehozott egyszerű szolgáltatásra (vagy alkalmazás példányaira) vonatkoznak. 

Az Application objektum az alkalmazás három aspektusát ismerteti: Hogyan lehet jogkivonatokat kiállítani az alkalmazáshoz való hozzáféréshez, az alkalmazáshoz szükséges erőforrásokat és az alkalmazás által elvégezhető műveleteket. 

A [Azure Portal][AZURE-Portal] **Alkalmazásregisztrációk** paneljének használatával listázhatja és kezelheti a saját bérlője alkalmazás-objektumait.

A Microsoft Graph [alkalmazás entitása][MS-Graph-App-Entity] határozza meg az Application objektum tulajdonságainak sémáját.

## <a name="service-principal-object"></a>Egyszerű szolgáltatásnév objektum
Az Azure AD-bérlő által védett erőforrások eléréséhez a hozzáférést igénylő entitást egy rendszerbiztonsági tagnek kell képviselnie. Ez a felhasználó (egyszerű felhasználónév) és az alkalmazások (egyszerű szolgáltatásnév) esetében is igaz. A rendszerbiztonsági tag meghatározza az Azure AD-bérlőben a felhasználóhoz/alkalmazáshoz tartozó hozzáférési szabályzatot és engedélyeket. Ez lehetővé teszi az olyan alapvető funkciók használatát, mint a felhasználó/alkalmazás hitelesítése a bejelentkezéskor és az erőforrás-hozzáférés engedélyezésekor.

Egy egyszerű szolgáltatásnév egy globális Application objektum helyi képviselete vagy alkalmazás-példánya egyetlen bérlőben vagy címtárban. Az egyszerű szolgáltatásnév az Application objektumból létrehozott konkrét példány, amely bizonyos tulajdonságokat örököl az alkalmazás-objektumtól.  A rendszer minden olyan bérlőn létrehoz egy szolgáltatásnevet, amelyben az alkalmazás használatban van, és hivatkozik a globálisan egyedi alkalmazás-objektumra.  Az egyszerű szolgáltatásnév objektum azt határozza meg, hogy az alkalmazás mit tud ténylegesen végrehajtani az adott bérlőn, ki férhet hozzá az alkalmazáshoz, és hogy milyen erőforrások férhetnek hozzá az alkalmazáshoz. 

Ha egy alkalmazás engedélyt kap a bérlő erőforrásainak elérésére (regisztráció vagy [beleegyezés](developer-glossary.md#consent)esetén), létrejön egy egyszerű szolgáltatásnév objektum. A [Azure PowerShell](howto-authenticate-service-principal-powershell.md), az Azure CLI, a [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http), a [Azure Portal][AZURE-Portal]és más eszközök használatával is létrehozhat egyszerű szolgáltatásnév-objektumokat a bérlőn.  A portál használatakor a rendszer automatikusan létrehoz egy szolgáltatásnevet az alkalmazások regisztrálásakor.

A portál **vállalati alkalmazások** paneljén az egyszerű szolgáltatások listázása és kezelése történik a bérlőben. Megtekintheti az egyszerű szolgáltatás engedélyeit, a felhasználó által megadott engedélyeket, amelyeket a felhasználók megtettek a beleegyezés, a bejelentkezési adatok és egyebek között.

A Microsoft Graph [ServicePrincipal entitás][MS-Graph-Sp-Entity] definiálja az egyszerű szolgáltatásnév objektumának sémáját.

## <a name="relationship-between-application-objects-and-service-principals"></a>Alkalmazás-és egyszerű szolgáltatások közötti kapcsolat

Az Application objektum az alkalmazás *globális* ábrázolása az összes bérlő esetében, és az egyszerű szolgáltatásnév egy adott bérlőn való használatra szolgáló *helyi* képviselet.

Az alkalmazásobjektum szolgál sablonként, amelyből a közös és az alapértelmezett tulajdonságok *származtatása* történik a megfelelő szolgáltatásnév-objektumok létrehozásához. Az Application objektumnak ezért 1:1 kapcsolattal kell rendelkeznie a szoftver alkalmazással, és 1: sok kapcsolat van a hozzá tartozó szolgáltatásnév-objektummal.

Minden olyan bérlőn létre kell hozni egy szolgáltatásnevet, amelyben az alkalmazás használatban van, ami lehetővé teszi, hogy személyazonosságot hozzon létre a bejelentkezéshez és/vagy a bérlő által védett erőforrásokhoz való hozzáféréshez. Egybérlős alkalmazás csak egy szolgáltatásnévvel rendelkezik (a saját bérlőjében), melynek létrehozása és a használatának engedélyezése az alkalmazás regisztrációja során történik. A több-bérlős webalkalmazások/API-k egy egyszerű szolgáltatásnevet is létrehoznak minden olyan bérlőn, ahol az adott bérlő felhasználója beleegyezett a használatára.

> [!NOTE]
> Az alkalmazás-objektumon végrehajtott módosításokat a rendszer csak az alkalmazás otthoni bérlője (a bérlő, ahol regisztrálva van) egyszerű szolgáltatásnév objektumában is megjeleníti. A több-bérlős alkalmazások esetében az Application objektum módosításai nem jelennek meg a fogyasztói bérlők egyszerű szolgáltatásaiban, amíg el nem távolítja a hozzáférést az [alkalmazás-hozzáférési paneljén](https://myapps.microsoft.com) , és nem ad vissza engedélyt.
>
> Azt is vegye figyelembe, hogy a natív alkalmazások alapértelmezés szerint több-bérlőként vannak regisztrálva.

## <a name="example"></a>Példa

Az alábbi ábrán az alkalmazás alkalmazás-objektuma és a hozzá tartozó egyszerű szolgáltatásnév objektumok közötti kapcsolat látható az **HR**-alkalmazás nevű minta több-bérlős alkalmazás kontextusában. Ebben a példában három Azure AD-bérlő található:

- **Adatum** – a **HR-alkalmazást** fejlesztő vállalat által használt bérlő
- **Contoso** – a contoso szervezet által használt bérlő, amely a **HR-alkalmazás** fogyasztója
- **Fabrikam** – a fabrikam-szervezet által használt bérlő, amely a **HR-alkalmazást** is használja

![Kapcsolat az alkalmazás-objektum és az egyszerű szolgáltatásnév objektum között](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Ebben a példában a következő forgatókönyvben:

| Lépés | Leírás |
|------|-------------|
| 1    | Az alkalmazás és a szolgáltatás egyszerű objektumainak létrehozásának folyamata az alkalmazás kezdőlapjának bérlője. |
| 2    | Ha a contoso és a fabrikam rendszergazdája teljes körű beleegyezik, a rendszer létrehoz egy egyszerű szolgáltatásnév-objektumot a vállalat Azure AD-bérlőben, és hozzárendeli a rendszergazda által megadott engedélyeket. Azt is vegye figyelembe, hogy a HR-alkalmazás konfigurálható/úgy lett kialakítva, hogy engedélyezze a felhasználók számára az egyéni használatra való hozzájárulásukat. |
| 3    | A HR-alkalmazás (contoso és Fabrikam) fogyasztói bérlői mindegyike saját egyszerű szolgáltatásnév-objektummal rendelkezik. Mindegyik azt jelenti, hogy az alkalmazás egy példányát futásidőben, a megfelelő rendszergazda által eljuttatott engedélyek szabályozzák. |

## <a name="next-steps"></a>Következő lépések

- Az alkalmazás és a szolgáltatás egyszerű objektumainak lekérdezéséhez használhatja a [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) is.
- Az alkalmazás alkalmazás-objektumát a Microsoft Graph API, a [Azure Portal][AZURE-Portal] Application manifest Editor vagy az [Azure ad PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)segítségével érheti el, ahogyan azt a OData [alkalmazási entitása][MS-Graph-App-Entity]képviseli.
- A Microsoft Graph API-n vagy az [Azure ad PowerShell-parancsmagokon](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)keresztül elérheti egy alkalmazás egyszerű szolgáltatásának objektumát, amelyet a OData [ServicePrincipal-entitása][MS-Graph-Sp-Entity]is képvisel.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
