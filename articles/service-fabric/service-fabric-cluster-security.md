---
title: Azure Service Fabric-fürt biztonságossá tétele
description: Ismerje meg az Azure Service Fabric-fürtök biztonsági forgatókönyveit, valamint a megvalósításához használható különböző technológiákat.
ms.topic: conceptual
ms.date: 08/14/2018
ms.openlocfilehash: 6f7bb785184938fe5c1e20e3c915b0112c7723ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573068"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric fürt biztonsági forgatókönyvei

Az Azure Service Fabric-fürt egy saját erőforrás. Az Ön feladata, hogy biztosítsa a fürtök védelmét, hogy megakadályozza a jogosulatlan felhasználók csatlakozását. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön. Nem biztonságos fürtöt hozhat létre, azonban ha a fürt felügyeleti végpontokat tesz elérhetővé a nyilvános internethez, a névtelen felhasználók csatlakozhatnak hozzá. A nem biztonságos fürtök nem támogatottak az éles számítási feladatokhoz. 

Ez a cikk áttekintést nyújt az Azure-fürtök és önálló fürtök biztonsági forgatókönyvei, valamint a megvalósításához használható különféle technológiákról:

* Csomópontok közötti biztonság
* Az ügyfél és a csomópont közötti biztonság
* Szerepköralapú hozzáférés-vezérlés Service Fabric

## <a name="node-to-node-security"></a>Csomópontok közötti biztonság

A csomópontok közötti biztonság a fürtben lévő virtuális gépek vagy számítógépek közötti kommunikáció biztonságossá tételéhez nyújt segítséget. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében.

![Csomópontok közötti kommunikáció diagramja][Node-to-Node]

Az Azure-on és a Windows rendszeren futó önálló fürtökön futó fürtök egyaránt használhatják a Windows Server rendszerű számítógépek [tanúsítvány](/previous-versions/msp-n-p/ff649801(v=pandp.10)) -vagy [Windows-biztonságát](/previous-versions/msp-n-p/ff649396(v=pandp.10)) .

### <a name="node-to-node-certificate-security"></a>Csomópontok közötti tanúsítvány biztonsága

A Service Fabric a fürt létrehozásakor a csomópont típusú konfiguráció részeként megadott X. 509 kiszolgálói tanúsítványokat használja. A cikk végén egy rövid áttekintést talál a tanúsítványok közül, valamint arról, hogyan lehet beszerezni vagy létrehozni őket.

