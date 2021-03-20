---
title: Az Azure AD UserPrincipalName feltöltése
description: A következő dokumentum azt ismerteti, hogyan történik a UserPrincipalName attribútum feltöltése.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317591"
---
# <a name="azure-ad-userprincipalname-population"></a>Az Azure AD UserPrincipalName feltöltése

Ez a cikk azt ismerteti, hogyan tölthetők fel a UserPrincipalName attribútum a Azure Active Directory (Azure AD) szolgáltatásban.
A UserPrincipalName attribútum értéke a felhasználói fiókok Azure AD-felhasználóneve.

## <a name="upn-terminology"></a>UPN-terminológia
Ebben a cikkben a következő terminológiát használjuk:

|Időszak|Leírás|
|-----|-----|
|Kezdeti tartomány|Az alapértelmezett tartomány (onmicrosoft.com) az Azure AD-Bérlőben. Például: contoso.onmicrosoft.com.|
|Microsoft Online E-mail útválasztási cím (MOERA)|Az Azure AD kiszámítja a MOERA az Azure AD MailNickName attribútumból és az Azure AD kezdeti tartományból &lt; MailNickName &gt;&#64;&lt; kezdeti tartományként &gt; .|
|Helyszíni mailNickName attribútum|Active Directory egyik attribútuma, amelynek értéke egy Exchange-szervezet felhasználójának aliasát jelöli.|
|Helyszíni levelezési attribútum|A Active Directory egyik attribútuma, amelynek értéke a felhasználó e-mail-címének felel meg|
|Elsődleges SMTP-cím|Egy Exchange-címzett objektum elsődleges e-mail-címe. Például SMTP: user \@ contoso.com.|
|Másodlagos bejelentkezési azonosító|A UserPrincipalName eltérő helyszíni attribútum, például a mail attribútum, amely a bejelentkezéshez használatos.|

