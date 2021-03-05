---
title: Active Directory-alapú szolgáltatások összehasonlítása az Azure-ban | Microsoft Docs
description: Ebben az áttekintésben összehasonlítjuk a Active Directory Domain Services, Azure Active Directory és Azure Active Directory Domain Services különböző identitás-ajánlatait.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: justinha
ms.openlocfilehash: 48ce982a6d6d7a3ed12d6e3b4711d111dec4d6f8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174631"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Az önállóan felügyelt Active Directory Domain Services, Azure Active Directory és felügyelt Azure Active Directory Domain Services összehasonlítása

Az alkalmazások, szolgáltatások vagy eszközök központi identitáshoz való hozzáférésének három gyakori módja van az Azure-ban Active Directory-alapú szolgáltatások használatának. Ez a választási lehetőség az Identity Solutions szolgáltatásban rugalmasságot biztosít a szervezet igényeinek legmegfelelőbb címtár használatához. Ha például többnyire csak a csak felhőalapú felhasználókat felügyeli, akkor előfordulhat, hogy nem érdemes saját Active Directory Domain Services (AD DS) identitás-megoldást létrehoznia és futtatnia. Ehelyett egyszerűen használhatja a Azure Active Directory.

Habár a három Active Directory-alapú identitási megoldás közös nevet és technológiát használ, úgy tervezték, hogy olyan szolgáltatásokat nyújtsanak, amelyek megfelelnek a különböző felhasználói igényeknek. Ezek az identitás-megoldások és-funkciók magas szinten a következők:

* **Active Directory Domain Services (AD DS)** – nagyvállalati használatra kész Lightweight Directory Access Protocol (LDAP) kiszolgáló, amely olyan kulcsfontosságú funkciókat biztosít, mint például az identitás és a hitelesítés, a számítógép-objektumok kezelése, a csoportházirend és a megbízhatósági kapcsolatok.
    * AD DS a helyszíni informatikai környezettel rendelkező számos szervezet központi összetevője, amely a felhasználói fiókok hitelesítését és a számítógép-felügyeleti funkciókat biztosítja.
    * További információ: [Active Directory Domain Services áttekintése a Windows Server dokumentációjában][overview-adds].
* **Azure Active Directory (Azure ad)** – felhőalapú identitás-és mobileszköz-kezelés, amely felhasználói fiókokat és hitelesítési szolgáltatásokat biztosít olyan erőforrásokhoz, mint például a Microsoft 365, a Azure Portal vagy az SaaS-alkalmazás.
    * Az Azure AD szinkronizálható helyszíni AD DS környezettel, így egyetlen identitást biztosíthat a felhőben natív módon működő felhasználóknak.
    * További információ az Azure AD-ről: [Mi az Azure Active Directory?][whatis-azuread]
* **Azure Active Directory Domain Services (Azure AD DS)** – felügyelt tartományi szolgáltatásokat biztosít a teljes mértékben kompatibilis, hagyományos AD DS-funkciók, például a tartományhoz való csatlakozás, a csoportházirend, az LDAP és a KERBEROS/NTLM hitelesítés részeként.
    * Az Azure AD DS integrálható az Azure AD-vel, amely képes szinkronizálni a helyszíni AD DS-környezettel. Ez a képesség kiterjeszti a központi identitás használati eseteit az Azure-ban futó hagyományos webalkalmazásokra egy lift-és eltolási stratégia részeként.
    * Ha többet szeretne megtudni az Azure AD-vel és a helyszíni szolgáltatással való szinkronizálásról, tekintse meg a [felügyelt tartományokban található objektumok és hitelesítő adatok szinkronizálásának módját][synchronization].

Ez az áttekintő cikk összehasonlítja, hogy ezek az identitás-megoldások hogyan működhetnek együtt, vagy a szervezet igényeitől függően függetlenül használhatók.

