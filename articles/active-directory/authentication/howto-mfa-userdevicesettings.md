---
title: Az Azure AD Multi-Factor Authentication-Azure Active Directory hitelesítési módszereinek kezelése
description: Ismerje meg, hogyan konfigurálhatja Azure Active Directory Azure AD-beli felhasználói beállításait Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0be7e50c5cce511fafd7d8b407626bd57659bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183132"
---
# <a name="manage-user-authentication-methods-for-azure-ad-multi-factor-authentication"></a>Az Azure AD-Multi-Factor Authentication felhasználói hitelesítési módszereinek kezelése

Az Azure AD-felhasználók a kapcsolattartási adatok két különböző készletével rendelkeznek:  

- Nyilvános profil elérhetőségi adatai, amelyek a felhasználói profilban kezelhetők, és a szervezet tagjai számára láthatók. A helyszíni Active Directoryról szinkronizált felhasználók esetében ezeket az információkat a helyszíni Windows Server Active Directory Domain Services kezeli.
- A hitelesítési módszereket, amelyek mindig magánjellegűek, és csak hitelesítésre használják, beleértve a többtényezős hitelesítést (MFA). A rendszergazdák kezelhetik ezeket a metódusokat a felhasználó hitelesítési módszer paneljén, és a felhasználók a MyAccount biztonsági adatok lapján kezelhetik a módszereiket.

Az Azure AD-Multi-Factor Authentication módszereinek a felhasználókhoz való kezelésekor a hitelesítési rendszergazdák a következőket tehetik: 

1. Hitelesítési módszerek hozzáadása egy adott felhasználóhoz, beleértve az MFA-hoz használt telefonszámokat is.
1. A felhasználó jelszavának alaphelyzetbe állítása.
1. A felhasználónak újra regisztrálnia kell az MFA-t.
1. Meglévő MFA-munkamenetek visszavonása.
1. Felhasználó meglévő alkalmazási jelszavának törlése  

## <a name="add-authentication-methods-for-a-user"></a>Hitelesítési módszerek hozzáadása felhasználóhoz 

A felhasználóhoz a Azure Portal vagy Microsoft Graph használatával adhat hitelesítési módszereket.  

> [!NOTE]
> Biztonsági okokból a nyilvános felhasználói kapcsolattartási adatok mezői nem használhatók MFA végrehajtásához. Ehelyett a felhasználóknak fel kell tölteniük a hitelesítési módszerek számát az MFA használatára.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Hitelesítési módszerek hozzáadása a Azure Portal":::

Hitelesítési módszerek hozzáadása felhasználóhoz a Azure Portal használatával:  

1. Jelentkezzen be az **Azure Portalra**. 
1. Tallózással keresse meg **Azure Active Directory** a  >  **felhasználók**  >  **minden felhasználóját**. 
1. Válassza ki azt a felhasználót, akivel hozzá szeretné adni a hitelesítési módszert, és válassza a **hitelesítési módszerek** lehetőséget.  
1. Az ablak tetején válassza a **+ hitelesítési módszer hozzáadása** elemet.
   1. Válasszon egy metódust (telefonszám vagy e-mail-cím). Az e-mailek az önjelszó visszaállítására használhatók, de a hitelesítés nem lehetséges. Telefonszám hozzáadásakor válasszon egy telefonszámot, és adja meg a telefonszámot érvényes formátumban (például + 1 4255551234).
   1. Válassza a **Hozzáadás** lehetőséget.

> [!NOTE]
> Az előzetes verzió használata lehetővé teszi a rendszergazdák számára, hogy a felhasználók számára elérhető hitelesítési módszereket adjanak hozzá, míg az eredeti felület csak a telefonos és a másodlagos telefonos módszerek frissítését teszi lehetővé.

### <a name="manage-methods-using-powershell"></a>Metódusok kezelése a PowerShell használatával:  

