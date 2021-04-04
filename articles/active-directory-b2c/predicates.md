---
title: Predikátumok és PredicateValidations
titleSuffix: Azure AD B2C
description: A helytelenül formázott adatok nem adhatók hozzá a Azure AD B2C-bérlőhöz egyéni szabályzatok használatával Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108416"
---
# <a name="predicates-and-predicatevalidations"></a>Predikátumok és PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **predikátumok** és a **PredicateValidations** elemek lehetővé teszik egy ellenőrzési folyamat elvégzését annak érdekében, hogy csak a megfelelően formázott adatok legyenek beírva a Azure Active Directory B2C (Azure ad B2C) bérlőbe.

Az alábbi ábrán az elemek közötti kapcsolat látható:

![Predikátumok és predikátumok érvényességi kapcsolatát bemutató ábra](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikátumok

A **predikátum** elem egy alapszintű érvényesítést határoz meg a jogcím típusának és a visszaadott értékének ellenőrzéséhez `true` `false` . Az érvényesítés egy megadott **metódus** elem és a metódushoz tartozó **Paraméterek** elemeinek használatával történik. Egy predikátum például megtekintheti, hogy a karakterlánc-jogcím értéke a megadott minimális és maximális paraméterek tartományán belül van-e, illetve hogy egy karakterlánc-jogcím értéke tartalmaz-e karakterkészletet. A **UserHelpText** elem hibaüzenetet jelenít meg a felhasználók számára, ha az ellenőrzés sikertelen. A **UserHelpText** elem értéke honosítható a [nyelvi Testreszabás](localization.md)használatával.

A **predikátumok** elemnek közvetlenül a [BuildingBlocks](buildingblocks.md) elem **ClaimsSchema** eleme után kell megjelennie.

A **predikátumok** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Predikátum | 1: n | Predikátumok listája. |

A **predikátum** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A predikátumhoz használt azonosító. Más elemek is használhatják ezt az azonosítót a szabályzatban. |
| Metódus | Yes | Az érvényesítéshez használandó metódus típusa Lehetséges értékek: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)vagy [IsDateRange](#isdaterange).  |
| HelpText | No | Hibaüzenet a felhasználók számára, ha az ellenőrzés sikertelen. Ez a karakterlánc honosítható a [nyelvi Testreszabás](localization.md) használatával |

A **predikátum** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | Elavult Hibaüzenet a felhasználók számára, ha az ellenőrzés sikertelen. |
| Paraméterek | 1:1 | A karakterlánc-érvényesítési metódus típusának paraméterei |

A **Parameters (paraméterek** ) elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Paraméter | 1: n | A karakterlánc-érvényesítési metódus típusának paraméterei |

A **paraméter** elem a következő attribútumokat tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Id | 1:1 | A paraméter azonosítója. |

### <a name="predicate-methods"></a>Predikátum-metódusok

#### <a name="islengthrange"></a>IsLengthRange

A IsLengthRange metódus ellenőrzi, hogy a karakterlánc-jogcím értéke a megadott minimális és maximális paraméterek tartományán belül van-e. A predikátum elem a következő paramétereket támogatja:

| Paraméter | Kötelező | Leírás |
| ------- | ----------- | ----------- |
| Maximum | Yes | A megadható karakterek maximális száma. |
| Minimális | Yes | A karakterek minimális számának megadása kötelező. |


A következő példa egy IsLengthRange metódust mutat be a paraméterekkel, `Minimum` `Maximum` amelyek meghatározzák a karakterlánc hosszának tartományát:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

A MatchesRegex metódus ellenőrzi, hogy egy karakterlánc-jogcím értéke megfelel-e egy reguláris kifejezésnek. A predikátum elem a következő paramétereket támogatja:

| Paraméter | Kötelező | Leírás |
| ------- | ----------- | ----------- |
| Válaszban | Yes | Az egyeztetendő reguláris kifejezési minta. |

A következő példa egy `MatchesRegex` metódust mutat be a paraméterrel, `RegularExpression` amely egy reguláris kifejezést határoz meg:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

A IncludesCharacters metódus ellenőrzi, hogy egy karakterlánc-jogcím értéke tartalmazza-e a karakterkészletet. A predikátum elem a következő paramétereket támogatja:

| Paraméter | Kötelező | Leírás |
| ------- | ----------- | ----------- |
| CharacterSet | Yes | A megadható karakterek halmaza. Például kisbetűk, nagybetűk,  `a-z` `A-Z` számjegyek `0-9` vagy szimbólumok listája, például: `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

A következő példa egy `IncludesCharacters` metódust mutat be a paraméterrel, `CharacterSet` amely megadja a karakterek készletét:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

A IsDateRange metódus ellenőrzi, hogy a Date jogcím értéke a megadott minimális és maximális paraméterek közé esik-e. A predikátum elem a következő paramétereket támogatja:

| Paraméter | Kötelező | Leírás |
| ------- | ----------- | ----------- |
| Maximum | Yes | A legnagyobb lehetséges dátum, amelyet meg lehet adni. A dátum formátuma a következő: `yyyy-mm-dd` egyezmény vagy `Today` . |
| Minimális | Yes | A legkisebb lehetséges dátum, amelyet meg lehet adni. A dátum formátuma a következő: `yyyy-mm-dd` egyezmény vagy `Today` .|

Az alábbi példa egy `IsDateRange` metódust mutat be a paraméterekkel, `Minimum` `Maximum` amelyek a dátumtartományt a és a formátumával határozzák meg `yyyy-mm-dd` `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Míg a predikátumok határozzák meg a jogcím típusának ellenőrzéséhez szükséges érvényesítést, a **PredicateValidations** csoport olyan predikátumok halmazát adja meg, amelyek a jogcím típusára alkalmazható felhasználói bemeneti érvényesítést hoznak létre. Minden **PredicateValidation** elem olyan **PredicateGroup** -elemeket tartalmaz, amelyek egy **predikátumra** mutató **PredicateReference** elemek készletét tartalmazzák. Az ellenőrzés elvégzéséhez a jogcím értékének át kell haladnia a **PredicateGroup** összes olyan tesztjét, amely az összes **PredicateReference** -elemet tartalmazza.

A **PredicateValidations** elemnek közvetlenül kell szerepelnie a **predikátumok** elemnél a [BuildingBlocks](buildingblocks.md) elemen belül.

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

A **PredicateValidations** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | A predikátum érvényesítésének listája. |

A **PredicateValidation** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A predikátum érvényesítéséhez használt azonosító. A **claimType** elem a szabályzatban használhatja ezt az azonosítót. |

A **PredicateValidation** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Predikátum-csoportok listája. |

A **PredicateGroups** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Predikátumok listája. |

A **PredicateGroup** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A predikátum-csoporthoz használt azonosító.  |

A **PredicateGroup** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  A predikátum leírása, amely hasznos lehet a felhasználók számára, hogy tudják, milyen értékeket kell beírniuk. |
| PredicateReferences | 1: n | A predikátumokra vonatkozó hivatkozások listája. |

A **PredicateReferences** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| MatchAtLeast | No | Megadja, hogy az értéknek meg kell egyeznie legalább annyi predikátum-definícióval, amelyet a bemenet elfogad. Ha nincs megadva, az értéknek meg kell egyeznie az összes predikátum-definícióval. |

A **PredicateReferences** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Egy predikátumra mutató hivatkozás. |

A **PredicateReference** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | A predikátum érvényesítéséhez használt azonosító.  |


## <a name="configure-password-complexity"></a>Jelszó bonyolultságának konfigurálása

A **predikátumok** és a **PredicateValidationsInput** segítségével szabályozhatja a felhasználók által a fiók létrehozásakor biztosított jelszavak összetettségi követelményeit. Alapértelmezés szerint a Azure AD B2C erős jelszavakat használ. A Azure AD B2C az ügyfelek által használható jelszavak bonyolultságának szabályozásához is támogatja a konfigurációs beállításokat. A jelszó bonyolultságát a következő predikátum-elemek használatával határozhatja meg:

-  A `IsLengthRange` metódust használó IsLengthBetween8And64 ellenőrzi, hogy a jelszónak 8 és 64 karakter közöttinek kell lennie.
- **Kisbetűs** a `IncludesCharacters` metódus használatával ellenőrzi, hogy a jelszó kisbetűs betűt tartalmaz-e.
- A metódust használó **nagybetűkkel** `IncludesCharacters` ellenőrzi, hogy a jelszó nagybetűt tartalmaz-e.
-  A `IncludesCharacters` metódust használó szám azt ellenőrzi, hogy a jelszó tartalmaz-e számjegyet.
-  A `IncludesCharacters` metódust használó szimbólum azt ellenőrzi, hogy a jelszóban szerepel-e több szimbólum karakter.
- **PIN-kód** a `MatchesRegex` metódus használatával ellenőrzi, hogy a jelszó csak számokat tartalmaz-e.
-  A `MatchesRegex` metódust használó AllowedAADCharacters ellenőrzi, hogy a jelszó csak érvénytelen karaktert adott-e meg.
-  A `MatchesRegex` metódust használó DisallowedWhitespace ellenőrzi, hogy a jelszó nem kezdődik vagy végződhet szóköz karakterrel.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Az alapszintű érvényesítések meghatározása után összekapcsolhatja őket, és létrehozhat egy olyan jelszóházirend-készletet, amelyet használhat a szabályzatban:

- A **SimplePassword** ellenőrzi a DisallowedWhitespace, a AllowedAADCharacters és a IsLengthBetween8And64
- A **StrongPassword** érvényesíti a DisallowedWhitespace, a AllowedAADCharacters és a IsLengthBetween8And64. Az utolsó csoport `CharacterClasses` egy további predikátumokat futtat, amelyek `MatchAtLeast` értéke 3. A felhasználói jelszónak 8 és 16 karakter közöttinek kell lennie, és a következő karakterek közül hármat kell megadnia: kisbetűs, nagybetűs, szám vagy szimbólum.
- A **CustomPassword** csak a DisallowedWhitespace, a AllowedAADCharacters érvényesíti. Így a felhasználó bármilyen hosszúságú jelszót biztosíthat, feltéve, hogy a karakterek érvényesek.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

A jogcím típusa mezőben adja hozzá az **PredicateValidationReference** elemet, és adja meg az azonosítót az predikátumok egyikének (például SimplePassword, StrongPassword vagy CustomPassword).

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Az alábbi ábrán látható, hogy az elemek hogyan vannak rendszerezve, amikor Azure AD B2C megjeleníti a hibaüzenetet:

![A predikátum és a PredicateGroup-jelszó bonyolultsági példájának ábrája](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Dátumtartomány konfigurálása

A **predikátumok** és a **PredicateValidations** elemek segítségével a **UserInputType** minimális és maximális értékeit is szabályozhatja `DateTimeDropdown` . Ehhez hozzon létre egy **predikátumot** a `IsDateRange` metódussal, és adja meg a minimális és a maximális paramétereket.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Adjon hozzá egy, a predikátumra mutató hivatkozást tartalmazó **PredicateValidation** `DateRange` .

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

A jogcím típusa mezőben adja hozzá az **PredicateValidationReference** elemet, és adja meg az azonosítót a következőként: `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [konfigurálhatja a jelszó-bonyolultságot a Azure Active Directory B2C egyéni házirendjeivel a](password-complexity.md) predikátumok érvényességének használatával.