A tanúsítvány biztonsági beállítása a fürt létrehozásakor Azure Resource Manager sablon vagy önálló JSON-sablon használatával, vagy a Azure Portal. Service Fabric SDK alapértelmezett viselkedése az, hogy a legtávolabbi időpontban helyezi üzembe és telepítse a tanúsítványt a közeljövőben; a klasszikus viselkedés lehetővé tette az elsődleges és a másodlagos tanúsítványok definiálását, így lehetővé teszi a manuálisan kezdeményezett átváltást, és az új funkciók használata esetén nem ajánlott. A használni kívánt elsődleges tanúsítványok a legtávolabbi a jövőbeli lejárati dátummal, és nem lehetnek azonosak az ügyfél és a [csomópont közötti biztonsághoz](#client-to-node-security)beállított rendszergazdai ügyféltől és csak olvasható ügyféltanúsítványoktől.

A következő témakörből megtudhatja, hogyan állíthatja be a tanúsítvány-biztonságot az Azure-fürtben: [fürt beállítása Azure Resource Manager sablon használatával](service-fabric-cluster-creation-via-arm.md).

Ha szeretné megtudni, hogyan állíthatja be a tanúsítványalapú biztonságot egy önálló Windows Server-fürthöz tartozó fürtben, tekintse meg a [különálló fürt biztonságossá tétele Windows rendszeren X. 509 tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md)című témakört.

### <a name="node-to-node-windows-security"></a>Csomópontok közötti Windows-Biztonság

> [!NOTE]
> A Windows-hitelesítés a Kerberoson alapul. Az NTLM hitelesítés típusa nem támogatott.
>
> Ha lehetséges, használjon X. 509 tanúsítványalapú hitelesítést Service Fabric-fürtökhöz.

Ha szeretné megtudni, hogyan állíthatja be a Windows-biztonságot egy önálló Windows Server-fürthöz, tekintse meg az [önálló fürt biztonságossá tétele Windows rendszeren a Windows biztonsági szolgáltatással](service-fabric-windows-cluster-windows-security.md)című témakört.

## <a name="client-to-node-security"></a>Az ügyfél és a csomópont közötti biztonság

Az ügyfél és a csomópont közötti biztonság hitelesíti az ügyfeleket, és segít a fürtben lévő ügyfelek és egyes csomópontok közötti kommunikáció biztonságossá tételében. Ez a típusú biztonság biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön üzembe helyezett alkalmazásokhoz. Az ügyfelek egyedi módon azonosíthatók a Windows biztonsági hitelesítő adataik vagy a tanúsítvány-biztonsági hitelesítő adataik használatával.

![Az ügyfél és a csomópont közötti kommunikáció diagramja][Client-to-Node]

Az Azure-on és a Windows rendszeren futó önálló fürtökön futó fürtök egyaránt használhatják a [tanúsítvány](/previous-versions/msp-n-p/ff649801(v=pandp.10)) -vagy a [Windows-biztonságot](/previous-versions/msp-n-p/ff649396(v=pandp.10)), bár a javaslat az X. 509 tanúsítványalapú hitelesítés használata, ha lehetséges.

### <a name="client-to-node-certificate-security"></a>Az ügyfél és a csomópont közötti tanúsítvány biztonsága

Az ügyfél és a csomópont közötti biztonság beállítása a fürt létrehozásakor, vagy a Azure Portal egy Resource Manager-sablon vagy egy önálló JSON-sablon használatával. A tanúsítvány létrehozásához meg kell adnia egy rendszergazdai ügyféltanúsítványt vagy egy felhasználói ügyféltanúsítványt. Az ajánlott eljárás az, hogy a rendszergazdai ügyfél és a felhasználói Ügyféltanúsítványok nem egyeznek meg a [csomópontok közötti biztonsághoz](#node-to-node-security)megadott elsődleges és másodlagos tanúsítványokkal. A fürt tanúsítványainak ugyanazokkal a jogokkal rendelkeznek, mint az ügyfél-rendszergazdai tanúsítványok. Ezeket azonban kizárólag fürtnek kell használni, és nem a rendszergazda felhasználók számára ajánlott biztonsági eljárás.

A felügyeleti tanúsítvány használatával a fürthöz csatlakozó ügyfelek teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez. A fürthöz a csak olvasási jogosultsággal rendelkező felhasználói ügyféltanúsítvány használatával csatlakozó ügyfelek csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez. Ezek a tanúsítványok a jelen cikk későbbi részében ismertetett Service Fabric RBAC használatosak.

A következő témakörből megtudhatja, hogyan állíthatja be a tanúsítvány-biztonságot az Azure-fürtben: [fürt beállítása Azure Resource Manager sablon használatával](service-fabric-cluster-creation-via-arm.md).

Ha szeretné megtudni, hogyan állíthatja be a tanúsítványalapú biztonságot egy önálló Windows Server-fürthöz tartozó fürtben, tekintse meg a [különálló fürt biztonságossá tétele Windows rendszeren X. 509 tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md)című témakört.

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Az Azure-ban az ügyfél és a csomópont közötti Azure Active Directory biztonság

Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlők) az alkalmazásokhoz való felhasználói hozzáférést kezeljék. Az alkalmazások a webes bejelentkezési kezelőfelülettel és a natív ügyfél-felhasználói felülettel rendelkezők számára vannak osztva. Ha még nem hozott létre bérlőt, először olvassa el, [hogyan szerezhet be Azure Active Directory bérlőt][active-directory-howto-tenant].

A Service Fabric-fürtök több belépési pontot biztosítanak a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorer][service-fabric-visualizing-your-cluster] és a [Visual studiót][service-fabric-manage-application-in-visual-studio]is. Ennek eredményeképpen két Azure AD-alkalmazást hoz létre a fürthöz, egy webalkalmazáshoz és egy natív alkalmazáshoz való hozzáférés szabályozásához.

