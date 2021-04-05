---
title: Elkülönítés az Azure nyilvános felhőben | Microsoft Docs
description: Ismerje meg, hogyan biztosítja az Azure a kártékony és a nem rosszindulatú felhasználók elkülönítését, és különböző elkülönítési lehetőségeket kínál az építészek számára.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c06fb0830ae709918b668ed60efbaaf47a63ce84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842838"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Elkülönítés az Azure nyilvános felhőben

Az Azure lehetővé teszi alkalmazások és virtuális gépek (VM-EK) futtatását megosztott fizikai infrastruktúrán. Az alkalmazások felhőalapú környezetben való futtatásának egyik legfőbb gazdasági indítéka az, hogy a megosztott erőforrások költségeit több ügyfél között is el tudja osztani. A több-bérlős megoldás a hatékonyságot növeli a különböző ügyfelek számára alacsony költségek mellett. Sajnos a fizikai kiszolgálók és más infrastruktúra-erőforrások megosztásának kockázata is fennáll, hogy az érzékeny alkalmazásokat és virtuális gépeket tetszőleges és potenciálisan rosszindulatú felhasználóhoz lehessen futtatni.

Ez a cikk bemutatja, hogyan biztosítja az Azure a kártékony és a nem rosszindulatú felhasználók elkülönítését, és útmutatást nyújt a felhőalapú megoldások tervezéséhez azáltal, hogy különféle elkülönítési lehetőségeket kínál az építészek számára.

## <a name="tenant-level-isolation"></a>Bérlői szint elkülönítése

A felhő-számítástechnika egyik legfőbb előnye, hogy egy közös, közös infrastruktúrát használ egyszerre számos ügyfélen, ami a méretgazdaságosságot eredményezi. Ezt a koncepciót több-bérlőnek nevezzük. A Microsoft folyamatosan gondoskodik arról, hogy a Microsoft Cloud Azure több-bérlős architektúrája támogassa a biztonságot, a titkosságot, az adatvédelmet, az integritást és a rendelkezésre állási szabványokat.

Felhőalapú munkahelyek esetén a bérlő olyan ügyfelet vagy szervezetet jelent, amely a felhőszolgáltatás adott példányát birtokolja és kezeli. A Microsoft Azure által biztosított Identity platformmal a bérlő egyszerűen az Azure Active Directory (Azure AD) dedikált példánya, amelyet a szervezet kap, és amely a Microsoft Cloud Service szolgáltatásra való feliratkozáskor megkapta és tulajdonosa.

Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúrájával megakadályozható az ügyfél- és identitásadatok keveredése. Ez azt jelenti, hogy az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

### <a name="azure-tenancy"></a>Azure-bérlet

Az Azure-beli bérlet (Azure-előfizetés) egy "ügyfél/számlázási" kapcsolatra és egy [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)egyedi [bérlőre](../../active-directory/develop/quickstart-create-new-tenant.md) utal. A bérlői szint elkülönítése Microsoft Azure a Azure Active Directory és az [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md) használatával érhető el. Minden Azure-előfizetés egy Azure Active Directory (AD) címtárral van társítva.

A címtárban lévő felhasználók, csoportok és alkalmazások kezelhetik az Azure-előfizetés erőforrásait. Ezeket a hozzáférési jogosultságokat a Azure Portal, az Azure parancssori eszközök és az Azure felügyeleti API-k használatával rendelheti hozzá. Az Azure AD-bérlők logikailag el vannak különítve a biztonsági határok használatával, így egyetlen ügyfél sem férhet hozzá vagy nem veszélyeztetheti a közös bérlőket, akár rosszindulatúan, akár véletlenül. Az Azure AD olyan "operációs rendszer nélküli" kiszolgálókon fut, amelyek elkülönített hálózati szegmensen vannak elkülönítve, ahol a gazdagép szintű csomagszűrés és a Windows tűzfal blokkolja a nemkívánatos kapcsolatokat és a forgalmat.

- Az Azure AD-beli adathozzáféréshez felhasználói hitelesítésre van szükség a biztonsági jogkivonat-szolgáltatás (STS) használatával. Az engedélyezési rendszer a felhasználó létezésére, engedélyezett állapotára és szerepkörére vonatkozó információkat használja annak megállapítására, hogy a cél bérlő számára a kért hozzáférés engedélyezve van-e a felhasználó számára ebben a munkamenetben.

![Azure-bérlet](./media/isolation-choices/azure-isolation-fig1.png)

- A bérlők diszkrét tárolók, és ezek között nincs kapcsolat.

- Nincs hozzáférés a bérlők között, kivéve, ha a bérlői rendszergazda a többi bérlőtől összevont vagy felhasználói fiókokat ad hozzá.

- Az Azure AD szolgáltatást tartalmazó kiszolgálókhoz való fizikai hozzáférés korlátozott, és közvetlen hozzáférés az Azure AD háttérrendszer-rendszereihez.

