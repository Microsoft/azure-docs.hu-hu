---
title: 'Azure AD Connect: áttelepítés az Azure AD-ből az összevonásról a PTA-re'
description: Ebből a cikkből megtudhatja, hogyan helyezheti át hibrid identitási környezetét az összevonás és az átmenő hitelesítés között.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10c4c0e6e40636e4803e054155d6fdaa12a9366
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858552"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrálás az összevonásból a Azure Active Directory áteresztő hitelesítésre

Ez a cikk azt ismerteti, hogyan helyezheti át szervezeti tartományait Active Directory összevonási szolgáltatások (AD FS) (AD FS) áteresztő hitelesítésre.

> [!NOTE]
> A hitelesítési módszer megváltoztatásához tervezési, tesztelési és lehetséges állásidő szükséges. Az [előkészített](how-to-connect-staged-rollout.md) bevezetéssel az áteresztő hitelesítéssel alternatív módszerekkel lehet tesztelni és fokozatosan áttérni az összevonás és a felhő közötti hitelesítésre.
> 
> Ha lépcsőzetes bevezetést használ, ne feledje, hogy kikapcsolja a szakaszos bevezetési funkciókat, miután befejezte a kivágást.  További információ: [Migrálás felhőalapú hitelesítésre szakaszos bevezetés használatával](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Az átmenő hitelesítésre való Migrálás előfeltételei

A következő előfeltételek szükségesek az áttelepítéshez a AD FS használatával áteresztő hitelesítés használatával.

### <a name="update-azure-ad-connect"></a>Azure AD Connect frissítése

Az átmenő hitelesítés használatával történő áttelepítéshez szükséges lépések sikeres végrehajtásához [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure ad Connect) 1.1.819.0 vagy újabb verziójúnak kell lennie. Azure AD Connect 1.1.819.0 a bejelentkezések átalakításának módja jelentősen megváltozik. A AD FSról a felhőbe történő Migrálás teljes ideje a jelen verzióban akár percek alatt is elvégezhető.

> [!IMPORTANT]
> Előfordulhat, hogy az elavult dokumentációban, eszközökben és blogokban a felhasználók átalakítására van szükség, amikor a tartományokat összevont identitásról felügyelt identitásra konvertálja. A *felhasználók konvertálása* már nem szükséges. A Microsoft dolgozik a dokumentáció és az eszközök frissítésén, hogy tükrözze ezt a változást.

Azure AD Connect frissítéséhez hajtsa végre a [Azure ad Connect: frissítés a legújabb verzióra](./how-to-upgrade-previous-version.md)című témakör lépéseit.

### <a name="plan-authentication-agent-number-and-placement"></a>A hitelesítési ügynök számának és elhelyezésének megtervezése

Az átmenő hitelesítéshez egyszerűsített ügynököket kell telepíteni a Azure AD Connect-kiszolgálóra és a Windows Servert futtató helyszíni számítógépre. A késés csökkentése érdekében telepítse az ügynököket a lehető legközelebb a Active Directory tartományvezérlőkre.

A legtöbb ügyfél esetében két vagy három hitelesítési ügynök elegendő a magas rendelkezésre állás és a szükséges kapacitás biztosításához. A bérlők legfeljebb 12 ügynököt regisztrálhatnak. Az első ügynök mindig a Azure AD Connect-kiszolgálóra van telepítve. Az ügynök korlátozásait és az ügynök üzembe helyezési lehetőségeit az [Azure ad átmenő hitelesítés: jelenlegi korlátozások](./how-to-connect-pta-current-limitations.md)című részben tekintheti meg.

### <a name="plan-the-migration-method"></a>Az áttelepítési módszer megtervezése

Két módszer közül választhat, amelyek áttelepíthetők az összevont identitások kezeléséről az átmenő hitelesítésre és a zökkenőmentes egyszeri bejelentkezésre (SSO). Az Ön által használt módszer attól függ, hogy a AD FS-példányt eredetileg konfigurálták-e.

* **Azure AD Connect**. Ha eredetileg Azure AD Connect használatával konfigurálta a AD FSt, akkor a Azure AD Connect varázslóval át *kell* váltania az átmenő hitelesítésre.

   A Azure AD Connect automatikusan futtatja a **set-MsolDomainAuthentication** parancsmagot a felhasználói bejelentkezési módszer módosításakor. Azure AD Connect automatikusan unfederates az összes ellenőrzött összevont tartományt az Azure AD-bérlőben.

   > [!NOTE]
   > Jelenleg, ha a Azure AD Connectt a AD FS konfigurálására használta, nem kerülheti el a bérlő összes tartományát, ha a felhasználói bejelentkezést az átmenő hitelesítés unfederating módosítja.
‎
* **Azure ad Connect a PowerShell**-lel. Ezt a módszert csak akkor használhatja, ha a Azure AD Connect használatával eredetileg nem konfigurálta AD FS. Ennél a lehetőségnél továbbra is meg kell változtatnia a felhasználói bejelentkezési módszert a Azure AD Connect varázslón keresztül. Ezzel a beállítással a fő különbség az, hogy a varázsló nem futtatja automatikusan a **set-MsolDomainAuthentication** parancsmagot. Ezzel a beállítással teljes mértékben szabályozhatja, hogy mely tartományok konvertálása és milyen sorrendben történjen.

A következő részben ismertetett lépéseket követve megtudhatja, hogy melyik módszert használja.

#### <a name="verify-current-user-sign-in-settings"></a>Aktuális felhasználói bejelentkezési beállítások ellenőrzése

1. Jelentkezzen be az [Azure ad-portálra](https://aad.portal.azure.com/) egy globális rendszergazdai fiók használatával.
2. A **felhasználói bejelentkezés** szakaszban ellenőrizze az alábbi beállításokat:
   * Az **összevonás** **engedélyezve** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **Letiltva** értékre van állítva.
   * Az **átmenő hitelesítés** **Letiltva** értékre van állítva.

   ![Képernyőkép a Azure AD Connect felhasználói bejelentkezési szakaszban található beállításokról](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Az összevonás konfigurálási módjának ellenőrzése

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect. Válassza a **Konfigurálás** lehetőséget.
2. A **További feladatok** lapon válassza a **jelenlegi konfiguráció megtekintése** lehetőséget, majd kattintson a **tovább** gombra.<br />
 
   ![Képernyőkép az aktuális konfiguráció megtekintése lehetőségről a további feladatok oldalon](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Az **összevonás kezelése > további feladatok** területen görgessen a **Active Directory összevonási szolgáltatások (AD FS) (AD FS)** elemre.<br />

   * Ha a AD FS konfiguráció megjelenik ebben a szakaszban, nyugodtan feltételezheti, hogy AD FS eredetileg Azure AD Connect használatával lett konfigurálva. A tartományokat összevont identitásról felügyelt identitásra konvertálhatja a **felhasználói bejelentkezés Azure ad Connect módosítása** lehetőség használatával. A folyamattal kapcsolatos további információkért tekintse meg a következő szakaszt **: az átmenő hitelesítés konfigurálása Azure ad Connect használatával**.
   * Ha AD FS nem szerepel az aktuális beállítások listáján, akkor a PowerShell használatával manuálisan kell konvertálnia a tartományokat összevont identitásról a felügyelt identitásra. A folyamattal kapcsolatos további információkért tekintse meg a **B. lehetőséget: Váltás az összevonásról áteresztő hitelesítésre Azure ad Connect és a PowerShell használatával**.

### <a name="document-current-federation-settings"></a>Az aktuális összevonási beállítások dokumentálása

Az aktuális összevonási beállítások megkereséséhez futtassa a **Get-MsolDomainFederationSettings** parancsmagot:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Példa:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Ellenőrizze, hogy vannak-e testreszabott beállítások az összevonási tervezési és telepítési dokumentációhoz. Pontosabban keresse meg a testreszabásokat a **PreferredAuthenticationProtocol**, a **SupportsMfa** és a **PromptLoginBehavior**.

További információért lásd a következő cikkeket:

* [AD FS prompt = bejelentkezési paraméter támogatása](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Ha a **SupportsMfa** értéke **true (igaz**), a rendszer helyszíni multi-Factor Authentication megoldást használ egy második tényezős kihívás beadására a felhasználói hitelesítési folyamatba. Ez a beállítás már nem működik az Azure AD-hitelesítési forgatókönyvek esetében. 
>
> Ehelyett az Azure AD Multi-Factor Authentication felhőalapú szolgáltatásával végezze el ugyanezt a funkciót. A folytatás előtt alaposan értékelje ki a multi-Factor Authentication követelményeit. A tartományok konvertálása előtt meg kell ismernie az Azure AD Multi-Factor Authentication használatát, a licenceléssel kapcsolatos szempontokat és a felhasználói regisztráció folyamatát.

#### <a name="back-up-federation-settings"></a>Összevonási beállítások biztonsági mentése

Bár a jelen cikkben ismertetett folyamatok során a AD FS Farm többi függő entitása nem módosult, javasoljuk, hogy a AD FS Farm aktuálisan érvényes biztonsági másolatával rendelkezzen, amelyről visszaállíthatók. Az ingyenes Microsoft [AD FS Rapid Restore Tool](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)használatával létrehozhat egy aktuálisan érvényes biztonsági mentést. Használhatja az eszközt a AD FS biztonsági mentésére, illetve egy meglévő Farm visszaállítására vagy egy új Farm létrehozására.

Ha úgy dönt, hogy nem használja a AD FS gyors visszaállítás eszközt, akkor exportálnia kell az Microsoft 365 Identity platform függő entitás megbízhatóságát és a hozzá társított egyéni jogcím-szabályokat. A függő entitás megbízhatóságát és a hozzá tartozó jogcím-szabályokat a következő PowerShell-példa használatával exportálhatja:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Az üzembe helyezés szempontjai és a AD FS használata

Ez a szakasz a telepítési szempontokat és a AD FS használatának részleteit ismerteti.

### <a name="current-ad-fs-use"></a>Aktuális AD FS-használat

Az összevont identitásról felügyelt identitásra való áttérés előtt tekintse meg az Azure AD-hez, Microsoft 365hoz és más alkalmazásokhoz (függő entitások megbízhatóságának) való használatának AD FSét. Pontosabban vegye figyelembe az alábbi táblázatban ismertetett forgatókönyveket:

| Ha a(z) | Majd |
|-|-|
| A AD FS használatát tervezi más alkalmazásokkal (az Azure AD és a Microsoft 365 kivételével). | A tartományok konvertálása után AD FS és Azure AD-t is használhat. Vegye figyelembe a felhasználói élményt. Bizonyos esetekben előfordulhat, hogy a felhasználóknak kétszer kell megadniuk a hitelesítést: egyszer az Azure AD-be (ahol a felhasználó SSO-hozzáférést kap más alkalmazásokhoz, például Microsoft 365), és újra minden olyan alkalmazáshoz, amely továbbra is a függő entitás megbízhatóságának AD FS van kötve. |
| A AD FS-példánya nagymértékben testre szabható, és a onload.js fájl adott testreszabási beállításaira támaszkodik (például ha megváltoztatta a bejelentkezési folyamatot, hogy a felhasználók csak az egyszerű felhasználónév (UPN) helyett a **sAMAccountName** használják a felhasználónevet, vagy ha a szervezete jelentősen kihasználta a bejelentkezési élményt). Az onload.js fájlt nem lehet duplikálni az Azure AD-ben. | A folytatás előtt ellenőriznie kell, hogy az Azure AD megfelel-e az aktuális testreszabási követelményeknek. További információért és útmutatásért tekintse meg a AD FS branding és a AD FS testreszabása című szakaszt.|
| AD FS használatával blokkolhatja a hitelesítési ügyfelek korábbi verzióit.| A [feltételes hozzáférés-vezérlés](../conditional-access/concept-conditional-access-conditions.md) és az [Exchange Online ügyfél-hozzáférési szabályok](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)együttes használatával vegye figyelembe a hitelesítési ügyfelek korábbi verzióit letiltó AD FS vezérlőket. |
| A felhasználóknak a többtényezős hitelesítést kell végrehajtaniuk a helyszíni multi-Factor Authentication kiszolgálói megoldáson, amikor a felhasználók hitelesítik AD FS.| Felügyelt identitási tartományban a többtényezős hitelesítési kihívás a helyszíni multi-Factor Authentication megoldáson keresztül nem szúrható be a hitelesítési folyamatba. A tartomány átalakítása után azonban használhatja az Azure AD Multi-Factor Authentication szolgáltatást a többtényezős hitelesítéshez.<br /><br /> Ha a felhasználók jelenleg nem használják az Azure AD Multi-Factor Authentication, egy egyszeri bejelentkezést igénylő felhasználói regisztráció lépésre van szükség. Elő kell készítenie és továbbítania kell a tervezett regisztrációt a felhasználók számára. |
| Jelenleg a AD FS hozzáférés-vezérlési házirendjeit (AuthZ-szabályok) használja a Microsoft 365hoz való hozzáférés szabályozásához.| Érdemes lehet a szabályzatokat az egyenértékű Azure AD [feltételes hozzáférési szabályzatokkal](../conditional-access/overview.md) és az [Exchange Online ügyfél-hozzáférési szabályokkal](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)helyettesíteni.|

### <a name="common-ad-fs-customizations"></a>Gyakori AD FS testreszabások

Ez a szakasz a gyakori AD FS testreszabásokat ismerteti.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork jogcím

AD FS kiadja a **InsideCorporateNetwork** jogcímet, ha a hitelesítést végző felhasználó a vállalati hálózaton belül van. Ezt követően a jogcím átadható az Azure AD-nek. A rendszer a felhasználó hálózati helye alapján a többtényezős hitelesítés megkerülésére használatos. Ha meg szeretné tudni, hogyan állapítható meg, hogy ez a funkció jelenleg elérhető-e a AD FSban, tekintse meg [az összevont felhasználók megbízható IP](../authentication/howto-mfa-adfs.md)-címei

A **InsideCorporateNetwork** jogcím nem érhető el, miután a tartományok át lettek alakítva átmenő hitelesítésre. Az [Azure ad elnevezett helyeinek](../reports-monitoring/quickstart-configure-named-locations.md) használatával lecserélheti a funkciót.

A nevesített helyek konfigurálása után frissítenie kell az összes olyan feltételes hozzáférési házirendet, amely konfigurálva lett a hálózat **minden megbízható hely** vagy **MFA megbízható IP** -cím értékének befoglalására vagy kizárására az új elnevezett helyeknek megfelelően.

A feltételes hozzáférés **hely** feltételével kapcsolatos további információkért lásd: [Active Directory feltételes hozzáférési helyek](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök

Amikor csatlakoztat egy eszközt az Azure AD-hoz, létrehozhat feltételes hozzáférési szabályokat, amelyek kikényszerítik, hogy az eszközök megfeleljenek a biztonsági és megfelelőségi hozzáférési előírásoknak. A felhasználók személyes fiók helyett szervezeti munkahelyi vagy iskolai fiókkal is bejelentkezhetnek az eszközre. Hibrid Azure AD-hez csatlakoztatott eszközök használata esetén Active Directory tartományhoz csatlakoztatott eszközöket csatlakoztathat az Azure AD-hez. Lehetséges, hogy az összevont környezet a szolgáltatás használatára van beállítva.

Annak biztosítása érdekében, hogy a hibrid csatlakoztatások továbbra is működjenek a tartományhoz csatlakoztatott összes eszközön, miután a tartományok áteresztő hitelesítésre lettek konvertálva Windows 10-es ügyfelek esetén, a Azure AD Connect használatával kell szinkronizálnia Active Directory számítógépfiókokat az Azure AD-be.

A Windows 8 és a Windows 7 rendszerű számítógépfiókok esetében a hibrid illesztés zökkenőmentes egyszeri bejelentkezést használ a számítógép Azure AD-ben való regisztrálásához. A Windows 8 és a Windows 7 rendszerű számítógépek fiókjait nem kell szinkronizálnia, mint a Windows 10-es eszközökhöz. A Windows 8 és a Windows 7 rendszerű ügyfelek esetében azonban frissítenie kell egy frissített workplacejoin.exe-fájlt (. msi-fájlon keresztül), hogy a zökkenőmentes SSO használatával regisztrálja magukat. [Töltse le az. msi fájlt](https://www.microsoft.com/download/details.aspx?id=53554).

További információ: [hibrid Azure ad-hez csatlakoztatott eszközök konfigurálása](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Arculat

Ha a szervezete [testreszabja a AD FS bejelentkezési oldalain](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) a szervezettel kapcsolatos információk megjelenítéséhez, érdemes lehet hasonló [testreszabásokat felvenni az Azure ad bejelentkezési oldalára](../fundamentals/customize-branding.md).

Bár a hasonló testreszabások elérhetők, a bejelentkezési lapokon a konverziót követően néhány vizualizációs változást is el kell várni. Előfordulhat, hogy meg kívánja adni a kommunikáció várható változásaival kapcsolatos információkat a felhasználók számára.

> [!NOTE]
> A szervezet arculata csak akkor érhető el, ha a prémium szintű vagy alapszintű licencet vásárolja meg Azure Active Directory vagy Microsoft 365 licenccel rendelkezik.

## <a name="plan-for-smart-lockout"></a>Tervezze meg az intelligens zárolást

Az Azure AD intelligens zárolása védelmet biztosít a találgatásos jelszavak elleni támadásokkal szemben. Az intelligens zárolás megakadályozza, hogy a helyszíni Active Directory fiók zárolva legyen, ha az átmenő hitelesítés használatban van, és a fiókzárolási csoportházirend be van állítva a Active Directory.

További információ: [Azure Active Directory Smart zárolás](../authentication/howto-password-smart-lockout.md).

## <a name="plan-deployment-and-support"></a>Üzembe helyezés és támogatás tervezése

A jelen szakaszban ismertetett feladatok végrehajtásával megkönnyítheti a telepítés és a támogatás megtervezését.

### <a name="plan-the-maintenance-window"></a>A karbantartási időszak megtervezése

Bár a tartomány-átalakítási folyamat viszonylag gyors, az Azure AD továbbra is küldhet bizonyos hitelesítési kéréseket a AD FS-kiszolgálóknak legfeljebb négy órával a tartomány átalakításának befejeződése után. Ez a négy órás időszak alatt, a különböző szolgáltatási oldali gyorsítótártól függően előfordulhat, hogy az Azure AD nem fogadja el ezeket a hitelesítést. Előfordulhat, hogy a felhasználók hibaüzenetet kapnak. A felhasználó továbbra is sikeresen tud hitelesíteni a AD FSon, de az Azure AD már nem fogadja el a felhasználó kiállított jogkivonatát, mert az összevonási megbízhatóság megszűnt.

Csak azok a felhasználók férhetnek hozzá a szolgáltatáshoz, akik egy webböngészőn keresztül férnek hozzá a szolgáltatáshoz a konvertálás utáni időszakban. Az örökölt ügyfeleket (Exchange ActiveSync, Outlook 2010/2013) nem érinti a rendszer, mert az Exchange Online a hitelesítő adataik gyorsítótárát tárolja egy meghatározott időtartamra. A gyorsítótár a felhasználó csendes újrahitelesítésére szolgál. A felhasználónak nem kell visszatérnie AD FShoz. Ezeknek az ügyfeleknek az eszközön tárolt hitelesítő adatai a gyorsítótárazott adatok törlését követően csendben újra hitelesíthetők. A felhasználók a tartomány-átalakítási folyamat eredményeképpen nem kapják meg a jelszó kéréseit.

A modern hitelesítési ügyfelek (Office 2016 és Office 2013, iOS és Android rendszerű alkalmazások) érvényes frissítési jogkivonattal szerezhetnek be új hozzáférési jogkivonatokat az erőforrásokhoz való folyamatos hozzáféréshez ahelyett, hogy az AD FS-re térnek vissza. Ezek az ügyfelek a tartomány-átalakítási folyamat során felmerülő összes jelszóra vonatkoznak. Az ügyfelek további konfigurálás nélkül továbbra is működőképesek lesznek.

> [!IMPORTANT]
> Ne állítsa le a AD FS környezetet, vagy távolítsa el az Microsoft 365 függő entitás megbízhatóságát, amíg nem ellenőrizte, hogy az összes felhasználó sikeresen tud-e hitelesíteni a felhőalapú hitelesítés használatával.

### <a name="plan-for-rollback"></a>A visszaállítás megtervezése

Ha olyan jelentős problémát tapasztal, amelyet nem tud gyorsan feloldani, dönthet úgy, hogy visszaállítja a megoldást az összevonásra. Fontos megtervezni, mi a teendő, ha a központi telepítés nem a kívánt módon van kiépítve. Ha a tartomány vagy a felhasználók átalakítása meghiúsul az üzembe helyezés során, vagy ha vissza kell térnie az összevonáshoz, meg kell ismernie, hogyan csökkentheti az esetleges kimaradásokat, és csökkentheti a felhasználókra gyakorolt hatást.

#### <a name="to-roll-back"></a>Visszaállítás

A visszaállítás megtervezéséhez olvassa el az összevonási tervezési és telepítési dokumentációt az adott központi telepítés részleteinek megtekintéséhez. A folyamatnak tartalmaznia kell ezeket a feladatokat:

* Felügyelt tartományok átalakítása összevont tartományokra a **Convert-MSOLDomainToFederated** parancsmag használatával.
* Ha szükséges, konfigurálja a további jogcímek szabályait.

### <a name="plan-communications"></a>A kommunikáció tervezése

Az üzembe helyezés és a támogatás megtervezésének fontos része annak biztosítása, hogy a felhasználók proaktívan tájékoztassanak a közelgő változásokról. A felhasználóknak előre ismerniük kell, hogy milyen élményt jelenthetnek, és mire van szükségük.

Az átmenő hitelesítés és a zökkenőmentes SSO üzembe helyezése után a felhasználói bejelentkezés az Azure AD változásain keresztül hitelesített Microsoft 365 és egyéb erőforrások elérését is eredményezi. A hálózaton kívüli felhasználók csak az Azure AD bejelentkezési oldalát látják. Ezeket a felhasználókat a rendszer nem irányítja át a külső elérésű webalkalmazás-proxy kiszolgálók által megjelenített űrlapalapú oldalra.

Adja meg a következő elemeket a kommunikációs stratégiában:

* Értesítse a felhasználókat a közelgő és kiadott funkciókról a következő használatával:
   * E-mailek és egyéb belső kommunikációs csatornák.
   * Vizualizációk, például plakátok.
   * Executive, Live vagy más kommunikáció.
* Határozza meg, hogy ki fogja testre szabni a kommunikációt, és ki fogja elküldeni a kommunikációt, és mikor.

## <a name="implement-your-solution"></a>A megoldás implementálása

Megtervezte a megoldást. Most már elvégezheti a megvalósítást. A megvalósítás a következő összetevőket foglalja magában:

* Felkészülés a zökkenőmentes egyszeri bejelentkezésre.
* A bejelentkezési módszer áteresztő hitelesítésre való módosítása és a zökkenőmentes egyszeri bejelentkezés engedélyezése.

### <a name="step-1-prepare-for-seamless-sso"></a>1. lépés: Felkészülés a zökkenőmentes egyszeri bejelentkezésre

Ahhoz, hogy az eszközök zökkenőmentes egyszeri bejelentkezést használjanak, hozzá kell adnia egy Azure AD-URL-címet a felhasználók intranetes zónájának beállításaihoz egy Active Directory csoportházirend használatával.

Alapértelmezés szerint a böngészők automatikusan kiszámítják a megfelelő zónát (Internet vagy intranet) egy URL-címről. Például **http: \/ \/ contoso/** maps to the intranet zóna és **http: \/ \/ intranet.contoso.com** maps to the Internet Zone (mivel az URL-cím egy pontot tartalmaz). A böngészők Kerberos-jegyeket küldenek egy Felhőbeli végpontra (például az Azure AD URL-címére), ha explicit módon hozzáadja az URL-címet a böngésző intranetes zónájához.

Végezze [el az](./how-to-connect-sso-quick-start.md) eszközök szükséges módosításainak lépéseit.

> [!IMPORTANT]
> Ennek a módosításnak a végrehajtásával nem módosítható, hogy a felhasználók hogyan jelentkeznek be az Azure AD-be. Azonban fontos, hogy a folytatás előtt alkalmazza ezt a konfigurációt az összes eszközre. Azokat a felhasználókat, akik nem kaptak ezt a konfigurációt, egyszerűen be kell írniuk egy felhasználónevet és jelszót az Azure AD-be való bejelentkezéshez.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>2. lépés: a bejelentkezési módszer módosítása átmenő hitelesítésre és a zökkenőmentes egyszeri bejelentkezés engedélyezése

Két lehetősége van a bejelentkezési módszer áteresztő hitelesítésre való módosítására és a zökkenőmentes egyszeri bejelentkezés engedélyezésére.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>A. lehetőség: átmenő hitelesítés konfigurálása Azure AD Connect használatával

Ezt a módszert akkor használja, ha a Azure AD Connect használatával először konfigurálta AD FS-környezetét. Ez a módszer nem használható, ha a Azure AD Connect használatával eredetileg *nem* konfigurálta AD FS-környezetét.

> [!IMPORTANT]
> Az alábbi lépések elvégzése után a rendszer az összes tartományt összevont identitásról felügyelt identitásra konvertálja. További információkért tekintse át [az áttelepítési módszer megtervezése](#plan-the-migration-method)című témakört.

Először módosítsa a bejelentkezési módszert:

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect varázslót.
2. Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra. 
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **felhasználói bejelentkezés** lapon válassza az **átmenő hitelesítés** gombot, jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet, majd kattintson a **tovább** gombra.
5. Az **egyszeri bejelentkezés engedélyezése** lapon adja meg egy tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a **tovább** gombra.

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés engedélyezéséhez tartományi rendszergazdai fiók hitelesítő adatai szükségesek. A folyamat a következő műveleteket hajtja végre, ehhez emelt szintű engedélyekre van szükség. A tartományi rendszergazdai fiók hitelesítő adatai nem Azure AD Connect vagy az Azure AD-ben tárolódnak. A tartományi rendszergazdai fiók hitelesítő adatai csak a funkció bekapcsolására használhatók. A rendszer a folyamat sikeres befejeződése után elveti a hitelesítő adatokat.
   >
   > 1. A helyszíni Active Directory-példányban létrejön egy AZUREADSSOACC nevű számítógépfiók (amely az Azure AD-t jelöli).
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos egyszerű szolgáltatásnév (SPN) jön létre, amelyek az Azure AD-bejelentkezés során használt két URL-címet jelölik.

6. A **készen áll a konfigurálásra** lapon jelölje be a **szinkronizálási folyamat elindítása a konfiguráció befejeződése** után jelölőnégyzetet. Ezután válassza a **Konfigurálás** lehetőséget.<br />

   ![A konfigurálásra kész lap képernyőképe](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Az Azure AD-portálon válassza a **Azure Active Directory** lehetőséget, majd válassza a **Azure ad Connect** lehetőséget.
8. Ellenőrizze ezeket a beállításokat:
   * Az **összevonás** **Letiltva** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **engedélyezve** értékre van állítva.
   * Az **átmenő hitelesítés** **engedélyezve** értékre van állítva.<br />

   ![A felhasználói bejelentkezési szakaszban található beállításokat megjelenítő képernyőkép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Tovább gombra. További hitelesítési módszerek üzembe helyezése:

1. A Azure Portal lépjen a **Azure Active Directory**  >  **Azure ad Connect** elemre, majd válassza az **átmenő hitelesítés** lehetőséget.
2. Az **átmenő hitelesítés** lapon kattintson a **Letöltés** gombra.
3. Az **ügynök letöltése** lapon válassza a **feltételek elfogadása és a letöltés** lehetőséget.

   A további hitelesítési ügynökök megkezdik a letöltést. Telepítse a másodlagos hitelesítési ügynököt egy tartományhoz csatlakoztatott kiszolgálóra. 

   > [!NOTE]
   > Az első ügynököt mindig a Azure AD Connect-kiszolgálóra telepíti a Azure AD Connect eszköz **felhasználói bejelentkezés** szakaszában megadott konfigurációs módosítások részeként. Telepítsen minden további hitelesítési ügynököt egy külön kiszolgálóra. Javasoljuk, hogy két vagy három további hitelesítési ügynök legyen elérhető. 

4. Futtassa a hitelesítési ügynök telepítését. A telepítés során meg kell adnia egy globális rendszergazdai fiók hitelesítő adatait.

   ![Képernyőfelvétel: a Microsoft Azure AD kapcsolódási ügynök csomagjának futtatásához használt telepítési gomb.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![A Microsoft bejelentkezési oldalát bemutató képernyőkép.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. A hitelesítési ügynök telepítése után visszatérhet a továbbított hitelesítési ügynök állapota lapra, ahol megtekintheti a további ügynökök állapotát.

Ugorjon a [tesztelés és a további lépések](#testing-and-next-steps)elemre.

> [!IMPORTANT]
> Ugorja át a " **B" lehetőséget: váltson át az összevonás és az átmenő hitelesítés Azure ad Connect és a PowerShell használatával**. Az ebben a szakaszban ismertetett lépések nem érvényesek, ha az A kapcsolót választotta a bejelentkezési módszer áteresztő hitelesítésre való módosítására és a zökkenőmentes egyszeri bejelentkezés engedélyezésére. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>B. lehetőség: Váltás az összevont hitelesítésre a Azure AD Connect és a PowerShell használatával

Akkor használja ezt a beállítást, ha a Azure AD Connect használatával eredetileg nem konfigurálta az összevont tartományokat.

Először engedélyezze az átmenő hitelesítést:

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect varázslót.
2. Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra.
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **felhasználói bejelentkezés** lapon válassza az **átmenő hitelesítés** gombot. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet, majd kattintson a **tovább** gombra.
5. Az **egyszeri bejelentkezés engedélyezése** lapon adja meg egy tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a **tovább** gombra.

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés engedélyezéséhez tartományi rendszergazdai fiók hitelesítő adatai szükségesek. A folyamat a következő műveleteket hajtja végre, ehhez emelt szintű engedélyekre van szükség. A tartományi rendszergazdai fiók hitelesítő adatai nem Azure AD Connect vagy az Azure AD-ben tárolódnak. A tartományi rendszergazdai fiók hitelesítő adatai csak a funkció bekapcsolására használhatók. A rendszer a folyamat sikeres befejeződése után elveti a hitelesítő adatokat.
   > 
   > 1. A helyszíni Active Directory-példányban létrejön egy AZUREADSSOACC nevű számítógépfiók (amely az Azure AD-t jelöli).
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos egyszerű szolgáltatásnév (SPN) jön létre, amelyek az Azure AD-bejelentkezés során használt két URL-címet jelölik.

6. A **készen áll a konfigurálásra** lapon jelölje be a **szinkronizálási folyamat elindítása a konfiguráció befejeződése** után jelölőnégyzetet. Ezután válassza a **Konfigurálás** lehetőséget.<br />

   ![A konfigurálásra kész lapra és a configure (Konfigurálás) gombra megjelenítő képernyőkép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   A **configure (Konfigurálás**) gombra kattintva a következő lépések végezhetők el:

   1. Az első áteresztő hitelesítési ügynök telepítve van.
   2. Az áteresztő funkció engedélyezve van.
   3. A zökkenőmentes egyszeri bejelentkezés engedélyezve van.

7. Ellenőrizze ezeket a beállításokat:
   * Az **összevonás** **engedélyezve** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **engedélyezve** értékre van állítva.
   * Az **átmenő hitelesítés** **engedélyezve** értékre van állítva.
   
   ![Képernyőkép, amely a felhasználói bejelentkezés szakaszban megjelenő beállításokat jeleníti meg.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Válassza az **átmenő hitelesítés** lehetőséget, és ellenőrizze, hogy az állapot **aktív**-e.<br />
   
   Ha a hitelesítési ügynök nem aktív, hajtson végre néhány [hibaelhárítási lépést](./tshoot-connect-pass-through-authentication.md) a következő lépésben a tartomány-átalakítási folyamat folytatása előtt. Ha a tartomány konvertálása előtt a hitelesítés leáll, a rendszer az áteresztő hitelesítési ügynökök sikeres telepítése és a Azure Portal **aktív** állapotának ellenőrzése előtt veszélyezteti a hitelesítési kimaradást.

Következő lépésként telepítsen további hitelesítési ügynököket:

1. A Azure Portal lépjen a **Azure Active Directory**  >  **Azure ad Connect** elemre, majd válassza az **átmenő hitelesítés** lehetőséget.
2. Az **átmenő hitelesítés** lapon kattintson a **Letöltés** gombra. 
3. Az **ügynök letöltése** lapon válassza a **feltételek elfogadása és a letöltés** lehetőséget.
 
   A hitelesítési ügynök elindul a letöltéshez. Telepítse a másodlagos hitelesítési ügynököt egy tartományhoz csatlakoztatott kiszolgálóra.

   > [!NOTE]
   > Az első ügynököt mindig a Azure AD Connect-kiszolgálóra telepíti a Azure AD Connect eszköz **felhasználói bejelentkezés** szakaszában megadott konfigurációs módosítások részeként. Telepítsen minden további hitelesítési ügynököt egy külön kiszolgálóra. Javasoljuk, hogy két vagy három további hitelesítési ügynök legyen elérhető.
 
4. Futtassa a hitelesítési ügynök telepítését. A telepítés során meg kell adnia egy globális rendszergazdai fiók hitelesítő adatait.<br />

   ![Képernyőkép a telepítés gombra a Microsoft Azure AD a hitelesítési ügynök csomagjának összekapcsolása lapon](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![A bejelentkezési oldalt megjelenítő képernyőkép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. A hitelesítési ügynök telepítése után visszatérhet a továbbított hitelesítési ügynök állapota lapra, ahol megtekintheti a további ügynökök állapotát.

Ezen a ponton az összevont hitelesítés továbbra is aktív és működőképes a tartományokban. A telepítés folytatásához az egyes tartományokat összevont identitásról felügyelt identitásra kell konvertálni, hogy az átmenő hitelesítés megkezdje a tartományhoz tartozó hitelesítési kérelmek kiszolgálását.

Az összes tartományt egyszerre nem kell konvertálnia. Dönthet úgy is, hogy az éles bérlőn teszteli a tartományt, vagy a legalacsonyabb számú felhasználóval rendelkező tartománnyal kezdi meg.

Fejezze be az átalakítást az Azure AD PowerShell-modul használatával:

1. A PowerShellben jelentkezzen be az Azure AD-be egy globális rendszergazdai fiók használatával.
2. Az első tartomány átalakításához futtassa a következő parancsot:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Az Azure ad-portálon válassza a **Azure Active Directory**  >  **Azure ad Connect** lehetőséget.
4. Az összes összevont tartomány konvertálása után ellenőrizze ezeket a beállításokat:
   * Az **összevonás** **Letiltva** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **engedélyezve** értékre van állítva.
   * Az **átmenő hitelesítés** **engedélyezve** értékre van állítva.<br />

   ![Képernyőkép, amely az Azure AD-portál felhasználói bejelentkezés szakaszában található beállításokat jeleníti meg.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Tesztelés és további lépések

Az átmenő hitelesítés ellenőrzéséhez és az átalakítási folyamat befejezéséhez hajtsa végre az alábbi feladatokat.

### <a name="test-pass-through-authentication"></a>Átmenő hitelesítés tesztelése 

Ha a bérlő összevont identitást használt, a rendszer átirányítja a felhasználókat az Azure AD bejelentkezési oldaláról a AD FS környezetbe. Most, hogy a bérlő az összevont hitelesítés helyett az átmenő hitelesítés használatára van konfigurálva, a rendszer nem irányítja át a felhasználókat a AD FS. Ehelyett a felhasználók közvetlenül az Azure AD bejelentkezési oldalán jelentkeznek be.

Az átmenő hitelesítés tesztelése:

1. Nyissa meg az Internet Explorert InPrivate módban, hogy a zökkenőmentes SSO ne jelentkezzen be automatikusan.
2. Nyissa meg az Office 365 bejelentkezési oldalát ( [https://portal.office.com](https://portal.office.com/) ).
3. Adjon meg egy felhasználói UPN-t, majd kattintson a **tovább** gombra. Győződjön meg arról, hogy megadta a helyszíni Active Directory-példányról szinkronizált hibrid felhasználó egyszerű felhasználónevét, és korábban összevont hitelesítést használt. Ekkor megjelenik a Felhasználónév és a jelszó megadására szolgáló lap:

   ![Képernyőkép, amely megjeleníti a bejelentkezési oldalt, amelyben beírja a felhasználónevet](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![A bejelentkezési oldalt megjelenítő képernyőkép, amelyben a jelszót adja meg](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. A jelszó megadása és a **Bejelentkezés** kiválasztása után a rendszer átirányítja az Office 365 portálra.

   ![Az Office 365 portált bemutató képernyőkép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Zökkenőmentes egyszeri bejelentkezés tesztelése

A zökkenőmentes egyszeri bejelentkezés tesztelése:

1. Jelentkezzen be egy olyan tartományhoz csatlakoztatott gépre, amely a vállalati hálózathoz csatlakozik.
2. Az Internet Explorerben vagy a Chrome-ban nyissa meg a következő URL-címek egyikét (a "contoso" kifejezést cserélje le a tartományra):

   * https: \/ \/ MyApps.microsoft.com/contoso.com
   * https: \/ \/ MyApps.microsoft.com/contoso.onmicrosoft.com

   A rendszer röviden átirányítja a felhasználót az Azure AD bejelentkezési oldalára, amely megjeleníti a "kísérlet a bejelentkezésre" üzenetet. A felhasználó nem kér felhasználónevet vagy jelszót.<br />

   ![Az Azure AD bejelentkezési oldalát és üzenetét bemutató képernyőkép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. A rendszer átirányítja a felhasználót, és sikeresen bejelentkezett a hozzáférési panelre:

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés olyan Microsoft 365-szolgáltatások esetében működik, amelyek támogatják a tartományi emlékeztetőt (például myapps.microsoft.com/contoso.com). Jelenleg a Microsoft 365 portál (portal.office.com) nem támogatja a tartományi tippeket. A felhasználóknak meg kell adniuk egy egyszerű felhasználónevet. Egy egyszerű felhasználónév megadása után a zökkenőmentes egyszeri bejelentkezés a felhasználó nevében lekéri a Kerberos-jegyet. A felhasználó jelszava megadása nélkül bejelentkezett.

   > [!TIP]
   > A továbbfejlesztett SSO-élmény érdekében érdemes lehet az [Azure ad Hybrid Joint telepíteni a Windows 10-es](../devices/overview.md) verzióra.

### <a name="remove-the-relying-party-trust"></a>A függő entitás megbízhatóságának eltávolítása

Miután ellenőrizte, hogy az összes felhasználó és ügyfél sikeresen hitelesítve lett az Azure AD-n keresztül, nyugodtan távolíthatja el a Microsoft 365 függő entitás megbízhatóságát.

Ha nem használja a AD FS más célra (azaz más függő entitások megbízhatóságára), akkor a AD FS ezen a ponton is biztonságosan leszerelhető.

### <a name="rollback"></a>Visszaállítás

Ha felfedez egy jelentős problémát, és nem tudja gyorsan feloldani, dönthet úgy, hogy visszaállítja a megoldást az összevonáshoz.

Az adott központi telepítés részleteiért olvassa el az összevonási tervezési és telepítési dokumentációt. A folyamatnak a következő feladatokat kell magában foglalnia:

* Felügyelt tartományok átalakítása összevont hitelesítésre a **Convert-MSOLDomainToFederated** parancsmag használatával.
* Ha szükséges, konfigurálja a további jogcímek szabályait.

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName-frissítések szinkronizálása

A **userPrincipalName** attribútum korábbi frissítései a helyszíni környezetből származó szinkronizálási szolgáltatást használják, kivéve, ha mindkét feltétel teljesül:

* A felhasználó felügyelt (nem összevont) identitás tartományban található.
* A felhasználóhoz nincs hozzárendelve licenc.

A szolgáltatás ellenőrzésével vagy bekapcsolásával kapcsolatos további információkért lásd: [userPrincipalName-frissítések szinkronizálása](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>A zökkenőmentes SSO Kerberos-visszafejtési kulcs átadása

Fontos, hogy a AZUREADSSOACC számítógépfiók (amely az Azure AD-t jelképező) Kerberos-visszafejtési kulcsát gyakran átadja. A AZUREADSSOACC-számítógépfiók a helyszíni Active Directory erdőben jön létre. Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot, hogy az Active Directory tartományi tagok jelszavas beküldésének módját. Nincs társítva csatlakoztatott eszköz a AZUREADSSOACC számítógépfiók-objektumhoz, ezért a váltást manuálisan kell végrehajtania.

Kezdeményezheti a zökkenőmentes SSO Kerberos-visszafejtési kulcs átváltását a Azure AD Connect rendszert futtató helyszíni kiszolgálón.

További információ: [hogyan a AZUREADSSOACC-számítógépfiók Kerberos-visszafejtési kulcsának átadása?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Figyelje meg a hitelesítési ügynököket futtató kiszolgálókat a megoldás rendelkezésre állásának fenntartása érdekében. Az általános kiszolgálói teljesítményszámlálók mellett a hitelesítési ügynökök olyan teljesítmény-objektumokat is elérhetővé tesznek, amelyek segíthetnek a hitelesítési statisztikák és hibák megismerésében.

A hitelesítési ügynökök az alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin. alatt található Windows-eseménynaplókban naplózzák a műveleteket.

A hibaelhárításhoz bekapcsolhatja a naplózást is.

További információ: [Azure Active Directory átmenő hitelesítés hibáinak megoldása](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg [Azure ad Connect tervezési fogalmakkal](plan-connect-design-concepts.md).
* Válassza ki a [megfelelő hitelesítést](./choose-ad-authn.md).
* A [támogatott topológiák](plan-connect-design-concepts.md)megismerése.
