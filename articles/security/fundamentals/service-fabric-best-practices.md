---
title: Ajánlott eljárások az Azure Service Fabric Security szolgáltatáshoz
description: Ez a cikk az Azure Service Fabric biztonsággal kapcsolatos ajánlott eljárásokat ismerteti.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: a7396c9a29c7d9f69dbe6a9cc5cd085c72ebafde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94700946"
---
# <a name="azure-service-fabric-security-best-practices"></a>Az Azure Service Fabric ajánlott biztonsági eljárásai
Az alkalmazások Azure-ban való üzembe helyezése gyors, egyszerű és költséghatékony. Mielőtt üzembe helyezné a Felhőbeli alkalmazást éles környezetben, tekintse át az alkalmazásban található biztonságos fürtök megvalósításához szükséges alapvető és ajánlott eljárások listáját.

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

Minden ajánlott eljáráshoz a következőket magyarázza el:

-   Az ajánlott eljárás.
-   Miért érdemes megvalósítani az ajánlott eljárásokat.
-   Mi történhet, ha nem alkalmazza az ajánlott eljárást.
-   Hogyan sajátíthatja el az ajánlott eljárások megvalósítását.

A következő Azure Service Fabric ajánlott biztonsági eljárásokat javasoljuk:

-   Biztonságos fürtök létrehozásához használja Azure Resource Manager sablonokat és a Service Fabric PowerShell-modult.
-   Használjon X. 509 tanúsítványokat.
-   Biztonsági házirendek konfigurálása.
-   A Reliable Actors biztonsági konfigurációjának implementálása.
-   Konfigurálja a TLS-t az Azure Service Fabrichoz.
-   Használja az Azure Service Fabric hálózati elkülönítést és biztonságot.
-   Azure Key Vault konfigurálása a biztonsághoz.
-   Felhasználók társítása szerepkörökhöz.


## <a name="best-practices-for-securing-your-clusters"></a>Ajánlott eljárások a fürtök biztonságossá tételéhez

Mindig használjon biztonságos fürtöt:
-   A fürt biztonságának megvalósítása tanúsítványok használatával.
-   Az ügyfél-hozzáférés (rendszergazda és csak olvasható) megadása Azure Active Directory (Azure AD) használatával.

Automatikus központi telepítések használata:
-   Szkripteket használhat a titkok létrehozásához, üzembe helyezéséhez és átadásához.
-   Tárolja a titkokat a Azure Key Vaultban, és használja az Azure AD-t az összes többi ügyfél-hozzáféréshez.
-   Hitelesítés megkövetelése a titkos kulcsokhoz való emberi hozzáféréshez.

Emellett vegye figyelembe a következő konfigurációs beállításokat:
-   Hozzon létre peremhálózati hálózatokat (más néven vagy demilitarizált zónákat, DMZ és szűrt alhálózatokat) az Azure hálózati biztonsági csoportok (NSG) használatával.
-   A fürt virtuális gépei (VM-EK) és a fürt kezelése a Távoli asztali kapcsolat-t tartalmazó Jump Servers használatával.

A fürtöket védeni kell, hogy megakadályozza a jogosulatlan felhasználók csatlakozását, különösen akkor, ha egy fürt éles üzemben fut. Bár lehetséges, hogy nem biztonságos fürtöt hoz létre, a névtelen felhasználók csatlakozhatnak a fürthöz, ha a fürt elérhetővé teszi a felügyeleti végpontokat a nyilvános internethez.

A fürt biztonságának különböző technológiákkal történő megvalósítására három [forgatókönyv](../../service-fabric/service-fabric-cluster-security.md) áll rendelkezésre:

-   Csomópontok közötti biztonság: Ez a forgatókönyv biztosítja a virtuális gépek és a fürtben lévő számítógépek közötti kommunikációt. Ez a biztonsági mód biztosítja, hogy csak azok a számítógépek tárolhatnak alkalmazásokat és szolgáltatásokat a fürtben, amelyek jogosultak a fürthöz való csatlakozásra.
Ebben az esetben az Azure-on futó fürtök vagy a Windows rendszeren futó önálló fürtök a Windows Server rendszerű számítógépek [tanúsítvány](../../service-fabric/service-fabric-windows-cluster-x509-security.md) -vagy [Windows-biztonsági](../../service-fabric/service-fabric-windows-cluster-windows-security.md) szolgáltatását használhatják.
-   Ügyfél és csomópont közötti biztonság: Ez a forgatókönyv a Service Fabric-ügyfél és a fürt egyes csomópontjai közötti kommunikációt biztosítja.
-   Service Fabric szerepköralapú hozzáférés-vezérlés (Service Fabric RBAC): Ez a forgatókönyv külön identitásokat (tanúsítványokat, Azure AD-t stb.) használ a fürthöz hozzáférő minden rendszergazda és felhasználói ügyfél szerepkörhöz. A szerepkör-identitásokat a fürt létrehozásakor kell megadnia.

>[!NOTE]
>**Biztonsági javaslat Azure-fürtökhöz:** Az Azure AD biztonsági szolgáltatásával hitelesítheti az ügyfeleket és a tanúsítványokat a csomópontok közötti biztonság érdekében.

Önálló Windows-fürt konfigurálásához tekintse meg az [önálló Windows-fürt beállításainak konfigurálása](../../service-fabric/service-fabric-cluster-manifest.md)című témakört.

