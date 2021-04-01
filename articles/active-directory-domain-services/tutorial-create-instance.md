---
title: Oktatóanyag – Azure Active Directory Domain Services felügyelt tartomány létrehozása | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy Azure Active Directory Domain Services felügyelt tartományt a Azure Portal használatával.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 945c131394a0a3c6273f79044c8500a2feba70fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618144"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain"></a>Oktatóanyag: Azure Active Directory Domain Services felügyelt tartomány létrehozása és konfigurálása

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja fel. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi, hogy a felhasználók a vállalati hitelesítő adataikkal jelentkezzenek be, és meglévő csoportokat és felhasználói fiókokat is használhatnak az erőforrásokhoz való hozzáférés biztosításához.

Felügyelt tartományt a Hálózatkezelés és a szinkronizálás alapértelmezett konfigurációs beállításaival hozhat létre, vagy [manuálisan is megadhatja ezeket a beállításokat][tutorial-create-instance-advanced]. Ez az oktatóanyag bemutatja, hogyan hozhat létre és konfigurálhat egy Azure AD DS felügyelt tartományt az alapértelmezett beállításokkal a Azure Portal használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek megismerése
> * Felügyelt tartomány létrehozása
> * Jelszókivonat szinkronizálásának engedélyezése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Az Azure AD DS engedélyezéséhez *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.
* Az Azure-előfizetésben *közreműködő* jogosultságokkal kell rendelkeznie a szükséges Azure AD DS-erőforrások létrehozásához.

Bár az Azure AD DS esetében nem szükséges, az Azure AD-bérlő számára ajánlott az önkiszolgáló [jelszó-visszaállítás (SSPR) konfigurálása][configure-sspr] . A felhasználók SSPR nélkül is módosíthatják a jelszavukat, de a SSPR segít, ha elfelejtik a jelszavukat, és vissza kell állítani.

> [!IMPORTANT]
> A felügyelt tartomány létrehozása után nem helyezheti át a felügyelt tartományt más erőforráscsoporthoz, virtuális hálózatra, előfizetésre stb. Ügyeljen arra, hogy a felügyelt tartomány központi telepítésekor a legmegfelelőbb előfizetést, erőforráscsoportot, régiót és virtuális hálózatot válassza ki.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban a felügyelt tartományt a Azure Portal használatával hozza létre és konfigurálja. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-a-managed-domain"></a>Felügyelt tartomány létrehozása

A **Azure ad Domain Services engedélyezése** varázsló elindításához hajtsa végre a következő lépéseket:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Adja meg a *tartományi szolgáltatásokat* a keresési sávban, majd válassza a *Azure ad Domain Services* lehetőséget a keresési javaslatok közül.
1. A Azure AD Domain Services lapon válassza a **Létrehozás** lehetőséget. Elindul a **Azure ad Domain Services engedélyezése** varázsló.
1. Válassza ki azt az Azure- **előfizetést** , amelyben létre szeretné hozni a felügyelt tartományt.
1. Válassza ki azt az **erőforráscsoportot** , amelyhez a felügyelt tartománynak tartoznia kell. Válassza az **új létrehozása** lehetőséget, vagy válasszon ki egy meglévő erőforráscsoportot.

Felügyelt tartomány létrehozásakor meg kell adnia egy DNS-nevet. A DNS-név kiválasztásakor a következő szempontokat kell figyelembe venni:

* **Beépített tartomány neve:** Alapértelmezés szerint a rendszer a könyvtár beépített tartománynevét használja (a *. onmicrosoft.com* utótagot). Ha engedélyezni szeretné a biztonságos LDAP-hozzáférést a felügyelt tartományhoz az interneten keresztül, nem hozhat létre digitális tanúsítványt a kapcsolat védelméhez ezzel az alapértelmezett tartománnyal. A Microsoft tulajdonosa a *. onmicrosoft.com* tartomány, így a hitelesítésszolgáltató (CA) nem bocsát ki tanúsítványt.
* **Egyéni tartománynevek:** A leggyakoribb módszer egy egyéni tartománynév megadása, amely általában az egyik, amelyet már birtokol, és irányítható. Ha irányítható, egyéni tartományt használ, a forgalom az alkalmazások támogatásához szükséges módon képes megfelelően átáramlani.
* **Nem irányítható tartomány utótagja:** Általában azt javasoljuk, hogy kerülje a nem átirányítható tartománynevek utótagját, például a *contoso. local* nevet. A *. local* utótag nem irányítható, és a DNS-feloldással kapcsolatos problémákat okozhat.

