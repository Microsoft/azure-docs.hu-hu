---
title: Azure Active Directory Connect GYIK – | Microsoft Docs
description: Ez a cikk a Azure AD Connectával kapcsolatos gyakori kérdésekre ad választ.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c0ae8ec210356f6027a46ed01f2a7126ea4a49
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644731"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect GYIK

## <a name="general-installation"></a>Általános telepítés

**K: Hogyan lehet megerősíteni a Azure AD Connect-kiszolgálót a biztonsági támadási felület csökkentése érdekében?**

A Microsoft azt javasolja, hogy a Azure AD Connect kiszolgáló megerősítse a biztonsági támadási felületet az IT-környezet ezen kritikus összetevője számára.  Az alábbi ajánlásokat követve csökkentheti a szervezete biztonsági kockázatait.

* Azure AD Connect üzembe helyezése egy tartományhoz csatlakoztatott kiszolgálón, és a rendszergazdai hozzáférés korlátozása a tartományi rendszergazdák vagy más szigorúan ellenőrzött biztonsági csoportok számára

További információ: 

* [Rendszergazdák csoportok biztonságossá tétele](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Beépített rendszergazdai fiókok biztonságossá tétele](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Biztonsági fejlesztés és fenntartás a támadási felületek csökkentésével](/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [A Active Directory támadási felület csökkentése](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**K: a telepítés akkor működik, ha a Azure Active Directory (Azure AD) globális rendszergazdája kétfaktoros hitelesítést (2FA) engedélyez?**  
A február 2016-es verziótól kezdve ez a forgatókönyv támogatott.

**K: van mód a Azure AD Connect felügyelet nélküli telepítésére?**  
Azure AD Connect telepítése csak a telepítővarázsló használata esetén támogatott. Felügyelet nélküli, csendes telepítés nem támogatott.

**K: van olyan erdő, amelyben nem lehet kapcsolatba lépni az egyik tartománnyal. Hogyan telepíteni a Azure AD Connectt?**  
A február 2016-es verziótól kezdve ez a forgatókönyv támogatott.

**K: a Azure Active Directory Domain Services (Azure AD DS) Health Agent működik a Server Core-ban?**  
Igen. Az ügynök telepítése után a következő PowerShell-parancsmag használatával végezheti el a regisztrációs folyamatot: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**K: Azure AD Connect támogatja a két tartományból az Azure AD-be való szinkronizálást?**  
Igen, ez a forgatókönyv támogatott. Tekintse át a [több tartományt](how-to-connect-install-multiple-domains.md).
 
**K: több összekötő is lehet ugyanahhoz a Active Directory tartományhoz Azure AD Connect-ben?**  
Nem, a több összekötő ugyanahhoz az AD-tartományhoz nem támogatott. 

**K: áthelyezhetem a Azure AD Connect adatbázist a helyi adatbázisból egy távoli SQL Server-példányba?**   
Igen, a következő lépések általános útmutatást nyújtanak ennek végrehajtásához. Jelenleg egy részletesebb dokumentumon dolgozunk.
1. A LocalDB ADSync-adatbázis biztonsági mentése.
Ennek a legegyszerűbb módja, ha a SQL Server Management Studio telepítve van ugyanarra a gépre, mint Azure AD Connect. Kapcsolódjon a *(LocalDb) .\ADSync*, és végezze el a AdSync-adatbázis biztonsági mentését.

2. Állítsa vissza a ADSync-adatbázist a távoli SQL Server-példányra.

3. Telepítse a Azure AD Connectt a meglévő [távoli SQL-adatbázison](how-to-connect-install-existing-database.md).
   A cikk bemutatja, hogyan lehet migrálni egy helyi SQL-adatbázis használatával. Ha egy távoli SQL Database-adatbázis használatával végez áttelepítést, a folyamat 5. lépésében meg kell adnia egy meglévő szolgáltatásfiókot is, amelyet a Windows Sync szolgáltatás fog futni. Ez a szinkronizálási motor szolgáltatás fiókja a következő módon van leírva:
   
      **Meglévő szolgáltatásfiók használata**: alapértelmezés szerint a Azure ad Connect egy virtuális szolgáltatásfiókot használ a szinkronizálási szolgáltatásokhoz. Ha távoli SQL Server példányt használ, vagy egy hitelesítést igénylő proxyt használ, egy felügyelt szolgáltatásfiókot vagy egy szolgáltatásfiókot kell használnia a tartományban, és ismernie kell a jelszót. Ezekben az esetekben adja meg a használni kívánt fiókot. Győződjön meg arról, hogy a telepítést futtató felhasználók a rendszergazdák az SQL-ben, hogy a szolgáltatásfiók bejelentkezési hitelesítő adatai is létrehozhatók. További információ: [Azure ad Connect-fiókok és-engedélyek](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      A legújabb buildben az SQL-rendszergazda sávon kívül kiépítheti az adatbázist, majd az Azure AD Connect-rendszergazda adatbázis-tulajdonosi jogosultságokkal telepítheti. További információkért lásd: [Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md).

A dolgok egyszerű megtartása érdekében javasoljuk, hogy az SQL-ben rendszergazdaként telepítse a Azure AD Connectt telepítő felhasználókat. A legutóbbi buildek azonban mostantól használhatnak delegált SQL-rendszergazdákat a [Azure ad Connect telepítése SQL meghatalmazott rendszergazdai engedélyekkel](how-to-connect-install-sql-delegation.md)című témakörben leírtak szerint.

**K: mi a legjobb gyakorlat a mezőből?**  

A következő egy tájékoztató dokumentum, amely bemutatja a mérnöki, támogatási és tanácsadóink által az évek során kifejlesztett ajánlott eljárásokat.  Ez egy olyan listajeles listában jelenik meg, amelyet gyorsan lehet hivatkozni.  Bár ez a lista átfogóan próbálkozik, előfordulhat, hogy további ajánlott eljárások is léteznek, amelyek még nem tették meg a listán.

- Ha teljes SQL-t használ, akkor továbbra is a helyi és a távoli
    - Kevesebb ugrás
    - Könnyebben elhárítható a hibák
    - Kevésbé összetett
    - Erőforrásokat kell kijelölnie az SQL-hez, és lehetővé kell tennie a Azure AD Connect és az operációs rendszer terhelését
- Proxy kihagyása, ha az összes lehetséges, ha nem tudja megkerülni a proxyt, akkor biztosítania kell, hogy az időkorlát 5 percnél nagyobb legyen.
- Ha proxyra van szükség, a proxyt hozzá kell adnia a machine.config fájlhoz.
- Vegye figyelembe a helyi SQL-feladatokat és-karbantartást, valamint azt, hogy ezek milyen hatással lesznek Azure AD Connect – különösen újraindexelésre
- Külső megoldás biztosítása, mint a DNS
- Győződjön meg arról, hogy a [kiszolgáló specifikációi](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) a fizikai vagy virtuális kiszolgálók használatát ajánlják
- Győződjön meg arról, hogy ha olyan virtuális kiszolgálót használ, amelyet a szükséges erőforrások dedikáltak
- Győződjön meg arról, hogy a lemez és a lemez konfigurációja megfelel a SQL Server ajánlott eljárásainak
- Azure AD Connect Health telepítése és konfigurálása figyeléshez
- Használja a Azure AD Connect beépített törlési küszöbértéket.
- Körültekintően tekintse át a kiadási frissítéseket, hogy készüljön fel az összes módosításra és az új attribútumokra, amelyeket fel lehet venni
- Biztonsági mentés minden
    - Biztonsági mentési kulcsok
    - Biztonsági másolatok szinkronizálási szabályai
    - Biztonsági mentési kiszolgáló konfigurációja
    - Biztonsági másolat SQL Database
- Győződjön meg arról, hogy nincs olyan külső biztonsági mentési ügynök, amely az SQL VSS-író nélkül készít biztonsági mentést az SQL-kiszolgálóról (közös a virtuális kiszolgálókon, külső pillanatképekkel)
- A bonyolultságuk hozzáadásakor használt egyéni szinkronizálási szabályok korlátozása
- Azure AD Connect-kiszolgálók kezelése 0. szintű kiszolgálóként
- A felhő-szinkronizálási szabályok módosításának furfangos a hatás és a megfelelő üzleti illesztőprogramok megismerése nélkül
- Győződjön meg arról, hogy a megfelelő URL-cím és tűzfal portok nyitva vannak Azure AD Connect és Azure AD Connect Health támogatásához
- A felhőben szűrt attribútum kihasználása a látszólagos objektumok hibakereséséhez és megelőzéséhez
- Az átmeneti kiszolgálóval gondoskodjon arról, hogy a kiszolgálók közötti konzisztenciát a Azure AD Connect konfigurációs dokumentáló használja
- Az átmeneti kiszolgálóknak különálló adatközpontokban kell lenniük (fizikai helyük
- Az átmeneti kiszolgálók nem magas rendelkezésre állású megoldásnak számítanak, de több átmeneti kiszolgálóval is rendelkezhet
- A "késés" átmeneti kiszolgálók bevezetése miatt előfordulhat, hogy hiba esetén lehetséges az állásidő.
- Az előkészítési kiszolgáló összes frissítésének tesztelése és ellenőrzése először
- Mindig ellenőrizze az exportálást, mielőtt átvált az átmeneti kiszolgálóra.  Kihasználhatja az átmeneti kiszolgálót a teljes importáláshoz és a teljes szinkronizáláshoz az üzleti hatás csökkentése érdekében
- A lehető legnagyobb mértékben megtarthatja a verziók közötti konzisztenciát Azure AD Connect-kiszolgálók között 

**K: engedélyezhető Azure AD Connect az Azure AD Connector-fiók létrehozása munkacsoportos gépen?**
Nem.  Ahhoz, hogy az Azure AD Connect automatikusan létre lehessen hozni az Azure AD Connector-fiókot, a gépnek tartományhoz kell tartoznia.  

## <a name="network"></a>Network (Hálózat)
**K: van egy tűzfallal, hálózati eszközzel vagy mással, amely korlátozza, hogy a kapcsolatok mikor maradhatnak nyitva a hálózaton. Mire használható az ügyféloldali időtúllépési küszöbérték a Azure AD Connect használatakor?**  
Minden hálózati szoftver, fizikai eszköz vagy bármi más, ami korlátozza a kapcsolatok nyitva maradható maximális idejét, legalább öt perc (300 másodperc) küszöbértéket kell használnia ahhoz, hogy a kiszolgáló, amelyen a Azure AD Connect-ügyfél telepítve van, és Azure Active Directory. Ez a javaslat a korábban megjelent Microsoft identitásszinkronizálás-eszközökre is vonatkozik.

**K: támogatottak egycímkés tartományok (SLDs)?**  
Habár erősen ajánljuk ezt a hálózati konfigurációt ([lásd a cikket](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), Azure ad Connect szinkronizálást egyetlen címkéző tartománnyal használva támogatott, feltéve, hogy az egyszintű tartomány hálózati konfigurációja megfelelően működik.

**K: támogatottak a különálló AD-tartományokkal rendelkező erdők?**  
Nem, Azure AD Connect nem támogatja a különálló névtereket tartalmazó helyszíni erdőket.

**K: támogatottak a "pontozott" NetBIOS-nevek?**  
Nem, Azure AD Connect nem támogatja a helyszíni erdőket és tartományokat, amelyekben a NetBIOS-név egy pontot (.) tartalmaz.

**K: támogatott a tiszta IPv6-környezet?**  
Nem, Azure AD Connect nem támogatja a tiszta IPv6-környezetet.

**Q:I több erdőből álló környezettel és a két erdő közötti hálózattal a NAT (hálózati címfordítás) van használatban. A két erdő között Azure AD Connect használ?**</br>
Nem, Azure AD Connect használata NAT-kapcsolaton keresztül nem támogatott. 

## <a name="federation"></a>Összevonás
**K: mit tegyek, ha olyan e-mailt kapok, amely megkéri a Microsoft 365-tanúsítvány megújítását?**  
A tanúsítvány megújításával kapcsolatos útmutatásért lásd: [tanúsítványok megújítása](how-to-connect-fed-o365-certs.md).

**K: "a függő entitás automatikus frissítése" beállítás van beállítva a Microsoft 365 függő entitáshoz. Kell-e bármilyen műveletet végrehajtani, ha a jogkivonat-aláíró tanúsítványa automatikusan áthalad?**  
Használja a [tanúsítványok megújítása](how-to-connect-fed-o365-certs.md)című cikkben ismertetett útmutatást.

## <a name="environment"></a>Környezet
**K: támogatja a kiszolgáló átnevezését Azure AD Connect telepítését követően?**  
Nem. A kiszolgálónév módosítása azt eredményezi, hogy a Szinkronizáló motor nem tud csatlakozni az SQL Database-példányhoz, és a szolgáltatás nem indítható el.

**K: a következő generációs kriptográfiai (NGC) szinkronizálási szabályok támogatottak egy FIPS-kompatibilis gépen?**  
Nem.  Ez nem támogatott.

**Q. Ha Letiltottam egy szinkronizált eszközt (például: HAADJ) a Azure Portalban, miért van újra engedélyezve?**<br>
A szinkronizált eszközöket lehet létrehozni vagy elsajátítani a helyszínen. Ha egy szinkronizált eszköz van engedélyezve a helyszínen, akkor a Azure Portal még akkor is újra engedélyezheti, ha korábban egy rendszergazda letiltotta. Egy szinkronizált eszköz letiltásához használja a helyszíni Active Directory a számítógépfiók letiltásához.

**Q. Ha letiltom a felhasználói bejelentkezést a Microsoft 365 vagy az Azure AD-portálon a szinkronizált felhasználók számára, miért nem oldja fel a rendszer az ismételt bejelentkezést?**<br>
A szinkronizált felhasználókat a helyszínen lehet létrehozni vagy elsajátítani. Ha a fiók engedélyezve van a helyszínen, akkor feloldja a rendszergazda által elhelyezett bejelentkezési blokk blokkolását.

## <a name="identity-data"></a>Azonosító adatok
**K: Miért nem egyezik a helyszíni egyszerű felhasználónévvel az Azure AD-ben a userPrincipalName (UPN) attribútum?**  
További információt a következő cikkekben talál:

* [A Microsoft 365ban, az Azure-ban vagy az Intune-ban lévő felhasználónevek nem egyeznek meg a helyszíni egyszerű felhasználónévvel vagy a másodlagos bejelentkezési AZONOSÍTÓval](https://mskb.pkisolutions.com/kb/2523192)
* [A Azure Active Directory Sync Tool nem szinkronizálja a módosításokat, miután a felhasználói fiók egyszerű felhasználónevét más összevont tartomány használatára változtatta](https://mskb.pkisolutions.com/kb/2669550)

Az Azure AD-t úgy is beállíthatja, hogy a szinkronizálási motor frissítse az egyszerű felhasználónevet a [Azure ad Connect Sync szolgáltatás szolgáltatásai](how-to-connect-syncservice-features.md)című témakörben leírtak szerint.

**K: támogatja a helyszíni Azure AD-csoport vagy a Contact objektum egy meglévő Azure AD-csoporttal vagy kapcsolatfelvételi objektummal való megegyezését?**  
Igen, ez a puha egyezés a proxyAddress alapul. A Soft Matching nem támogatja a levelezésre nem alkalmas csoportok használatát.

**K: támogatja a ImmutableId attribútum manuális beállítását egy meglévő Azure AD-csoporton vagy egy kapcsolatfelvételi objektumon, hogy az megfeleljen egy helyszíni Azure AD-csoportnak vagy egy Contact objektumnak?**  
Nem, manuálisan állítja be a ImmutableId attribútumot egy meglévő Azure AD-csoporton vagy egy kapcsolattartási objektumon, hogy az megfeleljen. ez jelenleg nem támogatott.

## <a name="custom-configuration"></a>Egyéni konfiguráció
**K: hol vannak a Azure AD Connect PowerShell-parancsmagok dokumentálva?**  
Az ezen a helyen dokumentált parancsmagok kivételével a Azure AD Connectban található további PowerShell-parancsmagok nem támogatottak az ügyfelek általi használatra.

**K: használhatom a "kiszolgálói exportálás/kiszolgáló Importálás" lehetőséget, amely a Synchronization Service Manager található a kiszolgálók közötti konfiguráció áthelyezéséhez?**  
Nem. Ez a beállítás nem kéri le az összes konfigurációs beállítást, és nem használható. Ehelyett a varázslóval hozza létre az alapkonfigurációt a második kiszolgálón, majd a szinkronizálási szabálygyűjtemény használatával PowerShell-parancsfájlokat hozhat létre a kiszolgálók közötti egyéni szabályok áthelyezéséhez. További információ: [swing Migration](how-to-upgrade-previous-version.md#swing-migration).

**K: a jelszavak gyorsítótárazása az Azure bejelentkezési oldalán lehetséges, és a gyorsítótárazás megakadályozható, mert az *automatikus kiegészítés = "false"* attribútummal rendelkező Password input elemet tartalmaz?**  
Jelenleg a **jelszó** mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítés címkét, nem támogatott. Jelenleg olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mezőhöz.

**K: az Azure bejelentkezési oldala azon felhasználók felhasználónevét jeleníti meg, akik korábban sikeresen bejelentkezett. Ki lehet kapcsolni ezt a viselkedést?**  
Jelenleg a **jelszó** beviteli mező HTML-attribútumainak módosítása, beleértve az automatikus kiegészítés címkét, nem támogatott. Jelenleg olyan szolgáltatáson dolgozunk, amely lehetővé teszi az egyéni JavaScript használatát, amely lehetővé teszi bármely attribútum hozzáadását a **jelszó** mezőhöz.

**K: van mód az egyidejű munkamenetek megelőzésére?**  
Nem.

## <a name="auto-upgrade"></a>Automatikus frissítés

**K: milyen előnyökkel jár az automatikus frissítés használata?**  
Javasoljuk, hogy az összes ügyfél számára engedélyezze a Azure AD Connect telepítésének automatikus frissítését. Ennek előnye, hogy mindig megkapja a legújabb javításokat, beleértve a Azure AD Connectban található biztonsági rések biztonsági frissítéseit is. A frissítési folyamat fájdalommentes, és automatikusan megtörténik, amint új verzió érhető el. Több ezer Azure AD Connect ügyfél használja az automatikus frissítést minden új kiadással.

Az automatikus frissítési folyamat mindig először megállapítja, hogy a telepítés jogosult-e az automatikus frissítésre. Ha ez jogosult, a verziófrissítést a rendszer elvégzi és teszteli. A folyamat emellett a szabályok és az adott környezeti tényezők egyéni módosításait is keresi. Ha a tesztek azt mutatják, hogy a frissítés sikertelen, a rendszer automatikusan visszaállítja az előző verziót.

A folyamat a környezet méretétől függően több órát is igénybe vehet. A frissítés folyamatban van, a Windows Server Active Directory és az Azure AD közötti szinkronizálás nem történik meg.

**K: kaptam egy e-mailt, amely azt jelzi, hogy az automatikus frissítés már nem működik, és új verziót kell telepíteni. Miért van erre szükség?**  
A múlt évben a Azure AD Connect egy olyan verzióját adtuk ki, amely bizonyos esetekben letiltotta az automatikus verziófrissítés funkciót a kiszolgálón. Kijavítottuk a problémát Azure AD Connect 1.1.750.0-verzióban. Ha a probléma befolyásolta a problémát, a megoldáshoz PowerShell-parancsfájl futtatásával vagy a Azure AD Connect legújabb verziójára történő manuális frissítéssel csökkentheti. 

A PowerShell-szkript futtatásához [töltse le a parancsfájlt](/samples/browse/?redirectedfrom=TechNet-Gallery) , majd futtassa a Azure ad Connect-kiszolgálón egy felügyeleti PowerShell-ablakban. A szkript futtatásának megismeréséhez [tekintse meg ezt a rövid videót](https://aka.ms/repairaadcau).

A manuális frissítéshez le kell töltenie és futtatnia kell a AADConnect.msi fájl legújabb verzióját.
 
-  Ha a jelenlegi verziója régebbi, mint a 1.1.750.0, [töltse le és frissítse a legújabb verzióra](https://www.microsoft.com/download/details.aspx?id=47594).
- Ha a Azure AD Connect verziója 1.1.750.0 vagy újabb, nincs szükség további műveletre. Már használja az automatikus frissítés javítását tartalmazó verziót. 

**K: kaptam egy e-mailt, amely azt jelzi, hogy a legújabb verzióra kell frissíteni az automatikus frissítés újbóli engedélyezéséhez. A 1.1.654.0 verziót használom. Frissíteni kell?**  
Igen, az automatikus frissítés újbóli engedélyezéséhez frissítenie kell a 1.1.750.0 vagy újabb verzióra. [Töltse le és frissítse a legújabb verzióra](https://www.microsoft.com/download/details.aspx?id=47594).

**K: kaptam egy e-mailt, amely azt jelzi, hogy a legújabb verzióra kell frissíteni az automatikus frissítés újbóli engedélyezéséhez. Ha az automatikus frissítés engedélyezéséhez a PowerShellt használta, továbbra is telepíteni kell a legújabb verziót?**  
Igen, továbbra is frissítenie kell a 1.1.750.0 vagy újabb verzióra. Az automatikus frissítés szolgáltatás a PowerShell-lel való engedélyezése nem csökkenti a verziókban a 1.1.750.0 előtt talált automatikus frissítési problémát.

**K: szeretnék egy újabb verzióra frissíteni, de nem biztos benne, hogy Azure AD Connect telepítettem, és nem rendelkezünk a felhasználónévvel és a jelszóval. Erre van szükségünk?**
Nem kell tudnia a Azure AD Connect frissítéséhez eredetileg használt felhasználónevet és jelszót. Használja a globális rendszergazdai szerepkörrel rendelkező bármely Azure AD-fiókot.

**K: Hogyan lehet megkeresni, hogy a Azure AD Connect melyik verzióját használom?**  
Annak ellenőrzéséhez, hogy a Azure ad Connect melyik verziója van telepítve a kiszolgálón, lépjen a Vezérlőpult elemre, és keresse meg Microsoft Azure ad kapcsolódás telepített verzióját a **programok**  >  **programok és szolgáltatások** elem kiválasztásával, ahogy az itt látható:

![Azure AD Connect verziója a Vezérlőpulton](./media/reference-connect-faq/faq1.png)

**K: Hogyan frissíteni a Azure AD Connect legújabb verziójára?**  
A legújabb verzióra való frissítéssel kapcsolatos további információkért lásd [Azure ad Connect: frissítés egy korábbi verzióról a legújabbra](how-to-upgrade-previous-version.md). 

**K: már frissítve lett az előző év Azure AD Connect legújabb verziójára. Újra kell frissíteni?**  
A Azure AD Connect csapat gyakori frissítéseket tesz elérhetővé a szolgáltatásban. A hibajavítások és a biztonsági frissítések, valamint az új funkciók kihasználása érdekében fontos a kiszolgáló naprakészen tartása a legújabb verzióval. Ha engedélyezi az automatikus frissítést, a szoftver verziója automatikusan frissül. Azure AD Connect verziószámának megkereséséhez tekintse meg a [Azure ad Connect: verzió kiadási előzményeit](reference-connect-version-history.md).

**K: mennyi ideig tart a frissítés végrehajtása, és milyen hatással van a felhasználókra?**  
A frissítéshez szükséges idő a bérlő méretétől függ. Nagyobb szervezetek esetén érdemes lehet az esti vagy hétvégi frissítést elvégezni. A frissítés során nem zajlik szinkronizálási tevékenység.

**K: úgy vélem, hogy Azure AD Connectra Frissítettem, de az Office-portál továbbra is megemlíti az rSync-t. Miért van ez?**  
Az Office-csapat működik az Office-portál frissítéseinek beszerzésére, hogy tükrözze az aktuális terméknév nevét. Nem tükrözi, hogy melyik szinkronizáló eszközt használja.

**K: az automatikus verziófrissítés állapota azt mondja, hogy "Felfüggesztve". Miért van felfüggesztve? Engedélyezem?**  
Egy korábbi verzióban egy hiba lett bevezetve, amely bizonyos körülmények között az automatikus frissítési állapotot "Felfüggesztve" értékre állítja. A manuális engedélyezése technikailag lehetséges, de számos összetett lépést igényel. A legjobb megoldás, ha a Azure AD Connect legújabb verzióját telepíti.

**K: a vállalatom szigorú változás-felügyeleti követelményeket támaszt, és azt szeretném ellenőrizni, hogy mikor van leküldve. Szabályozható, hogy mikor induljon el az automatikus frissítés?**  
Nem, jelenleg nincs ilyen funkció. A szolgáltatás kiértékelése egy későbbi kiadásra történik.

**K: kapok egy e-mailt, ha az automatikus frissítés sikertelen? Honnan tudhatom, hogy sikeres volt?**  
A frissítés eredményét nem fogja értesíteni. A szolgáltatás kiértékelése egy későbbi kiadásra történik.

**K: az automatikus frissítések kiküldésének megtervezéséhez ütemtervet kell közzétenni?**  
Az automatikus frissítés az újabb verzió kiadási folyamatának első lépése. Ha új kiadás van, a rendszer automatikusan leküldi a frissítéseket. A Azure AD Connect újabb verziói előre be vannak jelentve az [Azure ad-ütemtervben](../fundamentals/whats-new.md).

**K: az automatikus frissítés is frissíti Azure AD Connect Health?**  
Igen, az automatikus frissítés frissíti a Azure AD Connect Healtht is.

**K: az Azure AD Connect-kiszolgálók automatikus frissítése átmeneti módban is?**  
Igen, egy átmeneti módban lévő Azure AD Connect-kiszolgáló automatikus frissítése is végezhető.

**K: Ha az automatikus frissítés meghiúsul, és a Azure AD Connect-kiszolgáló nem indul el, mi a teendő?**  
Ritka esetekben a Azure AD Connect szolgáltatás nem indul el a frissítés végrehajtása után. Ezekben az esetekben a kiszolgáló újraindítása általában kijavítja a problémát. Ha a Azure AD Connect szolgáltatás még nem indul el, nyisson meg egy támogatási jegyet. További információ: [Microsoft 365 támogatási szolgáltatáshoz való kapcsolódási kérelem létrehozása](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support). 

**K: nem tudom, mi a kockázat, amikor a Azure AD Connect újabb verziójára frissítek. Meg tud hívni nekem, hogy segítsenek a frissítésben?**  
Ha segítségre van szüksége a Azure AD Connect újabb verziójára való frissítéshez, nyisson meg egy támogatási jegyet a [szolgáltatási kérelem létrehozásakor, hogy kapcsolatba lépjen Microsoft 365 támogatási szolgálattal](/archive/blogs/praveenkumar/how-to-create-service-requests-to-contact-office-365-support).

## <a name="operational-best-practice"></a>Operatív ajánlott eljárás    
Az alábbiakban ismertetjük a Windows Server Active Directory és Azure Active Directory közötti szinkronizálás során megvalósított ajánlott eljárásokat.

**Multi-Factor Authentication alkalmazása az összes szinkronizált fiókra** Az Azure AD Multi-Factor Authentication segít megőrizni az adathozzáférést és az alkalmazásokat, miközben a felhasználók számára egyszerűséget biztosít. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést tesz lehetővé számos könnyen használható hitelesítési módszer segítségével. A rendszergazda által létrehozott konfigurációs döntések alapján előfordulhat, hogy a felhasználók nem tudják feltámadni az MFA-t. Az MFA-ról itt talál további információt: https://www.microsoft.com/security/business/identity/mfa?rtc=1

**Kövesse a Azure ad Connect-kiszolgáló biztonsági irányelveit** A Azure AD Connect kiszolgáló kritikus identitási adatokból áll, és 0. rétegű összetevőként kell kezelni, ahogy azt az [Active Directory felügyeleti csomag modelljében](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)dokumentálták. Tekintse meg a [AADConnect-kiszolgáló biztonságossá tételére vonatkozó irányelveket](./how-to-connect-install-prerequisites.md#azure-ad-connect-server)is.

**A PHS engedélyezése a kiszivárgott hitelesítő adatok észleléséhez** A jelszó-kivonatolási szinkronizálás lehetővé teszi a [kiszivárgott hitelesítő adatok észlelését](../identity-protection/concept-identity-protection-risks.md) is a hibrid fiókoknál. A Microsoft együtt működik a sötét webes kutatókkal és a bűnüldöző szervekkel nyilvánosan elérhető felhasználónevek és jelszavak párok kereséséhez. Ha ezek közül bármelyik pár megegyezik a felhasználókkal, a rendszer a társított fiókot magas kockázatra helyezi át. 


## <a name="troubleshooting"></a>Hibaelhárítás
**K: Hogyan Kérhetek segítséget a Azure AD Connect?**

[Keresés a Microsoft Tudásbázisban (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Az Azure AD Connect támogatásával kapcsolatos gyakori hibajavítási hibákért keresse meg a KB-os technikai megoldásokat.

[A Microsoft Q&egy kérdés oldalt Azure Active Directory](/answers/topics/azure-active-directory.html)

* [Az Azure ad-Közösségre](/answers/topics/azure-active-directory.html)kattintva technikai kérdéseket és válaszokat kereshet, vagy megteheti a saját kérdéseit.

[Támogatás kérése az Azure AD-hez](../fundamentals/active-directory-troubleshooting-support-howto.md)

**K: Miért jelenik meg a 6311-es és a 6401-es esemény a szinkronizálási lépés hibája után?**

A 6311-as események – **a kiszolgáló váratlan hibát észlelt a visszahívás** és a 6401 végrehajtása során – **a felügyeleti ügynök vezérlő váratlan hibát észlelt** – a szinkronizálási lépés hibája után mindig naplózva lesz. A hibák elhárításához törölnie kell a szinkronizálási lépés hibáit.  További információ: [hibaelhárítási hibák a szinkronizálás során](tshoot-connect-sync-errors.md) és az [objektumok szinkronizálásának hibaelhárítása Azure ad Connect szinkronizálással](tshoot-connect-objectsync.md)