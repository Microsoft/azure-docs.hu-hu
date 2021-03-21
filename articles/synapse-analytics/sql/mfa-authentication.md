---
title: Multi-Factor HRE-hitelesítés használata
description: A szinapszis SQL támogatja a SQL Server Management Studio (SSMS) kapcsolatait Active Directory univerzális hitelesítés használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 7b3b8aae1345339dc34137550f3fe4c5be915ae5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672727"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Multi-Factor HRE-hitelesítés használata a szinapszis SQL-sel (SSMS-támogatás az MFA-hoz)

A szinapszis SQL támogatja a SQL Server Management Studio (SSMS) kapcsolatait *Active Directory univerzális hitelesítés* használatával. 

Ez a cikk a különböző hitelesítési lehetőségek közötti különbségeket ismerteti, valamint az univerzális hitelesítéssel kapcsolatos korlátozásokat is. 

**Töltse le a legújabb SSMS** -t az ügyfélszámítógépen, töltse le a SSMS legújabb verzióját a [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true)webhelyről.

Az ebben a cikkben tárgyalt összes szolgáltatáshoz használja a 17,2-es vagy újabb 2017-es verziót.  A legutóbbi kapcsolódás párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:

![Képernyőfelvétel: Kapcsolódás a kiszolgálóhoz párbeszédpanel, ahol kiválaszthatja a kiszolgáló nevét és a hitelesítési lehetőséget.](./media/mfa-authentication/1mfa-universal-connect.png "Befejezi a Felhasználónév mezőt.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési lehetőség  

Active Directory univerzális hitelesítés támogatja a két nem interaktív hitelesítési módszert:
    - `Active Directory - Password` hitelesítés
    - `Active Directory - Integrated` hitelesítés

Két nem interaktív hitelesítési modell is létezik, amelyek számos különböző alkalmazásban (ADO.NET, JDCB, ODC stb.) is használhatók. Ez a két módszer soha nem eredményez előugró párbeszédpanelt:

- `Active Directory - Password`
- `Active Directory - Integrated`

Az interaktív módszer az, hogy az Azure AD Multi-Factor Authentication (MFA) is támogatja a következőt:

- `Active Directory - Universal with MFA`

Az Azure AD MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, PIN-kóddal ellátott intelligens kártyák vagy mobil alkalmazások értesítése), így a felhasználók kiválaszthatják a kívánt módszert. Az interaktív MFA az Azure AD-vel az ellenőrzés előugró párbeszédpanelét eredményezheti.

