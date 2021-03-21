---
title: Erőforrások biztonságossá tétele az Azure AD MFA-val és az ADFS-Azure Active Directory
description: Ez az Azure AD Multi-Factor Authentication oldal, amely leírja, hogyan kezdheti el az Azure AD MFA használatát, és hogyan AD FS a felhőben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96743241"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>A felhőalapú erőforrások biztonságossá tétele az Azure AD Multi-Factor Authentication és AD FS

Ha a szervezete Azure Active Directory összevonással összevont, az Azure ad Multi-Factor Authentication vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával gondoskodhat az Azure AD által elért erőforrások védelméről. Az alábbi eljárásokkal védheti Azure Active Directory erőforrásokat az Azure AD Multi-Factor Authentication vagy a Active Directory összevonási szolgáltatások (AD FS) használatával.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Az Azure AD-erőforrások AD FS-sel való védelme

A felhőszolgáltatás biztosításához állítson be egy jogcímszabályt, hogy az Active Directory összevonási szolgáltatások a multipleauthn jogcímet adja ki, amikor egy felhasználó sikeresen végez kétlépéses ellenőrzést. Ez a jogcím átkerül az Azure AD-re. Az alábbi eljárás bemutatja ennek lépéseit:

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.

   ![ADFS-konzol – függő entitások Megbízhatóságai](./media/howto-mfa-adfs/trustedip1.png)

4. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása** elemre.

   ![Kiadási átalakítási szabályok szerkesztése](./media/howto-mfa-adfs/trustedip2.png)

5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.

   ![Képernyőfelvétel: átalakítási jogcím szabályának hozzáadása varázsló, amelyben kiválaszthatja a jogcím-szabály sablonját.](./media/howto-mfa-adfs/trustedip3.png)

6. Adjon nevet a szabálynak. 
7. Válassza a **Hitelesítési módszerek hivatkozásai** lehetőséget a Bejövő jogcím típusaként.
8. Válassza **Az összes jogcímérték továbbítása** lehetőséget.
    ![Képernyőfelvétel: az átalakítási jogcím szabályának hozzáadása varázsló, amely az összes jogcím értékének átadását választja.](./media/howto-mfa-adfs/configurewizard.png)
9. Kattintson a **Finish** (Befejezés) gombra. Zárja be az AD FS felügyeleti konzolt.

## <a name="trusted-ips-for-federated-users"></a>Megbízható IP-címek összevont felhasználók számára

Az adminisztrátorok a megbízható IP-címek segítségével megkerülhetik a kétlépéses ellenőrzést olyan IP-címek vagy összevont felhasználók esetében, akiknek a kérései a saját intranetes hálózatukról származnak. Az alábbi szakaszok azt ismertetik, hogyan konfigurálható az Azure AD Multi-Factor Authentication megbízható IP-címek összevont felhasználókkal és kétlépéses ellenőrzéssel, ha egy összevont felhasználók intraneten belülről érkező kérés származik. Ehhez úgy kell konfigurálni az AD FS-t, hogy áteresztést vagy a bejövő jogcímsablonok szűrését használja a vállalati hálózaton belüli jogcímtípushoz.

Ez a példa Microsoft 365t használ a függő entitás megbízhatóságához.

### <a name="configure-the-ad-fs-claims-rules"></a>Az AD FS-jogcímszabályok konfigurálása

Az első lépés az AD FS-jogcímek konfigurálása. Két jogcímszabályt hozzon létre: egyet a vállalati hálózaton belüli jogcímtípushoz és egy másikat ahhoz, hogy a felhasználók bejelentkezve maradjanak.

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity platform** elemre, és válassza a **jogcímek szerkesztése...** 
    ![ lehetőséget. ADFS-konzol – jogcím szabályainak szerkesztése](./media/howto-mfa-adfs/trustedip1.png)
4. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása elemre.** 
    ![ Jogcím-szabály hozzáadása](./media/howto-mfa-adfs/trustedip2.png)
5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.
   ![Képernyőfelvétel: az átalakítási jogcím szabályának hozzáadása varázsló, amelyben a bejövő jogcím továbbítása vagy szűrése lehetőséget választja.](./media/howto-mfa-adfs/trustedip3.png)
6. A Jogcímszabály neve melletti mezőben adjon nevet a szabálynak. Például: InsideCorpNet.
7. A bejövő jogcím típusa melletti legördülő listából válassza a **vállalati hálózaton belül** lehetőséget.
   ![A vállalati hálózat jogcímen belüli Hozzáadás](./media/howto-mfa-adfs/trustedip4.png)
8. Kattintson a **Finish** (Befejezés) gombra.
9. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása** elemre.
10. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Jogcímek küldése egyéni szabállyal** elemet a legördülő menüből, és kattintson a **Tovább** gombra.
11. A jogcím szabály neve alatt található mezőben adja meg a *bejelentkezett felhasználók megtartása* értéket.
12. Az Egyéni szabály mezőbe írja be a következőt:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. Kattintson a **Finish** (Befejezés) gombra.
14. Kattintson az **Alkalmaz** gombra.
15. Kattintson az **OK** gombra.
16. Zárja be az AD FS felügyeleti konzolt.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>Az Azure AD Multi-Factor Authentication megbízható IP-címeinek konfigurálása összevont felhasználókkal

Most, hogy megvannak a jogcímek, konfigurálhatjuk a megbízható IP-címeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés**  >  **névvel ellátott helyet**.
3. A **feltételes hozzáférés – elnevezett helyek** panelen válassza az **MFA megbízható IP** -címek konfigurálása lehetőséget.

   ![Azure AD feltételes hozzáférés – nevesített helyek konfigurálása MFA megbízható IP-címek](./media/howto-mfa-adfs/trustedip6.png)

4. A Szolgáltatásbeállítások oldalon, a **megbízható IP**-címek területen válassza a **többtényezős hitelesítés kihagyása az összevont felhasználók számára az intraneten** lehetőséget.  
5. Kattintson a **Mentés** gombra.

Ennyi az egész! Ezen a ponton az összevont Microsoft 365 felhasználóknak csak az MFA használatát kell használniuk, ha egy jogcím a vállalati intraneten kívülről származik.
