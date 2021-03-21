---
title: Magas rendelkezésre állás az Azure MFA-kiszolgálóhoz – Azure Active Directory
description: Az Azure Multi-Factor Authentication-kiszolgáló több példányának üzembe helyezése magas rendelkezésre állást biztosító konfigurációkon.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584476"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Az Azure Multi-Factor Authentication-kiszolgáló konfigurálása a magas rendelkezésre állás érdekében

Ha magas rendelkezésre állást szeretne biztosítani az Azure Server MFA üzembe helyezéséhez, több MFA-kiszolgálót kell telepítenie. Ez a szakasz egy elosztott terhelésű kialakítással kapcsolatos információkat tartalmaz, hogy magas rendelkezésre állású célokat lehessen elérni az Azure MFS Server üzembe helyezése során.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

## <a name="mfa-server-overview"></a>MFA-kiszolgáló – áttekintés

Az Azure MFA-kiszolgáló szolgáltatási architektúrája több összetevőből áll, ahogy az alábbi ábrán is látható:

 ![MFA-kiszolgáló architektúrájának összetevői](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Az MFA-kiszolgálók olyan Windows-kiszolgálók, amelyeken telepítve van az Azure Multi-Factor Authentication szoftver. Az MFA-kiszolgáló példányát aktiválnia kell az Azure-beli MFA-szolgáltatással a működéséhez. A helyszínen több MFA-kiszolgáló is telepíthető.

Az első telepített MFA-kiszolgáló az elsődleges MFA-kiszolgáló, amelyet az Azure MFA szolgáltatás alapértelmezés szerint aktivál. Az elsődleges MFA-kiszolgáló egy írható másolatot tartalmaz a PhoneFactor. pfdata adatbázisról. Az MFA-kiszolgáló példányainak későbbi telepítése alárendeltnek nevezzük. Az MFA-alárendeltek rendelkeznek a PhoneFactor. pfdata adatbázis többszörözött írásvédett másolatával. Az MFA-kiszolgálók a távoli eljáráshívás (RPC) használatával replikálják az adatokat. Az összes MFA-kiszolgálónak együtt kell lennie tartományhoz csatlakoztatottnak vagy önállónak az információk replikálásához.

Mindkét MFA elsődleges és alárendelt MFA-kiszolgáló kommunikál az MFA szolgáltatással, ha kétfaktoros hitelesítésre van szükség. Ha például egy felhasználó olyan alkalmazáshoz próbál hozzáférni, amely kétfaktoros hitelesítést igényel, akkor a felhasználót először egy identitás-szolgáltató hitelesíti, például Active Directory (AD).

Az AD-vel való sikeres hitelesítés után az MFA-kiszolgáló kommunikálni fog az MFA szolgáltatással. Az MFA-kiszolgáló megvárja az MFA szolgáltatástól érkező értesítést, hogy engedélyezze vagy megtagadja a felhasználó hozzáférését az alkalmazáshoz.

Ha az MFA elsődleges kiszolgálója offline állapotba kerül, a hitelesítések továbbra is feldolgozhatók, de az MFA-adatbázis módosítását igénylő műveletek nem dolgozhatók fel. (Példák: a felhasználók hozzáadása, az önkiszolgáló PIN-kód módosítása, a felhasználói adatok módosítása vagy a felhasználói portálhoz való hozzáférés)

## <a name="deployment"></a>Üzembe helyezés

Vegye figyelembe a következő fontos pontokat az Azure MFA-kiszolgáló és a hozzá kapcsolódó összetevők terheléselosztásához.

* **A RADIUS standard használatával magas rendelkezésre állást érhet el**. Ha az Azure MFA-kiszolgálókat RADIUS-kiszolgálóként használja, lehetősége van arra, hogy egy MFA-kiszolgálót elsődleges RADIUS-hitelesítési célként és más Azure MFA-kiszolgálóként konfiguráljon másodlagos hitelesítési célként. A magas rendelkezésre állás eléréséhez azonban ez a módszer nem lehet praktikus, mert meg kell várnia az időtúllépési időtartamot, amikor a hitelesítés meghiúsul az elsődleges hitelesítési célhelyen, mielőtt hitelesíteni lehetne őket a másodlagos hitelesítési cél használatával. A RADIUS-ügyfél és a RADIUS-kiszolgálók (ebben az esetben a RADIUS-kiszolgálóként működő Azure MFA-kiszolgálók) közötti RADIUS-forgalom terheléselosztása hatékonyabb, így a RADIUS-ügyfelek egyetlen URL-címmel konfigurálhatók, amelyre rámutatnak.
* **Az MFA alárendeltek manuális előléptetésére van szükség**. Ha az elsődleges Azure MFA-kiszolgáló offline állapotba kerül, a másodlagos Azure MFA-kiszolgálók továbbra is feldolgozzák az MFA-kérelmeket. Amíg azonban az elsődleges MFA-kiszolgáló nem érhető el, a rendszergazdák nem adhatnak hozzá felhasználókat vagy nem módosíthatnak MFA-beállításokat, és a felhasználók nem végezhetnek módosításokat a felhasználói portál használatával. Az elsődleges szerepkörbe beosztott MFA-támogatás mindig manuális folyamat.
* **Az összetevők Separability**. Az Azure MFA-kiszolgáló számos olyan összetevőből áll, amelyek ugyanarra a Windows Server-példányra vagy különböző példányokra telepíthetők. Ezek az összetevők a felhasználói portált, a Mobile App Web Service-t és az ADFS-adaptert (Agent) tartalmazzák. Ez a separability lehetővé teszi a webalkalmazás-proxy használatával a felhasználói portál és a Mobile App Web Server közzétételét a peremhálózaton. Ilyen konfiguráció a kialakítás általános biztonságához járul hozzá, ahogy az a következő ábrán is látható. Az MFA felhasználói portál és a Mobile App webkiszolgáló is üzembe helyezhető, HA terheléselosztási konfigurációban van.

   ![MFA-kiszolgáló peremhálózati hálózattal](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Az egyszeri jelszó (OTP) az SMS-ben (más néven egyirányú SMS) a Sticky-munkamenetek használatát igényli, ha a forgalom terheléselosztásra kerül**. Az egyirányú SMS egy olyan hitelesítési lehetőség, amelynek hatására az MFA-kiszolgáló elküldi a felhasználóknak egy egyszeri jelszavas üzenetet. A felhasználó beírja az egyszeri jelszavas hitelesítés ablakát az MFA-kihívás befejezéséhez. Ha terheléselosztást végez az Azure MFA-kiszolgálókon, akkor ugyanazt a kiszolgálót kell megadnia, amely a kezdeti hitelesítési kérést kézbesíti, és amely az OTP-üzenetet fogadó kiszolgáló Ha egy másik MFA-kiszolgáló megkapja az egyszeri jelszavas választ, a hitelesítési kérdés sikertelen lesz. További információt az SMS-ben az [Azure MFA-kiszolgálóhoz hozzáadott egyszeri jelszóval foglalkozó](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)témakörben talál.
* **A felhasználói portál és a Mobile App Web Service elosztott terhelésű üzembe helyezése Sticky-munkameneteket igényel**. Ha terheléselosztást végez az MFA felhasználói Portálon és a Mobile App Web Service-ben, minden munkamenetnek ugyanazon a kiszolgálón kell maradnia.

## <a name="high-availability-deployment"></a>Magas rendelkezésre állású központi telepítés

Az alábbi ábrán az Azure MFA és annak összetevőinek teljes, HA terheléselosztásos implementációja látható, valamint az ADFS-vel való használatra.

 ![Azure MFA-kiszolgáló HA megvalósítás](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Jegyezze fel a következő elemeket az előző diagram megfelelő számú területéhez.

1. A két Azure MFA-kiszolgáló (MFA1 és MFA2) elosztott terhelésű (mfaapp.contoso.com), és úgy van konfigurálva, hogy statikus portot (4443) használjon a PhoneFactor. pfdata adatbázis replikálásához. A Web Service SDK telepítve van minden egyes MFA-kiszolgálón, hogy engedélyezze a kommunikációt az 443-as TCP-porton az ADFS-kiszolgálókkal. Az MFA-kiszolgálók állapot nélküli terheléselosztási konfigurációban vannak telepítve. Ha azonban az egyszeri jelszavas küldést SMS-ben szeretné használni, az állapot-nyilvántartó terheléselosztást kell használnia.
   ![Azure MFA-kiszolgáló – app Server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Mivel az RPC dinamikus portokat használ, nem ajánlott a tűzfalakat az RPC által potenciálisan használható dinamikus portok tartományához megnyitni. Ha tűzfallal rendelkezik az MFA-alkalmazáskiszolgáló **között** , akkor úgy kell konfigurálnia az MFA-kiszolgálót, hogy az alárendelt és elsődleges kiszolgálók közötti replikációs forgalomra vonatkozóan statikus porton kommunikáljon, és a tűzfalon nyissa meg a portot. A statikus portot úgy kényszerítheti, hogy létrehoz egy DWORD beállításjegyzékbeli értéket a ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` híváskor ```Pfsvc_ncan_ip_tcp_port``` , és az értéket egy elérhető statikus portra állítja be. A kapcsolatokat mindig az alárendelt MFA-kiszolgálók kezdeményezik az elsődleges számára, a statikus portot csak az elsődlegesen kell végrehajtani, de mivel a beosztottak bármikor előállíthatók az elsődlegesnek, a statikus portot minden MFA-kiszolgálón be kell állítani.

2. A két felhasználói portál/MFA-alapú mobileszköz-kiszolgáló (MFA-UP-MAS1 és MFA-UP-MAS2) **állapot-nyilvántartó** konfigurációban (MFA.contoso.com) van elosztott terhelésű. Ne felejtse el, hogy a Sticky-munkamenetek az MFA felhasználói portál és a mobil App Service terheléselosztásának követelménye.
   ![Azure MFA-kiszolgáló – felhasználói portál és mobil App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. Az ADFS-kiszolgálófarm elosztott terhelésű, és a peremhálózaton elosztott terhelésű ADFS-proxyk használatával történik az interneten való közzététele. Minden ADFS-kiszolgáló az ADFS-ügynököt használja az Azure MFA-kiszolgálókkal való kommunikációhoz egyetlen terheléselosztási URL-cím (mfaapp.contoso.com) használatával a 443-as TCP-porton keresztül.

## <a name="next-steps"></a>Következő lépések

* [Az Azure MFA-kiszolgáló telepítése és konfigurálása](howto-mfaserver-deploy.md)
