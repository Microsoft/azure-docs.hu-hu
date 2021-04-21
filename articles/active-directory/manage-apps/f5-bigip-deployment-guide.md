---
title: Azure AD biztonságos hibrid hozzáférés F5-ös üzembe helyezési útmutatóval | Microsoft Docs
description: 'Oktatóanyag: F5 BIG-IP Virtual Edition (VE) virtuális gép üzembe helyezése az Azure IaaS-ben a biztonságos hibrid hozzáférés érdekében'
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783322"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Oktatóanyag: F5 BIG-IP Virtual Edition virtuális gép üzembe helyezése az Azure IaaS-ben a biztonságos hibrid hozzáférés érdekében

Ez az oktatóanyag végigvezeti a BIG-IP Vitural Edition (VE) Azure IaaS-beli üzembe helyezésének teljes folyamatán. Az oktatóanyag végére a következőt kell látnia:

- Egy teljesen előkészített BIG-IP virtuális gép (VM) a biztonságos hibrid hozzáférés (SHA) koncepció igazolásának modellezéséhez

- Előkészítési példány új BIG-IP rendszerfrissítések és gyorsjavítások teszteléséhez

## <a name="prerequisites"></a>Előfeltételek

Az F5 BIG-IP korábbi ismerete vagy ismerete nem szükséges, de javasoljuk, hogy ismerkedjen meg az [F5 BIG-IP terminológiával.](https://www.f5.com/services/resources/glossary) A BIG-IP üzembe helyezéséhez az Azure-ban az SHA használatához a következő szükséges:

- Fizetős Azure-előfizetés vagy ingyenes, 12 hónapos [próbaverziós előfizetés.](https://azure.microsoft.com/free/)

- Az alábbi F5 BIG-IP licenc-termékkódok bármelyike

  - F5 BIG-IP® legjobb csomag

  - F5 BIG-IP Access Policy Manager™ (APM) önálló licenc

  - F5 BIG-IP Access Policy Manager™ (APM) bővítménylicenc meglévő BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90 napos BIG-IP teljes funkció [próbaverziós licence.](https://www.f5.com/trial/big-ip-trial.php)

- Helyettesítő vagy tulajdonos alternatív neve (SAN) tanúsítvány, amely webalkalmazásokat tesz közzé a Secure Socket Layer (SSL) használatával. [A let's encrypt](https://letsencrypt.org/) ingyenes 90 napos tanúsítványt biztosít a teszteléshez.

- Ssl-tanúsítvány a felügyeleti felület BIG-IPs biztosításához. A webalkalmazások közzétételéhez használt tanúsítvány használható, ha a tárgya a BIG-IP teljes tartománynevének (FQDN) felel meg. Egy *.contoso.com tulajdonossal definiált helyettesítő karakteres tanúsítvány például alkalmas a következőre: `https://big-ip-vm.contoso.com:8443`

A virtuális gépek üzembe helyezése és az alaprendszer-konfigurációk körülbelül 30 percet is igénybe venek, így a BIG-IP platform készen áll az itt felsorolt SHA-forgatókönyvek bármelyikének [végrehajtására.](f5-aad-integration.md)

A forgatókönyvek teszteléséhez ez az oktatóanyag feltételezi, hogy a BIG-IP egy azure-beli erőforráscsoportban lesz üzembe helyezni, amely egy Active Directory (AD) környezetet tartalmaz. A környezetnek egy tartományvezérlőből (DC) és egy webgazda (IIS) virtuális gépből kell állnia. Az is rendben van, ha ezek a kiszolgálók más helyeken vannak a BIG-IP virtuális géphez, feltéve, hogy a BIG-IP rálátást biztosít az adott forgatókönyv támogatásához szükséges szerepkörökre. Azok a forgatókönyvek is támogatottak, amelyekben a BIG-IP virtuális gép VPN-kapcsolaton keresztül csatlakozik egy másik környezethez.

Ha nincsenek az itt említett tesztelési elemek, üzembe helyezhet egy teljes AD tartományi környezetet az Azure-ban a szkript [használatával.](https://github.com/Rainier-MSFT/Cloud_Identity_Lab) A parancsfájlokkal futtatott automatizálással a minta tesztalkalmazások gyűjteménye programozott módon is üzembe helyezhető egy [IIS-webkiszolgálón.](https://github.com/jeevanbisht/DemoSuite)

>[!NOTE]
>A [Azure Portal](https://portal.azure.com/#home) folyamatosan fejlődik, így az oktatóanyag egyes lépései eltérhetnek a tényleges elrendezéstől, amely a Azure Portal.

## <a name="azure-deployment"></a>Azure-beli üzembe helyezés

A BIG-IP különböző topológiákban helyezhető üzembe. Ez az útmutató egyetlen hálózati adapter (NIC) üzembe helyezésére összpontosít. Ha azonban a BIG-IP üzemelő példány több hálózati adaptert igényel a magas rendelkezésre álláshoz, a hálózati elkülönítéshez vagy az 1 GB-nál nagyobb átviteli sebességhez, fontolja meg az F5 előre lefordított [Azure Resource Manager-sablonjainak (ARM) használatával.](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)

A BIG-IP VE telepítéséhez a következő feladatokat kell elvégeznie [a Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps)

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/#home) egy olyan fiókkal, amely rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel. Például: Közreműködő

2. A felső menüszalag keresőmezőbe írja be a **marketplace**, majd az **Enter lehetőséget.**

3. Írja **be az F5 billentyűt** a Marketplace szűrőbe, majd az Enter **billentyűt**

4. A **felső menüszalagon válassza** a + Hozzáadás lehetőséget, írja be az **F5 billentyűt** a piactér szűrőjébe, majd kattintson az Enter **gombra.**

5. Válassza **az F5 BIG-IP Virtual Edition (BYOL) lehetőséget Válasszon** ki egy  >    >  **szoftvercsomagot F5 BIG-IP VE - ALL (BYOL, 2 rendszerindítási hely)**

6. Válassza a **Létrehozás** lehetőséget.

![A képen a szoftvercsomag kiválasztásának lépései láthatóak](./media/f5ve-deployment-plan/software-plan.png)

7. Lépjen végig az **Alapvető beállítások menün,** és használja az alábbi beállításokat

 |  Projekt részletei     |  Érték     |
 |:-------|:--------|
 |Előfizetés|Cél-előfizetés a BIG-IP virtuális gép üzembe helyezéséhez|
 |Erőforráscsoport | Meglévő Azure-erőforráscsoport a BIG-IP virtuális gép üzembe helyezése vagy létrehozása. A tartományvezérlő és az IIS virtuális gépek erőforráscsoportja legyen|
 | **Példány adatai**|  |
 |Virtuális gép neve| BIG-IP-VM példa |
 |Region | Cél Azure Geo BIG-IP-VM-hez |
 |Rendelkezésre állási beállítások| Csak akkor engedélyezze, ha virtuális gépet használ éles környezetben|
 |Kép| F5 BIG-IP VE – ALL (BYOL, 2 rendszerindítási hely)|
 |Azure Spot-példány| Nem, de nyugodtan engedélyezheti, ha szükséges. |
 |Méret| A minimális specifikációnak 2 vCPU-nak és 8 Gb memóriának kell lennie|
 |**Rendszergazdai fiók**|  |
 |Hitelesítéstípus|Most válassza ki a jelszót. Később kulcspárra válthat |
 |Felhasználónév|Az identitás, amely BIG-IP helyi fiókként lesz létrehozva a felügyeleti felületeinek eléréséhez. A felhasználónév megkülönbözteti a KIS- és nagybetűket.|
 |Jelszó|Rendszergazdai hozzáférés biztonságossáése erős jelszóval|
 |**Bejövőport-szabályok**|  |
 |Nyilvános bejövő portok|Nincs|

8. Válassza **a Tovább: Lemezek lehetőséget** az összes alapértelmezett érték meghagyása mellett, majd válassza a **Tovább: Hálózatkezelés lehetőséget.**

9. A **Hálózat menüben** töltse ki ezeket a beállításokat.

 |Hálózati adapter|      Érték |
 |:--------------|:----------------|
 |Virtuális hálózat|A tartományvezérlő és az IIS virtuális gépek által használt Azure-beli virtuális hálózat, vagy hozzon létre egyet|
 |Alhálózat| Ugyanaz az Azure belső alhálózat, mint a tartományvezérlő és az IIS virtuális gépek, vagy hozzon létre egyet|
 |Nyilvános IP-cím |  Nincs|
 |Hálózati adapter hálózati biztonsági csoportja| Válassza a Nincs lehetőséget, ha az előző lépésekben kiválasztott Azure-alhálózat már társítva van egy hálózati biztonsági csoporthoz (NSG-hez); egyéb esetben válassza az Alapszintű lehetőséget|
 |A hálózatépítés felgyorsítása| Ki |
 |**Terheléselosztás**|     |
 |Virtuális gép terheléselosztása| No|

10. Válassza **a Tovább: Kezelés lehetőséget,** és adja meg ezeket a beállításokat.

 |Figyelés|    Érték |
 |:---------|:-----|
 |Részletes figyelés| Ki|
 |Rendszerindítási diagnosztika|Engedélyezés egyéni tárfiókkal. Lehetővé teszi a BIG-IP Secure Shell (SSH) felülethez való csatlakozást a konzol soros konzolján keresztül a Azure Portal. Válassza ki az elérhető Azure Storage-fiókokat|
 |**Identitás**|  |
 |Rendszer által hozzárendelt felügyelt identitás|Ki|
 |Azure Active Directory|A BIG-IP jelenleg nem támogatja ezt a lehetőséget|
 |**Automatikus leállítás**|    |
 |Automatikus leállítás engedélyezése| Tesztelés esetén érdemes lehet naponta leállítani a BIG-IP-VM virtuális gépet|

11. Válassza **a Tovább: Speciális lehetőséget** az összes alapértelmezett érték meghagyása mellett, majd válassza a **Tovább: Címkék lehetőséget.**

12. Válassza **a Tovább: Áttekintés + létrehozás** lehetőséget a BIG-IP-VM konfigurációk áttekintéséhez, mielőtt a Létrehozás gombra választanának az üzembe helyezéshez. 

13. A BIG-IP virtuális gépek teljes üzembe helyezésének ideje általában 5 perc. Ha elkészült, ne válassza az Erőforrás megnyitása **lehetőséget,** hanem bontsa ki  a Azure Portal bal oldali menüjét, és válassza az Erőforráscsoportok lehetőséget az új BIG-IP-VM-hez való navigáláshoz. Ha a virtuális gép létrehozása sikertelen, válassza a **Vissza és** a **Tovább lehetőséget.**

## <a name="network-configuration"></a>Hálózati konfiguráció

Amikor a BIG-IP virtuális gép először elindul, **a** hálózati adaptere egy elsődleges magánhálózati IP-címmel lesz kiépítve, amelyet annak az Azure-alhálózatnak az Dynamic Host Configuration Protocol (DHCP) szolgáltatása ad ki, amelyhez csatlakozik. Ezt az IP-címet a BIG-IP Traffic Management operációs rendszere (TMOS) használja a következővel való kommunikációhoz:

- Kommunikáció más gazdagépekkel és szolgáltatásokkal

- Kimenő hozzáférés a nyilvános internethez

- Bejövő hozzáférés az BIG-IPs webes konfigurációs és SSH felügyeleti felületéhez

Ezen felügyeleti felületek internetes elérhetővé tevésével nő az BIG-IPs támadási felülete, ezért az BIG-IPs elsődleges IP-címe nem lett kiépítve nyilvános IP-címmel az üzembe helyezés során. Ehelyett egy másodlagos belső IP-cím és a társított nyilvános IP-cím lesz kiépítve a közzétételi szolgáltatásokhoz.
Ez a virtuális gép nyilvános IP-címe és privát IP-címe közötti 1–1. leképezés lehetővé teszi, hogy a külső forgalom elérje a virtuális gépet. Azonban egy Azure NSG-szabályra is szükség van a forgalom engedélyezése érdekében, ugyanúgy, mint a tűzfalon.

Az ábrán egy BIG-IP VE üzemelő példánya látható az Azure-ban, amely egy elsődleges IP-címmel van konfigurálva az általános műveletekhez és felügyelethez, valamint egy különálló virtuális kiszolgálói IP-címmel a közzétételi szolgáltatásokhoz.
Ebben az elrendezésben egy NSG-szabály lehetővé teszi a távoli forgalmat, amely a közzétett szolgáltatás nyilvános IP-címére irányít, mielőtt tovább lenne továbbítva a `intranet.contoso.com` BIG-IP virtuális kiszolgálóra.

![A képen az egyetlen hálózati adapter üzembe helyezése látható Alapértelmezés szerint az Azure-beli virtuális gépekhez kiadott privát és nyilvános IP-k mindig dinamikusak, ezért a virtuális gépek minden újraindításakor valószínűleg ](./media/f5ve-deployment-plan/single-nic-deployment.png) változni fognak. Az előre nem látható csatlakozási problémák elkerülése érdekében BIG-IPs felügyeleti IP-címet statikusra, és tegye meg ugyanezt a szolgáltatások közzétételéhez használt másodlagos IP-címekkel.

1. A BIG-IP virtuális gép menüjében válassza a Beállítások  >  **Hálózat**

2. A hálózatnézetben válassza a Hálózati adapter jobb **oldalon található hivatkozást**

![A képen a hálózati konfiguráció látható](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>A virtuális gépek nevei véletlenszerűen jönnek létre az üzembe helyezés során.

3. A bal oldali panelen  válassza az IP-konfigurációk, majd az **ipconfig1 sor lehetőséget.**

4. Állítsa az **IP-hozzárendelési** beállítást statikusra, és szükség esetén módosítsa a BIG-IP virtuális gépek elsődleges IP-címét. Ezután válassza **a Mentés lehetőséget,** és zárja be az ipconfig1 menüt

>[!NOTE]
>Ezt az elsődleges IP-címet fogja használni a BIG-IP-VM csatlakoztatáshoz és kezeléséhez.

5. A **felső menüszalagon válassza** a + Hozzáadás lehetőséget, és adjon nevet egy másodlagos magánhálózati IP-címnek, például: ipconfig2

6. Állítsa a **magánhálózati** IP-cím beállításainak Kiosztás beállítását **Statikusra.** A következő magasabb vagy alacsonyabb egymást követő IP-cím biztosítása segít a sorrendben tartani a dolgokat.

7. A Nyilvános IP-cím mezőben adja meg a **Társítás lehetőséget,** majd válassza a **Létrehozás lehetőséget.**

8. Adjon nevet az új nyilvános IP-címnek, például BIG-IP-VM_ipconfig2_Public

9. Ha a rendszer kéri, állítsa a **termékváltozatot** Standardra

10. Ha a rendszer kéri, állítsa **a réteget** a **Következőre: Globális** és

11. Állítsa a **Hozzárendelés beállítást** **Statikusra,** majd kattintson kétszer az **OK** gombra.

A BIG-IP-VM most már készen áll a beállításra a következővel:

- **Elsődleges magánhálózati** IP-cím: A BIG-IP-VM webes konfigurációs segédprogrammal és SSH-n keresztüli kezelésére van kivatva. A BIG-IP rendszer ezt használja ön **IP-címként** a közzétett háttérszolgáltatásokhoz való csatlakozáshoz. Emellett olyan külső szolgáltatásokhoz is csatlakozik, mint az NTP, az AD és az LDAP.

- **Másodlagos magánhálózati IP-cím:** BIG-IP APM virtuális kiszolgáló létrehozásakor használatos, amely a közzétett szolgáltatások bejövő kérését figyeli.

- **Nyilvános IP-cím:** A másodlagos magánhálózati IP-címhez társítva lehetővé teszi, hogy a nyilvános internetről származó ügyfélforgalom elérje a közzétett szolgáltatás(ok) BIG-IP virtuális kiszolgálóját

A példa egy virtuális gép nyilvános és privát IP-k közötti 1–1 kapcsolatát mutatja be. Az Azure-beli virtuális gép hálózati adaptere csak egy elsődleges IP-címmel lehet, és minden további létrehozott IP-címet mindig másodlagosnak nevezzük.

>[!NOTE]
>A BIG-IP-szolgáltatások közzétételéhez szüksége lesz a másodlagos IP-leképezésekre.

![Ez a kép az összes másodlagos IP-t megjeleníti](./media/f5ve-deployment-plan/secondary-ips.png)

Ha az SHA-t a BIG-IP **access** irányított konfigurációval valósítja meg, ismételje meg az 5–11. lépést, hogy további privát és nyilvános IP-párokat hozzon létre minden további, a BIG-IP APM-en keresztül közzétenni tervezett szolgáltatáshoz. Ugyanez a módszer akkor is használható, ha a speciális konfigurációt használó BIG-IPs **közzé.** Ebben a forgatókönyvben azonban lehetősége van elkerülni a nyilvános IP-címekkel kapcsolatos többletterhelést egy [SNI-konfiguráció](https://support.f5.com/csp/#/article/K13452) használatával. Ebben a konfigurációban a BIG-IP virtuális kiszolgáló fogadja az összes kapott ügyfélforgalmat, és továbbirányítja azt a célhelyre.

## <a name="dns-configuration"></a>DNS-konfiguráció

Fontos, hogy a DNS konfigurálva legyen az ügyfelek számára a közzétett SHA-szolgáltatások a BIG-IP-VM nyilvános IP-cím(i)re való feloldásához.

A következő lépések feltételezik, hogy az SHA-szolgáltatásokhoz használt nyilvános tartomány DNS-zónáját az Azure kezeli. A lokátorrekordok létrehozásának DNS-alapelvei azonban továbbra is érvényesek, függetlenül attól, hogy a DNS-zónát hol kezeli a rendszer.

1. Ha még nincs megnyitva, bontsa ki a portál bal oldali menüjét, és lépjen a BIG-IP-VM virtuális gépre az **Erőforráscsoportok lehetőséggel.**

2. A BIG-IP virtuális gép menüjében válassza a **Beállítások**  >  **Hálózat**

3. A BIG-IP-VMs hálózatnézetben válassza ki az első másodlagos IP-címet a legördülő IP-konfiguráció listából, és válassza ki a hálózati adapter nyilvános IP-címére **mutató hivatkozást**

![A hálózati adapter nyilvános IP-címét bemutató képernyőkép](./media/f5ve-deployment-plan/networking.png)

4. A bal **oldali panel** Beállítások szakasza  alatt válassza a Konfiguráció lehetőséget a kiválasztott másodlagos IP-cím nyilvános IP- és DNS-tulajdonságainak menüjének a kiválasztásához.

5. Válassza ki **és hozza létre** az aliasrekordot, majd válassza ki a **DNS-zónát** a legördülő listából. Ha egy DNS-zóna még nem létezik, akkor az Azure-n kívül is kezelhető, vagy létrehozhat egyet az Azure AD-ben ellenőrzött tartomány-utótaghoz.

6. Az első DNS-aliasrekord létrehozásához használja a következő adatokat:

 | Mező | Érték |
 |:-------|:-----------|
 |Előfizetés| Ugyanaz az előfizetés, mint a BIG-IP-VM|
 |DNS-zóna| A közzétett webhelyek által az ellenőrzött tartomány utótagja mérvadó DNS-zónát fog használni, például a www.contoso.com |
 |Name | A megadott állomásnév a kiválasztott másodlagos IP-címhez társított nyilvános IP-cím lesz feloldva. Győződjön meg arról, hogy a megfelelő DNS-ről IP-címleképezésre van meghatározva. Lásd az utolsó képet a Hálózati konfigurációk szakaszban, például intranet.contoso.com > 13.77.148.215.|
 | TTL | 1 |
 |TTL-egységek | Óra |

7. A **beállítások nyilvános** DNS-be mentéséhez válassza a Létrehozás az Azure-hoz lehetőséget.

8. Hagyja meg **a DNS-név címkét (nem kötelező),** és a Nyilvános IP-cím **menü** bezárása előtt válassza a Mentés lehetőséget.

9. Ismételje meg az 1–6. lépést, és hozzon létre további DNS-rekordokat minden olyan szolgáltatáshoz, amit közzé fog tenni a BIG-IP interaktív konfigurációjának használatával.

  A DNS-rekordok létrehozása után bármelyik online eszközt, [](https://dnschecker.org/) például a DNS-ellenőrzőt használhatja annak ellenőrzésére, hogy a létrehozott rekord sikeresen propagálva lett-e az összes globális nyilvános DNS-kiszolgálón.

  Ha DNS-tartománynévterét egy külső szolgáltató, például a [GoDaddy](https://www.godaddy.com/)használatával kezeli, akkor rekordokat kell létrehoznia a saját DNS-kezelési létesítményük használatával.

>[!NOTE]
>A számítógép helyi gazdagépfájlját is használhatja a DNS-rekordok tesztelése és gyakori váltása során. A Windows rendszerű számítógépen található localhosts fájl a Win + R billentyűkombináció lenyomásával érhető el, és a futtatási mezőben el kell küldje az illesztőprogramok szót.  Ne feledje, hogy a localhost rekord csak a helyi számítógép DNS-megoldását biztosítja, más ügyfeleket nem.

## <a name="client-traffic"></a>Ügyfélforgalom

Alapértelmezés szerint az Azure-beli virtuális hálózatok és a társított alhálózatok olyan magánhálózatok, amelyek nem képesek fogadni az internetes forgalmat. A BIG-IP-VM hálózati adapterét az üzembe helyezés során megadott NSG-hez kell csatolni. Ahhoz, hogy a külső webes forgalom elérje a BIG-IP-VM virtuális gépet, meg kell határoznia egy bejövő NSG-szabályt, amely engedélyezi a nyilvános internetről a 443-as (HTTPS) és a 80-as (HTTP) portokat.

1. A BIG-IP virtuális gép fő Áttekintés **menüjében** válassza a **Hálózat lehetőséget**

2. Válassza **a Bejövő** szabály hozzáadása lehetőséget a következő NSG-szabálytulajdonságok hozzáadásához:

 |     Mező   |   Érték        |
 |:------------|:------------|
 |Forrás| Bármely|
 |Forrásporttartományok| *|
 |Cél IP-címek|A BIG-IP-VM másodlagos magánhálózati IP-címek vesszővel elválasztott listája|
 |Célportok| 80 443|
 |Protokoll| TCP |
 |Művelet| Engedélyezés|
 |Prioritás|A legalacsonyabb elérhető érték 100 és 4096 között|
 |Name | Leíró név, például: `BIG-IP-VM_Web_Services_80_443`|

3. A **módosítások véglegesítéshez** válassza a Hozzáadás lehetőséget, majd zárja be **a Hálózat menüt.**

Mostantól a BÁRMELY helyről származó HTTP- és HTTPS-forgalom elérheti az összes BIG-IP-VM másodlagos felületet. A 80-as port engedélyezésével lehetővé teszi, hogy a BIG-IP APM automatikusan átirányítsa a felhasználókat a HTTP-ről a HTTPS-re. Ez a szabály szükség esetén módosítható cél IP-k hozzáadásához vagy eltávolításához.

## <a name="manage-big-ip"></a>BIG-IP kezelése

A BIG-IP rendszer felügyelete a webes konfigurációs felhasználói felületen keresztül, amely a következő ajánlott módszerek valamelyikével érhető el:

- A BIG-IP belső hálózatán belüli gépről

- A BIG-IP-VM belső hálózatához csatlakoztatott VPN-ügyfélről

- Közzététel [Azure AD-alkalmazásproxy](./application-proxy-add-on-premises-application.md)

A fennmaradó konfigurációkhoz való folytatáshoz el kell döntenie a legmegfelelőbb módszert. Szükség esetén közvetlenül csatlakozhat a webes konfigurációhoz az internetről a BIG-IP elsődleges IP-címének nyilvános IP-címmel való konfigurálásával. Ezután hozzáad egy NSG-szabályt, amely engedélyezi a 8443-as forgalmat az elsődleges IP-címhez. Ügyeljen arra, hogy a forrást a saját megbízható IP-címére korlátozza, különben bárki csatlakozhat.

Ha elkészült, ellenőrizze, hogy tud-e csatlakozni a BIG-IP virtuális gép webes konfigurációhoz, és jelentkezzen be a virtuális gép üzembe helyezése során megadott hitelesítő adatokkal:

- Ha egy virtuális gépről csatlakozik a belső hálózatán vagy VPN-en keresztül, csatlakozzon közvetlenül a BIG-IPs IP-címhez és a webes konfigurációs porthoz. Például: `https://<BIG-IP-VM_Primary_IP:8443`. A böngésző rákérdez, hogy a kapcsolat nem biztonságos-e, de figyelmen kívül hagyhatja a parancssort, amíg a BIG-IP nincs konfigurálva. Ha a böngésző nem blokkolja a hozzáférést, törölje a gyorsítótárát, és próbálkozzon újra.

- Ha a webes konfigurációt a alkalmazásproxy, akkor a megadott URL-cím használatával külsőleg, a port hozzáfűzése nélkül férhet hozzá a webes konfigurációhoz, például: `https://big-ip-vm.contoso.com` . A belső URL-címet a webes konfigurációs port használatával kell meghatározni, például: `https://big-ip-vm.contoso.com:8443` 

A BIG-IP rendszerek a mögöttes SSH-környezeten keresztül is kezelhetők, amelyet általában parancssori (CLI) feladatokhoz és gyökérszintű hozzáféréshez használnak. A CLI-hez való csatlakozásra több lehetőség is rendelkezésre áll, például:

- [Azure Bastion szolgáltatás:](../../bastion/bastion-overview.md)Gyors és biztonságos kapcsolatokat tesz lehetővé a vNET-en belüli bármely virtuális géphez, bármilyen helyről

- Közvetlen csatlakozás SSH-ügyfélen, például a PuTTY-n keresztül JIT módszeren keresztül

- Soros konzol: A portál Virtuális gépek menüjének Támogatás és hibaelhárítás szakaszában található. Nem támogatja a fájlátvitelt.

- Akárcsak a webes konfigurációnál, közvetlenül is csatlakozhat a CLI-hez az internetről úgy, hogy konfigurálja a BIG-IP elsődleges IP-címét egy nyilvános IP-címmel, és hozzáad egy NSG-szabályt az SSH-forgalom engedélyezése érdekében. Ha ezt a módszert használja, mindenképpen korlátozza a forrást a saját megbízható IP-címére.  

## <a name="big-ip-license"></a>BIG-IP-licenc

A BIG-IP rendszert aktiválni és ki kellépítenünk az APM modullal, mielőtt konfigurálni lehet a szolgáltatások és az SHA közzétételére.

1. Jelentkezzen be újra a webes konfigurációba, és az Általános **tulajdonságok lapon** válassza az Aktiválás **lehetőséget**

2. Az **Alapregisztrációs kulcs mezőbe** írja be az F5 által biztosított megkülönbözteti a kis- és nagybetűket

3. Hagyja meg az  **Aktiválási módszer** automatikus beállítását, és válassza a **Tovább** lehetőséget. A BIG-IP érvényesíti a licencet, és megjeleníti a végfelhasználói licencszerződést (EULA).

4. Válassza **az Elfogadás lehetőséget,** és várjon, amíg az aktiválás befejeződik, mielőtt a **Folytatás lehetőséget választná.**

5. Jelentkezzen be újra, és a Licencösszegzés oldal alján válassza a Tovább **lehetőséget.** A BIG-IP most megjeleníti azon modulok listáját, amelyek az SHA-hez szükséges különböző funkciókat biztosítják. Ha ezt nem látja, a fő lapon válassza a Rendszererőforrás-kiépítés  >  **lapot.**

6. Ellenőrizze a hozzáférési szabályzat (APM) kiépítési oszlopát

![A képen a hozzáférés-kiépítés látható](./media/f5ve-deployment-plan/access-provisioning.png)

7. Válassza **a Küldés lehetőséget,** és fogadja el a figyelmeztetést

8. Várjon, amíg a BIG-IP befejezi az új funkciók bevilágítását. A teljes inicializálás előtt többször is ciklikus lehet. 

9. Ha készen áll, **válassza a Folytatás** lehetőséget, majd a About **(About)** lapon válassza **a Run the setup utility (Telepítési segédprogram futtatása) lehetőséget.**

>[!IMPORTANT]
>Az F5-licencek egyetlen BIG-IP VE-példány számára vannak korlátozva egyszerre. Előfordulhat, hogy a licencet egy példányról egy másikra szeretné átemelni, [](https://support.f5.com/csp/article/K41458656) és ha így tesz, mindenképpen vonja vissza a próbalicencet az aktív példányon a leszerelés előtt, különben a licenc véglegesen elveszik.

## <a name="provision-big-ip"></a>BIG-IP kiépítése

Alapvető fontosságú, hogy biztonságossá BIG-IPs webes konfigurációk felügyeleti forgalmát. Konfigurálnia kell egy eszközkezelési tanúsítványt, amely segít megvédeni a webes konfigurációs csatornát a biztonsági biztonságtól.

1. A bal oldali navigációs sávon válassza a **Rendszer** tanúsítványkezelési  >    >  **forgalom**  >  **tanúsítványkezelése SSL-tanúsítványlista**  >  **importálása adatokat.**

2. Az Importálás **típusa legördülő** listában válassza a **PKCS 12(IIS)** lehetőséget, majd válassza **a Fájl lehetőséget.** Keresse meg azt az SSL-web tanúsítványt, amely tulajdonosnévvel vagy SAN-nal rendelkezik, és amely lefedi a BIG-IP-VM hozzárendelésének teljes tartománynevét a következő néhány lépésben

3. Adja meg a tanúsítvány jelszavát, majd válassza az Importálás **lehetőséget**

4. A bal oldali navigációs sávon kattintson a **Rendszerplatformra.**  >  

5. A BIG-IP-VM konfigurálása teljes állomásnévvel és a környezete időzónával, majd frissítés 

![A képen általános tulajdonságok láthatóak](./media/f5ve-deployment-plan/general-properties.png)

6. A bal oldali navigációs sávon kattintson a Rendszerkonfiguráció eszköz   >    >    >  **NTP-fájlja pontra.**

7. Adjon meg egy megbízható NTP-forrást, és **válassza a Hozzáadás,** majd a **Frissítés lehetőséget.** Például: `time.windows.com`

Most egy DNS-rekordra van szükség az előző lépésekben BIG-IPs FQDN elsődleges magánhálózati IP-címére való feloldásához. Hozzá kell adni egy rekordot a környezet belső DNS-éhez vagy egy számítógép localhost fájljéhez, amely a BIG-IP webes konfigurációhoz fog csatlakozni. Mindkét esetben a böngésző figyelmeztetése többé nem jelenik meg, amikor közvetlenül csatlakozik a webes konfigurációhoz. Ez azt jelenti, hogy nem alkalmazásproxy más fordított proxyn keresztül.

## <a name="ssl-profile"></a>SSL-profil

Fordított proxyként a BIG-IP-rendszerek egyszerű továbbítási szolgáltatásként, más néven transzparens proxyként vagy teljes proxyként is viselkedhetnek, amely aktívan részt vesz az ügyfelek és kiszolgálók közötti adatcserében.
A teljes proxy két különálló kapcsolatot hoz létre: egy előteres TCP-ügyfélkapcsolatot, valamint egy különálló háttérbeli TCP-kiszolgálókapcsolatot, amelyek között egy gyenge eltérés található. Az ügyfelek az egyik végén, más néven **virtuális** kiszolgálóként csatlakoznak a proxy listenerhez, és a proxy különálló, független kapcsolatot létesít a háttérkiszolgálóval. Ez kétirányú mindkét oldalon.
Ebben a teljes proxymódban az F5 BIG-IP rendszer képes megvizsgálni a forgalmat, így a kérésekkel és válaszokkal való interakció is lehetséges. Bizonyos funkciók, például a terheléselosztás és a webes teljesítmény optimalizálása, valamint a fejlettebb forgalomfelügyeleti szolgáltatások, például az alkalmazásréteg biztonsága, a webgyorsítás, a lapútválasztás és a biztonságos távelérés, erre a funkcióra támaszkodnak.
SSL-alapú szolgáltatások közzétételekor az ügyfelek és a háttérszolgáltatások közötti forgalom visszafejtése és titkosítása BIG-IP SSL történik.

Kétféle profil létezik:

- **Ügyfél SSL:** A BIG-IP rendszerek ssl használatával belső szolgáltatások közzétételére való beállításának leggyakoribb módja egy ügyfél SSL-profil létrehozása. Az ügyfél SSL-profillal a BIG-IP rendszer vissza tudja fejteni a bejövő ügyfélkéréseket, mielőtt továbbküldi őket egy lefelé irányuló szolgáltatásnak. Ezután titkosítja a kimenő háttér válaszokat, mielőtt elküldi azokat az ügyfeleknek.

- **Kiszolgálói SSL:** A HTTPS-hez konfigurált háttérszolgáltatásokhoz konfigurálhatja a BIG-IP-t egy kiszolgálói SSL-profil használatára. Kiszolgálói SSL-profil esetén a BIG-IP újratitkosítja az ügyfélkérést, mielőtt továbbküldi azt a cél háttérszolgáltatásnak. Amikor a kiszolgáló titkosított választ ad vissza, a BIG-IP rendszer visszafejti és újra titkosítja a választ, mielőtt továbbküldi az ügyfélnek a konfigurált ügyfél SSL-profilon keresztül.

Az ügyfél- és a kiszolgálói SSL-profilok kiépítése is rendelkezik a BIG-IP előre konfigurált és készen áll az összes SHA-forgatókönyvhöz.

1. A bal oldali navigációs sávon válassza a **Rendszer** tanúsítványkezelési  >    >  **forgalom**  >  **tanúsítványkezelése SSL-tanúsítványlista**  >  **importálása adatokat.**

2. Az Importálás **típusa legördülő** listában válassza a **PKCS 12(IIS) lehetőséget**

3. Adja meg az importált tanúsítvány nevét, például:  `ContosoWilcardCert`

4. Válassza **a Fájl kiválasztása** lehetőséget annak az SSL-web tanúsítványnak a tallózáshoz, amely a közzétett szolgáltatásokhoz használni kívánt tartomány-utótagnak felel meg

5. Adja meg **az importált** tanúsítvány jelszavát, majd válassza az Importálás **lehetőséget.**

6. A bal oldali navigációs sávon válassza a **Helyi** forgalmi profilok SSL-ügyfél lehetőséget,  >    >    >   majd válassza a Létrehozás **lehetőséget.**

7. Az Új **ügyfél SSL-profil oldalán** adjon meg egy egyedi rövid nevet az új ügyfél SSL-profilja számára, és győződjön meg arról, hogy a Szülő profil az **ügyfelekre van állítval**

![A képen a big-ip frissítés látható](./media/f5ve-deployment-plan/client-ssl.png)

8. Jelölje be a jobb távoli jelölőnégyzetet a Tanúsítványkulcs-lánc **sorban,** majd válassza a Hozzáadás **lehetőséget**

9. A három legördülő listában válassza ki a jelszó nélkül importált helyettesítő tanúsítványt, majd válassza a **Kész hozzáadása**  >  **lehetőséget.**

![A képen a big-ip contoso helyettesítő karakter látható](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Ismételje meg a 6–9. lépést egy **SSL-kiszolgálói tanúsítványprofil létrehozásához.** A felső menüszalagon válassza az  >  **SSL-kiszolgáló létrehozása**  >  **lehetőséget.**

11. Az Új **kiszolgálói SSL-profil lapon** adjon meg egy egyedi rövid nevet az új kiszolgálói SSL-profilnak, és győződjön meg arról, hogy a Szülő profil kiszolgálókra **van állítval**

12. Jelölje be a jobb far-right (jobb) jelölőnégyzetet a Certificate (Tanúsítvány) és a Key (Kulcs) soroknál, majd a legördülő listából válassza ki az importált tanúsítványt, majd kattintson a Finished (Kész) **gombra.**

![A képen a big-ip kiszolgáló összes profiljának frissítése látható](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Ne aggódjon, ha nem tud SSL-tanúsítványt beszerezni, használhatja az integrált önaírt BIG-IP-kiszolgálót és az ügyfél SSL-tanúsítványait. Csak egy tanúsítványhiba jelenik meg a böngészőben.

A BIG-IP SHA-hez való előkészítésének utolsó lépése annak biztosítása, hogy meg tudja találni a közzétételhez szükséges erőforrásokat, valamint azt a címtárszolgáltatást, amelyre az SSO-hoz támaszkodik. A BIG-IP két névfeloldási forrással rendelkezik, kezdve a helyi/.../hosts fájllal, vagy ha nem található rekord, a BIG-IP rendszer a konfigurált DNS-szolgáltatást használja. A hosts fájl metódus nem vonatkozik az FQDN-t tartalmazó APM-csomópontokra és -készletekre.

1. A webes konfigurációban menjen a Rendszerkonfigurációs eszköz  >    >  **DNS-éhez.**  >  

2. A **DNS keresési kiszolgáló listájában** adja meg a környezetek DNS-kiszolgálójának IP-címét

3. Válassza a **Frissítés**  >  **hozzáadása lehetőséget**

Különálló és választható lépésként érdemes lehet [LDAP-konfigurációt](https://somoit.net/f5-big-ip/authentication-using-active-directory) használni a BIG-IP-rendszergazdai hitelesítéshez az AD-hez a helyi BIG-IP-fiókok kezelése helyett.

## <a name="update-big-ip"></a>BIG-IP frissítése

A BIG-IP-VM-et frissíteni kell, hogy elérhető legyen a [forgatókönyv-alapú útmutatóban](f5-aad-integration.md)szereplő összes új funkció. Ellenőrizheti, hogy a rendszer TMOS-verziója az egérmutatót BIG-IPs főoldal bal felső oldalán található gazdanév fölé. Javasoljuk, hogy a 15.x vagy magasabb, az F5 letöltésből beszerezhető [et futtasa.](https://downloads.f5.com/esd/productlines.jsp) Az útmutató [segítségével](https://support.f5.com/csp/article/K34745165) frissítheti a fő rendszer operációs rendszerét (TMOS).

Ha a fő TMOS frissítése nem lehetséges, fontolja meg az irányított konfiguráció önálló frissítését az alábbi lépésekkel.

1. A BIG-IP webes konfiguráció fő lapján válassza az **Irányított hozzáférés**  >  **konfigurációja lapot.**

2. Az Irányított **konfiguráció lapon** válassza az Irányított konfiguráció **frissítése lehetőséget.**

![A kép a big-ip frissítését mutatja be](./media/f5ve-deployment-plan/update-big-ip.png)

3. Az Irányított **konfiguráció frissítése párbeszédpanelen** válassza a **Fájl** lehetőséget a letöltött esetcsomag feltöltéséhez, majd válassza a Feltöltés és **telepítés lehetőséget.**

4. Ha a frissítés befejeződött, válassza a Folytatás **lehetőséget.**

## <a name="backup-big-ip"></a>BIG-IP biztonsági mentése

Most, hogy a BIG-IP rendszer teljesen ki van építve, javasoljuk, hogy a konfigurációról teljes biztonsági másolatot készít:

1. Ugrás a **Rendszerarchivárak**  >  **létrehozása**  >  **gombra**

2. Adjon meg egy egyedi **fájlnevet,** **és** engedélyezze a titkosítást jelszóval

3. Állítsa a **Titkos kulcsok beállítást** a Be include (Be) beállításra az eszköz- és SSL-tanúsítványok biztonságiának beállításhoz. 

4. Válassza a **Kész lehetőséget,** és várjon, amíg a folyamat befejeződik

5. Megjelenik egy művelet állapota, amely a biztonsági mentés eredményét jelzi. Válassza az **OK** gombot.

6. Mentse helyileg a Felhasználói konfigurációs csoport (UCS) archívumát a biztonsági másolat hivatkozásának kiválasztásával, majd válassza a **Letöltés lehetőséget.**

Választható lépésként biztonsági másolatot készíthet a teljes rendszerlemezről az [Azure-pillanatképek](../../virtual-machines/windows/snapshot-copy-managed-disk.md)használatával, amely a webes konfiguráció biztonsági mentésével ellentétben bizonyos válságtervet biztosít a TMOS-verziók közötti teszteléshez, vagy egy új rendszerre való visszaállításhoz.

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

A BIG-IP-címek visszaállítása a biztonsági mentéshez hasonló eljárást követ, és a BIG-IP virtuális gépek közötti konfigurációk áttelepítésére is használható. A biztonsági másolatok importálása előtt érdemes figyelembe venni a támogatott frissítési útvonalak részleteit.

1. Ugrás a **Rendszerarchivumra**  >  

2. Válassza ki a visszaállítani kívánt biztonsági másolat  hivatkozását, vagy válassza a Feltöltés gombot egy korábban mentett, a listában nem található UCS-archívum megkeresásához

3. Adja meg a biztonsági mentéshez szükséges jelszót, és válassza a Visszaállítás **lehetőséget**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>A cikk írásakor az AzVmSnapshot parancsmag a legújabb pillanatkép visszaállítására van korlátozva a dátum alapján. A pillanatképek a virtuális gép erőforráscsoportja gyökerében vannak tárolva. Vegye figyelembe, hogy a pillanatképek visszaállítása újraindítja az Azure-beli virtuális gépet, ezért ezt gondosan gondolja át.

## <a name="additional-resources"></a>További források

-   [BIG-IP VE-jelszó visszaállítása az Azure-ban](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Jelszó visszaállítása a portál használata nélkül](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [A BIG-IP VE felügyeletéhez használt hálózati adapter módosítása](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Útvonalak egyetlen hálózati adapter konfigurációjában](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Következő lépések

Válasszon ki egy [üzembe helyezési forgatókönyvet,](f5-aad-integration.md) és indítsa el az implementációt.