Az Azure-on futó fürtök esetében Azure Active Directory (Azure AD) használatával is biztonságossá teheti a felügyeleti végpontokhoz való hozzáférést. A szükséges Azure AD-összetevők létrehozásával és a fürt létrehozásakor történő feltöltésével kapcsolatos további információkért lásd: az [Azure ad beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Biztonsági javaslatok

Az Azure-ban üzemeltetett nyilvános hálózatokban üzembe helyezett Service Fabric-fürtök esetén az ügyfél és a csomópont közötti kölcsönös hitelesítésre vonatkozó javaslat a következő:

* Azure Active Directory használata az ügyfél identitásához
* A http-kommunikáció kiszolgálói identitására és TLS-titkosítására vonatkozó tanúsítvány

Az Azure-ban üzemeltetett nyilvános hálózatokban üzembe helyezett Service Fabric-fürtök esetén a csomópontok közötti biztonságra vonatkozó javaslat a csomópontok hitelesítésére szolgáló fürt tanúsítványának használata.

Önálló Windows Server-fürtök esetén a Windows Server 2012 R2 és a Windows Active Directory használata esetén javasoljuk, hogy a Windows-biztonságot csoportosan felügyelt szolgáltatásfiókok használatával használja. Ellenkező esetben használja a Windows-biztonságot Windows-fiókokkal.

## <a name="service-fabric-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés Service Fabric

A hozzáférés-vezérlés használatával korlátozhatja a hozzáférést bizonyos fürt műveleteihez a különböző felhasználói csoportok esetében. Ez segít a fürt biztonságosabbá tételében. A fürthöz csatlakozó ügyfelek esetében két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.

A rendszergazdai szerepkörrel rendelkező felhasználók teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez, beleértve az olvasási és írási képességeket is. A felhasználói szerepkörhöz hozzárendelt felhasználók alapértelmezés szerint csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például a lekérdezési képességekhez). Emellett az alkalmazásokat és a szolgáltatásokat is feloldják.

Állítsa be a rendszergazda és a felhasználói ügyfél szerepkört a fürt létrehozásakor. Rendeljen hozzá szerepköröket külön identitások (például tanúsítványok vagy az Azure AD használatával) megadásával az egyes szerepkörök típusaihoz. További információ az alapértelmezett hozzáférés-vezérlési beállításokról és az alapértelmezett beállítások módosításáról: [Service Fabric szerepköralapú hozzáférés-vezérlés Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md)számára.

## <a name="x509-certificates-and-service-fabric"></a>X. 509 tanúsítványok és Service Fabric

Az X. 509 digitális tanúsítványok általában az ügyfelek és a kiszolgálók hitelesítésére szolgálnak. Emellett az üzenetek titkosítására és digitális aláírására is használhatók. A Service Fabric X. 509 tanúsítványokat használ a fürt biztonságossá tételéhez és az alkalmazás biztonsági funkcióinak biztosításához. További információ az X. 509 digitális tanúsítványokról: [a tanúsítványok használata](/dotnet/framework/wcf/feature-details/working-with-certificates). A [Key Vault](../key-vault/general/overview.md) segítségével kezelheti az Azure-beli Service Fabric-fürtök tanúsítványait.

Néhány fontos szempont:

* Ha éles számítási feladatokat futtató fürtökhöz szeretne tanúsítványokat létrehozni, használjon megfelelően konfigurált Windows Server Certificate Service-t, vagy egyet egy jóváhagyott hitelesítésszolgáltatótól [(CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Soha ne használjon olyan ideiglenes vagy tesztelési tanúsítványokat, amelyeket az éles környezetben lévő MakeCert.exe eszközökkel hoz létre.
* Önaláírt tanúsítványt is használhat, de csak tesztelési fürtben. Ne használjon önaláírt tanúsítványt az éles környezetben.
* A tanúsítvány ujjlenyomatának létrehozásakor győződjön meg róla, hogy létrehoz egy SHA1 ujjlenyomatot. A rendszer az ügyfél és a fürt tanúsítvány-ujjlenyomatai megfelelnek konfigurálásakor az SHA1-t használja.

### <a name="cluster-and-server-certificate-required"></a>Fürt és kiszolgáló tanúsítványa (kötelező)

Ezek a tanúsítványok (egy elsődleges és opcionálisan másodlagos) szükségesek a fürt biztonságossá tételéhez és a jogosulatlan hozzáférés megakadályozásához. Ezek a tanúsítványok biztosítják a fürt és a kiszolgáló hitelesítését.

A fürt hitelesítése a fürt-összevonás csomópont-csomópont típusú kommunikációját hitelesíti. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek bizonyítani tudják, hogy az identitásuk ezzel a tanúsítvánnyal is rendelkezhet. A kiszolgáló-hitelesítés hitelesíti a fürtszolgáltatási végpontokat egy felügyeleti ügyfélen, hogy a felügyeleti ügyfél tudja, hogy a valódi fürthöz beszél, nem pedig a középső ember. Ez a tanúsítvány egy TLS-t is biztosít a HTTPS-kezelési API-hoz és a HTTPS-en keresztüli Service Fabric Explorerhoz. Amikor egy ügyfél vagy csomópont hitelesít egy csomópontot, az egyik kezdeti ellenőrzés a **tulajdonos** mezőben lévő köznapi név értéke. A köznapi névnek vagy a tanúsítvány tulajdonosának alternatív neveinek (SANs) szerepelnie kell az engedélyezett köznapi nevek listáján.

A tanúsítványnak meg kell felelnie a következő követelményeknek:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot. Ezeknek a tanúsítványoknak jellemzően kiterjesztése. pfx vagy. PEM  
* A tanúsítványt létre kell hozni a Key Exchange számára, amely exportálható egy személyes információcsere (. pfx) fájlba.
* A **tanúsítvány tulajdonosának nevének egyeznie kell azzal a tartománnyal, amelyet a Service Fabric-fürt eléréséhez használ**. Ez a megfeleltetés szükséges ahhoz, hogy TLS-t biztosítson a fürt HTTPS-felügyeleti végpontjának és Service Fabric Explorer. A *. cloudapp.azure.com tartományhoz nem szerezhet be TLS/SSL-tanúsítványt a hitelesítésszolgáltatótól (CA). Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Néhány további megfontolandó szempont:

* A **tulajdonos** mező több értékkel is rendelkezhet. Minden érték előtaggal van ellátva, hogy jelezze az érték típusát. Az inicializálás általában **CN** ( *köznapi név*); például: **cn = www \. contoso.com**.
* A **Tárgy** mező üres is lehet.
* Ha az opcionális **tulajdonos alternatív neve** mező fel van töltve, a tanúsítvány köznapi nevével és egy, San-ra vonatkozó bejegyzéssel kell rendelkeznie. Ezek a **DNS-név** értékeiként vannak megadva. Ha meg szeretné tudni, hogyan hozhatók létre a (z) SANs tanúsítvánnyal rendelkező tanúsítványok, tekintse meg [a tulajdonos alternatív nevének hozzáadása biztonságos LDAP-tanúsítványhoz](https://support.microsoft.com/kb/931351)című témakört.
* A tanúsítvány **rendeltetésszerű felhasználási** célja mezőjének tartalmaznia kell egy megfelelő értéket, például a **kiszolgáló-hitelesítés** vagy az **ügyfél-hitelesítés** értékét.

### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)

A fürtön tetszőleges számú további tanúsítvány telepíthető alkalmazás biztonsági célokra. A fürt létrehozása előtt vegye figyelembe azokat az alkalmazás-biztonsági forgatókönyveket, amelyekhez tanúsítvány szükséges a csomópontokon való telepítéshez, például:

* Az alkalmazás konfigurációs értékeinek titkosítása és visszafejtése.
* Adattitkosítás a csomópontok között a replikálás során.

A biztonságos fürtök létrehozásának koncepciója ugyanaz, függetlenül attól, hogy Linux-vagy Windows-fürtök.

### <a name="client-authentication-certificates-optional"></a>Ügyfél-hitelesítési tanúsítványok (nem kötelező)

A rendszergazda vagy a felhasználói ügyfél műveleteihez tetszőleges számú további tanúsítvány is megadható. Az ügyfél használhatja ezeket a tanúsítványokat, ha kölcsönös hitelesítésre van szükség. Az ügyféltanúsítványok általában nem harmadik féltől származó HITELESÍTÉSSZOLGÁLTATÓ által kiállított tanúsítványok. Ehelyett az aktuális felhasználói hely személyes tárolója általában tartalmaz egy legfelső szintű hitelesítésszolgáltató által elhelyezett ügyféltanúsítványt. A tanúsítványnak az **ügyfél-hitelesítés** **kívánt céljának** kell lennie.  

Alapértelmezés szerint a fürt tanúsítványa rendszergazdai jogosultságokkal rendelkezik. Ezek a további Ügyféltanúsítványok nem telepíthetők a fürtbe, de a fürt konfigurációjában engedélyezettként vannak megadva.  Az ügyféltanúsítványt azonban telepíteni kell az ügyfélszámítógépekre a fürthöz való kapcsolódáshoz és a műveletek elvégzéséhez.

> [!NOTE]
> Egy Service Fabric-fürtön lévő összes felügyeleti művelethez kiszolgálói tanúsítványok szükségesek. Az ügyféltanúsítványok nem használhatók felügyelethez.

## <a name="next-steps"></a>Következő lépések

* [Fürt létrehozása az Azure-ban Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md)
* [Fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
