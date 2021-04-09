---
title: Az Azure AD Schema Extension attribútumainak használata a jogcímek között
titleSuffix: Microsoft identity platform
description: Ismerteti, hogyan használhatók a címtár-séma bővítmény attribútumai a felhasználói adatoknak a jogkivonat-jogcímek alkalmazásaiba való küldéséhez.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755727"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>A Directory sémakezelő bővítmény attribútumainak használata a jogcímek között

A címtár-séma bővítmény attribútumai lehetővé teszik a további adatok tárolását a felhasználói objektumok és más címtárobjektumok (például csoportok, bérlői adatok, egyszerű szolgáltatások) Azure Active Directory.  Csak a felhasználói objektumok bővítmény-attribútumai használhatók jogcímek alkalmazásokhoz való kibocsátására. Ez a cikk azt ismerteti, hogyan használhatók a címtár-séma bővítmény attribútumai a felhasználói adatoknak a jogkivonat-jogcímek alkalmazásaiba való küldéséhez.

> [!NOTE]
> A Microsoft Graph két másik kiterjesztési mechanizmust biztosít a Graph-objektumok testreszabásához. Ezek Microsoft Graph Open Extensions és Microsoft Graph Schema Extensions néven ismertek. A részletekért tekintse meg a [Microsoft Graph dokumentációját](/graph/extensibility-overview) . Az ezen képességeket használó Microsoft Graph objektumokban tárolt adatforrások nem érhetők el a jogkivonatokban lévő jogcímek forrásaként.

A címtár-séma bővítmény attribútumai mindig a bérlő egyik alkalmazásához vannak társítva, és az alkalmazás *applicationId* hivatkoznak a nevükben.

A Directory sémakezelő bővítmény attribútumának azonosítója *Extension_xxxxxxxxx_AttributeName*.  Ahol a *XXXXXXXXX* annak az alkalmazásnak a *applicationId* , amelyhez a bővítményt meghatározták.

## <a name="registering-and-using-directory-schema-extensions"></a>A címtár-séma bővítményeinek regisztrálása és használata
A címtár-séma bővítményeinek attribútumai kétféleképpen regisztrálhatók és tölthetők fel:

- Az AD-kapcsolódás konfigurálásával létrehozhatók és szinkronizálhatók az adatok a helyszíni AD-ből. Lásd: [Azure ad Connect Sync Directory Extensions](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- A Microsoft Graph használatával regisztrálhat, beállíthatja és beolvashatja a séma- [bővítmények](/graph/extensibility-overview)értékét. A [PowerShell-parancsmagok](/powershell/azure/active-directory/using-extension-attributes-sample) is elérhetők.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Jogcímek kibocsátása az AD-kapcsolattal létrehozott címtár-séma bővítmény attribútumaiból származó adatokkal
Az AD-kapcsolat használatával létrehozott és szinkronizált címtár-séma-bővítményi attribútumok mindig az AD-kapcsolat által használt alkalmazás-AZONOSÍTÓhoz vannak társítva. A jogcímek forrásaként is használhatók, ha a **vállalati** alkalmazások konfigurációjában jogcímként konfigurálja azokat az SAML-alkalmazások számára, amelyeket a katalógus vagy a katalóguson kívüli alkalmazás konfigurálásával, a **vállalati alkalmazások** esetében regisztrálnak  Miután az AD-kapcsolaton keresztül létrehozott egy címtár-kiterjesztési attribútum szerepel a címtárban, az SAML SSO-jogcímek konfigurációs felhasználói felületén fog megjelenni.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Olyan jogcímek kibocsátása, amelyek a Graph vagy a PowerShell használatával egy alkalmazáshoz létrehozott címtár-séma bővítmény attribútumaiból származó adatokkal rendelkeznek.
Ha a Directory sémakezelő bővítmény attribútuma Microsoft Graph vagy PowerShell használatával van regisztrálva (az alkalmazások kezdeti beállítása vagy üzembe helyezési lépése alapján), akkor ugyanaz az alkalmazás konfigurálható a Azure Active Directoryban, hogy az adott attribútumban lévő adatok a felhasználó bejelentkezésekor a jogcímben lévő felhasználói objektumból fogadhasson.  Az alkalmazás úgy konfigurálható, hogy olyan címtár-séma-bővítményekben fogadja az adatfogadást, amelyek nem [kötelező jogcímeket](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)használnak ugyanazon az alkalmazáson.  Ezek megadhatók az alkalmazás jegyzékfájljában.  Ez lehetővé teszi, hogy a több-bérlős alkalmazások saját használatra regisztrálják a címtár-séma bővítmény attribútumait. Ha az alkalmazás egy bérlőhöz lett kiépítve, a társított címtár-séma bővítményei elérhetővé válnak a bérlő felhasználói számára, és felhasználhatók.  Miután konfigurálta a bérlőt és a hozzájárulást, felhasználhatja az adatok gráfon keresztüli tárolására és lekérésére, valamint a jogkivonatokban lévő jogcímekre való leképezésre, amelyeket a Microsoft Identity platform bocsát ki az alkalmazásokba.

A címtár-séma bővítmény attribútumai regisztrálhatók és kitölthetők bármely alkalmazáshoz.

Ha egy alkalmazásnak egy másik alkalmazásban regisztrált kiterjesztési attribútumból származó adatokkal kell elküldenie a jogcímeket, a jogcímek [leképezésére szolgáló szabályzatot](active-directory-claims-mapping.md) fel kell használni a bővítmény attribútumnak a jogcímre való leképezéséhez.  A címtár-séma bővítmény attribútumainak kezelésére szolgáló közös minta egy olyan alkalmazás létrehozása, amely kifejezetten a szükséges séma-bővítmények regisztrálási pontja.  Nem kell valódi alkalmazásnak lennie, és ez a technika azt jelenti, hogy az összes bővítmény ugyanazzal az alkalmazás-AZONOSÍTÓval rendelkezik a nevükben.

Íme például egy jogcím-hozzárendelési szabályzat, amely egyetlen jogcímet bocsát ki egy OAuth-vagy OIDC-jogkivonat címtár-séma bővítmény-attribútumában:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Ahol a *xxxxxxx* annak az alkalmazásnak az azonosítója, amelyben a bővítmény regisztrálva van.

> [!TIP]
> A kis-és nagybetűk közötti konzisztencia akkor fontos, ha a címtár-bővítmény attribútumait az objektumokon beállítja  A bővítmény-attribútumok nevei nem érzékenyek a beállításra, de a rendszer megkülönbözteti a kis-és nagybetűket, amikor a jogkivonat-szolgáltatás beolvassa a címtárból.  Ha egy "LegacyId" nevű felhasználói objektumra és egy "LegacyId" nevű másik felhasználói objektumra van beállítva, akkor ha az attribútum a "LegacyId" név használatával van leképezve egy jogcímre, akkor a rendszer sikeresen beolvassa az adatgyűjtést, és az első felhasználóhoz tartozó jogkivonatban szereplő jogcímet, a másodikat nem.
>
> A beépített címtár attribútumaihoz használt "id" paraméter a "ExtensionID" a címtár-bővítmény attribútumaihoz.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan [adhat hozzá egyéni vagy további jogcímeket a SAML 2,0 és a JSON web tokens (JWT) jogkivonatokhoz](active-directory-optional-claims.md).
- Megtudhatja, hogyan [szabhatja testre a jogkivonatokban kibocsátott jogcímeket egy adott alkalmazáshoz](active-directory-claims-mapping.md).