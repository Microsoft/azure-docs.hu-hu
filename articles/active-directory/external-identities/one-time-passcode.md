---
title: Egyszeres PIN-kódos hitelesítés B2B vendégfelhasználók számára – Azure AD
description: Az e-mailes egyszer használt PIN-kód használata a B2B-vendégfelhasználók hitelesítéséhez anélkül, hogy Microsoft-fiók.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529914"
---
# <a name="email-one-time-passcode-authentication"></a>E-mailes, egyszer használt PIN-kódos hitelesítés

Ez a cikk azt ismerteti, hogyan engedélyezheti az e-mailes egyszer használható PIN-kódos hitelesítést a B2B vendégfelhasználók számára. Az e-mailes pin-kód funkció akkor hitelesíti a B2B-vendégfelhasználókat, ha nem hitelesíthetők más módon, például az Azure AD-n, Microsoft-fiók -n (MSA) vagy Google-összevonáson keresztül. Az egyszeres PIN-kódos hitelesítéssel nem kell új jelszót Microsoft-fiók. Amikor a vendégfelhasználó bevált egy meghívót, vagy hozzáfér egy megosztott erőforráshoz, ideiglenes kódot kérhet, amelyet a rendszer elküld az e-mail-címére. Ezután meg kell adnia ezt a kódot a bejelentkezés folytatásához.

