---
title: Hibrid identitások tervezése – adatvédelmi stratégia az Azure-ban | Microsoft Docs
description: Megadhatja a hibrid identitási megoldás adatvédelmi stratégiáját, hogy megfeleljen az Ön által meghatározott üzleti követelményeknek.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703852"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Adatvédelmi stratégia definiálása a hibrid személyazonossági megoldáshoz
Ebben a feladatban megadhatja a hibrid identitási megoldás adatvédelmi stratégiáját, hogy megfeleljen az Ön által meghatározott üzleti követelményeknek:

* [Adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Adatvédelmi beállítások megadása
Ahogy azt a [címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)című részben leírtak szerint, Microsoft Azure ad szinkronizálhatók a helyszíni Active Directory Domain Services (AD DS) használatával. Ez az integráció lehetővé teszi, hogy a szervezetek az Azure AD használatával ellenőrizzék a felhasználók hitelesítő adatait, amikor a vállalati erőforrásokhoz próbálnak hozzáférni. Ezt mindkét esetben használhatja: a helyszíni és a felhőben tárolt adatok. Az Azure AD-beli adathozzáféréshez felhasználói hitelesítésre van szükség a biztonsági jogkivonat-szolgáltatás (STS) használatával.

A hitelesítés után az egyszerű felhasználónevet (UPN) a rendszer beolvassa a hitelesítési jogkivonatból. Ezt követően az engedélyezési rendszer meghatározza a felhasználó tartományának megfelelő replikált partíciót és tárolót. A felhasználó létezésére, engedélyezett állapotára és szerepkörére vonatkozó információk alapján az engedélyezési rendszer eldönti, hogy jogosult-e a cél bérlőhöz való hozzáférés a felhasználó számára az adott munkamenetben. Bizonyos felhatalmazott műveletek (konkrétan a felhasználói és a jelszó-visszaállítás létrehozása) létrehozhatnak egy olyan naplózási nyomvonalat, amelyet a bérlői rendszergazda a megfelelőségi erőfeszítések vagy nyomozások kezeléséhez használ.

Az adatok a helyszíni adatközpontból az Azure Storage-ba való áthelyezése az adatmennyiség, a sávszélesség rendelkezésre állása vagy egyéb megfontolások miatt nem mindig valósítható meg. Az [Azure Storage import/export szolgáltatás](../../import-export/storage-import-export-service.md) hardveres lehetőséget kínál a nagy mennyiségű adatok blob Storage-tárolóban történő elhelyezésére és lekérésére. Lehetővé teszi a [BitLocker által titkosított merevlemez-](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) meghajtók közvetlen küldését egy Azure-adatközpontba, ahol a Felhőbeli kezelők feltöltik a tartalmat a Storage-fiókjába, vagy letölthetik az Azure-adatait a meghajtóra, hogy visszatérjenek Önnek. Ehhez a folyamathoz csak titkosított lemezek lesznek elfogadva (a feladatok telepítése során a szolgáltatás által létrehozott BitLocker-kulcs használatával). A BitLocker-kulcsot külön nyújtják az Azure-hoz, így biztosítva a sávon kívüli kulcsok megosztását.

Mivel a tranzitban lévő adatok különböző forgatókönyvekben is megkerülhetnek, fontos tudni, hogy Microsoft Azure [virtuális hálózatkezelést](https://azure.microsoft.com/documentation/services/virtual-network/) használ a bérlők forgalmának elkülönítésére, olyan mértékeket alkalmazva, mint a gazdagép-és a vendég szintű tűzfalak, az IP-csomagszűrés, a portok blokkolása és a https-végpontok. Azonban a legtöbb Azure belső kommunikációja, beleértve az infrastruktúra-infrastruktúra és az infrastruktúra-ügyfél (helyszíni) is, titkosítva van. Egy másik fontos forgatókönyv az Azure-adatközpontokon belüli kommunikáció; A Microsoft felügyeli a hálózatokat annak biztosítására, hogy egyetlen virtuális gép sem megszemélyesítheti vagy hallgatózik egy másik IP-címen. A TLS/SSL használata az Azure Storage-hoz vagy SQL-adatbázisokhoz való hozzáféréskor, illetve Cloud Serviceshoz való csatlakozáskor használatos. Ebben az esetben az ügyfél rendszergazdája a TLS/SSL-tanúsítvány beszerzéséhez és a bérlői infrastruktúrához való üzembe helyezéséhez felelős. A Virtual Machines közötti adatforgalom ugyanabban az üzemelő példányban, illetve a bérlők között, az Microsoft Azure Virtual Network használatával a titkosított kommunikációs protokollok, például a HTTPS, az SSL/TLS vagy más rendszerek is védhetők.

Attól függően, hogy hogyan válaszolt a kérdésekre az adatvédelmi [követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)során, meg kell tudnia határozni, hogyan kívánja biztosítani az adatok védelmét, és hogy a hibrid identitási megoldás hogyan segíthet Önnek az adott folyamaton. A következő táblázat az Azure által támogatott, az egyes adatvédelmi forgatókönyvekhez elérhető lehetőségeket mutatja be.

| Adatvédelmi beállítások | Nyugalmi állapotban a felhőben | Helyszíni pihenés | Átvitel közben |
| --- | --- | --- | --- |
| BitLocker meghajtótitkosítás |X |X | |
| Adatbázisok titkosítása SQL Server |X |X | |
| Virtuális gép – virtuális gép titkosítása | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Az egyes Azure-szolgáltatásoknak megfelelő minősítésekkel kapcsolatos további információkért tekintse meg a [megfelelőségi funkciót](https://azure.microsoft.com/support/trust-center/services/) [Microsoft Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/) .
> Mivel az adatvédelem lehetőségei többrétegű megközelítést használnak, a beállítások összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy az összes rendelkezésre álló beállítást kihasználja az egyes állapotokhoz.
>
>

## <a name="define-content-management-options"></a>Tartalomkezelési beállítások megadása

Az Azure AD egy hibrid identitás-infrastruktúra kezelésére való használatának egyik előnye, hogy a folyamat teljes mértékben átlátható a végfelhasználó szemszögéből. A felhasználó megpróbál hozzáférni egy megosztott erőforráshoz, az erőforrásnak hitelesítésre van szüksége, a felhasználónak hitelesítési kérelmet kell küldenie az Azure AD-nek a jogkivonat beszerzéséhez és az erőforráshoz való hozzáféréshez. Ez a teljes folyamat a háttérben, felhasználói beavatkozás nélkül történik. 

Az adatvédelemmel kapcsolatos fontos szervezetek általában az adatok besorolását igénylik a megoldáshoz. Ha a jelenlegi helyszíni infrastruktúra már használja az adatok besorolását, akkor az Azure AD-t használhatja a felhasználó identitásának fő tárházához. A helyszíni adatbesoroláshoz használt általános eszközt a Windows Server 2012 R2 [adatbesorolási eszközkészletének](/previous-versions/tn-archive/hh204743(v=technet.10)) nevezzük. Ez az eszköz segíthet a privát felhőben lévő fájlkiszolgálók adatainak azonosításában, besorolásában és védelmében. A feladat végrehajtásához a Windows Server 2012 [automatikus fájl besorolása](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) is használható.

Ha a szervezet nem rendelkezik adatbesorolással, de az érzékeny fájlokat a helyszíni új kiszolgálók hozzáadása nélkül kell védelemmel ellátnia, a Microsoft [Azure Rights Management szolgáltatást](/azure/information-protection/what-is-azure-rms)használhatja.  A Azure RMS titkosítási, identitási és engedélyezési házirendeket használ a fájlok és az e-mailek biztonságossá tételéhez, és több eszközön, például telefonokon, tablettákon és számítógépeken is működik. Mivel a Azure RMS egy felhőalapú szolgáltatás, nem kell explicit módon konfigurálnia a megbízhatóságokat más szervezetekkel, mielőtt megoszthatja velük a védett tartalmakat. Ha már rendelkezik Microsoft 365 vagy egy Azure AD-címtárral, a szervezetek közötti együttműködés automatikusan támogatott. Azt is megteheti, hogy csak azokat a címtár-attribútumokat szinkronizálja, amelyeknek Azure RMSnia kell a helyszíni Active Directory-fiókok közös identitásának támogatásához Azure Active Directory Synchronization Services (Azure AD-szinkronizáló) vagy Azure AD Connect használatával.

A tartalomkezelés létfontosságú része annak megértése, hogy ki fér hozzá az erőforráshoz, ezért az Identitáskezelés szempontjából fontos a részletes naplózási funkció. Az Azure AD több mint 30 napot biztosít, többek között a következőket:

* A szerepkör-tagság változásai (például: felhasználó hozzáadva a globális rendszergazdai szerepkörhöz)
* Hitelesítő adatok frissítése (pl. jelszó módosítása)
* Tartományi felügyelet (pl.: egyéni tartomány ellenőrzése, tartomány eltávolítása)
* Alkalmazások hozzáadása vagy eltávolítása
* Felhasználói felügyelet (pl.: Hozzáadás, eltávolítás, felhasználó frissítése)
* Licencek hozzáadása vagy eltávolítása

> [!NOTE]
> További információ az Azure-beli naplózási képességekről: [Microsoft Azure Security and Audit log Management](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) .
> Attól függően, hogy hogyan válaszolt a [tartalomkezelési követelmények meghatározására](plan-hybrid-identity-design-considerations-contentmgt-requirements.md), meg kell tudni határoznia, hogyan kívánja felügyelni a tartalmat a hibrid identitási megoldásban. Habár a 6. táblázatban szereplő összes beállítás képes az Azure AD integrálására, fontos meghatározni, hogy melyik az üzleti igényeknek megfelelő.
>
>

| Tartalomkezelési beállítások | Előnyök | Hátrányok |
| --- | --- | --- |
| Központi helyszíni (Active Directory Rights Management-kiszolgáló) |Az adatbesorolásért felelős kiszolgálói infrastruktúra teljes körű ellenőrzése <br> Beépített képesség a Windows Server rendszerben, nincs szükség további licencre vagy előfizetésre <br> Integrálható az Azure AD-vel hibrid forgatókönyvekben <br> Támogatja a tartalomvédelmi szolgáltatás (IRM) képességeit a Microsoft Online szolgáltatásaiban, például az Exchange Online-ban és a SharePoint Online-ban, valamint Microsoft 365 <br> A támogatja a helyszíni Microsoft Server-termékeket, például az Exchange Servert, a SharePoint Servert, valamint a Windows Servert és a fájl-besorolási infrastruktúrát (-t) futtató fájlkiszolgálók használatát. |Nagyobb karbantartás (naprakészen tarthatja a frissítéseket, a konfigurációt és a lehetséges frissítéseket), mivel a kiszolgáló tulajdonosa <br> Kiszolgálói infrastruktúra megkövetelése a helyszínen<br> Az Azure-funkciók natív módon való kihasználása |
| A felhőben központosított (Azure RMS) |Könnyebben kezelhető a helyszíni megoldáshoz képest <br> Integrálható AD DS hibrid forgatókönyv esetén <br>  Teljes mértékben integrált az Azure AD-vel <br> A szolgáltatás üzembe helyezéséhez nincs szükség helyszíni kiszolgálóra <br> Támogatja a helyszíni Microsoft-termékek, például az Exchange Server, a SharePoint, a Server és a Windows Servert futtató fájlkiszolgálók használatát, valamint a fájlok besorolását, az infrastruktúrát <br> A BYOK képességgel teljes mértékben vezérelheti a bérlői kulcsát. |A szervezetnek rendelkeznie kell egy, az RMS-t támogató felhőalapú előfizetéssel. <br> A szervezetnek rendelkeznie kell egy Azure AD-címtárral az RMS felhasználói hitelesítésének támogatásához |
| Hibrid (Azure RMS integrált, helyszíni Active Directory Rights Management-kiszolgálóval) |Ez a forgatókönyv a helyszíni és a Felhőbeli központi környezet előnyeit is összegzi. |A szervezetnek rendelkeznie kell egy, az RMS-t támogató felhőalapú előfizetéssel. <br> A szervezetnek rendelkeznie kell egy Azure AD-címtárral az RMS felhasználói hitelesítésének támogatásához. <br> Kapcsolatot igényel az Azure Cloud Service és a helyszíni infrastruktúra között |

## <a name="define-access-control-options"></a>Hozzáférés-vezérlési beállítások megadása
Az Azure AD-ben elérhető hitelesítési, engedélyezési és hozzáférés-vezérlési képességek kihasználásával lehetővé teheti, hogy a vállalat egy központi identitás-tárházat használjon, miközben lehetővé teszi a felhasználók és a partnerek számára az egyszeri bejelentkezés (SSO) használatát az alábbi ábrán látható módon:

![központosított felügyelet](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Központosított felügyelet és teljes körű integráció más címtárakkal

Azure Active Directory egyszeri bejelentkezést biztosít több ezer SaaS-alkalmazáshoz és helyszíni webalkalmazásokhoz. Tekintse meg az [Azure Active Directory összevonás-kompatibilitási listát: külső gyártótól származó identitás-szolgáltatók, amelyek segítségével az egyszeri bejelentkezést](how-to-connect-fed-compatibility.md) a Microsoft által tesztelt SSO harmadik féltől származó további részletekre lehet alkalmazni. Ez a funkció lehetővé teszi, hogy a szervezet különböző B2B-forgatókönyveket vezessen be az identitás-és hozzáférés-kezelés irányításával. A B2B tervezési folyamat során azonban fontos megérteni a partner által használt hitelesítési módszert, és ellenőrizni, hogy az Azure támogatja-e ezt a módszert. Az Azure AD jelenleg a következő módszereket támogatja:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokenek
* Tanúsítványok

> [!NOTE]
> az egyes protokollokkal és az Azure képességeivel kapcsolatos további részletekért olvassa el [Azure Active Directory hitelesítési protokollok](/previous-versions/azure/dn151124(v=azure.100)) című részt.
>
>

Az Azure AD-támogatással a mobil üzleti alkalmazások ugyanazt az egyszerű Mobile Services hitelesítési funkciót használhatják, amely lehetővé teszi, hogy az alkalmazottak a vállalati Active Directory hitelesítő adataikkal jelentkezzenek be a mobil alkalmazásaiba. Ezzel a szolgáltatással az Azure AD-t identitás-szolgáltatóként támogatja Mobile Services a már támogatott más identitás-szolgáltatók mellett (amelyek közé tartozik a Microsoft-fiókok, a Facebook-azonosító, a Google-azonosító és a Twitter-azonosító). Ha a helyszíni alkalmazások a vállalat AD DS található felhasználó hitelesítő adatait használják, a partnerek és a felhőből érkező felhasználók hozzáférésének transzparensnek kell lennie. Kezelheti a felhasználó feltételes hozzáférés-vezérlését (felhőalapú) webalkalmazásokhoz, webes API-hoz, a Microsoft Cloud Serviceshez, a harmadik féltől származó SaaS-alkalmazásokhoz és a natív (mobil) ügyfélalkalmazások számára, valamint a biztonság, a naplózás és a jelentéskészítés előnyeit egy helyen. Javasoljuk azonban, hogy a megvalósítást nem éles környezetben vagy korlátozott számú felhasználóval ellenőrizze.

> [!TIP]
> fontos megjegyezni, hogy az Azure AD-nek nincs Csoportházirenda, mivel AD DS rendelkezik. Az eszközökre vonatkozó szabályzat érvénybe léptetéséhez mobileszköz-kezelési megoldásra van szükség, például [Microsoft Intune](/mem/intune/).
>
>

Ha a felhasználó az Azure AD-vel lett hitelesítve, fontos, hogy kiértékelje a felhasználó által birtokolt hozzáférési szintet. A felhasználó által az erőforráson átadott hozzáférés szintje eltérő lehet. Míg az Azure AD további biztonsági réteget is hozzáadhat az egyes erőforrásokhoz való hozzáférés szabályozásával, vegye figyelembe, hogy maga az erőforrás is rendelkezhet külön hozzáférés-vezérlési listával, például a fájlkiszolgálón található fájlok hozzáférés-vezérlésével. A következő ábra összefoglalja a hibrid forgatókönyvekben használható hozzáférés-vezérlési szinteket:

![hozzáférés-vezérlés](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Az ábrán látható összes interakció az X. ábrán egy, az Azure AD által lefedett hozzáférés-vezérlési forgatókönyvet jelöl. Az alábbiakban az egyes forgatókönyvek leírását olvashatja:

1. A helyszínen üzemeltetett alkalmazásokhoz való feltételes hozzáférés: a Windows Server 2012 R2-vel való AD FS használatára konfigurált alkalmazásokhoz hozzáférési házirendekkel rendelkező regisztrált eszközöket használhat.

2. Access Control a Azure Portal: az Azure lehetővé teszi, hogy a portálhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával is szabályozhatja. Ez a módszer lehetővé teszi a vállalat számára, hogy korlátozza azon műveletek számát, amelyeket egy adott személy elvégezhet a Azure Portalban. Ha az Azure RBAC használatával szabályozza a portálhoz való hozzáférést, a rendszergazdák a következő hozzáférés-kezelési megközelítések segítségével delegálhatja a hozzáférést:

   - Csoport alapú szerepkör-hozzárendelés: a helyi Active Directoryról szinkronizálható Azure AD-csoportokhoz is hozzárendelhet hozzáférést. Így kihasználhatja a szervezete által a csoportok kezelésére szolgáló eszközök és folyamatok által létrehozott meglévő befektetéseket. Használhatja a prémium szintű Azure AD meghatalmazott csoportjának felügyeleti szolgáltatását is.
   - Beépített szerepkörök használata az Azure-ban: három szerepkör – tulajdonos, közreműködő és olvasó – használatával biztosíthatja, hogy a felhasználók és a csoportok csak a feladataik elvégzéséhez szükséges feladatokat tudják elvégezni.
   -  Az erőforrásokhoz való részletes hozzáférés: szerepköröket rendelhet hozzá felhasználókhoz és csoportokhoz egy adott előfizetéshez, erőforráscsoporthoz vagy egyéni Azure-erőforráshoz, például webhelyhez vagy adatbázishoz. Így biztosíthatja, hogy a felhasználók hozzáférhessenek az összes szükséges erőforráshoz, és ne férhessenek hozzájuk a nem szükséges erőforrásokhoz.

   > [!NOTE]
   > Ha alkalmazásait fejleszti, és testre szeretné szabni a hozzáférés-vezérlést, akkor az Azure AD-alkalmazás szerepkörei is használhatók az engedélyezéshez. Tekintse át ezt a [WebApp-RoleClaims-DotNet példát](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) arról, hogyan hozhatja létre az alkalmazást ennek a funkciónak a használatára.


3. Microsoft 365 alkalmazások feltételes hozzáférése Microsoft Intune használatával: az IT-rendszergazdák feltételes hozzáférésű eszközökre vonatkozó szabályzatokat hozhatnak létre a vállalati erőforrások biztonságossá tételéhez, ugyanakkor ugyanakkor lehetővé teszik, hogy a megfelelő eszközökön lévő információs szakemberek hozzáférjenek a szolgáltatásokhoz. 
  
4. Feltételes hozzáférés SaaS-alkalmazásokhoz: [Ez a funkció](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) lehetővé teszi az alkalmazáson belüli multi-Factor Authentication hozzáférési szabályok konfigurálását, valamint a nem megbízható hálózaton lévő felhasználók hozzáférésének letiltását. A multi-Factor Authentication szabályokat alkalmazhatja az alkalmazáshoz rendelt összes felhasználóra, vagy csak a megadott biztonsági csoportokban lévő felhasználókra. A felhasználók kizárják a multi-Factor Authentication követelményét, ha a szervezet hálózatán belüli IP-címről érik el az alkalmazást.

Mivel a hozzáférés-vezérlés lehetőségei többrétegű megközelítést használnak, a beállítások összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy az egyes forgatókönyvekhez elérhető összes lehetőség kihasználható az erőforrásokhoz való hozzáférés szabályozásához.

## <a name="define-incident-response-options"></a>Incidens-válasz beállításainak megadása
Az Azure AD a felhasználó tevékenységének figyelésével segítséget nyújt a környezet számára a lehetséges biztonsági kockázatok azonosítása érdekében. Az Azure AD-hozzáférési és-használati jelentések segítségével megtekintheti a szervezete címtárának integritását és biztonságát. Ezekkel az információkkal a rendszergazdák jobban meghatározhatják, hogy az esetleges biztonsági kockázatok milyen hatással lehetnek a kockázatok enyhítésére.  [Prémium szintű Azure ad-előfizetés](../fundamentals/active-directory-get-started-premium.md) olyan biztonsági jelentéseket tartalmaz, amelyek lehetővé teszik az adatok beszerzését. Az [Azure ad-jelentések](../reports-monitoring/overview-reports.md) a következőképpen vannak kategorizálva:

* **Anomália-jelentések**: olyan bejelentkezési eseményeket tartalmaz, amelyek rendellenesnek bizonyultak. A cél az, hogy tisztában legyen az ilyen tevékenységekkel, és lehetővé teszi annak meghatározását, hogy az esemény gyanús-e.
* **Integrált alkalmazási jelentés**: betekintést nyújt a Felhőbeli alkalmazások használatára a szervezetében. A Azure Active Directory több ezer Felhőbeli alkalmazással is rendelkezik integrációval.
* **Hibajelentések**: azok a hibák, amelyek a fiókok külső alkalmazásokba való kiépítés során fordulhatnak elő.
* **Felhasználó-specifikus jelentések**: egy adott felhasználó eszközének vagy bejelentkezési tevékenységének megjelenítése.
* **Tevékenységnaplók**: az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban, valamint a csoportos tevékenységek változásaira, valamint a jelszó-visszaállításra és a regisztrációs tevékenységre vonatkozó összes naplózott esemény rekordját tartalmazza.

> [!TIP]
> Egy másik jelentés, amely segíthet az incidensek megválaszolásában dolgozó csapatnál is, ha a [felhasználó kiszivárgott hitelesítő adatokat tartalmazó](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) jelentést készít. Ez a jelentés felfedi a kiszivárgott hitelesítő adatok listája és a bérlője közötti egyezéseket.
>


Az Azure AD-ben az incidensek megválaszolásának vizsgálatakor felhasználható egyéb fontos beépített jelentések a következők:

* **Jelszó-visszaállítási tevékenység**: adja meg a rendszergazdát, hogy betekintést nyújtson arról, hogyan használják aktívan a jelszó-visszaállítást a szervezeten belül.
* A **jelszó-visszaállítás regisztrációs tevékenysége**: betekintést biztosít a felhasználók számára a jelszó-visszaállítási módszerekhez, valamint a kiválasztott módszerekhez.
* **Csoportos tevékenység**: a hozzáférési panelen kezdeményezett csoport (például a hozzáadott vagy eltávolított felhasználók) változásainak előzményeit jeleníti meg.

A prémium szintű Azure AD alapvető jelentési képességein kívül az incidensekre adott válasz vizsgálati folyamata során is kihasználhatja a naplózási jelentés előnyeit, például a következő információk beszerzéséhez:

* A szerepkör-tagság változásai (például a globális rendszergazdai szerepkörhöz hozzáadott felhasználó)
* Hitelesítő adatok frissítései (például a jelszó módosítása)
* Tartományi felügyelet (például egyéni tartomány ellenőrzése, tartomány eltávolítása)
* Alkalmazások hozzáadása vagy eltávolítása
* Felhasználói felügyelet (például felhasználók hozzáadása, eltávolítása, frissítése)
* Licencek hozzáadása vagy eltávolítása

Mivel az incidensek válasza többrétegű megközelítést használ, a beállítások összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy az egyes forgatókönyvekhez rendelkezésre álló összes lehetőséget kihasználva az Azure AD jelentéskészítési funkciójának használatát igényli a vállalat incidens-válaszának részeként.

## <a name="next-steps"></a>Következő lépések
[Hibrid Identitáskezelés kezelési feladatainak meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)