---
title: Oktatóanyag – egyetlen erdő egyetlen Azure AD-Bérlővel való integrálása
description: Ez a témakör az előfeltételeket és a hardverkövetelmények Felhőbeli szinkronizálását ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a93ce1c4274f63a863880e932b247726639e5cd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614572"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Oktatóanyag: egyetlen erdő integrálása egyetlen Azure AD-Bérlővel

Ez az oktatóanyag végigvezeti a hibrid identitási környezetek létrehozásán a Azure Active Directory (Azure AD) összekapcsolásával a Cloud Sync használatával.

![Létrehozás](media/tutorial-single-forest/diagram-2.png)

Az oktatóanyagban létrehozott környezetet tesztelésre vagy a Felhőbeli szinkronizálás megismerésére használhatja.

## <a name="prerequisites"></a>Előfeltételek
### <a name="in-the-azure-active-directory-admin-center"></a>A Azure Active Directory felügyeleti központban

1. Hozzon létre egy csak felhőalapú globális rendszergazdai fiókot az Azure AD-bérlőn. Így kezelheti a bérlő konfigurációját, ha a helyszíni szolgáltatások meghibásodnak vagy elérhetetlenné válnak. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../fundamentals/add-users-azure-active-directory.md). Ennek a lépésnek a befejezése kritikus fontosságú annak biztosítása érdekében, hogy ne legyen kizárva a bérlőből.
2. Adjon hozzá egy vagy több [Egyéni tartománynevet](../fundamentals/add-custom-domain.md) az Azure ad-bérlőhöz. A felhasználók a következő tartománynevek egyikével jelentkezhetnek be.

### <a name="in-your-on-premises-environment"></a>Helyszíni környezetben

1. Windows Server 2012 R2 vagy újabb rendszert futtató, tartományhoz csatlakoztatott, legalább 4 GB RAM-mal és .NET 4.7.1 + futtatókörnyezettel rendelkező gazdagép azonosítása 

