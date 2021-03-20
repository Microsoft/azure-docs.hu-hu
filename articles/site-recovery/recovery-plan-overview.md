---
title: A Azure Site Recovery helyreállítási tervei
description: A Azure Site Recovery helyreállítási terveinek megismerése.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343914"
---
# <a name="about-recovery-plans"></a>A helyreállítási tervek ismertetése

Ez a cikk a [Azure site Recovery](site-recovery-overview.md)helyreállítási terveinek áttekintését tartalmazza.

A helyreállítási terv a gépeket helyreállítási csoportokba gyűjti a feladatátvétel céljából. A helyreállítási terv segítséget nyújt egy rendszeres helyreállítási folyamat definiálásához, ha olyan kis független egységeket hoz létre, amelyek feladatátvételt hajtanak végre. Egy egység általában egy alkalmazást jelöl a környezetben.

- A helyreállítási terv meghatározza a gépek feladatátvételét, valamint azt a sorozatot, amelyben a feladatátvétel után indulnak.
- A helyreállítási tervek a feladatátvétel és az Azure-beli feladat-visszavétel esetében is használhatók.
- Akár 100 védett példány is hozzáadható egy helyreállítási tervhez.
- A tervet testreszabhatja rendelés, utasítások és feladatok hozzáadásával.
- A terv meghatározása után futtathatja a feladatátvételt.
- A gépek több helyreállítási tervben is szerepelhetnek, amelyekben a következő tervek kihagyják a gép központi telepítését/indítását, ha korábban egy másik helyreállítási terv használatával telepítették.



### <a name="why-use-a-recovery-plan"></a>Miért érdemes helyreállítási tervet használni?

Helyreállítási tervek használata a következőhöz:

* Alkalmazás modellezése a függőségei köré.
* A helyreállítási időre vonatkozó célkitűzés (RTO) csökkentése érdekében automatizálja a helyreállítási feladatokat.
* Győződjön meg arról, hogy készen áll az áttelepítésre vagy a vész-helyreállításra, mivel az alkalmazások egy helyreállítási terv részét képezik.
* Futtasson feladatátvételi teszteket a helyreállítási terveken, hogy a vész-helyreállítás vagy az áttelepítés a várt módon működjön.


## <a name="model-apps"></a>Modell alkalmazások 
Az alkalmazás-specifikus tulajdonságok rögzítéséhez létrehozhat egy helyreállítási csoportot. Tegyük fel például, hogy egy tipikus háromrészes alkalmazást alkalmazunk egy SQL Server-háttérrel, a köztes hálózattal és egy webes előtérben. A helyreállítási tervet általában úgy szabhatja testre, hogy az egyes rétegekbe tartozó gépek a feladatátvétel után megfelelő sorrendben induljon el.

- Az SQL-háttérnek először el kell indulnia, a middleware következő, végül pedig a webes felület.
- Ez az indítási sorrend biztosítja, hogy az alkalmazás az utolsó gép indításakor is működik.
- Ez a sorrend biztosítja, hogy amikor a middleware elindul, és megpróbál csatlakozni a SQL Server szintjéhez, a SQL Server-szintet már futtatja. 
- Ez a megrendelés azt is lehetővé teszi, hogy az előtér-kiszolgáló utoljára induljon el, így a végfelhasználók nem csatlakoznak az alkalmazás URL-címéhez az összes összetevő üzembe helyezése előtt, és az alkalmazás készen áll a kérelmek fogadására.

A rendelés létrehozásához vegyen fel csoportokat a helyreállítási csoportba, és vegyen fel gépeket a csoportokba.
- A sorrend megadásakor a rendszer a sorrendet használja. A műveletek szükség szerint párhuzamosan futnak az alkalmazás-helyreállítási RTO javítására.
- Az egyetlen csoportba tartozó gépek párhuzamosan hajtják végre a feladatátvételt.
- A különböző csoportokban lévő gépek a csoportosítási sorrend szerint működnek, így a 2. csoportba tartozó gépek csak azt követően kezdik el a feladatátvételt, miután az 1. csoportban lévő összes gép felvette és elindult.

    ![Példa helyreállítási tervre](./media/recovery-plan-overview/rp.png)

