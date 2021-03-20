---
title: Azure Lab Services vs Azure DevTest Labs
description: Azure DevTest Labs és Azure Lab Services összehasonlítása.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: b1cd476faf6c457033ffeace03cd2e37b51e8578
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480082"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Azure DevTest Labs és Azure Lab Services összehasonlítása
Az Azure-ban két olyan szolgáltatás található, amely lehetővé teszi, hogy tesztkörnyezet-környezeteket hozzon létre a felhőben. 

- **Azure DevTest Labs** – ez a szolgáltatás lehetővé teszi, hogy gyorsan beállítson egy környezetet a csapat számára (például: fejlesztési környezet vagy tesztkörnyezetben a felhőben). A tesztkörnyezet tulajdonosa létrehoz egy tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket, és elérhetővé teszi azokat a tesztkörnyezet felhasználói számára. A labor felhasználói a laborban csatlakoznak a virtuális gépekhez (VM), és felhasználhatják azokat a napi munkához, a rövid távú projektekhez. Miután a felhasználók megkezdik az erőforrások használatát a tesztkörnyezetben, a tesztkörnyezet egy rendszergazdája több tesztkörnyezetre kiterjedően elemezheti a költségeket és a használatot, és átfogó szabályzatokat állíthat be a vállalat vagy csapat költségeinek optimalizálásához.
- **Azure Lab Services** – ez a szolgáltatás lehetővé teszi a felügyelt laborok típusának létrehozását. Jelenleg a tanterem Labs az egyetlen, Azure Lab Services által támogatott felügyelt tesztkörnyezet. A szolgáltatás maga kezeli a felügyelt tesztkörnyezet összes infrastruktúrájának kezelését, a virtuális gépek felgyorsítását a hibák kezelésére és az infrastruktúra skálázására. Azt követően, hogy a rendszergazda létrehoz egy labor-fiókot Azure Lab Servicesban, az oktató gyorsan beállíthat egy labort az osztályához, megadhatja az osztályban felhasználható virtuális gépek számát és típusát, valamint felhasználókat adhat hozzá a osztályhoz. Miután a felhasználó regisztrált a osztályba, a felhasználó elérheti a virtuális gépet, hogy az osztályhoz tartozó gyakorlatokat végezzen.  

## <a name="key-capabilities"></a>Főbb képességek

Ezek a szolgáltatások (Azure DevTest Labs és Azure Lab Services) a következő főbb képességeket és funkciókat támogatják:

- **A tesztkörnyezetek gyors és rugalmas beállítása**. A tesztkörnyezetek tulajdonosai az Azure Lab Services használatával gyorsan állíthatnak be az igényeinek megfelelő tesztkörnyezetet. A szolgáltatás lehetővé teszi az összes Azure-infrastruktúra működésének kezelését a felügyelt labor típusok esetében, illetve a labor tulajdonosai számára a labor tulajdonosának előfizetésében az infrastruktúra önálló felügyeletét és testreszabását. A szolgáltatás biztosítja az infrastruktúra beépített méretezését és rugalmasságát azon tesztkörnyezetek esetén, amelyeket a tulajdonos helyett kezel.
- **Egyszerű használat a tesztkörnyezetek felhasználói számára**. A felügyelt labor típusokban, például a tantermi laborokban a labor felhasználói regisztrálhatnak egy regisztrációs kóddal rendelkező laborba, és bármikor hozzáférhetnek a laborhoz a labor erőforrásainak használatához. A DevTest Labs szolgáltatásban létrehozott tesztkörnyezetekben a tesztkörnyezet tulajdonosa engedélyt adhat a tesztkörnyezet felhasználóinak a virtuális gépek létrehozásához és eléréséhez, az adatlemezek felügyeletéhez és újbóli használatához, valamint többször használható titkos kulcsok beállításához.  
- **Költségoptimalizálás és elemzés**. A tesztkörnyezet tulajdonosa ütemezéseket állíthat be a tesztkörnyezetben a virtuális gépek automatikus leállításához és elindításához. A tesztkörnyezet tulajdonosa beállíthat egy ütemezést azon időtartamok meghatározásához, amikor a tesztkörnyezet virtuális gépei elérhetők a felhasználók számára, és felhasználóalapú vagy tesztkörnyezet-alapú használati szabályzatokat állíthat be a költségek optimalizálása, továbbá a tesztkörnyezetben jelentkező használati és tevékenységi trendek elemzése érdekében. A felügyelt labor típusok, például a tanterem Labs esetében jelenleg a költségcsökkentés és az elemzési lehetőségek kisebb részhalmaza érhető el.
- **Beágyazott biztonság**. A tesztkörnyezet tulajdonosa beállíthat egy virtuális magánhálózatot és alhálózatot a tesztkörnyezet számára, és engedélyezheti a megosztott nyilvános IP-címeket. A tesztkörnyezetek felhasználói biztonságosan érhetik el az erőforrásokat az ExpressRoute vagy helyek közötti VPN használatával konfigurált virtuális hálózattal. (Jelenleg csak a DevTest Labs szolgáltatásban érhető el.)
- **Integrálás a munkafolyamatokba és az eszközökbe**. Az Azure Lab Services lehetővé teszi a tesztkörnyezetek integrálását a vállalat webhelyébe és felügyeleti rendszereibe. Automatikusan építhet ki környezeteket a folyamatos integrációs/folyamatos üzembehelyezési (CI/CD) eszközökből. (Jelenleg csak a DevTest Labs szolgáltatásban érhető el.)

