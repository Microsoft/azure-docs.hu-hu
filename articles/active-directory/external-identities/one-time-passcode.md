---
title: Egyszeri jelszavas hitelesítés a VÁLLALATKÖZI vendég felhasználói számára – Azure AD
description: E-mail egyszeri jelszó használata a B2B vendég-felhasználók hitelesítéséhez Microsoft-fiók nélkül.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9bc7d94cff46b54b81615cf064ed63927b3ec60
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929293"
---
# <a name="email-one-time-passcode-authentication-preview"></a>E-mail egyszeri jelszó-hitelesítés (előzetes verzió)

Ez a cikk azt ismerteti, hogyan engedélyezhető a levelezés egyszeri jelszavas hitelesítése a B2B vendég felhasználói számára. Az egyszeri e-mail-jelszó funkció hitelesíti a B2B vendég felhasználóit, ha nem hitelesíthetők más módon, például az Azure AD-vel, a Microsoft-fiók (MSA) vagy a Google Federation szolgáltatással. Egyszeri jelszavas hitelesítés esetén nincs szükség Microsoft-fiók létrehozására. Ha a vendég felhasználó bevált egy meghívót, vagy egy megosztott erőforráshoz fér hozzá, ideiglenes kódot kérhet, amelyet a rendszer elküld az e-mail-címére. Ezután a kód beírásával folytathatja a bejelentkezést.