- Az Azure AD-felhasználók nem férnek hozzá a fizikai eszközökhöz vagy a helyszínekhez, ezért nem lehetséges, hogy megkerüljék a logikai Azure RBAC-szabályzatok által jelzett ellenőrzéseket.

Diagnosztikai és karbantartási igények esetén az igény szerinti jogosultságszint-emelést alkalmazó operatív modell szükséges és használatos. A Azure AD Privileged Identity Management (PIM) bevezeti a jogosult rendszergazda fogalmát. a [jogosult rendszergazdáknak](../../active-directory/privileged-identity-management/pim-configure.md) olyan felhasználóknak kell lenniük, akiknek most, de nem minden nap van szükségük privilegizált hozzáférésre. A szerepkör inaktív, amíg a felhasználónak nincs szüksége a hozzáférésre, majd szükség esetén a felhasználó egy aktiválási folyamat teljesítésével válhat aktív rendszergazdává egy előre meghatározott időtartamra.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory a saját védett tárolójában lévő összes bérlőt üzemelteti, és a tárolóra vonatkozó szabályzatokkal és engedélyekkel kizárólag a bérlő tulajdonosa és felügyeli.

A bérlői tárolók fogalma mélyen gyökerezik a címtárszolgáltatás minden rétegében, a portálok egészen az állandó tárterületig.

Még akkor is, ha több Azure Active Directory-bérlő metaadatait ugyanazon a fizikai lemezen tárolják, nincs kapcsolat a címtárszolgáltatás által definiált tárolók között, amelyet viszont a bérlő rendszergazdája diktál.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) lehetővé teszi az Azure-előfizetésekben elérhető különböző összetevők megosztását azáltal, hogy részletes hozzáférés-kezelést biztosít az Azure-hoz. Az Azure RBAC lehetővé teszi, hogy elkülönítse a feladatokat a szervezeten belül, és biztosítsa a hozzáférést, hogy a felhasználóknak milyen feladatokat kell elvégezniük. Ahelyett, hogy az Azure-előfizetésben vagy-erőforrásokban mindenki számára nem korlátozott engedélyeket adna, csak bizonyos műveleteket engedélyezhet.

Az Azure RBAC három alapvető szerepkörrel rendelkezik, amelyek minden erőforrástípus esetében érvényesek:

- A **tulajdonos** teljes hozzáféréssel rendelkezik az összes erőforráshoz, beleértve a másokhoz való hozzáférés delegálására vonatkozó jogosultságot is.

- A **közreműködő** az Azure-erőforrások összes típusát létrehozhatja és kezelheti, de mások számára nem biztosít hozzáférést.

- Az **olvasó** megtekintheti a meglévő Azure-erőforrásokat.

![Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](./media/isolation-choices/azure-isolation-fig3.png)

Az Azure további Azure-szerepkörei lehetővé teszik bizonyos Azure-erőforrások kezelését. Például a Virtuális gépek közreműködője szerepkör virtuális gépek létrehozását és kezelését teszi lehetővé. Nem biztosít számukra hozzáférést az Azure Virtual Network vagy az alhálózathoz, amelyhez a virtuális gép csatlakozik.

Az [Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md) az Azure-ban elérhető szerepköröket sorolja fel. Meghatározza azokat a műveleteket és hatókört, amelyeket az egyes beépített szerepkörök a felhasználók számára biztosítanak. Ha a saját szerepköröket is meg szeretné határozni még több szabályozáshoz, tekintse meg az [Egyéni szerepkörök létrehozása az Azure RBAC](../../role-based-access-control/custom-roles.md)című témakört.

A Azure Active Directory további képességei a következők:

