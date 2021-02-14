---
title: A klasszikusról Azure Resource Managerre való Migrálás megtervezése
description: Ebből a cikkből megtudhatja, hogyan tervezheti meg a IaaS-erőforrások áttelepítését a klasszikusról Azure Resource Managerra.
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: c29ca5f51e59775479fa5e97e88b28dba110d866
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366075"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](classic-vm-deprecation.md#how-does-this-affect-me).

Míg Azure Resource Manager sok csodálatos funkciót kínál, fontos, hogy megtervezze a migrációs utat, hogy a dolgok zökkenőmentesek legyenek. A tervezés során eltöltött idő biztosítja, hogy az áttelepítési tevékenységek végrehajtása során ne tapasztaljon problémát.

> [!NOTE]
> Az Azure Customer tanácsadó csapat és a Felhőbeli megoldás-építészek a nagy környezetek áttelepítését végző ügyfelekkel együttműködve jelentős mértékben járultak hozzá az alábbi útmutatóhoz. Ez a dokumentum továbbra is frissülni fog, ahogy a siker új mintája, ezért térjen vissza időről időre, és ellenőrizze, hogy van-e új javaslat.

Az áttelepítési út négy általános fázisban érhető el:

![Áttelepítési fázisok](./media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Felkészülés

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

A technikai követelmények méretétől, a földrajzi helytől és az üzemeltetési gyakorlattól függően érdemes megfontolnia a következőket:

1. Miért Azure Resource Manager a szervezete számára?  Mik az áttelepítés üzleti okai?
2. Mik a Azure Resource Manager technikai okai?  Milyen további Azure-szolgáltatásokat szeretne használni?
3. Mely alkalmazások (vagy virtuális gépek készletei) szerepelnek az áttelepítésben?
4. Mely forgatókönyvek támogatottak az áttelepítési API-val?  Tekintse át a nem [támogatott funkciókat és konfigurációkat](migration-classic-resource-manager-overview.md).
5. A működési csapatai mostantól támogatják a klasszikus és a Azure Resource Manager is az alkalmazásokat/virtuális gépeket?
6. Hogyan változtatja meg a virtuális gépek üzembe helyezési, felügyeleti, figyelési és jelentéskészítési folyamatait a (ha egyáltalán) Azure Resource Manager?  Frissíteni kell az üzembe helyezési parancsfájlokat?
7. Mi a kommunikációs terv az érdekelt felek (végfelhasználók, alkalmazások tulajdonosai és infrastruktúra-tulajdonosok) riasztására?
8. A környezet összetettsége alapján szükség van olyan karbantartási időszakra, amelyben az alkalmazás nem érhető el a végfelhasználók és az alkalmazások tulajdonosai számára?  Ha igen, mennyi ideig?
9. Mi a képzési terv, hogy az érdekelt felek jól tájékozott és jártasak legyenek a Azure Resource Managerban?
10. Mi a program Management vagy projektmenedzsment terv az áttelepítéshez?
11. Mik a Azure Resource Manager áttelepítési és egyéb kapcsolódó technológiai útitervek Ütemtervei?  Optimálisan vannak igazítva?

### <a name="patterns-of-success"></a>Sikeres minták

A sikeres ügyfelek részletes tervekkel rendelkeznek, ahol az előző kérdéseket tárgyalják, dokumentálják és szabályozzák.  Győződjön meg arról, hogy az áttelepítési csomagok széles körben kommunikálnak a szponzorokkal és az érdekelt felekkel.  Az áttelepítési lehetőségekkel kapcsolatos ismeretek megismerése; az alábbi áttelepítési dokumentum beolvasása kifejezetten ajánlott.

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)

### <a name="pitfalls-to-avoid"></a>Kikerülő buktatók

