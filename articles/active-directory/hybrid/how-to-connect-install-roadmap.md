---
title: Az Azure AD Connect és az Azure AD Connect Health telepítési ütemterve. | Microsoft Docs
description: Ez a dokumentum áttekinti azokat a telepítési lehetőségeket és módokat, amelyekkel telepíthető az Azure AD Connect és a Connect Health.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fe128e37103fc913b97f8ac8b9f0395ad74c4198
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496969"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Az Azure AD Connect és az Azure AD Connect Health telepítési ütemterve

## <a name="install-azure-ad-connect"></a>Az Azure AD Connect telepítése

> [!IMPORTANT]
> A Microsoft nem támogatja az Azure AD Connect szinkronizálásának módosítását vagy a hivatalos dokumentumokban szereplő műveleteken kívüli használat. Minden ilyen művelet azt eredményezheti, hogy az Azure AD Connect szinkronizálása inkonzisztens vagy nem támogatott állapotba kerül. A Microsoft ezért nem tud műszaki támogatást biztosítani az ilyen környezetekhez.

Az Azure AD Connect a [Microsoft letöltőközpontból](https://go.microsoft.com/fwlink/?LinkId=615771) tölthető le.

| Megoldás | Forgatókönyv |
| --- | --- |
| Előkészületek – [Hardverkövetelmények és előfeltételek](how-to-connect-install-prerequisites.md) |<li>Az Azure AD Connect telepítése előtt végrehajtandó lépések.</li> |
| [Gyorsbeállítások](how-to-connect-install-express.md) |<li>Ennek a lehetőségnek a használata akkor ajánlott, ha egyerdős AD-vel rendelkezik.</li> <li>Felhasználói bejelentkezés egyetlen jelszóval, jelszó-szinkronizálás segítségével.</li> |
| [Testreszabott beállítások](how-to-connect-install-custom.md) |<li>Több erdő megléte esetén használatos. Számos helyszíni [topológiát](plan-connect-topologies.md) támogat.</li> <li>Testre szabhatja a bejelentkezést, például átmenő hitelesítést alkalmazhat, ADFS-t állíthat be az összevonáshoz, vagy külső identitásszolgáltatót használhat.</li> <li>Testre szabhatja a szinkronizálási funkciókat, például a szűrést és a visszaírást.</li> |
| [Frissítés a DirSync szolgáltatásról](how-to-dirsync-upgrade-get-started.md) |<li>Akkor használatos, ha már rendelkezik működő DirSync-kiszolgálóval.</li> |
| [Frissítés Azure AD Sync-ről vagy Azure AD Connectről](how-to-upgrade-previous-version.md) |<li>Igény szerint számos különböző módszer áll rendelkezésére.</li> |

[A telepítést követően](how-to-connect-post-installation.md) ellenőrizze a megfelelő működést, és rendeljen licenceket a felhasználókhoz.

### <a name="next-steps-to-install-azure-ad-connect"></a>Az Azure AD Connect telepítésének következő lépései
|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect letöltése | [Az Azure AD Connect letöltése](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Telepítés gyorsbeállítások használatával | [Az Azure AD Connect gyorstelepítése](./how-to-connect-install-express.md)|
|Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](./how-to-connect-install-custom.md)|
|Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|A telepítést követően | [A telepítés ellenőrzése és licencek hozzárendelése](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>További információk az Azure AD Connect telepítésével kapcsolatban
Az [üzemeltetéssel](how-to-connect-sync-operations.md) kapcsolatban felmerülő kérdések kezelésére is érdemes felkészülni. [Vészhelyzet](how-to-connect-sync-operations.md#disaster-recovery) esetére megfontolhatja egy készenléti kiszolgáló üzembe állítását. Ha gyakori konfigurációs módosításokat tervez, egy [átmeneti üzemmódú](how-to-connect-sync-operations.md#staging-mode) kiszolgáló beállításán is elgondolkodhat.

|Témakör |Hivatkozás|  
| --- | --- |
|Támogatott topológiák | [Azure AD Connect-topológiák](plan-connect-topologies.md)|
|Tervezési alapelvek | [Az Azure AD Connect tervezési alapelvei](plan-connect-design-concepts.md)|
|Telepítési fiókok | [További információk az Azure AD Connect hitelesítő adataival és engedélyeivel kapcsolatban](reference-connect-accounts-permissions.md)|
|Az üzemeltetés megtervezése | [Az Azure AD Connect szinkronizálása: Üzemeltetési feladatok és szempontok](how-to-connect-sync-operations.md)|
|A felhasználói bejelentkezés lehetőségei | [A felhasználói bejelentkezés lehetőségei az Azure AD Connectben](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>A szinkronizálási funkciók konfigurálása
Az Azure AD Connect számos, szükség szerint bekapcsolható vagy alapértelmezés szerint engedélyezett funkcióval rendelkezik. Bizonyos forgatókönyvek és topológiák esetén ezen funkciók némelyike további konfigurációs beállítások megadását igényli.

[Szűrés](how-to-connect-sync-configure-filtering.md) használatára akkor van szükség, ha korlátozni kívánja az Azure AD-vel szinkronizálandó objektumok körét. Alapértelmezés szerint valamennyi felhasználó, névjegy és Windows 10 rendszert használó számítógép szinkronizálva van. A szűrési beállítások tartományok, szervezeti egységek vagy attribútumok szerint módosíthatók.

A [jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) az Active Directory jelszókivonatát szinkronizálja az Azure AD-vel. A végfelhasználó ugyanazt a jelszót használhatja a helyszíni alkalmazásban és a felhőben, de csak az egyik helyen kezelheti. Mivel ez a funkció szolgáltatóként a helyszíni Active Directoryt használja, saját jelszóházirendjét is alkalmazhatja.

A [jelszóvisszaíró](../authentication/quickstart-sspr.md) szolgáltatás lehetővé teszi a felhasználók számára jelszavak módosítását és visszaállítását a felhőben, valamint a helyszíni jelszóházirend alkalmazását.

Az [eszközvisszaíró](how-to-connect-device-writeback.md) szolgáltatás lehetővé teszi egy regisztrált Azure AD-eszköz visszaírását a helyszíni Active Directoryra, így ott feltételes hozzáféréssel használható.

A [véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) szolgáltatás alapértelmezés szerint be van kapcsolva, és a felhőcímtárat védi az egy időben végrehajtott többszörös törlésektől. Alapértelmezés szerint futtatásonként 500 törlést tesz lehetővé. Ezt a beállítást szervezetének mérete alapján módosíthatja.

Az [automatikus frissítés](how-to-connect-install-automatic-upgrade.md) szolgáltatás alapértelmezés szerint engedélyezve van a gyorsbeállításokkal végrehajtott telepítéseknél, és gondoskodik arról, hogy az Azure AD Connect mindig a legújabb kiadásra frissüljön.

### <a name="next-steps-to-configure-sync-features"></a>A szinkronizálási funkciók konfigurálásának következő lépései
|Témakör |Hivatkozás|  
| --- | --- |
|A szűrés konfigurálása | [Az Azure AD Connect szinkronizálása: A szűrés konfigurálása](how-to-connect-sync-configure-filtering.md)|
|Jelszókivonat szinkronizálása | [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
|Átmenő hitelesítés | [Átmenő hitelesítés](how-to-connect-pta.md)
|Jelszóvisszaíró | [A jelszókezelés első lépései](../authentication/quickstart-sspr.md)|
|Eszközvisszaíró | [Eszközvisszaírás engedélyezése az Azure AD Connectben](how-to-connect-device-writeback.md)|
|Véletlen törlések megakadályozása | [Az Azure AD Connect szinkronizálása: Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|Automatikus frissítés | [Azure AD Connect: Automatikus frissítés](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Az Azure AD Connect szinkronizálásának testreszabása
Az Azure AD Connect-szinkronizálás alapértelmezett konfigurációja a legtöbb ügyfél és topológia számára megfelelő. Mindig akadnak azonban olyan helyzetek, amikor az alapértelmezett konfiguráció nem használható, és módosítást igényel. A módosítások végrehajtása a jelen szakaszban és a hivatkozott témakörökben leírtak szerint támogatott.

Amennyiben korábban nem dolgozott szinkronizált topológiákkal, ismerkedjen meg a [technikai kulcsfogalmakkal](how-to-connect-sync-technical-concepts.md) foglalkozó részben leírt alapismeretekkel és kifejezésekkel. Az Azure AD Connect az MIIS2003, az ILM2007 és az FIM2010 továbbfejlesztése. Még ha egyes elemek azonosak is, számos módosításra került sor.

Az [alapértelmezett konfiguráció](concept-azure-ad-connect-sync-default-configuration.md) azt feltételezi, hogy egynél több erdő is lehet a konfigurációban. Ezekben a topológiákban a felhasználói objektumok kapcsolattartóként is megjelenhetnek egy másik erdőben. A felhasználók rendelkezhetnek hivatkozott postafiókkal is egy másik erőforráserdőben. Az alapértelmezett konfiguráció viselkedésének leírása a [felhasználókkal és kapcsolattartókkal](concept-azure-ad-connect-sync-user-and-contacts.md) foglalkozó részben található meg.

A szinkronizálás során alkalmazott konfigurációs modell neve: [deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md). A speciális attribútumfolyamok [függvényeket](reference-connect-sync-functions-reference.md) használnak az attribútumok gyors átalakításához. Az Azure AD Connectben elérhető eszközök segítségével a teljes konfiguráció megtekintésére és vizsgálatára van lehetőség. Amennyiben konfigurációs módosításokat kíván végrehajtani, támaszkodjon az [ajánlott eljárásokra](how-to-connect-sync-best-practices-changing-default-configuration.md), így könnyebben használatba veheti az új kiadásokat.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Az Azure AD Connect-szinkronizálás testreszabásának következő lépései
|Témakör |Hivatkozás|  
| --- | --- |
|Az Azure AD Connect-szinkronizáláshoz kapcsolódó összes cikk | [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)|
|Technikai kulcsfogalmak | [Az Azure AD Connect szinkronizálása: Műszaki fogalmak](how-to-connect-sync-technical-concepts.md)|
|Az alapértelmezett konfiguráció ismertetése | [Az Azure AD Connect szinkronizálása: Az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md)|
|A felhasználók és a kapcsolattartók ismertetése | [Az Azure AD Connect szinkronizálása: Understanding felhasználók és a kapcsolattartók](concept-azure-ad-connect-sync-user-and-contacts.md)|
|Deklaratív kiépítés | [Az Azure AD Connect szinkronizálása: Deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Az alapértelmezett konfiguráció módosítása | [Az alapértelmezett konfiguráció módosításának ajánlott eljárásai](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Az összevonási funkciók konfigurálása

Az Azure AD Connect több olyan szolgáltatást is kínál, amelyek segítségével az Azure AD összevonási szolgáltatás egyszerűbben alkalmazható az AD FS használatával, valamint egyszerűbben kezelhető az összevonási megbízhatósági kapcsolat. Az Azure AD Connect az AD FS szolgáltatást a Windows Server 2012 R2-es és azt követő verzióin támogatja.

Akkor is [frissítse az AD FS-farm SSL-tanúsítványát](how-to-connect-fed-ssl-update.md), ha nem az Azure AD Connect használatával kezeli az összevonási megbízhatósági kapcsolatot.

Szükség szerint bővítse a farmot [egy AD FS-kiszolgáló hozzáadásával](how-to-connect-fed-management.md#addadfsserver).

Az Azure AD használatával néhány kattintással [hozza helyre a megbízhatósági kapcsolatot](how-to-connect-fed-management.md#repairthetrust).

Az ADFS [több tartomány](how-to-connect-install-multiple-domains.md) támogatására is konfigurálható. Előfordulhat például, hogy több legfelső szintű tartományt kell használnia az összevonáshoz.

Ha az ADFS-kiszolgáló még nem lett konfigurálva az Azure AD-tanúsítványok automatikus frissítésére, vagy ha nem ADFS rendszerű megoldást alkalmaz, értesítést kap, amikor [frissítenie kell a tanúsítványokat](how-to-connect-fed-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Az összevonási funkciók konfigurálásának következő lépései
|Témakör |Hivatkozás|  
| --- | --- |
|Minden AD FS-cikk | [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)|
|Az ADFS konfigurálása altartományokkal | [Többtartományos támogatás az Azure AD összevonási szolgáltatásához](how-to-connect-install-multiple-domains.md)|
|AD FS-farm kezelése | [Az AD FS kezelése és testreszabása az Azure AD Connect segítségével](how-to-connect-fed-management.md)|
|Összevonási tanúsítványok manuális frissítése | [Az Office 365 és az Azure AD összevonási tanúsítványainak megújítása](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Az Azure AD Connect Health használatának első lépései
Az Azure AD Connect Health használatának elkezdéséhez hajtsa végre a következő lépéseket:

1. [Szerezze be a Prémium szintű Azure AD-t](../fundamentals/active-directory-get-started-premium.md) vagy [indítson egy próbaverziót](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Töltse le és telepítse az Azure AD Connect Health-ügynököket](#download-and-install-azure-ad-connect-health-agent) az identitás-kiszolgálókra.
3. Az Azure AD Connect Health irányítópultját a következő címen tekintheti meg: [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Ne feledje, hogy mielőtt bármilyen adatot megtekinthetne az Azure AD Connect Health irányítópultján, telepítenie kell az Azure AD Connect Health-ügynököket a célkiszolgálókon.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Az Azure AD Connect Health-ügynök letöltése és telepítése
* Győződjön meg róla, hogy [teljesülnek az Azure AD Connect Health követelményei](how-to-connect-health-agent-install.md#requirements).
* Ismerkedés az Azure AD Connect Health for AD FS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD FS szolgáltatáshoz.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Tekintse meg a telepítési utasításokat](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Ismerkedés az Azure AD Connect Health szinkronizálási szolgáltatás használatával
    * [Töltse le, és telepítse az Azure AD Connect legújabb verzióját.](https://go.microsoft.com/fwlink/?linkid=615771) A szinkronizálási állapotügynök az (1.0.9125.0-s verziójú vagy újabb) Azure AD Connect részeként telepíthető.
* Ismerkedés az Azure AD Connect Health for AD DS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD DS szolgáltatáshoz](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Tekintse meg a telepítési utasításokat](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Az Azure AD Connect Health portál
Az Azure AD Connect Health portálon riasztásokat, teljesítményfigyelési adatokat és használatelemzési információkat tekinthet meg. Az Azure AD Connect Health fő panelje a https://aka.ms/aadconnecthealth URL-címen található. A panelek az ablakoknak megfelelő funkciót töltenek be. A fő panelen a **Gyors üzembe helyezés**, az Azure AD Connect Health szolgáltatásai és további konfigurációs lehetőségek láthatók. Tekintse meg az alábbi képernyőfelvételt, valamint az azt követő rövid magyarázatot. Az ügynökök üzembe helyezése után az állapotfigyelő szolgáltatás automatikusan azonosítja az Azure AD Connect Health által monitorozott szolgáltatásokat.

> [!NOTE]
> A licencelési információkért lásd: [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md) vagy [Az Azure AD díjszabási oldala](https://aka.ms/aadpricing).
    
![Az Azure AD Connect Health portál](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Gyors üzembe helyezési**: Ha ezt a beállítást, a **gyors üzembe helyezés** panel nyílik meg. A **Get Tools** (Eszközök beszerzése) lehetőség kiválasztásával letöltheti az Azure AD Connect Health-ügynököt. Emellett hozzáférhet a dokumentációkhoz, és visszajelzést is küldhet.
* **Az Azure Active Directory Connect (sync)**: Ez a lehetőség az Azure AD Connect Health által aktuálisan figyelt Azure AD Connect-kiszolgálók mutatja. A **Szinkronizálási hibák** bejegyzés az első bevezetett szinkronizálási szolgáltatása alapszintű szinkronizálási hibáit jeleníti meg kategóriák szerint. Ha kiválasztja a **Szinkronizálási szolgáltatások** bejegyzést, a megnyíló panel az Azure AD Connect-kiszolgálókra vonatkozó információkat tartalmazza. További információ ezekről a képességekről: [Az Azure AD Connect Health szinkronizálási szolgáltatás használata az AD FS szolgáltatással](how-to-connect-health-sync.md).
* **Active Directory összevonási szolgáltatások**: Ez a lehetőség az Azure AD Connect Health által aktuálisan figyelt összes AD FS szolgáltatást mutatja. Ha kiválaszt egy példányt, a megnyíló panel az adott szolgáltatáspéldányra vonatkozó információkat tartalmazza. Az információk között tulajdonságokat, riasztásokat, megfigyelési adatokat, használatelemzést és egy áttekintést talál. További információ ezekről a képességekről: [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md).
* **Active Directory Domain Services**: Ez a lehetőség az Azure AD Connect Health által aktuálisan figyelt összes AD DS-erdőt mutatja. Ha kiválaszt egy erdőt, a megnyíló panel az adott erdőre vonatkozó információkat tartalmazza. Ezek az adatok a következők áttekintését teszik lehetővé: lényeges információk, tartományvezérlő irányítópult, replikáció állapota irányítópult, riasztások és figyelés. További információ ezekről a képességekről: [Az Azure AD Connect Health használata az AD DS szolgáltatással](how-to-connect-health-adds.md).
* **Konfigurálása**: Ez a szakasz az alábbi funkciók be- és kikapcsolása lehetőséget kínál:

  - A **Beállítások** bejegyzésben megtalálhatók az ügynökei alapszintű konfigurációi is. Az automatikus frissítési beállítás lehetővé teszi, hogy az Azure AD Connect Health-ügynök automatikus frissítése a legújabb verzióra: Automatikusan frissül az Azure AD Connect Health-ügynök legújabb verziójára, ha elérhetővé válnak. Ez e beállítás alapértelmezés szerint engedélyezve van. Az Azure AD-címtár állapotadatok hibaelhárítási céllal Microsoft hozzáférés engedélyezése: Ha ez engedélyezve van, a Microsoft ugyanazokat az adatokat láthatja, amelyeket látható. Ez az információ a hibaelhárítás és a problémakezelés során jelenthet segítséget. A beállítás alapértelmezés szerint le van tiltva.
* A **Szerepköralapú hozzáférés-vezérlés (IAM)** szakasz ismerteti a Connect Health adatokhoz való hozzáférés felügyeletét szerepkör alapban. 

## <a name="next-steps"></a>További lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Jelszókivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|
- [Átmenő hitelesítés](how-to-connect-pta.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
- [Az Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
