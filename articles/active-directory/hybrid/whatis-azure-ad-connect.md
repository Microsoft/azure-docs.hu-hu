---
title: Mi az Azure AD Connect és a kapcsolat állapota. | Microsoft Docs
description: Ismerje meg a helyszíni környezet Azure AD-vel való szinkronizálásához és figyeléséhez használt eszközöket.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afc3d1d84d7b50cdaa7031230d7085e9f611c6c6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304970"
---
# <a name="what-is-azure-ad-connect"></a>Mi az Azure AD Connect?

A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat.  Ez a tevékenység a következő jellemzőkkel bír:
     
- [Jelszó kivonatának szinkronizálása](whatis-phs.md) – a bejelentkezési módszer, amely egy helyszíni ad-jelszó kivonatát szinkronizálja az Azure ad-vel.
- [Átmenő hitelesítés](how-to-connect-pta.md) – a bejelentkezési módszer, amely lehetővé teszi, hogy a felhasználók ugyanazt a jelszót használják a helyszínen és a felhőben, de nincs szükség összevont környezet további infrastruktúrájának használatára.
- [Összevonás-integráció](how-to-connect-fed-whatis.md) – az összevonás a Azure ad Connect választható része, és a hibrid környezetek helyszíni AD FS-infrastruktúrával való konfigurálására használható. Emellett AD FS felügyeleti képességeket is biztosít, mint például a tanúsítvány megújítása és a AD FS-kiszolgáló további telepítései.
- [Szinkronizálás](how-to-connect-sync-whatis.md) – a felhasználók, csoportok és egyéb objektumok létrehozásához felelős.  Továbbá a helyszíni felhasználók és csoportok személyazonossági információinak biztosítása a felhővel egyező.  Ez a szinkronizálás jelszó-kivonatokat is tartalmaz.
- [Állapot-figyelés](whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) – a Azure ad Connect Health robusztus figyelést biztosíthat, és a Azure Portal központi helyet biztosíthat a tevékenység megtekintéséhez. 


![Mi az az Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Mi az Azure AD Connect Health?

A Azure Active Directory (Azure AD) csatlakozási állapota biztosítja a helyszíni identitás-infrastruktúra hatékony monitorozását. Lehetővé teszi a megbízható kapcsolat fenntartását a Microsoft 365 és a Microsoft Online Services szolgáltatással.  Ezt a megbízhatóságot úgy érheti el, ha a legfontosabb identitás-összetevők számára biztosít figyelési képességeket. Emellett a kulcsfontosságú adatpontokat is könnyen elérhetővé teszi az összetevőkről.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. A Azure AD Connect Health portálon megtekintheti a riasztásokat, a Teljesítményfigyelőt, a használati elemzéseket és az egyéb információkat. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. A felhasználók és a szervezetek a következőket vehetik igénybe:

* A felhasználók egyetlen identitással férhetnek hozzá a helyszíni alkalmazásokhoz és a felhőalapú szolgáltatásokhoz, például a Microsoft 365hoz.
* Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.
* Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. A Azure AD Connect az Identity Integration Tools (például az rSync és a Azure AD-szinkronizáló) régebbi verzióit váltja fel. További információ: [hibrid identitás címtár-integrációs eszközök összehasonlítása](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
Az Azure AD-val végzett hitelesítés során a felhasználók hatékonyabbak, mivel közös identitással férhet hozzá a Felhőbeli és a helyszíni erőforrásokhoz. A környezet megbízhatóságának biztosítása, hogy a felhasználók hozzáférhessenek ezekhez az erőforrásokhoz, kihívás lesz.  Azure AD Connect Health segíti a helyszíni identitás-infrastruktúra monitorozását és elemzését, így biztosítva a környezet megbízhatóságát. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerűen és gyorsan végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS legfontosabb képességei.

Főbb előnyök és ajánlott eljárások:

|Főbb előnyök|Ajánlott eljárások|
|-----|-----|
|Fokozott biztonság|[Extranet zárolási tendenciák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Jelentések a sikertelen bejelentkezésekről](how-to-connect-health-adfs-risky-ip.md)</br>[Az adatvédelem megfelelő](reference-connect-health-user-privacy.md)|
|Riasztás a [kritikus ADFS-rendszerproblémákkal](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services) kapcsolatban|Kiszolgálók konfigurálása és rendelkezésre állása</br>[Teljesítmény és csatlakozási lehetőségek](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Rendszeres karbantartás|
|Egyszerűen üzembe helyezhető és kezelhető|[Gyors ügynök telepítése](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs)</br>Az ügynökök automatikus frissítése a legújabb verzióra</br>Az adatok perceken belül elérhetők a portálon|
Részletes [használati metrikák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|A leggyakrabban használt alkalmazások</br>Hálózati helyek és TCP-kapcsolat</br>Jogkivonat-kérelmek kiszolgálónként|
|Nagyszerű felhasználói élmény|Az Azure Portal irányítópult stílusa</br>[Riasztások e-mailben](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>A Azure AD Connect használatára vonatkozó licencfeltételek

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>A Azure AD Connect Health használatára vonatkozó licencfeltételek
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Következő lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md)
