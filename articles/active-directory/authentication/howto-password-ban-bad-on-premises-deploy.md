---
title: Helyszíni Azure AD jelszavas védelem üzembe helyezése
description: Ismerje meg, hogyan tervezhet és helyezhet üzembe Azure AD-jelszavas védelmet helyszíni Active Directory Domain Services környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8cae19bd07e1cc87a0aaa25e47cf5f431d566ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653813"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Helyszíni Azure Active Directory jelszavas védelem tervezése és üzembe helyezése

A felhasználók gyakran olyan gyakori helyi szavakat (például iskolát, sport csapatot vagy híres személyt) használó jelszavakat hoznak létre. Ezek a jelszavak könnyen kiolvashatók és gyengék a szótáron alapuló támadásokkal szemben. A szervezet erős jelszavainak kényszerítéséhez Azure Active Directory (Azure AD) jelszavas védelme globális és egyéni tiltott jelszavakat tartalmaz. A jelszó-módosítási kérelem meghiúsul, ha egyezés szerepel a tiltott jelszavak listájában.

A helyszíni Active Directory Domain Services (AD DS) környezetének védelme érdekében telepítheti és konfigurálhatja az Azure AD jelszavas védelmet a helyszíni TARTOMÁNYVEZÉRLŐvel való együttműködéshez. Ebből a cikkből megtudhatja, hogyan telepítheti és regisztrálja az Azure AD jelszavas védelem-proxy szolgáltatást és az Azure AD jelszavas védelmet biztosító tartományvezérlő ügynököt a helyszíni környezetben.

