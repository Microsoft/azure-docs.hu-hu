---
title: Gyorsított labor-fiók telepítési útmutatója Azure Lab Services
description: Ez az útmutató segítséget nyújt a rendszergazdáknak az iskolán belüli használatra szolgáló labor-fiókok gyors létrehozásában.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669389"
---
# <a name="lab-account-setup-guide"></a>Lab-fiók telepítési útmutatója
Ha Ön rendszergazda, a Azure Lab Services-környezet beállítása előtt először létre kell hoznia egy *Lab-fiókot* az Azure-előfizetésén belül. A labor-fiókok egy vagy több laborhoz tartozó tárolók, és a beállítás csak néhány percet vesz igénybe.

Ez az útmutató három szakaszt tartalmaz:
-  Előfeltételek
-  A labor-fiók beállításainak megtervezése
-  A labor-fiók beállítása

## <a name="prerequisites"></a>Előfeltételek
A következő fejezetek felvázolják, hogy mit kell tennie, mielőtt beállít egy labor-fiókot.


### <a name="access-your-azure-subscription"></a>Hozzáférés az Azure-előfizetéshez
Labor-fiók létrehozásához hozzá kell férnie egy Azure-előfizetéshez, amely már be van állítva az iskolájában. Lehet, hogy az iskolája egy vagy több előfizetéssel rendelkezik. Az előfizetés segítségével kezelheti az Azure-erőforrások és-szolgáltatások, például a labor-fiókok számlázási és biztonsági szolgáltatásait.  Az Azure-előfizetéseket általában az informatikai részleg kezeli.  További információkért tekintse meg a [Azure Lab Services-Administrator útmutató](./administrator-guide.md#subscription)"előfizetés" című szakaszát.

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Megbecsülheti, hogy hány virtuális GÉPET és virtuálisgép-méretet kell megadnia
Fontos tudni, hogy hány [virtuális gép (VM) és virtuálisgép-méret](./administrator-guide.md#vm-sizing) szükséges az iskolai laborban. 

A laborok és a képek strukturálásával kapcsolatos útmutatásért tekintse meg a [fizikai laborból a Azure Lab Servicesre való áttérés](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)utáni blogbejegyzést.

A laborok struktúrájával kapcsolatos további útmutatásért tekintse meg a [Azure Lab Services-Administrator útmutató](./administrator-guide.md#lab)"labor" szakaszát.

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Az előfizetés virtuális gépek korlátai és a regionális virtuálisgép-kapacitás ismertetése
A virtuális gépek számának és a laborok virtuálisgép-méretének becslése után a következőket kell tennie:

- Győződjön meg arról, hogy az Azure-előfizetése kapacitásának korlátja lehetővé teszi a virtuális gépek számát és a laborban használni kívánt virtuálisgép-méretet.
- Hozzon létre egy olyan régión belüli labor-fiókot, amely rendelkezik elegendő rendelkezésre álló virtuálisgép-kapacitással.

További információ: a [virtuális gépek előfizetési korlátai és a regionális kapacitás](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Döntse el, hogy hány Lab-fiókot hoz létre

A gyors kezdéshez hozzon létre egyetlen Lab-fiókot a saját erőforráscsoporthoz.  Később szükség szerint további labor-fiókokat és erőforráscsoportokat is létrehozhat. Előfordulhat például, hogy végül egy labor-fiókkal és egy erőforráscsoport-erőforrással rendelkezik, így egyértelműen elkülönítheti a költségeket. 

A labor-fiókokkal, az erőforráscsoportok és a költségek elválasztásával kapcsolatos további információkért lásd:
- [Azure Lab Services – rendszergazdai útmutató](./administrator-guide.md#resource-group) "erőforráscsoport" szakasza
- [Azure Lab Services – rendszergazdai útmutató](./administrator-guide.md#lab-account) "labor-fiók" szakasza 
- [Cost Management Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>A labor-fiók beállításainak megtervezése

A labor-fiókok beállításainak megtervezéséhez vegye figyelembe az alábbi kérdéseket.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Kinek kell a labor-fiók tulajdonosai és közreműködői?

Az iskolai rendszergazdák általában a labor-fiók tulajdonosi és közreműködői szerepkörein vesznek részt. Ezek a szerepkörök felelősek azoknak a házirendeknek a kezeléséért, amelyek a labor-fiókban található összes laboratóriumra érvényesek. A labor-fiókot létrehozó személy automatikusan tulajdonosa. További tulajdonosokat és közreműködőket adhat hozzá az előfizetéséhez társított Azure Active Directory (Azure AD) bérlőhöz. 

A labor-fiók tulajdonosi és közreműködői szerepköreivel kapcsolatos további információkért tekintse meg az [Azure Lab Services-Administrator útmutató](./administrator-guide.md#manage-identity)"identitás kezelése" című szakaszát.

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

A labor felhasználói csak egyetlen listát látnak a virtuális gépekről, amelyekhez hozzáféréssel rendelkeznek a Azure Lab Services Azure AD-bérlők között.

### <a name="who-will-be-allowed-to-create-labs"></a>Kik számára engedélyezett a laborok létrehozása?

Dönthet úgy is, hogy az IT-csapattal vagy oktatókkal hozza létre a laborokat. A laborok létrehozásához ezeket a személyeket hozzá kell rendelnie a Lab-fiókon belül a labor létrehozói szerepkörhöz. Ezt a szerepkört általában az iskolai előfizetéséhez társított Azure AD-bérlőből rendeli hozzá. Aki létrehoz egy labort, a rendszer automatikusan a labor tulajdonosaként rendeli hozzá.  

A labor létrehozói szerepkörről a [Azure Lab Services-Administrator útmutató](./administrator-guide.md#manage-identity)"identitás kezelése" szakaszában talál további információt.

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kik is jogosultak a laborok felügyeletére?

AZT is megteheti, hogy a own\manage Labs-t és oktatókat is kiválaszthatja *anélkül* , hogy a laborokat létre tudja hozni.  Ebben az esetben az előfizetéséhez tartozó Azure AD-bérlőhöz tartozó felhasználókat a meglévő Labs tulajdonosának vagy közreműködőinek kell megadnia.  

A labor tulajdonosi és közreműködői szerepköreivel kapcsolatos további információkért tekintse meg az [Azure Lab Services-Administrator útmutató](./administrator-guide.md#manage-identity)"identitás kezelése" című szakaszát.

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Szeretné menteni a képeket, és megoszthatja őket a laborban?

A megosztott képkatalógus egy olyan szolgáltatás, amely a képek mentésére és megosztására használható. Azon osztályok esetében, amelyeknek ugyanazt a rendszerképet kell használniuk, a labor létrehozói létrehozzák a rendszerképet, majd exportálják azt egy megosztott képgyűjteménybe.  Miután exportált egy képet a megosztott rendszerkép-katalógusba, felhasználhatja az új Labs létrehozásához.

Előfordulhat, hogy létre szeretné hozni a lemezképeket a fizikai környezetben, majd importálja őket egy megosztott képgyűjteménybe. További információkért tekintse meg az [Egyéni rendszerkép importálása megosztott képgyűjteménybe](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)című blogbejegyzésben található bejegyzést.

Ha úgy dönt, hogy a megosztott rendszerkép-katalógus szolgáltatást használja, létre kell hoznia vagy csatolnia kell egy megosztott képtárat a labor-fiókjához. Most elhalaszthatja ezt a döntést, mert egy megosztott képkatalógus bármikor csatolható egy labor-fiókhoz.  

További információkért lásd:
- Az [Azure Lab Services-Administrator útmutató](./administrator-guide.md#shared-image-gallery) "megosztott képgyűjtemény" szakasza
- [Azure Lab Services – rendszergazdai útmutató](./administrator-guide.md#pricing) "díjszabás" szakasza

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Milyen rendszerképeket használnak a laborok az Azure Marketplace-en?

Az Azure Marketplace több száz olyan képet biztosít, amely lehetővé teszi, hogy a labor-készítők a laborok létrehozásához használhassák őket. Egyes képek tartalmazhatnak mindent, amit a labor már igényel. Más esetekben a rendszerképeket kiindulási pontként használhatja, majd a tesztkörnyezet létrehozója testre is szabhatja azt további alkalmazások vagy eszközök telepítésével.

Ha nem tudja, hogy mely rendszerképekre van szüksége, később is visszatérhet, hogy engedélyezze őket. Az elérhető képek közül a legjobb módszer, ha először egy tesztkörnyezet-fiókot hoz létre. Ez hozzáférést biztosít, így áttekintheti az elérhető rendszerképek és azok tartalmának listáját.  

További információ: [a labor-készítők számára elérhető Azure Marketplace-lemezképek megadása](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>A labor virtuális gépeknek más Azure-vagy helyszíni erőforrásokhoz is hozzá kell férniük?

Labor-fiók beállításakor a labor-fiókját is használhatja egy virtuális hálózattal.  Ne feledje, hogy a virtuális hálózatnak és a labor-fióknak ugyanabban a régióban kell lennie.  Annak eldöntéséhez, hogy szükség van-e egy virtuális hálózatra, vegye figyelembe a következő forgatókönyveket:

- **Hozzáférés a licenckiszolgálóval**
  
   Az Azure Marketplace-rendszerképek használata esetén az operációs rendszer licencének díja a labor Services díjszabásában van beépítve. Nem kell azonban licenceket megadnia magának az operációs rendszernek. A telepített további szoftverekhez és alkalmazásokhoz szükség szerint meg kell adnia egy licencet.  A licenckiszolgáló elérése:
   - Dönthet úgy, hogy egy helyszíni licenckiszolgálóra csatlakozik.  A helyszíni licenckiszolgálóra való csatlakozáshoz további beállítások szükségesek.
   - Egy másik lehetőség, amely gyorsabban beállítható, egy Azure-beli virtuális gépen futtatott licenckiszolgáló létrehozása.  Az Azure-beli virtuális gép egy olyan virtuális hálózaton belül található, amelyet a labor-fiókjával együtt kezel.

- **Hozzáférés más helyszíni erőforrásokhoz, például fájlmegosztás vagy adatbázis**

   Általában egy virtuális hálózatot hoz létre, amely helyek közötti virtuális hálózati átjáró használatával biztosítja a helyszíni erőforrásokhoz való hozzáférést. Az ilyen típusú környezet beállítása további időt is igénybe vesz.

- **Hozzáférés más Azure-erőforrásokhoz, amelyek a virtuális hálózaton kívül találhatók**

   Ha olyan Azure-erőforrások elérésére van szüksége, amelyek *nem* biztonságosak a virtuális hálózaton belül, a nyilvános interneten keresztül is elérheti őket, anélkül, hogy bármilyen társat kellene tennie.

   A virtuális hálózatokról további információt a következő témakörben talál:
   - A "virtuális hálózat" szakasz az [architektúra alapjairól Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [A labor-hálózat összekötése egy társ virtuális hálózattal Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Tesztkörnyezet létrehozása megosztott erőforrással Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>A labor-fiók beállítása

A tervezés befejezése után készen áll a labor-fiók beállítására. Ugyanezeket a lépéseket alkalmazhatja [Azure Lab Services a Teams](./lab-services-within-teams-overview.md)szolgáltatásban való beállításához.

1. **Hozza létre a labor-fiókját**. Útmutatásért lásd: [Lab-fiók létrehozása](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Az elnevezési konvenciókkal kapcsolatos információkért tekintse meg a [Azure Lab Services-Administrator útmutató](./administrator-guide.md#naming)"névadás" című szakaszát.

1. **Adja hozzá a felhasználókat a labor létrehozói szerepkörhöz**. Útmutatásért lásd: [felhasználók hozzáadása a labor létrehozói szerepkörhöz](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Kapcsolódjon egy társ virtuális hálózathoz**. Útmutatásért lásd: [a tesztkörnyezet hálózatának összekötése egyenrangú virtuális hálózattal](./how-to-connect-peer-virtual-network.md).

   Előfordulhat, hogy [a labor virtuális gépek címtartomány konfigurálására](./how-to-configure-lab-accounts.md)vonatkozó utasításokat is meg kell adnia.

1. **Képek engedélyezése és áttekintése**. Útmutatásért lásd: [annak megadása, hogy mely Azure Marketplace-lemezképek érhetők el a labor-készítők](./specify-marketplace-images.md)számára.

   Az egyes Azure Marketplace-képek tartalmának áttekintéséhez válassza ki a rendszerkép nevét. Az alábbi képernyőkép például az Ubuntu Data Science VM rendszerképének részleteit jeleníti meg:

   ![Képernyőkép az Azure Marketplace-en való áttekintésre elérhető rendszerképek listájáról.](./media/setup-guide/review-marketplace-images.png)

   Ha egy megosztott képtárat csatolnak a labor-fiókjához, és engedélyezni szeretné az egyéni lemezképek megosztását a labor-készítők számára, hajtsa végre a hasonló lépéseket az alábbi képernyőképen látható módon:

   ![Képernyőkép az engedélyezett Egyéni rendszerképek listájáról egy megosztott Képtárban.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Következő lépések

A laborok beállításával és kezelésével kapcsolatos további információkért lásd:

- [Tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)  
- [Labor telepítési útmutatója](setup-guide.md)
