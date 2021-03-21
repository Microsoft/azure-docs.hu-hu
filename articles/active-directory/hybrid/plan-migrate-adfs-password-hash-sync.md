---
title: 'Azure AD Connect: Migrálás az PHS az Azure AD-be | Microsoft Docs'
description: Ebből a cikkből megtudhatja, hogyan helyezheti át hibrid identitási környezetét az összevonásból a jelszó-kivonatoló szinkronizálásba.
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
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861816"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Áttelepítés az összevonásból a jelszó-kivonatolási szinkronizálásba Azure Active Directory

Ez a cikk azt ismerteti, hogyan helyezheti át szervezeti tartományait Active Directory összevonási szolgáltatások (AD FS) (AD FS) jelszó-kivonatolási szinkronizálásra.

> [!NOTE]
> A hitelesítési módszer megváltoztatásához tervezési, tesztelési és lehetséges állásidő szükséges. Az [előkészített](how-to-connect-staged-rollout.md) bevezetéssel alternatív módszerekkel tesztelheti és fokozatosan áttelepítheti az összevonási rendszerről a Felhőbeli hitelesítésre a jelszó-kivonatoló szinkronizálás használatával.
>
> Ha lépcsőzetes bevezetést használ, ne feledje, hogy kikapcsolja a szakaszos bevezetési funkciókat, miután befejezte a kivágást.  További információ: [Migrálás felhőalapú hitelesítésre szakaszos bevezetés használatával](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>A jelszó-kivonat szinkronizálására való Migrálás előfeltételei

A következő előfeltételek szükségesek az áttelepítéshez a AD FS használatával a jelszó-kivonatoló szinkronizálás használatához.


### <a name="update-azure-ad-connect"></a>Azure AD Connect frissítése

A jelszó-kivonatos szinkronizálásra való áttelepítéshez szükséges lépések végrehajtásának minimális követelménye az [Azure ad kapcsolódási](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ez a verzió jelentős változásokat tartalmaz a bejelentkezési konverzió végrehajtásához, és csökkenti a teljes időt, hogy az összevonási rendszerről a Felhőbeli hitelesítésre való áttérést akár percek alatt is elvégezze.


> [!IMPORTANT]
> Előfordulhat, hogy az elavult dokumentációban, eszközökben és blogokban a felhasználók átalakítására van szükség, amikor a tartományokat összevont identitásról felügyelt identitásra konvertálja. A *felhasználók konvertálása* már nem szükséges. A Microsoft dolgozik a dokumentáció és az eszközök frissítésén, hogy tükrözze ezt a változást.

Azure AD Connect frissítéséhez hajtsa végre a [Azure ad Connect: frissítés a legújabb verzióra](./how-to-upgrade-previous-version.md)című témakör lépéseit.

### <a name="password-hash-synchronization-required-permissions"></a>Jelszó kivonatának szinkronizálásához szükséges engedélyek

Az expressz beállítások vagy egy egyéni telepítés használatával Azure AD Connect konfigurálhatja. Ha az egyéni telepítési lehetőséget használta, előfordulhat, hogy a jelszó-kivonat szinkronizálásához [szükséges engedélyek](./reference-connect-accounts-permissions.md) nem teljesülnek.

A Azure AD Connect Active Directory Domain Services (AD DS) szolgáltatás fiókjának a következő engedélyekkel kell rendelkeznie a jelszó-kivonatok szinkronizálásához:

* Címtárbeli módosítások replikálása
* A címtár összes módosításának replikálása

Most már jó ideje ellenőrizni, hogy ezek az engedélyek teljesülnek-e az erdőben lévő összes tartományhoz.

### <a name="plan-the-migration-method"></a>Az áttelepítési módszer megtervezése

Két módszer közül választhat az összevont identitáskezelésból a jelszó-kivonat szinkronizálására és a zökkenőmentes egyszeri bejelentkezésre (SSO) való Migrálás esetén. Az Ön által használt módszer attól függ, hogy a AD FS-példányt eredetileg konfigurálták-e.

* **Azure AD Connect**. Ha eredetileg a Azure AD Connect használatával konfigurálta a AD FSt, akkor a Azure AD Connect varázsló segítségével módosítania *kell* a jelszó-kivonatolási szinkronizálást.

   A Azure AD Connect automatikusan futtatja a **set-MsolDomainAuthentication** parancsmagot a felhasználói bejelentkezési módszer módosításakor. Azure AD Connect automatikusan unfederates az összes ellenőrzött összevont tartományt az Azure AD-bérlőben.

   > [!NOTE]
   > Jelenleg, ha a Azure AD Connectt a AD FS konfigurálására használta, akkor a bérlő összes tartománya nem unfederating el, ha a felhasználói bejelentkezést jelszó-kivonatolási szinkronizálásra módosítja. ‎
* **Azure ad Connect a PowerShell**-lel. Ezt a módszert csak akkor használhatja, ha a Azure AD Connect használatával eredetileg nem konfigurálta AD FS. Ennél a lehetőségnél továbbra is meg kell változtatnia a felhasználói bejelentkezési módszert a Azure AD Connect varázslón keresztül. Ezzel a beállítással a fő különbség az, hogy a varázsló nem futtatja automatikusan a **set-MsolDomainAuthentication** parancsmagot. Ezzel a beállítással teljes mértékben szabályozhatja, hogy mely tartományok konvertálása és milyen sorrendben történjen.

A következő részben ismertetett lépéseket követve megtudhatja, hogy melyik módszert használja.

#### <a name="verify-current-user-sign-in-settings"></a>Aktuális felhasználói bejelentkezési beállítások ellenőrzése

Az aktuális felhasználói bejelentkezési beállítások ellenőrzése:

1. Jelentkezzen be az [Azure ad-portálra](https://aad.portal.azure.com/) egy globális rendszergazdai fiók használatával.
2. A **felhasználói bejelentkezés** szakaszban ellenőrizze az alábbi beállításokat:
   * Az **összevonás** **engedélyezve** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **Letiltva** értékre van állítva.
   * Az **átmenő hitelesítés** **Letiltva** értékre van állítva.

   ![Képernyőkép a Azure AD Connect felhasználói bejelentkezési szakaszban található beállításokról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>A Azure AD Connect konfigurációjának ellenőrzése

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect. Válassza a **Konfigurálás** lehetőséget.
2. A **További feladatok** lapon válassza a **jelenlegi konfiguráció megtekintése** lehetőséget, majd kattintson a **tovább** gombra.<br />

   ![Képernyőfelvétel a további feladatok oldalon kiválasztott aktuális konfiguráció megtekintése lehetőségről](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. A **megoldás áttekintése** lapon jegyezze fel a jelszó- **kivonat szinkronizálásának** állapotát.<br /> 

   * Ha a **jelszó-kivonat szinkronizálása** **Letiltva** értékre van állítva, hajtsa végre a cikk lépéseit az engedélyezéshez.
   * Ha a **jelszó-kivonat szinkronizálása** **engedélyezve** értékre van állítva, kihagyhatja az **1. lépés: a jelszó-kivonatok szinkronizálásának engedélyezése** című szakaszt ebben a cikkben.
4. A **megoldás áttekintése** lapon görgessen a **Active Directory összevonási szolgáltatások (AD FS) (AD FS)** elemre.<br />

   * Ha a AD FS konfiguráció megjelenik ebben a szakaszban, nyugodtan feltételezheti, hogy AD FS eredetileg Azure AD Connect használatával lett konfigurálva. A tartományokat összevont identitásról felügyelt identitásra konvertálhatja a **felhasználói bejelentkezés Azure ad Connect módosítása** lehetőség használatával. A folyamat részletesen szerepel a (z) szakaszának **: Váltás az összevonás és jelszó kivonat-szinkronizálásra Azure ad Connect használatával**.
   * Ha AD FS nem szerepel az aktuális beállítások listáján, akkor a PowerShell használatával manuálisan kell konvertálnia a tartományokat összevont identitásról a felügyelt identitásra. A folyamattal kapcsolatos további információkért tekintse meg a **B. lehetőséget: Váltás az összevonásról a jelszó-kivonatoló szinkronizálásra a Azure ad Connect és a PowerShell használatával**.

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
> Ha a **SupportsMfa** értéke **true (igaz**), a rendszer helyszíni multi-Factor Authentication megoldást használ egy második tényezős kihívás beadására a felhasználói hitelesítési folyamatba. Ez a beállítás már nem működik az Azure AD hitelesítési forgatókönyvek esetében, miután a tartományt összevontról felügyelt hitelesítésre konvertálta. Az összevonás letiltása után megszakítja a kapcsolatot a helyszíni összevonási kapcsolattal, és ez magában foglalja a helyszíni MFA-adaptereket is. 
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

AD FS kiadja a **InsideCorporateNetwork** jogcímet, ha a hitelesítést végző felhasználó a vállalati hálózaton belül van. Ezt követően a jogcím átadható az Azure AD-nek. A rendszer a felhasználó hálózati helye alapján a többtényezős hitelesítés megkerülésére használatos. Ha meg szeretné tudni, hogyan állapítható meg, hogy a funkció jelenleg engedélyezve van-e a AD FSban, tekintse meg az [összevont felhasználók megbízható IP](../authentication/howto-mfa-adfs.md)-címei

A **InsideCorporateNetwork** jogcím nem érhető el, ha a tartományok konvertálása jelszó-kivonatolási szinkronizálásra történik. Az [Azure ad elnevezett helyeinek](../reports-monitoring/quickstart-configure-named-locations.md) használatával lecserélheti a funkciót.

A nevesített helyek konfigurálása után frissítenie kell az összes olyan feltételes hozzáférési házirendet, amely konfigurálva lett a hálózat **minden megbízható hely** vagy **MFA megbízható IP** -cím értékének befoglalására vagy kizárására az új elnevezett helyeknek megfelelően.

A feltételes hozzáférés **hely** feltételével kapcsolatos további információkért lásd: [Active Directory feltételes hozzáférési helyek](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök

Amikor csatlakoztat egy eszközt az Azure AD-hoz, létrehozhat feltételes hozzáférési szabályokat, amelyek kikényszerítik, hogy az eszközök megfeleljenek a biztonsági és megfelelőségi hozzáférési előírásoknak. A felhasználók személyes fiók helyett szervezeti munkahelyi vagy iskolai fiókkal is bejelentkezhetnek az eszközre. Hibrid Azure AD-hez csatlakoztatott eszközök használata esetén Active Directory tartományhoz csatlakoztatott eszközöket csatlakoztathat az Azure AD-hez. Lehetséges, hogy az összevont környezet a szolgáltatás használatára van beállítva.

Annak biztosítása érdekében, hogy a hibrid csatlakoztatások továbbra is működjenek a tartományhoz csatlakoztatott összes eszközön, miután a tartományokat jelszó-kivonatolási szinkronizálásra konvertálja, a Windows 10-es ügyfelek esetében Azure AD Connect eszköz beállításait kell használnia, hogy szinkronizálja Active Directory számítógépfiókokat az Azure AD-be. 

A Windows 8 és a Windows 7 rendszerű számítógépfiókok esetében a hibrid illesztés zökkenőmentes egyszeri bejelentkezést használ a számítógép Azure AD-ben való regisztrálásához. A Windows 8 és a Windows 7 rendszerű számítógépek fiókjait nem kell szinkronizálnia, mint a Windows 10-es eszközökhöz. A Windows 8 és a Windows 7 rendszerű ügyfelek esetében azonban frissítenie kell egy frissített workplacejoin.exe-fájlt (. msi-fájlon keresztül), hogy a zökkenőmentes SSO használatával regisztrálja magukat. [Töltse le az. msi fájlt](https://www.microsoft.com/download/details.aspx?id=53554).

További információ: [hibrid Azure ad-hez csatlakoztatott eszközök konfigurálása](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Arculat

Ha a szervezete [testreszabja a AD FS bejelentkezési oldalain](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) a szervezettel kapcsolatos információk megjelenítéséhez, érdemes lehet hasonló [testreszabásokat felvenni az Azure ad bejelentkezési oldalára](../fundamentals/customize-branding.md).

Bár a hasonló testreszabások elérhetők, a bejelentkezési lapokon a konverziót követően néhány vizualizációs változást is el kell várni. Előfordulhat, hogy meg kívánja adni a kommunikáció várható változásaival kapcsolatos információkat a felhasználók számára.

> [!NOTE]
> A szervezet arculata csak akkor érhető el, ha a prémium szintű vagy alapszintű licencet vásárolja meg Azure Active Directory vagy Microsoft 365 licenccel rendelkezik.

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

A jelszó-kivonatolási szinkronizálás és a zökkenőmentes SSO üzembe helyezése után a felhasználói bejelentkezés az Azure AD változásain keresztül hitelesített Microsoft 365 és egyéb erőforrások elérését is eredményezi. A hálózaton kívüli felhasználók csak az Azure AD bejelentkezési oldalát látják. Ezeket a felhasználókat a rendszer nem irányítja át a külső elérésű webalkalmazás-proxy kiszolgálók által megjelenített űrlapalapú oldalra.

Adja meg a következő elemeket a kommunikációs stratégiában:

* Értesítse a felhasználókat a közelgő és kiadott funkciókról a következő használatával:
   * E-mailek és egyéb belső kommunikációs csatornák.
   * Vizualizációk, például plakátok.
   * Executive, Live vagy más kommunikáció.
* Határozza meg, hogy ki fogja testre szabni a kommunikációt, és ki fogja elküldeni a kommunikációt, és mikor.

## <a name="implement-your-solution"></a>A megoldás implementálása

Megtervezte a megoldást. Most már elvégezheti a megvalósítást. A megvalósítás a következő összetevőket foglalja magában:

* Jelszó-kivonat szinkronizálásának engedélyezése.
* Felkészülés a zökkenőmentes egyszeri bejelentkezésre.
* A bejelentkezési módszer módosítása a jelszó-kivonatolási szinkronizáláshoz és a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez.

### <a name="step-1-enable-password-hash-synchronization"></a>1. lépés: jelszó-kivonatolási szinkronizálás engedélyezése

A megoldás megvalósításának első lépéseként engedélyezze a jelszó-kivonatoló szinkronizálást a Azure AD Connect varázsló segítségével. A jelszó-kivonatolási szinkronizálás egy opcionális funkció, amelyet az összevonás szolgáltatást használó környezetekben engedélyezhet. Nincs hatással a hitelesítési folyamatra. Ebben az esetben Azure AD Connect megkezdi a jelszó-kivonatok szinkronizálását anélkül, hogy ez befolyásolná a felhasználókat, akik az összevonás használatával jelentkeznek be.

Ezért javasoljuk, hogy ezt a lépést előkészítési feladatként végezze el, mielőtt módosítaná a tartomány bejelentkezési módszerét. Ezt követően elegendő idő van arra, hogy ellenőrizze, hogy a jelszó-kivonat szinkronizálása megfelelően működik-e.

A jelszó-kivonat szinkronizálásának engedélyezése:

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect varázslót, majd válassza a **Konfigurálás** lehetőséget.
2. Válassza a **szinkronizálási beállítások testreszabása lehetőséget**, majd kattintson a **tovább** gombra.
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **címtárak összekapcsolása** lapon válassza a **tovább** lehetőséget.
5. A **tartomány és szervezeti egység szűrése** lapon válassza a **tovább** lehetőséget.
6. A **választható szolgáltatások** lapon válassza a **Jelszó-szinkronizálás** lehetőséget, majd kattintson a **tovább** gombra.
 
   ![Képernyőfelvétel a választható funkciók lapon kiválasztott jelszó-szinkronizálási lehetőségről](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Kattintson a **Tovább gombra** a fennmaradó lapokon. Az utolsó lapon válassza a **Konfigurálás** lehetőséget.
8. Azure AD Connect elindítja a jelszó-kivonatok szinkronizálását a következő szinkronizáláskor.

A jelszó-kivonat szinkronizálásának engedélyezése után a Azure AD Connect szinkronizálási hatókörben lévő összes felhasználóhoz tartozó jelszó-kivonatok újrakivonatolása és írása az Azure AD-ba. A felhasználók számától függően ez a művelet néhány percig vagy akár több órát is igénybe vehet.

Tervezési célokra érdemes megbecsülni, hogy körülbelül 20 000 felhasználót dolgoz fel 1 órán belül.

A jelszó-kivonatoló szinkronizálás megfelelő működésének ellenőrzéséhez hajtsa végre a **hibaelhárítási** feladatot a Azure ad Connect varázslóban:

1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a Futtatás rendszergazdaként lehetőség használatával.
2. Futtassa a vagy a parancsot `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted` .
3. Indítsa el a Azure AD Connect varázslót.
4. Lépjen a **További feladatok** lapra, válassza a **hibakeresés** lehetőséget, majd kattintson a **tovább** gombra.
5. A **Hibaelhárítás** lapon kattintson a **Launch (indítás** ) elemre a PowerShell hibaelhárítási menüjének elindításához.
6. A főmenüben válassza a **jelszó-kivonatolási szinkronizálás hibáinak megoldása** lehetőséget.
7. Az almenüben válassza a **jelszó kivonat szinkronizálása egyáltalán nem működik** lehetőséget.

Hibaelhárítási problémák esetén lásd: [jelszó-kivonatolási szinkronizálás hibaelhárítása Azure ad Connect szinkronizálással](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>2. lépés: a zökkenőmentes egyszeri bejelentkezés előkészítése

Ahhoz, hogy az eszközök zökkenőmentes egyszeri bejelentkezést használjanak, hozzá kell adnia egy Azure AD-URL-címet a felhasználók intranetes zónájának beállításaihoz egy Active Directory csoportházirend használatával.

Alapértelmezés szerint a böngészők automatikusan kiszámítják a megfelelő zónát (Internet vagy intranet) egy URL-címről. Például **http: \/ \/ contoso/** maps to the intranet zóna és **http: \/ \/ intranet.contoso.com** maps to the Internet Zone (mivel az URL-cím egy pontot tartalmaz). A böngészők Kerberos-jegyeket küldenek egy Felhőbeli végpontra (például az Azure AD URL-címére), ha explicit módon hozzáadja az URL-címet a böngésző intranetes zónájához.

Végezze [el az](./how-to-connect-sso-quick-start.md) eszközök szükséges módosításainak lépéseit.

> [!IMPORTANT]
> Ennek a módosításnak a végrehajtásával nem módosítható, hogy a felhasználók hogyan jelentkeznek be az Azure AD-be. Azonban fontos, hogy a folytatás előtt alkalmazza ezt a konfigurációt az összes eszközre. Azokat a felhasználókat, akik nem kaptak ezt a konfigurációt, egyszerűen be kell írniuk egy felhasználónevet és jelszót az Azure AD-be való bejelentkezéshez.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>3. lépés: a bejelentkezési módszer módosítása jelszó-kivonatolási szinkronizáláshoz és a zökkenőmentes egyszeri bejelentkezés engedélyezése

A bejelentkezési módszer a jelszó-kivonatolási szinkronizáláshoz és a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez két lehetőség közül választhat.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>"A" lehetőség: Váltás az összevonásról a jelszó-kivonatolási szinkronizálásra Azure AD Connect használatával

Ezt a módszert akkor használja, ha a Azure AD Connect használatával először konfigurálta AD FS-környezetét. Ez a módszer nem használható, ha a Azure AD Connect használatával eredetileg *nem* konfigurálta AD FS-környezetét.

Először módosítsa a bejelentkezési módszert:

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect varázslót.
2. Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra. 

   ![Képernyőkép a felhasználói bejelentkezés módosítása lehetőségről a további feladatok lapon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **felhasználói bejelentkezés** lapon válassza a **jelszó kivonat szinkronizálása gombot**. Győződjön meg arról, hogy a **ne alakítsa át a felhasználói fiókokat** jelölőnégyzetet. A beállítás elavult. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet, majd kattintson a **tovább** gombra.

   ![Az egyszeri bejelentkezés engedélyezése oldal képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > A Azure AD Connect verzió 1.1.880.0 kezdve a **zökkenőmentes egyszeri bejelentkezés** jelölőnégyzet alapértelmezés szerint be van jelölve.

   > [!IMPORTANT]
   > Nyugodtan figyelmen kívül hagyhatja azokat a figyelmeztetéseket, amelyek azt jelzik, hogy a felhasználói átalakítás és a jelszó kivonatának teljes szinkronizálása szükséges az összevonás és a felhő közötti hitelesítés közötti átalakításhoz Vegye figyelembe, hogy ezek a lépések többé nem szükségesek. Ha továbbra is ezeket a figyelmeztetéseket látja, győződjön meg arról, hogy az Azure AD Connect legújabb verzióját futtatja, és az útmutató legújabb verzióját használja. További információ: [Update Azure ad Connect](#update-azure-ad-connect).

5. Az **egyszeri bejelentkezés engedélyezése** lapon adja meg a tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a **tovább** gombra.

   ![Képernyőkép az egyszeri bejelentkezés engedélyezése oldalról, ahol megadhatja a tartományi rendszergazdai fiók hitelesítő adatait.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés engedélyezéséhez tartományi rendszergazdai fiók hitelesítő adatai szükségesek. A folyamat a következő műveleteket hajtja végre, ehhez emelt szintű engedélyekre van szükség. A tartományi rendszergazdai fiók hitelesítő adatai nem Azure AD Connect vagy az Azure AD-ben tárolódnak. A tartományi rendszergazdai fiók hitelesítő adatai csak a funkció bekapcsolására használhatók. A rendszer a folyamat sikeres befejeződése után elveti a hitelesítő adatokat.
   >
   > 1. A helyszíni Active Directory-példányban létrejön egy AZUREADSSOACC nevű számítógépfiók (amely az Azure AD-t jelöli).
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos egyszerű szolgáltatásnév (SPN) jön létre, amelyek az Azure AD-bejelentkezés során használt két URL-címet jelölik.

6. A **készen áll a konfigurálásra** lapon jelölje be a **szinkronizálási folyamat elindítása a konfiguráció befejeződése** után jelölőnégyzetet. Ezután válassza a **Konfigurálás** lehetőséget.

      ![A konfigurálásra kész lap képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Ezen a ponton az összes összevont tartomány a felügyelt hitelesítésre lesz módosítva. A jelszó kivonatának szinkronizálása az új hitelesítési módszer.

7. Az Azure ad-portálon válassza a **Azure Active Directory**  >  **Azure ad Connect** lehetőséget.
8. Ellenőrizze ezeket a beállításokat:
   * Az **összevonás** **Letiltva** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **engedélyezve** értékre van állítva.
   * A **Jelszó-szinkronizálás** **engedélyezve** értékre van állítva.<br /> 

   ![Képernyőkép, amely az Azure AD-portál felhasználói bejelentkezés szakaszában található beállításokat jeleníti meg.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Ugorjon a [tesztelés és a további lépések](#testing-and-next-steps)elemre.

   > [!IMPORTANT]
   > Ugorja **át a B. lehetőséget: váltson az összevonásról a jelszó-kivonatolási szinkronizálásra Azure ad Connect és a PowerShell használatával**. Az ebben a szakaszban ismertetett lépések nem érvényesek, ha az A kapcsolót választotta a bejelentkezési módszer módosítására jelszó-kivonatolási szinkronizáláshoz, valamint a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>B. lehetőség: Váltás az összevonásról a jelszó-kivonatolási szinkronizálásra a Azure AD Connect és a PowerShell használatával

Akkor használja ezt a beállítást, ha a Azure AD Connect használatával eredetileg nem konfigurálta az összevont tartományokat. A folyamat során engedélyezheti a zökkenőmentes egyszeri bejelentkezést, és átválthatja a tartományokat összevontról felügyelt értékre.

1. A Azure AD Connect-kiszolgálón nyissa meg a Azure AD Connect varázslót.
2. Válassza **a felhasználói bejelentkezés módosítása** lehetőséget, majd kattintson a **tovább** gombra.
3. A **Kapcsolódás az Azure ad-hoz** lapon adja meg a globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **felhasználói bejelentkezés** lapon válassza a **jelszó kivonat szinkronizálása** gombot. Jelölje be az **egyszeri bejelentkezés engedélyezése** jelölőnégyzetet, majd kattintson a **tovább** gombra.

   A jelszó-kivonat szinkronizálásának engedélyezése előtt: ![ képernyőkép, amely a nem konfigurálható beállítást mutatja a felhasználói bejelentkezési oldalon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   A jelszó-kivonat szinkronizálásának engedélyezése után: ![ képernyőfelvétel, amely az új beállításokat jeleníti meg a felhasználói bejelentkezési oldalon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > A Azure AD Connect verzió 1.1.880.0 kezdve a **zökkenőmentes egyszeri bejelentkezés** jelölőnégyzet alapértelmezés szerint be van jelölve.

5. Az **egyszeri bejelentkezés engedélyezése** lapon adja meg egy tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a **tovább** gombra.

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés engedélyezéséhez tartományi rendszergazdai fiók hitelesítő adatai szükségesek. A folyamat a következő műveleteket hajtja végre, ehhez emelt szintű engedélyekre van szükség. A tartományi rendszergazdai fiók hitelesítő adatai nem Azure AD Connect vagy az Azure AD-ben tárolódnak. A tartományi rendszergazdai fiók hitelesítő adatai csak a funkció bekapcsolására használhatók. A rendszer a folyamat sikeres befejeződése után elveti a hitelesítő adatokat.
   >
   > 1. A helyszíni Active Directory-példányban létrejön egy AZUREADSSOACC nevű számítógépfiók (amely az Azure AD-t jelöli).
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos egyszerű szolgáltatásnév (SPN) jön létre, amelyek az Azure AD-bejelentkezés során használt két URL-címet jelölik.

6. A **készen áll a konfigurálásra** lapon jelölje be a **szinkronizálási folyamat elindítása a konfiguráció befejeződése** után jelölőnégyzetet. Ezután válassza a **Konfigurálás** lehetőséget.

   ![Képernyőfelvétel a configure (Konfigurálás) gombra a konfigurálásra kész lapon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Amikor kiválasztja a **Konfigurálás** gombot, a zökkenőmentes egyszeri bejelentkezés az előző lépésben megadott módon van konfigurálva. A jelszó-kivonat szinkronizálásának konfigurációja nincs módosítva, mert korábban engedélyezték.

   > [!IMPORTANT]
   > A felhasználók jelenleg nem jelentkeznek be módosításokkal.

7. Az Azure AD-portálon ellenőrizze ezeket a beállításokat:
   * Az **összevonás** **engedélyezve** értékre van állítva.
   * A **zökkenőmentes egyszeri bejelentkezés** **engedélyezve** értékre van állítva.
   * A **Jelszó-szinkronizálás** **engedélyezve** értékre van állítva.

   ![A felhasználói bejelentkezési szakaszban található beállításokat megjelenítő képernyőkép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Tartományok konvertálása összevontról felügyelt értékre

Ezen a ponton az összevonás továbbra is engedélyezve van, és működőképes a tartományokban. Az üzembe helyezés folytatásához minden tartományt át kell alakítani összevontról, hogy felügyelve legyen a felhasználói hitelesítés a jelszó-kivonatoló szinkronizálással.

> [!IMPORTANT]
> Az összes tartományt egyszerre nem kell konvertálnia. Dönthet úgy is, hogy az éles bérlőn teszteli a tartományt, vagy a legalacsonyabb számú felhasználóval rendelkező tartománnyal kezdi meg.

Fejezze be az átalakítást az Azure AD PowerShell-modul használatával:

1. A PowerShellben jelentkezzen be az Azure AD-be egy globális rendszergazdai fiók használatával.
2. Az első tartomány átalakításához futtassa a következő parancsot:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Az Azure ad-portálon válassza a **Azure Active Directory**  >  **Azure ad Connect** lehetőséget.
4. A következő parancs futtatásával ellenőrizze, hogy a tartományt felügyelte-e a rendszer:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Tesztelés és további lépések

A jelszó-kivonat szinkronizálásának ellenőrzéséhez és az átalakítási folyamat befejezéséhez hajtsa végre a következő feladatokat.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Hitelesítés tesztelése a jelszó-kivonatolási szinkronizálás használatával 

Ha a bérlő összevont identitást használt, a rendszer átirányítja a felhasználókat az Azure AD bejelentkezési oldaláról a AD FS környezetbe. Most, hogy a bérlő úgy van beállítva, hogy összevont hitelesítés helyett jelszó-kivonatoló szinkronizálást használjon, a rendszer nem irányítja át a felhasználókat a AD FS. Ehelyett a felhasználók közvetlenül az Azure AD bejelentkezési oldalán jelentkeznek be.

A jelszó-kivonat szinkronizálásának tesztelése:

1. Nyissa meg az Internet Explorert InPrivate módban, hogy a zökkenőmentes SSO ne jelentkezzen be automatikusan.
2. Nyissa meg az Office 365 bejelentkezési oldalát ( [https://portal.office.com](https://portal.office.com/) ).
3. Adjon meg egy felhasználói UPN-t, majd kattintson a **tovább** gombra. Győződjön meg arról, hogy megadta a helyszíni Active Directory-példányról szinkronizált hibrid felhasználó egyszerű felhasználónevét, és korábban összevont hitelesítést használt. Ekkor megjelenik a Felhasználónév és a jelszó megadására szolgáló lap:

   ![Képernyőkép, amely megjeleníti a bejelentkezési oldalt, amelyben beírja a felhasználónevet](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![A bejelentkezési oldalt megjelenítő képernyőkép, amelyben a jelszót adja meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. A jelszó megadása és a **Bejelentkezés** kiválasztása után a rendszer átirányítja az Office 365 portálra.

   ![Az Office 365 portált bemutató képernyőkép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Zökkenőmentes egyszeri bejelentkezés tesztelése

1. Jelentkezzen be egy olyan tartományhoz csatlakoztatott gépre, amely a vállalati hálózathoz csatlakozik.
2. Az Internet Explorerben vagy a Chrome-ban nyissa meg a következő URL-címek egyikét (a "contoso" kifejezést cserélje le a tartományra):

   * https: \/ \/ MyApps.microsoft.com/contoso.com
   * https: \/ \/ MyApps.microsoft.com/contoso.onmicrosoft.com

   A rendszer röviden átirányítja a felhasználót az Azure AD bejelentkezési oldalára, amely megjeleníti a "kísérlet a bejelentkezésre" üzenetet. A felhasználó nem kér felhasználónevet vagy jelszót.<br />

   ![Az Azure AD bejelentkezési oldalát és üzenetét bemutató képernyőkép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
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

### <a name="troubleshooting"></a>Hibaelhárítás

A támogatási csapatnak tisztában kell lennie azzal, hogyan lehet elhárítani azokat a hitelesítési problémákat, amelyek vagy a folyamat során vagy az összevonás által felügyelt változás után jelentkeznek. Az alábbi hibaelhárítási dokumentáció segítséget nyújt a támogatási csapatnak a gyakori hibaelhárítási lépésekkel, valamint a probléma elkülönítésére és megoldására alkalmas műveletek megismerésére.

[A jelszó-kivonatok szinkronizálásának Azure Active Directory hibáinak megoldása](./tshoot-connect-password-hash-synchronization.md)

[Azure Active Directory zökkenőmentes egyszeri bejelentkezés hibáinak megoldása](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>A zökkenőmentes SSO Kerberos-visszafejtési kulcs átadása

Fontos, hogy a AZUREADSSOACC számítógépfiók (amely az Azure AD-t jelképező) Kerberos-visszafejtési kulcsát gyakran átadja. A AZUREADSSOACC-számítógépfiók a helyszíni Active Directory erdőben jön létre. Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot, hogy az Active Directory tartományi tagok jelszavas beküldésének módját. Nincs társítva csatlakoztatott eszköz a AZUREADSSOACC számítógépfiók-objektumhoz, ezért a váltást manuálisan kell végrehajtania.

Kezdeményezheti a zökkenőmentes SSO Kerberos-visszafejtési kulcs átváltását a Azure AD Connect rendszert futtató helyszíni kiszolgálón.

További információ: [hogyan a AZUREADSSOACC-számítógépfiók Kerberos-visszafejtési kulcsának átadása?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg [Azure ad Connect tervezési fogalmakkal](plan-connect-design-concepts.md).
* Válassza ki a [megfelelő hitelesítést](./choose-ad-authn.md).
* A [támogatott topológiák](plan-connect-design-concepts.md)megismerése.
