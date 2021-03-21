---
title: Az Azure AD biztonságos hibrid hozzáférése az F5 üzembe helyezési útmutatóval | Microsoft Docs
description: Oktatóanyag az F5 BIG-IP Virtual Edition (VE) virtuális gép üzembe helyezéséhez az Azure IaaS a biztonságos hibrid hozzáférés érdekében
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730657"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Oktatóanyag az F5 BIG-IP Virtual Edition virtuális gép üzembe helyezéséhez az Azure IaaS a biztonságos hibrid hozzáférés érdekében

Ez az oktatóanyag végigvezeti a BIG-IP Vitural Edition (VE) Azure IaaS való üzembe helyezésének teljes folyamatán. Az oktatóanyag végére a következőket kell tennie:

- Teljes mértékben előkészített BIG-IP virtuális gép (VM) a koncepció biztonságos hibrid hozzáférési (SHA) igazolásának modellezéséhez

- Az új BIG-IP Rendszerfrissítések és gyorsjavítások tesztelésére szolgáló előkészítési példány

## <a name="prerequisites"></a>Előfeltételek

Az előző F5 BIG-IP-élmény vagy-ismeret nem szükséges, azonban javasoljuk, hogy ismerkedjen meg az [F5 Big-IP terminológiával](https://www.f5.com/services/resources/glossary). Az SHA használatához a BIG-IP Azure-beli üzembe helyezéséhez a következőket kell tennie:

- Fizetős Azure-előfizetés vagy ingyenes 12 hónapos [próbaverziós előfizetés](https://azure.microsoft.com/free/).

- A következő F5 BIG-IP licenc SKU-i

  - F5 BIG-IP® legjobb csomag

  - F5 BIG-IP Access Policy Manager™ (APM) önálló licenc

  - F5 BIG-IP Access Policy Manager™ (APM) bővítmény licence meglévő BIG-IP F5 BIG-IP® helyi Traffic Manager™ (LTM)
  
  - 90-nap BIG-IP teljes szolgáltatás [próbaverziós licence](https://www.f5.com/trial/big-ip-trial.php).

- Helyettesítő karakter vagy tulajdonos alternatív neve (SAN), a webalkalmazások Secure Socket Layer (SSL) protokollon keresztüli közzétételéhez. A [titkosítás](https://letsencrypt.org/) ingyenes 90 napos tanúsítványt biztosít a teszteléshez.

- SSL-tanúsítvány a BIG-IPs felügyeleti felület biztonságossá tételéhez. A webalkalmazások közzétételéhez használt tanúsítvány használható, ha a tulajdonos a BIG-IP teljes tartománynevét (FQDN) is megfelel. Például a tárgy *. contoso.com definiált helyettesítő karakteres tanúsítvány a következőre alkalmas: `https://big-ip-vm.contoso.com:8443`

A virtuális gépek üzembe helyezése és az alaprendszer-konfigurációk kb. 30 percet vesznek igénybe. Ekkor a BIG-IP platform készen áll az [itt](f5-aad-integration.md)felsorolt SHA-forgatókönyvek megvalósítására.

A forgatókönyvek teszteléséhez ez az oktatóanyag feltételezi, hogy a BIG-IP üzembe helyezése egy Active Directory (AD-) környezetet tartalmazó Azure-erőforráscsoporthoz történik. A környezetnek a tartományvezérlő (DC) és a webgazda (IIS) virtuális gépekből kell állnia. Ha ezek a kiszolgálók más helyen találhatók a BIG-IP virtuális gépen, akkor is rendben van, így a BIG-IP-nek minden olyan szerepköre van, amely az adott forgatókönyv támogatásához szükséges. Azok a forgatókönyvek, amelyekben a BIG-IP virtuális gép VPN-kapcsolaton keresztül egy másik környezethez csatlakozik, szintén támogatottak.

Ha nem rendelkezik az itt említett elemekkel a teszteléshez, a [szkript](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)használatával üzembe helyezhet egy teljes ad tartományi környezetet az Azure-ban. A minta tesztelési alkalmazások gyűjteménye programozott módon is telepíthető egy IIS-webállomásra ezen [parancsfájl-automatizálás](https://github.com/jeevanbisht/DemoSuite)használatával.

>[!NOTE]
>A [Azure Portal](https://portal.azure.com/#home) folyamatosan fejlődik, így az oktatóanyag egyes lépései eltérhetnek a Azure Portalban megfigyelt tényleges elrendezéstől.

## <a name="azure-deployment"></a>Azure-beli üzembe helyezés

A BIG-IP különböző topológiákban is üzembe helyezhető. Ez az útmutató egyetlen hálózati adapter (NIC) központi telepítésére koncentrál. Ha azonban a BIG-IP-környezet több hálózati adaptert igényel a magas rendelkezésre álláshoz, a hálózat elkülönítéséhez vagy az 1 GB-nál több átviteli sebességhez, érdemes lehet F5's előre lefordított [Azure Resource Manager (ARM) sablonokat](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)használni.

A BIG-IP VE [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps)való üzembe helyezéséhez hajtsa végre a következő feladatokat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/#home) egy olyan fiókkal, amely rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel. Például: közreműködő

2. A felső menüszalag keresőmezőbe írja be a **piactér** kifejezést, majd **írja be** a következőt: ENTER

3. Írja be az **F5** billentyűt a piactér szűrőbe, majd **írja be az ENTER billentyűt**

4. Válassza a felső menüszalagon a **+ Hozzáadás** lehetőséget, és írja be az **F5** billentyűt a piactér szűrőbe, majd írja **be** a következőt:

5. Válassza az **F5 Big-IP Virtual Edition (BYOL)** elemet,  >  és **válassza ki az**  >  **F5 Big-IP ve-all (BYOL, 2 rendszerindítási helyszínek) csomagot.**

6. Válassza a **Létrehozás** lehetőséget.

![A rendszerkép megjeleníti a szoftvercsomagok kiválasztásának lépéseit.](./media/f5ve-deployment-plan/software-plan.png)

7. Lépjen az **alapismeretek** menüre, és használja az alábbi beállításokat

 |  Projekt részletei     |  Érték     |
 |:-------|:--------|
 |Előfizetés|Cél-előfizetés a BIG-IP virtuális gépek üzembe helyezéséhez|
 |Erőforráscsoport | Meglévő Azure-erőforráscsoport a BIG-IP virtuális gép üzembe helyezése vagy létrehozása. A tartományvezérlő és az IIS-alapú virtuális gépek azonos erőforráscsoporthoz kell lennie|
 | **Példány adatai**|  |
 |Virtuális gép neve| Példa BIG-IP-VM |
 |Region | Cél Azure geo a BIG-IP-VM számára |
 |Rendelkezésre állási beállítások| Csak a virtuális gép éles környezetben való használatának engedélyezése|
 |Kép| F5 BIG-IP VE-ALL (BYOL, 2 rendszerindítási helyszín)|
 |Azure Spot-példány| Nem, de szükség esetén szabadon engedélyezheti |
 |Méret| A minimális specifikációnak 2 vCPU és 8 GB memóriának kell lennie|
 |**Rendszergazdai fiók**|  |
 |Hitelesítéstípus|Válassza a jelszó lehetőséget most. Később is válthat egy kulcspárt |
 |Felhasználónév|Az identitás, amelyet a rendszer a felügyeleti felületek eléréséhez a BIG-IP helyi fiókként hoz létre. A Felhasználónév megkülönbözteti a kis-és nagybetűket.|
 |Jelszó|Biztonságos rendszergazdai hozzáférés erős jelszóval|
 |**Bejövő portok szabályai**|  |
 |Nyilvános bejövő portok|Nincs|

8. Válassza a **Next (tovább** ) lehetőséget: az összes alapértelmezett beállítást elhagyó lemezek, majd a **Tovább: hálózatkezelés** elemre.

9. A **hálózat** menüben hajtsa végre ezeket a beállításokat.

 |Hálózati adapter|      Érték |
 |:--------------|:----------------|
 |Virtuális hálózat|A tartományvezérlő és az IIS-alapú virtuális gépek által használt Azure-VNet, vagy létrehozhat egy|
 |Alhálózat| Ugyanaz az Azure belső alhálózat, mint a tartományvezérlő és az IIS-alapú virtuális gépek, vagy hozzon létre egyet|
 |Nyilvános IP-cím |  Nincs|
 |NIC hálózati biztonsági csoport| Válassza a nincs lehetőséget, ha az előző lépésekben kiválasztott Azure-alhálózat már társítva van egy hálózati biztonsági csoporttal (NSG). egyéb esetben válassza az alapszintű lehetőséget|
 |Hálózati gyorsítás| Ki |
 |**Terheléselosztás**|     |
 |Virtuális gép terheléselosztása| No|

10. Válassza a **Tovább: kezelés** lehetőséget, majd fejezze be ezeket a beállításokat.

 |Figyelés|    Érték |
 |:---------|:-----|
 |Részletes figyelés| Ki|
 |Rendszerindítási diagnosztika|Engedélyezés egyéni Storage-fiókkal. Lehetővé teszi a BIG-IP Secure Shell (SSH) felülethez való csatlakozást a Azure Portal soros konzolján keresztül. Bármely elérhető Azure Storage-fiók kiválasztása|
 |**Identitás**|  |
 |Rendszerhez rendelt felügyelt identitás|Ki|
 |Azure Active Directory|A BIG-IP jelenleg nem támogatja ezt a beállítást|
 |**Automatikus leállítás**|    |
 |Automatikus leállítás engedélyezése| Ha tesztelést szeretne végezni, érdemes lehet beállítani a BIG-IP-VM-t a napi leállítás érdekében|

11. Válassza a **Next (tovább** ) lehetőséget: Ha az összes alapértelmezett beállítást elhagyja, válassza a Next (tovább) lehetőséget **: címkék**.

12. Válassza a **Next (tovább): felülvizsgálat + létrehozás** lehetőséget a Big-IP-VM-konfigurációk áttekintéséhez, mielőtt kiválasztja a **Létrehozás** elemet a központi telepítéshez.

13. A BIG-IP virtuális gépek teljes üzembe helyezéséhez szükséges idő általában 5 perc. Ha végzett, ne válassza az **erőforrás** megnyitása lehetőséget, és válassza ki a Azure Portal bal oldali menüjét, és válassza az **erőforráscsoportok** lehetőséget az új Big-IP-VM eléréséhez. Ha a virtuális gép létrehozása sikertelen, válassza a **vissza** és a **tovább** lehetőséget.

## <a name="network-configuration"></a>Hálózati konfiguráció

Amikor a BIG-IP virtuális gép először elindul, a hálózati adaptere az Azure-alhálózat Dynamic Host Configuration Protocol (DHCP) által kiállított **elsődleges** magánhálózati IP-címmel lesz kiépítve, amelyhez csatlakozik. Ezt az IP-címet a BIG-IP Traffic Management operációs rendszere (TMOS) fogja használni a következővel való kommunikációhoz:

- Kommunikáció más gazdagépekkel és szolgáltatásokkal

- Kimenő hozzáférés a nyilvános internethez

- Bejövő hozzáférés a BIG-IPs web config és SSH felügyeleti interfészekhez

Ezen felügyeleti felületek egyike az internetre növeli a BIG-IPs támadási felületet, ezért a BIG-IPs elsődleges IP-cím nem lett kiépítve nyilvános IP-címmel az üzembe helyezés során. Ehelyett a közzétételi szolgáltatások számára a másodlagos belső IP-cím és a hozzá tartozó nyilvános IP-cím lesz kiépítve.
Ez az 1 – 1 hozzárendelés egy virtuális gép nyilvános IP-címe és a magánhálózati IP-cím között lehetővé teszi, hogy a külső forgalom elérje a virtuális gépet. Azonban egy Azure NSG-szabály is szükséges ahhoz, hogy lehetővé váljon a forgalom, ugyanúgy, mint a tűzfal.

Az ábrán egy, az Azure-ban található BIG-IP-cím egyetlen hálózati adapteres telepítése látható, amely az általános működés és felügyelet elsődleges IP-címével, valamint egy külön virtuális kiszolgáló IP-címeivel van konfigurálva a közzétételi szolgáltatások számára.
Ebben a megoldásban egy NSG-szabály lehetővé teszi, hogy a távoli forgalom a `intranet.contoso.com` közzétett szolgáltatás nyilvános IP-címére irányítsa, mielőtt a Big-IP virtuális kiszolgálóra továbbítva lenne.

![A rendszerkép egyetlen NIC-telepítést jelenít ](./media/f5ve-deployment-plan/single-nic-deployment.png) meg alapértelmezés szerint, az Azure-beli virtuális gépek számára kiadott magán-és nyilvános IP-címek mindig dinamikusak, így a virtuális gép minden újraindítása valószínűleg megváltozhat. A BIG-IPs felügyeleti IP-cím statikusra változtatásával és a közzétételi szolgáltatások közzétételéhez használt másodlagos IP-címekkel megegyező módon elkerülheti a váratlan kapcsolódási problémákat.

1. A Big-IP VM menüjéből válassza a **Beállítások**  >  **hálózatkezelés** lehetőséget.

2. A hálózat nézetben válassza a **hálózati adaptertől** jobbra mutató hivatkozást.

![A rendszerkép a hálózati konfigurációt jeleníti meg](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>A rendszer véletlenszerűen hozza létre a virtuális gépek nevét az üzembe helyezés során.

3. A bal oldali ablaktáblán válassza az **IP-konfigurációk** lehetőséget, majd válassza a **ipconfig1** sort.

4. Állítsa a statikus **IP-hozzárendelés** lehetőséget, és ha szükséges, módosítsa a Big-IP virtuális gépek elsődleges IP-címét. Ezután kattintson a **Mentés** gombra, és a ipconfig1 menü bezárásához

>[!NOTE]
>Ezt az elsődleges IP-címet fogja használni a BIG-IP-VM-hez való kapcsolódáshoz és felügyelethez.

5. Válassza a felső menüszalagon a **+ Hozzáadás** elemet, és adjon meg egy másodlagos magánhálózati IP-címet (például ipconfig2).

6. A magánhálózati IP-cím beállításainak **foglalási** beállítását állítsa **statikusra**. Ha a következő magasabb vagy alacsonyabb szintű IP-címet adja meg, a dolgok sorrendjét is megtarthatja.

7. A **hozzárendelni** kívánt nyilvános IP-cím beállítása és a **Létrehozás** kiválasztása

8. Adja meg az új nyilvános IP-cím nevét, például BIG-IP-VM_ipconfig2_Public

9. Ha a rendszer kéri, állítsa be a **SKU** -t a standard értékre

10. Ha a rendszer kéri, állítsa a **szintet** **globális** értékre, és

11. Állítsa a **hozzárendelés** beállítást **statikus** értékre, majd kattintson kétszer **az OK gombra** .

A BIG-IP-VM most már készen áll a beállításra:

- **Elsődleges magánhálózati IP-cím**: a Big-IP-VM a webes konfigurációs segédprogram és az SSH használatával történő felügyeletére szolgál. A BIG-IP rendszer saját **IP** -címeként fogja használni a közzétett háttér-szolgáltatásokhoz való kapcsolódáshoz. Emellett olyan külső szolgáltatásokhoz is csatlakozik, mint például az NTP, az AD és az LDAP.

- **Másodlagos magánhálózati IP-cím**: olyan Big-IP APM virtuális kiszolgáló létrehozásakor használatos, amely a közzétett szolgáltatás (ok) ra irányuló bejövő kérelmeket figyeli.

- **Nyilvános IP-cím**: a másodlagos magánhálózati IP-címhez van társítva, lehetővé teszi a nyilvános internetről érkező ügyfelek forgalmát, hogy elérjék a közzétett szolgáltatás (ok) hoz elérhető Big-IP virtuális kiszolgálót.

A példa a virtuális gépek nyilvános és magánhálózati IP-címei közötti 1 – 1 kapcsolatot szemlélteti. Az Azure-beli virtuálisgép-ADAPTERek csak egy elsődleges IP-címmel rendelkezhetnek, és a további létrehozott IP-címeket mindig másodlagosnak nevezzük.

>[!NOTE]
>A BIG-IP-szolgáltatások közzétételéhez másodlagos IP-hozzárendelésekre van szükség.

![Ez a rendszerkép az összes másodlagos IP-címet megjeleníti](./media/f5ve-deployment-plan/secondary-ips.png)

Ha az SHA-t a BIG-IP- **hozzáférés irányított konfigurációjának** használatával kívánja megvalósítani, ismételje meg a 5-11-as lépéseket további magán-és nyilvános IP-párok létrehozásához minden olyan szolgáltatáshoz, amelyet a Big-IP APM segítségével szeretne közzétenni Ugyanezt a megközelítést is használhatja, ha BIG-IPs **Speciális konfiguráció** használatával tesz közzé szolgáltatásokat. Ebben a forgatókönyvben azonban lehetősége van a nyilvános IP-címek használatának elkerülésére a [kiszolgálónév-jelző (SNI)](https://support.f5.com/csp/#/article/K13452) konfigurációjának használatával. Ebben a konfigurációban a BIG-IP virtuális kiszolgáló fogadja az összes fogadott ügyfél-forgalmat, és továbbítja azt a célhelyre.

## <a name="dns-configuration"></a>DNS-konfiguráció

Elengedhetetlen, hogy az ügyfelek számára a DNS-t úgy konfigurálja, hogy a közzétett SHA-szolgáltatásokat a BIG-IP-VM nyilvános IP-címére oldja fel.

Az alábbi lépések feltételezik, hogy az SHA-szolgáltatásokhoz használt nyilvános tartomány DNS-zónája az Azure-ban van kezelve. A lokátor rekord létrehozásakor azonban ugyanazok a DNS-alapelvek érvényesek, függetlenül attól, hogy a DNS-zóna hogyan legyen kezelve.

1. Ha még nincs megnyitva, bontsa ki a portál bal oldali menüjét, és navigáljon a BIG-IP-VM-re az **erőforráscsoportok** lehetőség használatával

2. A Big-IP VM menüjéből válassza a **Beállítások**  >  **hálózatkezelés** lehetőséget.

3. A BIG-IP-VM hálózatok nézetben válassza ki az első másodlagos IP-címet a legördülő IP-konfiguráció listából, és válassza ki a **hálózati adapter nyilvános IP-** címére mutató hivatkozást.

![Képernyőkép a hálózati adapter nyilvános IP-címének megjelenítéséhez](./media/f5ve-deployment-plan/networking.png)

4. A bal oldali ablaktábla **Beállítások** szakasza alatt válassza a **konfiguráció** lehetőséget a nyilvános IP-cím és a DNS-tulajdonságok menü megnyitásához a kiválasztott másodlagos IP-címhez.

5. Válassza ki és **hozzon létre** egy alias rekordot, és válassza ki a **DNS-zónát** a legördülő listából. Ha egy DNS-zóna még nem létezik, akkor az Azure-on kívül is felügyelhető, vagy létrehozhat egyet az Azure AD-ben ellenőrzött tartományi utótaghoz.

6. Az első DNS-alias rekord létrehozásához használja az alábbi adatokat:

 | Mező | Érték |
 |:-------|:-----------|
 |Előfizetés| Ugyanaz az előfizetés, mint a BIG-IP-VM|
 |DNS-zóna| A közzétett webhelyek által használt ellenőrzött tartomány utótagjának mérvadó DNS-zónája, például www.contoso.com |
 |Name | Az Ön által megadott állomásnév a kiválasztott másodlagos IP-címhez társított nyilvános IP-címhez lesz feloldva. Ügyeljen arra, hogy a megfelelő DNS-t adja meg az IP-megfeleltetésekhez. Lásd: az utolsó rendszerkép a hálózatkezelési konfigurációk szakaszban, például intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL-egységek | Óra |

7. Válassza a **Létrehozás** az Azure-hoz lehetőséget, hogy ezeket a beállításokat a nyilvános DNS-be mentse.

8. Hagyja meg a **DNS-név címkéjét (nem kötelező)** , majd a nyilvános IP-cím bezárása előtt válassza a **Mentés** lehetőséget.

9. Ismételje meg az 1 – 6. lépést, hogy további DNS-rekordokat hozzon létre minden olyan szolgáltatáshoz, amelyet a BIG-IP irányított konfigurációjának használatával szeretne közzétenni.

  A DNS-rekordok a helyén bármely online eszköz, például a [DNS-ellenőrző](https://dnschecker.org/) használatával ellenőrizhetik, hogy egy létrehozott rekord sikeresen propagálva lett-e az összes globális nyilvános DNS-kiszolgálón.

  Ha a DNS-tartomány névterét egy olyan külső szolgáltató használatával felügyeli, mint a [GoDaddy](https://www.godaddy.com/), akkor a saját DNS-felügyeleti létesítményével kell létrehoznia a rekordokat.

>[!NOTE]
>A számítógép helyi gazdagép-fájlját is használhatja, ha a tesztelés és a DNS-rekordok gyakran váltanak át. A Windows rendszerű SZÁMÍTÓGÉPeken található localhost fájlok a Win + R gomb megnyomásával és a Futtatás **mezőbe való** beküldéssel érhetők el. Csak ügyeljen arra, hogy a localhost-rekordok csak a helyi számítógép DNS-feloldását biztosítják, nem más ügyfelek számára.

## <a name="client-traffic"></a>Ügyfél-forgalom

Alapértelmezés szerint az Azure virtuális hálózatok és a társított alhálózatok olyan magánhálózatok, amelyek nem tudnak internetes forgalmat fogadni. A BIG-IP-VM hálózati adapterét csatlakoztatni kell az üzembe helyezés során megadott NSG. Ahhoz, hogy a külső webes forgalom elérje a BIG-IP-VM-et, meg kell határoznia egy bejövő NSG-szabályt, amely engedélyezi a 443 (HTTPS) és a 80 (HTTP) portokat a nyilvános internetről.

1. A BIG-IP virtuális gép fő **áttekintő** menüjében válassza a **hálózatkezelés** lehetőséget.

2. Válassza a Bejövő szabály **hozzáadása** lehetőséget a következő NSG-szabály tulajdonságainak megadásához:

 |     Mező   |   Érték        |
 |:------------|:------------|
 |Forrás| Bármely|
 |Forrásporttartományok| *|
 |Cél IP-címei|A BIG-IP-VM másodlagos magánhálózati IP-címeinek vesszővel tagolt listája|
 |Célportok| 80 443|
 |Protokoll| TCP |
 |Művelet| Engedélyezés|
 |Prioritás|A legalacsonyabb elérhető érték 100 – 4096|
 |Name | Leíró név, például: `BIG-IP-VM_Web_Services_80_443`|

3. Válassza a **Hozzáadás** lehetőséget a módosítások elvégzéséhez, majd a **hálózat** menü bezárásához.

A HTTP-és HTTPS-forgalom bárhonnan elérhető lesz az összes BIG-IP-VM másodlagos csatoló eléréséhez. Az 80-es port engedélyezése hasznos, ha a BIG-IP APM lehetővé teszi a felhasználók számára a HTTP-ről a HTTPS-re való automatikus átirányítást. Ez a szabály módosítható a cél IP-címek hozzáadásához vagy eltávolításához, ha szükséges.

## <a name="manage-big-ip"></a>BIG-IP kezelése

A BIG-IP rendszer a webes konfigurációs felhasználói felületén keresztül lesz felügyelve, amely a következő ajánlott módszerek valamelyikével érhető el:

- A BIG-IP belső hálózatán található gépről

- A BIG-IP-VM belső hálózatához csatlakozó VPN-ügyfélről

- Közzététel az [Azure ad Application Proxyon](./application-proxy-add-on-premises-application.md) keresztül

A többi konfiguráció folytatásához el kell döntenie a legmegfelelőbb módszert. Ha szükséges, közvetlenül kapcsolódhat a webes konfigurációhoz az internetről, ha a BIG-IP elsődleges IP-címét egy nyilvános IP-címmel konfigurálja. Ezután adjon hozzá egy NSG-szabályt, amely engedélyezi az 8443 forgalmat az adott elsődleges IP-címhez. Ügyeljen arra, hogy a forrást a saját megbízható IP-címére korlátozza, ellenkező esetben bárki csatlakozhat.

Ha elkészült, ellenőrizze, hogy tud-e csatlakozni a BIG-IP virtuális gép web config konfigurációjához, és jelentkezzen be a virtuális gép telepítése során megadott hitelesítő adatokkal:

- Ha belső hálózatán vagy VPN-en keresztül csatlakozik egy virtuális gépről, csatlakoztassa közvetlenül a BIG-IPs elsődleges IP-címet és a web config portot. Például: `https://<BIG-IP-VM_Primary_IP:8443`. A böngésző kéri a nem biztonságos kapcsolatok megadását, de figyelmen kívül hagyhatja a kérést, amíg a BIG-IP konfigurálva nem lesz. Ha a böngésző ragaszkodik a hozzáférés blokkolásához, törölje a gyorsítótárat, és próbálkozzon újra.

- Ha az alkalmazás-proxyn keresztül közzétette a webes konfigurációt, akkor a megadott URL-cím használatával a webkonfigurációt külsőleg is elérheti a port hozzáfűzése nélkül, például: `https://big-ip-vm.contoso.com` . A belső URL-címet a web config port használatával kell definiálni, például: `https://big-ip-vm.contoso.com:8443` 

A BIG-IP rendszer a mögöttes SSH-környezettel is kezelhető, amely jellemzően parancssori (CLI) feladatokhoz és a gyökér szintű hozzáféréshez használatos. Több lehetőség is létezik a parancssori felülethez való csatlakozáshoz, beleértve a következőket:

- [Azure Bastion szolgáltatás](../../bastion/bastion-overview.md): gyors és biztonságos csatlakozást tesz lehetővé a vNET belüli bármely virtuális géphez bármely helyről

- Közvetlen kapcsolat egy SSH-ügyféllel, például a JIT-módszer használatával

- Soros konzol: a virtuális gépek menü támogatás és hibaelhárítás szakaszának alján, a portálon érhető el. Nem támogatja a fájlátvitelt.

- A webes konfigurációhoz hasonlóan közvetlenül is csatlakozhat az internetről a CLI-hez úgy, hogy a BIG-IP elsődleges IP-címét egy nyilvános IP-címmel konfigurálja, és egy NSG-szabályt ad hozzá az SSH-forgalom engedélyezéséhez. Ha ezt a módszert használja, ügyeljen arra, hogy a forrást a saját megbízható IP-címére korlátozza.  

## <a name="big-ip-license"></a>BIG-IP-licenc

A BIG-IP rendszernek aktiválva kell lennie, és az APM modullal kell kiépíteni, mielőtt a közzétételi szolgáltatások és az SHA számára konfigurálva lenne.

1. Jelentkezzen be újra a webes konfigurációba, és az **Általános tulajdonságok** lapon válassza az **aktiválás** lehetőséget.

2. Az **alapszintű regisztrációs kulcs** mezőben adja meg az F5 által biztosított kis-és nagybetűket megkülönböztető kulcsot

3. Hagyja meg az **aktiválási módszert** **automatikusra** , és válassza a **tovább** lehetőséget, a Big-IP ellenőrzi a licencet, és megjeleníti a végfelhasználói licencszerződést (EULA).

4. A **folytatáshoz** válassza az **elfogadás** lehetőséget, és várjon, amíg az aktiválás befejeződik.

5. Jelentkezzen be újra, és a licenc összegzése lap alján kattintson a **Tovább gombra**. A BIG-IP mostantól megjeleníti az SHA-hez szükséges különböző funkciókat biztosító modulok listáját. Ha ezt nem látja, a főlapon **lépjen a**  >  **rendszererőforrás kiépítés** elemre.

6. Hozzáférési szabályzat (APM) kiépítési oszlopának keresése

![A képen megjelenik a hozzáférés kiépítés](./media/f5ve-deployment-plan/access-provisioning.png)

7. Válassza a **Küldés** lehetőséget, és fogadja el a figyelmeztetést

8. Legyen türelemmel, és várjon, amíg a BIG-IP befejeződik az új funkciók megvilágítása. A teljes inicializálás előtt többször is ciklusba kerülhet. 

9. Ha elkészült, válassza a **Folytatás** lehetőséget, majd a **Névjegy** lapon válassza **a telepítési segédprogram futtatása** lehetőséget.

>[!IMPORTANT]
>Az F5-es licencek csak egyetlen BIG-IP VE-példány általi felhasználásra vannak korlátozva egyszerre. Előfordulhat, hogy az egyik példányból a másikba kívánja áttelepíteni a licencet, és ha így tesz, az aktív példányon [vissza kell vonnia](https://support.f5.com/csp/article/K41458656) a próbaverziós licencét, mielőtt leszerelik, ellenkező esetben a licenc véglegesen el fog veszni.

## <a name="provision-big-ip"></a>BIG-IP kiépítése

A BIG-IPs web config-re irányuló és onnan érkező felügyeleti forgalom védelme rendkívül fontos. Konfiguráljon egy eszköz-felügyeleti tanúsítványt a webes konfigurációs csatorna biztonságának védelméhez.

1. A bal oldali navigációs sávon **válassza a**  >  **rendszertanúsítvány-kezelés**  >  **forgalmi tanúsítványkezelő**  >  **SSL-tanúsítvány lista**  >  **Importálás** lehetőséget.

2. Az **Importálás típusa** legördülő listából válassza a **PKCS 12 (IIS)** lehetőséget, és **válassza a fájl** elemet. Keressen egy olyan SSL-webtanúsítványt, amelynek a tulajdonos neve vagy a SAN, amely az FQDN-t fogja tartalmazni, a BIG-IP-VM hozzárendelését a következő néhány lépésben

3. Adja meg a tanúsítványhoz tartozó jelszót, majd válassza az **Importálás** lehetőséget.

4. A bal oldali navigációs sávon **lépjen a**  >  **rendszerplatform** elemre.

5. Konfigurálja a BIG-IP-VM-et egy teljesen minősített állomásnévvel és a környezetének időzónáját, majd a **frissítés** után

![A képen az általános tulajdonságok láthatók](./media/f5ve-deployment-plan/general-properties.png)

6. A bal oldali navigációs sávon **lépjen a**  >  **rendszerkonfiguráció**  >  **eszköz**  >  **NTP** elemre.

7. Adjon meg egy megbízható NTP-forrást, és válassza a **Hozzáadás**, majd a **frissítés** elemet. Például: `time.windows.com`

Most egy DNS-rekordra van szüksége az előző lépésekben megadott BIG-IPs teljes tartománynév feloldásához az elsődleges magánhálózati IP-címhez. Egy rekordot fel kell venni a környezet belső DNS-fiókjába vagy egy olyan számítógép localhost fájljába, amely a BIG-IP webes konfigurációjához való kapcsolódáshoz lesz használva. Mindkét esetben a böngésző figyelmeztetése már nem jelenik meg, amikor közvetlenül csatlakozik a webes konfigurációhoz. Ez nem az alkalmazásproxy vagy más fordított proxy használatával történik.

## <a name="ssl-profile"></a>SSL-profil

Fordított proxyként a BIG-IP rendszer egyszerű továbbítási szolgáltatásként működhet, más néven transzparens proxyként, vagy egy teljes proxy, amely aktívan részt vesz az ügyfelek és a kiszolgálók közötti adatcsere során.
A teljes proxy két különálló kapcsolatot hoz létre: egy előtéri TCP-ügyfélkapcsolatot, valamint egy különálló háttérbeli TCP-kiszolgáló kapcsolatát, és a közepén lévő enyhe hézagot. Az ügyfelek az egyik végponton, más néven **virtuális kiszolgálón** keresztül csatlakoznak a proxy-figyelőhöz, a proxy pedig különálló, független kapcsolatot létesít a háttér-kiszolgálóval. Ez kétirányú mindkét oldalon.
Ebben a teljes proxy módban az F5 BIG-IP rendszer képes a forgalom vizsgálatára, ezért a kérelmekkel és a válaszokkal való interakció is lehetséges. Ezen funkció alapján bizonyos funkciók, például a terheléselosztás és a webes teljesítmény optimalizálása, valamint a fejlettebb forgalomirányítási szolgáltatások, például az alkalmazási réteg biztonsága, a webes gyorsítás, az oldal-Útválasztás és a biztonságos távoli hozzáférés.
Az SSL-alapú szolgáltatások közzétételekor az ügyfelek és a háttérbeli szolgáltatások közötti adatforgalom visszafejtésének és titkosításának folyamatát a BIG IP SSL profilok kezelik.

Két típusú profil létezik:

- **Ügyfél SSL**: az SSL-t használó belső szolgáltatások közzétételének leggyakoribb módja egy ügyfél SSL-profiljának létrehozása. Az ügyfél SSL-profiljaival a BIG-IP rendszer visszafejtheti a bejövő ügyfelek kéréseit, mielőtt elküldené őket egy alsóbb rétegbeli szolgáltatásba. Ezután titkosítja a kimenő háttérbeli válaszokat, mielőtt elküldené őket az ügyfeleknek.

- **Kiszolgáló SSL**: a https-hez konfigurált háttér-szolgáltatások esetében a Big-IP protokollt a kiszolgáló SSL-profiljának használatára állíthatja be. A kiszolgálói SSL-profillal a BIG-IP újra titkosítja az ügyfél kérését, mielőtt elküldené a cél háttér-szolgáltatásba. Ha a kiszolgáló titkosított választ ad vissza, a BIG-IP rendszer visszafejti és újra titkosítja a választ, mielőtt elküldené az ügyfélnek a konfigurált ügyfél SSL-profilján keresztül.

Az ügyfél-és kiszolgálói SSL-profilok kiépítésével a BIG-IP előre konfigurálva lesz, és készen áll az összes SHA-forgatókönyvre.

1. A bal oldali navigációs sávon **válassza a**  >  **rendszertanúsítvány-kezelés**  >  **forgalmi tanúsítványkezelő**  >  **SSL-tanúsítvány lista**  >  **Importálás** lehetőséget.

2. Az **Importálás típusa** legördülő listából válassza a **PKCS 12 (IIS) lehetőséget.**

3. Adja meg az importált tanúsítvány nevét, például:  `ContosoWilcardCert`

4. Válassza a **fájl kiválasztása** lehetőséget, hogy megkeresse az SSL webes tanúsítvány tulajdonosának nevét, amely a közzétett szolgáltatásokhoz használt tartományi utótagnak felel meg

5. Adja meg az importált tanúsítvány **jelszavát** , majd válassza az **Importálás** lehetőséget.

6. A bal oldali navigációs sávon lépjen a **helyi forgalmi**  >  **profilok**  >  **SSL**  >  -**ügyfél** elemre, majd válassza a **Létrehozás** lehetőséget.

7. Az **új ügyfél SSL-profilja** lapon adja meg az új ügyfél SSL-profiljának egyedi rövid nevét, és győződjön meg arról, hogy a szülő profil **clientssl** van beállítva.

![A képen a Big-IP frissítése látható](./media/f5ve-deployment-plan/client-ssl.png)

8. Jelölje be a jobb oldali jelölőnégyzetet a **tanúsítvány kulcstartójának** sorában, és válassza a **Hozzáadás** lehetőséget.

9. A három legördülő listából válassza ki a jelszó nélkül importált helyettesítő karaktert, majd válassza a **Hozzáadás**  >  **kész** lehetőséget.

![A képen a Big-IP contoso helyettesítő karakterének frissítése látható](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Az **SSL-kiszolgálói tanúsítvány profiljának** létrehozásához ismételje meg a 6-9. lépést. A felső menüszalagon válassza az **SSL**-  >  **kiszolgáló**  >  **létrehozása** lehetőséget.

11. Az **új kiszolgáló SSL-profilja** lapon adja meg az új kiszolgáló SSL-profiljának egyedi rövid nevét, és győződjön meg arról, hogy a szülő profil **serverssl** van beállítva.

12. Jelölje be a tanúsítványhoz és a kulcshoz tartozó sorok szélsőjobboldali jelölőnégyzetét, majd a legördülő listából válassza ki az importált tanúsítványt, majd fejezze be a **befejezést**.

![A képen a Big-IP-kiszolgáló összes profiljának frissítése látható](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Ne essen kétségbe, ha nem tud SSL-tanúsítványt beszerezni, használhatja az integrált önaláírt BIG-IP-kiszolgáló és az ügyfél SSL-tanúsítványait. A böngészőben csak egy tanúsítvány jelenik meg.

A BIG-IP for SHA előkészítésének egyik utolsó lépése annak biztosítása, hogy képes legyen megkeresni az erőforrások közzétételét, valamint azt a címtárszolgáltatás-szolgáltatást, amely az egyszeri bejelentkezéshez is támaszkodik. A BIG-IP két névfeloldási forrással rendelkezik, kezdve a helyi/.../hosts fájllal, vagy ha a rekord nem található meg a BIG-IP rendszer által használt DNS-szolgáltatástól függetlenül. A hosts file metódus nem vonatkozik a teljes tartománynevet használó APM-csomópontokra és-készletekre.

1. A webes konfiguráció területen **lépjen a**  >  **rendszerkonfiguráció**  >  **eszköz**  >  **DNS** elemre.

2. A **DNS-keresési kiszolgáló listában** adja meg a környezetek DNS-KISZOLGÁLÓJÁNAK IP-címét.

3. Válassza a  >  **frissítés** hozzáadása elemet.

Különálló és választható lépésként érdemes lehet egy [LDAP-konfigurációt](https://somoit.net/f5-big-ip/authentication-using-active-directory) használni a Big-IP-rendszergazdák hitelesítésére az ad-ben a helyi Big-IP-fiókok kezelése helyett.

## <a name="update-big-ip"></a>BIG-IP frissítése

A BIG-IP-VM-et frissíteni kell a [forgatókönyv-alapú útmutatóban](f5-aad-integration.md)szereplő összes új funkció zárolásának feloldásához. Az egérmutatót a Főoldal bal felső részén található BIG-IPs állomásnév fölé helyezve a System TMOS. Azt javasoljuk, hogy a v15. x vagy újabb verzióra futtassa az [F5 letöltést](https://downloads.f5.com/esd/productlines.jsp). A fő rendszeroperációs rendszer (TMOS) frissítéséhez használja az [útmutatót](https://support.f5.com/csp/article/K34745165) .

Ha a fő TMOS frissítése nem lehetséges, akkor az alábbi lépések végrehajtásával vegye figyelembe, hogy az irányított konfigurációt csak a lehető legkevesebbre frissíti.

1. A Big-IP webes konfiguráció főoldaláról nyissa meg a **hozzáférés**  >  **irányított konfigurációt**

2. Az **irányított konfiguráció** lapon válassza az **irányított konfiguráció frissítése** elemet.

![A képen a Big-IP frissítése látható](./media/f5ve-deployment-plan/update-big-ip.png)

3. A **frissítés irányított konfiguráció** párbeszédpanelen válassza a **Fájl elemet** a letöltött használati eset csomag feltöltéséhez, majd válassza a **feltöltés és telepítés** lehetőséget.

4. Ha a frissítés befejeződött, válassza a **Folytatás** lehetőséget.

## <a name="backup-big-ip"></a>BIG-IP biztonsági mentés

Ha a BIG-IP rendszer már teljesen kiépítve van, javasoljuk, hogy készítsen teljes biztonsági mentést a konfigurációról:

1. Ugrás a   >  **rendszerarchívumok**  >  **létrehozásához**

2. Adjon meg egyedi **fájlnevet** , és engedélyezze a **titkosítást** egy hozzáférési kóddal

3. Állítsa be a **titkos kulcsok** lehetőséget, hogy az **tartalmazza** az eszköz és az SSL-tanúsítványok biztonsági mentését is

4. Válassza a **kész** lehetőséget, és várja meg, amíg a folyamat befejeződik

5. A biztonsági mentési eredmény állapotot biztosító műveleti állapot jelenik meg. Válassza az **OK** gombot.

6. Mentse helyileg a felhasználói konfigurációs készlet (FKR) archívumát a biztonsági mentés hivatkozásának kiválasztásával, és válassza a **Letöltés** lehetőséget.

Választható lépésként a teljes rendszerlemez biztonsági mentését is elvégezheti az Azure- [Pillanatképek](../../virtual-machines/windows/snapshot-copy-managed-disk.md)használatával, amely a webkonfiguráció biztonsági mentésével ELLENTÉTBEN a TMOS-verziók közötti tesztelésre, vagy egy friss rendszerre való visszagörgetésre is lehetőséget nyújt.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>BIG-IP visszaállítása

A BIG-IP visszaállítása hasonló eljárást követ a biztonsági mentéshez, és a nagy IP-alapú virtuális gépek közötti konfigurációk áttelepítésére is használható. A biztonsági másolat importálása előtt meg kell figyelni a támogatott frissítési útvonalakkal kapcsolatos adatokat.

1. Ugrás a   >  **rendszerarchívumok** szolgáltatásra

2. Vagy válassza ki a visszaállítani kívánt biztonsági másolat hivatkozását, vagy válassza a **feltöltés** gombot egy korábban mentett FKR-Archívum megkereséséhez, amely nem szerepel a listában

3. Adja meg a biztonsági mentéshez használt jelszót, és válassza a **visszaállítás** lehetőséget.

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Az írás időpontjában a AzVmSnapshot parancsmag a legutóbbi pillanatkép visszaállítására korlátozódik a dátum alapján. A pillanatképek tárolása a virtuális gép erőforráscsoport gyökerében történik. Ügyeljen arra, hogy a pillanatképek visszaállítása újraindítja az Azure-beli virtuális gépeket, így körültekintően járjon el.

## <a name="additional-resources"></a>További források

-   [BIG-IP VE-jelszó alaphelyzetbe állítása az Azure-ban](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [A jelszó alaphelyzetbe állítása a portál használata nélkül](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [A BIG-IP VE-felügyelethez használt hálózati adapter módosítása](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Az egyes hálózati adapterek konfigurációjának útvonalai](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Következő lépések

Válasszon egy [üzembe helyezési forgatókönyvet](f5-aad-integration.md) , és indítsa el a megvalósítást.