## <a name="what-is-userprincipalname"></a>Mi az a UserPrincipalName?
A UserPrincipalName egy olyan attribútum, amely a felhasználó internetes stílusú bejelentkezési neve az Internet standard [RFC 822-es](https://www.ietf.org/rfc/rfc0822.txt)szabvány alapján. 

### <a name="upn-format"></a>UPN formátuma
Az egyszerű felhasználónév egy UPN-előtagot (a felhasználói fiók nevét) és egy UPN-utótagot (DNS-tartománynevet) tartalmaz. Az előtag a "" szimbólum használatával csatlakozik az utótaghoz \@ . Például: "valaki \@ example.com". Az egyszerű felhasználónévnek egyedinek kell lennie az összes rendszerbiztonsági tag objektum között egy címtár-erdőben. 

## <a name="upn-in-azure-ad"></a>UPN az Azure AD-ben 
Az Azure AD egyszerű felhasználónevet használ a felhasználók bejelentkezésének engedélyezéséhez.  A felhasználó által használható egyszerű felhasználónév attól függ, hogy a tartomány ellenőrzése megtörtént-e.  Ha a tartomány ellenőrzése megtörtént, akkor az adott utótaggal rendelkező felhasználó bejelentkezhet az Azure AD-be.  

A Azure AD Connect szinkronizálja az attribútumot.  A telepítés során megtekintheti azokat a tartományokat, amelyek ellenőrzése megtörtént, valamint a nem.

   ![Nem ellenőrzött tartományok](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Másodlagos bejelentkezési azonosító
Bizonyos környezetekben előfordulhat, hogy a végfelhasználók csak az e-mail-címüket ismerik, és nem az egyszerű felhasználónevet.  Az e-mail-cím használata a vállalati házirend vagy a helyszíni üzletági alkalmazások függősége miatt lehet.

Az alternatív bejelentkezési azonosító lehetővé teszi a bejelentkezési élmény konfigurálását, amelyben a felhasználók az egyszerű felhasználónévtől (például a levelezéstől) eltérő attribútummal jelentkezhetnek be.

Ha engedélyezni szeretné az alternatív bejelentkezési azonosítót az Azure AD-ben, akkor a Azure AD Connect használatakor nincs szükség további konfigurációs lépésekre. A másodlagos azonosító közvetlenül a varázslóból is konfigurálható. Tekintse meg a felhasználók Azure AD bejelentkezési konfigurációját a szinkronizálás szakasza alatt. Az **egyszerű felhasználónév** legördülő menüben válassza ki az alternatív bejelentkezési azonosító attribútumát.

![Képernyőkép, amely kiemeli az egyszerű felhasználónevek listáját, ahol kiválaszthatja a másodlagos bejelentkezési azonosító attribútumot.](./media/plan-connect-userprincipalname/altloginid.png)  

További információ: [alternatív bejelentkezési azonosító konfigurálása](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) és [Azure ad bejelentkezési konfiguráció](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Nem ellenőrzött UPN-utótag
Ha a helyszíni UserPrincipalName attribútum vagy az alternatív bejelentkezési azonosító utótagja nem lett ellenőrizve az Azure AD-Bérlővel, akkor az Azure AD-UserPrincipalName attribútum értéke a MOERA értékre van állítva. Az Azure AD kiszámítja a MOERA az Azure AD MailNickName attribútumból és az Azure AD kezdeti tartományból &lt; MailNickName &gt;&#64;&lt; kezdeti tartományként &gt; .

## <a name="verified-upn-suffix"></a>Ellenőrzött UPN-utótag
Ha a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító utótagja az Azure AD-Bérlővel van ellenőrizve, akkor az Azure AD UserPrincipalName attribútumának értéke azonos lesz a helyszíni UserPrincipalName attribútummal/a másodlagos bejelentkezési azonosító értékével.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD-MailNickName-attribútum értékének kiszámítása
Mivel az Azure AD UserPrincipalName attribútum értéke MOERA lehet, fontos tisztában lennie azzal, hogy az Azure AD MailNickName attribútumának értéke, amely a MOERA-előtag, ki van számítva.

Amikor első alkalommal szinkronizál egy felhasználói objektumot egy Azure AD-Bérlővel, az Azure AD a megadott sorrendben ellenőrzi a következő elemeket, és beállítja a MailNickName attribútum értékét az első meglévőre:

- Helyszíni mailNickName attribútum
- Elsődleges SMTP-címnek előtagja
- Helyszíni levelezési attribútum előtagja
- A helyszíni userPrincipalName attribútum és az alternatív bejelentkezési azonosító előtagja
- Másodlagos SMTP-címek előtagja

Amikor egy felhasználói objektum frissítéseit szinkronizálja az Azure AD-Bérlővel, az Azure AD csak abban az esetben frissíti a MailNickName attribútum értékét, ha a helyszíni mailNickName attribútum értéke frissítve van.

>[!IMPORTANT]
>Az Azure AD csak abban az esetben számítja ki a UserPrincipalName attribútum értékét, ha a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító értékének frissítése szinkronizálva van az Azure AD-Bérlővel. 
>
>Ha az Azure AD újraszámítja a UserPrincipalName attribútumot, akkor az újraszámítja a MOERA is. 

## <a name="upn-scenarios"></a>UPN-forgatókönyvek
Az alábbi példák azt szemléltetik, hogyan számítja ki az UPN-t az adott forgatókönyv alapján.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>1. forgatókönyv: nem ellenőrzött UPN-utótag – kezdeti szinkronizálás

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Helyszíni felhasználói objektum:
- mailNickName: &lt; nincs beállítva&gt;
- proxyAddresses: { SMTP:us1@contoso.com }
- levelezési us2@contoso.com
- userPrincipalName us3@contoso.com

Az első alkalommal szinkronizálta a felhasználói objektumot az Azure AD-Bérlővel
- Állítsa be az Azure AD MailNickName attribútumot az elsődleges SMTP-címek előtagjaként.
- Állítsa be a MOERA  &lt; MailNickName &gt;&#64;&lt; kezdeti tartományba &gt; .
- Állítsa be az Azure AD UserPrincipalName attribútumát MOERA értékre.

Azure AD-bérlő felhasználói objektum:
- MailNickName: US1           
- UserPrincipalName us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>2. forgatókönyv: nem ellenőrzött UPN-utótag – helyszíni mailNickName attribútum beállítása

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: { SMTP:us1@contoso.com }
- levelezési us2@contoso.com
- userPrincipalName us3@contoso.com

A frissítés szinkronizálása a helyszíni mailNickName attribútumon az Azure AD-bérlőn
- Frissítse az Azure AD MailNickName attribútumát a helyszíni mailNickName attribútummal.
- Mivel a helyszíni userPrincipalName attribútum nem frissült, az Azure AD UserPrincipalName attribútuma nem változik.

Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- UserPrincipalName us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>3. forgatókönyv: nem ellenőrzött UPN-utótag – a helyszíni userPrincipalName attribútum frissítése

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: { SMTP:us1@contoso.com }
- levelezési us2@contoso.com
- userPrincipalName us5@contoso.com

A frissítés szinkronizálása a helyszíni userPrincipalName attribútumon az Azure AD-bérlőn
- A helyszíni userPrincipalName attribútum frissítése elindítja a MOERA és az Azure AD UserPrincipalName attribútumának újraszámítását.
- Állítsa be a MOERA &lt; MailNickName &gt;&#64;&lt; kezdeti tartományba &gt; .
- Állítsa be az Azure AD UserPrincipalName attribútumát MOERA értékre.

Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>4. forgatókönyv: nem ellenőrzött UPN-utótag – az elsődleges SMTP-cím és a helyszíni levelezési attribútum frissítése

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: { SMTP:us6@contoso.com }
- levelezési us7@contoso.com
- userPrincipalName us5@contoso.com

A frissítés szinkronizálása a helyszíni levelezési attribútumon és az elsődleges SMTP-címen az Azure AD-bérlőn
- A felhasználói objektum kezdeti szinkronizálását követően a helyszíni levelezési attribútum és az elsődleges SMTP-cím frissítései nem érintik az Azure AD-MailNickName vagy a UserPrincipalName attribútumot.

Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>5. forgatókönyv: ellenőrzött UPN-utótag – a helyszíni userPrincipalName-attribútum utótagjának frissítése

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: { SMTP:us6@contoso.com }
- levelezési us7@contoso.com
- userPrincipalName us5@verified.contoso.com

A frissítés szinkronizálása a helyszíni userPrincipalName attribútummal az Azure AD-bérlőn
- A helyszíni userPrincipalName attribútum frissítése elindítja az Azure AD UserPrincipalName attribútumának újraszámítását.
- Állítsa be az Azure AD UserPrincipalName attribútumot a helyszíni userPrincipalName attribútumra, mert az UPN-utótag ellenőrzése az Azure AD-Bérlővel történik.

Azure AD-bérlő felhasználói objektum:
- MailNickName: us4     
- UserPrincipalName us5@verified.contoso.com

## <a name="next-steps"></a>Következő lépések
- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)