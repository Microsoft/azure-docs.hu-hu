---
title: Sub utazások a Azure Active Directory B2Cban | Microsoft Docs
description: Egy egyéni házirend Alútvonal elemének megadásával Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97386867"
---
# <a name="sub-journeys"></a>Alárendelt útvonalak

Az alárendelt útvonalak segítségével rendezheti és egyszerűsítheti a folyamat lépéseinek menetét a felhasználói úton. A [felhasználói interakciósorozatok](userjourneys.md) explicit elérési utakat határoznak meg, amelyen keresztül egy szabályzat lehetővé teszi a jogcímfelhasználó alkalmazásnak a kívánt jogcímek beszerzését a felhasználó számára. A felhasználó ezeken az elérési utakon keresztül kérheti le a jogcímeket, amelyeket bemutathat a függő entitásnak. Más szóval a felhasználói útvonalak határozzák meg azt az üzleti logikát, amit a végfelhasználó a Azure AD B2C identitás-kezelési keretrendszere feldolgozza a kérést. A felhasználói út olyan összehangoló sorozatot jelöl, amelyet egy sikeres tranzakcióhoz kell követni. Egy előkészítési lépés [ClaimsExchange](userjourneys.md#claimsexchanges) eleme egyetlen [technikai profilhoz](technicalprofiles.md) van kötve, amely végre van hajtva.

A sub Journey olyan előkészítési lépések csoportosítása, amelyek a felhasználói úton lévő bármely ponton meghívhatók. Az alútvonalak használatával újrafelhasználható lépések hozhatók létre, illetve az üzleti logikát jobban reprezentáló elágazás valósítható meg.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Felhasználói utazás elágazása

Az alárendelt útvonalak ugyanúgy viselkednek, mint a [felhasználói útvonalak](userjourneys.md), mivel mindkettő olyan előkészítési sorozatot képvisel, amelyet egy sikeres tranzakcióhoz kell követni. A felhasználói útvonalak saját maguk is meghívhatók, és a végrehajtáshoz SendClaims lépés szükséges. Az alárendelt útvonalak a felhasználói utazások összetevői, és nem hívhatók meg egymástól függetlenül, és a rendszer mindig egy felhasználói útra hívja meg őket.

Az elágazás legfontosabb összetevője, hogy lehetővé tegye az üzleti logika jobb feldolgozását a felhasználói úton. A közös előkészítési lépések különálló darabokra vannak csoportosítva, amelyeket külön kell meghívni. A sub Journey leegyszerűsítheti az utazást, ahol több összekapcsolási lépés is összekapcsolható (azonos előfeltételekkel). Egy Alútvonal csak egy felhasználói útra hívható, ezért nem hívhat meg egy másik sub-utazást.

Az alárendelt útvonalak két típusa létezik:

- **Call** -visszaadja a vezérlőt a hívónak. Az Alútvonal végre lesz hajtva, majd a vezérlés visszakerül a felhasználói útra jelenleg végrehajtás alatt álló előkészítési lépéshez.
- **Átvitel** – átviszi a vezérlést az alárendelt útra (visszafordíthatatlan elágazás). Az alárendelt útnak rendelkeznie kell egy SendClaims lépéssel, hogy visszaadja a jogcímeket a függő entitás alkalmazásának.

## <a name="example-scenarios"></a>Példaforgatókönyvek

### <a name="call-sub-journey"></a>Alárendelt út hívása

A hívások alútvonala a következő esetekben hasznos:

- Age kapuzás: az Age kapuzás esetében számos közös összetevő van a felhasználói útvonalak között. Az elágazás lehetővé teszi a közös elemek megosztható összetevőkbe való fordítását.  
- Szülői engedély: az elágazás lehetővé teszi a szülői beleegyező kialakítás kényelmét azáltal, hogy lehetővé teszi, hogy a felhasználótól érkező jogcímeket elérjük a kiskorú által végrehajtott, valamint a szülői beleegyező felhasználói útra, miután megtalálta, hogy a felhasználó beleegyezik. 
- Regisztráljon a bejelentkezéshez: Vegyünk egy olyan forgatókönyvet, amelyben egy felhasználó már létezik a címtárban, de elfelejtette, hogy valóban létrehozott egy fiókot. Előfordulhat, hogy a felhasználónak nem kell megadnia, hogy a megadott hitelesítő adatok már léteznek, és arra kényszerítse a felhasználót, hogy indítsa újra az utat, hogy a házirend képes legyen a feliratkozási folyamatból az adott felhasználó bejelentkezési folyamatára váltani.  

### <a name="transfer-sub-journey"></a>Átvitel alútvonala

Az átvitel alútvonala az alábbi helyzetekben hasznos:

- Egy blokk oldal megjelenítése.
- A/B tesztelés, ha a kérést egy alútvonalra irányítja, hogy végrehajtsa és kiadja a jogkivonatot.

## <a name="adding-a-subjourneys-element"></a>Alútvonal-elem hozzáadása

A következő példa egy `SubJourney` típusú elemre `Call` mutat, amely visszaadja a vezérlést a felhasználói útra.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Az alábbi példa egy `SubJourney` típusú elemet `Transfer` mutat be, amely visszaadja a tokent a függő entitás alkalmazásának.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>Alútvonali lépés meghívása

A rendszer egy új előkészítési lépést `InvokeSubJourney` használ egy alárendelt út végrehajtásához. A következő példa egy példát mutat be a jelen előkészítési lépés összes végrehajtási elemére.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Egy Alútvonal nem hívhat meg egy másik alútvonalat.

## <a name="components"></a>Összetevők

A szabályzat által támogatott alárendelt útvonalak definiálásához adjon hozzá egy **Alútvonal** elemet a házirendfájl legfelső szintű eleméhez.

A **alútvonalak** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Alútvonal | 1: n | Egy alárendelt út, amely meghatározza a teljes felhasználói folyamathoz szükséges összes szerkezetet. |

A **alútvonalak** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Id | Yes | Az Alútvonal-azonosító, amelyet a felhasználó a szabályzat alútvonalára való hivatkozáshoz használhat. A [jelölt](userjourneys.md#journeylist) elem **SubJourneyReferenceId** eleme erre az attribútumra mutat. |
| Típus | Yes | Lehetséges értékek: `Call` , vagy `Transfer` . További információ: [felhasználói utazás elágazása](#user-journey-branching)|

A **Alútvonal** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Egy olyan előkészítési sorozatot, amelyet követni kell egy sikeres tranzakcióhoz. Minden felhasználói út a sorrendben végrehajtott előkészítési lépések rendezett listáját tartalmazza. Ha bármelyik lépés meghiúsul, a tranzakció meghiúsul. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

A előkészítési lépés elemeinek teljes listáját lásd: [UserJourneys](userjourneys.md).

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [UserJourneys](userjourneys.md)