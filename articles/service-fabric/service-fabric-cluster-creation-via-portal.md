---
title: Service Fabric-fürt létrehozása az Azure Portalon
description: Ismerje meg, hogyan állíthat be biztonságos Service Fabric-fürtöt az Azure-ban a Azure Portal és a Azure Key Vault használatával.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563726"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Service Fabric-fürt létrehozása az Azure-ban a Azure Portal használatával
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Ez egy lépésenkénti útmutató, amely végigvezeti egy Service Fabric-fürt (Linux vagy Windows) az Azure-ban való beállításának lépésein a Azure Portal használatával. Ez az útmutató végigvezeti a következő lépéseken:

* Hozzon létre egy fürtöt az Azure-ban a Azure Portalon keresztül.
* A rendszergazdák hitelesítése tanúsítványokkal.

> [!NOTE]
> A speciális biztonsági beállítások, mint például a felhasználói hitelesítés és az alkalmazások biztonságának beállítása Azure Active Directory, [a fürt létrehozása Azure Resource Manager használatával][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Fürtbiztonság 
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások különféle részeinek védelmére. A tanúsítványok Service Fabricban való használatával kapcsolatos további információkért lásd a [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security].

Ha első alkalommal hoz létre Service Fabric-fürtöt, vagy fürtöt helyez üzembe a tesztelési feladatokhoz, ugorjon a következő szakaszra (**fürt létrehozása a Azure Portal**), és a rendszer tanúsítványokat hoz létre a fürtökhöz, amelyek tesztelési feladatokat futtatnak. Ha éles számítási feladatokhoz állít be fürtöt, akkor folytassa az olvasást.

#### <a name="cluster-and-server-certificate-required"></a>Fürt és kiszolgáló tanúsítványa (kötelező)
Ez a tanúsítvány szükséges a fürt biztonságossá tételéhez és a jogosulatlan hozzáférés megakadályozásához. Több módon biztosítja a fürt biztonságát:

* **Fürt hitelesítése:** A fürt-összevonás csomópont-csomópont típusú kommunikációjának hitelesítése. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek bizonyítani tudják, hogy az identitásuk ezzel a tanúsítvánnyal is rendelkezhet.
* **Kiszolgáló hitelesítése:** Hitelesíti a fürtszolgáltatási végpontokat egy felügyeleti ügyfélen, hogy a felügyeleti ügyfél tudja, hogy az a valódi fürthöz beszél. Ez a tanúsítvány a HTTPS felügyeleti API-hoz és a HTTPS-en keresztüli Service Fabric Explorerhoz is biztosít TLS-t.

Ezeknek a céloknak a kiszolgálásához a tanúsítványnak meg kell felelnie a következő követelményeknek:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot.
* A tanúsítványt létre kell hozni a személyes információcsere (. pfx) fájlba exportálható kulcscsere-fájlhoz.
* A tanúsítvány **tulajdonosának nevének meg kell egyeznie** a Service Fabric fürt eléréséhez használt tartománnyal. Erre azért van szükség, hogy biztosítsa a TLS-t a fürt HTTPS-felügyeleti végpontjai és Service Fabric Explorer számára. Nem szerezhet be TLS/SSL-tanúsítványt a tartományhoz tartozó hitelesítésszolgáltatótól (CA) `.cloudapp.azure.com` . Szerezzen be egy egyéni tartománynevet a fürt számára. Amikor tanúsítványt kér egy HITELESÍTÉSSZOLGÁLTATÓTÓL, a tanúsítvány tulajdonosának nevének meg kell egyeznie a fürthöz használt egyéni tartománynévvel.

#### <a name="client-authentication-certificates"></a>Ügyfél-hitelesítési tanúsítványok
További Ügyféltanúsítványok hitelesítik a rendszergazdákat a fürt felügyeleti feladataihoz. Service Fabric két hozzáférési szinttel rendelkezik: a **rendszergazda** és a **csak olvasási** jogosultsággal rendelkező felhasználó. Legalább egyetlen tanúsítványt kell használni a rendszergazdai hozzáféréshez. További felhasználói szintű hozzáféréshez külön tanúsítványt kell megadni. A hozzáférési szerepkörökkel kapcsolatos további információkért lásd: [Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlése][service-fabric-cluster-security-roles].

Nem kell feltöltenie az ügyfél-hitelesítési tanúsítványokat, hogy a Key Vault működjön a Service Fabricval. Ezeket a tanúsítványokat csak azokhoz a felhasználókhoz kell megadni, akik jogosultak a fürtözés kezelésére. 

> [!NOTE]
> Azure Active Directory a fürt felügyeleti műveleteihez szükséges ügyfelek hitelesítésének ajánlott módja. Azure Active Directory használatához [létre kell hoznia egy fürtöt a Azure Resource Manager használatával][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Alkalmazás-tanúsítványok (nem kötelező)
A fürtön tetszőleges számú további tanúsítvány telepíthető alkalmazás biztonsági célokra. A fürt létrehozása előtt vegye figyelembe azokat az alkalmazás-biztonsági forgatókönyveket, amelyekhez tanúsítvány szükséges a csomópontokon való telepítéshez, például:

* Az alkalmazás konfigurációs értékeinek titkosítása és visszafejtése
* Adattitkosítás a csomópontok között a replikálás során 

Az alkalmazás tanúsítványait nem lehet konfigurálni, ha [a Azure Portalon keresztül hoz létre fürtöt](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Ha az alkalmazás tanúsítványait a fürt telepítési ideje szerint szeretné konfigurálni, [Azure Resource Manager használatával kell létrehoznia egy fürtöt][create-cluster-arm]. Az alkalmazáshoz tartozó tanúsítványokat a fürt létrehozása után is felveheti.

## <a name="create-cluster-in-the-azure-portal"></a>Fürt létrehozása a Azure Portalban

Ahhoz, hogy az alkalmazás igényeinek megfelelő üzemi fürtöt hozzon létre, némi tervezést kell végrehajtania, hogy ezzel segítse Önt, javasoljuk, hogy olvassa el és Ismerje meg a [Service Fabric-fürt tervezési szempontjait][service-fabric-cluster-capacity] ismertető dokumentumot. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>A Service Fabric fürterőforrás keresése

Jelentkezzen be az [Azure Portalra][azure-portal].
Az új erőforrás-sablon hozzáadásához kattintson az **erőforrás létrehozása** elemre. A piactéren megkeresheti az Service Fabric-fürtöt a **piactéren** **.**
Válassza ki **Service Fabric fürtöt** a listából.

![Keresse meg a Azure Portal Service Fabric fürtözött sablonját.][SearchforServiceFabricClusterTemplate]

Navigáljon a **Service Fabric-fürt** panelre, és kattintson a **Létrehozás** gombra.

A **Service Fabric-fürt létrehozása** panel a következő négy lépésből áll:

### <a name="1-basics"></a>1. alapismeretek
![Képernyőfelvétel új erőforráscsoport létrehozásáról.][CreateRG]

Az alapvető beállítások panelen meg kell adnia a fürt alapszintű részleteit.

1. Adja meg a fürt nevét.
2. Adja meg a virtuális gépek Távoli asztalhoz tartozó **felhasználónevet** és **jelszót** .
3. Ügyeljen arra, hogy válassza ki azt az **előfizetést** , amelyre telepíteni szeretné a fürtöt, különösen ha több előfizetéssel rendelkezik.
4. Hozzon létre egy új **erőforráscsoportot**. A legjobb, ha ugyanazt a nevet adja, mint a fürt, mert később könnyebben megtalálhatja őket, különösen akkor, ha módosítani kívánja a központi telepítést, vagy törli a fürtöt.
   
   > [!NOTE]
   > Bár úgy is dönthet, hogy meglévő erőforráscsoportot használ, célszerű létrehozni egy új erőforráscsoportot. Így egyszerűen törölheti a fürtöket és az általa használt összes erőforrást.
   > 
   > 
5. Válassza ki azt a **helyet** , amelyben létre szeretné hozni a fürtöt. Ha olyan meglévő tanúsítványt szeretne használni, amelyet már feltöltött egy kulcstartóba, ugyanazt a régiót kell használnia, amelyben a kulcstartó található. 

### <a name="2-cluster-configuration"></a>2. fürtkonfiguráció
![Csomópont típusának létrehozása][CreateNodeType]

Konfigurálja a fürtcsomópontok konfigurációját. A csomópontok típusai határozzák meg a virtuálisgép-méreteket, a virtuális gépek számát és a tulajdonságaikat. A fürt több csomópontos típussal is rendelkezhet, de az elsődleges csomópont típusának (a portálon definiált elsőnek) legalább öt virtuális géppel kell rendelkeznie, mivel ez a csomópont típusa, ahol Service Fabric rendszerszolgáltatások vannak elhelyezve. Ne konfigurálja az **elhelyezési tulajdonságokat** , mert a rendszer automatikusan hozzáadja a "NodeTypeName" alapértelmezett elhelyezési tulajdonságát.

> [!NOTE]
> A több csomópontos típusok gyakori forgatókönyve egy előtér-szolgáltatást és egy háttér-szolgáltatást tartalmazó alkalmazás. Az előtér-szolgáltatást kisebb virtuális gépeken (például D2_V2) helyezi üzembe az internetre nyitott portokkal, és a háttér-szolgáltatást nagyobb méretű virtuális gépeken helyezi üzembe (például D3_V2, D6_V2, D15_V2 stb.) anélkül, hogy az internethez csatlakozó portok meg legyenek nyitva.
> 

1. Válassza ki a csomópont típusának nevét (1 – 12 karakter, amely csak betűket és számokat tartalmaz).
2. Az elsődleges csomópont típusához tartozó virtuális gépek minimális **méretét** a fürthöz választott **tartóssági szint** vezérli. A tartóssági szintek alapértelmezett értéke bronz. További információ a tartósságról: [a Service Fabric fürt tartósságának kiválasztása][service-fabric-cluster-durability].
3. Válassza ki a **virtuális gép méretét**. A D sorozatú virtuális gépek SSD-meghajtókkal rendelkeznek, és az állapot-nyilvántartó alkalmazások esetében kifejezetten ajánlottak. Ne használjon olyan VM SKU-t, amely részleges maggal rendelkezik, vagy kevesebb, mint 10 GB szabad lemezterülettel rendelkezik. A virtuális gép méretének kiválasztásával kapcsolatos segítségért tekintse meg a [Service Fabric-fürt tervezési szempontja című dokumentumot][service-fabric-cluster-capacity] .
4.  Az **egycsomópontos fürtök és a három csomópontos fürtök** csak tesztelési célú használatra szolgálnak. A futó éles munkaterhelések esetében nem támogatottak.
5. Válassza ki a **virtuálisgép-méretezési csoport kezdeti kapacitását** a csomópont típusához. A csomópont-típusokban lévő virtuális gépek számának méretezése vagy kicsinyítése később, de az elsődleges csomópont típusától függően a minimum öt az éles számítási feladatokhoz. A többi csomópont-típushoz legalább egy virtuális gép tartozhat. Az elsődleges csomópont típusú virtuális gépek minimális **száma** a fürt **megbízhatóságát** vezérli.  
6. **Egyéni végpontok** konfigurálása. Ebben a mezőben megadhatja a portok vesszővel tagolt listáját, amelyeket a Azure Load Balancer az alkalmazások számára elérhető nyilvános internetre kíván tenni. Ha például egy webalkalmazást szeretne üzembe helyezni a fürtön, írja be az "80" értéket, hogy engedélyezze a forgalmat a 80-es porton a fürtben. További információ a végpontokról: [kommunikáció az alkalmazásokkal][service-fabric-connect-and-communicate-with-services]
7. **Fordított proxy engedélyezése**.  A [Service Fabric fordított proxy](service-fabric-reverseproxy.md) segíti a Service Fabric-fürtökön futó, a http-végpontokkal rendelkező más szolgáltatásokkal való kommunikációt.
8. Vissza a **fürtkonfiguráció** panelen, a **választható beállítások megjelenítése** alatt konfigurálja a fürt **diagnosztikát**. Alapértelmezés szerint a diagnosztika engedélyezve van a fürtön, hogy segítséget nyújtson a hibák elhárításához. Ha le szeretné tiltani a diagnosztika állapotát, állítsa be az **állapotot** **kikapcsolva** értékre. A diagnosztika kikapcsolása **nem** ajánlott. Ha már létrehozta Application Insights projektet, majd adja meg a kulcsát, hogy az alkalmazás-Nyomkövetések hozzá legyenek irányítva.
9. **DNS-szolgáltatás belefoglalása**.  A [DNS szolgáltatás](service-fabric-dnsservice.md) olyan választható szolgáltatás, amely lehetővé teszi más szolgáltatások keresését a DNS protokoll használatával.
10. Válassza ki a **háló frissítési módját** , amelyre a fürtöt be szeretné állítani. Válassza az **automatikus** lehetőséget, ha azt szeretné, hogy a rendszer automatikusan vegye fel a legújabb elérhető verziót, és próbálja meg frissíteni a fürtöt. Állítsa a módot **manuálisra**, ha a támogatott verziót szeretné kiválasztani. A háló frissítési módjával kapcsolatos további részletekért tekintse meg a [fürt frissítési Service Fabricét ismertető dokumentumot.][service-fabric-cluster-upgrade]

> [!NOTE]
> Csak a Service Fabric támogatott verzióit futtató fürtöket támogatjuk. A **manuális** mód kiválasztásával Ön vállalja a felelősséget, hogy a fürtöt egy támogatott verzióra frissítse.
> 

### <a name="3-security"></a>3. biztonság
![Képernyőkép a Azure Portal biztonsági beállításairól.][BasicSecurityConfigs]

A biztonságos tesztelési fürt létrehozásához egyszerűen biztosítjuk az **alapszintű** lehetőséget. Ha már rendelkezik tanúsítvánnyal, és feltölti a [kulcstartóba](../key-vault/index.yml) (és engedélyezte a kulcstartót az üzembe helyezéshez), használja az **Egyéni** lehetőséget.

#### <a name="basic-option"></a>Alapszintű beállítás
A képernyők használatával hozzáadhat vagy felhasználhat egy meglévő kulcstartót, és hozzáadhat egy tanúsítványt. A tanúsítvány hozzáadása szinkron folyamat, ezért meg kell várnia a tanúsítvány létrehozását.

Az előző folyamat befejezéséig ellenáll a képernyőről való navigálás kísértésének.

![Képernyőfelvétel: a Security (biztonság) lap, amely alapszintű a Key Vault panelen van kiválasztva, és létrehozza a Key Vault panelt.][CreateKeyVault]

Most, hogy létrehozta a kulcstartót, szerkessze a Key Vault hozzáférési szabályzatait. 

![Képernyőfelvétel: a Service Fabric-fürt létrehozása panel 3. lehetőséggel kiválasztott biztonsági beállítással, valamint annak magyarázatával, hogy a kulcstartó nincs engedélyezve.][CreateKeyVault2]

Kattintson a **hozzáférési házirendek szerkesztése**, majd a **speciális hozzáférési szabályzatok megjelenítése** elemre, és engedélyezze az Azure Virtual Machines való hozzáférést az üzembe helyezéshez. Javasoljuk, hogy engedélyezze a sablon központi telepítését is. Ha elvégezte a beállításokat, ne felejtsen el a **Save (Mentés** ) gombra kattintani, és lépjen ki a **hozzáférési házirendek** panelről.

![A képernyőfelvétel megjeleníti a Service Fabric fürt létrehozása panelt, amelyen megnyílik a biztonsági ablaktábla, és megnyílik a hozzáférési házirendek ablaktábla.][CreateKeyVault3]

Adja meg a tanúsítvány nevét, majd kattintson **az OK** gombra.

![Képernyőfelvétel: a Service Fabric-fürt létrehozása panel a korábban megjelenő biztonsági beállítással, de nem tartalmazza azt a magyarázatot, hogy a kulcstartó nincs engedélyezve.][CreateKeyVault4]

#### <a name="custom-option"></a>Egyéni beállítás
Ugorja át ezt a szakaszt, ha már végrehajtotta az **alapszintű** beállítás lépéseit.

![Képernyőfelvétel: a fürt biztonsági beállításainak konfigurálása párbeszédpanel.][SecurityCustomOption]

A Biztonság lap elvégzéséhez szüksége lesz a forrás Key vaultra, a tanúsítvány URL-címére és a tanúsítvány ujjlenyomatára vonatkozó információkra. Ha nincs kéznél, nyisson meg egy böngészőablakot, és a Azure Portal tegye a következőket

1. Navigáljon a Key Vault szolgáltatáshoz.
2. Válassza a "tulajdonságok" fület, és másolja az "erőforrás-azonosító" kifejezést a "forrás Key Vault" értékre a másik böngészőablakban. 

    ![A képernyőfelvételen a kulcstartó Tulajdonságok ablak látható.][CertInfo0]

3. Most válassza a "tanúsítványok" fület.
4. Kattintson a tanúsítvány ujjlenyomatára, amely a verziók lapra lép.
5. Kattintson a jelenlegi verzió alatt látható GUID-azonosítóra.

    ![Képernyőfelvétel: a Key Vault tanúsítványának ablaka][CertInfo1]

6. Ekkor az alábbihoz hasonló képernyőn kell megjelennie. Másolja a hexadecimális SHA-1 ujjlenyomatot a "Tanúsítvány ujjlenyomata" értékre a másik böngészőablakban
7. Másolja a "titkos azonosító" kifejezést a "tanúsítvány URL-címe" értékre más böngészőablakban.

    ![Képernyőfelvétel: a tanúsítvány verziószáma párbeszédpanel, amely a tanúsítvány-azonosító másolására szolgáló lehetőséget jeleníti meg.][CertInfo2]

Jelölje be a **Speciális beállítások konfigurálása** jelölőnégyzetet a **rendszergazdai ügyfél** és a **csak olvasási jogosultsággal** rendelkező ügyfelek tanúsítványainak megadásához. Ezekben a mezőkben adja meg a rendszergazdai ügyféltanúsítvány ujjlenyomatát és a csak olvasási jogosultsággal rendelkező felhasználói ügyféltanúsítvány ujjlenyomatát, ha van ilyen. Amikor a rendszergazdák megpróbálnak csatlakozni a fürthöz, csak akkor kapnak hozzáférést, ha az itt megadott ujjlenyomat-értékekkel egyező tanúsítvánnyal rendelkeznek.  

### <a name="4-summary"></a>4. Összefoglalás

Most már készen áll a fürt üzembe helyezésére. Mielőtt ezt megtenné, töltse le a tanúsítványt, és tekintse meg a hivatkozáshoz tartozó nagy kék információs mezőt. Ügyeljen arra, hogy a tanúsítvány biztonságos helyen maradjon. szüksége lesz rá a fürthöz való kapcsolódáshoz. Mivel a letöltött tanúsítvány nem rendelkezik jelszóval, javasoljuk, hogy adjon hozzá egyet.

A fürt létrehozásának befejezéséhez kattintson a **Létrehozás** gombra. Igény szerint letöltheti a sablont.

![Képernyőfelvétel: az Service Fabric-fürt összefoglalásának létrehozása lap, amely egy tanúsítvány megtekintésére és letöltésére szolgáló hivatkozást mutat be.][Summary]

A létrehozás folyamatát az értesítésekben követheti nyomon. (Kattintson a "harang" ikonra a képernyő jobb felső sarkában található állapotjelző sáv közelében.) Ha a fürt létrehozásakor a **rögzítés a kezdőpulton** elemre kattintott, akkor a **Service Fabric-fürt üzembe helyezése** a **kezdőképernyőn** rögzített állapotban jelenik meg. Ez a folyamat hosszabb időt is igénybe vehet. 

Ahhoz, hogy a PowerShell vagy a parancssori felület használatával hajtsa végre a felügyeleti műveleteket a fürtön, a fürthöz való [csatlakozásról](service-fabric-connect-to-secure-cluster.md)további tudnivalókat itt olvashat:.

## <a name="view-your-cluster-status"></a>A fürt állapotának megtekintése
![Képernyőkép a fürt részleteiről az irányítópulton.][ClusterDashboard]

A fürt létrehozása után megtekintheti a fürtöt a portálon:

1. Lépjen a **Tallózás** elemre, és kattintson **Service Fabric fürtök** lehetőségre.
2. Keresse meg a fürtöt, és kattintson rá.
3. A fürt részletei megjelennek az irányítópulton, beleértve a fürt nyilvános végpontját és egy, a Service Fabric Explorerre mutató hivatkozást.

A fürt irányítópult paneljének **csomópont-figyelő** szakasza a kifogástalan állapotú virtuális gépek számát jelzi. A fürt állapotával kapcsolatos további részletekért tekintse meg [Service Fabric Health Model bevezetését][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric fürtök esetében bizonyos számú csomópontra van szükség, amely mindig a rendelkezésre állás fenntartását és az állapot megőrzését jelenti – a továbbiakban "a kvórum fenntartása". Ezért általában nem biztonságos a fürtben lévő összes gép leállítása, kivéve, ha először a [teljes biztonsági mentést][service-fabric-reliable-services-backup-restore]hajtotta végre az állapotáról.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Távoli kapcsolódás virtuálisgép-méretezési csoport példányaihoz vagy fürtcsomóponton
A fürtben megadott NodeTypes mindegyike egy virtuálisgép-méretezési csoportba kerül, amely beállítja a beállítását. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Következő lépések
Ezen a ponton van egy biztonságos fürt, amely tanúsítványokat használ a felügyeleti hitelesítéshez. Ezután [kapcsolódjon a fürthöz](service-fabric-connect-to-secure-cluster.md) , és Ismerje meg, hogyan [kezelheti az alkalmazási titkokat](service-fabric-application-secret-management.md).  Emellett megismerheti [Service Fabric támogatási lehetőségeit](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