## <a name="scenarios"></a>Forgatókönyvek

Íme néhány olyan forgatókönyv, amely Azure DevTest Labs és Azure Lab Services támogatást nyújt:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Méretezhető számítógépes tesztkörnyezet beállítása a felhőben az osztályterem számára  

- Felügyelt osztályterem-tesztkörnyezet létrehozása. Mindössze közölnie kell a szolgáltatással, hogy pontosan mire van szüksége, a szolgáltatás pedig létrehozza és felügyeli a tesztkörnyezet infrastruktúráját, így az osztály tanítására összpontosíthat, és nem kell a tesztkörnyezet technikai részleteivel foglalkoznia.
- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű időt biztosíthat minden diák számára a virtuális gépek használatához az osztálytermi munka elvégzéséhez.  
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően.
- A tesztkörnyezet törlése egyetlen kattintással, miután végzett.

### <a name="use-devtest-labs-for-development-environments"></a>A DevTest Labs használata fejlesztési környezetekhez

Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a fejlesztői gépeket a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- Lehetővé teszi a fejlesztők számára a fejlesztési gépek igény szerinti kiépítését.
- Windows- és Linux-környezetek kiépítése újrahasznosítható sablonokkal és összetevőkkel.
- A fejlesztők szükség esetén könnyedén testreszabhatják a fejlesztési gépeket.
- A költségek szabályozása érdekében a rendszergazdák gondoskodhatnak arról, hogy a fejlesztők ne kapjanak több virtuális gépet, mint amennyire a fejlesztéshez szükségük van, illetve hogy a virtuális gépek leálljanak, amikor nincsenek használatban.

