---
title: Architektúrák Oracle-alkalmazások üzembe helyezéséhez az Azure Virtual Machineson | Microsoft Docs
description: Alkalmazás-architektúrák Oracle-alkalmazások, például az E-Business Suite, a JD Edwards EnterpriseOne és a PeopleSoft Microsoft Azure virtuális gépeken való üzembe helyezéséhez az Azure-ban vagy az Oracle Cloud Infrastructure-ben (OCI) található adatbázisokkal.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669925"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architektúrák Oracle-alkalmazások üzembe helyezéséhez az Azure-ban

A Microsoft és az Oracle együttesen együttműködve lehetővé teszi, hogy az ügyfelek olyan Oracle-alkalmazásokat telepítsenek, mint például az Oracle E-Business Suite, a JD Edwards EnterpriseOne és a PeopleSoft a felhőben. Az előnézeti magánhálózat Microsoft Azure és az Oracle Cloud Infrastructure (OCI) közötti [összekapcsolásának](configure-azure-oci-networking.md) bevezetésével az Oracle-alkalmazások mostantól üzembe helyezhetők az Azure-ban az Azure-ban vagy a OCI-ban található háttér-adatbázisokkal. Az Oracle-alkalmazások Azure Active Directory is integrálhatók, így az egyszeri bejelentkezés beállítható úgy, hogy a felhasználók bejelentkezhetnek az Oracle-alkalmazásba a Azure Active Directory (Azure AD) hitelesítő adataik használatával.

A OCI több Oracle Database-lehetőséget is kínál Oracle-alkalmazásokhoz, többek között a DBaaS, a Exadata Cloud Service-hez, az Oracle RAC-hoz és az infrastruktúra-szolgáltatáshoz (IaaS). Az önálló adatbázisok jelenleg nem támogatottak az Oracle-alkalmazásokhoz.

[Több lehetőség](oracle-overview.md) is van az Oracle-alkalmazások Azure-ban történő üzembe helyezésére, többek között a rendelkezésre állásra és a biztonságos használatra. Az Azure Oracle Database-alapú virtuálisgép- [rendszerképeket](oracle-vm-solutions.md) is kínál, amelyeket akkor telepíthet, ha az Oracle-alkalmazásokat teljes egészében az Azure-ban futtatja.

Az alábbi részekben a Microsoft és az Oracle architektúrára vonatkozó javaslatai alapján az Oracle E-Business Suite, a JD Edwards EnterpriseOne és a PeopleSoft egy Felhőbeli konfigurációban vagy teljes egészében az Azure-ban is üzembe helyezhető. A Microsoft és az Oracle tesztelte ezeket az alkalmazásokat, és megerősítette, hogy a teljesítmény megfelel az Oracle által az alkalmazások számára beállított szabványoknak.

## <a name="architecture-considerations"></a>Architektúrával kapcsolatos megfontolások

Az Oracle-alkalmazások több szolgáltatásból állnak, amelyek az Azure-ban vagy akár több virtuális gépen is tárolhatók, és opcionálisan OCI is. 

Az alkalmazás példányai saját vagy nyilvános végpontokkal is beállíthatók. A Microsoft és az Oracle azt ajánlja, hogy az alkalmazás felügyeletéhez külön alhálózatban állítson be egy nyilvános IP-címmel rendelkező *megerősített gazda virtuális gépet* . Ezután rendeljen hozzá csak privát IP-címeket a többi géphez, beleértve az adatbázis-szintet is. 

Az alkalmazások többfelhős architektúrában való beállításakor meg kell tervezni, hogy az Azure-beli virtuális hálózat IP-címe ne fedje át a magánhálózati IP-címtartományt a OCI Virtual Cloud Networkben.

A további biztonság érdekében állítsa be a hálózati biztonsági csoportokat egy alhálózati szinten, hogy csak bizonyos portok és IP-címek forgalmát engedélyezze. Például a középső szinten lévő gépek csak a virtuális hálózaton belülről kapnak forgalmat. Nincs külső forgalom közvetlenül a középső rétegű gépek eléréséhez.

A magas rendelkezésre állás érdekében megadhatja a különböző kiszolgálók redundáns példányait ugyanabban a rendelkezésre állási csoport vagy különböző rendelkezésre állási zónában. A rendelkezésre állási zónák lehetővé teszik a 99,99%-os üzemidőt biztosító SLA elérését, míg a rendelkezésre állási csoportok lehetővé teszik a 99,95%-os ÜZEMIDŐt a régióban. A cikkben bemutatott példák két rendelkezésre állási zónában vannak üzembe helyezve.

