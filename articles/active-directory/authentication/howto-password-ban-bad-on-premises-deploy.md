---
title: Üzembe helyezése az Azure AD jelszó protection előzetes verziója
description: Az Azure AD jelszó protection előzetes kiadásáról hozzá tartozó rossz jelszavak helyszíni üzembe helyezése
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: c74cd5c8a8cfd00d61006c7ebbc4ced299b68e40
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422252"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Előzetes verzió: Azure AD jelszóvédelem üzembe helyezése

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Most, hogy egy-egy [jelszavas védelem az Azure AD a Windows Server Active Directory a kényszerítés módja](concept-password-ban-bad-on-premises.md), és hajtsa végre a központi telepítés a következő lépéssel.

## <a name="deployment-strategy"></a>Üzembe helyezési stratégiához

A Microsoft azt javasolja, hogy minden telepítéshez rendszervizsgálati módban indítsa el. Vizsgálati üzemmód az alapértelmezett kezdeti beállítása ahol folytathatja a jelszavak állítható be, és az eseménynapló-bejegyzéseket létrehozni minden le lesz tiltva. Proxy-kiszolgáló (ko) és a tartományvezérlő ügynökök teljes mértékben telepített rendszervizsgálati módban, rendszeres figyelést kell elvégezni ahhoz, hogy milyen hatást jelszóházirend meghatározása kényszerítési lenne a felhasználók és a környezet, ha a házirend kényszerítve lenne.

Az ellenőrzési szakaszban található számos szervezet:

* A meglévő operatív folyamatok használata biztonságosabb, jelszavak javítása van szükségük.
* Felhasználók vannak bemutatásával nem biztonságos jelszavak rendszeresen kiválasztása
* Tájékoztassa a felhasználóknak a közelgő változást végpontbiztonság kényszerítése, az hatással lehet rájuk, és segít jobban megérteni, hogyan kiválaszthatnak biztonságosabb, jelszavak van szükségük.

A funkció elfogadható időn rendszervizsgálati módban futott, miután a kényszerítési konfigurációját a lehet tükrözni **naplózási** való **érvényesítése** ezáltal igénylő biztonságosabb, jelszavak. Ez idő alatt célirányos figyelése, célszerű.

## <a name="deployment-requirements"></a>Központi telepítésére vonatkozó követelmények

* Minden tartományvezérlő, amelyen telepíteni szeretné az Azure AD jelszó DC védelmi ügynök szolgáltatás futnia kell a Windows Server 2012 vagy újabb.

   > [!NOTE]
   > Server Core-alapú operációs rendszerek jelenleg nem támogatottak. Ez a támogatás általánosan elérhető tervezünk

* Minden olyan gép, amelyen telepíteni szeretné az Azure AD jelszóvédelem proxyszolgáltatás futnia kell a Windows Server 2012 R2 vagy újabb.

   > [!NOTE]
   > Server Core-alapú operációs rendszerek jelenleg nem támogatottak. Ez a támogatás általánosan elérhető tervezünk