- Az Azure AD lehetővé teszi az SSO bejelentkezést a SaaS-alkalmazások számára a futtatási helyüktől függetlenül. Egyes alkalmazások az Azure AD-vel összevontan működnek, mások jelszavas egyszeri bejelentkezést használnak. Az összevont alkalmazások támogatják a felhasználók üzembe helyezését és a [jelszavas](https://www.techopedia.com/definition/31415/password-vault)tárat is.

- Az [Azure Storage](https://azure.microsoft.com/services/storage/)-ban tárolt adatokhoz való hozzáférést hitelesítés védi. Mindegyik Storage-fiókhoz tartozik egy elsődleges kulcs (a[Storage-fiók kulcsa](../../storage/common/storage-account-create.md)vagy a sak), valamint egy másodlagos titkos kulcs (a közös hozzáférési aláírás vagy SAS).

- Az Azure AD a helyszíni címtárakkal [Active Directory összevonási szolgáltatások (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs), szinkronizálást és replikációt biztosító szolgáltatáson keresztül biztosítja az identitást.

- Az [Azure AD multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) a többtényezős hitelesítési szolgáltatás, amely megköveteli, hogy a felhasználók a bejelentkezéseket a Mobile App, a telefonhívás vagy a szöveges üzenet használatával ellenőrizzék. Az Azure AD segítségével az Azure Multi-Factor Authentication-kiszolgálóval biztonságossá teheti a helyszíni erőforrásokat, valamint egyéni alkalmazásokat és címtárakat is használhat az SDK használatával.

- A [Azure ad Domain Services](https://azure.microsoft.com/services/active-directory-ds/) tartományvezérlők üzembe helyezése nélkül csatlakoztathatja az Azure-beli virtuális gépeket egy Active Directory tartományhoz. A vállalati Active Directory hitelesítő adataival bejelentkezhet ezekre a virtuális gépekre, és a Csoportházirend használatával felügyelheti a tartományhoz csatlakoztatott virtuális gépeket az összes Azure-beli virtuális gép biztonsági alapkonfigurációjának érvényesítéséhez.

- A [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy olyan, a felhasználók felé irányuló alkalmazások számára elérhető, globális identitású felügyeleti szolgáltatást nyújt, amely több száz millió identitásra méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók a meglévő közösségi fiókjaik használatával vagy a hitelesítő adatok létrehozásával jelentkezhetnek be az összes alkalmazásba.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Elkülönítés a Microsoft-rendszergazdáktól & adatok törlése

A Microsoft erős intézkedéseket tesz az adatok nem megfelelő hozzáférés vagy jogosulatlan személyek általi használata elleni védelme érdekében. Ezeket az üzemeltetési folyamatokat és vezérlőket az [online szolgáltatások használati feltételei](https://aka.ms/Online-Services-Terms)határozzák meg, amelyek az adataihoz való hozzáférésre vonatkozó szerződéses kötelezettségvállalásokat nyújtanak.

- A Microsoft mérnökök nem rendelkeznek alapértelmezett hozzáféréssel a felhőben tárolt adataihoz. Ehelyett hozzáférést kapnak a felügyelet felügyelete alatt, csak szükség esetén. Ezt a hozzáférést gondosan ellenőrzik és naplózzák, és visszavonjuk, ha már nincs rá szükség.
- A Microsoft más cégeket is felvehet, hogy a nevében korlátozott szolgáltatásokat nyújtsanak. Az alvállalkozók csak olyan szolgáltatások nyújtásához férhetnek hozzá az ügyféladatokhoz, amelyekhez a szolgáltatást felhasználták, és azokat nem használhatják más célra. Emellett a szerződések az ügyfelek adatainak titkosságának fenntartására is kötelezve vannak.

Az auditált minősítésekkel rendelkező üzleti szolgáltatásokat, például az ISO/IEC 27001-et, a Microsoft és az akkreditált könyvvizsgáló cégek rendszeresen ellenőrzik, amelyek mintavételes naplózást végeznek a hozzáférés igazolására, csak a jogos üzleti célokra. Bármikor és bármilyen okból bármikor elérheti saját vásárlói adatait.

Ha bármilyen adattörlést töröl, Microsoft Azure törli az adatfájlokat, beleértve a gyorsítótárazott vagy a biztonsági másolatokat is. A hatókörön belüli szolgáltatások esetében a törlés a megőrzési időszak végét követő 90 napon belül megtörténik. (A hatókörön belüli szolgáltatások az [online szolgáltatások használati feltételeinek](https://aka.ms/Online-Services-Terms)adatfeldolgozási feltételek szakaszában vannak meghatározva.)

Ha a tárterülethez használt lemezmeghajtó hardverhiba miatt meghibásodik, a rendszer biztonságosan [törli vagy megsemmisíti](https://microsoft.com/trustcenter/privacy/you-own-your-data) azt, mielőtt a Microsoft visszaadja azt a gyártónak a pótláshoz vagy a javításhoz. A meghajtón lévő adatbevitel felül van írva, így biztosítható, hogy az egyes eszközök nem állíthatók helyre.

## <a name="compute-isolation"></a>Számítási elkülönítés

A Microsoft Azure különböző felhőalapú számítástechnikai szolgáltatásokat kínál, amelyek számos számítási példányt tartalmaznak & olyan szolgáltatásokkal, amelyek automatikusan fel-és leskálázást tesznek lehetővé az alkalmazás vagy a vállalat igényeinek megfelelően. Ezeknek a számítási példányoknak és szolgáltatásoknak több szinten kell elkülöníteniük az adatok védelmét anélkül, hogy az ügyfelek által igényelt konfiguráció rugalmasságát kellene feláldozni.

### <a name="isolated-virtual-machine-sizes"></a>Elkülönített virtuálisgép-méretek

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Dedikált gazdagépek

Az előző szakaszban ismertetett elkülönített gazdagépeken kívül az Azure dedikált gazdagépeket is biztosít. A dedikált gazdagépek az Azure-ban olyan szolgáltatás, amely egy vagy több virtuális gép üzemeltetésére képes fizikai kiszolgálókat biztosít, és amelyek egyetlen Azure-előfizetéshez vannak hozzárendelve. A dedikált gazdagépek hardveres elkülönítést biztosítanak a fizikai kiszolgáló szintjén. Más virtuális gépek nem lesznek elhelyezve a gazdagépeken. A dedikált gazdagépek ugyanabban az adatközpontban vannak üzembe helyezve, és ugyanazon a hálózaton és a mögöttes tárolási infrastruktúrán osztoznak, mint más, nem elkülönített gazdagépek. További információ: az [Azure dedikált gazdagépek](../../virtual-machines/dedicated-hosts.md)részletes áttekintése.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V & root VM & vendég virtuális gépek között

Az Azure számítási platformja a gép virtualizálása alapján történik – ami azt jelenti, hogy minden ügyfél kódja egy Hyper-V virtuális gépen fut. Minden egyes Azure-csomóponton (vagy hálózati végponton) található egy olyan hypervisor, amely közvetlenül a hardveren fut, és a csomópontot egy változó számú vendég Virtual Machines (VM) értékre osztja.

![Hyper-V & root VM & vendég virtuális gépek között](./media/isolation-choices/azure-isolation-fig4.jpg)

Mindegyik csomópont egy speciális, a gazdagép operációs rendszerét futtató virtuális géppel is rendelkezik. A kritikus határ a virtuális gép elkülönítése a vendég virtuális gépektől és a vendég virtuális gépektől egymástól, a hypervisor és a root operációs rendszer által felügyelt. A hypervisor/root operációs rendszer párosítása kihasználja a Microsoft évtizedes operációsrendszer-biztonsági élményét, és a Microsoft Hyper-V-vel való újabb tanulását a vendég virtuális gépek erős elkülönítésének biztosításához.

Az Azure platform virtualizált környezetet használ. A felhasználói példányok olyan önálló virtuális gépekként működnek, amelyek nem rendelkeznek hozzáféréssel a fizikai gazdagépekhez.

Az Azure hypervisor úgy viselkedik, mint a Micro-kernel, és a vendég virtuális gépekről érkező összes hardveres hozzáférési kérést átadja a gazdagépnek feldolgozásra egy VMBus nevű megosztott memória felület használatával. Ez megakadályozza, hogy a felhasználók nyers olvasási, írási és végrehajtási hozzáférést szerezzenek a rendszerhez, és csökkenti a rendszererőforrások megosztásának kockázatát.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Speciális virtuálisgép-elhelyezési algoritmus & az oldalsó csatornák elleni támadások elleni védelem

A több virtuális gépre kiterjedő támadás két lépésből áll: egy támadó által vezérelt virtuális gép ugyanazon a gazdagépen található, mint az egyik károsult virtuális gép, majd az elkülönítési határ megsértve, hogy a bizalmas sértettek adatait ellopják, vagy befolyásolják a teljesítményt a kapzsiság vagy a vandalizmus szempontjából. A Microsoft Azure mindkét lépésben védelmet biztosít egy speciális virtuálisgép-elhelyezési algoritmus és az összes ismert csatornás támadás, például a zajos szomszéd virtuális gépek elleni védelem használatával.

### <a name="the-azure-fabric-controller"></a>Az Azure Fabric Controller

Az Azure Fabric Controller felelős az infrastruktúra-erőforrások bérlői számítási feladatokhoz való kiosztásához, és a gazdagépről a virtuális gépek felé irányuló, egyirányú kommunikációt kezeli. Az Azure Fabric-vezérlő virtuálisgép-elhelyezési algoritmusa igen kifinomult, és szinte lehetetlen a fizikai gazdagép szintjének előrejelzése.

![Az Azure Fabric Controller](./media/isolation-choices/azure-isolation-fig5.png)

Az Azure hypervisor kikényszeríti a memória és a folyamatok elkülönítését a virtuális gépek között, és biztonságosan irányítja a hálózati forgalmat a vendég operációs rendszer bérlői számára. Ez kiküszöböli a virtuális gép szintjén és a csatornán keresztüli támadás lehetőségét.

Az Azure-ban a legfelső szintű virtuális gép speciális: egy megerősített operációs rendszert futtat, amely egy Fabric-ügynököt (FA) futtató fő operációs rendszer. A FAs-t a vendég operációs rendszereken belül, az ügyfél-virtuális gépeken kezelheti. A FAs a tárolási csomópontokat is kezeli.

Az Azure hypervisor, a root OS/FA, valamint az ügyfél virtuális gépei és gáz-gyűjteménye egy számítási csomópontból áll. A FAs-t egy Fabric-vezérlő (FC) felügyeli, amely a számítási és tárolási csomópontokon kívül van (a számítási és a tárolási fürtöket külön FCs kezeli). Ha az ügyfél a futása közben frissíti az alkalmazás konfigurációs fájlját, az FC kommunikál a gyári kapcsolattal, amely értesíti a számítógépeket a konfiguráció módosításának alkalmazásáról. Hardverhiba esetén az FC automatikusan megkeresi a rendelkezésre álló hardvereket, és ott újraindítja a virtuális gépet.

![Azure Fabric-vezérlő](./media/isolation-choices/azure-isolation-fig6.jpg)

A Fabric-vezérlőktől az ügynök felé irányuló kommunikáció egyirányú. Az ügynök egy SSL-védelemmel ellátott szolgáltatást valósít meg, amely csak a vezérlőtől érkező kérésekre válaszol. Nem kezdeményezhet kapcsolatokat a vezérlőhöz vagy más emelt szintű belső csomóponthoz. Az FC az összes választ úgy kezeli, mintha nem voltak megbízhatók.

![Háló vezérlő](./media/isolation-choices/azure-isolation-fig7.png)

Az elkülönítés kiterjeszti a virtuális gépet a vendég virtuális gépekről, a vendég virtuális gépeket pedig egymástól. A számítási csomópontok is el vannak különítve a tárolási csomópontok között a fokozott védelem érdekében.

A hypervisor és a gazda operációs rendszer hálózati csomagokat biztosít, így biztosíthatja, hogy a nem megbízható virtuális gépek nem tudnak hamisított forgalmat előállítani, vagy nem fogadnak hozzájuk címzett forgalmat, a védett infrastruktúra-végpontokra irányítsák a forgalmat, vagy nem megfelelő szórásos forgalmat küldenek/fogadnak.

### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>A háló vezérlő ügynöke által a virtuális gép elkülönítéséhez konfigurált további szabályok

Alapértelmezés szerint a rendszer a virtuális gép létrehozásakor letiltja az összes forgalmat, majd a háló vezérlő ügynök konfigurálja a csomagszűrőket a szabályok és kivételek hozzáadásához az engedélyezett forgalom engedélyezéséhez.

A szabályok két kategóriába sorolhatók:

- A **Számítógép konfigurációja vagy az infrastruktúra szabályai:** Alapértelmezés szerint minden kommunikáció le van tiltva. Kivételt képeznek a virtuális gépek számára a DHCP-és DNS-forgalom küldésének és fogadásának engedélyezése. A virtuális gépek forgalmat is küldhetnek a "nyilvános" internetre, és az ugyanazon Azure-Virtual Network és az operációs rendszer aktiválási kiszolgálóján belüli más virtuális gépekre is küldhetnek forgalmat. A virtuális gépek engedélyezett kimenő célhelyek listája nem tartalmazza az Azure útválasztó alhálózatait, az Azure-felügyeletet és a Microsoft egyéb tulajdonságait.
- **Szerepkör-konfigurációs fájl:** Ez határozza meg a beérkező Access Control listákat (ACL-eket) a bérlő szolgáltatási modellje alapján.

### <a name="vlan-isolation"></a>VLAN-elkülönítés

Az egyes fürtökön három VLAN van:

![VLAN-elkülönítés](./media/isolation-choices/azure-isolation-fig8.jpg)

- A fő VLAN – a nem megbízható ügyfél-csomópontok közötti kapcsolat
- Az FC VLAN – megbízható FCs-és támogató rendszereket tartalmaz
- Az eszköz VLAN – megbízható hálózati és egyéb infrastruktúra-eszközöket tartalmaz

A kommunikáció az FC VLAN-ról a fő VLAN-ra engedélyezett, de nem indítható el a fő VLAN-ról az FC VLAN-ra. A kommunikáció a fő VLAN-ról az eszköz VLAN-ra is le van tiltva. Ez biztosítja, hogy még akkor is, ha az ügyfél kódját futtató csomópont sérül, nem képes a csomópontok támadására sem az FC, sem az eszköz VLAN-ok esetében.

## <a name="storage-isolation"></a>Tároló elkülönítése

### <a name="logical-isolation-between-compute-and-storage"></a>Logikai elkülönítés a számítás és a tárolás között

Az alapvető kialakítás részeként Microsoft Azure elkülöníti a virtuális gépeken alapuló számításokat a tárterületről. Ez a szétválasztás lehetővé teszi, hogy a számítások és a tárolók egymástól függetlenül méretezhetők legyenek, így könnyebben biztosítható több-bérlő és elkülönítés.

Ezért az Azure Storage külön hardveren fut, és nincs hálózati kapcsolat az Azure számítási feladatokkal, kivéve a logikailag. Ez azt jelenti, hogy a virtuális lemezek létrehozásakor a lemezterület nincs lefoglalva a teljes kapacitáshoz. Ehelyett létrejön egy tábla, amely a virtuális lemezen lévő címeket leképezi a fizikai lemezen lévő területekre, és a tábla kezdetben üres. **Amikor az ügyfél először ír le adatot a virtuális lemezen, a rendszer leosztja a fizikai lemezen lévő helyet, és egy mutatót helyez el a táblába.**

### <a name="isolation-using-storage-access-control"></a>Elkülönítés a Storage hozzáférés-vezérlésével

**Az Azure Storage-ban Access Control** egyszerű hozzáférés-vezérlési modellel rendelkezik. Az egyes Azure-előfizetések egy vagy több Storage-fiókot is létrehozhatnak. Mindegyik Storage-fiók egyetlen titkos kulccsal rendelkezik, amely a Storage-fiókban lévő összes adattal való hozzáférés szabályozására szolgál.

![Elkülönítés a Storage hozzáférés-vezérlésével](./media/isolation-choices/azure-isolation-fig9.png)

**Az Azure Storage-beli adattárolók (beleértve a táblákat is) hozzáférését** egy [sas (közös hozzáférési aláírás)](../../storage/common/storage-sas-overview.md) jogkivonattal szabályozhatja, amely hatókörön belüli hozzáférést biztosít. Az SAS egy lekérdezési sablon (URL) használatával jön létre, amely a [sak (a Storage-fiók kulcsával)](/previous-versions/azure/reference/ee460785(v=azure.100))van aláírva. Az [aláírt URL-cím](../../storage/common/storage-sas-overview.md) megadható egy másik folyamatnak (azaz delegált), amely kitöltheti a lekérdezés részleteit, és elvégezheti a tárolási szolgáltatás kérését. Az SAS lehetővé teszi, hogy időalapú hozzáférést biztosítson az ügyfelek számára a Storage-fiók titkos kulcsának felfedése nélkül.

Az SAS azt jelenti, hogy az ügyfél korlátozott engedélyeket biztosíthat a Storage-fiókban lévő objektumoknak egy adott időtartamra és egy megadott engedélyekkel. Ezeket a korlátozott engedélyeket a fiók hozzáférési kulcsainak megosztása nélkül biztosíthatjuk.

### <a name="ip-level-storage-isolation"></a>IP-szintű tároló elkülönítése

Tűzfalat hozhat létre, és meghatározhatja a megbízható ügyfelek IP-címtartományt. Az IP-címtartomány csak a megadott tartományon belüli IP-címmel rendelkező ügyfelek csatlakozhatnak az [Azure Storage](../../storage/blobs/security-recommendations.md)szolgáltatáshoz.

Az IP-tárolási adatokat olyan hálózati mechanizmussal lehet védeni a jogosulatlan felhasználóktól, amely az IP-tárolóra irányuló dedikált vagy dedikált bújtatási alagút kiosztására szolgál.

### <a name="encryption"></a>Titkosítás

Az Azure a következő titkosítási típusokat biztosítja az adatvédelem érdekében:

- Titkosítás az átvitel során
- Titkosítás inaktív állapotban

#### <a name="encryption-in-transit"></a>Titkosítás átvitel közben

Az átvitel közbeni titkosítás egy olyan mechanizmus, amely az adatok védelmét a hálózatokon keresztül továbbítja. Az Azure Storage használatával az alábbiakkal védheti meg az adatvédelmet:

- [Átviteli szintű titkosítás](../../storage/blobs/security-recommendations.md), például https, ha az Azure Storage-ba vagy az-ba helyezi át az adatátvitelt.
- [Vezetékes titkosítás](../../storage/blobs/security-recommendations.md), például SMB 3,0 titkosítás az Azure-fájlmegosztás számára.
- [Ügyféloldali titkosítás](../../storage/blobs/security-recommendations.md), amely a tárterületre való átvitel előtt titkosítja az adatátvitelt, és visszafejti az adatmennyiséget a tárterületről való átadást követően.

#### <a name="encryption-at-rest"></a>Titkosítás nyugalmi állapotban

Számos szervezet esetében az [adattitkosítás](isolation-choices.md) az adatok védelme, a megfelelőség és az adatok szuverenitása szempontjából kötelező lépés. Három Azure-szolgáltatás áll rendelkezésre, amelyek a "nyugalmi állapotban" lévő adatok titkosítását teszik lehetővé:

- [Storage Service encryption](../../storage/blobs/security-recommendations.md) lehetővé teszi, hogy a Storage szolgáltatás automatikusan titkosítsa az adattitkosítást az Azure Storage-ba való íráskor.
- Az [ügyféloldali titkosítás](../../storage/blobs/security-recommendations.md) emellett biztosítja a titkosítás nyugalmi funkcióját is.
- [Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) lehetővé teszi a IaaS virtuális gépek által használt operációsrendszer-lemezek és adatlemezek titkosítását.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) virtuális gépekhez (VM) segít a szervezeti biztonsági és megfelelőségi követelmények megoldásában azáltal, hogy titkosítja a virtuálisgép-lemezeket (beleértve a rendszerindító és az adatlemezeket is) a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)által felügyelt kulcsokkal és szabályzatokkal.

A Windows rendszerhez készült lemez-titkosítási megoldás a [Microsoft BitLocker meghajtótitkosításon](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11))alapul, és a Linux-megoldás a [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt)alapú.

A megoldás a következő forgatókönyveket támogatja a IaaS virtuális gépekhez, amikor azok engedélyezve vannak Microsoft Azureban:

- Integráció a Azure Key Vault
- Standard szintű virtuális gépek: A, D, DS, G, GS, és így tovább, sorozatú IaaS virtuális gépek
- Titkosítás engedélyezése Windows és Linux rendszerű IaaS virtuális gépeken
- Az operációs rendszer és az adatmeghajtók titkosításának letiltása Windows IaaS virtuális gépeken
- A Linux IaaS-alapú virtuális gépek adatmeghajtóinak titkosításának letiltása
- A titkosítás engedélyezése a Windows ügyfél operációs rendszerét futtató IaaS virtuális gépeken
- A kötetek titkosításának engedélyezése csatlakoztatási útvonalakkal
- A titkosítás engedélyezése a lemezes csíkozással (RAID) konfigurált Linux rendszerű virtuális gépeken a [mdadm](https://en.wikipedia.org/wiki/Mdadm) használatával
- A Linux rendszerű virtuális gépek titkosításának engedélyezése az adatlemezek [LVM (Logic Volume Manager)](/windows/win32/fileio/about-volume-management) használatával
- A titkosítás engedélyezése a tárolóhelyek használatával konfigurált Windows rendszerű virtuális gépeken
- Az összes Azure-beli nyilvános régió támogatott

A megoldás nem támogatja az alábbi forgatókönyveket, szolgáltatásokat és technológiákat a kiadásban:

- Alapszintű IaaS virtuális gépek
- A Linux IaaS virtuális gépekhez tartozó operációsrendszer-meghajtók titkosításának letiltása
- A klasszikus virtuálisgép-létrehozási módszer használatával létrehozott IaaS virtuális gépek
- Integráció a helyszíni kulcskezelő szolgáltatással
- Azure Files (megosztott fájlrendszer), a hálózati fájlrendszer (NFS), a dinamikus kötetek és a szoftveres RAID-rendszerekkel konfigurált Windows-alapú virtuális gépek

## <a name="sql-database-isolation"></a>Elkülönítés SQL Database

Az SQL Database a Microsoft Cloud egy, a piacvezető Microsoft SQL Server motoron alapuló relációs adatbázis-szolgáltatása, amely képes a kritikus fontosságú számítási feladatok kezelésére. SQL Database előre jelezhető adatelkülönítést biztosít a fiók szintjén, a földrajz/régió alapján és a hálózatkezelésen alapuló, közel nulla felügyelettel.

### <a name="sql-database-application-model"></a>SQL Database alkalmazás modellje

A [Microsoft SQL Database](../../azure-sql/database/single-database-create-quickstart.md) egy SQL Server technológiákra épülő, felhőalapú, a szolgáltatással foglalkozó adatbázis-szolgáltatás. A Microsoft a felhőben üzemeltetett, magasan elérhető, skálázható, több-bérlős adatbázis-szolgáltatást nyújt.

Az alkalmazás szempontjából a SQL Database a következő hierarchiát biztosítja: az egyes szinteken az egyes szintek egy-a-többhöz rendelkeznek.

![SQL Database alkalmazás modellje](./media/isolation-choices/azure-isolation-fig10.png)

A fiók és az előfizetés Microsoft Azure platform-fogalmakat a számlázás és a felügyelet hozzárendeléséhez.

A logikai SQL-kiszolgálók és-adatbázisok SQL Database-specifikus fogalmak, amelyeket a SQL Database, a OData és a TSQL felületek, illetve a Azure Portal használatával felügyelnek.

A SQL Database-kiszolgálók nem fizikai vagy virtuálisgép-példányok, hanem adatbázisok gyűjteményei, felügyeleti és biztonsági szabályzatok megosztása, amelyeket az úgynevezett "logikai főkiszolgáló" adatbázisa tárol.

![SQL Database](./media/isolation-choices/azure-isolation-fig11.png)

A logikai Master adatbázisok a következők:

- A kiszolgálóhoz való kapcsolódáshoz használt SQL-bejelentkezések
- Tűzfalszabályok

Az ugyanazon kiszolgálóról származó adatbázisokra vonatkozó számlázási és használati információk nem garantáltak a fürt ugyanazon fizikai példányán, hanem a csatlakozáskor az alkalmazásoknak meg kell adniuk a célként megadott adatbázis nevét.

Az ügyfél szemszögéből a kiszolgáló egy geo-grafikus régióban jön létre, míg a kiszolgáló tényleges létrehozása a régió egyik fürtjében történik.

### <a name="isolation-through-network-topology"></a>Elkülönítés hálózati topológián keresztül

Ha létrejön egy kiszolgáló, és a DNS-neve regisztrálva van, a DNS-név az úgynevezett "átjáró VIP" címet adja meg abban az adatközpontban, ahol a kiszolgálót elhelyezték.

A VIP (virtuális IP-cím) mögött található állapot nélküli Gateway-szolgáltatások gyűjteménye. Általánosságban elmondható, hogy az átjárók abban az esetben vesznek részt, ha koordináció szükséges több adatforrás (főadatbázis, felhasználói adatbázis stb.) között. Az átjárószolgáltatás implementálja a következőket:

- **TDS-kapcsolatok proxyja.** Ebbe beletartozik a felhasználói adatbázis megkeresése a háttér-fürtben, a bejelentkezési folyamat implementálása, majd a TDS-csomagok továbbítása a háttérbe és vissza.
- **Adatbázis-kezelés.** Ez magában foglalja a munkafolyamatok gyűjteményének megvalósítását az adatbázis létrehozása/módosítása/eldobása során. Az adatbázis-műveleteket akár szippantás TDS-csomagok, akár explicit OData API-k is meghívhatják.
- Bejelentkezési/felhasználói műveletek létrehozása/módosítása/eldobása
- Kiszolgálófelügyeleti műveletek a OData API-n keresztül

![Elkülönítés hálózati topológián keresztül](./media/isolation-choices/azure-isolation-fig12.png)

Az átjáró mögötti réteg neve "háttér". Ez az a hely, ahol az összes adattárolót egy nagyon elérhető módon tárolja. Minden egyes adat egy "partíció" vagy "feladatátvételi egység" csoportjába tartozik, amelyek mindegyike legalább három replikával rendelkezik. A replikákat SQL Server motor tárolja és replikálja, és egy olyan feladatátvételi rendszer kezeli, amelyet gyakran "Fabric"-ként nevezünk.

Általában a háttérrendszer biztonsági óvintézkedésként nem továbbítja a kimenő fájlokat más rendszereknek. Ez az előtér-(átjáró-) rétegben található rendszerek számára van fenntartva. Az átjárók rétegének gépei korlátozott jogosultságokkal rendelkeznek a háttér-gépeken, így a támadási felületet a mélyebb védelmi mechanizmusnak megfelelően csökkentheti.

### <a name="isolation-by-machine-function-and-access"></a>Elkülönítés gépi funkció és hozzáférés alapján

SQL Database (a különböző gépi funkciókon futó szolgáltatásokból áll. SQL Database a "háttér" felhő-adatbázisra és az "előtér-felügyeleti" környezetekre van osztva, és a forgalom általános alapelve csak a háttér-és nem kimenő. Az előtér-környezet más szolgáltatások külvilág felé tud kommunikálni, és általában csak korlátozott engedélyekkel rendelkezik a háttérben (elég a meghívott belépési pontok meghívásához).

## <a name="networking-isolation"></a>Hálózati elkülönítés

Az Azure-beli üzembe helyezés több rétegű hálózati elkülönítéssel rendelkezik. Az alábbi ábrán az Azure hálózati elkülönítésének különböző rétegei láthatók az ügyfelek számára. Ezek a rétegek mind az Azure platformon, mind pedig az ügyfél által definiált funkciók. Az internetről beérkező Azure DDoS elkülöníti az Azure-ra irányuló nagy méretű támadásokat. Az elkülönítés következő rétege az ügyfél által meghatározott nyilvános IP-címek (végpontok), amelyek segítségével megállapítható, hogy a felhőalapú szolgáltatás milyen forgalmat továbbíthat a virtuális hálózatra. A natív Azure-beli virtuális hálózat elkülönítése biztosítja a teljes elkülönítést az összes többi hálózattól, és a forgalom csak a felhasználó által konfigurált útvonalakon és metódusokon keresztül folyik. Ezek az elérési utak és módszerek a következő réteg, ahol a NSG, a UDR és a hálózati virtuális berendezések használatával elkülönítési határokat hozhat létre a védett hálózatban található alkalmazások központi telepítésének védelme érdekében.

![Hálózati elkülönítés](./media/isolation-choices/azure-isolation-fig13.png)

**Forgalom elkülönítése:** A [virtuális hálózat](../../virtual-network/virtual-networks-overview.md) az Azure platform forgalom-elkülönítési határa. Az egyik virtuális hálózatban lévő virtuális gépek nem tudnak közvetlenül kommunikálni egy másik virtuális hálózatban lévő virtuális gépekkel, még akkor is, ha az ügyfél mindkét virtuális hálózatot létrehozta. Az elkülönítés olyan kritikus tulajdonság, amely biztosítja, hogy az ügyfél virtuális gépei és kommunikációja a virtuális hálózaton belül maradjon.

Az [alhálózat](../../virtual-network/virtual-networks-overview.md) egy további elkülönítési réteget biztosít a virtuális hálózaton az IP-címtartomány alapján. A virtuális hálózat IP-címei a virtuális hálózatok több alhálózatra oszthatók a szervezet és a biztonság érdekében. Egy VNeten belül az alhálózatokra üzembe helyezett virtuális gépek és a PaaS szerepkörpéldányok (ugyanaz vagy különböző) további konfigurálás nélkül is tudnak egymással kommunikálni. A [hálózati biztonsági csoport (NSG)](../../virtual-network/virtual-networks-overview.md) konfigurálható úgy is, hogy engedélyezze vagy megtagadja a virtuálisgép-példányok hálózati FORGALMÁT a NSG hozzáférés-vezérlési listájában (ACL) konfigurált szabályok alapján. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuálisgép-példányra.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Windows Azure virtuális hálózatokban található gépek hálózati elkülönítési lehetőségeit](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Ez magában foglalja a klasszikus előtér-és háttér-forgatókönyvet, ahol egy adott háttérrendszer vagy alhálózat számítógépei csak bizonyos ügyfelek vagy más számítógépek számára engedélyezhetik az IP-címek engedélyezési listáján alapuló adott végponthoz való kapcsolódást.

- Ismerje meg a [virtuális gépek elkülönítését az Azure-ban](../../virtual-machines/isolation.md). Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve.