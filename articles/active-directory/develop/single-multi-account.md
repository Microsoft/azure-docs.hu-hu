---
title: Egy-és több fiók nyilvános ügyfélszoftvere | Azure
description: Egy és több fiókból álló nyilvános ügyfélalkalmazások áttekintése.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752977"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Egy-és több fiók nyilvános ügyfélalkalmazások

Ez a cikk segítséget nyújt az egy-és többfiókos nyilvános ügyfélprogramokban használt típusok megismerésében, és az egyfiókos nyilvános ügyfélalkalmazások használatára koncentrál. 

A Azure Active Directory Authentication Library (ADAL) a kiszolgálót modellezi.  A Microsoft Authentication Library (MSAL) Ehelyett az ügyfélalkalmazás modelljét használja.  Az Android-alkalmazások többsége nyilvános ügyfélnek számít. A nyilvános ügyfél olyan alkalmazás, amely nem tudja biztonságosan megőrizni a titkos kulcsot.  

A MSAL az API felületét arra specializálódja, hogy `PublicClientApplication` leegyszerűsítse és tisztázza az alkalmazások fejlesztési élményét, amely egyszerre csak egy fiókot használhat. `PublicClientApplication` a és a alosztálya `SingleAccountPublicClientApplication` `MultipleAccountPublicClientApplication` .  Az alábbi ábrán az osztályok közötti kapcsolat látható.

![SingleAccountPublicClientApplication UML-osztály diagramja](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Egy fiókhoz tartozó nyilvános ügyfélalkalmazás

A `SingleAccountPublicClientApplication` osztály lehetővé teszi, hogy olyan MSAL-alapú alkalmazást hozzon létre, amely egyszerre csak egyetlen fiók bejelentkezni. `SingleAccountPublicClientApplication``PublicClientApplication`a következő módokon különbözik:

- A MSAL nyomon követi a jelenleg bejelentkezett fiókot.
  - Ha az alkalmazás egy közvetítőt használ (az alapértelmezett Azure Portal az alkalmazás regisztrációja során), és olyan eszközre van telepítve, amelyen a közvetítő található, akkor a MSAL ellenőrzi, hogy a fiók továbbra is elérhető-e az eszközön.
- `signIn` lehetővé teszi a fiók explicit módon történő beadását, és a kérelmektől függetlenül.
- `acquireTokenSilent` nincs szükség fiók paraméterre.  Ha megadta a fiókot, és az Ön által megadott fiók nem egyezik meg a MSAL által követett aktuális fiókkal, a `MsalClientException` rendszer kidobja.
- `acquireToken` nem engedélyezi a felhasználó számára a fiókok váltását. Ha a felhasználó egy másik fiókra próbál váltani, kivétel keletkezik.
- `getCurrentAccount` egy eredmény objektumot ad vissza, amely a következőket biztosítja:
  - Logikai érték, amely azt jelzi, hogy a fiók módosult-e. Előfordulhat például, hogy egy fiók az eszközről való eltávolításának eredményeképpen módosul.
  - A korábbi fiók. Ez akkor lehet hasznos, ha a fiókot eltávolítja az eszközről, vagy amikor új fiók van bejelentkezve.
  - A currentAccount.
- `signOut` eltávolítja az ügyfélhez társított jogkivonatokat az eszközről.  

Ha egy androidos hitelesítési közvetítő, például Microsoft Authenticator vagy Intune Céges portál telepítve van az eszközön, és az alkalmazás a közvetítő használatára van konfigurálva, akkor `signOut` nem távolítja el a fiókot az eszközről.

## <a name="single-account-scenario"></a>Egyetlen fiókra vonatkozó forgatókönyv

A következő pszeudo-kód a használatát mutatja be `SingleAccountPublicClientApplication` .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Több fiók nyilvános ügyfélalkalmazás

A `MultipleAccountPublicClientApplication` osztály olyan MSAL-alapú alkalmazások létrehozására szolgál, amelyek lehetővé teszik, hogy egyszerre több fiók legyen bejelentkezve. Lehetővé teszi a fiókok beszerzését, hozzáadását és eltávolítását a következőképpen:

### <a name="add-an-account"></a>Fiók hozzáadása

Használjon egy vagy több fiókot az alkalmazásban `acquireToken` egy vagy több alkalommal történő meghívásával.  

### <a name="get-accounts"></a>Fiókok beolvasása

- `getAccount`Egy adott fiók beszerzésének meghívása.
- Hívja `getAccounts` meg az alkalmazást aktuálisan ismert fiókok listájának lekérését.

Az alkalmazás nem tudja felsorolni az összes Microsoft Identity platform-fiókot az eszközön, amely a közvetítő alkalmazás számára ismert. Csak azokat a fiókokat sorolja fel, amelyeket az alkalmazás használ.  Az eszközről eltávolított fiókokat ezek a függvények nem adják vissza.

### <a name="remove-an-account"></a>Fiók eltávolítása

Fiók azonosítójának meghívásával távolítsa el a fiókot `removeAccount` .

Ha az alkalmazás egy közvetítő használatára van konfigurálva, és egy közvetítő van telepítve az eszközön, akkor a rendszer nem távolítja el a fiókot a közvetítőtől a hívásakor `removeAccount` .  A rendszer csak az ügyfélhez társított jogkivonatokat távolítja el.

## <a name="multiple-account-scenario"></a>Több fiók esetén

A következő pszeudo-kód bemutatja, hogyan hozhat létre több fiókból álló alkalmazást, hogyan listázhatja a fiókokat az eszközön, és hogyan vásárolhat jogkivonatokat.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
