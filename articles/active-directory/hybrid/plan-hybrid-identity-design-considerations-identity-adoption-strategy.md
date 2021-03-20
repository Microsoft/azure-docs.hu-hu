---
title: Hybrid Identity design – bevezetési stratégia az Azure-ban | Microsoft Docs
description: A feltételes hozzáférés-vezérléssel az Azure AD ellenőrzi a felhasználó hitelesítése és az alkalmazáshoz való hozzáférés engedélyezése előtt kiválasztott konkrét feltételeket.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
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
ms.openlocfilehash: 7f52e46ff9cab7d3d150af9fd7b4f1c432bec74b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836189"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Hibrid identitás-bevezetési stratégia definiálása
Ebben a feladatban megadhatja a hibrid identitás-bevezetési stratégiát a hibrid személyazonossági megoldáshoz, hogy megfeleljenek a következő cikkben tárgyalt üzleti követelményeknek:

* [Üzleti igények meghatározása](plan-hybrid-identity-design-considerations-business-needs.md)
* [Címtár-szinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [A multi-Factor Authentication követelményeinek meghatározása](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Üzleti igényekre vonatkozó stratégia meghatározása
A szervezeti üzleti igényeket meghatározó első feladat.  Ez nagyon tág lehet, és a hatókör-kúszás akkor fordulhat elő, ha nem vigyáz rá.  Az elején tartsa egyszerűvé, de mindig ne feledje, hogy tervezzen egy olyan kialakítást, amely a jövőben is alkalmazkodik és megkönnyíti a változást.  Függetlenül attól, hogy ez egy egyszerű vagy rendkívül összetett, Azure Active Directory a Microsoft Identity platform, amely támogatja a Microsoft 365, a Microsoft Online Services és a Cloud Aware alkalmazásokat.

## <a name="define-an-integration-strategy"></a>Integrációs stratégia definiálása
A Microsoft három fő integrációs forgatókönyvet tartalmaz, amelyek a Felhőbeli identitások, a szinkronizált identitások és az összevont identitások.  Ezen integrációs stratégiák egyikének bevezetését érdemes megterveznie.  Az Ön által választott stratégia változhat, és a döntések közül választhat, hogy milyen típusú felhasználói élményt szeretne biztosítani, rendelkezik meglévő infrastruktúrával, és mi a legköltséghatékonyabb megoldás.  

![integrációs forgatókönyvek](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

A fenti ábrán meghatározott forgatókönyvek a következők:

* **Felhőbeli identitások**: ezek olyan identitások, amelyek kizárólag a felhőben léteznek.  Az Azure AD esetében a felhasználók az Azure AD-címtárban fognak megjelenni.
* **Szinkronizálva**: ezek a helyszíni és a felhőben található identitások.  A Azure AD Connect használatával ezek a felhasználók a meglévő Azure AD-fiókokkal jönnek létre vagy csatlakoznak.  A felhasználó jelszavas kivonatát a rendszer a helyszíni környezetből a felhőbe szinkronizálja, amit a jelszó-kivonatnak nevezünk.  Ha a szinkronizálást használja, az egyetlen figyelmeztetés, hogy ha egy felhasználó le van tiltva a helyszíni környezetben, akár három órát is igénybe vehet, hogy az adott fiók állapota megjelenjen az Azure AD-ben.  Ezt a szinkronizálási időtartam okozza.
* **Összevont**: ezek az identitások mind a helyszínen, mind a felhőben léteznek.  A Azure AD Connect használatával ezek a felhasználók a meglévő Azure AD-fiókokkal jönnek létre vagy csatlakoznak.  

> [!NOTE]
> A szinkronizálási lehetőségekkel kapcsolatos további információkért olvassa el a helyszíni [identitások integrálása a Azure Active Directorykal](whatis-hybrid-identity.md)című témakört.
> 
> 

Az alábbi táblázat segít meghatározni az alábbi stratégiák előnyeit és hátrányait:

| Stratégia | Előnyök | Hátrányok |
| --- | --- | --- |
| **Felhőbeli identitások** |Könnyebben kezelhető a kis szervezet számára. <br> Nincs a helyszínen telepíteni. Nincs szükség további hardverre<br>Könnyen letiltható, ha a felhasználó elhagyja a vállalatot |A felhasználóknak be kell jelentkezniük a Felhőbeli számítási feladatok elérésekor <br> Előfordulhat, hogy a jelszavak nem egyeznek a Felhőbeli és a helyszíni identitásokkal. |
| **Szinkronizált** |Helyszíni és Felhőbeli könyvtárak hitelesítése helyszíni jelszóval <br>Könnyebben kezelhető kis, közepes vagy nagyméretű szervezetek számára <br>Egyes erőforrások esetében a felhasználók egyszeri bejelentkezéssel (SSO) rendelkezhetnek <br> A Microsoft által javasolt szinkronizálási módszer <br> Könnyebben kezelhető |Előfordulhat, hogy néhány ügyfél vonakodik szinkronizálni a címtárakat a Felhőbeli konkrét vállalati szabályzatokkal |
| **Összevont** |A felhasználók rendelkezhetnek egyszeri bejelentkezéssel (SSO) <br>Ha a felhasználó leáll vagy elhagyja a fiókot, a fiók azonnal letiltható, és a rendszer visszavonja a hozzáférést.<br> Olyan speciális forgatókönyvek támogatása, amelyek szinkronizálása nem végezhető el |További lépések a beállításához és konfigurálásához <br> Nagyobb karbantartás <br> További hardvert igényelhet az STS-infrastruktúrához <br> Az összevonási kiszolgáló telepítéséhez további hardverre lehet szükség. Ha AD FS van használatban, további szoftverre van szükség <br> Széles körű beállítás szükséges az egyszeri bejelentkezéshez <br> Kritikus meghibásodási pont, ha az összevonási kiszolgáló nem érhető el, a felhasználók nem fognak tudni hitelesítést végezni |

### <a name="client-experience"></a>Ügyfélélmény
A használt stratégia a felhasználó bejelentkezési élményét fogja diktálni.  Az alábbi táblázatokból megtudhatja, hogy a felhasználók mire számíthatnak a bejelentkezési élményre.  Nem minden összevont Identitáskezelő támogatja az egyszeri bejelentkezést az összes forgatókönyvben.

**Doman-csatlakoztatott és magánhálózati alkalmazások**:

| Alkalmazás | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Böngészők |Űrlapalapú hitelesítés |egyszeri bejelentkezés, esetenként a szervezet AZONOSÍTÓjának megadásához szükséges |
| Outlook |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |
| Skype vállalati verzió (Lync) |Hitelesítő adatok bekérése |egyszeri bejelentkezés a Lync szolgáltatáshoz, a rendszer kéri az Exchange hitelesítő adatait |
| OneDrive Vállalati verzió |Hitelesítő adatok bekérése |egyszeri bejelentkezés |
| Office Pro Plus-előfizetés |Hitelesítő adatok bekérése |egyszeri bejelentkezés |

**Külső vagy nem megbízható források**:

| Alkalmazás | Szinkronizált identitás | Federated Identity |
| --- | --- | --- |
| Böngészők |Űrlapalapú hitelesítés |Űrlapalapú hitelesítés |
| Outlook, Skype vállalati verzió (Lync), OneDrive for Business, Office-előfizetés |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |
| Exchange ActiveSync |Hitelesítő adatok bekérése |egyszeri bejelentkezés a Lync szolgáltatáshoz, a rendszer kéri az Exchange hitelesítő adatait |
| Mobilalkalmazások |Hitelesítő adatok bekérése |Hitelesítő adatok bekérése |

Ha az 1. feladatból azt állapította meg, hogy rendelkezik harmadik féltől származó identitásszolgáltató, vagy az egyiket fogja használni az Azure AD-vel való összevonás biztosításához, akkor a következő támogatott képességekre van szüksége:

* A SP-Lite profilnak megfelelő SAML 2,0-szolgáltatók támogatják az Azure AD-hez és a kapcsolódó alkalmazásokhoz való hitelesítést.
* Támogatja a passzív hitelesítést, amely megkönnyíti az OWA, a Spongya stb. hitelesítését.
* Az Exchange Online-ügyfelek a SAML 2,0 bővített ügyféloldali profil (ECP) használatával támogatottak

Emellett tisztában kell lennie azzal, hogy milyen funkciók nem lesznek elérhetők:

* A WS-Trust/összevonás támogatása nélkül minden más aktív ügyfél megszakad
  * Ez azt jelenti, hogy nincs Lync-ügyfél, OneDrive-ügyfél, Office-előfizetés, Office Mobile az Office 2016 előtt
* Az Office és a passzív hitelesítés közötti áttérés lehetővé teszi számukra a tiszta SAML 2,0 IDP használatát, de a támogatás továbbra is ügyfél-ügyfél alapon történik.

> [!NOTE]
> A legutóbb frissített listán olvassa el az [Azure ad-összevonás kompatibilitási listáját](how-to-connect-fed-compatibility.md)ismertető cikket.
> 
> 

## <a name="define-synchronization-strategy"></a>Szinkronizálási stratégia definiálása
Ebben a feladatban meghatározza azokat az eszközöket, amelyeket a rendszer a szervezet helyszíni adatai felhőbe való szinkronizálásához és a használni kívánt topológiához fog használni.  Mivel a legtöbb szervezet Active Directory használ, a fenti kérdések megválaszolásához Azure AD Connect használatával kapcsolatos információkat részletesen ismertetjük.  Olyan környezetek esetén, amelyek nem rendelkeznek Active Directoryval, a stratégia megtervezése érdekében a FIM 2010 R2 vagy a faplatform 2016 használatával kapcsolatos információk találhatók.  A Azure AD Connect jövőbeli kiadásai azonban támogatni fogják az LDAP-címtárakat, így az ütemtervtől függően ezek az információk segíthetnek.

### <a name="synchronization-tools"></a>Szinkronizáló eszközök
Az évek során több szinkronizálási eszköz is létezett és használatban volt különböző forgatókönyvekhez.  Jelenleg Azure AD Connect az összes támogatott forgatókönyv esetén a választott eszköz.  A AAD-szinkronizáló és az rSync is a környékén marad, és a környezetében is jelen lehet. 

> [!NOTE]
> Az egyes eszközök támogatott képességeivel kapcsolatos legfrissebb információkért olvassa el a [címtár-integrációs eszközök összehasonlítását](plan-hybrid-identity-design-considerations-tools-comparison.md) ismertető cikket.  
> 
> 

### <a name="supported-topologies"></a>Támogatott topológiák
A szinkronizálási stratégia definiálásakor meg kell határozni a használni kívánt topológiát. A 2. lépésben meghatározott információktól függően meghatározhatja, hogy melyik topológiát kell használni. Az egyetlen erdő, egyetlen Azure AD-topológia a leggyakoribb, és egyetlen Active Directory erdőből és egyetlen Azure AD-példányból áll.  Ez a forgatókönyvek többségében lesz felhasználva, és a várt topológia a Azure AD Connect Express telepítésekor az alábbi ábrán látható módon.

![Az ](./media/plan-hybrid-identity-design-considerations/single-forest.png) egyerdős támogatott topológiák esetében gyakori, hogy a nagyméretű és még kisebb szervezetek több erdőt is tartalmazhatnak, ahogy az 5. ábrán is látható.

> [!NOTE]
> További információ a különböző helyszíni és Azure AD-topológiákkal kapcsolatban: Azure AD Connect Sync olvassa el a [Azure ad Connect topológiáit](plan-connect-topologies.md)ismertető cikket.
> 
> 

![több erdőből álló topológia](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Több erdős forgatókönyv

Ha ez a helyzet, akkor a több erdőből álló önálló Azure AD-topológiát figyelembe kell venni, ha a következő elemek teljesülnek:

* A felhasználók csak 1 identitással rendelkeznek az összes erdőben – az alábbi egyedi azonosítású felhasználók szakasz ezt részletesebben ismerteti.
* A felhasználó hitelesíti magát az erdőben, amelyben az identitásuk található
* Az egyszerű felhasználónév és a forrás horgonya (nem módosítható azonosító) Ebből az erdőből származik.
* Az összes erdő Azure AD Connect érhető el – ez azt jelenti, hogy nem kell tartományhoz csatlakoztatni, és egy DMZ-ben helyezhető el, ha ez megkönnyíti ezt.
* A felhasználóknak csak egy postaládájuk van
* A felhasználó postaládáját tároló erdő a legjobb adatminőséggel rendelkezik az Exchange globális címlistában (GAL) látható attribútumok számára.
* Ha a felhasználó nem tartalmaz postaládát, akkor bármely erdő felhasználható az értékek
* Ha csatolt postaládája van, akkor egy másik, a bejelentkezéshez használt erdőben is van egy másik fiók.

> [!NOTE]
> A helyszíni és a felhőben található objektumok "csatlakoztatva" vannak egy egyedi azonosítóval. A címtár-szinkronizálás kontextusában ez az egyedi azonosító a SourceAnchor néven szerepel. Az egyszeri bejelentkezés kontextusában ezt a ImmutableId nevezzük. A SourceAnchor használatára vonatkozó további megfontolások a [Azure ad Connect kialakításával kapcsolatos fogalmakat](plan-connect-design-concepts.md#sourceanchor) ismertetik.
> 
> 

Ha a fentiek nem teljesülnek, és egynél több aktív fiókkal vagy több postaládával rendelkezik, Azure AD Connect választ egyet, és figyelmen kívül hagyja a másikat.  Ha csatolt postaládákkal rendelkezik, de nincs más fiókja, ezek a fiókok nem lesznek exportálva az Azure AD-ba, és a felhasználó nem lesz tagja egyetlen csoportnak sem.  Ez nem más, mint ahogy a múltban volt, és szándékos, hogy jobban támogassa ezeket a több erdőre kiterjedő forgatókönyveket. Az alábbi ábrán egy többerdős forgatókönyv látható.

![több Azure AD-bérlő](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Több erdőre kiterjedő több Azure AD-forgatókönyv**

Azt javasoljuk, hogy csak egyetlen címtárral rendelkezzen az Azure AD-ben egy szervezet számára, de támogatja azt 1:1 kapcsolat egy Azure AD Connect szinkronizáló kiszolgáló és egy Azure AD-címtár között.  Az Azure AD minden példánya esetében Azure AD Connect telepítésére van szükség.  Az Azure AD-t a tervezés elkülönítve, az Azure AD egyik példányában lévő felhasználók azonban nem látják majd a felhasználókat egy másik példányban.

Lehetséges, hogy a Active Directory egy helyszíni példányát több Azure AD-címtárhoz is összekapcsolja az alábbi ábrán látható módon:

![egyerdős szűrés](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Egyerdős szűrési forgatókönyv**

Ehhez az alábbiaknak igaznak kell lenniük:

* Azure AD Connect szinkronizálási kiszolgálókat úgy kell konfigurálni, hogy azok mindegyike kölcsönösen exkluzív objektumokat hozzon létre.  Ez például az egyes kiszolgálók egy adott tartományra vagy szervezeti egységre való hatókörével végezhető el.
* Egy DNS-tartomány csak egyetlen Azure AD-címtárban regisztrálható, így a helyszíni AD felhasználói UPN-nek külön névtereket kell használnia
* Az Azure AD egy példányában lévő felhasználók csak a példányból származó felhasználókat láthatják.  A többi példányban nem jelennek meg a felhasználók.
* Csak az egyik Azure AD-címtár engedélyezheti az Exchange Hybrid szolgáltatást a helyszíni AD-vel
* A kölcsönös kizárólagosság a visszaírásra is vonatkozik.  Ez lehetővé teszi, hogy néhány írási visszavonási funkció ne legyen támogatva ehhez a topológiához, mivel ezek egyetlen helyszíni konfigurációt feltételeznek.  Ide tartoznak az alábbiak:
  * Csoportos írás – visszaállítás az alapértelmezett konfigurációval
  * Eszköz visszaírása

A következők nem támogatottak, és nem választhatók implementációként:

* Nem támogatott, hogy több Azure AD Connect szinkronizálási kiszolgáló is csatlakozzon ugyanahhoz az Azure AD-címtárhoz, még akkor is, ha azok kölcsönösen kizárólagos objektum szinkronizálására vannak konfigurálva
* Nem támogatott, hogy ugyanazt a felhasználót több Azure AD-címtárral szinkronizálja. 
* Emellett nem támogatott a konfiguráció módosítása, hogy egy Azure AD-beli felhasználók egy másik Azure AD-címtárban is megjelenjenek. 
* Emellett nem támogatott a Azure AD Connect Sync módosítása több Azure AD-címtárhoz való kapcsolódásra.
* Az Azure AD-címtárakat elszigetelten tervezték. Nem támogatott a Azure AD Connect Sync konfigurációjának módosítása egy másik Azure AD-címtárból származó adatok olvasására, ha egy közös és egységesített GAL-t próbál létrehozni a címtárak között. Emellett nem támogatott a felhasználók névjegyekként való exportálása egy másik helyszíni AD-hez Azure AD Connect Sync használatával.

> [!NOTE]
> Ha a szervezete korlátozza a hálózaton lévő számítógépeket az internethez való csatlakozáshoz, ez a cikk felsorolja azokat a végpontokat (FQDN-EK, IPv4-és IPv6-címtartományok), amelyeket a kimenő engedélyezési listán, valamint az ügyfélszámítógépeken az Internet Explorer megbízható helyek zónájában kell szerepeltetni, hogy a számítógépek sikeresen használhassák a Microsoft 365. További információért olvassa el az [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)című témakört.
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Multi-Factor Authentication stratégia meghatározása
Ebben a feladatban a használni kívánt multi-Factor Authentication stratégiát fogja meghatározni.  Az Azure AD Multi-Factor Authentication két különböző verzióban érhető el.  Az egyik a felhőalapú, a másik pedig a helyszíni Azure MFA-kiszolgáló használatával.  A fenti kiértékelés alapján meghatározhatja, hogy melyik megoldás a legmegfelelőbb a stratégiához.  Az alábbi táblázat segítségével meghatározhatja, hogy melyik tervezési lehetőség felel meg legjobban a vállalat biztonsági követelményeinek:

Multi-Factor design beállításai:

| Biztonságos eszköz | MFA a felhőben | Helyszíni MFA |
| --- | --- | --- |
| Microsoft-alkalmazások |igen |igen |
| SaaS-alkalmazások az alkalmazáskatalógusban |igen |igen |
| Az Azure AD-alkalmazásproxyn keresztül közzétett IIS-alkalmazások |igen |igen |
| Nem a Azure AD alkalmazás proxyn keresztül közzétett IIS-alkalmazások |nem |igen |
| Távelérés VPN-ként, RDG |nem |igen |

Annak ellenére, hogy a stratégia esetében is fennáll a megoldás, továbbra is a fenti értékelést kell használnia, ahol a felhasználók találhatók.  Ennek hatására előfordulhat, hogy a megoldás változhat.  Az alábbi táblázat segítségével meghatározhatja a következőt:

| Felhasználó helye | Előnyben részesített kialakítási lehetőség |
| --- | --- |
| Azure Active Directory |Több FactorAuthentication a felhőben |
| Azure AD és helyszíni AD összevonással az AD FS-sel |Mindkettő |
| Azure AD-t és helyszíni AD-t használó Azure AD Connect a jelszó-szinkronizálás nélkül |Mindkettő |
| Azure AD és helyszíni Azure AD Connect használatával jelszó-szinkronizálással |Mindkettő |
| Helyszíni AD |Multi-Factor Authentication-kiszolgáló |

> [!NOTE]
> Győződjön meg arról is, hogy a kiválasztott multi-Factor Authentication kialakítási beállítás támogatja a kialakításhoz szükséges szolgáltatásokat.  További információért olvassa el [a többtényezős biztonsági megoldás kiválasztása lehetőséget](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth szolgáltató
A többtényezős hitelesítés alapértelmezés szerint olyan globális rendszergazdák számára érhető el, akik Azure Active Directory Bérlővel rendelkeznek. Ha azonban szeretné kiterjeszteni a többtényezős hitelesítést az összes felhasználóra, és/vagy szeretné, hogy a globális rendszergazdák el tudják érni a kihasználható funkciókat, például a felügyeleti portált, az egyéni üdvözléseket és a jelentéseket, akkor meg kell vásárolnia és konfigurálnia Multi-Factor Authentication szolgáltatót.

> [!NOTE]
> Győződjön meg arról is, hogy a kiválasztott multi-Factor Authentication kialakítási beállítás támogatja a kialakításhoz szükséges szolgáltatásokat. 
> 
> 

## <a name="next-steps"></a>Következő lépések
[Adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

