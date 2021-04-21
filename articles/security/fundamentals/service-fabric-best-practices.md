---
title: Ajánlott eljárások az Azure Service Fabric biztonsághoz
description: Ez a cikk ajánlott eljárásokat tartalmaz az Azure Service Fabric biztonságával kapcsolatban.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7d87e2496158fec8ff33ab8586c845a6207f810
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816061"
---
# <a name="azure-service-fabric-security-best-practices"></a>Az Azure Service Fabric ajánlott biztonsági eljárásai
Az alkalmazások gyors, egyszerű és költséghatékony üzembe helyezése az Azure-ban. Mielőtt üzembe helyezi a felhőalkalmazást éles környezetben, tekintse át a biztonságos fürtök alkalmazáson belül való megvalósításának alapvető és ajánlott eljárásait.

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

Minden ajánlott eljáráshoz a következőt ismertetjük:

-   Mi az ajánlott eljárás?
-   Miért érdemes az ajánlott eljárásokat megvalósítani.
-   Mi történhet, ha nem implementálja az ajánlott eljárásokat?
-   Hogyan valósítható meg az ajánlott eljárás?

A következő Azure-beli Service Fabric ajánlott biztonsági eljárásokat javasoljuk:

-   Használjon Azure Resource Manager sablonokat és a Service Fabric PowerShell-modult biztonságos fürtök létrehozásához.
-   X.509-tanúsítványokat használjon.
-   Biztonsági szabályzatok konfigurálása.
-   Implementálja a Reliable Actors biztonsági konfigurációt.
-   Konfigurálja a TLS-t az Azure Service Fabric.
-   Hálózatelszigetelés és -biztonság használata az Azure Service Fabric.
-   Konfigurálja Azure Key Vault biztonsági beállításokat.
-   Felhasználók hozzárendelése szerepkörökhöz.


## <a name="best-practices-for-securing-your-clusters"></a>Ajánlott eljárások a fürtök biztonságossá tétele érdekében

Mindig használjon biztonságos fürtöt:
-   Fürtbiztonság megvalósítása tanúsítványokkal.
-   Ügyfél-hozzáférés (rendszergazdai és csak olvasható) Azure Active Directory (Azure AD) használatával.

Automatizált üzembe helyezés használata:
-   Szkriptek használata a titkos kulcsok létrehozásához, üzembe helyezéséhez és átváltáshoz.
-   Tárolja a titkos Azure Key Vault, és használja az Azure AD-t minden más ügyfél-hozzáféréshez.
-   Hitelesítés szükséges a titkos kulcsokhoz való emberi hozzáféréshez.

Emellett vegye figyelembe a következő konfigurációs beállításokat:
-   Szegélyhálózatok (más néven demilitarizált zónák, DMZ-k és képernyős alhálózatok) létrehozása Azure hálózati biztonsági csoportok (NSG-k) használatával.
-   Fürtözött virtuális gépek elérése vagy a fürt kezelése jump servers with Távoli asztali kapcsolat.

A fürtök biztonságának meg kell akadályozni a jogosulatlan felhasználók csatlakozását, különösen akkor, ha egy fürt éles környezetben fut. Bár létre lehet hozni egy nem biztonságos fürtöt, a névtelen felhasználók akkor is csatlakozhatnak a fürthöz, ha a fürt felügyeleti végpontokat fed le a nyilvános interneten.

A [fürtbiztonság különböző](../../service-fabric/service-fabric-cluster-security.md) technológiákkal való megvalósításának három forgatókönyve van:

-   Csomópontok közötti biztonság: Ez a forgatókönyv biztosítja a virtuális gépek és a fürtben lévő számítógépek közötti kommunikációt. Ez a biztonsági forma biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek tudjanak alkalmazásokat és szolgáltatásokat a fürtön belül biztosítani.
Ebben a forgatókönyvben az Azure-ban futó fürtök vagy a Windowson [](../../service-fabric/service-fabric-windows-cluster-x509-security.md) futó önálló fürtök tanúsítványbiztonságot vagy [Windows-biztonságot](../../service-fabric/service-fabric-windows-cluster-windows-security.md) használhatnak Windows Server rendszerű gépeken.
-   Ügyfél és csomópont közötti biztonság: Ez a forgatókönyv biztonságossá Service Fabric ügyfél és a fürt egyes csomópontjai között.
-   Service Fabric hozzáférés-vezérlés (Service Fabric RBAC): Ez a forgatókönyv külön identitásokat (tanúsítványokat, Azure AD-t stb.) használ a fürthöz hozzáférő összes rendszergazdai és felhasználói ügyfélszerepkörhöz. A szerepkör-identitásokat a fürt létrehozásakor adhatja meg.

>[!NOTE]
>**Biztonsági javaslatok Azure-fürtökhöz:** Az Azure AD biztonságával hitelesítheti az ügyfeleket és tanúsítványokat a csomópontok és csomópontok között.

Önálló Windows-fürt konfigurálásról lásd: Configure settings for a standalone Windows cluster (Önálló [Windows-fürt beállításainak konfigurálása).](../../service-fabric/service-fabric-cluster-manifest.md)

Használjon Azure Resource Manager sablonokat és Service Fabric PowerShell-modult egy biztonságos fürt létrehozásához.
A biztonságos fürt sablonok használatával való Service Fabric lépésenként Azure Resource Manager lásd: Create a Service Fabric cluster (Új fürt [létrehozása).](../../service-fabric/service-fabric-cluster-creation-via-arm.md)

Használja a Azure Resource Manager sablont:
-   A fürt testreszabása a sablon használatával a virtuális gépek virtuális merevlemezei (VHD-k) felügyelt tárolójának konfigurálásához.
-   A konfiguráció egyszerű kezeléséhez és naplózáshoz használja a sablont az erőforráscsoport módosításainak irányításához.

Kezelje a fürtkonfigurációt kódként:
-   Alaposan ellenőrizze az üzembe helyezési konfigurációkat.
-   Ne használja az implicit parancsokat az erőforrások közvetlen módosításához.