Az Azure AD jelszavas védelem helyszíni környezetben való működésével kapcsolatos további információkért lásd: [Az Azure ad jelszavas védelem betartatása a Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Központi telepítési stratégia

Az alábbi ábra bemutatja, hogyan működik együtt az Azure AD jelszavas védelem alapvető összetevői egy helyszíni Active Directory környezetben:

![Hogyan működnek együtt az Azure AD jelszavas védelmi összetevői?](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Érdemes áttekinteni, hogyan működik a szoftver az üzembe helyezése előtt. További információ: [Az Azure ad jelszavas védelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md).

Javasoljuk, hogy a központi telepítéseket *vizsgálati* módban indítsa el. A naplózási mód az alapértelmezett kezdeti beállítás, ahol a jelszavak továbbra is megadhatók. A blokkolni kívánt jelszavakat az eseménynapló rögzíti. Miután a proxykiszolgálót és a TARTOMÁNYVEZÉRLŐi ügynököt naplózási módban telepítette, figyelje meg, hogy a jelszóházirend milyen hatással lesz a felhasználókra, amikor a házirend érvénybe lép.

A naplózási szakaszban számos szervezet úgy találja, hogy a következő helyzetek érvényesek:

* A biztonságosabb jelszavak használatához javítaniuk kell a meglévő működési folyamatokat.
* A felhasználók gyakran nem biztonságos jelszavakat használnak.
* Tájékoztatniuk kell a felhasználókat a biztonsági kényszerítés közelgő változásáról, a lehetséges hatásokról, valamint a biztonságosabb jelszavak kiválasztásáról.

Az is lehetséges, hogy az erősebb jelszó-érvényesítés a meglévő Active Directory tartományvezérlő üzembe helyezési automatizálásának befolyásolására is használható. Javasoljuk, hogy a naplózási időszak kiértékelése során legalább egy DC-előléptetést és egy tartományvezérlő-lefokozást hajtson végre a probléma elhárítása érdekében. További információért tekintse át a következő cikkeket:

* [ ANtdsutil.exe nem tudja beállítani a Címtárszolgáltatások helyreállító módjának jelszavát.](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A tartományvezérlő replikájának előléptetése sikertelen a Címtárszolgáltatások helyreállító módjának jelszava miatt](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A tartományvezérlő lefokozása nem sikerült, mert gyenge a helyi rendszergazda jelszava](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Ha a szolgáltatás egy ésszerű időszakra vonatkozóan a vizsgálati módban fut, a konfigurációt átválthatja a *naplózásból* , hogy a *kényszerítse* a biztonságosabb jelszavak megkövetelését. Ebben az időszakban további monitorozásra is jó ötlet.

Fontos megjegyezni, hogy az Azure AD jelszavas védelme csak jelszó-módosítási vagy-beállítási műveletek esetén képes érvényesíteni a jelszavakat. Az Azure AD jelszavas védelem telepítése előtt elfogadott és Active Directory tárolt jelszavakat soha nem érvényesíti a rendszer, és folytatja a munkát. Az idő múlásával az összes felhasználó és fiók végül az Azure AD jelszavas védelem-érvényesített jelszavait fogja használni, mivel a meglévő jelszavaik általában lejárnak. A "jelszó soha nem jár le" beállítással konfigurált fiókok mentesülnek ettől.

### <a name="multiple-forest-considerations"></a>Több erdőre vonatkozó megfontolások

Az Azure AD jelszavas védelem több erdőben való üzembe helyezéséhez nem szükséges további követelmény.

Az egyes erdők egymástól függetlenül konfigurálhatók, a következő szakaszban leírtak szerint, az [Azure ad jelszavas védelem üzembe helyezéséhez](#download-required-software). Minden egyes Azure AD jelszavas védelmi proxy csak abban az erdőben tud tartományvezérlőket támogatni, amelyhez csatlakozik.

Az Azure AD jelszavas védelem szoftvere bármely erdőben nem ismeri a más erdőkben üzembe helyezett jelszavas védelmi szoftvereket, függetlenül a Active Directory megbízhatósági konfigurációtól.

### <a name="read-only-domain-controller-considerations"></a>Írásvédett tartományvezérlői megfontolások

A jelszó módosítása vagy az események beállítása nem lett feldolgozva, és csak olvasható tartományvezérlőkön (írásvédett tartományvezérlőn) marad. Ehelyett írható tartományvezérlőkre továbbítják őket. Nem kell telepítenie az Azure AD jelszavas védelem DC Agent szoftverét az írásvédett tartományvezérlőn.

Emellett nem támogatott az Azure AD Password Protection proxy szolgáltatás futtatása írásvédett tartományvezérlőn.

### <a name="high-availability-considerations"></a>Magas rendelkezésre állási megfontolások

A jelszavas védelem fő problémája az Azure AD jelszavas védelmi proxykiszolgálók rendelkezésre állása, amikor egy erdőben lévő tartományvezérlők megpróbálnak letölteni új szabályzatokat vagy más, az Azure-ból származó adatok letöltését. Minden egyes Azure AD Password Protection DC-ügynök egy egyszerű, ciklikusan megjelenő algoritmust használ, amikor eldönti, melyik proxykiszolgálót kell meghívni. Az ügynök kihagyja a nem válaszoló proxykiszolgáló-kiszolgálókat.

A címtár-és SYSVOL mappa állapotának kifogástalan replikálásával rendelkező Active Directory központi telepítések esetében két Azure AD-beli jelszavas védelmi proxykiszolgáló elegendő a rendelkezésre állás biztosításához. Ez a konfiguráció időben letölti az új szabályzatokat és egyéb adatmennyiségeket. Szükség esetén további Azure AD-beli jelszavas védelmi proxykiszolgálók is üzembe helyezhetők.

Az Azure AD jelszavas védelem tartományvezérlő-ügynökének kialakítása csökkenti a magas rendelkezésre álláshoz kapcsolódó szokásos problémákat. Az Azure AD jelszavas védelem tartományvezérlő ügynöke a legutóbb letöltött jelszóházirend helyi gyorsítótárát tárolja. Még ha az összes regisztrált proxykiszolgáló elérhetetlenné válik, az Azure AD jelszavas védelem DC-ügynökei továbbra is érvénybe lépnek a gyorsítótárazott jelszavas házirendjében.

A nagyméretű üzemelő példányok jelszavas házirendjének ésszerű frissítési gyakorisága általában nap, nem óra vagy kevesebb. Így a proxykiszolgálók rövid kimaradása nem befolyásolja jelentősen az Azure AD jelszavas védelmét.

## <a name="deployment-requirements"></a>Üzembe helyezésre vonatkozó követelmények

A licenceléssel kapcsolatos információkért lásd az [Azure ad jelszavas védelem licencelési követelményeit](concept-password-ban-bad.md#license-requirements)ismertető témakört.

A következő alapvető követelmények érvényesek:

* Az Azure AD jelszavas védelem összetevőit tartalmazó összes gépnek telepítve kell lennie az univerzális C futtatókörnyezetnek.
    * A futtatókörnyezet beszerzéséhez győződjön meg arról, hogy Windows Update összes frissítését elvégezte. Azt is megteheti, hogy egy operációs rendszerre vonatkozó frissítési csomagban van. További információ: az [univerzális C futtatókörnyezet frissítése a Windowsban](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Olyan fiókra van szüksége, amely Active Directory tartományi rendszergazdai jogosultságokkal rendelkezik az erdő gyökértartományában a Windows Server Active Directory-erdő Azure AD-vel való regisztrálásához.
* A Key Distribution szolgáltatást a Windows Server 2012-es és újabb verzióit futtató tartomány összes tartományvezérlőjén engedélyezni kell. Ez a szolgáltatás alapértelmezés szerint a manuális trigger indításával engedélyezhető.

* A hálózati kapcsolatnak léteznie kell legalább egy, az egyes tartományokban lévő tartományvezérlő és legalább egy olyan kiszolgáló között, amely az Azure AD jelszavas védelemhez az proxy szolgáltatást üzemelteti. Ez a kapcsolat lehetővé teszi, hogy a tartományvezérlő hozzáférhessen az RPC Endpoint Mapper port 135-es portjához és a proxykiszolgáló RPC-kiszolgáló portjához.
    * Alapértelmezés szerint az RPC-kiszolgáló portja egy dinamikus RPC-port, de konfigurálható [statikus port használatára](#static)is.
* Minden olyan gépen, amelyen telepítve van az Azure AD jelszavas védelmi proxy szolgáltatás, hálózati hozzáféréssel kell rendelkeznie a következő végpontokhoz:

    |**Végpont**|**Cél**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Azure AD jelszavas védelem funkció|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD jelszavas védelem – tartományvezérlő ügynök

Az Azure AD Password Protection DC-ügynökre az alábbi követelmények vonatkoznak:

* Az Azure AD jelszavas védelem tartományvezérlő-ügynök szoftverét futtató összes gépnek Windows Server 2012 vagy újabb rendszert kell futtatnia, beleértve a Windows Server Core kiadásait is.
    * A Active Directory tartományhoz vagy erdőhöz nem szükséges a Windows Server 2012 tartomány működési szintjének (DFL) vagy az erdő működési szintje (FFL). A [tervezési alapelvekben](concept-password-ban-bad-on-premises.md#design-principles)említettek szerint a DC-ügynök vagy a proxy szoftver futtatásához nincs szükség minimális DFL vagy FFL.
* Minden olyan gépen, amelyen telepítve van az Azure AD jelszavas védelmi proxy szolgáltatás, telepíteni kell a .NET 4.7.2-t.
    * Ha a .NET 4.7.2 még nincs telepítve, töltse le és futtassa a [Windows rendszerhez készült .NET-keretrendszer 4.7.2 offline telepítője által](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)talált telepítőt.
* Az Azure AD jelszavas védelem DC Agent szolgáltatást futtató Active Directory tartományoknak elosztott fájlrendszer replikációt (DFSR) kell használniuk a SYSVOL-replikációhoz.
   * Ha a tartomány még nem használja a DFSR-t, az Azure AD jelszavas védelem telepítése előtt át kell telepítenie. További információ [: SYSVOL-replikáció áttelepítési útmutatója: FRS – elosztott fájlrendszer replikációs szolgáltatása](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Az Azure AD jelszavas védelem TARTOMÁNYVEZÉRLŐi ügynöke jelenleg a SYSVOL-replikációhoz használt FRS (a megelőző technológia DFSR) használatával működő tartományvezérlőkön települ, de a szoftver nem fog megfelelően működni ebben a környezetben.
    >
    > További negatív mellékhatások például az egyes fájlok replikálásának meghiúsulása, a SYSVOL visszaállítási eljárásai pedig sikeresek, de az összes fájl replikálásának csendes sikertelensége esetén sikertelenek lesznek.
    >
    > A lehető leghamarabb telepítse át a tartományt a DFSR használatára, mind a DFSR rejlő előnyeit, mind pedig az Azure AD jelszavas védelem üzembe helyezésének feloldását. A szoftver jövőbeli verziói automatikusan le lesznek tiltva, ha olyan tartományban fut, amely továbbra is FÁJLREPLIKÁCIÓS szolgáltatást használ.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD jelszavas védelem proxy szolgáltatás

Az Azure AD jelszavas védelem proxy szolgáltatására az alábbi követelmények vonatkoznak:

* Az Azure AD jelszavas védelem-proxy szolgáltatást futtató összes gépnek Windows Server 2012 R2 vagy újabb rendszert kell futtatnia, beleértve a Windows Server Core kiadásait is.

    > [!NOTE]
    > Az Azure AD jelszavas védelmi proxy szolgáltatás üzembe helyezése kötelezően szükséges az Azure AD-jelszavas védelem üzembe helyezéséhez, annak ellenére, hogy a tartományvezérlő rendelkezik kimenő közvetlen internetkapcsolattal.

* Minden olyan gépen, amelyen telepítve van az Azure AD jelszavas védelmi proxy szolgáltatás, telepíteni kell a .NET 4.7.2-t.
    * Ha a .NET 4.7.2 még nincs telepítve, töltse le és futtassa a [Windows rendszerhez készült .NET-keretrendszer 4.7.2 offline telepítője által](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)talált telepítőt.
* Az Azure AD jelszavas védelmi proxy szolgáltatást futtató összes gépet úgy kell konfigurálni, hogy a tartományvezérlők számára engedélyezze a proxy szolgáltatásba való bejelentkezést. Ezt a képességet a "számítógép elérése a hálózatról" jogosultság-hozzárendelésen keresztül vezérelheti.
* Az Azure AD jelszavas védelmi proxy szolgáltatást futtató gépeket úgy kell konfigurálni, hogy engedélyezzék a kimenő TLS 1,2 HTTP-forgalmat.
* *Globális rendszergazdai* vagy *biztonsági rendszergazdai* fiók az Azure ad jelszavas védelmi proxy szolgáltatás és az erdő Azure ad-vel való regisztrálásához.
* A hálózati hozzáférés engedélyezéséhez engedélyezni kell az [alkalmazásproxy-környezet telepítési eljárásaiban](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)megadott portok és URL-címek készletét.

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD összekapcsolási ügynök frissítési előfeltételei

Az Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatását az Azure AD jelszavas védelem proxy szolgáltatásával párhuzamosan telepíti a rendszer. További konfigurálásra van szükség ahhoz, hogy a Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatása képes legyen működni:

* Ha a környezet HTTP-proxykiszolgálót használ, kövesse a [meglévő helyszíni proxykiszolgálók használata](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)című témakörben megadott útmutatást.
* A Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatásához a [TLS-követelményekben](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements)megadott TLS 1,2-lépéseket is meg kell adni.

> [!WARNING]
> Az Azure AD jelszavas védelem proxyja és az Azure AD Application Proxy a Microsoft Azure AD csatlakozás Agent Updater szolgáltatás különböző verzióit telepíti, ezért az utasítások az alkalmazásproxy tartalmára vonatkoznak. Ezek a különböző verziók nem kompatibilisek egymás mellett, és ennek köszönhetően az ügynök frissítési szolgáltatása nem fog tudni kapcsolatba lépni az Azure-nal a szoftverfrissítések számára, ezért soha ne telepítse az Azure AD jelszavas védelmi proxyt és az alkalmazásproxy-t ugyanarra a gépre.

## <a name="download-required-software"></a>Szükséges szoftverek letöltése

A helyszíni Azure AD jelszavas védelem telepítéséhez két kötelező telepítő szükséges:

* Azure AD Password Protection DC-ügynök (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD jelszavas védelmi proxy (*AzureADPasswordProtectionProxySetup.exe*)

Töltse le mindkét telepítőt a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>A proxy szolgáltatás telepítése és konfigurálása

Az Azure AD jelszavas védelem-proxy szolgáltatás általában a helyszíni AD DS-környezetben található tagkiszolgálón található. A telepítés után az Azure AD jelszavas védelmi proxy szolgáltatás kommunikál az Azure AD-vel, hogy megőrizze az Azure AD-bérlőhöz tartozó globális és ügyfél által tiltott jelszavak listáját.

A következő szakaszban az Azure AD jelszavas védelem DC-ügynökeit telepíti a helyi AD DS környezet tartományvezérlőinek tartományvezérlőjén. Ezek a DC-ügynökök kommunikálnak a proxy szolgáltatással, hogy megkapják a legutóbb betiltott jelszavak listáját a jelszó-módosítási események a tartományon belüli feldolgozásához.

Válasszon ki egy vagy több kiszolgálót az Azure AD jelszavas védelem proxy szolgáltatásának üzemeltetéséhez. A következő szempontokat kell figyelembe venni a-kiszolgáló (k) esetében:

* Minden ilyen szolgáltatás csak egyetlen erdőhöz biztosít jelszavas házirendeket. A gazdaszámítógépnek csatlakoznia kell az erdő bármely tartományához.
* A proxy szolgáltatást a root vagy a Child tartományba, vagy ezek kombinációját is telepítheti.
* Az erdő minden tartománya és egy jelszavas védelmi proxykiszolgáló között legalább egy TARTOMÁNYVEZÉRLŐnek hálózati kapcsolatra van szüksége.
* Az Azure AD jelszavas védelmi proxy szolgáltatást egy tartományvezérlőn futtathatja tesztelésre, de a tartományvezérlőhöz internetkapcsolat szükséges. Ez a kapcsolat biztonsági szempontból fontos lehet. Ezt a konfigurációt csak tesztelésre javasoljuk.
* A redundancia érdekében az erdőn belül legalább két Azure AD-alapú jelszavas védelmi proxykiszolgálót ajánlunk, ahogy azt az előző, [magas rendelkezésre állással kapcsolatos szempontokat](#high-availability-considerations)ismertető szakaszban is említettük.
* Nem támogatott az Azure AD Password Protection proxy szolgáltatás futtatása írásvédett tartományvezérlőn.

Az Azure AD jelszavas védelem proxy szolgáltatásának telepítéséhez hajtsa végre a következő lépéseket:

1. Az Azure AD jelszavas védelem proxy szolgáltatásának telepítéséhez futtassa a `AzureADPasswordProtectionProxySetup.exe` szoftver telepítőjét.

    A Szoftvertelepítés nem igényel újraindítást, és a szabványos MSI-eljárások használatával automatizálható, ahogy az alábbi példában is látható:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > A telepítési hiba elkerülése érdekében a Windows tűzfal szolgáltatásnak futnia kell a csomag telepítése előtt `AzureADPasswordProtectionProxySetup.exe` .
    >
    > Ha a Windows tűzfal úgy van konfigurálva, hogy ne fusson, a megoldás a telepítés során átmenetileg engedélyezi és futtatja a tűzfal szolgáltatást. A proxykiszolgáló a telepítés után nem rendelkezik specifikus függőséggel a Windows tűzfalon.
    >
    > Ha külső gyártótól származó tűzfalat használ, azt továbbra is konfigurálni kell, hogy az megfeleljen a telepítési követelményeknek. Ezek közé tartozik a 135-es port és a proxy RPC-kiszolgáló portjának bejövő hozzáférésének engedélyezése. További információ: az [üzembe helyezési követelmények](#deployment-requirements)előző szakasza.

1. Az Azure AD jelszavas védelem proxy szoftver tartalmaz egy új PowerShell-modult `AzureADPasswordProtection` . A következő lépések különféle parancsmagokat futtatnak ebből a PowerShell-modulból.

    A modul használatához nyisson meg egy PowerShell-ablakot rendszergazdaként, és importálja az új modult az alábbiak szerint:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Az Azure AD jelszavas védelem proxy szolgáltatásának futtatásához használja a következő PowerShell-parancsot:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Az eredménynek a *futó* **állapotot** kell mutatnia.

1. A proxy szolgáltatás a gépen fut, de nem rendelkezik hitelesítő adatokkal az Azure AD-vel való kommunikációhoz. Regisztrálja az Azure AD jelszavas védelmi proxykiszolgálót az Azure AD-ben a `Register-AzureADPasswordProtectionProxy` parancsmag használatával.

    Ehhez a parancsmaghoz *globális rendszergazdai* vagy *biztonsági rendszergazdai* hitelesítő adatokra van szükség az Azure-bérlőhöz. Ezt a parancsmagot helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatni kell.

    Miután a parancs egyszer sikeresen megtörtént, további meghívások is sikeresek lesznek, de szükségtelenek.

    A `Register-AzureADPasswordProtectionProxy` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure AD Multi-Factor Authentication, de a harmadik mód nem.

    > [!TIP]
    > Előfordulhat, hogy egy adott Azure-bérlő esetében a parancsmag első futtatásakor észrevehető késleltetési idő van. Ha nem jelent hibát, ne aggódjon ez a késés.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód nem működik a Server Core operációs rendszereken. Ehelyett használja a következő hitelesítési módok egyikét. Ez a mód akkor is meghiúsulhat, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megkerülő megoldás a konfiguráció letiltása, a proxy regisztrálása, majd újbóli engedélyezése.

     * Eszköz-kód hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Amikor a rendszer rákérdez, a hivatkozásra kattintva nyisson meg egy webböngészőt, és adja meg a hitelesítési kódot.

     * Csendes (jelszó-alapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód meghiúsul, ha az Azure AD-Multi-Factor Authentication a fiókja számára szükséges. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel MFA-t.
        >
        > Azt is megteheti, hogy az MFA szükséges, ha az Azure-eszköz regisztrációja (amelyet az Azure AD jelszavas védelem keretében használ) úgy van konfigurálva, hogy globálisan megkövetelje az MFA-t. Ennek a követelménynek a megkerülő megoldásához használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy átmenetileg kihasználhatja az Azure-eszköz regisztrációjának MFA-követelményét is.
        >
        > A módosítás végrehajtásához keresse meg és válassza ki **Azure Active Directory** a Azure Portal, majd az **eszközök > eszközbeállítások** elemet. Állítsa be a **többtényezős hitelesítés megkövetelése eszközt a** *nem* értékre való csatlakoztatáshoz. Ügyeljen rá, hogy ezt a beállítást állítsa vissza az *Igen* értékre, ha a regisztráció befejeződött.
        >
        > Javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra lehessen kihagyni.

    Jelenleg nem kell megadnia a *-ForestCredential* paramétert, amely a jövőbeli funkciók számára van fenntartva.

    Az Azure AD jelszavas védelem-proxy szolgáltatás regisztrációját csak egyszer kell elvégezni a szolgáltatás élettartama során. Ezt követően az Azure AD jelszavas védelmi proxy szolgáltatás automatikusan végrehajtja a többi szükséges karbantartást.

1. Most regisztrálja a helyszíni Active Directory erdőben a szükséges hitelesítő adatokat az Azure-hoz való kommunikációhoz a `Register-AzureADPasswordProtectionForest` PowerShell-parancsmag használatával.

    > [!NOTE]
    > Ha több Azure AD-beli jelszavas védelmi proxykiszolgáló van telepítve a környezetben, nem számít, hogy melyik proxykiszolgálót használja az erdő regisztrálásához.

    A parancsmaghoz *globális rendszergazdai* vagy *biztonsági rendszergazdai* hitelesítő adatokra van szükség az Azure-bérlőhöz. Helyszíni Active Directory vállalati rendszergazdai jogosultságokat is igényel. Ezt a parancsmagot a helyi rendszergazdai jogosultságokkal rendelkező fiókkal is futtatnia kell. Az erdő regisztrálásához használt Azure-fiók eltérő lehet a helyszíni Active Directory fióktól.
    
    Ez a lépés erdőn keresztül egyszer fut.

    A `Register-AzureADPasswordProtectionForest` parancsmag a következő három hitelesítési módot támogatja. Az első két mód támogatja az Azure AD Multi-Factor Authentication, de a harmadik mód nem.

    > [!TIP]
    > Előfordulhat, hogy egy adott Azure-bérlő esetében a parancsmag első futtatásakor észrevehető késleltetési idő van. Ha nem jelent hibát, ne aggódjon ez a késés.

     * Interaktív hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ez a mód nem működik a Server Core operációs rendszereken. Ehelyett használja a következő két hitelesítési mód egyikét. Ez a mód akkor is meghiúsulhat, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve vannak. A megkerülő megoldással tiltsa le ezt a konfigurációt, regisztrálja az erdőt, majd engedélyezze újra.  

     * Eszköz-kód hitelesítési mód:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Amikor a rendszer rákérdez, a hivatkozásra kattintva nyisson meg egy webböngészőt, és adja meg a hitelesítési kódot.

     * Csendes (jelszó-alapú) hitelesítési mód:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ez a mód meghiúsul, ha az Azure AD-Multi-Factor Authentication a fiókja számára szükséges. Ebben az esetben használja az előző két hitelesítési mód egyikét, vagy használjon egy másik fiókot, amely nem igényel MFA-t.
        >
        > Azt is megteheti, hogy az MFA szükséges, ha az Azure-eszköz regisztrációja (amelyet az Azure AD jelszavas védelem keretében használ) úgy van konfigurálva, hogy globálisan megkövetelje az MFA-t. Ennek a követelménynek a megkerülő megoldásához használhat egy másik fiókot, amely támogatja az MFA-t az előző két hitelesítési mód egyikével, vagy átmenetileg kihasználhatja az Azure-eszköz regisztrációjának MFA-követelményét is.
        >
        > A módosítás végrehajtásához keresse meg és válassza ki **Azure Active Directory** a Azure Portal, majd az **eszközök > eszközbeállítások** elemet. Állítsa be a **többtényezős hitelesítés megkövetelése eszközt a** *nem* értékre való csatlakoztatáshoz. Ügyeljen rá, hogy ezt a beállítást állítsa vissza az *Igen* értékre, ha a regisztráció befejeződött.
        >
        > Javasoljuk, hogy az MFA-követelményeket csak tesztelési célokra lehessen kihagyni.

       Ezek a példák csak akkor sikeresek, ha az aktuálisan bejelentkezett felhasználó Active Directory tartományi rendszergazda is a legfelső szintű tartományhoz. Ha ez nem igaz, alternatív tartományi hitelesítő adatokat is megadhat a *-ForestCredential* paraméter használatával.

    A Active Directory erdő regisztrációját csak egyszer kell elvégezni az erdő élettartama során. Ezt követően az erdőben lévő Azure AD jelszavas védelem DC-ügynökei automatikusan elvégzik a többi szükséges karbantartást. Az `Register-AzureADPasswordProtectionForest` erdő sikeres futtatása után a parancsmag további meghívásai sikeresek lesznek, de szükségtelenek.
    
    `Register-AzureADPasswordProtectionForest`Ahhoz, hogy a sikeres legyen, legalább egy Windows Server 2012 vagy újabb rendszert futtató tartományvezérlőnek elérhetőnek kell lennie az Azure ad jelszavas védelmi proxykiszolgáló tartományában. Az Azure AD jelszavas védelem – TARTOMÁNYVEZÉRLŐi ügynök szoftverét nem kell minden tartományvezérlőn telepíteni a lépés előtt.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>A proxy szolgáltatás konfigurálása HTTP-proxyn keresztüli kommunikációra

Ha a környezete egy adott HTTP-proxy használatát igényli az Azure-hoz való kommunikációhoz, az alábbi lépésekkel konfigurálhatja az Azure AD jelszavas védelmi szolgáltatást.

Hozzon létre egy *AzureADPasswordProtectionProxy.exe.config* fájlt a `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mappában. A következő tartalom belefoglalása:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Ha a HTTP-proxy hitelesítést igényel, adja hozzá a *useDefaultCredentials* címkét:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Mindkét esetben cserélje le a- `http://yourhttpproxy.com:8080` t az adott HTTP-proxykiszolgáló címe és portja helyére.

Ha a HTTP-proxy engedélyezési házirend használatára van konfigurálva, hozzáférést kell biztosítania annak a számítógépnek a Active Directory számítógépfiókja számára, amely a proxy szolgáltatást a jelszavas védelemhez futtatja.

Azt javasoljuk, hogy a *AzureADPasswordProtectionProxy.exe.config* fájl létrehozása vagy frissítése után állítsa le és indítsa újra az Azure ad jelszavas védelmi proxy szolgáltatást.

A proxy szolgáltatás nem támogatja a HTTP-proxyhoz való csatlakozáshoz megadott hitelesítő adatok használatát.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>A proxy szolgáltatás beállítása egy adott port figyelésére

Az Azure AD jelszavas védelem tartományvezérlő ügynökének szoftvere RPC protokollt használ a proxy szolgáltatással való kommunikációhoz. Alapértelmezés szerint az Azure AD jelszavas védelem proxy szolgáltatása figyeli az összes elérhető dinamikus RPC-végpontot. A szolgáltatás beállítható úgy, hogy egy adott TCP-portot figyelje, szükség esetén a hálózat topológiája vagy a környezete által támasztott tűzfalszabályok miatt.

<a id="static" /></a>Ha úgy szeretné konfigurálni a szolgáltatást, hogy statikus porton fusson, használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmagot a következő módon:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> A módosítások érvénybe léptetéséhez le kell állítania, majd újra kell indítania az Azure AD jelszavas védelem proxy szolgáltatását.

Ha úgy szeretné konfigurálni a szolgáltatást, hogy egy dinamikus porton fusson, ugyanazt az eljárást használja, de állítsa vissza a *StaticPort* nullára:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> A módosítások érvénybe léptetéséhez le kell állítania, majd újra kell indítania az Azure AD jelszavas védelem proxy szolgáltatását.

Az Azure AD jelszavas védelem-proxy szolgáltatáshoz manuális újraindítás szükséges a port konfigurációjának módosítása után. A konfiguráció módosítása után nem kell újraindítani az Azure AD jelszavas védelmi DC Agent szolgáltatást a tartományvezérlőkön.

A szolgáltatás jelenlegi konfigurációjának lekérdezéséhez használja a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmagot az alábbi példában látható módon.

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A következő példa kimenete azt mutatja, hogy az Azure AD jelszavas védelmi proxy szolgáltatás dinamikus portot használ:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>A DC Agent szolgáltatás telepítése

Az Azure AD jelszavas védelem DC Agent szolgáltatásának telepítéséhez futtassa a `AzureADPasswordProtectionDCAgentSetup.msi` csomagot.

A Szoftvertelepítés a szabványos MSI-eljárások használatával automatizálható, ahogy az alábbi példában is látható:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

A `/norestart` jelző kihagyható, ha szeretné, hogy a telepítő automatikusan újraindítsa a számítógépet.

A szoftver telepítése vagy eltávolítása újraindítást igényel. Ennek a követelménynek az az oka, hogy a rendszer csak a jelszó-szűrő DLL-eket tölti be vagy távolítja el újra.

A helyszíni Azure AD jelszavas védelem telepítése akkor fejeződik be, ha a tartományvezérlő ügynök szoftverét tartományvezérlőre telepítették, és a számítógép újraindul. Nincs szükség más konfigurációra. A helyi tartományvezérlők jelszavas változási eseményei az Azure AD-ban konfigurált betiltott jelszavak listáját használják.

Ha engedélyezni szeretné az Azure AD-beli jelszavas védelmet a Azure Portal, vagy egyéni tiltott jelszavakat konfigurál, tekintse [meg a helyszíni Azure ad jelszavas védelem engedélyezése](howto-password-ban-bad-on-premises-operations.md)című témakört.

> [!TIP]
> Az Azure AD jelszavas védelem tartományvezérlő ügynökét olyan gépre is telepítheti, amely még nem tartományvezérlő. Ebben az esetben a szolgáltatás elindul és fut, de mindaddig inaktív marad, amíg a gépet tartományvezérlővé nem előléptetik.

## <a name="upgrading-the-proxy-service"></a>A proxy szolgáltatás frissítése

Az Azure AD jelszavas védelem proxy szolgáltatása támogatja az automatikus frissítést. Az automatikus frissítés a Microsoft Azure AD összekapcsolási ügynök frissítési szolgáltatását használja, amelyet a rendszer a proxy szolgáltatással párhuzamosan telepít. Az automatikus frissítés alapértelmezés szerint be van kapcsolva, és a parancsmag használatával engedélyezhető vagy letiltható `Set-AzureADPasswordProtectionProxyConfiguration` .

Az aktuális beállítás a parancsmag használatával kérdezhető le `Get-AzureADPasswordProtectionProxyConfiguration` . Javasoljuk, hogy az automatikus frissítési beállítás mindig engedélyezve legyen.

A `Get-AzureADPasswordProtectionProxy` parancsmag segítségével lekérdezhető az összes jelenleg telepített Azure ad jelszavas védelmi proxykiszolgáló szoftveres verziója egy erdőben.

### <a name="manual-upgrade-process"></a>Manuális verziófrissítési folyamat

A szoftver telepítőjének legújabb verziójának futtatásával manuális frissítést hajthat végre `AzureADPasswordProtectionProxySetup.exe` . A szoftver legújabb verziója a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

Nem szükséges az Azure AD Password Protection proxy szolgáltatás aktuális verziójának eltávolítása – a telepítő helyben történő frissítést hajt végre. A proxy szolgáltatás frissítésekor nem szükséges újraindítást végezni. Előfordulhat, hogy a szoftverfrissítés szabványos MSI-eljárásokat használ, például: `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>A DC-ügynök frissítése

Ha az Azure AD jelszavas védelem DC-ügynökének újabb verziója érhető el, a frissítés a szoftvercsomag legújabb verziójának futtatásával valósítható meg `AzureADPasswordProtectionDCAgentSetup.msi` . A szoftver legújabb verziója a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

A DC-ügynök szoftverének aktuális verzióját nem szükséges eltávolítani – a telepítő helyben történő frissítést hajt végre. A DC-ügynök szoftverének frissítésekor mindig újraindítás szükséges – ezt a követelményt az alapvető Windows-viselkedés okozza.

Előfordulhat, hogy a szoftverfrissítés szabványos MSI-eljárásokat használ, például: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

`/norestart`Ha szeretné, hogy a telepítő automatikusan újraindítsa a gépet, kihagyhatja a jelzőt.

A `Get-AzureADPasswordProtectionDCAgent` parancsmag segítségével lekérdezhető az összes jelenleg telepített Azure ad Password Protection DC-ügynök szoftveres verziója egy erdőben.

## <a name="next-steps"></a>Következő lépések

Most, hogy telepítette az Azure AD jelszavas védelemhez szükséges szolgáltatásokat a helyszíni kiszolgálókon, [engedélyezze az Azure ad jelszavas védelmet a Azure Portal](howto-password-ban-bad-on-premises-operations.md) az üzembe helyezés befejezéséhez.