* Minden olyan gép, amelyen telepítve vannak az Azure AD jelszó-védelem összetevői beleértve a tartományvezérlőket kell rendelkeznie a Universal C futásidejű telepítve.
Ez lehetőleg azáltal, hogy teljes mértékben kijavítja a gép Windows Update-en keresztül történik. Ellenkező esetben lehet, egy megfelelő operációsrendszer-specifikus frissítési csomag telepítve – lásd: [Universal C-modul a Windows Update](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Minden olyan tartományban legalább egy tartományvezérlő és az Azure AD jelszó védelme proxy szolgáltatást futtató legalább egy kiszolgáló között léteznie kell a hálózati kapcsolatot. A kapcsolat lehetővé teszi a tartományvezérlő hozzáférés RPC-végpont eseményleképező portja (135-ös) és a proxy szolgáltatást az RPC-kiszolgáló portja.  Az RPC-kiszolgáló portja alapértelmezés szerint dinamikus RPC-portot, de konfigurálható (lásd alább) a statikus port használatára.
* Minden gép üzemeltetése az Azure AD jelszó védelme proxy szolgáltatás az alábbi végpontok hálózati hozzáféréssel kell rendelkeznie:

    |Végpont |Cél|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Az Azure AD jelszó-védelmi funkciók|

* Egy globális rendszergazdai fiókkal az Azure AD jelszó protection proxy szolgáltatás és az erdő regisztrálhat az Azure ad-ben.
* Az Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában, a Windows Server Active Directory-erdő regisztrálni az Azure AD-fiók.
* Minden olyan Active Directory-tartományban, a Tartományvezérlőn futó ügynök szolgáltatás szoftvert kell használnia elosztott fájlrendszer replikációs szolgáltatása a sysvol replikáció.

## <a name="single-forest-deployment"></a>Egyetlen erdő telepítés

Az előzetes verziója az Azure AD jelszóvédelem a proxy szolgáltatás akár két kiszolgálóra van telepítve, és a tartományvezérlő-ügynökszolgáltatás is növekményes üzembe helyezhetők az Active Directory-erdő összes tartományvezérlőjére.

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>A szoftver letöltése

Nincsenek Azure AD jelszóvédelem, amely letölthető a két szükséges telepítőinek a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszó védelme proxy szolgáltatás telepítése és konfigurálása

1. Válasszon egy vagy több kiszolgálót az Azure AD jelszó védelme proxy szolgáltatás üzemeltetéséhez.
   * Minden ilyen szolgáltatás lehet csak jelszóházirendet egyetlen erdő, és a gazdagépen kell lennie egy tartományhoz tartományhoz (gyökér- és alárendelt egyaránt támogatottak) az adott erdőben. Az Azure AD jelszó védelme proxy szolgáltatás feladatát, teljesítéséhez nincs léteznie kell az erdő minden tartományban legalább egy tartományvezérlő és az Azure AD jelszó védelmi Proxy gazdagép közötti hálózati kapcsolat.
   * Támogatott telepítése és futtatása az Azure AD jelszó védelme proxy szolgáltatás egy tartományvezérlőn tesztelési célokra, de a tartományvezérlő ezután internetkapcsolatra van szükség.

   > [!NOTE]
   > A nyilvános előzetes verziója támogatja a legfeljebb két (2) proxy kiszolgálók minden erdőre.

2. Telepítse a jelszót a házirend-proxyszolgáltatás szoftvert a AzureADPasswordProtectionProxy.msi MSI-csomaggal.
   * A Szoftvertelepítés nem kell újraindítani. A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

      > [!NOTE]
      > A Windows tűzfal szolgáltatásnak futnia kell az AzureADPasswordProtectionProxy.msi MSI-csomag telepítése előtt, különben telepítési hiba történik. Futtassa a Windows tűzfal van konfigurálva, a megoldás-e ideiglenes engedélyezéséhez, és indítsa el a Windows tűzfal szolgáltatást a telepítési folyamat során. A proxy szoftvert a Windows tűzfal szoftver telepítése után nincs konkrét függőségre rendelkezik. A külső tűzfalat használ, ha továbbra is kell konfigurálni annak a központi telepítésére vonatkozó követelmények kielégítéséhez (engedélyezi a 135-ös portot a bejövő hozzáférést és az RPC-proxy kiszolgáló portja e dinamikus vagy statikus). [Üzembe helyezési követelményeket lásd:](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Nyisson meg egy PowerShell-ablakot rendszergazdaként.
   * Az Azure AD jelszóvédelem Proxy szoftver AzureADPasswordProtection nevű új PowerShell-modult tartalmaz. A következő lépések alapján különböző parancsmagok futtatja a PowerShell-modult, és feltételezik, hogy megnyitották egy új PowerShell-ablakot, és a következőképpen importálta-e az új modul:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > A telepítő szoftver módosítja a gazdagép frissítéséből PSModulePath környezeti változót. Ahhoz, hogy a módosítás érvénybe léptetéséhez, így a AzureADPasswordProtection powershell-modul importálható és használt szükség lehet egy új PowerShell ablakot is megnyit.

   * Ellenőrizze, hogy a szolgáltatás fut, a következő PowerShell-paranccsal: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Az eredmény eredményt kell előállítania a **állapot** "Fut" eredményt visszaadó.

4. A proxy regisztrálása.
   * 3. lépés befejezése után az Azure AD jelszó védelme proxy szolgáltatás fut a gépen, de még nem rendelkezik a szükséges hitelesítő adatokat az Azure AD-kommunikációhoz. Az Azure AD-regisztráció engedélyezéséhez, hogy lehetővé teszi az szükséges a `Register-AzureADPasswordProtectionProxy` PowerShell-parancsmagot. A parancsmag megköveteli a globális rendszergazdai hitelesítő adatait az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Amint azt a megadott proxy Service, a további indítások sikeres `Register-AzureADPasswordProtectionProxy` továbbra is sikeres legyen, de nem szükségesek.
      * A parancsmag módon futtatható:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         A példában csak akkor működik, ha az aktuálisan bejelentkezett felhasználó egyben a legfelső szintű tartomány Active Directory tartományi rendszergazda. Helyett adja meg a szükséges tartományi hitelesítő adatokat a `-ForestCredential` paraméter.

   > [!NOTE]
   > Ez a művelet sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a IESC, a proxy, majd engedélyezze újra IESC regisztrálni.

   > [!NOTE]
   > Regisztráció az Azure AD jelszó protection proxy szolgáltatás várhatóan egy egyszeri lépés a szolgáltatás élettartama során. A proxy szolgáltatás automatikusan végez minden egyéb szükséges maintainenance ettől kezdődően. Azt egy adott erdő sikeres, ha további indítások "Register-AzureADPasswordProtectionProxy", továbbra is sikeres legyen, de nem szükségesek.

   > [!TIP]
   > Előfordulhatnak olyan jelentős késleltetés (sok másodperc) egy adott Azure-bérlő esetében futtassa ezt a parancsmagot, a parancsmag végrehajtásának befejeződése előtt először. Csak akkor jelent hibát, ezt a késést nem tekinthető ijesztő.

5. Regisztrálja az erdőben.
   * A helyszíni Active Directory-erdő inicializálni kell a szükséges hitelesítő adatokkal való kommunikációhoz, az Azure-ban a `Register-AzureADPasswordProtectionForest` Powershell-parancsmagot. A parancsmag megköveteli a globális rendszergazdai hitelesítő adatait az Azure-bérlőhöz, valamint a helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában. Ez a lépés minden erdőre egyszer fut.
      * A parancsmag módon futtatható:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         A példában csak akkor működik, ha az aktuálisan bejelentkezett felhasználó egyben a legfelső szintű tartomány Active Directory tartományi rendszergazda. A másik lehetőség a - ForestCredential paraméteren keresztül szükséges tartományi hitelesítő adatokat.

         > [!NOTE]
         > Ez a művelet sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a IESC, a proxy, majd engedélyezze újra IESC regisztrálni.

         > [!NOTE]
         > Ha a proxykiszolgálók több vannak telepítve a környezetben, nem számít, mely proxykiszolgáló van megadva a fenti eljárás során.

         > [!TIP]
         > Előfordulhatnak olyan jelentős késleltetés (sok másodperc) egy adott Azure-bérlő esetében futtassa ezt a parancsmagot, a parancsmag végrehajtásának befejeződése előtt először. Csak akkor jelent hibát, ezt a késést nem tekinthető ijesztő.

   > [!NOTE]
   > Az Active Directory-erdő regisztrációs várhatóan egy egyszeri lépés az erdő élettartama során. A tartományt vezérlő ügynökök erdőben futtató automatikusan végre fogja hajtani a bármely más szükséges maintainenance ettől kezdve. Miután egy adott erdő esetén, további indítások sikeres `Register-AzureADPasswordProtectionForest` továbbra is sikeres legyen, de nem szükségesek.

   > [!NOTE]
   > Ahhoz, hogy `Register-AzureADPasswordProtectionForest` legalább egy Windows Server 2012 vagy újabb tartomány sikeres vezérlő elérhetőnek kell lennie a proxykiszolgálót a tartományban. Azonban esetében nem követelmény, hogy a tartományvezérlő ügynök szoftvert telepítenie minden olyan tartományvezérlőn, ez a lépés előtt.

6. Nem kötelező: Konfigurálja az Azure AD jelszó védelme proxy szolgáltatás egy adott portot figyeljen.
   * RPC TCP-n keresztül az Azure AD jelszó védelmi proxy szolgáltatással való kommunikációban használják az Azure AD jelszóvédelem DC ügynökszoftver a tartományvezérlőkön. Alapértelmezés szerint az Azure AD jelszóvédelem jelszó házirend Proxy szolgáltatás figyeli az összes rendelkezésre álló dinamikus RPC-végpont. Hálózati topológia vagy tűzfalra vonatkozó követelmények miatt szükséges, ha a szolgáltatás ehelyett beállítható egy adott TCP-porton figyeljen.
      * A szolgáltatás futtatásához egy statikus port megadásához használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmagot.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

      * A szolgáltatás futtatásához egy dinamikus port megadásához, használja ugyanazt az eljárást azonban StaticPort vissza 0 értékre, például így:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

   > [!NOTE]
   > Az Azure AD jelszó védelme proxy szolgáltatás manuális újraindítását igényli, a portkonfigurációjának módosítását követően. Nem kell újraindítani az ilyen jellegű konfigurációs módosítások elvégzése után a tartományvezérlőn futó tartományvezérlő ügynök szoftver.

   * Az aktuális konfigurációját a szolgáltatás használatával lehet lekérdezni a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmag az alábbi példában látható módon:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Az Azure AD jelszó DC védelmi ügynök szolgáltatás telepítése

* Telepítse az Azure AD jelszó DC védelmi ügynök szolgáltatás szoftver használatával a `AzureADPasswordProtectionDCAgent.msi` MSI-csomag:
   * A Szoftvertelepítés kell indítani a telepítés, és távolítsa el, mert az operációsrendszer-követelményt, hogy jelszót szűrő DLL-ek csak betöltése vagy a számítógép újraindítását követően a memóriából.
   * Olyan számítógépen, amelyen még nem egy tartományvezérlőt a tartományvezérlő ügynökszolgáltatásának telepítése támogatott. Ebben az esetben a szolgáltatás elindul, és futtassa, de más módon lesz inaktívnak lennie amíg után a gép egy tartományvezérlő hitelesítenie kelljen magát.

   A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például:  `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > A példaparancs msiexec eredményezi egy azonnali újraindítás; Ez elkerülhető az megadásával a `/norestart` jelzőt.

Miután egy tartományvezérlőre van telepítve, és újraindul, az Azure AD jelszóvédelem DC ügynök szoftver telepítése befejeződött. További konfigurálást nem kötelező vagy nem lehetséges.

## <a name="multiple-forest-deployments"></a>Több erdő üzembe helyezés

Nem vonatkoznak további követelmények üzembe helyezéséhez az Azure AD jelszóvédelem több-erdőkön keresztüli. Minden olyan erdőben, egymástól függetlenül van konfigurálva, az egyetlen erdővel telepítési szakaszban leírtak szerint. Minden egyes Azure AD jelszóvédelem Proxy csak támogatja az erdő van csatlakoztatva, a tartományvezérlőket. Az Azure AD jelszó szoftvert egy adott erdőben nincs tudomása a másik erdőben találhatók, az Active Directory bizalmi kapcsolati konfigurációkat függetlenül üzembe helyezett szoftvert az Azure AD jelszó.

## <a name="read-only-domain-controllers"></a>Írásvédett tartományvezérlők

Jelszó changes\sets soha nem feldolgozása, és megőrzi a írásvédett tartományvezérlők (RODC-k); Ehelyett ezeket a rendszer továbbítja az írható tartományvezérlő. Ezért van, a tartományvezérlő ügynök szoftver telepítéséhez az RODC-k nem szükséges.

## <a name="high-availability"></a>Magas rendelkezésre állás

Magas rendelkezésre állásának az Azure AD jelszóvédelem biztosítása a fő aggodalma a proxy kiszolgálók rendelkezésre állásának esetén egy erdő tartományvezérlői próbál töltse le az új szabályzatok vagy más adatokat az Azure-ból. A nyilvános előzetes verzióban támogatja a legfeljebb két proxykiszolgáló erdőnként. Minden tartományvezérlő ügynök egyszerű Ciklikus időszeleteléses stílus algoritmust használ, mely proxykiszolgáló hívást, és kihagyja keresztül, amelyek nem válaszolnak proxykiszolgálók meghatározásakor.
A magas rendelkezésre állású vonatkozó szokásos problémákat vannak hivatottak DC ügynökszoftver kialakítását. A tartományvezérlő-ügynök a legutóbb letöltött jelszóházirend tartalmazó helyi gyorsítótár tárolja. Akkor is, ha a proxykiszolgálók valamilyen okból elérhetetlenné regisztrált, a tartományvezérlő-ügynökök továbbra is a gyorsítótárazott jelszóházirend kényszerítése. Egy ésszerű frissítési gyakoriság a jelszóházirendek nagyméretű környezetben általában a ahhoz a nap, óra nem vagy kisebb.   Ezért a proxykiszolgálók rövid valamilyen okból kimaradás lép, nem váltják ki jelentős hatással az Azure AD-jelszó protection szolgáltatást vagy a biztonsági előnyöket működését.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a helyszíni kiszolgálókon az Azure AD jelszóvédelem szükséges szolgáltatásokat végezze el a [utáni telepítse a konfigurációs és jelentéskészítési információkat összegyűjtési](howto-password-ban-bad-on-premises-operations.md) a telepítés befejezéséhez.

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)