További információ: [A DevTest Labs használata fejlesztéshez](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>A DevTest Labs használata tesztkörnyezetekhez

Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a gépeket a tesztelők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A tesztelők gyorsan tesztelhetik az alkalmazás legújabb verzióját Windows- és Linux-környezetek újrahasznosítható sablonokkal és összetevőkkel történő kiépítésével.
- A tesztelők vertikálisan felskálázhatják a terhelésteszteket több tesztügynök kiépítésével.
- A költségek szabályozása érdekében a rendszergazdák gondoskodhatnak arról, hogy a tesztelők ne kapjanak több virtuális gépet, mint amennyire a teszteléshez szükségük van, illetve hogy a virtuális gépek leálljanak, amikor nincsenek használatban.

További információ: [A DevTest Labs használata teszteléshez](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>A laborok típusai
Kétféle labort hozhat létre: a **felügyelt labor-típusokat** a Azure Lab Services és a **Labs** használatával Azure Lab Services. Ha azt szeretné, hogy a laborban milyen adatokat kell megadnia, és lehetővé kell tennie a szolgáltatás számára a laborhoz szükséges infrastruktúra beállítását és kezelését, válasszon az egyik **felügyelt labor típus** közül. Jelenleg a **tanterem Lab** az egyetlen felügyelt Lab-típus, amelyet a Azure Lab Services hozhat létre. Ha saját infrastruktúrát szeretne felügyelni, hozzon létre egy labort **Azure DevTest Labs** használatával.

Az alábbi szakaszokban részletesen is tárgyaljuk ezeket a tesztkörnyezeteket. 

## <a name="managed-lab-types"></a>Felügyelt tesztkörnyezet-típusok
Az Azure Lab Services segítségével Azure által kezelt infrastruktúrával rendelkező tesztkörnyezeteket hozhat létre. Ez a cikk felügyelt labor típusokként hivatkozik rájuk. A felügyelt labor típusok különböző típusú laborokat kínálnak, amelyek megfelelnek az adott igényeknek. Jelenleg csak a által támogatott felügyelt labor típus a **tanterem Lab**. 

A felügyelt labor-típusok lehetővé teszik, hogy azonnal, minimális telepítéssel kezdjen el. A szolgáltatás maga gondoskodik a tesztkörnyezet infrastruktúrájának átfogó kezeléséről, a virtuális gépek indításától a hibák kezelésén keresztül az infrastruktúra méretezéséig. A felügyelt laborok, például a tantermi laborok létrehozásához először létre kell hoznia egy Lab-fiókot a szervezet számára. A tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének a felügyeletéhez. 

Ha Azure-erőforrásokat hoz létre és használ ezekben a felügyelt labor-típusokban, a szolgáltatás az erőforrásokat belső Microsoft-előfizetésekben hozza létre és kezeli. nem pedig az Ön Azure-előfizetésében. A szolgáltatás belső Microsoft-előfizetésekben követi nyomon ezen erőforrások használatát. A használat számlázása a tesztkörnyezetfiókot tartalmazó Azure-előfizetésben történik.   

Íme néhány példa a **felügyelt labor-típusok használati eseteire**: 

- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű idő biztosítása minden diák számára a virtuális gépek használatához a házi feladatok vagy személyes projektek elvégzéséhez.
- Nagy teljesítményű számítások végrehajtására képes virtuális gépek készletének beállítása nagy számítási vagy grafikai igényű kutatáshoz. A virtuális gépek futtatása igény szerint, majd a gépek törlése, miután végzett. 
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően.  
- Virtuális gépekből álló tesztkörnyezet gyors kiépítése Hackathon-verseny megtartásához. A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 


## <a name="devtest-labs"></a>DevTest Labs
Felmerülhetnek olyan forgatókönyvek, amikor a saját előfizetésén belül szeretne felügyelni minden infrastruktúrát és konfigurációt. Ehhez létrehozhat egy tesztkörnyezetet az Azure DevTest Labs használatával az Azure Portalon. Ezekhez a tesztkörnyezetekhez nem kell tesztkörnyezetfiókot létrehoznia. Ezek a laborok nem jelennek meg a labor-fiókban (amely a felügyelt labor típusok esetében létezik).  

A **DevTest Labs tesztkörnyezetek néhány alkalmazási helyzete**: 

- Virtuális gépekből álló tesztkörnyezet gyors kiépítése Hackathon-verseny megtartásához, vagy egy konferencia gyakorlati foglalkozásához. A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 
- Az alkalmazással konfigurált virtuális gépek készletének létrehozása, és annak lehetővé tétele, hogy a csapat könnyedén használhasson virtuális gépeket programhiba-vizsgálathoz.  
- A fejlesztők számára szükséges eszközök mindegyikével konfigurált virtuális gépek biztosítása. Automatikus indítás és leállítás ütemezése a költségek minimalizálása érdekében. 
- A tesztgépekből álló tesztkörnyezet ismételt létrehozása az üzemi környezet részeként. A legújabb összetevők tesztelése, majd a tesztgépek törlése, miután végzett. 
- Különböző módon konfigurált virtuális gépek és több tesztügynök beállítása a méretezés és a teljesítmény teszteléséhez. 
- Képzési foglalkozások biztosítása az ügyfeleknek a termék legújabb verziójával konfigurált tesztkörnyezettel. Korlátozott mennyiségű idő biztosítása minden ügyfél számára a tesztkörnyezet használatához. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Felügyelt labor típusok és DevTest Labs
Az alábbi táblázat az Azure Lab Services által támogatott tesztkörnyezetek két típusát hasonlítja össze: 

| Funkciók | Felügyelt tesztkörnyezet-típusok | DevTest Labs |
| -------- | ----------------- | ---------- |
| Az Azure-infrastruktúra felügyelete a tesztkörnyezetben. |  A szolgáltatás automatikusan felügyeli. | A felhasználó saját maga felügyeli.  |
| Beépített rugalmasság az infrastruktúrával kapcsolatos problémákkal szemben | A szolgáltatás automatikusan kezeli. | A felhasználó saját maga felügyeli.  |
| Előfizetés-kezelés | A szolgáltatás kezeli az erőforrások kiosztását a szolgáltatást támogató Microsoft-előfizetéseken belül. A szolgáltatás automatikusan kezeli a méretezést. | A felhasználó saját maga felügyeli a saját Azure-előfizetésében. Nincsenek automatikus skálázási előfizetések. |
| Azure Resource Manager-alapú üzemi környezet a tesztkörnyezetben | Nem elérhető | Elérhető |

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket: 

- [Az osztályterem-tesztkörnyezetek ismertetése](../lab-services/classroom-labs-overview.md)
- [A DevTest Labs ismertetése](devtest-lab-overview.md)