Ha a többfelhős összekötő használatával helyez üzembe egy alkalmazást, továbbra is használhat egy meglévő ExpressRoute-áramkört az Azure-környezet a helyszíni hálózathoz való összekapcsolásához. Azonban külön ExpressRoute áramkörre van szükség ahhoz, hogy az összekötő OCI, mint a helyszíni hálózathoz való csatlakozás.

## <a name="e-business-suite"></a>E-Business csomag

Az Oracle E-Business Suite (EBS) az alkalmazások egy csomagja, például az Ellátásilánc-kezelés (SCM) és az Ügyfélkapcsolat-kezelés (CRM). A OCI felügyelt adatbázis-portfóliójának kihasználásához az EBS a Microsoft Azure és a OCI közötti többfelhős összekötő használatával telepíthető. Ebben a konfigurációban a bemutató és az alkalmazás szintjei az Azure-ban és az adatbázis-szinten futnak a OCI-ben, ahogyan az az alábbi architektúra ábrán látható (1. ábra).

![Az E-Business Suite többfelhős architektúrája](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*1. ábra: az E-Business Suite többfelhős architektúrája* 

Ebben az architektúrában az Azure-beli virtuális hálózat a OCI-alapú virtuális felhőhöz csatlakozik a többfelhős összekötő használatával. Az alkalmazás szintje az Azure-ban van beállítva, míg az adatbázis be van állítva a OCI-ben. Azt javasoljuk, hogy minden összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokról érkező adatforgalmat engedélyezzen az adott portokon.

Az architektúra az Azure-ban olyan, magasan elérhető Oracle-adatbázisokkal is adaptálható az üzembe helyezéshez, amelyek az Oracle adatvédelmet használják a régió két rendelkezésre állási zónájában. A következő diagram (2. ábra) egy példa erre az építészeti mintára:

![E-Business Suite – csak Azure-architektúrák](media/oracle-oci-applications/ebs-arch-azure.png)

*2. ábra: az E-Business Suite csak az Azure-architektúrája*

A következő szakaszok a különböző összetevőket ismertetik magas szinten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Alkalmazás (középső) szintje

Az alkalmazás szintje el van különítve a saját alhálózatában. Több virtuális gép van beállítva a hibatűréshez és az egyszerű javítások kezeléséhez. Ezeket a virtuális gépeket megosztott tárolók is használhatják, amelyeket Azure NetApp Files és ultra SSD-k kínálnak. Ez a konfiguráció lehetővé teszi a javítások egyszerű üzembe helyezését állásidő nélkül. Az alkalmazási rétegben található gépeket nyilvános Load balancernek kell ellátnia, hogy az EBS-alkalmazások szintjére érkező kéréseket akkor is dolgozza fel a rendszer, ha a réteg egyik gépe hiba miatt offline állapotban van.

### <a name="load-balancer"></a>Terheléselosztóval

Az Azure Load Balancer lehetővé teszi, hogy a magas rendelkezésre állás biztosítása érdekében a számítási feladatok több példányán keresztül ossza el a forgalmat. Ebben az esetben egy nyilvános terheléselosztó van beállítva, mivel a felhasználók hozzáférhetnek az EBS-alkalmazáshoz a weben keresztül. A terheléselosztó elosztja a terhelést mindkét gépre a középső szinten. A nagyobb biztonság érdekében csak a vállalati hálózatról a rendszerhez hozzáférő felhasználóktól érkező adatforgalmat helyek közötti VPN-vagy ExpressRoute-és hálózati biztonsági csoportok használatával.

### <a name="database-tier"></a>Adatbázis szintje

Ez a platform az Oracle-adatbázist üzemelteti, és a saját alhálózatára van elkülönítve. Ajánlott olyan hálózati biztonsági csoportokat felvenni, amelyek csak az alkalmazás szintjéről érkező forgalmat engedélyezik az Oracle-specifikus adatbázis 1521-es portjának adatbázis-szintjére.

A Microsoft és az Oracle a magas rendelkezésre állású telepítést ajánlja. Az Azure magas rendelkezésre állása úgy érhető el, ha két Oracle-adatbázist állít be két rendelkezésre állási zónában az Oracle-adatgárda használatával vagy Oracle Database Exadata Cloud Service-t a OCI-ben. Oracle Database Exadata Cloud Service használatakor az adatbázis két alhálózaton van üzembe helyezve. Az Oracle-adatvédelemmel rendelkező két rendelkezésre állási tartományban a OCI virtuális gépeken Oracle Database is beállíthatja.


### <a name="identity-tier"></a>Identitási rétegek

Az Identity (identitás) szintje tartalmazza az EBS-előállítási virtuális gépet. Az EBS-megerősítő segítségével szinkronizálhatja az Oracle Identity Cloud Service (IDCS) és az Azure AD identitásait. Az EBS-megerősítő azért szükséges, mert az EBS nem támogatja az egyszeri bejelentkezési protokollok, például az SAML 2,0 vagy az OpenID Connect használatát. Az EBS-ellenőrző felhasználja az OpenID Connect tokent (IDCS által generált), ellenőrzi, majd létrehoz egy munkamenetet a felhasználó számára az EBS-ben. 

Habár ez az architektúra az IDCS-integrációt mutatja be, az Azure AD Unified Access és az egyszeri bejelentkezés is engedélyezhető az Oracle Access Managerben Oracle Internet Directory vagy Oracle Unified Directory használatával. További információ: az [Oracle EBS üzembe helyezése az IDCS-integrációval](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) vagy az [Oracle EBS üzembe helyezése OAM-integrációval](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf).

A magas rendelkezésre állás érdekében javasolt, hogy az EBS-megerősítő kiszolgálókat több rendelkezésre állási zónába telepítse, egy terheléselosztó előtt.

Az infrastruktúra beállítása után az E-Business Suite az Oracle által biztosított telepítési útmutatót követve telepíthető.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Az Oracle JD Edwards EnterpriseOne egy átfogó, nagyvállalati erőforrás-tervezési szoftvert használó integrált alkalmazás. Ez egy többrétegű alkalmazás, amely Oracle vagy SQL Server adatbázis-háttérrel is beállítható. Ebből a szakaszból megtudhatja, hogyan telepítheti a JD Edwards EnterpriseOne egy Oracle-adatbázis háttér-OCI vagy az Azure-ban.

A következő javasolt architektúrán (3. ábra) a felügyelet, a bemutató és a középső rétegek az Azure-beli virtuális hálózatra lesznek telepítve. Az adatbázis üzembe helyezése a OCI virtuális felhőalapú hálózatában történik.

Csakúgy, mint az E-Business Suite esetében, beállíthat egy opcionális megerősített szintet is a biztonságos adminisztrációs célokra. Az alkalmazás-és adatbázis-példányok eléréséhez használja a megerősített VM-gazdagépet.

![JD Edwards EnterpriseOne – Felhőbeli architektúra](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*3. ábra: a JD Edwards EnterpriseOne többfelhős architektúrája*

Ebben az architektúrában az Azure-beli virtuális hálózat a OCI-alapú virtuális felhőhöz csatlakozik a Felhőbeli összekötő használatával. Az alkalmazás szintje az Azure-ban van beállítva, míg az adatbázis be van állítva a OCI-ben. Azt javasoljuk, hogy minden összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokról érkező adatforgalmat engedélyezzen az adott portokon.

Az architektúra az Azure-ban olyan, magasan elérhető Oracle-adatbázisokkal is adaptálható az üzembe helyezéshez, amelyek az Oracle adatvédelmet használják a régió két rendelkezésre állási zónájában. A következő diagram (4. ábra) egy példa erre az építészeti mintára:

![JD Edwards EnterpriseOne csak Azure architektúrával](media/oracle-oci-applications/jdedwards-arch-azure.png)

*4. ábra: a JD Edwards EnterpriseOne csak az Azure-architektúrával*

A következő szakaszok a különböző összetevőket ismertetik magas szinten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Felügyeleti szintek

Ahogy a neve is sugallja, ezt a szintet a felügyeleti feladatokhoz használja a rendszer. A felügyeleti szinten külön alhálózatot is kivésett. Az ebben a szinten található szolgáltatások és kiszolgálók elsődlegesen az alkalmazás telepítéséhez és felügyeletéhez használatosak. Ezért a kiszolgálók egyetlen példánya elegendő. Redundáns példányok nem szükségesek az alkalmazás magas rendelkezésre állásához.

A rétegek összetevői a következők:
    
 - **Kiépítési kiszolgáló** – ez a kiszolgáló az alkalmazás különböző összetevőinek végpontok közötti telepítésére szolgál. A többi szinten lévő példányokkal kommunikál, beleértve az adatbázis-szinten lévő példányokat a 22-es porton keresztül. A Server Manager konzolját a JD Edwards EnterpriseOne üzemelteti.
 - **Központi telepítési kiszolgáló** – ez a kiszolgáló elsődlegesen a JD Edwards EnterpriseOne telepítéséhez szükséges. A telepítési folyamat során ez a kiszolgáló a szükséges fájlok és telepítési csomagok központi tárháza működik. A szoftver terjesztése vagy üzembe helyezése a kiszolgáló többi kiszolgálójára és ügyfelére történik.
 - **Fejlesztői ügyfél** – ez a kiszolgáló webböngészőben és natív alkalmazásokban futó összetevőket tartalmaz.

### <a name="presentation-tier"></a>Bemutatási réteg

Ez a csomag különböző összetevőket tartalmaz, például az Application Interface Services (AIS), az Application Development Framework (ADF) és a Java Application Servers (JAS) összetevőt. Az ebben a platformban található kiszolgálók a középső szinten lévő kiszolgálókkal kommunikálnak. Ezeket a terheléselosztást egy terheléselosztó továbbítja, amely a szükséges kiszolgálóra irányítja a forgalmat, a port száma és a forgalom által fogadott URL-cím alapján. Azt javasoljuk, hogy a magas rendelkezésre állás érdekében az egyes kiszolgálói típusok több példányát telepítse.

Ebben a szinten a következő összetevők találhatók:
    
- **Application Interface Services (AIS)** – az AIS-kiszolgáló biztosítja a kommunikációs FELÜLETET a JD Edwards EnterpriseOne Mobile Enterprise Applications és a JD Edwards EnterpriseOne között.
- **Java alkalmazáskiszolgáló (Jas)** – a Jas kérelmeket fogad a terheléselosztótól, és a középső rétegre továbbítja a bonyolult feladatok végrehajtásához. A JAS képes egyszerű üzleti logikát végrehajtani.
- **Bi közzétevő kiszolgáló (bip)** – ez a kiszolgáló a JD Edwards EnterpriseOne alkalmazás által gyűjtött adatok alapján mutatja be a jelentéseket. Megtervezheti és szabályozhatja, hogy a jelentés hogyan jelenítse meg az adathalmazokat a különböző sablonok alapján.
- **Business Services-kiszolgáló (alaprendszer) – a nagyvállalati portál** lehetővé teszi az információcserét és a más Oracle-alkalmazásokkal való együttműködést.
- **Valós idejű események kiszolgálója (RTE)** – az RTE-kiszolgáló lehetővé teszi, hogy értesítéseket állítson be a külső rendszereknek a JDE EnterpriseOne rendszerbeli tranzakciókkal kapcsolatban. Előfizetői modellt használ, és lehetővé teszi, hogy a harmadik féltől származó rendszerek előfizessenek az eseményekre. A kérelmeknek az RTE-kiszolgálókra való terheléselosztásához győződjön meg arról, hogy a kiszolgálók fürtben vannak.
- **Application Development Framework-(ADF-) kiszolgáló** – az ADF-kiszolgáló az Oracle ADF-mel fejlesztett JD Edwards EnterpriseOne-alkalmazások futtatására szolgál. Ez egy, az ADF-futtatókörnyezettel rendelkező Oracle WebLogic-kiszolgálón van telepítve.

### <a name="middle-tier"></a>Középső rétegek

A középső szinten a logikai kiszolgáló és a Batch-kiszolgáló található. Ebben az esetben mindkét kiszolgáló ugyanarra a virtuális gépre van telepítve. Éles környezetben azonban ajánlott a logikai kiszolgáló és a Batch Server telepítése külön kiszolgálókon. A magasabb rendelkezésre állás érdekében több kiszolgáló van üzembe helyezve a középső rétegben két rendelkezésre állási zónában. Létre kell hozni egy Azure Load balancert, és ezeket a kiszolgálókat a háttér-készletbe kell helyezni, hogy mindkét kiszolgáló aktív és feldolgozási kérelmeket tartalmazzon.

A középső szinten lévő kiszolgálók kérelmeket fogadnak a megjelenítési és a nyilvános terheléselosztó kiszolgálóinak. A hálózati biztonsági csoport szabályait úgy kell beállítani, hogy megtagadják a forgalmat a bemutató szintű alhálózattól és a terheléselosztótól eltérő címről. Egy NSG-szabály is beállítható úgy, hogy a a 22-es porton keresztüli forgalmat felügyeleti célokra engedélyezze. Előfordulhat, hogy a nyilvános terheléselosztó használatával terheléselosztást kell biztosítania a középső szinten lévő virtuális gépek között.

A következő két összetevő a középső rétegben található:

- **Logikai kiszolgáló** – tartalmazza az üzleti logikát vagy az üzleti funkciókat.
- **Batch-kiszolgáló** – kötegelt feldolgozáshoz használatos

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Az Oracle PeopleSoft Application Suite szoftvereket tartalmaz az emberi erőforrások és a pénzügyi felügyelet számára. Az alkalmazáscsomag többrétegű, és az alkalmazások közé tartoznak a humán erőforrás-kezelési rendszerek (HRMS), az Ügyfélkapcsolat-kezelés (CRM), a Pénzügy-és ellátásilánc-felügyelet (FSCM) és a vállalati teljesítmény-felügyelet (EPM).

Azt javasoljuk, hogy a szoftvercsomag egyes szintjei a saját alhálózatán legyenek telepítve. Egy Oracle-adatbázis vagy Microsoft SQL Server szükséges az alkalmazáshoz tartozó háttér-adatbázisként. Ez a szakasz a PeopleSoft Oracle Database-háttérrel való üzembe helyezésének részleteit ismerteti.

A következő egy kanonikus architektúra a PeopleSoft Application Suite Felhőbeli architektúrában történő üzembe helyezéséhez (5. ábra).

![A PeopleSoft többfelhős architektúrája](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*5. ábra: a PeopleSoft többfelhős architektúrája*

Ebben a példában az Azure-beli virtuális hálózat a OCI-beli virtuális felhőhöz csatlakozik a Felhőbeli összekötő használatával. Az alkalmazás szintje az Azure-ban van beállítva, míg az adatbázis be van állítva a OCI-ben. Azt javasoljuk, hogy minden összetevőt a saját alhálózatára telepítsen hálózati biztonsági csoportokkal, hogy csak bizonyos alhálózatokról érkező adatforgalmat engedélyezzen az adott portokon.

Az architektúra az Azure-ban olyan, magasan elérhető Oracle-adatbázisokkal is adaptálható az üzembe helyezéshez, amelyek az Oracle adatvédelmet használják a régió két rendelkezésre állási zónájában. A következő diagram (6. ábra) egy példa erre az építészeti mintára:

![A PeopleSoft csak Azure architektúrája](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*6. ábra: a PeopleSoft csak az Azure architektúrája*

A következő szakaszok a különböző összetevőket ismertetik magas szinten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Alkalmazásréteg

Az alkalmazási szinten a PeopleSoft-alkalmazás-kiszolgálók, a PeopleSoft-webkiszolgálók, a rugalmas keresés és a PeopleSoft-feldolgozó ütemező példányai szerepelnek. Az Azure Load Balancer úgy van beállítva, hogy fogadja a felhasználóktól érkező kéréseket, amelyek az alkalmazási szinten a megfelelő kiszolgálóra vannak irányítva.

A magas rendelkezésre állás érdekében érdemes lehet minden kiszolgáló redundáns példányait beállítani az alkalmazási szinten a különböző rendelkezésre állási zónák között. Az Azure Load Balancer több háttér-készlettel is beállítható úgy, hogy az egyes kérelmeket a megfelelő kiszolgálóra irányítsa.

### <a name="peopletools-client"></a>PeopleTools-ügyfél

Az PeopleTools-ügyfél az adminisztrációs tevékenységek, például a fejlesztés, az áttelepítés és a frissítés végrehajtásához használatos. Mivel az PeopleTools-ügyfél nem szükséges az alkalmazás magas rendelkezésre állásának megvalósításához, nincs szükség a PeopleTools-ügyfél redundáns kiszolgálóira.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Következő lépések

Használjon [Terraform-szkripteket](https://github.com/microsoft/azure-oracle) Oracle-alkalmazások beállításához az Azure-ban, és hozzon létre több Felhőbeli kapcsolatot a OCI használatával.

A OCI kapcsolatos további információkért és tanulmányokhoz tekintse meg az [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentációját.
