---
title: Az Azure AD Multi-Factor Authentication használata az NPS-Azure Active Directory használatával
description: Ismerje meg, hogyan használható az Azure AD Multi-Factor Authentication képességei a meglévő hálózati házirend-kiszolgáló (NPS) hitelesítési infrastruktúrájának használatával
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 484dd8313710332660bb20d55f3dac2aa21bbc61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98232522"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-ad-multi-factor-authentication"></a>A meglévő hálózati házirend-kiszolgáló (NPS) infrastruktúrájának integrálása az Azure AD-vel Multi-Factor Authentication

Az Azure AD Multi-Factor Authentication hálózati házirend-kiszolgáló (NPS) bővítménye felhőalapú MFA-képességeket ad a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló bővítmény használatával telefonhívást, szöveges üzenetet vagy telefonos alkalmazást is hozzáadhat a meglévő hitelesítési folyamathoz anélkül, hogy új kiszolgálókat kellene telepítenie, konfigurálnia és karbantartani.

A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure AD-Multi-Factor Authentication között, hogy az összevont vagy szinkronizált felhasználók számára is biztosítson egy másik hitelesítési tényezőt.

## <a name="how-the-nps-extension-works"></a>A hálózati házirend-kiszolgáló bővítményének működése

Ha az Azure AD Multi-Factor Authentication hálózati házirend-kiszolgáló bővítményét használja, a hitelesítési folyamat a következő összetevőket tartalmazza:

1. A **NAS/VPN-kiszolgáló** fogadja a VPN-ügyfelektől érkező kéréseket, és RADIUS-kérelmekre konvertálja azokat a hálózati házirend-kiszolgálók
2. A **hálózati házirend-kiszolgáló** csatlakozik Active Directory Domain Serviceshoz (AD DS) a RADIUS-kérelmek elsődleges hitelesítésének végrehajtásához, és a sikeres művelet után átadja a kérést a telepített bővítményeknek.  
3. A **hálózati házirend-kiszolgáló bővítmény** elindít egy kérést az Azure AD-multi-Factor Authentication a másodlagos hitelesítéshez. Ha a bővítmény megkapja a választ, és ha az MFA-kérdés sikeres, akkor a hitelesítési kérést úgy hajtja végre, hogy a hálózati házirend-kiszolgálót olyan biztonsági jogkivonatokkal biztosítja, amelyekben az Azure STS által kiadott MFA-jogcím szerepel.
4. Az **Azure ad MFA** a Azure Active Directory (Azure ad) szolgáltatással kommunikál a felhasználó adatainak lekérése és a másodlagos hitelesítés végrehajtása a felhasználóhoz konfigurált ellenőrzési módszer használatával.

A következő ábra a magas szintű hitelesítési kérelmek folyamatát szemlélteti:

![A VPN-kiszolgálón keresztül a hálózati házirend-kiszolgáló és az Azure AD Multi-Factor Authentication NPS bővítmény használatával hitelesítő felhasználói hitelesítési folyamat ábrája](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>A RADIUS protokoll viselkedése és a hálózati házirend-kiszolgáló bővítmény

Mivel a RADIUS egy UDP protokoll, a küldő a csomagok elvesztését feltételezi, és a válaszra vár. Egy adott idő elteltével a kapcsolatok időtúllépést okozhatnak. Ha így van, a rendszer újraküldi a csomagot, mivel a feladó azt feltételezi, hogy a csomag nem éri el a célhelyet. A jelen cikkben található hitelesítési forgatókönyvben a VPN-kiszolgálók elküldik a kérést, és várniuk kell a válaszra. Ha a kapcsolat túllépi az időkorlátot, a VPN-kiszolgáló újra elküldi a kérelmet.

![A RADIUS UDP-csomagok folyamatának és az NPS-kiszolgáló válaszának időtúllépése utáni kérelmeknek a diagramja](./media/howto-mfa-nps-extension/radius-flow.png)

Előfordulhat, hogy az NPS-kiszolgáló nem válaszol a VPN-kiszolgáló eredeti kérelmére, mielőtt a kapcsolat megszakadt, mert az MFA-kérelem feldolgozása még folyamatban van. Előfordulhat, hogy a felhasználó nem válaszolt az MFA-kérésre, ezért az Azure AD Multi-Factor Authentication NPS bővítmény az esemény befejeződésére vár. Ebben az esetben a hálózati házirend-kiszolgáló a további VPN-kiszolgáló kérelmeket duplikált kérelemként azonosítja. Az NPS-kiszolgáló elveti ezeket az ismétlődő VPN-kiszolgálói kérelmeket.

![Az NPS-kiszolgáló diagramja, amely a RADIUS-kiszolgálótól érkező duplikált kérelmeket elveti](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

Ha megtekinti az NPS-kiszolgáló naplóit, láthatja, hogy ezek a további kérések el lettek vetve. Ennek a viselkedésnek a kialakítása úgy történik, hogy a végfelhasználók több kérést kapnak egyetlen hitelesítési kísérlethez. A hálózati házirend-kiszolgáló eseménynaplójában Elvetett kérelmek nem utalnak arra, hogy probléma van az NPS-kiszolgálóval vagy az Azure AD Multi-Factor Authentication NPS bővítménnyel.

Az Elvetett kérelmek minimálisra csökkentése érdekében javasoljuk, hogy a VPN-kiszolgálók legalább 60 másodperces időtúllépéssel legyenek konfigurálva. Ha szükséges, vagy ha csökkenteni kívánja az elvetett kérelmeket az eseménynaplókban, megnövelheti a VPN-kiszolgáló időtúllépési értékét 90 vagy 120 másodpercre.

Ennek az UDP protokollnak a működése miatt a hálózati házirend-kiszolgáló duplikált kérelmet kaphat, és egy másik MFA-kérést is küldhet, még azt követően is, hogy a felhasználó már válaszolt a kezdeti kérelemre. Ennek az időzítési feltételnek a elkerüléséhez az Azure AD Multi-Factor Authentication hálózati házirend-kiszolgáló bővítménye továbbra is szűri és elveti az ismétlődő kérelmeket, miután a rendszer sikeres választ küldött a VPN-kiszolgálónak.

![Az NPS-kiszolgáló diagramja továbbra is a VPN-kiszolgálótól érkező ismétlődő kérések elvetése után tíz másodpercig a sikeres válasz visszaadása után.](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

A hálózati házirend-kiszolgáló eseménynaplójában is láthatja az elvetett kérelmeket, még akkor is, ha az Azure AD Multi-Factor Authentication-kérés sikeres volt. Ez a várt viselkedés, és nem jelent problémát az NPS-kiszolgálóval vagy az Azure AD Multi-Factor Authentication NPS-bővítménnyel.

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

A hálózati házirend-kiszolgáló bővítménye automatikusan kezeli a redundanciát, így nincs szükség speciális konfigurációra.

Annyi Azure AD Multi-Factor Authentication-kompatibilis hálózati házirend-kiszolgálót hozhat létre, amennyire csak szüksége van. Ha több kiszolgálót is telepít, akkor mindegyikhez meg kell egyeznie a különbözeti ügyféltanúsítvány használatával. Az egyes kiszolgálókhoz tartozó tanúsítványok létrehozása azt jelenti, hogy az egyes tanúsítványokat egyenként is frissítheti, és nem kell aggódnia az összes kiszolgáló leállása miatt.

A VPN-kiszolgálók átirányítják a hitelesítési kéréseket, ezért tudniuk kell az új Azure AD Multi-Factor Authentication-kompatibilis NPS-kiszolgálókat.

## <a name="prerequisites"></a>Előfeltételek

A hálózati házirend-kiszolgáló bővítmény célja, hogy működjön a meglévő infrastruktúrával. Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel.

### <a name="licenses"></a>Licencek

Az Azure AD Multi-Factor Authentication hálózati házirend-bővítménye az [Azure ad multi-Factor Authentication licenccel](./concept-mfa-howitworks.md)rendelkező ügyfelek számára érhető el. Az Azure AD-Multi-Factor Authentication, például felhasználónként vagy hitelesítési licencek esetében a használaton kívüli licencek nem kompatibilisek a hálózati házirend-kiszolgáló bővítménnyel.

### <a name="software"></a>Szoftverek

Windows Server 2012 vagy újabb.

### <a name="libraries"></a>Kódtárak

Manuálisan kell telepítenie a következő tárat:

- [A Visual Studio 2015 szoftverhez készült Visual C++ terjeszthető változata](https://www.microsoft.com/download/details.aspx?id=48145)

A következő kódtárak automatikusan települnek a bővítménnyel.

- [Vizuális C++ újraterjeszthető csomagok a Visual Studio 2013 (x64) rendszerhez](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory modul a Windows PowerShell-verzió 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

A Windows PowerShell Microsoft Azure Active Directory modulja a telepítési folyamat részeként futtatott konfigurációs parancsfájllal is települ, ha még nincs jelen. Ha még nincs telepítve, a modult nem kell időben telepítenie.

### <a name="azure-active-directory"></a>Azure Active Directory

A hálózati házirend-kiszolgáló bővítményt használó mindenki számára Azure AD Connect használatával kell szinkronizálni az Azure AD-t, és regisztrálni kell az MFA-hoz.

A bővítmény telepítésekor szüksége lesz az Azure AD-bérlőhöz tartozó *bérlői azonosítóra* és rendszergazdai hitelesítő adatokra. A bérlő AZONOSÍTÓjának beszerzéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) az Azure-bérlő globális rendszergazdájaként.
1. Keresse meg és válassza ki a **Azure Active Directory**.
1. Az **Áttekintés** lapon megjelenik a *bérlő adatai* . A *bérlő azonosítója* mellett válassza a **Másolás** ikont az alábbi képernyőképen látható módon:

   ![A bérlő AZONOSÍTÓjának beolvasása a Azure Portalból](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A hálózati házirend-kiszolgálónak képesnek kell lennie kommunikálni a következő URL-címekkel a 80-es és a 443-es portokon

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /credentials.Azure.com*

Emellett a következő URL-címekhez való kapcsolódás szükséges ahhoz, hogy a [megadott PowerShell-parancsfájl használatával](#run-the-powershell-script)el lehessen végezni az adapter beállítását:

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.net*
* *https: \/ /www.powershellgallery.com*
* *https: \/ /aadcdn.msftauthimages.net*

## <a name="prepare-your-environment"></a>A környezet előkészítése

A hálózati házirend-kiszolgáló bővítmény telepítése előtt készítse elő a környezetet a hitelesítési forgalom kezeléséhez.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Hálózati házirend-kiszolgáló szerepkör engedélyezése tartományhoz csatlakoztatott kiszolgálón

Az NPS-kiszolgáló csatlakozik az Azure AD-hez, és hitelesíti az MFA-kérelmeket. Válasszon egy kiszolgálót ehhez a szerepkörhöz. Azt javasoljuk, hogy olyan kiszolgálót válasszon, amely nem kezeli más szolgáltatások kéréseit, mert a hálózati házirend-kiszolgáló bővítmény hibát jelez a nem RADIUS-kérelmeknél. A hálózati házirend-kiszolgálót a környezet elsődleges és másodlagos hitelesítési kiszolgálójának kell beállítania. A RADIUS-kérések nem állíthatók be másik kiszolgálóra.

1. A kiszolgálón nyissa meg a **Kiszolgálókezelő eszközt**. Válassza a **szerepkörök és szolgáltatások hozzáadása varázslót** a gyors *útmutató menüből.*
2. A telepítés típusa beállításnál válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés** lehetőséget.
3. Válassza ki a **hálózati házirend-és elérési szolgáltatások** kiszolgálói szerepkört. Előfordulhat, hogy egy ablak felugró ablakban tájékoztatja Önt a szerepkör futtatásához szükséges további funkciókról.
4. Folytassa a varázslót a *megerősítési* oldalig. Ha elkészült, válassza a **telepítés** lehetőséget.

A hálózati házirend-kiszolgáló szerepkör telepítése néhány percet is igénybe vehet. Ha elkészült, folytassa a következő részekkel, hogy konfigurálja a kiszolgálót a bejövő RADIUS-kérések kezelésére a VPN-megoldástól.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN-megoldás konfigurálása a hálózati házirend-kiszolgálóval való kommunikációhoz

Attól függően, hogy melyik VPN-megoldást használja, a RADIUS-hitelesítési házirend konfigurálásának lépései eltérőek. Konfigurálja a VPN-házirendet úgy, hogy az a RADIUS NPS-kiszolgálóra mutasson.

### <a name="sync-domain-users-to-the-cloud"></a>Tartományi felhasználók szinkronizálása a felhővel

Lehetséges, hogy ez a lépés már készen áll a bérlőre, de érdemes megnéznie, hogy a Azure AD Connect nemrég szinkronizálta-e az adatbázisokat.

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
2. **Azure Active Directory** kiválasztása  >  **Azure ad Connect**
3. Ellenőrizze, hogy a szinkronizálási állapot **engedélyezve** van-e, és hogy az utolsó szinkronizálás kevesebb mint egy órával ezelőtt történt-e.

Ha új szinkronizálási kört kell kiindulnia, tekintse meg a [Azure ad Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)című témakört.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>A felhasználók által használható hitelesítési módszerek meghatározása

Két tényező befolyásolja, hogy mely hitelesítési módszerek érhetők el egy NPS-bővítmény üzembe helyezésével:

* A RADIUS-ügyfél (VPN, NetScaler-kiszolgáló vagy más) és az NPS-kiszolgálók között használt jelszó-titkosítási algoritmus.
   - A **pap** az Azure ad multi-Factor Authentication összes hitelesítési módszerét támogatja a felhőben: telefonhívást, egyirányú szöveges üzenetet, a Mobile App notificationt, az esküt és a mobileszköz-ellenőrző kódot.
   - A **CHAPv2** és az **EAP** támogatja a telefonhívást és a Mobile apps-értesítést.

    > [!NOTE]
    > A hálózati házirend-kiszolgáló bővítmény központi telepítésekor ezeket a tényezőket használva kiértékelheti, hogy mely módszerek érhetők el a felhasználók számára. Ha a RADIUS-ügyfél támogatja a PAP-t, de az ügyfél UX nem tartalmaz bemeneti mezőket az ellenőrző kódhoz, akkor a telefonhívás és a Mobile App Notification a két támogatott lehetőség.
    >
    > A használt hitelesítési protokolltól (PAP, CHAP vagy EAP) függetlenül, ha az MFA-módszer szöveges (SMS, Mobile App ellenőrző kód vagy eskü típusú hardveres jogkivonat), és megköveteli, hogy a felhasználó kódot vagy szöveget írjon be a VPN-ügyfél felhasználói felületének beviteli mezőjébe, a hitelesítés sikeres lehet. A hálózati hozzáférési házirendben megadott RADIUS-attribútumok *azonban* *nem* továbbítódnak a (z) (a hálózati hozzáférési eszköz, például a VPN-átjáró) számára. Ennek eredményeképpen előfordulhat, hogy a VPN-ügyfél több hozzáféréssel rendelkezik, mint amennyit szeretné, vagy kevesebb hozzáférésre van szüksége, vagy nincs hozzáférése.

* Az ügyfélalkalmazás (VPN, NetScaler-kiszolgáló vagy más) által kezelhető bemeneti metódusok. A VPN-ügyfél például bizonyos módon lehetővé teszi, hogy a felhasználó szöveges vagy mobil alkalmazásból származó ellenőrző kódot írjon be?

A nem [támogatott hitelesítési módszereket letilthatja](howto-mfa-mfasettings.md#verification-methods) az Azure-ban.

### <a name="register-users-for-mfa"></a>Felhasználók regisztrálása MFA-hoz

A hálózati házirend-kiszolgáló bővítmény üzembe helyezése és használata előtt az Azure AD-Multi-Factor Authentication végrehajtásához szükséges felhasználókat regisztrálni kell az MFA-hoz. Ha tesztelni szeretné a bővítményt az üzembe helyezés során, szüksége lesz legalább egy olyan tesztelési fiókra, amely teljesen regisztrálva van az Azure AD Multi-Factor Authentication.

Ha egy teszt fiókot kell létrehoznia és konfigurálnia, kövesse az alábbi lépéseket:

1. Jelentkezzen be [https://aka.ms/mfasetup](https://aka.ms/mfasetup) egy teszt-fiókkal.
2. Kövesse az utasításokat az ellenőrzési módszer beállításához.
3. A Azure Portal rendszergazda felhasználóként [hozzon létre egy feltételes hozzáférési szabályzatot](howto-mfa-getstarted.md#create-conditional-access-policy) a tesztelési fiók többtényezős hitelesítésének megköveteléséhez.

> [!IMPORTANT]
>
> Győződjön meg arról, hogy a felhasználók sikeresen regisztráltak az Azure AD-Multi-Factor Authentication. Ha a felhasználók korábban csak az önkiszolgáló jelszó-visszaállításra (SSPR) vannak regisztrálva, akkor a *StrongAuthenticationMethods* engedélyezve van a fiókja számára. Az Azure AD-Multi-Factor Authentication akkor is érvényben van, ha a *StrongAuthenticationMethods* konfigurálva van, még akkor is, ha a felhasználó csak a SSPR van regisztrálva.
>
> A kombinált biztonsági regisztráció engedélyezhető, amely egyszerre konfigurálja a SSPR és az Azure AD-Multi-Factor Authentication. További információ: [a kombinált biztonsági információk regisztrációjának engedélyezése Azure Active Directoryban](howto-registration-mfa-sspr-combined.md).
>
> [Arra is kényszerítheti a felhasználókat, hogy regisztrálják újra a hitelesítési módszereket,](howto-mfa-userdevicesettings.md#manage-user-authentication-options) ha korábban csak engedélyezték a SSPR.

## <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének telepítése

> [!IMPORTANT]
> Telepítse a hálózati házirend-kiszolgáló bővítményt egy másik kiszolgálóra, mint a VPN-hozzáférési pont.

### <a name="download-and-install-the-nps-extension-for-azure-ad-mfa"></a>Töltse le és telepítse az Azure AD MFA NPS-bővítményét

A hálózati házirend-kiszolgáló bővítmény letöltéséhez és telepítéséhez hajtsa végre a következő lépéseket:

1. [Töltse le a hálózati házirend-kiszolgáló bővítményt](https://aka.ms/npsmfa) a Microsoft letöltőközpontból.
1. Másolja a bináris fájlt a konfigurálni kívánt hálózati házirend-kiszolgálóra.
1. Futtassa *setup.exe* , és kövesse a telepítési utasításokat. Ha hibát tapasztal, győződjön meg arról, hogy az [előfeltételként szükséges függvénytárak](#libraries) telepítése sikeres volt.

#### <a name="upgrade-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének frissítése

Ha később frissíti egy meglévő NPS-bővítmény telepítését, hogy elkerülje a mögöttes kiszolgáló újraindítását, hajtsa végre a következő lépéseket:

1. Távolítsa el a meglévő verziót.
1. Futtassa az új telepítőt.
1. Indítsa újra a *hálózati házirend-kiszolgáló (IAS)* szolgáltatást.

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A telepítő létrehoz egy PowerShell-parancsfájlt a `C:\Program Files\Microsoft\AzureMfa\Config` (ahol `C:\` a telepítési meghajtó). Ez a PowerShell-parancsfájl minden futtatáskor végrehajtja a következő műveleteket:

* Létrehoz egy önaláírt tanúsítványt.
* Társítja a tanúsítvány nyilvános kulcsát az Azure AD szolgáltatásban található egyszerű szolgáltatásnév számára.
* A tanúsítvány tárolása a helyi számítógép tanúsítványtárolójában.
* Hozzáférést biztosít a tanúsítvány titkos kulcsához a hálózati felhasználó számára.
* Újraindítja a hálózati házirend-kiszolgáló szolgáltatást.

Ha nem szeretne saját tanúsítványokat használni (a PowerShell-parancsfájl által létrehozott önaláírt tanúsítványok helyett), futtassa a PowerShell-parancsfájlt a hálózati házirend-bővítmény telepítésének befejezéséhez. Ha több kiszolgálóra telepíti a bővítményt, minden egyes kiszolgálónak saját tanúsítvánnyal kell rendelkeznie.

A terheléselosztási képességek és a redundancia biztosítása érdekében ismételje meg ezeket a lépéseket a további hálózati házirend-kiszolgálókon a kívánt módon:

1. Nyisson meg egy Windows PowerShell-parancssort rendszergazdaként.
1. Módosítsa a könyvtárat, ahová a telepítő létrehozta a PowerShell-parancsfájlt:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Futtassa a telepítő által létrehozott PowerShell-szkriptet.

   Előfordulhat, hogy először engedélyeznie kell a TLS 1,2 for PowerShellt ahhoz, hogy csatlakozni tudjon, és megfelelően töltse le a csomagokat:
   
   `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`

   > [!IMPORTANT]
   > Az Azure Government vagy az Azure China 21Vianet felhők használatát használó ügyfelek esetében először szerkessze a `Connect-MsolService` *AzureMfaNpsExtnConfigSetup.ps1* parancsfájlban található parancsmagokat, hogy tartalmazza a szükséges felhőhöz tartozó *AzureEnvironment* paramétereket. Válassza például a *-AzureEnvironment USGovernment* vagy a *-AzureEnvironment AzureChinaCloud*.
   >
   > További információ: a [MsolService paraméter referenciája](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Ha a rendszer kéri, jelentkezzen be az Azure AD-be rendszergazdaként.
1. PowerShell-kérések a bérlői AZONOSÍTÓhoz. Használja az előfeltételek szakaszban található Azure Portalból másolt *bérlői azonosító GUID azonosítót* .
1. A szkript befejezésekor megjelenik egy sikeres üzenet.  

Ha az előző számítógép-tanúsítvány lejárt, és új tanúsítvány lett létrehozva, törölje a lejárt tanúsítványokat. A lejárt tanúsítványok miatt a hálózati házirend-kiszolgáló bővítménnyel kapcsolatos problémák merülhetnek fel.

> [!NOTE]
> Ha saját tanúsítványokat használ a PowerShell-parancsfájllal történő tanúsítványok létrehozása helyett, akkor győződjön meg arról, hogy a hálózati házirend-kiszolgáló elnevezési konvencióhoz vannak igazítva. A tulajdonos nevének a **CN = \<TenantID\> , OU = Microsoft NPS bővítménynek** kell lennie.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government vagy az Azure China 21Vianet további lépések

Az Azure Government vagy az Azure China 21Vianet felhők használatát használó ügyfelek esetében a következő további konfigurációs lépésekre van szükség az egyes NPS-kiszolgálókon.

> [!IMPORTANT]
> Csak akkor konfigurálja ezeket a beállításjegyzék-beállításokat, ha Ön Azure Government vagy Azure China 21Vianet-ügyfél.

1. Ha Ön Azure Government vagy Azure China 21Vianet-ügyfél, nyissa meg a **Beállításszerkesztőt** a hálózati házirend-kiszolgálón.
1. Nyissa meg a `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa` címet.
1. Azure Government ügyfelek esetében állítsa be a következő kulcs értékeit:

    | Beállításkulcs       | Érték |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Az Azure China 21Vianet ügyfelei esetében állítsa be a következő kulcs-értékeket:

    | Beállításkulcs       | Érték |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Ismételje meg az előző két lépést az egyes NPS-kiszolgálók beállításkulcs-értékeinek beállításához.
1. Indítsa újra a hálózati házirend-kiszolgáló szolgáltatást az egyes NPS-kiszolgálókon.

    A minimális hatás érdekében minden hálózati házirend-kiszolgálót ki kell kapcsolni az NLB-rotációból, és várnia kell, amíg az összes kapcsolat el nem telik.

### <a name="certificate-rollover"></a>Tanúsítvány-rollover

A hálózati házirend-kiszolgáló bővítmény kiadási *1.0.1.32* a több tanúsítvány olvasása mostantól támogatott. Ez a funkció segít a működés közbeni tanúsítványok frissítéseinek megkönnyítése a lejárat előtt. Ha a szervezete az NPS bővítmény korábbi verzióját futtatja, frissítsen a *1.0.1.32* vagy újabb verzióra.

A szkript által létrehozott tanúsítványok `AzureMfaNpsExtnConfigSetup.ps1` 2 évig érvényesek. Lejárati tanúsítványok figyelése. A hálózati házirend-kiszolgáló kiterjesztéséhez tartozó tanúsítványok a *helyi számítógép* tanúsítványtárolójában vannak elhelyezve a *személyes* és a telepítési parancsfájlhoz megadott bérlői *azonosító számára.*

Ha egy tanúsítvány a lejárati dátumra közeledik, egy új tanúsítványt kell létrehozni a lecseréléséhez.  Ezt a folyamatot az újbóli futtatásával hajtja végre `AzureMfaNpsExtnConfigSetup.ps1` , és megtartja ugyanazt a bérlői azonosítót, amikor a rendszer kéri. Ezt a folyamatot meg kell ismételni a környezet minden NPS-kiszolgálóján.

## <a name="configure-your-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének konfigurálása

Ha a környezet előkészített, és a hálózati házirend-bővítmény már telepítve van a szükséges kiszolgálókra, beállíthatja a bővítményt.

Ez a szakasz tervezési szempontokat és javaslatokat tartalmaz a hálózati házirend-bővítmények sikeres üzembe helyezéséhez.

### <a name="configuration-limitations"></a>Konfigurációs korlátozások

- Az Azure AD Multi-Factor Authentication NPS-bővítménye nem tartalmaz olyan eszközöket, amelyekkel a felhasználók és beállítások áttelepíthetők az MFA-kiszolgálóról a felhőbe. Ezért javasoljuk, hogy a meglévő telepítés helyett a bővítményt használja az új központi telepítésekhez. Ha a bővítményt egy meglévő üzemelő példányon használja, a felhasználóknak újra kell végezniük az MFA adatainak a felhőben való feltöltéséhez.  
- A hálózati házirend-kiszolgáló bővítmény az UPN-t használja a helyszíni AD DS környezetből, hogy azonosítsa a felhasználót az Azure AD-Multi-Factor Authentication a másodlagos hitelesítés végrehajtásához. A bővítmény konfigurálható úgy, hogy más azonosítót használjon, például alternatív bejelentkezési azonosítót vagy egyéni AD DS mezőt, amely nem egyszerű felhasználónév. További információkért tekintse meg a [multi-Factor Authentication hálózati házirend-bővítményének speciális konfigurációs beállításait](howto-mfa-nps-extension-advanced.md)ismertető cikket.
- Nem minden titkosítási protokoll támogatja az összes ellenőrzési módszert.
   - A **pap** támogatja a telefonhívást, egyirányú szöveges üzenetet, a Mobile App notificationt és a Mobile App ellenőrző kódját
   - A **CHAPv2** és az **EAP** támogatásának telefonos hívása és a Mobile App Notification

### <a name="control-radius-clients-that-require-mfa"></a>Az MFA-t igénylő RADIUS-ügyfelek szabályozása

Miután engedélyezte az MFA-t egy RADIUS-ügyfél számára a hálózati házirend-kiszolgáló bővítmény használatával, az ügyfél összes hitelesítése szükséges az MFA végrehajtásához. Ha bizonyos RADIUS-ügyfelek esetében engedélyezni szeretné az MFA-t, de mások nem, akkor két hálózati házirend-kiszolgálót is beállíthat, és a bővítményt csak az egyikre telepítheti.

Konfigurálja azokat a RADIUS-ügyfeleket, amelyeknek az MFA-t szeretné megkövetelni a bővítménysel konfigurált hálózati házirend-kiszolgálónak küldött kérések küldéséhez, valamint más RADIUS-ügyfeleket az NPS-kiszolgálóhoz, amely nincs beállítva a bővítménnyel.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Az MFA-ban nem regisztrált felhasználók előkészítése

Ha az MFA-ban nem regisztrált felhasználók vannak, akkor megadhatja, hogy mi történjen a hitelesítéskor. Ennek a viselkedésnek a szabályozásához használja a beállításjegyzék elérési útja *HKLM\Software\Microsoft\AzureMFA* *REQUIRE_USER_MATCH* értékét. Ez a beállítás egyetlen konfigurációs lehetőséggel rendelkezik:

| Kulcs | Érték | Alapértelmezett |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | IGAZ/HAMIS | Nincs beállítva (megegyezik az igaz értékkel) |

Ez a beállítás határozza meg, hogy mi a teendő, ha a felhasználó nincs regisztrálva az MFA-hoz. Ha a kulcs nem létezik, nincs beállítva, vagy *igaz* értékre van állítva, és a felhasználó nincs regisztrálva, a bővítmény MEGHIÚSUL az MFA-kérdésben.

Ha a kulcs *hamis* értékre van állítva, és a felhasználó nincs regisztrálva, a hitelesítés az MFA végrehajtása nélkül folytatódik. Ha egy felhasználó regisztrálva van az MFA-ban, akkor is hitelesítenie kell magát az MFA-val, ha *REQUIRE_USER_MATCH* *hamis* értékre van állítva.

Dönthet úgy, hogy létrehozza ezt a kulcsot, és *false (hamis* ) értékre állítja a felhasználók bevezetését, és előfordulhat, hogy az Azure AD-multi-Factor Authentication még nem minden regisztrálva van. Mivel azonban a kulcs beállítása lehetővé teszi, hogy az MFA-ban nem regisztrált felhasználók bejelentkezzenek, el kell távolítania ezt a kulcsot az éles környezetbe való belépés előtt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="nps-extension-health-check-script"></a>NPS-bővítmény állapot-ellenőrzési parancsfájlja

A következő szkripttel végezheti el az alapszintű állapot-ellenőrzési lépéseket a hálózati házirend-kiszolgáló bővítményének hibaelhárításakor.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hogyan ellenőrizze, hogy az ügyféltanúsítvány a várt módon van-e telepítve?

Keresse meg a telepítő által a tanúsítvány-tárolóban létrehozott önaláírt tanúsítványt, és ellenőrizze, hogy a titkos kulcs rendelkezik-e a felhasználói *hálózati szolgáltatáshoz* megadott engedélyekkel. A tanúsítvány tulajdonosának neve **CN \<tenantid\> , OU = Microsoft NPS bővítmény**

A szkript által létrehozott önaláírt tanúsítványok `AzureMfaNpsExtnConfigSetup.ps1` érvényességi időtartama két év. A tanúsítvány telepítésének ellenőrzésekor azt is ellenőrizze, hogy a tanúsítvány még nem járt-e le.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Hogyan tudom ellenőrizni, hogy az ügyféltanúsítvány társítva van-e a bérlőhöz az Azure AD-ben?

Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsokat:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Ezek a parancsok kinyomtatják a bérlőhöz társító összes tanúsítványt a PowerShell-munkamenetben lévő NPS-bővítmény példányával. Keresse meg a tanúsítványt úgy, hogy a titkos kulcs nélküli *Base-64 kódolt X. 509 (. cer)* fájlként exportálja az ügyfél tanúsítványát, és összehasonlítja a listával a PowerShell-lel.

A következő parancs létrehoz egy *npscertificate* nevű fájlt a *C:* meghajtó gyökérkönyvtárában a Format *. cer* fájlban.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

A parancs futtatása után nyissa meg a *C:* meghajtó gyökerét, keresse meg a fájlt, és kattintson rá duplán. Lépjen a részletek elemre, és görgessen le az "ujjlenyomat" kifejezésre. Hasonlítsa össze a kiszolgálón telepített tanúsítvány ujjlenyomatát. A tanúsítvány ujjlenyomatai megfelelnek egyeznie kell.

Az *érvényes* – és az *érvényes – csak* az emberi olvashatóságot eredményező időbélyegek esetében használható a nyilvánvaló Misfits kiszűrésére, ha a parancs egynél több tanúsítványt ad vissza.

### <a name="why-cannot-i-sign-in"></a>Miért nem tudok bejelentkezni?

Győződjön meg arról, hogy a jelszó még nem járt le. A hálózati házirend-kiszolgáló bővítmény nem támogatja a jelszavak módosítását a bejelentkezési munkafolyamat részeként. További segítségért forduljon a szervezet informatikai részlegéhez.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Miért nem sikerül a kérések ADAL-jogkivonat hibája?

Ezt a hibát számos ok okozhatja. A következő lépésekkel végezheti el a hibaelhárítást:

1. Indítsa újra a hálózati házirend-kiszolgálót.
2. Ellenőrizze, hogy az ügyféltanúsítvány a várt módon van-e telepítve.
3. Ellenőrizze, hogy a tanúsítvány társítva van-e a bérlőhöz az Azure AD-ben.
4. Ellenőrizze, hogy a `https://login.microsoftonline.com/` elérhető-e a bővítményt futtató kiszolgálóról.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Miért sikertelen a hitelesítés a HTTP-naplókban, hogy a felhasználó nem található?

Ellenőrizze, hogy az AD-kapcsolat fut-e, és hogy a felhasználó megtalálható-e mind a helyszíni AD DS környezetben, mind az Azure AD-ben.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Miért látok HTTP-csatlakozási hibákat a naplókban az összes saját hitelesítéssel?

Ellenőrizze, hogy a https://adnotifications.windowsazure.com elérhető-e az NPS-bővítményt futtató kiszolgálóról.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Miért nem működik a hitelesítés, annak ellenére, hogy érvényes tanúsítvány van jelen?

Ha a számítógép korábbi tanúsítványa lejárt, és új tanúsítvány lett létrehozva, törölje a lejárt tanúsítványokat. A lejárt tanúsítványok a hálózati házirend-bővítmény indításával kapcsolatos problémákat okozhatnak.

Annak ellenőrzéséhez, hogy rendelkezik-e érvényes tanúsítvánnyal, ellenőrizze a helyi *számítógépfiók tanúsítvány-tárolóját* az MMC használatával, és győződjön meg arról, hogy a tanúsítvány nem felelt meg a lejárati dátumnak. Egy újonnan érvényes tanúsítvány létrehozásához futtassa újra a PowerShell- [telepítő parancsfájl futtatásának](#run-the-powershell-script)lépéseit.

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Miért látok elvetett kérelmeket a hálózati házirend-kiszolgáló naplófájljaiban?

Előfordulhat, hogy a VPN-kiszolgáló ismétlődő kéréseket küld az NPS-kiszolgálónak, ha az időtúllépés értéke túl alacsony. A hálózati házirend-kiszolgáló észleli ezeket az ismétlődő kérelmeket, és elveti őket. Ez a viselkedés a tervezés szerint történik, és nem jelez problémát az NPS-kiszolgálóval vagy az Azure AD Multi-Factor Authentication NPS-bővítménnyel.

A hálózati házirend-kiszolgáló naplófájljaiban megjelenő elvetett csomagok megtekintésével kapcsolatos további információkért lásd a [RADIUS-protokoll viselkedése és a hálózati házirend-kiszolgáló bővítménye](#radius-protocol-behavior-and-the-nps-extension) című részt a cikk elején.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>A TLS/SSL-protokollok és titkosítócsomagok kezelése

Javasoljuk, hogy a régebbi és gyengébb titkosítási csomagokat tiltsa le, vagy távolítsa el, kivéve, ha a szervezete megköveteli. A feladat végrehajtásával kapcsolatos információkért tekintse meg a következő cikket: [SSL/TLS protokollok és titkosítási csomagok kezelése AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>További hibaelhárítás

További hibaelhárítási útmutatást és lehetséges megoldásokat a cikkben talál, az [Azure AD multi-Factor Authentication hálózati házirend-kiszolgáló bővítményével kapcsolatos hibaüzenetek megoldásához](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Következő lépések

- [A hálózati házirend-kiszolgáló áttekintése és konfigurálása a Windows Server rendszerben](/windows-server/networking/technologies/nps/nps-top)

- Konfiguráljon alternatív azonosítókat a bejelentkezéshez, vagy állítson be olyan IP-címekre vonatkozó kivételeket, amelyek nem hajtják végre a kétlépéses ellenőrzést a [hálózati házirend-kiszolgáló bővítményének speciális konfigurációs beállításaiban multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Ismerje meg, hogyan integrálhatja a [Távoli asztali átjáró](howto-mfa-nps-extension-rdg.md) és a [VPN-kiszolgálókat](howto-mfa-nps-extension-vpn.md) a hálózati házirend-kiszolgáló bővítmény használatával

- [Hibák elhárítása az Azure AD-hez tartozó NPS-bővítményből Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