> [!TIP]
> Ha egyéni tartománynevet hoz létre, gondoskodjon a meglévő DNS-névterekről. Azt javasoljuk, hogy a tartománynevet a meglévő Azure-beli vagy helyszíni DNS-névtértől elkülönítve használja.
>
> Ha például meglévő DNS- *contoso.com* rendelkezik, hozzon létre egy felügyelt tartományt a *aaddscontoso.com* egyéni tartománynevével. Ha biztonságos LDAP-t kell használnia, regisztrálnia kell az egyéni tartománynevet a szükséges tanúsítványok létrehozásához.
>
> Előfordulhat, hogy létre kell hoznia néhány további DNS-rekordot a környezetében lévő más szolgáltatásokhoz, vagy feltételes DNS-továbbítókat a környezet meglévő DNS-neve között. Ha például olyan webkiszolgálót futtat, amely a gyökér DNS-nevet használja, akkor olyan elnevezési ütközések lehetnek, amelyek további DNS-bejegyzéseket igényelnek.
>
> Ezekben az oktatóanyagokban és útmutatókban a *aaddscontoso.com* egyéni tartományát használjuk rövid példaként. Az összes parancsban adja meg a saját tartománynevét.

A DNS-név következő korlátozásai is érvényesek:

* **Tartományi előtagra vonatkozó korlátozások:** Nem hozhat létre 15 karakternél hosszabb előtaggal rendelkező felügyelt tartományt. A megadott tartománynév előtagja (például a *aaddscontoso.com* tartomány *aaddscontoso* ) legalább 15 karakterből állhat.
* **Hálózati név ütközése:** A felügyelt tartomány DNS-tartományneve még nem létezik a virtuális hálózaton. Konkrétan a következő helyzetekben érdemes megkeresni a nevek ütközését:
    * Ha már rendelkezik egy Active Directory tartománnyal ugyanazzal a DNS-tartománynévvel az Azure-beli virtuális hálózaton.
    * Ha a virtuális hálózat, amelyen engedélyezni szeretné a felügyelt tartományt, VPN-kapcsolattal rendelkezik a helyszíni hálózattal. Ebben az esetben győződjön meg arról, hogy nem rendelkezik ugyanazzal a DNS-tartománynévvel rendelkező tartománnyal a helyszíni hálózaton.
    * Ha az Azure-beli virtuális hálózaton már van ilyen nevű Azure Cloud Service-szolgáltatás.

Felügyelt tartomány létrehozásához hajtsa végre a Azure Portal *alapismeretek* ablakában található mezőket:

1. Adja meg a felügyelt tartományhoz tartozó **DNS-tartománynevet** , figyelembe véve az előző pontokat.
1. Válassza ki azt az Azure- **helyet** , amelyben létre kívánja hozni a felügyelt tartományt. Ha olyan régiót választ, amely támogatja az Azure Availability Zones-t, az Azure AD DS erőforrásai a további redundancia érdekében a zónák között oszlanak meg.

    > [!TIP]
    > A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében minden engedélyezett régióban legalább három elkülönített zóna található.
    >
    > Nem kell konfigurálnia az Azure AD DS a zónák közötti elosztására. Az Azure platform automatikusan kezeli az erőforrások zónájának eloszlását. További információért és a régiók rendelkezésre állásának megtekintéséhez lásd: [Mi a Availability Zones az Azure-ban?][availability-zones]

1. Az **SKU** meghatározza a létrehozható erdőszintű megbízhatósági kapcsolatok teljesítményét, biztonsági mentési gyakoriságát és maximális számát. Az SKU-t a felügyelt tartomány létrehozása után is módosíthatja, ha az üzleti igények vagy követelmények megváltoznak. További információ: [Azure AD DS SKU-fogalmak][concepts-sku].

    Ebben az oktatóanyagban válassza ki a *szabványos* SKU-t.
1. Az *erdők* egy Active Directory Domain Services által az egy vagy több tartomány csoportosításához használt logikai szerkezetek. Alapértelmezés szerint a felügyelt tartomány *felhasználói* erdőként jön létre. Ez a típusú erdő az Azure AD összes objektumát szinkronizálja, beleértve a helyszíni AD DS környezetben létrehozott felhasználói fiókokat is.

    Az *erőforrás* -erdő csak a közvetlenül az Azure ad-ben létrehozott felhasználókat és csoportokat szinkronizálja. Az *erőforrás* -erdőkkel kapcsolatos további információkért, beleértve az egyiket, és hogyan hozhat létre erdőszintű megbízhatósági kapcsolatot a helyszíni AD DS-tartományokkal, tekintse meg az [Azure AD DS Resource Forests – áttekintés][resource-forests]című témakört.

    Ebben az oktatóanyagban válassza a *felhasználói* erdő létrehozása lehetőséget.

    ![Azure AD Domain Services felügyelt tartomány alapszintű beállításainak konfigurálása](./media/tutorial-create-instance/basics-window.png)