2. Ha tűzfal található a kiszolgálók és az Azure AD között, konfigurálja a következő elemeket:
   - Győződjön meg arról, hogy az ügynökök az alábbi portokon keresztül tehetnek *kimenő* kéréseket az Azure ad-nek:

     | Portszám | Használat célja |
     | --- | --- |
     | **80** | Letölti a visszavont tanúsítványok listáját (CRL) a TLS/SSL-tanúsítvány ellenőrzése közben. |
     | **443** | Kezeli a szolgáltatással folytatott összes kimenő kommunikációt |
     | **8080** (nem kötelező) | Az ügynökök 10 percenként jelentik az állapotukat az 8080-as porton keresztül, ha a 443-es port nem érhető el. Az állapot az Azure AD portálon látható. |
     
     Ha a tűzfal a felhasználók helye szerint érvényesíti a szabályokat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatások adatforgalma számára.
   - Ha a tűzfal vagy a proxy lehetővé teszi a biztonságos utótagok megadását, adja hozzá a t-t a **\* . msappproxy.net** és a **\* . servicebus.Windows.net** kapcsolatokhoz. Ha nem, engedélyezze a hozzáférést az [Azure Datacenter IP-tartományokhoz](https://www.microsoft.com/download/details.aspx?id=41653), amelyek hetente frissülnek.
   - Az ügynököknek hozzá kell férniük a **login.Windows.net** és a **login.microsoftonline.com** a kezdeti regisztrációhoz. Nyissa meg a tűzfalat az URL-címekhez is.
   - A tanúsítvány érvényesítéséhez oldja fel a következő URL-címeket: **mscrl.microsoft.com:80**, **CRL.microsoft.com:80**, **OCSP.msocsp.com:80** és **www \. Microsoft.com:80**. Mivel ezek az URL-címek más Microsoft-termékekkel való tanúsítvány-érvényesítéshez használatosak, előfordulhat, hogy az URL-címeket feloldják.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Az Azure AD Connect létesítési ügynök telepítése
1. Jelentkezzen be a tartományhoz csatlakoztatott kiszolgálóra.  Ha az  [alapszintű ad-és Azure-környezettel](tutorial-basic-ad-azure.md) foglalkozó oktatóanyagot használja, az DC1 lenne.
2. Jelentkezzen be a Azure Portalba kizárólag Felhőbeli globális rendszergazdai hitelesítő adatok használatával.
3. A bal oldalon válassza a **Azure Active Directory**, majd a **Azure ad Connect** lehetőséget, majd a központban válassza a **Felhőbeli szinkronizálás kezelése** elemet.

   ![Azure Portal](media/how-to-install/install-6.png)

4. Kattintson az **ügynök letöltése** elemre.
5. Futtassa az Azure AD Connect üzembe helyezési ügynököt.
6. A splash képernyőn **fogadja el** a licencelési feltételeket, majd kattintson a **telepítés** gombra.

   ![Képernyőfelvétel: "Microsoft Azure A D-hez való kapcsolat létesítésére szolgáló ügynök csomag" splash screen.](media/how-to-install/install-1.png)

7. A művelet befejezése után elindul a konfigurációs varázsló.  Jelentkezzen be az Azure AD globális rendszergazdai fiókjával.  Vegye figyelembe, hogy ha az Internet Explorer fokozott biztonsági funkciója engedélyezve van, ez letiltja a bejelentkezést.  Ebben az esetben zárjuk le a telepítést, tiltsa le az IE fokozott biztonságát a Kiszolgálókezelőben, majd kattintson a **HRE-létesítési ügynök varázslóra** a telepítés újraindításához.
8. A **Active Directory összekapcsolása** képernyőn kattintson a **könyvtár hozzáadása** lehetőségre, majd jelentkezzen be a Active Directory tartományi rendszergazdai fiókjával.  Megjegyzés: a tartományi rendszergazdai fióknak nem kell módosítania a jelszó-módosítási követelményeket. Abban az esetben, ha a jelszó lejár vagy megváltozik, újra kell konfigurálnia az ügynököt az új hitelesítő adatokkal. Ezzel a művelettel a helyszíni címtárat fogja felvenni.  Kattintson a **Tovább** gombra.

   ![A "kapcsolat Active Directory" képernyő képernyőképe.](media/how-to-install/install-3a.png)

9. A **konfiguráció kész** képernyőn kattintson a **Confirm (megerősítés**) gombra.  Ez a művelet regisztrálja és újraindítja az ügynököt.

   ![Képernyőkép, amely a "konfigurálás kész" képernyőt jeleníti meg.](media/how-to-install/install-4a.png)

10. Ha a művelet befejeződik, megjelenik egy értesítés: **az ügynök konfigurációjának ellenőrzése sikerült.**  Kattintson a **Kilépés** lehetőségre.</br>
![Üdvözlőképernyő](media/how-to-install/install-5.png)</br>
11. Ha továbbra is megjelenik a kezdeti splash képernyő, kattintson a **Bezárás** gombra.


## <a name="verify-agent-installation"></a>Ügynök telepítésének ellenőrzése
Az ügynök ellenőrzése a Azure Portal és az ügynököt futtató helyi kiszolgálón történik.

### <a name="azure-portal-agent-verification"></a>Azure Portal ügynök ellenőrzése
Az alábbi lépéseket követve ellenőrizheti, hogy az ügynök látja-e az Azure-t:

1. Jelentkezzen be az Azure Portalra.
2. A bal oldalon válassza a **Azure Active Directory**, majd a **Azure ad Connect** és a központban válassza a **Felhőbeli szinkronizálás kezelése** lehetőséget.</br>
![Azure Portal](media/how-to-install/install-6.png)</br>

3.  A **Azure ad Connect Cloud Sync (felhő szinkronizálása** ) képernyőn kattintson az **összes ügynök áttekintése** elemre.
![Azure AD-kiépítés](media/how-to-install/install-7.png)</br>
 
4. A helyszíni **kiépítési ügynökök képernyőjén** látni fogja a telepített ügynököket.  Ellenőrizze, hogy a szóban forgó ügynök **aktív**-e, és hogy van-e megjelölve.
![Kiépítési ügynökök](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>A helyi kiszolgálón
Az ügynök futtatásának ellenőrzéséhez kövesse az alábbi lépéseket:

1.  Jelentkezzen be a kiszolgálóra egy rendszergazdai fiókkal
2.  Nyissa meg a szolgáltatásokat vagy navigáljon a **szolgáltatáshoz** , vagy indítsa el a Start/Run/Services. msc parancsot.
3.  Győződjön meg arról, hogy a **szolgáltatások** területen a **Microsoft Azure ad összekapcsolási ügynök frissítése** és a **Microsoft Azure ad kapcsolat létesítése ügynök** van jelen, és az állapota **fut**.
![Szolgáltatások](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Azure AD Connect Felhőbeli szinkronizálás konfigurálása
 A kiépítés konfigurálásához kövesse az alábbi lépéseket

1.  Jelentkezzen be az Azure AD-portálra.
2.  Kattintson **Azure Active Directory**
3.  Kattintson **Azure ad Connect**
4.  Válassza a **felhőalapú szinkronizálás kezelése** 
 ![ képernyőképet a "Cloud Sync kezelése" hivatkozásra kattintva.](media/how-to-configure/manage-1.png)
5.  A  
 ![ "New Configuration" (új konfiguráció) hivatkozásra kattintva kiemelve Azure ad Connect Cloud Sync képernyő új konfiguráció képernyőképét.](media/tutorial-single-forest/configure-1.png)
7.  A konfiguráció képernyőn adja meg az **értesítő e-mailt**, helyezze át a választót az **engedélyezéshez** , majd kattintson a **Mentés** gombra.
![Képernyőkép a konfigurálásról a képernyőn megjelenő értesítő e-mailben és a kijelölés engedélyezése lapon.](media/how-to-configure/configure-2.png)
1.  A konfigurációs állapotnak most **kifogástalannak** kell lennie.
![Képernyőfelvétel: Azure AD Connect felhő szinkronizálási képernyője, amely kifogástalan állapotot mutat.](media/how-to-configure/manage-4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Ekkor ellenőrzi, hogy a helyszíni címtárban lévő felhasználók szinkronizálva lettek-e, és már léteznek-e az Azure AD-bérlőben.  Vegye figyelembe, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza a **Azure Active Directory**
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4. Ellenőrizze, hogy megjelenik-e az új felhasználók a bérlőben</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés az egyik felhasználóval

1. Nyissa meg a következő címet a böngészőben: [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely az új bérlőben lett létrehozva.  A következő formátumban kell bejelentkeznie: ( user@domain.onmicrosoft.com ). Ugyanazt a jelszót használja, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Ellenőrzés](media/tutorial-single-forest/verify-1.png)</br>

Ezzel sikeresen beállított egy hibrid identitási környezetet, amellyel tesztelheti és megismerheti az Azure által kínált lehetőségeket.


## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-sync.md)