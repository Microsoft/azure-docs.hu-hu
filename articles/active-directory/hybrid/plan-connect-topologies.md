---
title: 'Azure AD Connect: támogatott topológiák | Microsoft Docs'
description: Ez a témakör a Azure AD Connect támogatott és nem támogatott topológiáit részletezi.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359832"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect-topológiák
Ez a cikk ismerteti a különböző helyszíni és Azure Active Directory (Azure AD) topológiákat, amelyek a Azure AD Connect szinkronizálást használják kulcsfontosságú integrációs megoldásként. Ebben a cikkben a támogatott és a nem támogatott konfigurációk is szerepelnek.


A cikkben található képek jelmagyarázata:

| Description | Szimbólum |
| --- | --- |
| Helyszíni Active Directory erdő |![Helyszíni Active Directory erdő](./media/plan-connect-topologies/legendad1.png) |
| Helyszíni Active Directory szűrt importálással |![Active Directory szűrt importálással](./media/plan-connect-topologies/legendad2.png) |
| Azure AD Connect szinkronizáló kiszolgáló |![Azure AD Connect szinkronizáló kiszolgáló](./media/plan-connect-topologies/legendsync1.png) |
| Azure AD Connect szinkronizálási kiszolgáló "átmeneti üzemmód" |![Azure AD Connect szinkronizálási kiszolgáló "átmeneti üzemmód"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync Forefront Identity Manager (FIM) 2010 vagy Microsoft Identity Manager (webalkalmazások) 2016 |![GALSync FIM 2010 vagy a webcímen 2016](./media/plan-connect-topologies/legendsync3.png) |
| Azure AD Connect szinkronizáló kiszolgáló, részletes |![Azure AD Connect szinkronizáló kiszolgáló, részletes](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Nem támogatott forgatókönyv |![Nem támogatott forgatókönyv](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> A Microsoft nem támogatja az olyan konfigurációkon vagy műveleteken kívüli Azure AD Connect-szinkronizálások módosítását vagy működtetését, amelyek hivatalosan dokumentálva vannak. Ezen konfigurációk vagy műveletek bármelyike inkonzisztens vagy nem támogatott állapotba Azure AD Connect szinkronizálást eredményezhet. Ennek eredményeképpen a Microsoft nem tud technikai támogatást biztosítani az ilyen üzemelő példányokhoz.


## <a name="single-forest-single-azure-ad-tenant"></a>Egyetlen erdő, egyetlen Azure AD-bérlő
![Egyetlen erdő és egyetlen bérlő topológiája](./media/plan-connect-topologies/singleforestsingledirectory.png)

A leggyakoribb topológia egyetlen helyszíni erdő, amely egy vagy több tartománnyal és egyetlen Azure AD-Bérlővel rendelkezik. Azure AD-hitelesítés esetén a rendszer jelszó-kivonatolási szinkronizálást használ. A Azure AD Connect expressz telepítése csak ezt a topológiát támogatja.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Egyetlen erdő, több szinkronizáló kiszolgáló egyetlen Azure AD-bérlőhöz
![Nem támogatott, szűrt topológia egyetlen erdőhöz](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Ha több Azure AD Connect szinkronizálási kiszolgáló csatlakozik ugyanahhoz az Azure AD-bérlőhöz, az [átmeneti kiszolgáló](#staging-server)kivételével nem támogatott. Nem támogatott, még akkor is, ha ezek a kiszolgálók kölcsönösen exkluzív objektumokkal való szinkronizálásra vannak konfigurálva. Elképzelhető, hogy ezt a topológiát úgy tekinti, hogy egyetlen kiszolgálóról nem tudja elérni az erdő összes tartományát, vagy ha több kiszolgáló között szeretne terhelést terjeszteni.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Több erdő, egyetlen Azure AD-bérlő
![Több erdő és egyetlen bérlő topológiája](./media/plan-connect-topologies/multiforestsingledirectory.png)

Számos szervezet több helyszíni Active Directory erdővel rendelkező környezettel rendelkezik. A helyszíni Active Directory erdők több okból is megtalálhatók. A tipikus példák a fiók-erőforrás erdőkkel és az egyesítés vagy a beszerzés eredményével kapcsolatos tervek.

Ha több erdővel rendelkezik, minden erdőnek elérhetőnek kell lennie egyetlen Azure AD Connect szinkronizáló kiszolgálóval. A kiszolgálónak csatlakoznia kell egy tartományhoz. Ha az összes erdőt el szeretné érni, helyezze a kiszolgálót egy peremhálózati hálózatba (más néven DMZ, vagy demilitarizált Zone és screened subnet).

A Azure AD Connect telepítővarázsló számos lehetőséget kínál a több erdőben képviselt felhasználók konszolidálására. A cél az, hogy egy felhasználó csak egyszer képviselteti magát az Azure AD-ben. Vannak olyan gyakori topológiák, amelyeket a telepítési varázslóban a Custom telepítési útvonalon lehet konfigurálni. A **felhasználók egyedi azonosítása** lapon válassza ki a topológiát jelölő megfelelő beállítást. A konszolidáció csak a felhasználók számára van konfigurálva. A duplikált csoportok nincsenek összevonva az alapértelmezett konfigurációval.

A gyakori topológiákat a különálló topológiákkal, a [teljes hálóval](#multiple-forests-full-mesh-with-optional-galsync)és [a fiók – erőforrás topológiával](#multiple-forests-account-resource-forest)kapcsolatos szakaszokban tárgyaljuk.

Azure AD Connect szinkronizálás alapértelmezett konfigurációja feltételezi:

* Minden felhasználónak csak egy engedélyezett fiókja van, és az az erdő, ahol ez a fiók található, a felhasználó hitelesítésére szolgál. Ez feltételezi a jelszó-kivonatok szinkronizálását, az átmenő hitelesítést és az összevonást. A UserPrincipalName és a sourceAnchor/immutableID ebből az erdőből származnak.
* Minden felhasználó csak egy postaládával rendelkezik.
* A felhasználó postaládáját futtató erdő a legjobb adatminőséggel rendelkezik az Exchange globális címlistában (GAL) látható attribútumok számára. Ha nincs postaláda a felhasználó számára, akkor bármely erdő felhasználható ezen attribútumok értékének a kiszolgálása érdekében.
* Ha csatolt postaládával rendelkezik, akkor a bejelentkezéshez használt másik erdőben is van egy fiók.

Ha a környezete nem felel meg ezeknek a feltételezéseknek, a következő dolgok történnek:

* Ha egynél több aktív vagy egynél több postaládával rendelkezik, a szinkronizálási motor kiválasztja az egyiket, és figyelmen kívül hagyja a másikat.
* Egy másik aktív fiókot nem tartalmazó csatolt postaládát nem exportál az Azure AD-ba. A felhasználói fiók nem tagja egyetlen csoportnak sem. A nem rSync-ben lévő csatolt postaláda mindig normál postaládának felel meg. Ez a változás szándékosan eltérő viselkedést nyújt a több erdős forgatókönyvek jobb támogatásához.

[Az alapértelmezett konfiguráció megismeréséhez](concept-azure-ad-connect-sync-default-configuration.md)további részleteket talál.

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Több erdő, több szinkronizálási kiszolgáló egyetlen Azure AD-bérlőhöz
![Több erdőhöz és több szinkronizálási kiszolgálóhoz nem támogatott topológia](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Az egyetlen Azure AD-bérlőhöz csatlakoztatott több Azure AD Connect szinkronizáló kiszolgáló használata nem támogatott. A kivétel egy [átmeneti kiszolgáló](#staging-server)használata.

Ez a topológia különbözik attól, hogy az egyes Azure AD-bérlőhöz csatlakozó **több szinkronizálási kiszolgáló** nem támogatott.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Több erdő, egyetlen szinkronizálási kiszolgáló, a felhasználók csak egy címtárban jelennek meg
![A felhasználókat csak egyszer ábrázoló beállítás az összes címtárban](./media/plan-connect-topologies/multiforestusersonce.png)

![Több erdő és különböző topológiák ábrázolása](./media/plan-connect-topologies/multiforestseparatetopologies.png)

Ebben a környezetben az összes helyszíni erdő külön entitásként lesz kezelve. Egy másik erdőben sem található felhasználó. Minden erdő saját Exchange-szervezettel rendelkezik, és nincs GALSync az erdők között. Ez a topológia lehet az egyesítés/beszerzés vagy egy olyan szervezet esetében, amelyben az egyes üzleti egységek egymástól függetlenül működnek. Ezek az erdők ugyanabban a szervezetben találhatók az Azure AD-ben, és egyesített GAL-vel jelennek meg. Az előző ábrán az egyes erdőkben lévő összes objektum a metaverse-ben szerepel, és a cél Azure AD-bérlőn összesítve jelenik meg.

### <a name="multiple-forests-match-users"></a>Több erdő: a felhasználók egyeztetése
Az ilyen forgatókönyvek esetében gyakori, hogy a terjesztési és biztonsági csoportok felhasználók, névjegyek és külső rendszerbiztonsági tag (FSP-EK) kombinációját is tartalmazhatják. Active Directory Domain Services (AD DS) FSP használják a biztonsági csoportban lévő más erdők tagjainak ábrázolására. Az összes FSP az Azure AD-ben lévő valódi objektumra lesz feloldva.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Több erdő: teljes háló opcionális GALSync
![A mail attribútum használatának lehetősége a különböző címtárakban található felhasználói identitások egyeztetéséhez](./media/plan-connect-topologies/multiforestusersmail.png)

![Teljes szembőségű topológia több erdőhöz](./media/plan-connect-topologies/multiforestfullmesh.png)

A teljes szembőségű topológia lehetővé teszi, hogy a felhasználók és az erőforrások bármely erdőben legyenek elhelyezve. Általában kétirányú megbízhatósági kapcsolat áll fenn az erdők között.

Ha az Exchange egynél több erdőben található, lehet, hogy (opcionálisan) helyszíni GALSync-megoldást is tartalmaz. Ezután minden felhasználó a többi erdőben található partnerként jelenik meg. A GALSync általában a FIM 2010 vagy a webszolgáltatási 2016 használatával valósítható meg. Azure AD Connect nem használható helyszíni GALSync.

Ebben az esetben az Identity Objects a mail attribútumon keresztül csatlakozik. Az egyik erdőben postaládával rendelkező felhasználók a többi erdőben lévő partnerekkel csatlakoznak.

### <a name="multiple-forests-account-resource-forest"></a>Több erdő: fiók – erőforrás erdő
![A ObjectSID és a msExchMasterAccountSID attribútumok használatának lehetősége a több címtárban található identitások egyeztetéséhez](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Fiók – erőforrás-erdő topológiája több erdőhöz](./media/plan-connect-topologies/multiforestaccountresource.png)

A fiók-erőforrás erdő topológiájában egy vagy több, aktív felhasználói fiókkal rendelkező *fiók* -erdő található. Egy vagy több, letiltott fiókkal rendelkező *erőforrás* -erdővel is rendelkezik.

Ebben a forgatókönyvben egy (vagy több) erőforrás-erdő megbízhatónak tekinti az összes fiók erdőjét. Az erőforrás-erdő általában kiterjesztett Active Directory sémával rendelkezik az Exchange és a Lync szolgáltatással. Az összes Exchange-és Lync-szolgáltatás, valamint más megosztott szolgáltatások is ebben az erdőben találhatók. A felhasználók letiltott felhasználói fiókkal rendelkeznek ebben az erdőben, és a postaláda a fiók erdőhöz van csatolva.

## <a name="microsoft-365-and-topology-considerations"></a>Microsoft 365-és topológiai megfontolások
Egyes Microsoft 365 munkaterhelések bizonyos korlátozásokkal rendelkeznek a támogatott topológiákkal kapcsolatban:

| Számítási feladat | Korlátozások |
| --------- | --------- |
| Exchange Online | További információ az Exchange Online által támogatott hibrid topológiákkal kapcsolatban: [több Active Directory-erdővel rendelkező hibrid telepítések](/Exchange/hybrid-deployment/hybrid-with-multiple-forests). |
| Skype Vállalati verzió | Ha több helyszíni erdőt használ, csak a fiók – erőforrás erdő topológiája támogatott. További információ: [a Skype for Business Server 2015 környezeti követelményei](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Ha nagyobb szervezet, akkor érdemes megfontolnia a [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) funkció használatát. Lehetővé teszi annak meghatározását, hogy a felhasználók mely adatközpont-régiókban találhatók.

## <a name="staging-server"></a>Átmeneti kiszolgáló
![Átmeneti kiszolgáló a topológiában](./media/plan-connect-topologies/multiforeststaging.png)

Azure AD Connect támogatja a második kiszolgáló *átmeneti módban* való telepítését. Az ebben a módban lévő kiszolgálók beolvasják az összes csatlakoztatott könyvtár adatait, de nem írnak semmit a csatlakoztatott címtárakba. A normál szinkronizálási ciklust használja, ezért az azonosító adatok frissített másolatával rendelkezik.

Abban az esetben, ha az elsődleges kiszolgáló meghibásodik, feladatátvételt hajthat végre az átmeneti kiszolgálóval. Ezt a Azure AD Connect varázslóban teheti meg. Ez a második kiszolgáló egy másik adatközpontban is található, mert az elsődleges kiszolgálóval egyetlen infrastruktúra sincs megosztva. Manuálisan kell átmásolnia az elsődleges kiszolgálón végrehajtott konfigurációs módosításokat a második kiszolgálóra.

Egy átmeneti kiszolgáló használatával tesztelheti az új egyéni konfigurációt, és megadhatja, hogy milyen hatással van az adataira. Megtekintheti a módosításokat, és módosíthatja a konfigurációt. Ha elégedett az új konfigurációval, elvégezheti az átmeneti kiszolgálót az aktív kiszolgálón, és beállíthatja a régi aktív kiszolgálót átmeneti módba.

Ezzel a módszerrel is lecserélheti az aktív szinkronizáló kiszolgálót. Készítse elő az új kiszolgálót, és állítsa átmeneti módba. Győződjön meg arról, hogy jó állapotban van, tiltsa le az átmeneti üzemmódot (ami aktív), és állítsa le a jelenleg aktív kiszolgálót.

Több átmeneti kiszolgáló is lehet, ha több biztonsági mentést szeretne készíteni különböző adatközpontokban.

## <a name="multiple-azure-ad-tenants"></a>Több Azure AD-bérlő
Javasoljuk, hogy egyetlen bérlőt az Azure AD-ben egy szervezet számára.
Mielőtt több Azure AD-bérlőt szeretne használni, tekintse meg a [felügyeleti egységek kezelése az Azure ad-ben](../roles/administrative-units.md)című cikket. Olyan gyakori forgatókönyveket tartalmaz, amelyekben egyetlen bérlőt használhat.

![Topológia több erdőhöz és több bérlőhöz](./media/plan-connect-topologies/multiforestmultidirectory.png)

1:1 kapcsolat van egy Azure AD Connect szinkronizáló kiszolgáló és egy Azure AD-bérlő között. Minden egyes Azure AD-bérlőhöz egy Azure AD Connect szinkronizáló kiszolgáló telepítésére van szükség. Az Azure AD-bérlői példányok elkülönítve vannak a kialakítással. Ez azt eredményezi, hogy az egyik bérlő felhasználói nem látják a másik bérlő felhasználóit. Ha ezt az elkülönítést szeretné használni, ez egy támogatott konfiguráció. Ellenkező esetben az egyetlen Azure AD-bérlői modellt kell használnia.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Minden objektum csak egyszer egy Azure AD-bérlőben
![Egyetlen erdő szűrt topológiája](./media/plan-connect-topologies/singleforestfiltered.png)

Ebben a topológiában egy Azure AD Connect szinkronizáló kiszolgáló csatlakozik az egyes Azure AD-bérlőhöz. A Azure AD Connect szinkronizálási kiszolgálókat úgy kell konfigurálni, hogy az egyes objektumok kölcsönösen kizárják egymást a működéséhez. Például az egyes kiszolgálók hatókörét egy adott tartományhoz vagy szervezeti egységhez használhatja.

Egy DNS-tartomány csak egyetlen Azure AD-bérlőben regisztrálható. A helyszíni Active Directory-példány felhasználói UPN-példányainak külön névtereket is használniuk kell. Az előző képen például három különálló UPN-utótag van regisztrálva a helyszíni Active Directory-példányban: contoso.com, fabrikam.com és wingtiptoys.com. A helyszíni Active Directory tartományban lévő felhasználók eltérő névteret használnak.

>[!NOTE]
>A globális címlista szinkronizálása (GalSync) nem történik meg automatikusan ebben a topológiában, és további egyéni többkiszolgálós implementációra van szükség annak biztosításához, hogy minden bérlő teljes globális címlistában (GAL) legyen az Exchange Online és a Skype vállalati online verzióban.


Ez a topológia a következő korlátozásokkal rendelkezik a más támogatott helyzetekben:

* Legfeljebb 5 Azure Active Directory bérlő lehet a helyszíni Active Directory-példánnyal rendelkező Exchange Hybrid. Ez a forgatókönyv a [hibrid konfigurációs varázsló 2020](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698)-es verziójának frissítése című cikkben található.
* A hibrid konfigurációs varázslót futtató Exchange Servernek 2016 CU18 vagy 2019 CU7 vagy újabb verziójúnak kell lennie.
* Minden Azure AD Connect példánynak egy tartományhoz csatlakoztatott gépen kell futnia.
* A Azure AD Connect a tartomány/OU szűrés beállítással kell konfigurálni a felhasználók helyszíni címtárból történő szűréséhez. Ezzel a beállítással biztosíthatja, hogy a felhasználók csak egyetlen online Exchange-bérlőben jelenjenek meg.
* A Windows 10-es eszközökhöz csak egy Azure AD-bérlő társítható.
* Az egyszeri bejelentkezési (SSO) beállítás a jelszó-kivonatolási szinkronizáláshoz és az átmenő hitelesítéshez csak egy Azure AD-Bérlővel használható.

A kölcsönösen kizárható objektumokra vonatkozó követelmények a visszaírási is érvényesek. Egyes visszaírási-funkciók nem támogatottak ehhez a topológiához, mert egyetlen helyszíni konfigurációt feltételeznek. Ezek a funkciók a következők:

* A csoport visszaírási alapértelmezett konfigurációval.
* Eszköz visszaírási.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Minden objektum többször egy Azure AD-bérlőben
![Egyetlen erdőhöz és több bérlőhöz nem támogatott topológia](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Egyetlen erdőhöz és több összekötőhöz nem támogatott topológia](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Ezek a feladatok nem támogatottak:

* Ugyanaz a felhasználó több Azure AD-Bérlővel is szinkronizálható.
* Végezze el a konfiguráció módosítását, hogy az egyik Azure AD-bérlőben lévő felhasználók névjegyként jelenjenek meg egy másik Azure AD-bérlőben.
* Módosítsa Azure AD Connect szinkronizálást több Azure AD-bérlőhöz való kapcsolódáshoz.

### <a name="galsync-by-using-writeback"></a>GALSync a visszaírási használatával
![Nem támogatott topológia több erdőhöz és több címtárhoz, az Azure AD-vel való GALSync összpontosítva](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Nem támogatott topológia több erdőhöz és több könyvtárhoz, a helyszíni GALSync összpontosítva Active Directory](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Az Azure AD-bérlőket a kialakítás elkülöníti. Ezek a feladatok nem támogatottak:

* Módosítsa Azure AD Connect szinkronizálás konfigurációját egy másik Azure AD-bérlő adatainak beolvasásához.
* A felhasználókat névjegyekként exportálhatja egy másik helyszíni Active Directory-példányba Azure AD Connect Sync használatával.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync helyszíni szinkronizáló kiszolgálóval
![GALSync több erdő és több könyvtár topológiájában](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

A helyszíni FIM 2010 2016 vagy a GALSync segítségével szinkronizálhatja a felhasználókat a két Exchange-szervezet között. Az egyik szervezet felhasználói idegen felhasználóként vagy partnerként jelennek meg a másik szervezetben. Ezek a különböző helyszíni Active Directory példányok ezután szinkronizálhatók a saját Azure AD-bérlők használatával.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Jogosulatlan ügyfelek használata a Azure AD Connect háttér eléréséhez
![Jogosulatlan ügyfelek használata a Azure AD Connect háttér eléréséhez](./media/plan-connect-topologies/other-client-unsupported.png)

A Azure Active Directory Connect-kiszolgáló a Azure Active Directory Connect háttér használatával kommunikál a Azure Active Directoryokkal. Az ezzel a háttérrel való kommunikációhoz használható egyetlen szoftver Azure Active Directory Connect. Nem támogatott a Azure Active Directory Connect háttérrel való kommunikáció bármely más szoftver vagy módszer használatával. 

## <a name="next-steps"></a>Következő lépések
Ha meg szeretné tudni, hogyan telepítheti Azure AD Connect a forgatókönyvekhez, tekintse meg a [Azure ad Connect egyéni telepítését](how-to-connect-install-custom.md)ismertető témakört.

További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ a helyszíni [identitások és a Azure Active Directory integrálásáról](whatis-hybrid-identity.md).