![E-mail egyszeri jelszó áttekintő diagramja](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> **Március 2021**-én az e-mailes egyszeri jelszó funkció bekapcsolva lesz az összes meglévő bérlőhöz, és alapértelmezés szerint engedélyezve van az új bérlők számára. Ha nem szeretné engedélyezni a funkció automatikus bekapcsolását, letilthatja. Lásd az [e-mailek egyszeri jelszó letiltását](#disable-email-one-time-passcode) ismertető szakaszt.

> [!NOTE]
> Egyszeri jelszóval rendelkező felhasználóknak be kell jelentkezniük egy olyan hivatkozás használatával, amely tartalmazza a bérlői környezetet (például `https://myapps.microsoft.com/?tenantid=<tenant id>` vagy `https://portal.azure.com/<tenant id>` egy ellenőrzött tartomány esetén `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Az alkalmazásokra és az erőforrásokra mutató közvetlen hivatkozásokat is használhatja, amennyiben azok tartalmazzák a bérlői környezetet. A vendég felhasználók jelenleg nem tudnak bejelentkezni a bérlői kontextus nélküli végpontok használatával. A használatával például `https://myapps.microsoft.com` `https://portal.azure.com` hibaüzenetet fog eredményezni.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Felhasználói élmény egyszeri jelszó vendég felhasználói számára

Ha az e-mailes egyszeri jelszó funkció engedélyezve van, az újonnan meghívott felhasználók, [akik megfelelnek bizonyos feltételeknek](#when-does-a-guest-user-get-a-one-time-passcode) , egyszeri jelszavas hitelesítést fognak használni. Azok a vendég felhasználók, akik beváltottak egy meghívót az e-mail egyszeri jelszava engedélyezése előtt, továbbra is ugyanazt a hitelesítési módszert fogják használni.

Egyszeri PIN-kód-hitelesítéssel a vendég felhasználó a közvetlen hivatkozásra kattintva vagy a meghívó e-mailben is beválthatja a meghívót. Mindkét esetben egy üzenet jelenik meg a böngészőben, amely azt jelzi, hogy a rendszer elküld egy kódot a vendég felhasználói e-mail-címére. A vendég felhasználó kiválasztja a **küldési kódot**:

   ![A kód küldése gombra mutató képernyőkép](media/one-time-passcode/otp-send-code.png)

A rendszer elküld egy jelszót a felhasználó e-mail-címére. A felhasználó lekéri a PIN-kódot az e-mailben, és beírja a böngészőablakba:

   ![A kód megadása lapot ábrázoló képernyőfelvétel](media/one-time-passcode/otp-enter-code.png)

A vendég felhasználó hitelesítése megtörtént, és láthatja a megosztott erőforrást, vagy folytathatja a bejelentkezést.

> [!NOTE]
> Az egyszeri PIN-kódok 30 percig érvényesek. 30 perc elteltével az adott egyszeri PIN-kód már nem érvényes, és a felhasználónak újat kell igényelnie. A felhasználói munkamenetek 24 óra elteltével lejárnak. Ezt követően a vendég felhasználó új jelszót kap az erőforráshoz való hozzáféréskor. A munkamenet lejárata nagyobb biztonságot nyújt, különösen akkor, ha a vendég felhasználó elhagyja a vállalatot, vagy már nincs hozzáférése.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Mikor kap egyszeri jelszót a vendég felhasználó?

Ha egy vendég felhasználó bevált egy meghívót, vagy egy olyan erőforrásra mutató hivatkozást használ, amelyet megosztottak velük, akkor egyszeri jelszót kapnak, ha:

- Nem rendelkezik Azure AD-fiókkal
- Nincs Microsoft-fiók
- A meghívó bérlő nem állított be Google-összevonást a @gmail.com és a @googlemail.com felhasználók számára

A meghívás időpontjában nincs jelezve, hogy a meghívott felhasználó egyszer használatos hitelesítő adatokat fog használni. Ha azonban a vendég felhasználó bejelentkezik, egyszeri jelszó-hitelesítés lesz a tartalék módszer, ha más hitelesítési módszer nem használható.

Megtekintheti, hogy a vendég felhasználó egyszer használatos jelszót használ-e, ha megtekinti a **forrás** tulajdonságot a felhasználó adatai között. A Azure Portal lépjen a **Azure Active Directory**  >  **felhasználók** elemre, majd válassza ki a felhasználót a Részletek lap megnyitásához.

![Képernyőfelvétel: egyszeri jelszóval rendelkező felhasználó, amely az OTP forrás értékét tartalmazza](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Amikor egy felhasználó egyszeri jelszót vált ki, és később beolvas egy MSA, egy Azure AD-fiókot vagy más összevont fiókot, a rendszer továbbra is egyszer használatba veszi a hitelesítő kódot. Ha frissíteni szeretné a hitelesítési módszert, törölheti a vendég felhasználói fiókját, és újból meghívhatja őket.

### <a name="example"></a>Példa

A vendég felhasználó teri@gmail.com meghívja a fabrikam-t, amely nem rendelkezik a Google Federation beállítással. Teri nem rendelkezik Microsoft-fiók. Egy egyszeri jelszót kapnak a hitelesítéshez.

## <a name="disable-email-one-time-passcode"></a>E-mail egyszeri jelszó letiltása

Március 2021-én az e-mailes egyszeri jelszó funkció bekapcsolva lesz az összes meglévő bérlőhöz, és alapértelmezés szerint engedélyezve van az új bérlők számára. Ekkor a Microsoft többé nem támogatja a meghívások beváltását úgy, hogy nem felügyelt ("vírus" vagy "igény szerinti") Azure AD-fiókokat és bérlőket hoz létre VÁLLALATKÖZI együttműködési forgatókönyvek létrehozásához. Engedélyezjük az e-mailek egyszeri jelszavas szolgáltatását, mivel zökkenőmentes tartalék hitelesítési módszert biztosít a vendég felhasználói számára. Azonban lehetősége van letiltani ezt a funkciót, ha úgy dönt, hogy nem használja.

> [!NOTE]
>
> Ha az egyszeri e-mail-jelszó funkció engedélyezve van a bérlőben, és ki van kapcsolva, az egyszeri jelszót beváltó vendég-felhasználók nem fognak tudni bejelentkezni. Törölheti a vendég felhasználót, és újból meghívhatja őket, hogy újra bejelentkezzenek egy másik hitelesítési módszer használatával.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Az e-mail egyszeri jelszó funkciójának letiltása

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad globális rendszergazdaként.

2. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.

3. Külső **identitások**  >  **külső együttműködési beállítások** kiválasztása.

4. Az **e-mailek egyszeri jelszava a vendégek** számára területen jelölje be **az E-mail egyszeri jelszó letiltása a vendégek** számára jelölőnégyzetet.

    ![Egyszeri jelszóra vonatkozó e-mail-beállítások](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Ha a fenti beállítások helyett a következő váltógomb jelenik meg, ez azt jelenti, hogy korábban engedélyezte, letiltotta vagy választotta a funkció előnézetét. Válassza a **nem** lehetőséget a funkció letiltásához.
   >
   >![E-mailek egyszeri jelszavas beállításának engedélyezése](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Kattintson a **Mentés** gombra.

## <a name="note-for-public-preview-customers"></a>Megjegyzés a nyilvános előzetes verzió ügyfelei számára

Ha korábban már engedélyezte az egyszer használatos e-mail-jelszó nyilvános előzetesét, az automatikus szolgáltatás engedélyezésének március 2021-os dátuma nem vonatkozik Önre, így a kapcsolódó üzleti folyamatokat nem érinti a rendszer. Továbbá a Azure Portal az **e-mail egyszeri jelszava a vendégek** számára tulajdonság esetében nem fogja látni az **e-mailek egyszeri PIN**-kódját a 2021. márciusi vendégek számára. Ehelyett a következő **Igen** vagy **nincs** váltógomb jelenik meg:

![E-mailek egyszeri jelszavas beállításának engedélyezése](media/delegate-invitations/enable-email-otp-opted-in.png)

Ha azonban szeretné letiltani a szolgáltatást, és az automatikusan engedélyezve lesz a 2021 márciusában, a Microsoft Graph API [e-mail hitelesítési módszer konfigurációs erőforrástípus](https://aka.ms/exid-graphemailauth)használatával visszaállíthatja az alapértelmezett beállításokat. Miután visszaállította az alapértelmezett beállításokat, a következő beállítások lesznek elérhetők a **levelezés egyszeri jelszava a vendégek** számára:

- **E-mail egyszeri PIN-kód automatikus engedélyezése a vendégek számára a 2021 márciusában**. Alapértelmezett Ha az egyszer használatos e-mail-jelszó funkció még nincs engedélyezve a bérlőnél, a rendszer automatikusan bekapcsolja az 2021-es időpontban. Ha azt szeretné, hogy a szolgáltatás egyszerre legyen engedélyezve, nincs szükség további műveletekre. Ha már engedélyezte vagy letiltotta a szolgáltatást, ez a beállítás nem lesz elérhető.

- **E-mailes egyszeri jelszó engedélyezése a vendégek számára most**. Bekapcsolja a bérlői e-mailek egyszeri jelszavas szolgáltatását.

- **Tiltsa le az e-mailek egyszeri PIN-kódját a vendégek** számára. Kikapcsolja az e-mailek egyszeri jelszavas funkcióját a bérlő számára, és megakadályozza, hogy a funkció bekapcsoljon a 2021. márciusi időszakra.