![E-mailes, egyszer megadott PIN-kód áttekintése diagram](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **2021** októberéig az e-mailes egyszer használható PIN-kód funkció be lesz kapcsolva az összes meglévő bérlőn, és alapértelmezés szerint engedélyezve lesz az új bérlők számára. Ha nem szeretné engedélyezni a funkció automatikus bekapcsolását, letilthatja. Lásd [az e-mailes pin-kód egy alkalommal való letiltását.](#disable-email-one-time-passcode)
> - Az e-mailek egyszer megadott PIN-kód-beállításai átkerültek a Azure Portal a Külső együttműködési beállításokból **a** **Minden identitásszolgáltatóba.**

> [!NOTE]
> Az egyszer használt PIN-kód felhasználóinak olyan hivatkozással kell bejelentkezniük, amely tartalmazza a bérlői környezetet (például vagy , vagy ellenőrzött tartomány `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` esetén). Az alkalmazásokra és erőforrásokra mutató közvetlen hivatkozások is működnek, ha tartalmazzák a bérlői környezetet. A vendégfelhasználók jelenleg nem tudnak bérlői környezet nélkül bejelentkezni a végpontokkal. A használata például `https://myapps.microsoft.com` `https://portal.azure.com` hibát eredményez.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Felhasználói élmény az egyszeres PIN-kódú vendégfelhasználók számára

Ha az e-mailes pin-kód funkció engedélyezve [](#when-does-a-guest-user-get-a-one-time-passcode) van, a bizonyos feltételeknek megfelelő újonnan meghívott felhasználók egyszeres PIN-kódos hitelesítést fognak használni. Azok a vendégfelhasználók, akik az e-mailes pin-kód engedélyezése előtt beváltottak egy meghívót, továbbra is ugyanazt a hitelesítési módszert fogják használni.

Az egyszeres PIN-kódos hitelesítéssel a vendégfelhasználó beválthatja a meghívót egy közvetlen hivatkozásra kattintva vagy a meghívó e-mail használatával. Mindkét esetben a böngészőben egy üzenet jelzi, hogy a rendszer egy kódot küld a vendégfelhasználó e-mail-címére. A vendégfelhasználó a Kód **küldése lehetőséget választja:**

   ![Képernyőkép a Kód küldése gombról](media/one-time-passcode/otp-send-code.png)

A rendszer pin-kódot küld a felhasználó e-mail-címére. A felhasználó lekéri a PIN-kódot az e-mailből, és beírja a böngészőablakba:

   ![Képernyőkép az Enter code (Kód beíratja) lapról](media/one-time-passcode/otp-enter-code.png)

A vendégfelhasználó ezzel hitelesítve van, és láthatja a megosztott erőforrást, vagy folytathatja a bejelentkezést.

> [!NOTE]
> Az egyszer használható PIN-kód 30 percig érvényes. 30 perc múlva az adott egyszer használható PIN-kód már nem érvényes, és a felhasználónak új jelszót kell kérnie. A felhasználói munkamenetek 24 óra után lejárnak. Ezt követően a vendégfelhasználó új PIN-kódot kap, amikor hozzáfér az erőforráshoz. A munkamenet lejárata nagyobb biztonságot nyújt, különösen akkor, ha egy vendégfelhasználó távozik a vállalattól, vagy már nincs szüksége hozzáférésre.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Mikor kap egy vendégfelhasználó egy egyszeres PIN-kódot?

Amikor egy vendégfelhasználó bevált egy meghívót, vagy egy vele megosztott erőforrásra mutató hivatkozást használ, egyszer kap pin-kódot, ha:

- Nincs Azure AD-fiókjuk
- Nincs Microsoft-fiók
- A meghívó bérlő nem beállította a Google-összevonást a és a @gmail.com @googlemail.com felhasználók számára

A meghíváskor nincs jele annak, hogy a meghívott felhasználó egyszer fogja használni a PIN-kódos hitelesítést. Ha azonban a vendégfelhasználó bejelentkezik, az egyszeres PIN-kódos hitelesítés lesz a tartalék módszer, ha más hitelesítési módszerek nem használhatók.

Ha megtekinti a Forrás tulajdonságot a felhasználó adatai között, láthatja, hogy a vendégfelhasználók egyszer használt PIN-kódokkal hitelesítik-e magukat.  A Azure Portal nyissa meg Azure Active Directory Felhasználók lapot, majd válassza ki a felhasználót  >  a Részletek lap megnyitásához.

![Képernyőkép egy egyszeri PIN-kód-felhasználóról, egyszeri jelszavas forrásértékkel](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Ha egy felhasználó bevált egy egyszer használt JELSZÓT, majd később msa-t, Azure AD-fiókot vagy más összevont fiókot szerez be, továbbra is egy egyszer használt PIN-kód használatával hitelesíti magát. Ha frissíteni szeretné a felhasználó hitelesítési módszerét, visszaállíthatja az [érvényesítési állapotát.](reset-redemption-status.md)

### <a name="example&quot;></a>Példa

A teri@gmail.com vendégfelhasználó meg lesz hívva a Fabrikamba, amely nem rendelkezik beállított Google-összevonási beállításokkal. Teri nem rendelkezik Microsoft-fiók. Egyszer kapják meg a hitelesítő kódot.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>E-mailes egyszer megadott PIN-kód letiltása

2021 októberéig az e-mailes egyszer használható PIN-kód funkció minden meglévő bérlőn be lesz kapcsolva, és alapértelmezés szerint engedélyezve lesz az új bérlők számára. A Microsoft jelenleg nem támogatja a meghívók beváltási támogatását nem támogatott (&quot;vírusos&quot; vagy &quot;időben való") Azure AD-fiókok és -bérlők létrehozásával a B2B-együttműködési forgatókönyvekhez. Azért engedélyezjük az e-mail egyszeri PIN-kód funkcióját, mert zökkenőmentes tartalék hitelesítési módszert biztosít a vendégfelhasználók számára. Lehetősége van azonban letiltani ezt a funkciót, ha úgy dönt, hogy nem használja.

> [!NOTE]
>
> Ha az e-mailes pin-kód funkció engedélyezve van a bérlőben, és Ön kikapcsolja azt, akkor az egy alkalommal beváltott vendégfelhasználók nem fognak tudni bejelentkezni. Visszaállíthatja [az érvényesítési állapotukat,](reset-redemption-status.md) hogy újra be tudjanak jelentkezni egy másik hitelesítési módszerrel.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Az e-mailes egyszer használható PIN-kód funkció letiltása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/) Azure AD globális rendszergazdájaként.

2. A navigációs panelen válassza **a** Azure Active Directory lehetőséget.

3. Válassza **External Identities**  >  **Minden identitásszolgáltató lehetőséget.**

4. Válassza **az E-mail egyszer pin-kód küldése lehetőséget,** majd az **E-mail-cím pin-kód** letiltása a vendégek számára lehetőséget.

   > [!NOTE]
   > Az e-mailes egyszer megadott PIN-kód beállításai átkerültek a Azure Portal a Külső együttműködési beállításokból **a** **Minden identitásszolgáltatóba.**
   > Ha az e-mailes egyszeres PIN-kód helyett egy váltógombot lát, az azt jelenti, hogy korábban engedélyezte, letiltotta vagy feliratkozott a funkció előzetes kiadására. A **funkció letiltásához** válassza a Nem lehetőséget.
   >
   >![Az e-mailes pin-kód egy alkalommal váltógombja le van tiltva](media/one-time-passcode/enable-email-otp-disabled.png)

5. Kattintson a **Mentés** gombra.

## <a name="note-for-public-preview-customers"></a>Megjegyzés nyilvános előzetes verziójú ügyfelek számára

Ha korábban már feliratkozott az e-mailes pin-kód nyilvános előzetes kiadására, az automatikus funkció-engedélyezés 2021. októberi dátuma nem érvényes, így a kapcsolódó üzleti folyamatokat ez nem érinti. Emellett a Azure Portal **e-mailes pin-kód** a vendégek tulajdonságaihoz területen nem fogja látni az E-mailek egyszeres pin-kódának automatikus engedélyezése a vendégek számára **2021.** októbertől lehetőséget. Ehelyett a következő Igen  vagy **Nem** váltógombot láthatja:

![Az e-mail-cím egyszer megadott PIN-kódja feliratkozott](media/one-time-passcode/enable-email-otp-opted-in.png)

Ha azonban inkább le szeretné mondani a szolgáltatást, és 2021 októberében szeretné automatikusan engedélyezni, az Microsoft Graph API e-mail-hitelesítési módszerének erőforrástípusával visszaállíthatja az alapértelmezett [beállításokat.](/graph/api/resources/emailauthenticationmethodconfiguration) Miután visszaállítja az alapértelmezett beállításokat, a következő lehetőségek érhetők el az **E-mail-cím egy alkalommal pin-kód** a vendégek számára alatt:

![E-mail-cím engedélyezése, ha a PIN-kód egyszer le van választva](media/one-time-passcode/email-otp-options.png)

- E-mailes pin-kód automatikus engedélyezése a vendégek számára **2021. októbertől.** (Alapértelmezett) Ha az e-mailes pin-kód funkció még nincs engedélyezve a bérlő számára, 2021 októbertől automatikusan bekapcsol. Nincs szükség további műveletre, ha azt szeretné, hogy a funkció engedélyezve legyen az adott időpontban. Ha már engedélyezte vagy letiltotta a funkciót, ez a beállítás nem érhető el.

- **Engedélyezze az e-mailes pin-kódot a vendégek számára.** Bekapcsolja az e-mailes pin-kód funkcióját a bérlő számára.

- **Tiltsa le a vendégek e-mailes egyszer megadott PIN-kódját.** Kikapcsolja az e-mailes pin-kód funkcióját a bérlő számára, és megakadályozza, hogy a funkció 2021 októberében bekapcsoljon.

## <a name="note-for-azure-us-government-customers"></a>Megjegyzés az USA kormányzati Azure-ügyfelei számára

Az e-mailes egyszer használható PIN-kód szolgáltatás alapértelmezés szerint le van tiltva az Azure US Government felhőben. A partnerek csak akkor fognak tudni bejelentkezni, ha ez a funkció engedélyezve van. Az Azure nyilvános felhőjéből eltérően az Azure US Government-felhő nem támogatja a meghívók önkiszolgáló és Azure Active Directory beváltani.

 ![Az e-mailes pin-kód le van tiltva](media/one-time-passcode/enable-email-otp-disabled.png)

Az e-mailes egyszer használható PIN-kód funkció engedélyezése az Azure US Government felhőben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) Azure AD globális rendszergazdájaként.
2. A navigációs panelen válassza **a** Azure Active Directory lehetőséget.
3. Válassza **a Szervezeti kapcsolatok** Minden   >  **identitásszolgáltató lehetőséget.**

   > [!NOTE]
   > - Ha nem látja a Szervezeti **kapcsolatokat,** keressen rá a "External Identities" kifejezésre a felső keresősávban.

4. Válassza **az E-mail egyszer pin-kód lehetőséget,** majd válassza az **Igen lehetőséget.**
5. Kattintson a **Mentés** gombra.

További információ a jelenlegi korlátozásokról: [Azure US Government-felhők.](current-limitations.md#azure-us-government-clouds)