Az alkalmazások [életciklusának Service Fabric aspektusa](../../service-fabric/service-fabric-application-lifecycle.md) automatizálható. Az [Service Fabric PowerShell-modul](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatizálja az Azure-beli alkalmazások üzembe helyezésének, frissítésének, eltávolításának és Service Fabric általános feladatait. Az alkalmazáskezeléshez felügyelt API-k és HTTP API-k is elérhetők.

## <a name="use-x509-certificates"></a>X.509-tanúsítványok használata
A fürtök biztonságának biztosítása mindig X.509-tanúsítványokkal vagy Windows-biztonsággal. A biztonság csak a fürt létrehozásakor van konfigurálva. A fürt létrehozása után nem lehet bekapcsolni a biztonságot.

Fürt [tanúsítványának megadásához](../../service-fabric/service-fabric-windows-cluster-x509-security.md)állítsa a **ClusterCredentialType** tulajdonság értékét X509 értékre. Külső kapcsolatok kiszolgálói tanúsítványának megadásához állítsa a **ServerCredentialType** tulajdonságot X509-re.

Emellett kövesse az alábbi eljárásokat:
-   Hozza létre az éles fürtök tanúsítványait egy megfelelően konfigurált Windows Server-tanúsítványszolgáltatással. A tanúsítványokat egy jóváhagyott hitelesítésszolgáltatótól (CA) is beszerezheti.
-   Soha ne használjon ideiglenes vagy teszt tanúsítványt éles fürtökhöz, ha a tanúsítványt MakeCert.exe vagy hasonló eszközzel hozták létre.
-   Használjon önaírt tanúsítványt a tesztfürtökhöz, éles fürtökhöz azonban ne.

Ha a fürt nem biztonságos, bárki névtelenül csatlakozhat a fürthöz, és felügyeleti műveleteket hajthat végre. Ezért mindig X.509-tanúsítványokkal vagy Windows-biztonsággal biztosítsa az éles fürtök biztonságát.

Az X.509-tanúsítványok használatával kapcsolatos további információkért lásd: Tanúsítványok hozzáadása vagy eltávolítása egy Service Fabric [fürthöz.](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)

## <a name="configure-security-policies"></a>Biztonsági szabályzatok konfigurálása
Service Fabric biztosítja az alkalmazások által használt erőforrásokat is. Az olyan erőforrások, mint a fájlok, könyvtárak és tanúsítványok a felhasználói fiókokban vannak tárolva az alkalmazás telepítésekor. Ez a funkció még megosztott környezetben is biztonságossá teszi az alkalmazások futtatását egymástól.

-   Tartományi Active Directory vagy felhasználó használata: Futtassa a szolgáltatást egy Active Directory vagy csoportfiók hitelesítő adataival. Győződjön meg arról, Active Directory a tartományon belül használja a helyszíni Azure Active Directory. Hozzáférés a tartomány más olyan erőforrásaihoz, amelyek tartományi felhasználó vagy csoport használatával kaptak engedélyeket. Ilyen például az erőforrások, például a fájlmegosztások.

-   Biztonsági hozzáférési házirend hozzárendelése HTTP- és HTTPS-végpontok számára: Adja meg a **SecurityAccessPolicy** tulajdonságot egy **runas** házirend szolgáltatásra való alkalmazáshoz, amikor a szolgáltatásjegyzék VÉGPONT-erőforrásokat deklarál HTTP-val. A HTTP-végpontokhoz lefoglalt portok a szolgáltatás által futtatott runas felhasználói fiók megfelelő hozzáférés-vezérlésű listái. Ha a szabályzat nincs beállítva, http.sys nem fér hozzá a szolgáltatáshoz, és hibákat kaphat az ügyfél hívásaiból.

A biztonsági szabályzatok fürtökben való használatával kapcsolatos Service Fabric lásd: Configure security policies for your application (Biztonsági szabályzatok konfigurálása [az alkalmazáshoz).](../../service-fabric/service-fabric-application-runas-security.md)

## <a name="implement-the-reliable-actors-security-configuration"></a>A biztonsági Reliable Actors megvalósítása
Service Fabric Reliable Actors az aktortervezési minta implementációja. Mint minden szoftvertervezési minta, az adott minta használata azon alapul, hogy egy szoftveres probléma megfelel-e a mintának.

Az aktor tervezési mintája általában a következő szoftverproblémák vagy biztonsági forgatókönyvek megoldásának modellezéséhez nyújt segítséget:
-   A problématér nagy számú (ezres vagy több) kis, független és elszigetelt állapot- és logikai egységet foglal magában.
-   Olyan egyszálas objektumokkal dolgozik, amelyekhez nincs szükség jelentős külső összetevőkkel való interakcióra, beleértve az állapot lekérdezését a színészek egy halmazán keresztül.
-   Az aktorpéldányok nem blokkolják a kiszámíthatatlan késéssel hívókat I/O-műveletek kiadásával.

A Service Fabric az akák az alkalmazás-keretrendszer Reliable Actors vannak megvalósítva. Ez a keretrendszer az aktor mintán alapul, és a következőre [Service Fabric Reliable Services.](../../service-fabric/service-fabric-reliable-services-introduction.md) Minden megírható megbízható aktorszolgáltatás particionált, állapot- és megbízható szolgáltatás.

Minden aktor egy aktortípus példányaként van definiálva, ugyanúgy, ahogyan a .NET-objektum egy .NET-típus példánya. Egy számológép funkcióit megvalósító aktortípus például számos ilyen típusú aktort tartalmazhat, amelyek a fürt különböző csomópontjai között vannak elosztva.  Minden elosztott aktor egyedileg jellemezhető egy aktorazonosítóval.

[A replikálás során használt](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) kommunikációs csatorna a replika számára biztonsági konfigurációk segítségével van biztosítva. Ez a konfiguráció megakadályozza, hogy a szolgáltatások látják egymás replikációs forgalmát, és biztosítják a magas rendelkezésre állású adatok biztonságát. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza a replikáció biztonságát.
A replikatorkonfigurációk konfigurálják az aktorállapot-szolgáltató magas megbízhatóságú állapotba hozásáért felelős replikatort.

## <a name="configure-tls-for-azure-service-fabric"></a>A TLS konfigurálása az Azure Service Fabric
A kiszolgálóhitelesítési folyamat [hitelesíti](../../service-fabric/service-fabric-cluster-creation-via-arm.md) a fürt felügyeleti végpontját egy felügyeleti ügyfél számára. A felügyeleti ügyfél ezután felismeri, hogy a valódi fürthöz beszél. Ez a tanúsítvány egy [TLS-t](../../service-fabric/service-fabric-cluster-creation-via-arm.md) is biztosít a HTTPS felügyeleti API-hoz és a HTTPS Service Fabric Explorer keresztüli hitelesítéshez.
Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt kér egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell egyeznie a fürthöz használt egyéni tartománynévvel.

A TLS alkalmazáshoz való konfigurálásához először be kell szereznie egy SSL-/TLS-tanúsítványt, amelyet egy hitelesítésszolgáltató írt alá. A hitelesítésszolgáltató egy megbízható harmadik fél, amely tanúsítványokat ad ki a TLS biztonsági céljaira. Ha még nem rendelkezik SSL-/TLS-tanúsítvánnyal, be kell szereznie egyet az SSL/TLS-tanúsítványokat értékesítő vállalattól.

A tanúsítványnak meg kell felelnie az Azure-beli SSL-/TLS-tanúsítványokra vonatkozó alábbi követelményeknek:
-   A tanúsítványnak tartalmaznia kell egy titkos kulcsot.

-   A tanúsítványt kulcscseréhez kell létrehozni, és exportálhatónak kell lennie egy személyes információcsere (.pfx) fájlba.

-   A tanúsítvány tulajdonosnevének meg kell egyeznie a felhőszolgáltatás eléréséhez használt tartománynévvel.

    - Szerezzen be egy egyéni tartománynevet a felhőszolgáltatás eléréséhez.
    - Kérjen tanúsítványt egy hitelesítésszolgáltatótól a szolgáltatás egyéni tartománynevének megfelelő tulajdonosnévvel. Ha például az egyéni tartománynév __contoso__**.com,** a hitelesítésszolgáltató tanúsítványának **tulajdonosnevének .contoso.com** vagy __www__**.contoso.com.**

    >[!NOTE]
    >Nem szerezhet be SSL-/TLS-tanúsítványt hitelesítésszolgáltatótól a __cloudapp__**.net tartományhoz.**

-   A tanúsítványnak legalább 2048 bites titkosítást kell használnia.

A HTTP protokoll nem biztonságos, és lehallgatásos támadásoknak vannak kitéve. A HTTP-kapcsolaton keresztül továbbított adatok egyszerű szövegként vannak elküldve a webböngészőből a webkiszolgálóra vagy más végpontok között. A támadók elfoghatják és megtekinthetik a HTTP-n keresztül küldött bizalmas adatokat, például a hitelkártya adatait és a fiókba való bejelentkezéseket. Amikor egy böngészőn keresztül küld vagy küld el adatokat HTTPS-kapcsolaton keresztül, az SSL gondoskodik arról, hogy a bizalmas adatok titkosítva és biztonságosan elfogják őket.

Az SSL-/TLS-tanúsítványok használatával kapcsolatos további információkért lásd: [Configuring TLS for an application in Azure (TLS konfigurálása alkalmazásokhoz az Azure-ban).](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Hálózatelszigetelés és -biztonság használata az Azure Service Fabric
Állítson be egy 3 csomóponttípusú biztonságos fürtöt a mintasablon [Azure Resource Manager használatával.](../../azure-resource-manager/templates/template-syntax.md) A sablon és a hálózati biztonsági csoportok használatával szabályozhatja a bejövő és kimenő hálózati forgalmat.

A sablon minden virtuálisgép-méretezési készlethez rendelkezik NSG-vel, és a készlet be- és kifelé forgalmának vezérléséhez használható. A szabályok alapértelmezés szerint úgy vannak konfigurálva, hogy a rendszerszolgáltatásokhoz és a sablonban megadott alkalmazásportokhoz szükséges összes forgalmat engedélyezték. Tekintse át ezeket a szabályokat, és az igényeinek megfelelően módosítsa azokat, beleértve az új szabályok hozzáadását az alkalmazásokhoz.

További információkért lásd az [Azure-beli](../../service-fabric/service-fabric-patterns-networking.md)virtuális hálózatok gyakori hálózat Service Fabric.

## <a name="set-up-azure-key-vault-for-security"></a>Biztonsági Azure Key Vault beállítása
Service Fabric tanúsítványokat használ a hitelesítés és a titkosítás biztosításához a fürt és az alkalmazások biztonságossá tétele érdekében.

Service Fabric X.509-tanúsítványokat használ a fürtök biztonságának és az alkalmazásbiztonsági funkcióknak a biztosítása érdekében. A Azure Key Vault [azure-beli](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) fürtök tanúsítványait Service Fabric kezelheti. A fürtöt létrehozó Azure-erőforrás-szolgáltató lekérte a tanúsítványokat egy kulcstartóból. A szolgáltató ezután telepíti a tanúsítványokat a virtuális gépekre, amikor a fürt üzembe helyezése az Azure-ban történik.

Tanúsítványkapcsolat áll fenn [a](../../key-vault/general/security-features.md)Azure Key Vault, a Service Fabric fürt és a tanúsítványokat használó erőforrás-szolgáltató között. A fürt létrehozásakor a tanúsítványkapcsolatra vonatkozó információk egy kulcstartóban tárolódnak.

A kulcstartó beállításának két alapvető lépése van:
1. Hozzon létre egy erőforráscsoportot kifejezetten a kulcstartóhoz.

    Javasoljuk, hogy a kulcstartót a saját erőforráscsoportban helyezze el. Ez a művelet segít megelőzni a kulcsok és titkos kulcsok elvesztését, ha más erőforráscsoportokat távolít el, például a tárolást, a számítást vagy a fürtöt tartalmazó csoportot. A kulcstartót tartalmazó erőforráscsoportnak és az azt használó fürtnek ugyanabban a régióban kell lennie.

2. Hozzon létre egy kulcstartót az új erőforráscsoportban.

    Az üzembe helyezéshez engedélyezni kell a kulcstartót. A számítási erőforrás-szolgáltató ezután le tudja szerezni a tanúsítványokat a tárolóból, és telepítheti őket a virtuálisgép-példányokra.

A kulcstartó beállítását a Mi az a [kulcstartó Azure Key Vault? Azure Key Vault.](../../key-vault/general/overview.md)

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz
Miután létrehozta a fürtöt képviselő alkalmazásokat, rendelje hozzá a felhasználókat a Service Fabric által támogatott szerepkörökhöz: csak olvasható és rendszergazdai. Ezeket a szerepköröket a következővel rendelheti Azure Portal.

>[!NOTE]
> További információ a szerepköröknek a Service Fabric való használatával kapcsolatban: Service Fabric szerepköralapú [hozzáférés-vezérlés használata Service Fabric ügyfelekhez.](../../service-fabric/service-fabric-cluster-security-roles.md)

Az Azure Service Fabric két hozzáférés-vezérlési típust támogat [az](../../service-fabric/service-fabric-cluster-creation-via-arm.md)olyan ügyfelek számára, amelyek egy Service Fabric fürthöz csatlakoznak: a rendszergazdát és a felhasználót. A fürt rendszergazdája hozzáférés-vezérléssel korlátozhatja a hozzáférést bizonyos fürtműveletekkel a különböző felhasználói csoportok számára. A hozzáférés-vezérlés biztonságossá teszi a fürtöt.

## <a name="next-steps"></a>Következő lépések

- [Service Fabric ellenőrzőlista](../../service-fabric/service-fabric-best-practices-security.md)
- Állítsa be a [Service Fabric-fejlesztőkörnyezetet.](../../service-fabric/service-fabric-get-started.md)
- További információ [a Service Fabric lehetőségekről.](../../service-fabric/service-fabric-support.md)