---
title: Egy egyéni házirendek az Azure Active Directory B2C egy érvényesítési technikai profil meghatározása |} A Microsoft Docs
description: Adja meg az Azure Active Directory B2C-vel egyéni szabályzatok az Azure Active Directory technikai profil.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 466ed4c2bd353e4a5ec3bec5535b70a90446ee0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418280"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Egyéni Azure Active Directory B2C-házirendek egy érvényesítési technikai profil meghatározása

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egy ellenőrzési technikai profil el például egy szokásos technikai profil minden olyan protokoll, a [Azure Active Directory](active-directory-technical-profile.md) vagy egy [REST API-val](restful-technical-profile.md). Az érvényesítés technikai profilban adja vissza a kimeneti jogcímek, vagy adja vissza a hibaüzenet jelenik a HTTP 409 (ütközés válasz állapotkód), az alábbi adatokat:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

A rendszer adja vissza egy érvényesítési technikai profil jogcímeket vissza a jogcímek tulajdonságcsomag kerülnek. A következő érvényesítési technikai profilban használhatja azokat a jogcímeket.

Érvényesítési technikai profilok végrehajtása a sorrendben jelennek meg a **ValidationTechnicalProfiles** elemet. Konfigurálhatja egy érvényesítési technikai profilban bármely ezt követő érvényesítési technikai profilok végrehajtásának kell továbbra is, ha az érvényesítés technikai profilban hibát jelez, vagy sikeres-e.

Technikai profil feltételesen végrehajtani érvényesítést alapján meghatározott előfeltételeket a **ValidationTechnicalProfile** elemet. Ellenőrizheti például, hogy egy adott jogcímek létezik, vagy ha jogcím értéke vagy és a megadott érték nem.

Az önellenőrzött technikai profil egy érvényesítési technikai profil ellenőrzése vagy azok egy részét a kimeneti jogcímek használandó adhat meg. A bemeneti jogcímek között, a hivatkozott technikai profil összes szerepelnie kell a kimeneti jogcímek a hivatkozó érvényesítési technikai profil.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

A **ValidationTechnicalProfiles** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | A technikai profil ellenőrzése vagy azok egy részét a kimeneti jogcímek a hivatkozó technikai profil használható. |

A **ValidationTechnicalProfile** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | Egy már definiált vagy a szülő házirend technikai profil azonosítója. |
|ContinueOnError|Nem| Jelzi, hogy bármely ezt követő érvényesítési technikai profilok érvényesítése továbbra is ha az érvényesítés technikai profil hibát jelez. A lehetséges értékek: `true` vagy `false` (alapértelmezés szerint a további ellenőrzési profilok feldolgozása leáll, és hibát adott vissza). |
|ContinueOnSuccess | Nem | Jelzi, hogy bármely ezt követő érvényesítési profilok érvényesítése továbbra is ha az érvényesítés technikai profil sikeres. A lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `true`, tehát továbbra is a további ellenőrzési profilok feldolgozására. |

A **ValidationTechnicalProfile** elem tartalmazza a következő elemet:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Az Előfeltételek | 0:1 | Az érvényesítés technikai profil végrehajtásához kell biztosítani az Előfeltételek listáját. |

A **előfeltétel** elem tartalmazza a következő attribútumot:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Ellenőrizze vagy végrehajtani az előfeltétel-lekérdezés típusa. Mindkét `ClaimsExist` van megadva, győződjön meg arról, hogy műveleteket kell elvégezni, ha a megadott jogcím szerepel a felhasználó aktuális jogcímek készletében, vagy `ClaimEquals` van megadva, hogy a műveleteket kell elvégezni, ha a megadott jogcím létezik, és a hozzá tartozó érték megegyezik a megadott értéket. |
| `ExecuteActionsIf` | Igen | Azt jelzi, hogy e előfeltétele a műveleteket kell elvégezni, ha a tesztelés true vagy FALSE (hamis). |

A **előfeltétel** elem a következő elemeket tartalmazza:

| Elem | Ismétlődések | Leírás |
| ------- | ----------- | ----------- |
| Value | 1:n | Az ellenőrzés által használt adatokat. Ha ez az ellenőrzés típusát `ClaimsExist`, ebben a mezőben adja meg egy ClaimTypeReferenceId lekérdezéséhez. Ha a jelölőnégyzet típusú `ClaimEquals`, ebben a mezőben adja meg egy ClaimTypeReferenceId lekérdezéséhez. Míg egy másik értéket elem tartalmazza az ellenőrizendő értékkel.|
| Műveletek | 1:1 | Az előfeltétel-ellenőrzés belül egy vezénylési lépés teljesülése esetén végrehajtandó művelet. Értékét a **művelet** értékre van állítva `SkipThisValidationTechnicalProfile`. Itt adhatja meg, hogy a társított ellenőrzési technikai profil nem lehet végrehajtani. |

### <a name="example"></a>Példa

A követező példában érvényesítési technikai profilok:

1. Az első érvényesítési technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és nem folytatódik, ha hiba történik, mint például a érvénytelen felhasználónév vagy helytelen jelszó.
2. A következő érvényesítési technikai profil nem hajtható végre, ha a userType jogcím nem létezik, vagy ha a userType értéke `Partner`. Az ellenőrzési technikai profil megpróbálja a felhasználói profil olvasása a belső felhasználói adatbázisból, és továbbra is, ha hiba történik, például REST API-szolgáltatás nem érhető el, vagy bármely belső hiba.
3. Az utolsó ellenőrzési technikai profil nem hajtható végre, ha a userType jogcím még nem létezett, vagy ha a userType értéke `Customer`. Az ellenőrzési technikai profil megpróbálja beolvasni a felhasználói profilt a belső partneradatbázis, és továbbra is fennáll, ha hiba történik, például REST API-szolgáltatás nem érhető el, vagy bármely belső hiba.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
