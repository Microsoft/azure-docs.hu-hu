---
title: 'Azure ExpressRoute Private-társítás: IPsec átviteli mód konfigurálása – Windows-gazdagépek'
description: Az IPsec-átviteli mód engedélyezése az Azure Windows-alapú virtuális gépek és a helyszíni Windows-gazdagépek között, a csoportházirend-objektumok és a szervezeti egységek használatával történő ExpressRoute keresztül.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017272"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPsec-átviteli mód konfigurálása a ExpressRoute privát társításához

Ebből a cikkből megtudhatja, hogyan hozhat létre IPsec-alagutakat átviteli módban a ExpressRoute-alapú privát kapcsolaton keresztül. Az alagút a Windows rendszerű és a helyszíni Windows-gazdagépeket futtató Azure-beli virtuális gépek között jön létre. A jelen cikkben ismertetett lépések csoportházirend-objektumokat használnak. A konfigurációt szervezeti egységek (OU) és csoportházirend-objektumok (GPO-k) használata nélkül is létrehozhatja. A szervezeti egységek és a csoportházirend-objektumok kombinációja megkönnyíti a biztonsági házirendek szabályozását, és lehetővé teszi a gyors vertikális felskálázást. A cikkben ismertetett lépések feltételezik, hogy már rendelkezik Active Directory konfigurációval, és ismeri a szervezeti egységek és a csoportházirend-objektumok használatát.

## <a name="about-this-configuration"></a>A konfiguráció bemutatása

A következő lépésekben található konfiguráció egyetlen Azure-beli virtuális hálózatot (VNet) használ a ExpressRoute privát társításával. Ez a konfiguráció azonban más Azure-virtuális hálózatok és helyszíni hálózatokra is terjedhet. Ez a cikk segítséget nyújt egy olyan IPsec-titkosítási házirend definiálásához, amelyet az Azure-beli virtuális gépek vagy a helyszíni gazdagépek egy csoportjára alkalmazhat. Ezek az Azure-beli virtuális gépek vagy helyszíni gazdagépek ugyanahhoz a szervezeti egységhez tartoznak. Az Azure-beli virtuális gépek (VM1 és VM2) és a helyszíni host1 között csak a 8080-as porttal rendelkező HTTP-forgalomhoz konfigurálhatja a titkosítást. A követelmények alapján különböző típusú IPsec-házirendeket hozhat létre.

### <a name="working-with-ous"></a>Szervezeti egységek használata 

A szervezeti egységhez társított biztonsági házirendet a rendszer a csoportházirend-objektumon keresztül küldi el a számítógépeknek. A szervezeti egységek használatának néhány előnye, a házirendek egyetlen gazdagépre való alkalmazása helyett a következők:

* Ha egy házirendet egy szervezeti egységhez társít, azzal garantálja, hogy az ugyanahhoz a szervezeti egységhez tartozó számítógépek ugyanazt a szabályzatot kapják meg.
* A szervezeti egységhez társított biztonsági házirend módosítása a szervezeti egység összes gazdagépén alkalmazza a módosításokat.

### <a name="diagrams"></a>Diagramok

Az alábbi ábrán az összekapcsolási és a hozzárendelt IP-címtartomány látható. Az Azure-beli virtuális gépek és a helyszíni gazdagép Windows 2016 rendszert futtat. Az Azure-beli virtuális gépek és a helyszíni host1 ugyanahhoz a tartományhoz tartoznak. Az Azure-beli virtuális gépek és a helyszíni gazdagépek a DNS használatával megfelelően tudják feloldani a neveket.

[![1]][1]

Ez az ábra a ExpressRoute-alapú privát kapcsolaton belüli átviteli IPsec-alagutakat mutatja be.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Az IPsec-házirend használata

A Windows rendszerben a titkosítás az IPsec-házirenddel van társítva. Az IPsec-házirend határozza meg, hogy mely IP-forgalom van védve, és milyen biztonsági mechanizmust alkalmaz az IP-csomagokra.
Az **IPSec-házirendek** a következő elemekből **állnak: szűrőlisták**, **szűrési műveletek** és **biztonsági szabályok**.