A felügyelt tartományok gyors létrehozásához válassza a **felülvizsgálat + létrehozás** lehetőséget a további alapértelmezett konfigurációs beállítások elfogadásához. A létrehozási lehetőség kiválasztása esetén a következő alapértelmezett beállítások vannak konfigurálva:

* Létrehoz egy *aadds-vnet* nevű virtuális hálózatot, amely a *10.0.2.0/24* IP-címtartományt használja.
* Létrehoz egy *aadds-alhálózat* nevű alhálózatot a *10.0.2.0/24* IP-címtartomány használatával.
* Szinkronizálja az Azure AD *összes* felhasználóját a felügyelt tartományba.

Válassza a **felülvizsgálat + létrehozás** lehetőséget az alapértelmezett konfigurációs beállítások elfogadásához.

## <a name="deploy-the-managed-domain"></a>A felügyelt tartomány üzembe helyezése

A varázsló **Összefoglalás** lapján tekintse át a felügyelt tartomány konfigurációs beállításait. A varázsló bármelyik lépésére visszatérhet a módosítások elvégzéséhez. Ha egy felügyelt tartományt egy másik Azure AD-bérlőre kíván áttelepíteni, és ezeket a konfigurációs beállításokat szeretné használni, **letöltheti az automatizáláshoz használható sablont** is.

1. A felügyelt tartomány létrehozásához válassza a **Létrehozás** lehetőséget. Megjelenik egy megjegyzés, amely szerint bizonyos konfigurációs beállítások, például a DNS-név vagy a virtuális hálózat nem módosítható, ha az Azure-AD DS felügyelve lett létrehozva. A folytatáshoz kattintson **az OK gombra**.
1. A felügyelt tartomány kiépítés folyamata akár egy órát is igénybe vehet. Egy értesítés jelenik meg a portálon, amely az Azure AD DS üzemelő példányának állapotát mutatja. Válassza ki az értesítést, és tekintse meg az üzembe helyezés részletes folyamatát.

    ![Értesítés a telepítés Azure Portal folyamatban](./media/tutorial-create-instance/deployment-in-progress.png)