Multi-Factor Authentication leírását itt tekintheti meg: [multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-tartománynév vagy bérlői azonosító paraméter

A [SSMS 17-es verziójától](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true)kezdve a felhasználók, akik az aktuális Active Directoryba importálnak a vendég felhasználóként más Azure Active Directory-címtárból, megadhatják az Azure ad-tartománynevet vagy a bérlő azonosítóját a csatlakozáskor. 

A vendég felhasználók a más Azure-hirdetéseket, például a outlook.com, a hotmail.com, a live.com vagy más, például a gmail.com fiókokat meghívó felhasználókat is tartalmazhatnak. Ez az információ lehetővé teszi, hogy az **MFA-hitelesítéssel rendelkező Active Directory univerzálisan** azonosítsa a megfelelő hitelesítő szolgáltatót. Ez a beállítás a Microsoft-fiókok (MSA), például a outlook.com, a hotmail.com, a live.com és a nem MSA fiókok támogatásához is szükséges. 

Az univerzális hitelesítés használatával hitelesíteni kívánt felhasználóknak meg kell adniuk az Azure AD-tartománynevet vagy a bérlői azonosítót. Ez a paraméter az aktuális Azure AD-tartománynevet/bérlői azonosítót jelöli, amelyhez az Azure-kiszolgáló társítva van. 

Ha például az Azure Server társítva van az Azure AD-tartományhoz `contosotest.onmicrosoft.com` , ahol a felhasználó az `joe@contosodev.onmicrosoft.com` Azure ad-tartományból importált felhasználóként fut `contosodev.onmicrosoft.com` , a felhasználó hitelesítéséhez szükséges tartománynév `contosotest.onmicrosoft.com` . 

Ha a felhasználó az Azure AD-hez társított natív felhasználó, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői AZONOSÍTÓra. 

A (17,2-es SSMS-es verziótól kezdődő) paraméter megadásához a **Kapcsolódás az adatbázishoz** párbeszédpanelen végezze el a párbeszédpanelt, válassza a **Active Directory-Universal az MFA-** hitelesítéssel lehetőséget, válassza a **Beállítások**, majd a **Felhasználónév** mezőt, és válassza a **kapcsolat tulajdonságai** lapot. 

Ellenőrizze az **ad-tartomány nevét vagy a bérlő azonosítóját** , és adja meg a hitelesítő hatóságot, például a tartománynevet (**contosotest.onmicrosoft.com**) vagy a bérlő azonosítójának GUID azonosítóját.  

   ![Képernyőfelvétel: Kapcsolódás a kiszolgálóhoz a kapcsolat tulajdonságai lapon megadott értékekkel.](./media/mfa-authentication/mfa-tenant-ssms.png)

Ha a SSMS 18. x vagy újabb verzióját futtatja, akkor az AD-tartománynév vagy a bérlői azonosító már nem szükséges a vendég felhasználói számára, mert 18. x vagy újabb automatikusan felismeri.

   ![MFA-bérlő – SSMS](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD Business – üzleti támogatás   
Az Azure ad B2B-forgatókönyvek vendég felhasználói számára támogatott Azure AD-felhasználók (lásd: [Mi az az Azure B2B-együttműködés](../../active-directory/external-identities/what-is-b2b.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) a szinapszis SQL-hez való kapcsolódáshoz csak az aktuális Azure ad-ben létrehozott csoport tagjainak részeként, és az adott adatbázisban lévő Transact-SQL-utasítás használatával van leképezve manuálisan `CREATE USER` . 

Ha például `steve@gmail.com` meghívja az Azure ad-t `contosotest` (az Azure ad-tartománnyal `contosotest.onmicrosoft.com` ), egy Azure ad-csoportot, például a `usergroup` tagot tartalmazó Azure ad-ben kell létrehoznia `steve@gmail.com` . Ezután ezt a csoportot egy Transact-SQL-utasítás végrehajtásával létre kell hozni egy adott adatbázishoz (azaz MyDatabase) az Azure AD SQL-rendszergazda vagy az Azure AD DBO `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Az adatbázis-felhasználó létrehozása után a felhasználó `steve@gmail.com` bejelentkezhet a `MyDatabase` SSMS-hitelesítési lehetőség használatára `Active Directory – Universal with MFA support` . 

Alapértelmezés szerint a csoporthoz csak a kapcsolódási engedély és minden további adathozzáférés szükséges, amelyet a szokásos módon kell megadni. 

Vendég felhasználóként `steve@gmail.com` jelölje be a jelölőnégyzetet, és adja hozzá az ad-tartománynevet a `contosotest.onmicrosoft.com` SSMS- **kapcsolatok tulajdonságai** párbeszédpanelen. Az **Active Directory Domain Name vagy a bérlői azonosító** lehetőség csak az univerzális és MFA-kapcsolatok esetén támogatott, ellenkező esetben szürkén jelenik meg.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Általános hitelesítési korlátozások a szinapszis SQL-hez

- Az SSMS és a SqlPackage.exe az egyetlen jelenleg engedélyezett eszköz az MFA-hoz Active Directory univerzális hitelesítéssel.
- A SSMS 17,2-es verziója támogatja a többfelhasználós hozzáférést az MFA-val való univerzális hitelesítéssel. A 17,0-es és a 17,1-es verzióban a SSMS egy példányára való bejelentkezés korlátozott, egyetlen Azure Active Directory fiókra. Ha másik Azure AD-fiókkal szeretne bejelentkezni, a SSMS egy másik példányát kell használnia. (Ez a korlátozás Active Directory univerzális hitelesítésre korlátozódik, Active Directory jelszó-hitelesítéssel, Active Directory integrált hitelesítéssel vagy SQL Server hitelesítéssel is bejelentkezhet a különböző kiszolgálókra.
- A SSMS támogatja a Object Explorer, a lekérdezés-szerkesztő és a lekérdezés-áruház vizualizációjának Active Directory univerzális hitelesítését.
- A SSMS 17,2-es verziója a DacFx varázsló támogatását biztosítja az adatok exportálásához/kinyeréséhez/üzembe helyezéséhez. Ha egy adott felhasználó hitelesítése az univerzális hitelesítés használatával történik a kezdeti hitelesítési párbeszédpanelen, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer.
- A SSMS Táblatervező nem támogatja az univerzális hitelesítést.
- Az univerzális hitelesítéshez nincs szükség további szoftverre Active Directory, kivéve, ha a SSMS támogatott verzióját kell használnia.  
- Az univerzális hitelesítés Active Directory-hitelesítési tár (ADAL) verziója frissítve lett a legújabb ADAL.dll 3.13.9 elérhető verzióra. Lásd: [Active Directory-hitelesítési tár 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Következő lépések
További információ: [Kapcsolódás a SZINAPSZIS sqlhoz SQL Server Management Studio](get-started-ssms.md) cikkel.