Telepítse a Microsoft. Graph. Identity. bejelentkezések PowerShell-modult az alábbi parancsokkal. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Egy adott felhasználó telefonos alapú hitelesítési módszereinek listázása.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Hozzon létre egy mobiltelefonos hitelesítési módszert egy adott felhasználó számára.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType "mobile" -phoneNumber "+1 7748933135"
```

Adott telefonos módszer eltávolítása a felhasználó számára

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

A hitelesítési módszerek Microsoft Graph API-kkal is kezelhetők, további információt az [Azure ad hitelesítési módszerek API áttekintése](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) című dokumentumban találhat.

## <a name="manage-user-authentication-options"></a>Felhasználói hitelesítési beállítások kezelése

Ha a *hitelesítési rendszergazda* szerepkört rendelte hozzá, megkövetelheti a felhasználóktól a jelszavuk visszaállítását, az MFA regisztrálását, illetve a meglévő MFA-munkamenetek visszavonását a felhasználói objektumból. A felhasználói beállítások kezeléséhez végezze el a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon válassza az **Azure Active Directory** > **Felhasználók** > **Minden felhasználó** lehetőséget.
1. Válassza ki azt a felhasználót, akinek műveletet kíván végrehajtani, és válassza a **hitelesítési módszerek** lehetőséget. Az ablak tetején válassza a következő lehetőségek egyikét a felhasználó számára:
   - A **jelszó alaphelyzetbe állítása** visszaállítja a felhasználó jelszavát, és egy ideiglenes jelszót rendel hozzá, amelyet a következő bejelentkezéskor módosítani kell.
   - Az **MFA ismételt regisztrálásának megkövetelése** lehetővé teszi, hogy amikor a felhasználó legközelebb bejelentkezik, egy új MFA hitelesítési módszer beállítására van szükség.
   
      > [!NOTE]
      > A felhasználó jelenleg regisztrált hitelesítési módszereit nem törli a rendszer, ha egy rendszergazdának újra kell regisztrálnia az MFA-t. Miután egy felhasználó újra regisztrálja az MFA-t, javasoljuk, hogy tekintsék át a biztonsági adataikat, és töröljön minden korábban regisztrált hitelesítési módszert, amely már nem használható.
   
   - Az **MFA-munkamenetek visszavonása** törli a felhasználó által megjegyzett MFA-munkameneteket, és azt igényli, hogy az MFA-t a következő alkalommal kell végrehajtania, amikor a szabályzat az eszközön szükséges.
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Hitelesítési módszerek kezelése a Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>A felhasználók meglévő alkalmazási jelszavának törlése

Azok a felhasználók, akik meghatározott alkalmazási jelszavakkal rendelkeznek, a rendszergazdák is dönthetik el, hogy törlik-e a jelszavakat, így a régi hitelesítés meghiúsulhat az alkalmazásokban. Ezek a műveletek akkor lehetnek szükségesek, ha segítséget kell nyújtania egy felhasználónak, vagy vissza kell állítania a hitelesítési módszereiket. Az ezekhez az alkalmazásokhoz tartozó jelszavakkal társított nem böngésző alkalmazások nem fognak működni, amíg új alkalmazás jelszava nem jön létre. 

A felhasználók alkalmazási jelszavának törléséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali oldalon válassza **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza a **Multi-Factor Authentication** lehetőséget. Előfordulhat, hogy a menüpontra kell görgetni a jobb oldalon. Válassza az alábbi képernyőképet a teljes Azure Portal ablak és a menü helyének megtekintéséhez: [ ![ válassza a multi-Factor Authentication lehetőséget az Azure ad felhasználók ablakában.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Jelölje be a felügyelni kívánt felhasználó vagy felhasználók melletti jelölőnégyzetet. A jobb oldalon megjelenik a gyors lépésre vonatkozó beállítások listája.
1. Válassza a **felhasználói beállítások kezelése** lehetőséget, majd jelölje be a **kijelölt felhasználók által létrehozott összes meglévő alkalmazás jelszavának törlése** jelölőnégyzetet az alábbi példában látható módon: az ![ összes meglévő alkalmazás jelszavának törlése](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Válassza a **Mentés**, majd a **Bezárás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ez a cikk az egyéni felhasználói beállítások konfigurálását mutatja be. Az Azure AD Multi-Factor Authentication szolgáltatási beállításainak általános konfigurálását az [Azure ad multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)című témakörben tekintheti meg.

Ha a felhasználóknak segítségre van szüksége, tekintse meg az [Azure AD multi-Factor Authentication felhasználói útmutatóját](../user-help/multi-factor-authentication-end-user-first-time.md).