> [!div class="nextstepaction"]
> [Első lépésként hozzon létre egy Azure AD DS felügyelt tartományt a Azure Portal használatával][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS és önállóan felügyelt AD DS

Ha olyan alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyek olyan hagyományos hitelesítési mechanizmusokhoz férnek hozzá, mint például a Kerberos vagy az NTLM, kétféleképpen biztosíthat Active Directory Domain Services a felhőben:

* Azure Active Directory Domain Services (Azure AD DS) használatával létrehozott *felügyelt tartomány* . A Microsoft létrehozza és kezeli a szükséges erőforrásokat.
* Olyan *önállóan felügyelt* tartomány, amelyet a hagyományos erőforrások, például a virtuális gépek (VM), a Windows Server vendég operációs rendszer és a Active Directory Domain Services (AD DS) használatával hozhat létre és konfigurálhat. Ezután továbbra is felügyelheti ezeket az erőforrásokat.

Az Azure AD DS az alapvető szolgáltatási összetevőket a Microsoft *felügyelt* tartományi felhasználói felületként telepíti és tartja karban. Nem kell telepítenie, felügyelni, javítani és biztonságossá tenni a AD DS infrastruktúrát olyan összetevőkhöz, mint a virtuális gépek, a Windows Server operációs rendszer vagy a tartományvezérlők.

Az Azure AD DS szolgáltatások kisebb részhalmazát kínálja a hagyományos, önfelügyelt AD DS-környezet számára, ami csökkenti a tervezési és a felügyeleti komplexitást. Például nincs olyan AD-erdő, tartomány, hely, és replikációs hivatkozás a tervezéshez és a karbantartáshoz. [Az Azure AD DS és a helyszíni környezetek között továbbra is erdőszintű megbízhatósági kapcsolatok hozhatók létre][create-forest-trust].

A felhőben futó alkalmazások és szolgáltatások esetében, valamint a hagyományos hitelesítési mechanizmusokhoz (például Kerberos vagy NTLM) való hozzáférésre van szükség, az Azure AD DS felügyelt tartományi élményt biztosít a minimális adminisztratív terhelésnek megfelelően. További információ: [felügyeleti fogalmak a felhasználói fiókokhoz, jelszavakhoz és felügyelethez az Azure ad DSban][administration-concepts].

Ha önálló felügyelet alatt álló AD DS környezetet telepít és futtat, meg kell őriznie az összes kapcsolódó infrastruktúrát és címtár-összetevőt. Az önfelügyelt AD DS-környezetek További karbantartási terheléssel rendelkeznek, azonban további feladatokat is végrehajthat, például kiterjesztheti a sémát, vagy erdőszintű megbízhatósági kapcsolatot hozhat létre.

Közös üzembe helyezési modellek olyan önfelügyelt AD DS környezetekhez, amelyek identitást biztosítanak a Felhőbeli alkalmazásokhoz és szolgáltatásokhoz, többek között a következők:

* **Önálló, csak felhőalapú AD DS** – az Azure-beli virtuális gépek tartományvezérlőként vannak konfigurálva, és egy különálló, csak felhőalapú AD DS környezet jön létre. Ez a AD DS környezet nem integrálható helyszíni AD DS környezettel. A rendszer a virtuális gépek Felhőbeli beadásához és felügyeletéhez különböző hitelesítő adatokat használ.
* **Erőforrás-erdő üzembe helyezése** – az Azure-beli virtuális gépek tartományvezérlőként vannak konfigurálva, és egy AD DS tartomány jön létre, amely egy meglévő erdő részét képezi. Ezt követően a rendszer egy helyszíni AD DS-környezethez konfigurálja a megbízhatósági kapcsolatot. Más Azure-beli virtuális gépek tartományhoz csatlakozhatnak a felhőben található erőforrás-erdőhöz. A felhasználói hitelesítés VPN-/ExpressRoute-kapcsolaton keresztül történik a helyszíni AD DS környezettel.
* Helyszíni **tartomány kiterjesztése az Azure** -ra – az Azure Virtual Network VPN-/ExpressRoute-kapcsolaton keresztül csatlakozik egy helyszíni hálózathoz. Az Azure virtuális gépek csatlakoznak ehhez az Azure-beli virtuális hálózathoz, amely lehetővé teszi, hogy a tartományhoz csatlakozzanak a helyszíni AD DS környezethez.
    * Alternatív megoldásként Azure-beli virtuális gépeket hozhat létre, és áttekintheti őket replika tartományvezérlőként a helyszíni AD DS tartományból. Ezek a tartományvezérlők egy VPN-/ExpressRoute-kapcsolaton keresztül replikálódnak a helyszíni AD DS környezetbe. A helyszíni AD DS tartomány hatékonyan bővíthető az Azure-ba.

Az alábbi táblázat néhány olyan funkciót vázol fel, amelyek a szervezet számára szükségesek, valamint a felügyelt Azure AD DS tartomány vagy egy önállóan felügyelt AD DS tartomány közötti különbségek:

| **Szolgáltatás** | **Azure AD DS** | **Önállóan felügyelt AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Felügyelt szolgáltatás**                               | **&#x2713;** | **&#x2715;** |
| **Biztonságos központi telepítések**                            | **&#x2713;** | A rendszergazda gondoskodik az üzemelő példányról |
| **DNS-kiszolgáló**                                    | **&#x2713;** (felügyelt szolgáltatás) |**&#x2713;** |
| **Tartományi vagy vállalati rendszergazdai jogosultságok** | **&#x2715;** | **&#x2713;** |
| **Csatlakozás tartományhoz**                                   | **&#x2713;** | **&#x2713;** |
| **Tartományi hitelesítés NTLM és Kerberos használatával** | **&#x2713;** | **&#x2713;** |
| **Kerberos által korlátozott delegálás**               | Erőforrás-alapú | Erőforrás-alapú & fiók alapú|
| **Egyéni szervezeti egység szerkezete**                           | **&#x2713;** | **&#x2713;** |
| **Csoportházirend**                                  | **&#x2713;** | **&#x2713;** |
| **Sémakiterjesztések**                             | **&#x2715;** | **&#x2713;** |
| **AD-tartomány/erdőszintű megbízhatósági kapcsolatok**                     | **&#x2713;** (egyirányú kimenő erdőszintű megbízhatósági kapcsolat) | **&#x2713;** |
| **Biztonságos LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP-olvasás**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP-írás**                                    | **&#x2713;** (a felügyelt tartományon belül) | **&#x2713;** |
| **Földrajzilag elosztott üzemelő példányok**                   | **&#x2713;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS és Azure AD

Az Azure AD segítségével kezelheti a szervezet által használt eszközök identitását, és szabályozhatja a vállalati erőforrásokhoz való hozzáférést ezekből az eszközökről. A felhasználók az Azure AD-vel is regisztrálhatják személyes eszközeiket (saját (BYO) modell), amely identitást biztosít az eszköz számára. Az Azure AD ezt követően hitelesíti az eszközt, amikor egy felhasználó bejelentkezik az Azure AD-be, és az eszköz használatával fér hozzá a biztonságos erőforrásokhoz. Az eszköz a mobileszköz-felügyeleti (MDM) szoftverek, például a Microsoft Intune használatával felügyelhető. Ez a kezelési képesség lehetővé teszi a bizalmas erőforrásokhoz való hozzáférést a felügyelt és a szabályzatoknak megfelelő eszközökre.

A hagyományos számítógépek és laptopok is csatlakozhatnak az Azure AD-hez. Ez a mechanizmus ugyanazokat az előnyöket nyújtja, mint a személyes eszközök Azure AD-vel való regisztrálásának, például a felhasználók számára, hogy a vállalati hitelesítő adataik használatával bejelentkezzenek az eszközre.

Az Azure AD-hez csatlakoztatott eszközök a következő előnyöket nyújtják:

* Egyszeri bejelentkezés (SSO) az Azure AD által védett alkalmazásokhoz.
* Vállalati házirend-kompatibilis barangolás a felhasználói beállítások között az eszközökön.
* Hozzáférés a vállalati Windows áruházhoz vállalati hitelesítő adatok használatával.
* Vállalati Windows Hello.
* Korlátozott hozzáférés az alkalmazásokhoz és erőforrásokhoz a vállalati házirendnek megfelelő eszközökről.

Az eszközök csatlakoztathatók az Azure AD-hez olyan hibrid üzembe helyezéssel vagy anélkül, amely helyszíni AD DS környezetet is tartalmaz. Az alábbi táblázat ismerteti a közös eszközök tulajdonosi modelljeit, valamint azt, hogy ezek általában hogyan csatlakoznak egy tartományhoz:

| **Eszköz típusa**                                        | **Eszközplatformok**             | **Mechanizmus**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Személyes eszközök                                          | Windows 10, iOS, Android, macOS | Az Azure AD-ban regisztrálva    |
| A szervezet tulajdonában lévő eszköz nincs a helyszíni AD DShoz csatlakoztatva | Windows 10                       | Azure AD-hez csatlakoztatva        |
| A szervezet tulajdonában lévő eszköz egy helyszíni AD DShoz csatlakoztatva  | Windows 10                       | csatlakozik a Hibrid Azure AD-hez |

Egy Azure AD-hez csatlakoztatott vagy regisztrált eszközön a felhasználói hitelesítés a modern OAuth/OpenID Connect-alapú protokollok használatával történik. Ezek a protokollok úgy lettek kialakítva, hogy az interneten keresztül működjenek, ezért kiválóan alkalmasak a mobil környezetekhez, ahol a felhasználók bárhonnan hozzáférhetnek a vállalati erőforrásokhoz

Az Azure AD DS csatlakoztatott eszközeivel az alkalmazások a Kerberos és az NTLM protokollt használhatják a hitelesítéshez, így az Azure-beli virtuális gépeken való futtatásra áttelepített örökölt alkalmazások a lift és a SHIFT stratégia részeként is támogatottak. Az alábbi táblázat az eszközök megjelenítésének és hitelesítésének különbségeit vázolja fel a címtárban:

| **Szempont**                      | **Azure AD-hez csatlakoztatott**                                 | **Azure AD DS-csatlakozás**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Eszköz által vezérelt            | Azure AD                                            | Azure AD DS felügyelt tartomány                                                |
| Képviselet a címtárban | Eszközök objektumai az Azure AD-címtárban            | Számítógép-objektumok az Azure AD DS felügyelt tartományban                        |
| Hitelesítés                  | OAuth/OpenID Connect-alapú protokollok              | Kerberos és NTLM protokollok                                               |
| Kezelés                      | Mobileszköz-felügyeleti (MDM) szoftverek, például az Intune | Csoportházirend                                                              |
| Hálózatkezelés                      | Az interneten működik                             | Csatlakoznia kell ahhoz a virtuális hálózathoz, amelyhez a felügyelt tartományt telepíteni kell |
| Nagyszerű...                    | Végfelhasználói mobil-vagy asztali eszközök                  | Az Azure-ban üzembe helyezett kiszolgálói virtuális gépek                                              |


Ha a helyszíni AD DS és az Azure AD az ADFS használatával összevont hitelesítésre van konfigurálva, akkor az Azure DS-ben nem érhető el (aktuális/érvényes) jelszó-kivonat. A Fed-hitelesítés végrehajtása előtt létrehozott Azure AD-felhasználói fiókok esetében előfordulhat, hogy régi jelszó kivonata van, de ez valószínűleg nem egyezik meg a helyszíni jelszavuk kivonatával. Ezért az Azure AD DS nem fogja tudni érvényesíteni a felhasználók hitelesítő adatait

## <a name="next-steps"></a>Következő lépések

Az Azure AD DS használatának megkezdéséhez [hozzon létre egy azure AD DS felügyelt tartományt a Azure Portal használatával][tutorial-create].

További információt [a felhasználói fiókokkal, jelszavakkal és az Azure-AD DS felügyeletével kapcsolatos fogalmakról][administration-concepts] , valamint az [objektumok és a hitelesítő adatok felügyelt tartományon belüli szinkronizálásáról][synchronization]is olvashat.

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
