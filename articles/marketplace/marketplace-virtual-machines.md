---
title: Virtuálisgép-ajánlat megtervezése – Microsoft kereskedelmi piactér
description: Ez a cikk ismerteti a virtuális gépek Azure Marketplace-re való közzétételének követelményeit.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 9169d691b58ad4a59796123c64f856128a0f8c83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879326"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Virtuális gépek ajánlatának megtervezése

Ez a cikk a virtuális gépek (VM) kereskedelmi piactéren való közzétételének különböző lehetőségeit és követelményeit ismerteti. A VM-ajánlatok az Azure Marketplace-en üzembe helyezett és számlázható ajánlatok.

Mielőtt elkezdené, [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](./partner-center-portal/create-account.md) , és ellenőrizze, hogy a fiókja regisztrálva van-e a kereskedelmi piactér programban.

### <a name="technical-fundamentals"></a>Technikai alapismeretek

Az ajánlatok tervezésének, kiépítésének és tesztelésének folyamata időt vesz igénybe, és az Azure platformon és az ajánlat létrehozásához használt technológiákban is szaktudást igényel. A mérnöki csapatnak rendelkeznie kell az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)szolgáltatásával, valamint az [Azure-alkalmazások tervezésével és architektúrával](https://azure.microsoft.com/solutions/architecture/)kapcsolatos ismeretekkel. Tekintse meg ezeket a további technikai forrásokat: 

- Oktatóanyagok
  - [Linux rendszerű virtuális gépek](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows rendszerű virtuális gépek](../virtual-machines/windows/tutorial-manage-vm.md)

- Példák
  - [Azure CLI-minták Linux rendszerű virtuális gépekhez](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell Linux rendszerű virtuális gépekhez](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Azure CLI-minták Windows rendszerű virtuális gépekhez](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell Windows rendszerű virtuális gépekhez](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Technikai követelmények

A virtuálisgép-ajánlatok a következő műszaki követelményekkel rendelkeznek:

- Elő kell készítenie egy operációs rendszer virtuális merevlemezét (VHD). Az adatlemezek virtuális merevlemezei nem kötelezőek. Ezt az alábbiakban részletesebben ismertetjük.
- Az ügyfél bármikor lemondhatja az ajánlatot.
- Létre kell hoznia legalább egy csomagot az ajánlathoz. A csomag díjszabása a kiválasztott [licencelési lehetőség](#licensing-options) alapján történik.
   > [!IMPORTANT]
   > A tervekben szereplő összes virtuálisgép-rendszerképnek azonos számú adatlemezzel kell rendelkeznie.

A virtuális gépek két összetevőt tartalmaznak:

- **Operációs VHD** – az ajánlatával üzembe helyezett operációs rendszert és megoldást tartalmazza. A VHD előkészítési folyamata attól függően eltérő, hogy Linux-, Windows-vagy egyéni virtuális gép-e.
- **Adatlemez virtuális merevlemezei** (nem kötelező) – dedikált, állandó tárterület egy virtuális géphez. Ne használja az operációs rendszer VHD-jét (például a C: meghajtót) az állandó információk tárolásához. 
    - Akár 16 adatlemez is felhasználható.
    - Adatlemez esetén használjon egy VHD-t, még akkor is, ha a lemez üres.

    > [!NOTE]
    > A használt operációs rendszertől függetlenül csak a megoldáshoz szükséges adatlemezek minimális számát adja hozzá. Az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket az üzembe helyezéskor, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is.

A technikai eszközök előkészítésével kapcsolatos részletes utasításokért lásd: [virtuális gép létrehozása jóváhagyott alap használatával](azure-vm-create-using-approved-base.md) vagy [virtuális gép létrehozása a saját rendszerkép használatával](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Előnézet célközönsége

Az előzetes verzió célközönsége az Azure Marketplace-en való közzététel előtt fér hozzá a virtuálisgép-ajánlathoz, hogy az élő közzététel előtt tesztelje a végpontok közötti funkciókat. A **célközönség előnézete** lapon megadhatja az előzetes verziójú célközönséget. 

> [!NOTE]
> Az előzetes verzió célközönsége különbözik egy privát csomagtól. Egy privát csomag csak a kiválasztott célközönség számára érhető el. Ez lehetővé teszi, hogy egy egyéni csomagot adott ügyfelekkel egyeztesse. További információt a következő szakaszban talál: csomagok.

Meghívhat a Microsoft-fiók (MSA) vagy a Azure Active Directory (Azure AD) e-mail-címére. Legfeljebb 10 e-mail-címet adjon meg manuálisan, vagy importáljon akár 20-at egy. CSV-fájllal. Ha az ajánlata már élő, akkor is megadhatja az előnézeti közönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

## <a name="plans-and-pricing"></a>Csomagok és díjszabás

A VM-ajánlatokhoz legalább egy csomag szükséges. A terv meghatározza a megoldás hatókörét és korlátait, valamint a kapcsolódó díjszabást. Az ajánlathoz több csomagot is létrehozhat, így az ügyfelek különböző technikai és licencelési lehetőségeket biztosíthatnak, valamint ingyenes próbaverziókat is használhatnak. Tekintse meg a [kereskedelmi piactérre vonatkozó terveket és díjszabást](plans-pricing.md) a csomagokkal kapcsolatos általános útmutatásért, beleértve a díjszabási modelleket, az ingyenes próbaverziókat és a saját terveket. 

A virtuális gépek teljes mértékben elérhetők az utólagos elszámolású vagy a saját licencű (BYOL) licencelési modellek használatával. A Microsoft üzemelteti a kereskedelmi tranzakciót, és az Ön nevében számlázza az ügyfelet. Az ügyfél és a Microsoft közötti előnyben részesített fizetési kapcsolat előnyeit használhatja, beleértve a nagyvállalati szerződéseket is. További információ: [kereskedelmi Piactéri Transact-képességek](./marketplace-commercial-transaction-capabilities-and-considerations.md).

> [!NOTE]
> Az Azure-előfizetések (korábbi nevén: pénzügyi kötelezettségvállalás) egy Nagyvállalati Szerződéshöz társítva használhatók a virtuális gép Azure-beli használatára, de nem a szoftveres licencelési díjakra.

### <a name="licensing-options"></a>Licencelési lehetőségek

Az új virtuálisgép-ajánlat közzétételének előkészítése során el kell döntenie, hogy melyik licencelési lehetőséget választja ki. Ez határozza meg, hogy milyen további információkat kell megadnia az ajánlat a partner Centerben való létrehozásakor.

A virtuális gépekhez elérhető licencelési lehetőségek a következők:

| Licencelési lehetőség | Tranzakciós folyamat |
| --- | --- |
| Ingyenes próbaidőszak | Egy-, három vagy hat hónapos ingyenes próbaverziót kínál ügyfeleinek. |
| Tesztelési meghajtó | Ez a beállítás lehetővé teszi, hogy az ügyfelek további díjak nélkül értékeljék a virtuális gépeket. Nincs szükségük meglévő Azure-ügyfélre a próbaidőszakos felhasználói élményhez. Részletekért lásd: [Mi az a test Drive?](./what-is-test-drive.md) |
| BYOL | A saját licencelési lehetőség használata lehetővé teszi, hogy az ügyfelek meglévő szoftver-licenceket hozzanak az Azure-ba.\* |
| Használat-alapú | Ez a beállítás lehetővé teszi, hogy az ügyfelek óradíjat fizessenek. |
| Interaktív bemutató  | Interaktív bemutató használatával biztosíthatja ügyfelei számára a megoldás irányítását. Az előny az, hogy a próbaverziót a komplex megoldás bonyolult beállítása nélkül is kínálhatja. |
|

\* Közzétevőként a szoftverlicenc-tranzakció valamennyi aspektusát támogatja, beleértve a rendelést, a teljesítést, a mérést, a számlázást, a számlázást, a fizetést és a gyűjtést.

Az alábbi példa egy virtuálisgép-ajánlatot mutat be az Azure Marketplace-en, amely használaton alapuló díjszabással rendelkezik.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Minta virtuálisgép-ajánlat képernyője.":::

### <a name="private-plans"></a>Saját csomagok

A virtuális gép felderítését és üzembe helyezését egy adott ügyfélre korlátozhatja a rendszerkép és a díjszabás privát csomagként való közzétételével. A Private-csomagok lehetővé teszi, hogy exkluzív ajánlatokat hozzon létre a legközelebbi ügyfelei számára, és testreszabott szoftvereket és feltételeket nyújtson. A testreszabott feltételek lehetővé teszik különféle forgatókönyvek kiemelését, beleértve a speciális díjszabási és használati feltételeket, valamint a korlátozott kiadású szoftverek korai elérését. A privát csomagok lehetővé teszik, hogy meghatározott díjszabást vagy termékeket biztosítson az ügyfelek korlátozott készlete számára.

További információ: a kereskedelmi Piactéri ajánlatok és [a Microsoft kereskedelmi piactéren elérhető privát ajánlatok](private-offers.md) [csomagjai és díjszabása](plans-pricing.md) .

## <a name="test-drives"></a>Tesztverziók

Engedélyezheti a virtuális gép tesztelési meghajtójának engedélyezését. A tesztelési meghajtók adott számú óráig biztosítanak hozzáférést egy előre konfigurált környezethez. Engedélyezheti a tesztelési meghajtókat bármilyen közzétételi lehetőséghez, azonban ez a funkció további követelményekkel is rendelkezik. További információ a tesztelési meghajtókról: [Mi az a test Drive?](what-is-test-drive.md). A különböző típusú tesztelési meghajtók konfigurálásával kapcsolatos információkért lásd: a tesztvezetés [technikai konfigurációjának tesztelése](test-drive-technical-configuration.md).

> [!TIP]
> A tesztelési meghajtó nem azonos az [ingyenes próbaverzióval](plans-pricing.md#free-trials). Tesztelési meghajtót, ingyenes próbaverziót vagy mindkettőt is kipróbálhat. A megoldás a rögzített időszakra is biztosítja ügyfelei számára a megoldást. A test Drive azonban magában foglalja a termék legfontosabb funkcióit és előnyeit is, amelyek egy valós megvalósítási forgatókönyvben jelennek meg.

## <a name="customer-leads"></a>Ügyfél-érdeklődők

Az ügyfelek adatainak összegyűjtéséhez az ajánlatot az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötni. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és online áruházát, ahol az ajánlatot megtalálták, a rendszer a konfigurált CRM-rendszerbe küldi el. A kereskedelmi piactér számos CRM-rendszert támogat, valamint egy Azure-tábla használatát vagy egy HTTPS-végpont konfigurálását a Power automatizálás használatával.

A CRM-kapcsolatok bármikor hozzáadhatók vagy módosíthatók az ajánlat létrehozásakor vagy azt követően is. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Jogi szerződések

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

Ha úgy dönt, hogy a normál szerződést használja, lehetősége van arra, hogy univerzális módosítási feltételeket adjon hozzá, és legfeljebb 10 egyéni módosítást alkalmazzon a standard szerződéshez. A normál szerződés helyett a saját használati feltételeit is használhatja. Ezeket a részleteket a **Tulajdonságok** lapon fogja kezelni. Részletes információ: [standard szintű szerződés a Microsoft kereskedelmi piactérről](standard-contract.md).

> [!NOTE]
> Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés vagy a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szintű szerződés feltételeit, ezt a standard szintű szerződés módosításaival teheti meg.

## <a name="cloud-solution-providers"></a>Felhőszolgáltatók

Ha az ajánlatot a partner Centerben hozza létre, megjelenik az **viszonteladás a CSP** -n keresztül lapon. Ez a beállítás lehetővé teszi, hogy a Microsoft Cloud Solution Providers (CSP) program részét képező partnerek a virtuális gépet egy csomagban lévő ajánlat részeként értékesítsenek. Az összes saját Licences (BYOL) csomag automatikusan bekerül a programba. Dönthet úgy is, hogy nem BYOL terveket is választ. További információért lásd a [Cloud Solution Provider programot](cloud-solution-providers.md) . 

> [!NOTE]
> Már elérhető a felhőalapú megoldás-szolgáltató (CSP) Partner Channel-beli aktiválása. Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [**Cloud Solution Providers**](./cloud-solution-providers.md)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-ajánlat létrehozása az Azure Marketplace-en](azure-vm-create.md)
- [Hozzon létre egy virtuális gépet jóváhagyott alap használatával](azure-vm-create-using-approved-base.md) , vagy [hozzon létre egy virtuális gépet saját rendszerkép használatával](azure-vm-create-using-own-image.md).
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)