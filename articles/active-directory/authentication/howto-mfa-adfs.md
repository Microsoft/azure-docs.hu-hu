---
title: Felhőalapú erőforrások biztosítása az Azure MFA és AD FS – Azure Active Directory
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát a felhőben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0abf2eca52616638f0c4dce89691c0d4f7875106
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60415343"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>A felhőerőforrások védelme Azure Multi-Factor Authentication hitelesítéssel és AD FS-sel

Ha a szervezete Azure Active Directory-összevonást használ, és az Azure AD által elért erőforrásokkal rendelkezik, az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal (AD FS) védheti meg ezeket az erőforrásokat. Az alábbi eljárásokkal védheti meg az Azure Active Directory-erőforrásokat az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Az Azure AD-erőforrások AD FS-sel való védelme

A felhőszolgáltatás biztosításához állítson be egy jogcímszabályt, hogy az Active Directory összevonási szolgáltatások a multipleauthn jogcímet adja ki, amikor egy felhasználó sikeresen végez kétlépéses ellenőrzést. Ez a jogcím átkerül az Azure AD-re. Az alábbi eljárás bemutatja ennek lépéseit:

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **Függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.

   ![AD FS-konzol – a függő entitás Megbízhatóságai](./media/howto-mfa-adfs/trustedip1.png)

4. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** elemre.

   ![Jogcímkiadás-átalakítási szabályok szerkesztése](./media/howto-mfa-adfs/trustedip2.png)

5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.

   ![Átalakítási Jogcímszabály varázslóban hozzáadása](./media/howto-mfa-adfs/trustedip3.png)

6. Adjon nevet a szabálynak. 
7. Válassza a **Hitelesítési módszerek hivatkozásai** lehetőséget a Bejövő jogcím típusaként.
8. Válassza **Az összes jogcímérték továbbítása** lehetőséget.
    ![Átalakítási jogcímszabály hozzáadása varázsló](./media/howto-mfa-adfs/configurewizard.png)
9. Kattintson a **Befejezés**gombra. Zárja be az AD FS felügyeleti konzolt.

## <a name="trusted-ips-for-federated-users"></a>Megbízható IP-címek összevont felhasználók számára

Az adminisztrátorok a megbízható IP-címek segítségével megkerülhetik a kétlépéses ellenőrzést olyan IP-címek vagy összevont felhasználók esetében, akiknek a kérései a saját intranetes hálózatukról származnak. A következő szakaszok leírják az Azure Multi-Factor Authentication megbízható IP-címei és az összevont felhasználók konfigurálását, valamint a kétlépéses ellenőrzés megkerülését, amikor egy kérés összevont felhasználó intranetes hálózatáról származik. Ehhez úgy kell konfigurálni az AD FS-t, hogy áteresztést vagy a bejövő jogcímsablonok szűrését használja a vállalati hálózaton belüli jogcímtípushoz.

Ez a példa az Office 365-öt használja a függőentitás-megbízhatóságokhoz.

### <a name="configure-the-ad-fs-claims-rules"></a>Az AD FS-jogcímszabályok konfigurálása

Az első lépés az AD FS-jogcímek konfigurálása. Két jogcímszabályt hozzon létre: egyet a vállalati hálózaton belüli jogcímtípushoz és egy másikat ahhoz, hogy a felhasználók bejelentkezve maradjanak.

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **Függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **a Microsoft Office 365 Identity Platform** válassza **Jogcímszabályok szerkesztése... ** 
    ![ADFS konzol – a Jogcímszabályok szerkesztése](./media/howto-mfa-adfs/trustedip1.png)
4. A kiadás átalakítási szabályai részben kattintson **szabály hozzáadása.** 
    ![Jogcímszabály hozzáadása](./media/howto-mfa-adfs/trustedip2.png)
5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.
   ![Átalakítási jogcímszabály hozzáadása varázsló](./media/howto-mfa-adfs/trustedip3.png)
6. A Jogcímszabály neve melletti mezőben adjon nevet a szabálynak. Példa: InsideCorpNet.
7. A Bejövő jogcím típusa melletti legördülő listából válassza a **Vállalati hálózaton belül** elemet.
   ![Jogcím a vállalati hálózaton belüli felvétele](./media/howto-mfa-adfs/trustedip4.png)
8. Kattintson a **Befejezés**gombra.
9. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** elemre.
10. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Jogcímek küldése egyéni szabállyal** elemet a legördülő menüből, és kattintson a **Tovább** gombra.
11. A Jogcímszabály neve: alatti mezőbe írja be a következőt: *Keep Users Signed In* (A felhasználók maradjanak bejelentkezve).
12. Az Egyéni szabály mezőbe írja be a következőt:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Bejelentkezett felhasználók egyéni jogcímszabályok létrehozása](./media/howto-mfa-adfs/trustedip5.png)
13. Kattintson a **Befejezés**gombra.
14. Kattintson az **Alkalmaz** gombra.
15. Kattintson az **OK** gombra.
16. Zárja be az AD FS felügyeleti konzolt.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Azure Multi-Factor Authentication megbízható IP-címeinek konfigurálása összevont felhasználókkal

Most, hogy megvannak a jogcímek, konfigurálhatjuk a megbízható IP-címeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Azure Active Directory** > **Feltételes hozzáférés** > **Nevesített helyek** lehetőséget.
3. A **Feltételes hozzáférés – Nevesített helyek** panelen válassza az  **MFA megbízható IP-címeinek konfigurálása** lehetőséget

   ![Azure AD – feltételes hozzáférés – nevesített helyek – MFA megbízható IP-címeinek konfigurálása](./media/howto-mfa-adfs/trustedip6.png)

4. A Szolgáltatásbeállítások oldalon, a **megbízható IP-címek** területen jelölje be a **Többtényezős hitelesítés kihagyása az összevont felhasználók intranetről indított kérelmei esetén** elemet.  
5. Kattintson a **mentés** gombra.

Készen is van. Ekkor az összevont Office 365-felhasználóknak csak az MFA-t kell használniuk, amikor egy jogcím a vállalati intraneten kívülről származik.
