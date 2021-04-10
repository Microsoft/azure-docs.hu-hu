---
title: Többtényezős hitelesítés konfigurálása
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Ismerje meg, hogyan használhatja a többtényezős hitelesítést a SSMS a Azure SQL Database, az Azure SQL felügyelt példány és az Azure szinapszis Analytics használatával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454389"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Többtényezős hitelesítés konfigurálása SQL Server Management Studio és az Azure AD-hez
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk bemutatja, hogyan használható a Azure Active Directory (Azure AD) multi-Factor Authentication (MFA) a SQL Server Management Studio (SSMS) szolgáltatással. Az Azure AD MFA a SSMS vagy a SqlPackage.exe [Azure SQL Database](sql-database-paas-overview.md), az [Azure SQL felügyelt példány](../managed-instance/sql-managed-instance-paas-overview.md) és az [Azure szinapszis Analytics szolgáltatáshoz](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)való csatlakoztatásakor használható. A többtényezős hitelesítés áttekintését lásd: [univerzális hitelesítés SQL Database, SQL felügyelt példánnyal és Azure szinapszis (SSMS-támogatás az MFA-hoz)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> A Azure SQL Databaseban, az Azure SQL felügyelt példányában és az Azure Szinapszisban található adatbázisokat a cikk további részében adatbázisként hivatkoznak, és a kiszolgáló arra a [kiszolgálóra](logical-servers.md) hivatkozik, amely a Azure SQL Database és az Azure szinapszis adatbázisait tárolja.

## <a name="configuration-steps"></a>Konfigurációs lépések

1. **Azure Active Directory konfigurálása** – további információ: [Az Azure ad-címtár felügyelete](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [a helyszíni identitások integrálása a Azure Active Directoryekkel](../../active-directory/hybrid/whatis-hybrid-identity.md), [saját tartománynév hozzáadása az Azure ad](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)-hez, [Microsoft Azure mostantól támogatja a Windows Server Active Directory összevonását](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), és a [Windows PowerShell használatával felügyelheti az Azure ad](/previous-versions/azure/jj151815(v=azure.100))-t.
2. Az **MFA konfigurálása** – részletes útmutatásért lásd: [Mi az az Azure ad multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md), [a feltételes hozzáférés (MFA) a Azure SQL Database és az adattárházban](conditional-access-configure.md). (A teljes feltételes hozzáféréshez prémium Azure Active Directory szükséges. A korlátozott MFA standard szintű Azure AD-vel érhető el.)
3. Az **Azure ad-hitelesítés konfigurálása** – lépésenkénti útmutatásért lásd: [Csatlakozás SQL Databasehoz, SQL felügyelt példányhoz vagy Azure szinapszis Azure Active Directory hitelesítés használatával](authentication-aad-overview.md).
4. **Töltse le a SSMS** -t az ügyfélszámítógépen, töltse le a legújabb SSMS a [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)webhelyről.

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Csatlakozás a SSMS-sel való univerzális hitelesítéssel

A következő lépések bemutatják, hogyan csatlakozhat a legújabb SSMS használatával.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Az univerzális hitelesítéssel való kapcsolódáshoz a SQL Server Management Studio (SSMS) **Kapcsolódás a kiszolgálóhoz** párbeszédpanelén válassza a **Active Directory-Universal lehetőséget az MFA támogatásával**. (Ha **Active Directory univerzális hitelesítést** látja, akkor nem a SSMS legújabb verziójával rendelkezik.)

   ![Képernyőkép a Kapcsolódás a kiszolgálóhoz párbeszédpanel kapcsolat tulajdonságai lapjáról, ahol a "MyDatabase" elem ki van választva a Kapcsolódás az adatbázishoz legördülő menüben.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Töltse ki a **Felhasználónév** mezőt a Azure Active Directory hitelesítő adataival a (z `user_name@domain.com` ) formátumban.

   ![Képernyőkép a Kapcsolódás a kiszolgálóhoz párbeszédpanelen a kiszolgáló típusa, a kiszolgáló neve, a hitelesítés és a Felhasználónév beállításnál.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Ha vendég felhasználóként csatlakozik, már nem kell végrehajtania az AD-tartománynév vagy a bérlői azonosító mezőt a vendég felhasználók számára, mert a SSMS 18. x vagy újabb automatikusan felismeri. További információ: [univerzális hitelesítés SQL Database, SQL felügyelt példánnyal és Azure szinapszis (SSMS-támogatás az MFA-hoz)](../database/authentication-mfa-ssms-overview.md).

   ![Képernyőkép a Kapcsolódás a kiszolgálóhoz párbeszédpanel kapcsolat tulajdonságai lapjáról, ahol a "MyDatabase" elem ki van választva a Kapcsolódás az adatbázishoz legördülő menüben.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Ha azonban vendég felhasználóként csatlakozik a 17. x vagy régebbi SSMS használatával, kattintson a **Beállítások** elemre, majd a **kapcsolat tulajdonságai** párbeszédpanelen adja meg az **ad-tartománynév vagy a bérlői azonosító mező nevét** .

   ![Képernyőfelvétel a kapcsolat tulajdonságai lapról a Kapcsolódás a kiszolgálóhoz párbeszédpanelen az s S M s-ben. az AD-tartománynév vagy a bérlői azonosító tulajdonság kitöltése megtörténik.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Válassza a **Beállítások** lehetőséget, majd adja meg az adatbázist a **Beállítások** párbeszédpanelen. (Ha a csatlakoztatott felhasználó egy vendég felhasználó (azaz joe@outlook.com ), jelölje be a jelölőnégyzetet, és adja hozzá az aktuális ad-tartománynevet vagy a bérlő azonosítóját a beállítások részeként. Lásd: [univerzális hitelesítés a SQL Database és az Azure szinapszis Analytics használatával (SSMS-támogatás az MFA-hoz)](../database/authentication-mfa-ssms-overview.md). Ezután kattintson a **Csatlakozás** gombra.  
5. Amikor megjelenik a **Bejelentkezés a fiókba** párbeszédpanel, adja meg a Azure Active Directory identitásának fiókját és jelszavát. Nincs szükség jelszóra, ha a felhasználó az Azure AD-vel összevont tartomány tagja.

   ![Képernyőfelvétel a Azure SQL Database és az adattárházhoz tartozó fiók párbeszédpanelre való bejelentkezésről. A fiók és a jelszó ki van töltve.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Ha az univerzális hitelesítéshez olyan fiókra van szükség, amely nem igényel MFA-t, akkor ezen a ponton kapcsolódhat. Az MFA-t igénylő felhasználók esetében folytassa a következő lépésekkel:
   >  

6. Előfordulhat, hogy két MFA beállítási párbeszédpanel jelenik meg. Ez az egyszeri művelet az MFA rendszergazdai beállításától függ, ezért opcionális lehet. Az MFA-kompatibilis tartományokban ez a lépés néha előre definiálva van (például a tartomány megköveteli, hogy a felhasználók intelligens kártyát és PIN-kódot használjanak).

   ![Képernyőkép a bejelentkezés a fiókba párbeszédpanelről Azure SQL Database és az adatraktárhoz, és a rendszer felszólítja a további biztonsági ellenőrzés beállítására.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. A második lehetőség egy alkalommal párbeszédpanel segítségével kiválaszthatja a hitelesítési módszer részleteit. A rendszergazda konfigurálja a lehetséges beállításokat.

   ![Képernyőkép a további biztonsági ellenőrzési párbeszédpanelről a hitelesítési módszer kiválasztására és konfigurálására szolgáló beállításokkal.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. A Azure Active Directory elküldi a megerősítési adatokat. Amikor megkapja az ellenőrző kódot, adja meg az **ENTER ellenőrző kód megadása** mezőbe, és kattintson a **Bejelentkezés** elemre.

   ![Képernyőkép a bejelentkezés a fiókba párbeszédpanelről Azure SQL Database és az adatraktárhoz egy ellenőrző kód megadására vonatkozó kéréssel.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Az ellenőrzés befejezésekor a SSMS általában feltételezi az érvényes hitelesítő adatokat és a tűzfal elérését.

## <a name="next-steps"></a>Következő lépések

- A többtényezős hitelesítés áttekintését lásd: [univerzális hitelesítés SQL Database, SQL felügyelt példánnyal és Azure szinapszis (SSMS-támogatás az MFA-hoz)](../database/authentication-mfa-ssms-overview.md).  
- Mások hozzáférésének biztosítása az adatbázishoz: [SQL Database hitelesítés és engedélyezés: hozzáférés biztosítása](logins-create-manage.md)  
- Győződjön meg arról, hogy mások kapcsolódhatnak a tűzfalon keresztül: [a kiszolgálói szintű tűzfalszabály konfigurálása a Azure Portal használatával](./firewall-configure.md)  
- A **Active Directory-Universal és az MFA-** hitelesítés használatakor a ADAL nyomkövetés a [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms)-es verziójától kezdődően érhető el. Alapértelmezés szerint kikapcsolhatja a ADAL nyomkövetését az **eszközök**, beállítások menü, az **Azure-szolgáltatások**, az **Azure-felhő**, a **ADAL kimeneti ablak nyomkövetési szintje** **lehetőséggel** , majd a **kimenet** lehetőséget a **nézet** menüben engedélyezheti. A Nyomkövetések a kimeneti ablakban érhetők el **Azure Active Directory lehetőség** kiválasztásakor.