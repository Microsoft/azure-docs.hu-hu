---
title: Azure egyszeri kijelentkezési SAML protokoll
description: Ez a cikk az egyszeri kijelentkezési SAML protokollt ismerteti Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dbe21d020d5d01f24913b95587721403fa218cc8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881265"
---
# <a name="single-sign-out-saml-protocol"></a>Egyszeri kijelentkezési SAML protokoll

Azure Active Directory (Azure AD) támogatja az SAML 2,0 webböngésző egyszeri kijelentkezési profilját. Az egyszeri bejelentkezés megfelelő működéséhez az alkalmazás **LogoutURL** explicit módon regisztrálni kell az Azure ad-ben az alkalmazás regisztrálása során. Az Azure AD a LogoutURL használatával irányítja át a felhasználókat, miután kijelentkezett.

Az alábbi ábra az Azure AD egyszeri kijelentkezési folyamatának munkafolyamatát mutatja be.

![Azure AD egyszeri kijelentkezési munkafolyamat](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
A Cloud Service egy üzenetet küld az `LogoutRequest` Azure ad-nek, amely jelzi, hogy a munkamenet le lett szakítva. Az alábbi részlet egy minta elemet mutat be `LogoutRequest` .

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Az `LogoutRequest` Azure ad-nak eljuttatott elem a következő attribútumokat igényli:

* `ID`-Ez azonosítja a kijelentkezési kérést. A értéke `ID` nem kezdődhet számmal. A tipikus eljárás egy GUID **azonosítójának** hozzáfűzése a GUID karakterlánc-ábrázolásához.
* `Version`– Az elem értékét állítsa **2,0**-ra. Kötelezően megadandó érték.
* `IssueInstant`– Ez egy, az `DateTime` egyezményes világidő (UTC) és az [oda-és visszaút formátuma ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)karakterlánc. Az Azure AD egy ilyen típusú értéket vár, de nem kényszeríti ki.

### <a name="issuer"></a>Kiállító
Az `Issuer` a elemnek `LogoutRequest` pontosan egyeznie kell az Azure ad felhőalapú szolgáltatásának egyik **ServicePrincipalNames** . Ez általában az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI-ra** van beállítva.

### <a name="nameid"></a>NameID
Az elem értékének `NameID` pontosan egyeznie kell a `NameID` kijelentkezett felhasználóval.

## <a name="logoutresponse"></a>LogoutResponse
Az Azure AD egy `LogoutResponse` adott elemre adott választ küld `LogoutRequest` . Az alábbi részlet egy mintát mutat be `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Az Azure AD beállítja `ID` az `Version` elemet és az `IssueInstant` értékeket a `LogoutResponse` elemben. Azt is megadja, `InResponseTo` hogy az elem a `ID` `LogoutRequest` Válasz kiváltotta attribútumának értéke.

### <a name="issuer"></a>Kiállító
Az Azure AD ezt az értéket állítja be, ahol az az `https://login.microsoftonline.com/<TenantIdGUID>/` \<TenantIdGUID> Azure ad-bérlő bérlői azonosítója.

Az elem értékének kiértékeléséhez `Issuer` használja az alkalmazás regisztrációja során megadott **alkalmazás-azonosító URI** azonosító értékét.

### <a name="status"></a>Állapot
Az Azure AD az `StatusCode` `Status` elem eleme alapján jelzi a kijelentkezés sikerességét vagy sikertelenségét. Ha a kijelentkezési kísérlet sikertelen, `StatusCode` akkor az elem egyéni hibaüzeneteket is tartalmazhat.
