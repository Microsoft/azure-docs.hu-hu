---
title: Érvényesítési technikai profil definiálása egyéni házirendben
titleSuffix: Azure AD B2C
description: Érvényesítse a jogcímeket egy érvényesítési műszaki profillal a Azure Active Directory B2C egyéni házirendjében.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 65a2eab05e7c475431602d9c2d3fc44b59bbc8f7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185726"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Érvényesítési műszaki profil definiálása egy Azure Active Directory B2C egyéni házirendben

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az érvényesítési technikai profil bármely protokoll, például [Azure Active Directory](active-directory-technical-profile.md) vagy [REST API](restful-technical-profile.md)általános műszaki profilja. Az érvényesítési technikai profil visszaadja a kimeneti jogcímeket, vagy HTTP 409 hibaüzenetet ad vissza (ütközési válasz állapotkód), a következő adatokkal:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Az érvényesítési technikai profilból visszaadott jogcímeket a rendszer visszaadja a jogcímek táskába. Ezeket a jogcímeket a következő érvényesítési technikai profilokban használhatja.

Az érvényesítési műszaki profilok a **ValidationTechnicalProfiles** elemben megjelenő sorozatban lesznek végrehajtva. Egy érvényesítési technikai profilban is konfigurálhatja, hogy a további érvényesítési technikai profilok végrehajtása folytatódjon-e, ha az érvényesítési technikai profil hibát jelez vagy sikeres.

Az érvényesítési műszaki profilok feltételesen hajthatók végre a **ValidationTechnicalProfile** elemben meghatározott előfeltételek alapján. Például megtekintheti, hogy létezik-e egy adott jogcím, vagy ha egy jogcím egyenlő vagy sem a megadott értékkel.

Az önellenőrzött műszaki profilok meghatározhatnak egy érvényesítési műszaki profilt, amelyet a rendszer a kimeneti jogcímek némelyikének vagy mindegyikének ellenőrzéséhez használ. A hivatkozott technikai profil összes bemeneti jogcímének szerepelnie kell a hivatkozó ellenőrzési technikai profil kimeneti jogcímeiben.

> [!NOTE]
> Csak az önjelölt technikai profilok használhatják az érvényesítési technikai profilokat. Ha ellenőrizni szeretné a nem önérvényesített technikai profilokból származó kimeneti jogcímeket, érdemes lehet egy további előkészítési lépést használni a felhasználói úton, hogy az ellenőrzésért felelős műszaki profilt is el lehessen végezni.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

A **ValidationTechnicalProfiles** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | A hivatkozó technikai profil kimeneti jogcímeinek érvényesítésére szolgáló technikai profil. |

A **ValidationTechnicalProfile** elem a következő attribútumot tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| ReferenceId | Igen | A házirend vagy a szülő házirendben már definiált technikai profil azonosítója. |
|ContinueOnError|Nem| Annak jelzése, hogy a további érvényesítési műszaki profilok érvényesítése folytatódjon-e, ha az érvényesítési technikai profil hibát jelez. Lehetséges értékek: `true` vagy `false` (alapértelmezés szerint a további ellenőrzési profilok feldolgozása leáll, és egy hibaüzenetet ad vissza). |
|ContinueOnSuccess | Nem | Annak jelzése, hogy a további ellenőrzési profilok érvényesítése folytatódjon-e, ha az érvényesítési technikai profil sikeres. Lehetséges értékek: `true` vagy `false`. Az alapértelmezett érték `true`, ami azt jelenti, hogy a további ellenőrzési profilok feldolgozása továbbra is fennáll. |

A **ValidationTechnicalProfile** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltételei | 0:1 | Azon előfeltételek listája, amelyeknek meg kell felelniük az érvényesítési technikai profil végrehajtásához. |

Az **előfeltétel** elem a következő attribútumot tartalmazza:

| Attribútum | Szükséges | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Az előfeltételként végrehajtandó ellenőrzés vagy lekérdezés típusa. Vagy `ClaimsExist` van megadva annak biztosításához, hogy a rendszer végrehajtsa a műveleteket, ha a megadott jogcímek szerepelnek a felhasználó jelenlegi jogcímek készletében, vagy `ClaimEquals` van megadva, hogy a rendszer végrehajtja a műveleteket, ha a megadott jogcím létezik, és annak értéke megegyezik a megadott értékkel. |
| `ExecuteActionsIf` | Igen | Azt jelzi, hogy az előfeltételben szereplő műveleteket kell-e végrehajtani, ha a teszt igaz vagy hamis. |

Az **előfeltétel** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1: n | Az ellenőrzés által használt adatértékek. Ha az ellenőrzési típus `ClaimsExist`, akkor ez a mező egy ClaimTypeReferenceId határoz meg a lekérdezéshez. Ha az ellenőrzési típus `ClaimEquals`, akkor ez a mező egy ClaimTypeReferenceId határoz meg a lekérdezéshez. Míg egy másik érték elem tartalmazza az ellenőrizendő értéket.|
| Műveletek | 1:1 | Az a művelet, amelyet akkor kell elvégezni, ha az előkészítési lépésen belüli előfeltétel-ellenőrzési érték igaz. A **művelet** értéke `SkipThisValidationTechnicalProfile`értékre van állítva. Megadja, hogy a társított érvényesítési technikai profilt ne lehessen végrehajtani. |

### <a name="example"></a>Példa

A következő példa ezeket az ellenőrzési technikai profilokat használja:

1. Az első érvényesítési technikai profil ellenőrzi a felhasználói hitelesítő adatokat, és nem folytatja a hibát, ha hiba történik, például érvénytelen Felhasználónév vagy helytelen jelszó.
2. A következő érvényesítési technikai profil nem hajtható végre, ha a userType jogcím nem létezik, vagy ha a userType értéke `Partner`. Az érvényesítési technikai profil megpróbálja beolvasni a felhasználói profilt a belső ügyfél-adatbázisból, és folytatja a hibát, például REST API a szolgáltatás nem érhető el, vagy belső hiba történik.
3. Az utolsó érvényesítés technikai profilja nem hajtható végre, ha a userType jogcím nem létezik, vagy ha a userType értéke `Customer`. Az érvényesítési technikai profil megpróbálja beolvasni a felhasználói profilt a belső partner adatbázisából, és a hiba bekövetkezésekor folytatja, például REST API szolgáltatás nem érhető el, vagy belső hiba történt.

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
