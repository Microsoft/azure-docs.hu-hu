---
title: A szolgáltatásként nyújtott platform (a-Service) szolgáltatás használata Azure DevTest Labs
description: Ismerje meg, hogyan használhatók a szolgáltatásként nyújtott platform (pass) szolgáltatások a Azure DevTest Labsban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478739"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>A szolgáltatásként nyújtott platform (a-Service) szolgáltatás használata Azure DevTest Labs
A DevTest Labs a környezetek funkció használatával támogatja a Pásti használatát. A DevTest Labs környezetei a git-tárházban előre konfigurált Azure Resource Manager-sablonokkal támogatottak. A környezetek a Pásti és a IaaS erőforrásokat is tartalmazhatják. Lehetővé teszik olyan összetett rendszerek létrehozását, amelyek olyan Azure-erőforrásokat is tartalmazhatnak, mint például a virtuális gépek, az adatbázisok, a virtuális hálózatok és a webalkalmazások, amelyek testre szabhatók a közös munkához. Ezek a sablonok lehetővé teszik az egységes üzembe helyezést és a környezetek jobb kezelését a forráskód-vezérlés használatával. 

Egy megfelelően beállított rendszer a következő forgatókönyveket teszi lehetővé: 

- A fejlesztők független és több környezettel rendelkeznek
- Különböző konfigurációk tesztelése aszinkron módon
- Integráció az előkészítési és a termelési folyamatba sablon módosítása nélkül
- Az azonos laborban található fejlesztési gépek és környezetek is javítják a felügyelet és a Cost-jelentéskészítés egyszerűségét.  

A DevTest Labs erőforrás-szolgáltató erőforrásokat hoz létre a labor felhasználó nevében, így nem kell további engedélyeket adni a tesztkörnyezet felhasználójának a Pásti-erőforrások használatának engedélyezéséhez. Az alábbi képen egy Service Fabric-fürt látható környezetként a laborban.

![Service Fabric-fürt környezetként](./media/create-environment-service-fabric-cluster/cluster-created.png)

A környezetek beállításával kapcsolatos további információkért lásd: [több virtuális gépre kiterjedő környezetek létrehozása és a Azure Resource Manager-sablonokkal rendelkező Pásti-erőforrások](devtest-lab-create-environment-from-arm.md). A DevTest Labs Azure Resource Manager sablonok nyilvános tárházával rendelkezik, amelyekkel környezetek hozhatók létre anélkül, hogy saját kezűleg kellene csatlakozniuk egy külső GitHub-forráshoz. A nyilvános környezetekről további információt a [nyilvános környezetek konfigurálása és használata Azure DevTest Labsban](devtest-lab-configure-use-public-environments.md)című témakörben talál.

A nagyméretű szervezeteknél a fejlesztési csapatok jellemzően olyan környezeteket biztosítanak, mint például a testreszabott/elkülönített tesztelési környezetek. Lehetnek olyan környezetek, amelyeket az összes csapat használhat egy üzleti egységben vagy egy divízióban. Előfordulhat, hogy az IT-csoport meg szeretné adni a környezetét, amelyet a szervezet összes csapata használhat.  

## <a name="customizations"></a>Testreszabások

#### <a name="sandbox"></a>Sandbox 
A labor tulajdonosa testreszabhatja a tesztkörnyezet környezetét, hogy megváltoztassa a felhasználó szerepkörét a **Readerből** az erőforráscsoporthoz tartozó **közreműködővé** . Ez a funkció a labor **beállításai** oldalon található a tesztkörnyezet **konfigurációjában és házirendjeiben** . A szerepkör ezen módosítása lehetővé teszi, hogy a felhasználó erőforrásokat adjon hozzá vagy távolítson el az adott környezetben. Ha továbbra is szeretné korlátozni a hozzáférést, használja az Azure-szabályzatokat. Ez a funkció lehetővé teszi az erőforrások és a konfiguráció testreszabását az előfizetés szintjén való hozzáférés nélkül.

#### <a name="custom-tokens"></a>Egyéni tokenek
Vannak olyan egyéni tesztkörnyezet-információk, amelyek az erőforráscsoport területén kívül esnek, és a sablon által elérhető környezetekre vonatkoznak. Íme néhány közülük: 

- Tesztkörnyezet hálózati azonosítása
- Hely
- A Storage-fiók, amelyben a Resource Manager-sablonok fájljai vannak tárolva. 
 
#### <a name="lab-virtual-network"></a>Tesztkörnyezet virtuális hálózata
A [Tesztkörnyezet virtuális hálózatához csatlakozó környezetek](connect-environment-lab-virtual-network.md) című cikk azt ismerteti, hogyan lehet módosítani a Resource Manager-sablont a `$(LabSubnetId)` jogkivonat használatára. Környezet létrehozásakor a `$(LabSubnetId)` jogkivonatot az első alhálózati jel váltja fel, ahol a **virtuális gép létrehozási** beállítása beállítás értéke TRUE ( **igaz**). Lehetővé teszi, hogy a környezet a korábban létrehozott hálózatokat használja. Ha ugyanazokat a Resource Manager-sablonokat szeretné használni a tesztelés előkészítési és éles környezetekben, használja `$(LabSubnetId)` alapértelmezett értékként egy Resource Manager-sablon paraméterében. 