1. A lap a telepítési folyamat frissítéseivel töltődik be, beleértve a címtárban lévő új erőforrások létrehozását is.
1. Válassza ki az erőforráscsoportot, például *myResourceGroup*, majd válassza ki a felügyelt tartományt az Azure-erőforrások listájából, például *aaddscontoso.com*. Az **Áttekintés** lapon látható, hogy a felügyelt tartomány jelenleg *telepítve* van. A felügyelt tartományt nem lehet a teljes kiépítés előtt konfigurálni.

    ![Tartományi szolgáltatások állapota a kiépítési állapotban](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Ha a felügyelt tartomány teljes mértékben kiépítve van, az **Áttekintés** lapon a tartomány állapota *fut* értékre látható.

    ![Tartományi szolgáltatások állapota sikeres kiépítés után](./media/tutorial-create-instance/successfully-provisioned.png)

> [!IMPORTANT]
> A felügyelt tartomány társítva van az Azure AD-bérlőhöz. A kiépítési folyamat során az Azure AD DS két, *tartományvezérlői szolgáltatásokat* és *AzureActiveDirectoryDomainControllerServices* nevű vállalati alkalmazást hoz létre az Azure ad-bérlőben. Ezek a vállalati alkalmazások a felügyelt tartomány kiszolgálásához szükségesek. Ne törölje ezeket az alkalmazásokat.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Az Azure virtuális hálózat DNS-beállításainak frissítése

Az Azure AD DS sikeres üzembe helyezése után konfigurálja úgy a virtuális hálózatot, hogy engedélyezze más csatlakoztatott virtuális gépek és alkalmazások számára a felügyelt tartomány használatát. A kapcsolat megadásához frissítse a virtuális hálózat DNS-kiszolgálójának beállításait úgy, hogy azokra a két IP-címre mutasson, ahol a felügyelt tartomány telepítve van.

1. A felügyelt tartomány **Áttekintés** lapján néhány **szükséges konfigurációs lépés** látható. Az első konfigurációs lépés a DNS-kiszolgáló beállításainak frissítése a virtuális hálózathoz. A DNS-beállítások megfelelő konfigurálása után ez a lépés már nem jelenik meg.

    A felsorolt címek a virtuális hálózatban használt tartományvezérlők. Ebben a példában ezek a címek a *10.0.2.4* és a *végpontjául szolgáló*. Később megkeresheti ezeket az IP-címeket a **Tulajdonságok** lapon.

    ![A virtuális hálózat DNS-beállításainak konfigurálása a Azure AD Domain Services IP-címekkel](./media/tutorial-create-instance/configure-dns.png)

1. A DNS-kiszolgáló virtuális hálózatra vonatkozó beállításainak frissítéséhez kattintson a **Konfigurálás** gombra. A DNS-beállítások konfigurálása automatikusan megtörténik a virtuális hálózathoz.

> [!TIP]
> Ha az előző lépésekben egy meglévő virtuális hálózatot jelölt ki, a hálózathoz csatlakozó virtuális gépek csak újraindítás után kapják meg az új DNS-beállításokat. A virtuális gépeket a Azure Portal, Azure PowerShell vagy az Azure CLI használatával indíthatja újra.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Felhasználói fiókok engedélyezése az Azure AD DS

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS a jelszó-kivonatokat az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban kell megadni. Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

> [!NOTE]
> A megfelelő konfigurálást követően a rendszer a használható jelszavak kivonatait a felügyelt tartományban tárolja. Ha törli a felügyelt tartományt, az ezen a ponton tárolt jelszó-kivonatokat is törli a rendszer.
>
> A szinkronizált hitelesítő adatokat az Azure AD-ben nem lehet újra felhasználni, ha később létrehoz egy felügyelt tartományt – újra kell konfigurálnia a jelszó-kivonatok szinkronizálását, hogy a rendszer újra tárolja a jelszó-kivonatokat. Korábban a tartományhoz csatlakoztatott virtuális gépek vagy felhasználók nem fognak tudni azonnal hitelesíteni magukat – az Azure AD-nek a jelszó-kivonatokat az új felügyelt tartományban kell kiállítania és tárolnia.
>
> További információ: jelszó- [kivonatolási szinkronizálási folyamat az Azure AD DS és Azure ad Connect][password-hash-sync-process].

A jelszó-kivonatok létrehozásához és tárolásához szükséges lépések eltérnek az Azure AD-ben létrehozott kizárólag felhőalapú felhasználói fiókok és a helyszíni címtárból a Azure AD Connect használatával szinkronizált felhasználói fiókok esetében.

A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Ezek a felhasználói fiókok nem szinkronizálhatók a helyszíni címtárból.

> Ebben az oktatóanyagban egy egyszerű, csak felhőalapú felhasználói fiókkal dolgozunk. További információ a Azure AD Connect használatához szükséges további lépésekről: [jelszó-kivonatolások szinkronizálása a helyszíni ad-ből a felügyelt tartományba szinkronizált felhasználói fiókokhoz][on-prem-sync].

> [!TIP]
> Ha az Azure AD-bérlő csak felhőalapú felhasználókat és felhasználókat tartalmaz a helyszíni AD-ből, mindkét lépést végre kell hajtania.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak meg kell változtatniuk a jelszavukat, mielőtt használniuk tudják az Azure AD DS-t. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához. A fiók nincs szinkronizálva az Azure AD-ből az Azure AD DSig, amíg meg nem változtatják a jelszót. Vagy járjon le a bérlő összes olyan felhőalapú felhasználójának jelszavával, akinek az Azure AD DS-t kell használnia, amely a következő bejelentkezéskor kényszeríti a jelszót, vagy utasítja a Felhőbeli felhasználókat, hogy manuálisan módosítsák a jelszavukat. Ebben az oktatóanyagban manuálisan módosítunk egy felhasználói jelszót.

Mielőtt egy felhasználó visszaállíthatja a jelszavát, az Azure AD-bérlőt [konfigurálni kell az önkiszolgáló jelszó-visszaállításhoz][configure-sspr].

A csak felhőalapú felhasználók jelszavának módosításához a felhasználónak a következő lépéseket kell elvégeznie:

1. Nyissa meg az Azure AD hozzáférési panel lapját a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](./media/tutorial-create-instance/select-profile.png)

1. A **profil** lapon válassza a **jelszó módosítása** lehetőséget.
1. A **jelszó módosítása** lapon adja meg a meglévő (régi) jelszavát, majd írja be és erősítse meg az új jelszót.
1. Válassza a **Küldés** lehetőséget.

Néhány percet vesz igénybe, miután módosította az új jelszóhoz tartozó jelszót, hogy az Azure AD DS használható legyen, és hogy sikeresen bejelentkezzen a felügyelt tartományhoz csatlakoztatott számítógépekre.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A felügyelt tartomány DNS-követelményeinek megismerése
> * Felügyelt tartomány létrehozása
> * Rendszergazda felhasználók hozzáadása a tartományi felügyelethez
> * Felhasználói fiókok engedélyezése az Azure AD DS számára és jelszó-kivonatok előállítása

A tartományhoz csatlakozó virtuális gépek és a felügyelt tartományt használó alkalmazások központi telepítése előtt állítson be egy Azure-beli virtuális hálózatot az alkalmazások számítási feladataihoz.

> [!div class="nextstepaction"]
> [Azure-beli virtuális hálózat konfigurálása alkalmazás-munkaterheléshez a felügyelt tartomány használatához](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