Ennek a testreszabásnak a helyén a következő történik, ha feladatátvételt futtat a helyreállítási tervben: 

1. A leállítási lépés megkísérli kikapcsolni a helyszíni gépeket. A kivétel a feladatátvételi teszt futtatása, amely esetben az elsődleges hely továbbra is fut. 
2. A Leállítás elindítja a helyreállítási tervben szereplő összes gép párhuzamos feladatátvételét.
3. A feladatátvétel replikált adattal készíti elő a virtuális gépek lemezeit.
4. Az indítási csoportok sorrendben futnak, és elindítják a gépeket az egyes csoportokban. Először az 1. csoport fut, majd a 2. csoport, végül pedig a 3. csoport. Ha egy csoportban több gép is van, akkor az összes gép párhuzamosan indul el.


## <a name="automate-tasks-in-recovery-plans"></a>Feladatok automatizálása a helyreállítási tervekben

A nagyméretű alkalmazások helyreállítása összetett feladat lehet. A manuális lépések végrehajtásával a folyamat hibát észlelt, és előfordulhat, hogy a feladatátvételt futtató személy nem veszi figyelembe az alkalmazás összes bonyolult feltételét. A helyreállítási terv segítségével megadhatja a sorrendet, és automatizálhatja az egyes lépésekhez szükséges műveleteket, Azure Automation runbookok használatával végezheti el a feladatátvételt az Azure-ba vagy a parancsfájlokba. Olyan feladatokhoz, amelyek nem lehetnek automatizáltak, a manuális műveletekre vonatkozó szüneteltetéseket is beillesztheti helyreállítási tervbe. Több különböző típusú feladat is konfigurálható:

* **Feladatok az Azure virtuális gépen feladatátvétel után**: Ha feladatátvételt végez az Azure-ba, általában műveleteket kell végrehajtania, hogy a feladatátvételt követően csatlakozni lehessen a virtuális géphez. Például: 
    * Hozzon létre egy nyilvános IP-címet az Azure-beli virtuális gépen.
    * Rendeljen egy hálózati biztonsági csoportot az Azure-beli virtuális gép hálózati adapteréhez.
    * Terheléselosztó hozzáadása egy rendelkezésre állási készlethez.
* **Feladatok a virtuális gépen a feladatátvételt követően**: ezek a feladatok általában újrakonfigurálja a gépen futó alkalmazást, hogy az továbbra is megfelelően működjön az új környezetben. Például:
    * Módosítsa az adatbázis-kapcsolatok karakterláncát a gépen belül.
    * Módosítsa a webkiszolgáló konfigurációját vagy szabályait.


### <a name="run-a-test-failover-on-recovery-plans"></a>Feladatátvételi teszt futtatása helyreállítási terveken

Helyreállítási terv használatával feladatátvételi tesztet indíthat. Használja az alábbi ajánlott eljárásokat:

- Teljes feladatátvétel futtatása előtt mindig végezzen feladatátvételi tesztet egy alkalmazáson. A feladatátvételi teszt segítségével ellenőrizheti, hogy az alkalmazás elérhető-e a helyreállítási helyen.
- Ha úgy találja, hogy kihagyott valamit, aktiválja a tisztítást, majd futtassa újra a feladatátvételi tesztet. 
- Futtasson többszöri feladatátvételi tesztet, amíg nem biztos benne, hogy az alkalmazás zökkenőmentesen működik.
- Mivel az egyes alkalmazások egyediek, létre kell hoznia minden egyes alkalmazáshoz testre szabott helyreállítási terveket, és mindegyiknél futtasson feladatátvételi tesztet.
- Az alkalmazások és a függőségeik gyakran változnak. A helyreállítási tervek naprakészen tartása érdekében minden negyedévben futtasson feladatátvételi tesztet minden egyes alkalmazáshoz.

    ![Képernyőkép egy példa tesztelési helyreállítási tervről Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Helyreállítási terv Videójának megtekintése

Tekintse meg a kétrétegű WordPress-alkalmazás helyreállítási tervének egy kattintással elvégezhető feladatátvételét bemutató gyors példát tartalmazó videót.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Következő lépések

- [Hozzon létre](site-recovery-create-recovery-plans.md) egy helyreállítási tervet.
- Feladatátvételek [futtatása](site-recovery-failover.md) . 