#### <a name="environment-storage-account"></a>Környezeti Storage-fiók
A DevTest Labs támogatja a [beágyazott Resource Manager-sablonok](../azure-resource-manager/templates/linked-templates.md)használatát. A [[beágyazott Azure Resource Manager-sablonok üzembe helyezése tesztelési környezetek számára című](deploy-nested-template-environments.md) cikk azt ismerteti, hogyan használhatók  `_artifactsLocation` és `_artifactsLocationSasToken` tokenek egy URI-hoz egy Resource Manager-sablonhoz a fő sablon beágyazott mappájában vagy a-ben. A két jogkivonattal kapcsolatos további információkért tekintse meg az [Azure Resource Manager – ajánlott eljárásokat ismertető útmutató](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md) **telepítési** összetevők című szakaszát.

## <a name="user-experience"></a>Felhasználó felület

## <a name="developer"></a>Fejlesztő
A fejlesztők ugyanazt a munkafolyamatot használják egy virtuális gép létrehozásához egy adott környezet létrehozásához. Kiválasztja a környezetet és a számítógép rendszerképét, és megadja a sablonhoz szükséges szükséges adatokat. A környezet minden fejlesztője lehetővé teszi a változások telepítését és a belső hurok-hibakeresést. A környezet bármikor létrehozható a legújabb sablon használatával.  Ez a funkció lehetővé teszi a környezetek megsemmisítését és újbóli létrehozását, hogy csökkentse az állásidőt a rendszer manuális létrehozásához vagy a hibák tesztelésének helyreállításához.  

### <a name="testing"></a>Tesztelés
A DevTest Labs-környezetek aszinkron módon teszik lehetővé az adott kódok és konfigurációk független tesztelését. Az általános gyakorlat az, hogy a környezetet az egyéni lekéréses kérelem kódjával hozza létre, és elkezdi az automatizált tesztelést. Az automatizált tesztelés befejezését követően a manuális tesztelés végezhető el az adott környezetben. Ezt a folyamatot általában a CI/CD folyamat részeként kell elvégezni. 

## <a name="management-experience"></a>Kezelési élmény

### <a name="cost-tracking"></a>Cost Tracking
A Cost Tracking szolgáltatás a teljes Cost trend részeként tartalmazza az Azure-erőforrásokat a különböző környezetekben. Az erőforrások díja nem szünteti meg a környezetben lévő különböző erőforrásokat, de a környezetet egyetlen díjként jeleníti meg.

### <a name="security"></a>Biztonság
A DevTest Labs szolgáltatással megfelelően konfigurált Azure-előfizetés [csak a laboron keresztül korlátozhatja az Azure-erőforrásokhoz való hozzáférést](devtest-lab-add-devtest-user.md). A környezetek révén a labor tulajdonosa lehetővé teheti a felhasználók számára, hogy a jóváhagyott konfigurációkhoz hozzáférjenek a Pásti-erőforrásokhoz anélkül, hogy a hozzáférés bármely más Azure-erőforráshoz Abban a forgatókönyvben, ahol a labor felhasználói testreszabják a környezeteket, a tesztkörnyezet tulajdonosa engedélyezheti a közreműködők hozzáférését. A közreműködői hozzáférés lehetővé teszi, hogy a labor felhasználó csak a felügyelt erőforráscsoport keretében vegye fel vagy távolítsa el az Azure-erőforrásokat. Lehetővé teszi a könnyebb nyomon követést és felügyeletet, és lehetővé teszi a felhasználó közreműködői hozzáférését az előfizetéshez.

### <a name="automation"></a>Automation
Az Automation a nagy méretű és hatékony ökoszisztéma egyik kulcsfontosságú összetevője. Az automatizálás a különböző környezetek előfizetések és laborok közötti felügyeletének vagy nyomon követésének kezeléséhez szükséges.

### <a name="cicd-pipeline"></a>CI/CD-folyamat
A DevTest Labs-beli Pásti-szolgáltatások a CI/CD-folyamat fejlesztéséhez olyan fókuszált központi telepítéseket biztosítanak, amelyekben a tesztkörnyezet a hozzáférést vezérli.

## <a name="next-steps"></a>Következő lépések
A környezetekkel kapcsolatos részletekért tekintse meg a következő cikkeket: 

- 
- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Környezet létrehozása önálló Service Fabric-fürttel Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Környezet összekötése a tesztkörnyezet virtuális hálózatával Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Környezetek integrálása az Azure DevOps CI/CD-folyamatokba](integrate-environments-devops-pipeline.md)
 





