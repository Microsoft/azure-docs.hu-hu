---
title: A megjelenítési vezérlőkkel rendelkező jogcímek ellenőrzése
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan használhatók a Azure AD B2C megjelenítési vezérlők az egyéni szabályzatok által biztosított felhasználói úton lévő jogcímek ellenőrzéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94532494"
---
# <a name="verification-display-control"></a>Ellenőrzés megjelenítésének vezérlője

Egy ellenőrző [megjelenítési vezérlőelem](display-controls.md) használatával ellenőrizheti a jogcímeket, például egy e-mail-címet vagy telefonszámot a felhasználónak küldött ellenőrző kóddal.

## <a name="verificationcontrol-actions"></a>VerificationControl műveletek

A hitelesítési megjelenítés vezérlőelem két lépésből áll (műveletek):

1. Kérjen meg egy célhelyet a felhasználónak, például egy e-mail-címet vagy telefonszámot, amelybe az ellenőrző kódot el kell juttatni. Amikor a felhasználó a **kód küldése** gombra kattint, a rendszer végrehajtja a **SendCode műveletet** a hitelesítés megjelenítése vezérlőben. A **SendCode művelet** létrehoz egy kódot, létrehozza a küldendő tartalmat, és elküldi azt a felhasználónak. A címek értéke előre kitölthető, és másodlagos faktoros hitelesítésként szolgál.

    ![Példa a kód küldésére műveletre](media/display-control-verification/display-control-verification-email-action-01.png)

1. A kód elküldése után a felhasználó beolvassa az üzenetet, beírja az ellenőrző kódot a megjelenítési vezérlő által megadott vezérlőelembe, és kiválasztja a **kód ellenőrzése** lehetőséget. A **kód ellenőrzése** lehetőség kiválasztásával a rendszer végrehajtja a **VerifyCode műveletet** a címben társított kód ellenőrzéséhez. Ha a felhasználó kiválasztja az **új kód küldése** lehetőséget, a rendszer ismét végrehajtja az első műveletet.

    ![Példa a kód ellenőrzésére műveletre](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>VerificationControl szükséges elemek

A **VerificationControl** tartalmaznia kell a következő elemeket:

- A típusa `DisplayControl` `VerificationControl` .
- `DisplayClaims`
  - **Küldje el** az-egy vagy több jogcímet, amely megadja, hogy hová szeretné elküldeni az ellenőrző kódot. Például: *e-mail-cím* , *országkód* és *telefonszám*.
  - **Ellenőrző kód** – az ellenőrző kód jogcíme, amelyet a felhasználó a kód elküldése után biztosít. Ezt a jogcímet kötelezőként kell beállítani, és a értékének a következőnek kell `ControlClaimType` lennie: `VerificationCode` .
- A kimeneti jogcímet (opcionális) vissza kell adni az önérvényesített oldalra, miután a felhasználó befejezte az ellenőrzési folyamatot. Például: *e-mail-cím* , *országkód* és *telefonszám*. Az önérvényesített technikai profil a jogcímeket használja az adatmegőrzéshez, vagy a kimeneti jogcímeket a következő előkészítési lépéshez adja.
- Két `Action` s a következő nevekkel:
  - **SendCode** – kódot küld a felhasználónak. Ez a művelet általában két ellenőrzési technikai profilt tartalmaz, egy kód létrehozásához és az elküldéséhez.
  - **VerifyCode** – ellenőrzi a kódot. Ez a művelet általában egyetlen ellenőrzési technikai profilt tartalmaz.

Az alábbi példában egy **e-mail-** szövegmező jelenik meg az oldalon. Ha a felhasználó megadja az e-mail-címét, és kiválasztja a **SendCode**-t, a **SendCode** műveletet a rendszer Azure ad B2C a háttérben indítja el.

Ezután a felhasználó beírja a **verificationCode** , és kiválasztja a **VerifyCode** lehetőséget, hogy aktiválja a **VerifyCode** műveletet a háttérben. Ha az összes érvényesítés sikeres, a **VerificationControl** teljesnek minősül, és a felhasználó továbbra is folytathatja a következő lépéssel.

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