Az IPsec-házirend konfigurálásakor fontos megérteni a következő IPsec-házirend-terminológiát:

* **IPSec-házirend:** Szabályok gyűjteménye. Egy adott időpontban csak egy házirend lehet aktív ("hozzárendelt"). Az egyes szabályzatok egy vagy több szabályt tartalmazhatnak, amelyek mindegyike egyszerre aktív lehet. A számítógépeknek adott időben csak egy aktív IPsec-házirendet lehet hozzárendelni. Az IPsec-házirenden belül azonban több, különböző helyzetekben elvégezhető műveletet is meghatározhat. Az IPsec-szabályok minden készlete egy olyan szűrő-listához van társítva, amely hatással van a szabály által érintett hálózati forgalom típusára.

* **Szűrőlisták:** A szűrőlisták egy vagy több szűrőből álló kötegek. Egy lista több szűrőt is tartalmazhat. A szűrők határozzák meg, hogy a kommunikáció a következő feltételek alapján blokkolva, engedélyezve vagy biztonságossá válik-e: IP-címtartományok, protokollok vagy akár konkrét portok. Mindegyik szűrő megfelel bizonyos feltételeknek; például egy adott alhálózatról küldött csomagok egy adott célport adott számítógépére érkeznek. Ha a hálózati feltételek egy vagy több szűrőnek felelnek meg, a szűrők listája aktiválva lesz. Minden szűrő definiálva van egy adott szűrő listáján belül. A szűrők nem oszthatók meg a szűrőlisták között. Egy adott szűrőlista azonban több IPsec-házirendbe is beépíthető. 

* **Szűrési műveletek:** A biztonsági módszer a számítógépek által az IKE-egyeztetések során kínált biztonsági algoritmusok, protokollok és kulcsok körét határozza meg. A szűrési műveletek a biztonsági módszerek listája, elsőbbségi sorrendben rangsorolva.  Amikor egy számítógép egy IPsec-munkamenetet egyeztet, a szűrőműveletek listájában tárolt biztonsági beállítások alapján elfogadja vagy elküldi a javaslatokat.

* **Biztonsági szabályok:** A szabályok szabályozzák, hogy az IPsec-házirendek hogyan és mikor védik a kommunikációt. A **szűrő-lista** és a **szűrési műveletek** használatával hozza létre az IPsec-szabályt az IPSec-kapcsolatok létrehozásához. Az egyes szabályzatok egy vagy több szabályt tartalmazhatnak, amelyek mindegyike egyszerre aktív lehet. Minden szabály tartalmazza az IP-szűrők listáját, valamint egy olyan biztonsági műveletet, amely az adott szűrőlista egyezésével történik:
  * IP-szűrési műveletek
  * Hitelesítési módszerek
  * IP-alagút beállításai
  * Kapcsolattípusok

[![5]][5]

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy megfelel a következő előfeltételeknek:

* Rendelkeznie kell egy működő Active Directory-konfigurációval, amelyet Csoportházirend beállítások megvalósítására használhat. További információ a csoportházirend-objektumokról: [csoportházirend objektumok](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
  * A ExpressRoute áramkör létrehozásával kapcsolatos információkért lásd: [ExpressRoute-áramkör létrehozása](expressroute-howto-circuit-arm.md). 
  * Ellenőrizze, hogy az áramkört engedélyezte-e a kapcsolat szolgáltatója. 
  * Ellenőrizze, hogy rendelkezik-e az Ön áramköréhez konfigurált Azure-beli magánhálózati kapcsolattal. Az útválasztási utasításokért tekintse meg az [Útválasztás konfigurálása](expressroute-howto-routing-arm.md) című cikket. 
  * Ellenőrizze, hogy rendelkezik-e egy VNet és egy virtuális hálózati átjáróval, és hogy létrejött-e teljesen kiépítve. A [ExpressRoute virtuális hálózati átjárójának létrehozásához](expressroute-howto-add-gateway-resource-manager.md)kövesse az utasításokat. A ExpressRoute virtuális hálózati átjárója a "ExpressRoute" GatewayType használja, nem pedig a VPN-t.

* A ExpressRoute virtuális hálózati átjárónak csatlakoznia kell a ExpressRoute-áramkörhöz. További információ: [VNet összekapcsolása egy ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md).

* Ellenőrizze, hogy az Azure Windows rendszerű virtuális gépek telepítve vannak-e a VNet.

* Ellenőrizze, hogy van-e kapcsolat a helyszíni gazdagépek és az Azure-beli virtuális gépek között.

* Ellenőrizze, hogy az Azure Windows rendszerű virtuális gépek és a helyszíni gazdagépek használhatnak-e DNS-t a nevek megfelelő feloldásához.

### <a name="workflow"></a>Munkafolyamat

1. Hozzon létre egy csoportházirend-objektumot, és rendelje hozzá a szervezeti egységhez.
2. IPsec- **szűrési művelet** definiálása.
3. Határozza meg az IPsec- **szűrőlisták listáját**.
4. Hozzon létre egy IPsec-házirendet **biztonsági szabályokkal**.
5. Rendelje hozzá az IPsec GPO-t a szervezeti egységhez.

### <a name="example-values"></a>Példaértékek

* **Tartománynév:** ipsectest.com

* **Szervezeti egység:** IPSecOU

* Helyszíni **Windows-számítógép:** host1

* **Azure Windows rendszerű virtuális gépek:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. csoportházirend-objektum létrehozása

1. Hozzon létre egy szervezeti egységhez csatolt új csoportházirend-objektumot a Csoportházirend felügyeleti beépülő modul megnyitásával. Ezután keresse meg azt a szervezeti egységet, amelyhez a csoportházirend-objektum csatolva lesz. A példában a szervezeti egység neve **IPSecOU**. 

   [![9]][9]
2. A Csoportházirend felügyeleti beépülő modulban válassza ki a szervezeti egységet, majd kattintson a jobb gombbal. A legördülő menüben válassza a "**csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt...**" lehetőséget.

   [![10]][10]
3. Adjon nevet a csoportházirend-objektumnak egy intuitív névvel, hogy később könnyen megkereshető legyen. Kattintson az **OK** gombra a csoportházirend-objektum létrehozásához és csatolásához.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. a csoportházirend-objektum hivatkozásának engedélyezése

Ha a csoportházirend-objektumot a szervezeti egységre szeretné alkalmazni, a csoportházirend-objektum nem csak a szervezeti egységhez van társítva, de a hivatkozást is engedélyezni kell.

1. Keresse meg a létrehozott csoportházirend-objektumot, kattintson rá a jobb gombbal, és válassza a **Szerkesztés** lehetőséget a legördülő menüből.
2. Ha a csoportházirend-objektumot a szervezeti egységre szeretné alkalmazni, válassza a **hivatkozás engedélyezve** lehetőséget.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. az IP-szűrő művelet megadása

1. A legördülő menüben kattintson a jobb gombbal a **Active Directory IP-biztonsági házirend** elemre, majd válassza az **IP-szűrőlisták és szűrési műveletek kezelése** lehetőséget.

   [![15]][15]
2. A "**szűrőműveletek kezelése**" lapon válassza a **Hozzáadás** lehetőséget.

   [![16]][16]

3. Az **IP-biztonsági szűrő művelet varázslóban** válassza a **tovább** lehetőséget.

   [![17]][17]
4. Adjon nevet a szűrőnek egy intuitív nevet, hogy később is megtalálja. Ebben a példában a szűrő művelet neve **myEncryption**. Leírást is hozzáadhat a szolgáltatáshoz. Ezután válassza a **tovább** lehetőséget.

   [![18]][18]
5. Az **egyeztetési biztonság** lehetővé teszi, hogy meghatározza a viselkedést, ha az IPSec nem hozható létre másik számítógéppel. Válassza a **Biztonság egyeztetése** elemet, majd kattintson a **tovább** gombra.

   [![19]][19]
6. Az **IPsec-et nem támogató számítógépekkel folytatott kommunikáció** lapon válassza a nem **biztonságos kommunikáció engedélyezése** lehetőséget, majd kattintson a **tovább** gombra.

   [![20]][20]
7. Az **IP-forgalom és biztonság** lapon válassza az **Egyéni** lehetőséget, majd a **beállítások...** lehetőséget.

   [![21]][21]
8. Az **egyéni biztonsági módszer beállításai** lapon válassza az **adatok sértetlensége és titkosítása (ESP): SHA1, 3DES** elemet. Ez után válassza az **OK** gombot.

   [![22]][22]
9. A **szűrőműveletek kezelése** oldalon láthatja, hogy a **myEncryption** szűrő hozzáadása sikeres volt. Válassza a **Bezárás** gombot.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. adja meg az IP-szűrőlisták listáját

Hozzon létre egy olyan szűrőlistát, amely a 8080-as porttal rendelkező titkosított HTTP-forgalmat határozza meg.

1. Ahhoz, hogy a forgalom milyen típusú forgalmat titkosítani kell, használjon **IP-szűrőlistát**. Az IP-szűrőlisták **kezelése** lapon válassza a **Hozzáadás** lehetőséget új IP-szűrőlista hozzáadásához.

   [![24]][24]
2. A **név:** mezőben adja meg az IP-szűrőlista nevét. Például: **Azure-onpremises-HTTP8080**. Ezután válassza a **Hozzáadás** lehetőséget.

   [![25]][25]
3. Az **IP-szűrő leírása és a tükrözött tulajdonság** lapon válassza a **tükrözött** lehetőséget. A tükrözött beállítás mindkét irányban elérhető csomagokat tartalmazza, ami kétirányú kommunikációt tesz lehetővé. Ezután kattintson a **Tovább** gombra.

   [![26]][26]
4. Az **IP-forgalom forrása** lapon, a **forrás címe:** legördülő menüben válasszon ki **egy adott IP-címet vagy alhálózatot**. 

   [![27]][27]
5. Adja meg a forráscím **IP-címét vagy alhálózatát:** az IP-forgalom, majd válassza a **tovább** lehetőséget.

   [![28]][28]
6. Itt adhatja meg a **cél címét:** IP-cím vagy alhálózat. Ezután válassza a **tovább** lehetőséget.

   [![29]][29]
7. Az **IP protokoll típusa** lapon válassza a **TCP** lehetőséget. Ezután válassza a **tovább** lehetőséget.

   [![30]][30]
8. Az **IP-protokoll portja** lapon válassza a **bármelyik portot** és **a következő portot:**. Írja be a **8080** értéket a szövegmezőbe. Ezek a beállítások csak a 8080-as porton megadott HTTP-forgalmat titkosítják. Ezután válassza a **tovább** lehetőséget.

   [![31]][31]
9. Tekintse meg az IP-szűrők listáját.  Az **Azure-onpremises-HTTP8080** IP-szűrőlista konfigurációja a következő feltételeknek megfelelő összes forgalom titkosítását indítja el:

   * Bármely forráscím a 10.0.1.0/24-ben (Azure Subnet2 alhálózattal)
   * Bármely célcím a 10.2.27.0/25-ben (helyszíni alhálózat)
   * TCP protokoll
   * 8080-es célport

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. az IP-szűrők listájának szerkesztése

A helyszíni gazdagépről az Azure-beli virtuális gépre történő azonos típusú forgalom titkosításához második IP-szűrőre van szükség. Kövesse az első IP-szűrő beállításához használt lépéseket, és hozzon létre egy új IP-szűrőt. Az egyetlen különbség a forrás-alhálózat és a célként megadott alhálózat.

1. Ha új IP-szűrőt szeretne hozzáadni az IP-szűrő listához, válassza a **Szerkesztés** lehetőséget.

   [![33]][33]
2. Az **IP-szűrők listája** lapon válassza a **Hozzáadás** lehetőséget.

   [![34]][34]
3. Hozzon létre egy második IP-szűrőt a következő példában szereplő beállítások használatával:

   [![35]][35]
4. A második IP-szűrő létrehozása után az IP-szűrő lista a következőképpen fog kinézni:

   [![36]][36]

Ha a helyszíni hely és az Azure-alhálózat között titkosításra van szükség az alkalmazások védetté tételéhez. A meglévő IP-szűrőlista módosítása helyett új IP-szűrőlista hozzáadására van lehetőség. Ha két vagy több IP-szűrőlistát társít ugyanahhoz az IPsec-házirendhez, a rugalmasságot biztosít Önnek. Az IP-szűrőlista módosítására vagy eltávolítására anélkül is lehetősége van, hogy ez hatással lenne a többi IP-szűrési listára.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. IPsec biztonsági házirend létrehozása 

Hozzon létre egy IPsec-házirendet biztonsági szabályokkal.

1. Válassza ki a **IPSecurity szabályzatokat az Active Directoryban** , amely a szervezeti egységhez van társítva. Kattintson a jobb gombbal, majd válassza az **IP-biztonsági házirend létrehozása** lehetőséget.

   [![37]][37]
2. Nevezze el a biztonsági házirendet. Például: **Policy-Azure-onpremises**. Ezután válassza a **tovább** lehetőséget.

   [![38]][38]
3. A jelölőnégyzet bejelölése nélkül válassza a **Next (tovább** ) lehetőséget

   [![39]][39]
4. Győződjön meg arról, hogy a **Tulajdonságok szerkesztése** jelölőnégyzet be van jelölve, majd válassza a **Befejezés** lehetőséget.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. az IPsec biztonsági házirend szerkesztése

Adja hozzá az IPsec-házirendhez a korábban konfigurált **IP-szűrési listát** és **szűrési műveletet** .

1. A HTTP-házirend tulajdonságok **szabályai** lapon válassza a **Hozzáadás** lehetőséget.

   [![41]][41]
2. Az Üdvözöljük lapon kattintson a **Tovább** gombra.

   [![42]][42]
3. A szabály lehetővé teszi az IPsec mód definiálását: bújtatási mód vagy átviteli mód.

   * Bújtatási módban az eredeti csomag IP-fejlécek készletével van beágyazva. A bújtatási mód védi a belső útválasztási adatokat az eredeti csomag IP-fejlécének titkosításával. A bújtatási mód széles körben implementálva van az átjárók között a helyek közötti VPN-forgatókönyvekben. A bújtatási mód a gazdagépek közötti végpontok közötti titkosításhoz használt legtöbb esetben használatos.

   * A szállítási mód csak a hasznos adatokat és az ESP-pótkocsit titkosítja. az eredeti csomag IP-fejléce nem titkosított. Átviteli módban a csomagok IP-forrása és IP-címe változatlan marad.

   Válassza ki **ezt a szabályt, ne adjon meg alagutat**, majd kattintson a **tovább** gombra.

   [![43]][43]
4. A **hálózat típusa** határozza meg, hogy melyik hálózati kapcsolat társítva van a biztonsági házirenddel. Válassza a **minden hálózati kapcsolat** lehetőséget, majd kattintson a **tovább** gombra.

   [![44]][44]
5. Válassza ki a korábban létrehozott IP-szűrő listát, az  **Azure-onpremises-HTTP8080**, majd a **tovább** lehetőséget.

   [![45]][45]
6. Válassza ki a korábban létrehozott szűrési művelet **myEncryption** .

   [![46]][46]
7. A Windows négy különböző típusú hitelesítést támogat: Kerberos, tanúsítványok, NTLMv2 és előmegosztott kulcs. Mivel a tartományhoz csatlakoztatott gazdagépekkel dolgozunk, válassza a **Active Directory alapértelmezett (Kerberos V5 protokoll)** lehetőséget, majd kattintson a **tovább** gombra.

   [![47]][47]
8. Az új szabályzat létrehozza a biztonsági szabályt: **Azure-onpremises-HTTP8080**. Válassza az **OK** lehetőséget.

   [![48]][48]

Az IPsec-házirend használatához az 8080-as célport összes HTTP-kapcsolatának az IPsec átviteli módot kell használnia. Mivel a HTTP egy tiszta szöveges protokoll, amely engedélyezi a biztonsági házirendet, gondoskodik arról, hogy az adattitkosítás a ExpressRoute privát kapcsolaton keresztül történjen. A Active Directory IPsec-házirendje összetettebb a fokozott biztonságú Windows tűzfal konfigurálásához. Ez azonban lehetővé teszi az IPsec-kapcsolatok további testreszabását.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. az IPsec csoportházirend-objektumának társítása a szervezeti egységhez

1. Tekintse meg a szabályzatot. A biztonsági csoportházirend definiálva van, de még nincs hozzárendelve.

   [![49]][49]
2. A biztonsági csoportházirend szervezeti egység **IPSecOU** való hozzárendeléséhez kattintson a jobb gombbal a biztonsági házirendre, majd válassza a **hozzárendelés** lehetőséget.
   A szervezeti egységhez tartozó összes számítógép esetében a biztonsági csoportházirend hozzá lesz rendelve.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Forgalom titkosításának ellenõrzése

Ha szeretné megtekinteni a szervezeti egységen alkalmazott titkosítási csoportházirend-objektumot, telepítse az IIS-t az összes Azure-beli virtuális gépre és a host1. Minden IIS testre szabott, hogy válaszoljon a HTTP-kérelmekre a 8080-es porton.
A titkosítás ellenőrzéséhez a szervezeti egység összes számítógépén telepítheti a Network szippantás (például a Wireshark) programot.
A PowerShell-parancsfájlok HTTP-ügyfélként működnek a 8080-es porton HTTP-kérések létrehozásához:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
A következő hálózati rögzítés megjeleníti a helyszíni host1 és a megjelenítési szűrő ESP-vel való eredményét, hogy csak a titkosított forgalomhoz illeszkedjen:

[![51]][51]

Ha a helyszíni PowerShell-szkriptet (HTTP-ügyfelet) futtatja, az Azure-beli virtuális gépen lévő hálózati rögzítés hasonló nyomkövetést mutat.

## <a name="next-steps"></a>Következő lépések

A ExpressRoute kapcsolatos további információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Hálózati diagram IPSec átviteli módja ExpressRoute-n keresztül"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPSec – érdekes forgalom"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPSec-házirend"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "szervezeti egység a csoportházirend"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "a szervezeti egységhez társított csoportházirend-objektum létrehozása"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "rendeljen hozzá egy nevet a szervezeti egységhez társított csoportházirend-objektumhoz"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "a csoportházirend-objektum szerkesztése"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP-szűrőlisták és szűrési műveletek kezelése"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "szűrő hozzáadása művelet"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Művelet varázsló"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "szűrési művelet neve"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "szűrési művelet"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "meg kell határozni, hogy a viselkedés nem biztonságos a kapcsolatok" létrehozásakor
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "biztonsági mechanizmus"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "egyéni biztonsági módszer"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "szűrési műveletek listája"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "új IP-szűrőlista hozzáadása"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "http-forgalom hozzáadása az IP-szűrőhöz"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "egyeztetési csomag mindkét irányban"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "a forrás-alhálózat kiválasztása"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "forrásoldali hálózat"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "célként megadott hálózat"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "forrásport és célport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Filter lista"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-szűrési lista http-forgalommal"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "második IP-szűrő hozzáadása"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-szűrési lista – második bejegyzés"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-szűrési lista – második bejegyzés"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "az IP-biztonsági házirend létrehozása"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "az IPSec-házirend neve"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPSec-házirend varázsló"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "az IPSec-házirend szerkesztése"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "új biztonsági szabály hozzáadása az IPSec-házirendhez"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "új biztonsági szabály létrehozása"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "átviteli mód"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "hálózat típusa"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "meglévő IP-szűrőlista kiválasztása"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "meglévő szűrési művelet kiválasztása"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "a hitelesítési módszer kiválasztása"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "a biztonsági házirend létrehozási folyamatának vége"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPSec-házirend csatolva a csoportházirend-objektumhoz, de nincs hozzárendelve"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec-házirend társítva a csoportházirend-objektumhoz"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "IPSec titkosított forgalom rögzítése"