- A terv elmulasztása.  Az áttelepítés technológiai lépései bebizonyítottak, és az eredmény kiszámítható.
- Feltételezi, hogy a platform által támogatott áttelepítési API az összes forgatókönyvet figyelembe veszi. Olvassa el a nem támogatott [funkciókat és konfigurációkat](migration-classic-resource-manager-overview.md) , hogy megtudja, milyen forgatókönyvek támogatottak.
- Nem tervezheti meg a végfelhasználók számára a lehetséges alkalmazások kimaradását.  Elég puffert kell megterveznie, hogy megfelelően figyelmeztesse a végfelhasználókat a potenciálisan elérhetetlen alkalmazási időre.


## <a name="lab-test"></a>Tesztkörnyezet tesztelése

**A környezet replikálása és a tesztek áttelepítése**
  > [!NOTE]
  > A meglévő környezet pontos replikálását olyan Közösség által támogatott eszköz használatával hajtja végre, amelyet Microsoft ügyfélszolgálata nem támogat hivatalosan. Ezért ez egy **opcionális** lépés, de a legjobb módszer az éles környezetek érintése nélküli problémák megoldására. Ha egy Közösség által biztosított eszközt nem használ, akkor olvassa el az alábbi, a száraz futtatási javaslat ellenőrzése/előkészítése/megszakítása című szakaszt.
  >

  A megfelelő forgatókönyv (számítási, hálózatkezelési és tárolási) tesztkörnyezet tesztelése a legjobb módszer a zökkenőmentes áttelepítés biztosításához. Ez a következőket nyújtja:

- Egy teljesen különálló labor vagy egy meglévő, nem éles környezet tesztelése. Javasoljuk, hogy egy teljesen különálló labort, amely többször is áttelepíthető, és romboló módon módosítható legyen.  Az alábbi lista a valós előfizetésből származó metaadatok gyűjtésére/hidrátára szolgáló parancsfájlokat sorolja fel.
- Érdemes létrehozni a labort egy külön előfizetésben. Ennek az az oka, hogy a labor többször is le lesz szakítva, és egy különálló, elkülönített előfizetéssel csökken az esélye annak, hogy valami valóban véletlenül törölve lesz.

  Ezt a AsmMetadataParser eszköz használatával lehet elvégezni. [Az eszközről itt olvashat bővebben](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Sikeres minták

A következő problémák léptek fel a nagyobb áttelepítések során. Ez nem teljes lista, ezért további részletekért tekintse meg a nem [támogatott funkciókat és konfigurációkat](migration-classic-resource-manager-overview.md) . Előfordulhat, hogy ezek a technikai problémák nem fordulnak elő, de ha az áttelepítés megkezdése előtt megoldást nyújt, zökkenőmentesebb élményt biztosíthat.

- A **száraz Futtatás ellenőrzése/előkészítése/megszakítása** – ez talán a legfontosabb lépés annak biztosításához, hogy a klasszikus Azure Resource Manager a migráció sikerességét. Az áttelepítési API három fő lépésből áll: érvényesítés, előkészítés és véglegesítés. Az ellenőrzés során a rendszer beolvassa a klasszikus környezet állapotát, és az összes probléma eredményét adja vissza. Mivel azonban előfordulhat, hogy bizonyos problémák léteznek a Azure Resource Manager veremben, az ellenőrzés nem fog mindent megfogni. Az áttelepítési folyamat következő lépése a felkészülés segít kitenni ezeket a problémákat. Az előkészítés a metaadatokat a Klasszikusról Azure Resource Managerra helyezi át, de nem véglegesíti az áthelyezést, és nem távolítja el és nem változtatja meg a klasszikus oldalon. A száraz Futtatás magában foglalja az áttelepítés előkészítését, majd a rendszer megszakítja (**nem véglegesíti**) az áttelepítés előkészítését. A száraz Futtatás ellenőrzésének/előkészítésének vagy megszakításának célja, hogy megtekintse az összes metaadatot a Azure Resource Manager veremben, vizsgálja meg (*programozott módon vagy a portálon*), és győződjön meg arról, hogy minden megfelelően működik, és technikai problémákkal végezhető el.  Emellett az áttelepítés időtartamát is lehetővé teszi, hogy ennek megfelelően tervezze meg az állásidőt.  Az érvényesítés/előkészítés/megszakítás nem okoz felhasználói állásidőt; ezért nem zavarja az alkalmazások használatát.
  - Az alábbi elemeket meg kell oldani a száraz Futtatás előtt, de a száraz futtatási teszt is biztonságosan kiüríti ezeket az előkészítési lépéseket, ha azok kimaradnak. A nagyvállalati áttelepítés során a száraz futtatást találtuk, hogy biztonságos és felbecsülhetetlen értékű módon biztosítható legyen az áttelepítés.
  - Ha az előkészítés fut, a vezérlési síkon (Azure felügyeleti műveletek) a teljes virtuális hálózat számára zárolva lesz, így az érvényesítés/előkészítés/megszakítás során nem lehet módosítani a virtuális gépek metaadatait.  Más esetben a rendszer nem érinti az alkalmazás funkcióit (RD, VM-használat stb.).  A virtuális gépek felhasználói nem fogják tudni, hogy a rendszer végrehajtja a száraz futtatást.

- **Express Route-áramkörök és VPN**. Az engedélyezési hivatkozásokkal rendelkező Express Route Gateway-átjárók nem telepíthetők át állásidő nélkül. A megkerülő megoldásért lásd: [ExpressRoute-áramkörök és társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager-alapú üzemi modellbe](../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-bővítmények** – a virtuálisgép-bővítmények potenciálisan a futó virtuális gépek áttelepítésének egyik legnagyobb útelzárások. A virtuálisgép-bővítmények szervizelése akár 1-2 napot is igénybe vehet, ezért ennek megfelelően tervezzen.  A futó virtuális gépek virtuálisgép-bővítményi állapotának jelentéséhez egy működő Azure-ügynökre van szükség. Ha az állapot egy futó virtuális gép esetében hibás, akkor a rendszer megállítja az áttelepítést. Magának az ügynöknek nem kell üzemképes a Migrálás engedélyezéséhez, de ha a bővítmények léteznek a virtuális gépen, akkor a Migrálás érdekében mind a működő ügynöknek, mind a kimenő internetkapcsolatnak (DNS) is szüksége lesz.
  - Ha a DNS-kiszolgálóval való kapcsolat megszakad az áttelepítés során, az összes virtuálisgép-bővítmény a BGInfo v1 kivételével. \* először el kell távolítani az összes virtuális gépről az áttelepítés előkészítése előtt, majd később újra hozzá kell adni a virtuális géphez a Azure Resource Manager áttelepítés után.  **Ez csak a rendszert futtató virtuális gépek esetében használható.**  Ha a virtuális gépek le lettek állítva, a virtuálisgép-bővítményeket nem kell eltávolítani. **Megjegyzés:** Számos bővítmény, például az Azure Diagnostics és a Security Center figyelése is újratelepíti magukat az áttelepítés után, így az eltávolítás nem jelent problémát.
  - Továbbá győződjön meg arról, hogy a hálózati biztonsági csoportok nem korlátozzák a kimenő internet-hozzáférést. Ez néhány hálózati biztonsági csoport konfigurációjának használatával fordulhat elő. A virtuálisgép-bővítmények Azure Resource Managerba való áttelepíteni kívánt kimenő internet-hozzáférésre (és DNS) van szükség.
  - A BGInfo-bővítmény két verziója létezik: v1 és v2.  Ha a virtuális gép a Azure Portal vagy a PowerShell használatával lett létrehozva, akkor a virtuális gép valószínűleg rendelkezik a v1 bővítménnyel. Ezt a bővítményt nem kell eltávolítani, és az áttelepítési API nem fogja kihagyni (áttelepíteni). Ha azonban a klasszikus virtuális gép az új Azure Portal lett létrehozva, akkor valószínűleg a BGInfo JSON-alapú v2-verzióját fogja telepíteni, amely áttelepíthető Azure Resource Manager, hogy az ügynök működik-e, és rendelkezik-e kimenő internet-hozzáféréssel (és DNS-vel).
  - **Szervizelési lehetőség 1**. Ha tudja, hogy a virtuális gépek nem rendelkeznek kimenő internet-hozzáféréssel, egy működő DNS-szolgáltatással és a virtuális gépeken működő Azure-ügynökökkel, akkor az előkészítés előtt távolítsa el az összes virtuálisgép-bővítményt, majd telepítse újra a virtuálisgép-bővítményeket az áttelepítés után.
  - **Szervizelési lehetőség 2**. Ha a virtuálisgép-bővítmények túl nagy mennyiségű akadályt mutatnak, egy másik lehetőség az összes virtuális gép leállítása/felszabadítása az áttelepítés előtt. Telepítse át a lefoglalt virtuális gépeket, majd indítsa újra őket a Azure Resource Manager oldalon. Ennek az az előnye, hogy a virtuálisgép-bővítmények migrálva lesznek. A hátránya, hogy az összes nyilvános virtuális IP-cím elvész (ez lehet egy nem kezdő), és természetesen a virtuális gépek leállnak, ami sokkal nagyobb hatással van a munkaalkalmazásokra.

    > [!NOTE]
    > Ha az áttelepített virtuális gépekre vonatkozó Azure Security Center házirend konfigurálva van, a biztonsági házirendet le kell állítani a bővítmények eltávolítása előtt, ellenkező esetben a biztonsági figyelési bővítményt a rendszer automatikusan újratelepíti a virtuális gépen a eltávolítása után.

- **Rendelkezésre állási készletek** – a virtuális hálózatok (vNet) Azure Resource Managerba való áttelepítéséhez a klasszikus üzembe helyezés (azaz a felhőalapú szolgáltatás) egy rendelkezésre állási csoportba kell tartoznia, vagy a virtuális gépeknek nem szabad egyetlen rendelkezésre állási csoportba esniük. Ha egynél több rendelkezésre állási csoport van a felhőalapú szolgáltatásban, Azure Resource Manager nem kompatibilis, és a rendszer leállítja az áttelepítést.  Emellett a rendelkezésre állási csoportokban nem lehetnek olyan virtuális gépek, amelyek nem egy rendelkezésre állási csoportba tartozó virtuális gépek. Ennek megoldásához javítania kell a Cloud Service-t, vagy újra kell keverni.  Ennek megfelelően tervezze meg az időt.

- **Webes/feldolgozói szerepkör telepítései** – a webes és feldolgozói szerepköröket tartalmazó Cloud Services nem telepíthetők át Azure Resource Managerre. A webes és feldolgozói szerepköröket először el kell távolítani a virtuális hálózatról az áttelepítés megkezdése előtt.  Egy tipikus megoldás a webes/feldolgozói szerepkör példányainak áthelyezése egy különálló klasszikus virtuális hálózatra, amely egy ExpressRoute-áramkörhöz is csatolva van, vagy a kód áttelepítésére az újabb Pásti App Services (ez a vita meghaladja a dokumentum hatókörét). Az előző újratelepítési esetben hozzon létre egy új klasszikus virtuális hálózatot, helyezze át/telepítse újra a webes/feldolgozói szerepköröket az új virtuális hálózatra, majd törölje a központi telepítéseket az áthelyezett virtuális hálózatról. Nincs szükség kód módosítására. Az új [Virtual Network](../virtual-network/virtual-network-peering-overview.md) -társítási képesség a webes/feldolgozói szerepköröket és az ugyanazon az Azure-régióban lévő más virtuális hálózatokat tartalmazó klasszikus virtuális hálózat összevonására használható (a virtuális hálózat migrálása a **virtuális hálózatok áttelepítése után nem telepíthető át**), így a teljesítmény elvesztése nélkül, valamint a késési/sávszélességre vonatkozó szankciók nélkül is biztosíthatja ugyanazt a képességet. A [Virtual Network](../virtual-network/virtual-network-peering-overview.md)-társítás hozzáadásával a webes/feldolgozói szerepkör telepítései könnyedén csökkenthetők, és nem letilthatják az áttelepítést Azure Resource Managerre.

- **Azure Resource Manager kvóták** – az Azure-régiók külön kvótával/korlátokkal rendelkeznek a klasszikus és a Azure Resource Manager esetében is. Bár az áttelepítési forgatókönyvben az új hardver nem használatos *(a meglévő virtuális gépeket a Klasszikusból a Azure Resource Managerra cseréljük)*, Azure Resource Manager kvótákat még az áttelepítés megkezdése előtt elegendő kapacitással kell meghozni. Az alább felsorolt főbb korlátokat az észlelt problémák okozzák.  A határértékek növeléséhez nyisson meg egy kvóta-támogatási jegyet.

    > [!NOTE]
    > Ezeket a korlátokat ugyanabban a régióban kell kiemelni, ahol az aktuális környezetét át kell telepíteni.
    >

  - Hálózati illesztők
  - Terheléselosztók
  - Nyilvános IP-címek
  - Statikus nyilvános IP-címek
  - Cores
  - Network Security Groups (Hálózati biztonsági csoportok)
  - Útvonaltáblák

    A jelenlegi Azure Resource Manager kvótákat a következő parancsokkal tekintheti meg az Azure CLI legújabb verziójának használatával.

    **Számítás** *(magok, rendelkezésre állási készletek)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Hálózat** *(virtuális hálózatok, statikus nyilvános IP-címek, nyilvános IP-címek, hálózati biztonsági csoportok, hálózati adapterek, terheléselosztó, útválasztási táblák)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Tárterület** *(Storage-fiók)*

    ```azurecli
    az storage account show-usage
    ```

- **Azure Resource Manager API-szabályozás korlátozásai** – ha van elég nagy környezet (például > 400 VNET-ben futó virtuális gépek esetében az írási (jelenleg **1200 írás/óra**) alapértelmezett API-sávszélesség-szabályozási korlátját is elérheti Azure Resource Managerokban. Az áttelepítés megkezdése előtt egy támogatási jegyet kell létrehoznia, hogy növelje ezt a korlátot az előfizetésében.

- **Kiépítés túllépte a virtuális gép állapotát** – ha bármely virtuális gép állapota túllépte az **időkorlátot**, ezt meg kell oldani az áttelepítés előtt. Ennek az az egyetlen módja, ha a virtuális gép megszüntetésével/újbóli kiépítésével (törlésével, a lemez megtartásával és a virtuális gép újbóli létrehozásával) csak az állásidőt használja.

- **ROLESTATEUNKNOWN VM-állapot** – ha az áttelepítés egy **ismeretlen szerepkör-állapot** miatt leáll, vizsgálja meg a virtuális gépet a portálon, és ellenőrizze, hogy fut-e. Ez a hiba általában a saját (nincs szükség szervizelése nélkül) elmúlik néhány perc múlva, és gyakran átmeneti típust mutat a virtuális gépek **indításakor**, **leállításakor**, **újraindításakor** . **Ajánlott eljárás:** próbálkozzon újra az áttelepítés elvégzésével néhány perc múlva.

- A **Fabric-fürt nem létezik** – bizonyos esetekben bizonyos virtuális gépek nem telepíthetők át különböző páratlan okok miatt. Ezek közül az egyik ismert eset az, ha a virtuális gép nemrég lett létrehozva (az elmúlt héten vagy azt megelőzően), és egy olyan Azure-fürtöt helyeztek el, amely még nem rendelkezik Azure Resource Manager számítási feladatokhoz.  Hibaüzenet jelenik meg, amely szerint a **Fabric-fürt nem létezik** , és a virtuális gép nem telepíthető át. Néhány nap várakozása általában megoldja ezt a problémát, mivel a fürt hamarosan Azure Resource Manager engedélyezve lesz. Azonban az egyik azonnali Áthidaló megoldás `stop-deallocate` a virtuális gép, majd az áttelepítés után folytatható, és a Azure Resource Manager az áttelepítés után indítsa el a virtuális gépet.

### <a name="pitfalls-to-avoid"></a>Kikerülő buktatók

- Ne használja a parancsikonokat, és hagyja ki a száraz futtatások ellenőrzése/előkészítése/megszakítása műveleteit.
- A lehetséges problémák többsége az érvényesítési/előkészítési/megszakítási lépések során felszínre kerül.

## <a name="migration"></a>Áttelepítés

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

Most már készen áll, mert a környezetével kapcsolatos ismert problémákkal dolgozott.

A valós áttelepítések esetében érdemes megfontolnia a következőket:

1. Megtervezheti és beütemezheti a virtuális hálózatot (a legkisebb áttelepítési egységet) növekvő prioritással.  Először hajtsa végre az egyszerű virtuális hálózatokat, és haladjon tovább a bonyolultabb virtuális hálózatokkal.
2. A legtöbb ügyfél nem üzemi és éles környezetekben fog rendelkezni.  A gyártás utolsóként való beosztása.
3. **(Nem kötelező)** A karbantartási állásidőt sok pufferrel ütemezhetik, ha nem várt probléma merül fel.
4. Ha problémák merülnek fel, kommunikálhat a támogatási csapatával, és összehangolhatja azokat.

### <a name="patterns-of-success"></a>Sikeres minták

A fenti tesztkörnyezet tesztelési szakaszának technikai útmutatását a valós áttelepítés előtt figyelembe kell venni és mérsékelni kell.  A megfelelő teszteléssel az áttelepítés valójában nem esemény.  Éles környezetekben hasznos lehet további támogatás, például megbízható Microsoft-partner vagy Microsoft Premier szolgáltatások.

### <a name="pitfalls-to-avoid"></a>Kikerülő buktatók

A nem teljes körű tesztelés problémát okozhat, és késleltetheti az áttelepítést.  

## <a name="beyond-migration"></a>Migrálás után

### <a name="technical-considerations-and-tradeoffs"></a>Technikai megfontolások és kompromisszumok

Most, hogy Azure Resource Manager, maximalizálja a platformot.  További előnyökért tekintse meg az [Azure Resource Manager áttekintése című témakört](../azure-resource-manager/management/overview.md) .

Megfontolandó szempontok:

- Az áttelepítés más tevékenységekkel való árukapcsolása.  A legtöbb ügyfél egy alkalmazás-karbantartási időszakot választ.  Ha igen, érdemes lehet ezt az állásidőt használni más Azure Resource Manager képességek, például a titkosítás és az áttelepítés Managed Disksre való engedélyezéséhez.
- Tekintse át a Azure Resource Manager technikai és üzleti okait; Engedélyezze a csak az Ön környezetére érvényes Azure Resource Manageron elérhető további szolgáltatásokat.
- A környezet modernizálása a Pásti-szolgáltatásokkal.

### <a name="patterns-of-success"></a>Sikeres minták

Legyen céltudatos, hogy milyen szolgáltatásokat szeretne engedélyezni a Azure Resource Manager.  Számos ügyfél az alábbi lenyűgöző Azure-környezeteket keresi:

- [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).
- [Azure Resource Manager sablonokat egyszerűbb és hatékonyabban felügyelt központi telepítéshez](../azure-resource-manager/templates/overview.md).
- [Címkék](../azure-resource-manager/management/tag-resources.md).
- [Tevékenység-vezérlés](../azure-resource-manager/management/view-activity-logs.md)
- [Azure-szabályzatok](../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Kikerülő buktatók

Ne feledje, miért kezdte meg ezt a klasszikus Azure Resource Manager áttelepítési útra.  Mi volt az eredeti üzleti ok? Sikerült elérni az üzleti okot?


## <a name="next-steps"></a>Következő lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)