Biztonságos fürt létrehozásához használja Azure Resource Manager sablonokat és a Service Fabric PowerShell-modult.
A biztonságos Service Fabric fürt Azure Resource Manager sablonok használatával történő létrehozásával kapcsolatos részletes útmutatásért lásd: [Service Fabric-fürt létrehozása](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Használja a Azure Resource Manager sablont:
-   A fürt testre szabásához használja a sablont a virtuális gép virtuális merevlemezek (VHD-k) felügyelt tárolójának konfigurálásához.
-   Az egyszerű konfiguráció felügyeletéhez és a naplózáshoz a sablon használatával hajtsa végre a módosításokat az erőforráscsoporthoz.

A fürt konfigurációjának kezelése kódként:
-   Legyen alapos az üzembe helyezési konfigurációk ellenőrzésekor.
-   Ne használjon implicit parancsokat az erőforrások közvetlen módosításához.

A [Service Fabric alkalmazás-életciklusának](../../service-fabric/service-fabric-application-lifecycle.md) számos aspektusa lehet automatizálható. A [Service Fabric PowerShell-modul](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatizálja az Azure Service Fabric-alkalmazások üzembe helyezésének, frissítésének, eltávolításának és tesztelésének gyakori feladatait. Az alkalmazások felügyeletéhez felügyelt API-k és HTTP API-k is elérhetők.

## <a name="use-x509-certificates"></a>X. 509 tanúsítványok használata
A fürtöket az X. 509 tanúsítványok vagy a Windows Biztonság használatával mindig biztonságossá teheti. A biztonság csak a fürt létrehozási idején van konfigurálva. A fürt létrehozása után nem lehet bekapcsolni a biztonságot.

A [fürt tanúsítványának](../../service-fabric/service-fabric-windows-cluster-x509-security.md)megadásához állítsa a **ClusterCredentialType** tulajdonság értékét X509 értékre. A külső kapcsolatokhoz tartozó kiszolgálói tanúsítvány megadásához állítsa a **ServerCredentialType** tulajdonságot X509 értékre.

Továbbá kövesse az alábbi eljárásokat:
-   Hozza létre a tanúsítványokat a termelési fürtökhöz a megfelelően konfigurált Windows Server Certificate Service használatával. A tanúsítványokat egy jóváhagyott hitelesítésszolgáltatótól (CA) is beszerezheti.
-   Soha ne használjon ideiglenes vagy tesztelési tanúsítványt az üzemi fürtökhöz, ha a tanúsítvány MakeCert.exe vagy hasonló eszköz használatával lett létrehozva.
-   Önaláírt tanúsítványt használhat a tesztelési fürtökhöz, de az éles fürtök esetében nem.

Ha a fürt nem biztonságos, bárki csatlakozhat a fürthöz névtelenül, és felügyeleti műveleteket hajthat végre. Emiatt az X. 509 tanúsítványok vagy a Windows Biztonság használatával mindig biztonságos üzemi fürtöket használjon.

Ha többet szeretne megtudni az X. 509 tanúsítványok használatáról, tekintse meg [a Service Fabric-fürt tanúsítványainak hozzáadása vagy eltávolítása](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)című témakört.

## <a name="configure-security-policies"></a>Biztonsági házirendek konfigurálása
A Service Fabric az alkalmazások által használt erőforrásokat is biztonságossá teszi. Az erőforrásokat, például a fájlokat, a címtárakat és a tanúsítványokat a felhasználói fiókok alatt tárolja a rendszer az alkalmazás telepítésekor. Ez a funkció lehetővé teszi, hogy az alkalmazások biztonságosabbak legyenek egymástól, még egy megosztott tárolt környezetben is.

-   Active Directory tartományi csoport vagy felhasználó használata: futtassa a szolgáltatást egy Active Directory felhasználói vagy csoportfiók hitelesítő adatai alatt. Ügyeljen arra, hogy a tartományon belül Active Directoryt használjon, és ne Azure Active Directory. A tartományhoz tartozó más erőforrásokhoz való hozzáférés, amelyek tartományi felhasználó vagy csoport használatával kaptak engedélyeket. Például olyan erőforrásokat, mint a fájlmegosztás.

-   Biztonsági hozzáférési házirend kiosztása HTTP-és HTTPS-végpontokhoz: adja meg azt a **SecurityAccessPolicy** -tulajdonságot, **amely egy futtató házirendet** alkalmaz egy szolgáltatásra, ha a szolgáltatás jegyzékfájlja http-vel deklarálja a végponti erőforrásokat. A HTTP-végpontok számára lefoglalt portok megfelelő hozzáférés-vezérlésű listája a szolgáltatás által futtatott futtató felhasználói fiókhoz. Ha a házirend nincs beállítva, http.sys nem fér hozzá a szolgáltatáshoz, és az ügyféltől érkező hívásokkal kapcsolatos hibák jelentkezhetnek.

Ha meg szeretné tudni, hogyan használhatja a biztonsági házirendeket egy Service Fabric fürtben, tekintse meg az [alkalmazás biztonsági házirendjeinek konfigurálása](../../service-fabric/service-fabric-application-runas-security.md)című témakört.

## <a name="implement-the-reliable-actors-security-configuration"></a>A Reliable Actors biztonsági konfigurációjának implementálása
A Service Fabric Reliable Actors a színész kialakítási mintájának implementációja. A szoftveres tervezési mintákhoz hasonlóan az adott minta használatára vonatkozó döntés attól függ, hogy a szoftverrel kapcsolatos probléma megfelel-e a mintának.

Általánosságban elmondható, hogy a Actor kialakítási mintája segítséget nyújt a következő szoftverekkel vagy biztonsági forgatókönyvekkel kapcsolatos megoldások modellezésében:
-   A problémás terület nagy számú (több ezer vagy több) kis, független és elszigetelt állapotú és logikai egységből áll.
-   Olyan egyszálas objektumokat használ, amelyek nem igényelnek jelentős interakciót a külső összetevőktől, többek között a lekérdezési állapotot a szereplők egy csoportján belül.
-   A színész példányai nem tudják előre nem látható késéssel letiltani a hívókat az I/O-műveletek kiadásával.

Service Fabric a szereplők a Reliable Actors alkalmazás-keretrendszerben valósulnak meg. Ez a keretrendszer a színész mintáján alapul, és [Service Fabric Reliable Servicesra](../../service-fabric/service-fabric-reliable-services-introduction.md)épül. Minden olyan megbízható Actors szolgáltatás, amelyet írsz, egy particionált, állapot-nyilvántartó megbízható szolgáltatás.

Minden szereplő egy Actor típusú példányként van definiálva, amely megegyezik azzal, ahogyan a .NET-objektum egy .NET-típusú példány. Például egy számológép funkcióit megvalósító **színészi típusnak** számos résztvevője lehet az adott típusból, amely a fürt különböző csomópontjain van elosztva. Az elosztott szereplők mindegyikét egyedileg, egy színész azonosítója jellemzi.

A [replikátor biztonsági beállításai](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) a replikálás során használt kommunikációs csatorna biztonságossá tételére szolgálnak. Ez a konfiguráció megakadályozza, hogy a szolgáltatások meglássák egymás replikációs forgalmát, és biztosítja, hogy a nagy rendelkezésre állású adatok biztonságosak legyenek. Alapértelmezés szerint a biztonsági konfiguráció üres szakasza megakadályozza a replikálás biztonságát.
A replikátor-konfigurációk olyan replikátort állítanak be, amely felelős a szereplők állami szolgáltatói állapotának nagyfokú megbízhatóságának kialakításához.

## <a name="configure-tls-for-azure-service-fabric"></a>A TLS konfigurálása az Azure Service Fabric
A kiszolgálói hitelesítési folyamat [hitelesíti](../../service-fabric/service-fabric-cluster-creation-via-arm.md) a fürt felügyeleti végpontját egy felügyeleti ügyfélen. Ezt követően a felügyeleti ügyfél felismeri, hogy az a valódi fürtre mutat. Ez a tanúsítvány egy [TLS](../../service-fabric/service-fabric-cluster-creation-via-arm.md) -t is biztosít a https-kezelési API-hoz és a HTTPS-en keresztüli Service Fabric Explorerhoz.
Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt kér a hitelesítésszolgáltatótól, a tanúsítvány tulajdonosának nevének meg kell egyeznie a fürthöz használt egyéni tartománynévvel.

A TLS alkalmazáshoz való konfigurálásához először be kell szereznie egy, a CA által aláírt SSL/TLS-tanúsítványt. A HITELESÍTÉSSZOLGÁLTATÓ egy olyan megbízható harmadik fél, amely TLS biztonsági célú tanúsítványokat bocsát ki. Ha még nem rendelkezik SSL/TLS-tanúsítvánnyal, be kell szereznie egy olyan vállalattól, amely SSL/TLS-tanúsítványokat árul.

A tanúsítványnak meg kell felelnie az alábbi követelményeknek az SSL/TLS-tanúsítványokhoz az Azure-ban:
-   A tanúsítványnak tartalmaznia kell egy titkos kulcsot.

-   A tanúsítványt létre kell hozni a Key Exchange számára, és exportálni kell egy személyes információcsere (. pfx) fájlba.

-   A tanúsítvány tulajdonosának nevének meg kell egyeznie a felhőalapú szolgáltatás eléréséhez használt tartománynévvel.

    - Szerezze be a felhőalapú szolgáltatás eléréséhez használandó egyéni tartománynevet.
    - Tanúsítvány kérése egy HITELESÍTÉSSZOLGÁLTATÓTÓL a szolgáltatás egyéni tartománynevének megfelelő tulajdonos nevével. Ha például az Egyéni tartománynév a __contoso__**. com**, a hitelesítésszolgáltatótól származó tanúsítványnak a tulajdonos neve **. contoso.com** vagy a __www__**. contoso.com** nevet kell tartalmaznia.

    >[!NOTE]
    >Nem szerezhet be SSL/TLS-tanúsítványt a HITELESÍTÉSSZOLGÁLTATÓTÓL a __cloudapp__**.net** -tartományhoz.

-   A tanúsítványnak legalább 2 048 bites titkosítást kell használnia.

A HTTP protokoll nem biztonságos, és a lehallgatási támadásokra is vonatkozik. A HTTP protokollon keresztül továbbított adatok egyszerű szövegként lesznek elküldve a webböngészőből a webkiszolgálóba vagy más végpontok között. A támadók elérhetik és megtekinthetik a HTTP-n keresztül elküldhető bizalmas adatokat, például a bankkártya adatait és a fiók bejelentkezéseit. Ha az adatokat HTTPS protokollon keresztül küldik vagy közzétesszük, az SSL gondoskodik arról, hogy a bizalmas adatok titkosítva legyenek és biztonságosak legyenek az elfogástól.

További információ az SSL/TLS-tanúsítványok használatáról: [TLS konfigurálása alkalmazáshoz az Azure-ban](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Hálózati elkülönítés és biztonság használata az Azure Service Fabric
Hozzon létre egy 3 NodeType biztonságos fürtöt a [Azure Resource Manager sablon](../../azure-resource-manager/templates/template-syntax.md) használatával mintaként. A bejövő és a kimenő hálózati forgalom vezérlése a sablon és a hálózati biztonsági csoportok használatával.

A sablon mindegyik virtuálisgép-méretezési csoporthoz tartozik egy NSG, amely a készleten belüli és kívüli forgalom vezérlésére szolgál. A szabályok alapértelmezés szerint úgy vannak konfigurálva, hogy engedélyezzék a rendszerszolgáltatásokhoz és a sablonban megadott alkalmazás-portokhoz szükséges összes forgalmat. Tekintse át ezeket a szabályokat, és végezze el az igényeinek megfelelő módosításokat, beleértve az alkalmazások új szabályainak hozzáadását.

További információ: [általános hálózati forgatókönyvek az Azure Service Fabrichoz](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Biztonsági Azure Key Vault beállítása
A Service Fabric tanúsítványokat használ a fürtök és alkalmazásai biztonságossá tételéhez a hitelesítés és a titkosítás biztosításához.

A Service Fabric X. 509 tanúsítványokat használ a fürt biztonságossá tételéhez és az alkalmazás biztonsági funkcióinak biztosításához. A Azure Key Vault segítségével kezelheti az Azure-beli Service Fabric-fürtök [tanúsítványait](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) . A fürtöket létrehozó Azure-erőforrás-szolgáltató lekéri a tanúsítványokat egy kulcstartóból. A szolgáltató ezután telepíti a tanúsítványokat a virtuális gépeken, amikor a fürt üzembe helyezése az Azure-ban történik.

[Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), a Service Fabric fürt és a tanúsítványokat használó erőforrás-szolgáltató között van egy tanúsítvány kapcsolata. A fürt létrehozásakor a rendszer egy kulcstartóban tárolja a tanúsítvány kapcsolatával kapcsolatos információkat.

A Key Vault beállításának két alapvető lépése van:
1. Hozzon létre egy erőforráscsoportot kifejezetten a kulcstartóhoz.

    Javasoljuk, hogy a kulcstartót a saját erőforráscsoporthoz helyezze. Ez a művelet segít megelőzni a kulcsok és a titkos kódok elvesztését, ha más erőforráscsoportokat is eltávolít, például a tárterületet, a számítást vagy a fürtöt tartalmazó csoportot. A kulcstárolót tartalmazó erőforráscsoportot az azt használó fürttel azonos régióban kell lennie.

2. Hozzon létre egy Key vaultot az új erőforráscsoporthoz.

    A Key vaultot engedélyezni kell a központi telepítéshez. A számítási erőforrás-szolgáltató ezután lekérheti a tanúsítványokat a tárolóból, és telepítheti őket a virtuálisgép-példányokon.

A Key Vault beállításával kapcsolatos további tudnivalókért tekintse meg a [Mi az Azure Key Vault?](../../key-vault/general/overview.md)című témakört.

## <a name="assign-users-to-roles"></a>Felhasználók társítása szerepkörökhöz
Miután létrehozta az alkalmazásokat a fürt képviseletére, rendelje hozzá a felhasználókat a Service Fabric által támogatott szerepkörökhöz: csak olvasható és rendszergazda. Ezeket a szerepköröket a Azure Portal használatával rendelheti hozzá.

>[!NOTE]
> További információ a Service Fabric szerepköreinek használatáról: [Service Fabric ügyfelek Service Fabric szerepköralapú hozzáférés-vezérlése](../../service-fabric/service-fabric-cluster-security-roles.md).

Az Azure Service Fabric két hozzáférés-vezérlési típust támogat a Service Fabric- [fürthöz](../../service-fabric/service-fabric-cluster-creation-via-arm.md)csatlakozó ügyfelekhez: rendszergazda és felhasználó. A fürt rendszergazdája a hozzáférés-vezérlés használatával korlátozhatja a hozzáférést bizonyos fürtműveleteket a felhasználók különböző csoportjaihoz. A hozzáférés-vezérlés biztonságosabbá teszi a fürtöt.

## <a name="next-steps"></a>Következő lépések

- [Service Fabric biztonsági ellenőrzőlista](../../service-fabric/service-fabric-best-practices-security.md)
- Állítsa be Service Fabric [fejlesztési környezetét](../../service-fabric/service-fabric-get-started.md).
- További információ a [Service Fabric támogatási lehetőségeiről](../../service-fabric/service-fabric-support.md).