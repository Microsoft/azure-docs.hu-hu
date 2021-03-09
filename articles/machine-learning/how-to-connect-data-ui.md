---
title: Kapcsolódás az Azure Storage Services-beli adatkezelési szolgáltatásaihoz
titleSuffix: Azure Machine Learning
description: Adattárolók és adatkészletek létrehozásával biztonságosan csatlakozhat az Azure-beli tárolási szolgáltatásokban tárolt adatforrásokhoz a Azure Machine Learning Studióval.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to, data4ml
ms.openlocfilehash: 0e2230bdcf2b2f4358db4a0d600506b711f39c45
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508231"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Kapcsolódás adatkapcsolathoz a Azure Machine Learning Studióval

Ebből a cikkből megtudhatja, hogyan férhet hozzá az adataihoz a [Azure Machine learning Studióval](overview-what-is-machine-learning-studio.md). Kapcsolódjon az Azure-beli Storage Services-adatokhoz [Azure Machine learning](how-to-access-data.md)adattárakkal, majd csomagolja ki az adatokat az ml-munkafolyamatokban [Azure Machine learning adatkészletekkel](how-to-create-register-datasets.md).

Az alábbi táblázat az adattárolók és adatkészletek előnyeit határozza meg és összegzi. 

|Objektum|Leírás| Előnyök|   
|---|---|---|
|Adattárak| Biztonságos kapcsolódás a Storage szolgáltatáshoz az Azure-ban a kapcsolati adatok tárolásával, például az előfizetés-AZONOSÍTÓval és a jogkivonat-hitelesítéssel a munkaterülethez társított [Key Vaultban](https://azure.microsoft.com/services/key-vault/) | Mivel az adatok tárolása biztonságos, <br><br> <li> Ne &nbsp; helyezzen el a hitelesítő &nbsp; &nbsp; adatokat &nbsp; vagy &nbsp; &nbsp; az eredeti adatforrásokat veszélyben. <li> Már nem kell a parancsfájlokba felvennie a kódot.
|Adathalmazok| Az adathalmazok létrehozásával az adatforrások helyére mutató hivatkozásokat és a metaadatok másolatait is létrehozza. Az adatkészletek segítségével <br><br><li> Az adathozzáférés a modell betanítása során.<li> Az adatmegosztás és együttműködés más felhasználókkal.<li> Kihasználhatja a nyílt forráskódú kódtárakat, például a pandat az adatfeltáráshoz. | Mivel az adathalmazok kiértékelése a lustán történik, és az adatokat a meglévő helyükön maradják, <br><br><li>Tároljon egyetlen másolatot az adattárolóban.<li> További tárolási költségek nem merülnek fel <li> Ne kockáztatja véletlenül az eredeti adatforrások módosítását.<li>Javítsa a ML-munkafolyamatok teljesítményének sebességét. 

Ha meg szeretné tudni, hogy az adattárolók és adatkészletek hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataiba, tekintse meg a [biztonságos hozzáférésről](concept-data.md#data-workflow) szóló cikket.

A Code első használatakor tekintse meg az alábbi cikkeket a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/) használatára a következő cikkekben:
* [Azure Storage-szolgáltatásokhoz való kapcsolódás adattárolókkal](how-to-access-data.md). 
* [Hozzon létre Azure Machine learning adatkészleteket](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Hozzáférés [Azure Machine learning studióhoz](https://ml.azure.com/).

- Egy Azure Machine Learning-munkaterület. [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md).

    -  Amikor létrehoz egy munkaterületet, az Azure Blob-tároló és az Azure-fájlmegosztás automatikusan adattárként lesz regisztrálva a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore` , illetve. Ha a blob Storage elegendő az igényeinek kielégítéséhez, `workspaceblobstore` akkor az alapértelmezett adattárként van beállítva, és már konfigurálva van a használatra. Ellenkező esetben szükség van egy Storage-fiókra az Azure-ban egy [támogatott tárolási típussal](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Adattárolók létrehozása

[Ezekből az Azure Storage-megoldásokból is létrehozhat adattárakat](how-to-access-data.md#matrix). **A nem támogatott tárolási megoldások esetében**, valamint a kimenő adatforgalomnak a ml-kísérletek során történő mentéséhez az adatait egy támogatott Azure Storage-megoldásba kell [áthelyeznie](how-to-access-data.md#move) . [További információ az](how-to-access-data.md)adattárolók használatáról. 

Hozzon létre egy új adattárt néhány lépésben a Azure Machine Learning Studióval.

> [!IMPORTANT]
> Ha az adattároló-fiók egy virtuális hálózaton található, további konfigurációs lépések szükségesek ahhoz, hogy a Studio hozzáférhessen az adataihoz. A megfelelő konfigurációs lépések végrehajtásához tekintse meg a [hálózati elkülönítés & az adatvédelem](how-to-enable-studio-virtual-network.md) témakört.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/).
1. A **kezelés** **alatt kattintson a** bal oldali ablaktábla adattárolók elemére.
1. Válassza az **+ új adattár** lehetőséget.
1. Fejezze be az űrlapot egy új adattár létrehozásához és regisztrálásához. Az űrlap intelligens módon frissül saját maga, az Azure Storage-típus és a hitelesítési típus választása alapján. Az űrlap feltöltéséhez szükséges hitelesítő adatok megkereséséhez tekintse meg a [tárolási hozzáférés és engedélyek című szakaszt](#access-validation) .

Az alábbi példa bemutatja, hogyan néz ki az űrlap az **Azure Blob-adattár** létrehozásakor:

![Űrlap új adattárhoz](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Adatkészletek létrehozása

Az adattár létrehozása után hozzon létre egy adatkészletet az adataival való kommunikációhoz. Az adatkészletek a gépi tanulási feladatokhoz, például a képzéshez egy lustán kiértékelt adatforrásba csomagolják az adatokat. [További információ az adathalmazokról](how-to-create-register-datasets.md).

Kétféle adatkészlet létezik, a FileDataset és a TabularDataset. 
A [FileDatasets](how-to-create-register-datasets.md#filedataset) egy vagy több fájlra vagy nyilvános URL-címekre mutató hivatkozásokat hoz létre. Míg a [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) táblázatos formában jeleníti meg az adatokat. 

Az alábbi lépések és animációk bemutatják, hogyan hozhat létre adatkészletet [Azure Machine learning Studióban](https://ml.azure.com).

> [!Note]
> A Azure Machine Learning Studióval létrehozott adatkészletek automatikusan regisztrálva lesznek a munkaterületen.

![Adatkészlet létrehozása a felhasználói felületen](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Adatkészlet létrehozása a Studióban:
1. Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).
1. Válassza az **adatkészletek** lehetőséget a bal oldali ablaktábla **eszközök** részén.
1. Válassza az **adatkészlet létrehozása** lehetőséget az adatkészlet forrásának kiválasztásához. Ez a forrás lehet helyi fájl, adattár, nyilvános URL-cím vagy [Azure Open-adatkészlet](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Válassza a **táblázatos** vagy a **fájl** lehetőséget az adatkészlet típusához.
1. Kattintson a **tovább** gombra az **adattár és a fájl kiválasztása** űrlap megnyitásához. Ezen az űrlapon kiválaszthatja, hogy hol szeretné megőrizni az adatkészletet a létrehozás után, valamint kiválaszthatja az adatkészlethez használni kívánt adatfájlokat.
    1. Az érvényesítés kihagyásának engedélyezése, ha az adatai virtuális hálózaton vannak. További információ a [virtuális hálózatok elkülönítéséről és adatvédelméről](how-to-enable-studio-virtual-network.md).
    1. Táblázatos adatkészletek esetében megadhat egy "idősor" tulajdonságot, amely lehetővé teszi az időkereten alapuló műveletek engedélyezését. Megtudhatja, hogyan [adhatja hozzá a idősor-tulajdonságokat az adatkészlethez](how-to-monitor-datasets.md#studio-dataset).
1. Válassza a **tovább** lehetőséget a **beállítások és az előnézet** és a **séma** űrlapok feltöltéséhez; ezeket a fájltípusok alapján intelligens módon töltik fel, és az űrlapokon való létrehozás előtt további beállításokkal konfigurálhatja az adatkészletet. 
1. Kattintson a **tovább** gombra a **részletek megerősítése** űrlap áttekintéséhez. Válassza ki a beállításokat, és hozzon létre egy opcionális adatprofilt az adatkészlethez. További információ az [adatprofilkészítésről](#profile).
1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Adatprofil és előnézet

Az adatkészlet létrehozása után ellenőrizze, hogy megtekintheti-e a profilt és az előnézetet a Studióban a következő lépésekkel. 

1. Bejelentkezés a [Azure Machine learning studióba](https://ml.azure.com/)
1. Válassza az **adatkészletek** lehetőséget a bal oldali ablaktábla **eszközök** részén.
1. Válassza ki a megtekinteni kívánt adatkészlet nevét. 
1. Kattintson a **Tallózás** fülre. 
1. Válassza az **előnézet** vagy a **profil** fület. 

![Adatkészlet profiljának és előnézetének megtekintése](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Az adatkészletek széles skáláját veheti igénybe, így ellenőrizheti, hogy az adatkészlete ML-re van-e állítva. A nem numerikus oszlopokhoz csak olyan alapszintű statisztikák tartoznak, mint a min, a Max és a errors. A numerikus oszlopok esetében statisztikai pillanataikat és becsült quantiles is áttekintheti. 

Azure Machine Learning adatkészlet adatprofilja az alábbiakat tartalmazza:

>[!NOTE]
> A lényegtelen típusokkal rendelkező funkciók esetében üres bejegyzések jelennek meg.

|Statisztikai adatok|Leírás
|------|------
|Szolgáltatás| Az összesíteni kívánt oszlop neve.
|Profil| Beágyazott vizualizáció a következtetett típus alapján. Például a karakterláncok, a logikai értékek és a dátumok értékének számít, míg a tizedesjegyek (numerikus értékek) közelítő hisztogramtal rendelkeznek. Ez lehetővé teszi az adateloszlás gyors megismerését.
|Típus eloszlása| Az oszlopokban lévő típusok soron belüli értékeinek száma. A nullák a saját típusúak, így ez a vizualizáció hasznos lehet a páratlan vagy hiányzó értékek észleléséhez.
|Típus|Az oszlop késleltetett típusa. A lehetséges értékek a következők: karakterláncok, logikai értékek, dátumok és tizedesjegyek.
|Min| Az oszlop minimális értéke. Az üres bejegyzések olyan szolgáltatások esetében jelennek meg, amelyek típusa nem rendelkezik a bennük rejlő sorrendtel (például logikai értékekkel).
|Max| Az oszlop maximális értéke. 
|Darabszám| Az oszlop hiányzó és nem hiányzó bejegyzéseinek száma.
|Hiányzó darabszám| A hiányzó oszlop bejegyzéseinek száma. Az üres karakterláncok és hibák értékekként vannak kezelve, így nem járulnak hozzá a "nem hiányzó darabszám" értékhez.
|Kvantilisek| Az egyes quantile megközelített értékek biztosítják az adatok terjesztésének érzékét.
|Középérték| Az oszlop számtani középértéke vagy átlaga.
|Szórás| Az oszlop adateloszlásának vagy variációjának mértékét.
|Variance| Az oszlop adatainak kiszóródásának mértéke az átlagos értéktől számítva. 
|Ferdeség| Az oszlop adatainak normál eloszlásból való megmérése.
|Csúcsosságát| Azt méri, hogy az oszlop adatmennyisége milyen mértékben lett összehasonlítva a normál eloszlással.

## <a name="storage-access-and-permissions"></a>Tárterület-hozzáférés és engedélyek

Az Azure Storage szolgáltatáshoz való biztonságos kapcsolódás érdekében Azure Machine Learning megköveteli, hogy rendelkezzen a megfelelő adattároló eléréséhez szükséges engedéllyel. Ez a hozzáférés az adattár regisztrálásához használt hitelesítési hitelesítő adatoktól függ.

### <a name="virtual-network"></a>Virtuális hálózat

Ha az adattároló-fiók egy **virtuális hálózaton** található, további konfigurációs lépések szükségesek ahhoz, hogy Azure Machine learning hozzáférjen az adataihoz. Az adattár létrehozásakor és regisztrálása során a megfelelő konfigurációs lépések végrehajtásához tekintse meg a [hálózati elkülönítés & az adatvédelem](how-to-enable-studio-virtual-network.md) témakört.  

### <a name="access-validation"></a>Hozzáférés ellenőrzése

**A kezdeti adattár-létrehozási és regisztrációs folyamat részeként** Azure Machine learning automatikusan ellenőrzi, hogy a mögöttes tárolási szolgáltatás létezik-e, és hogy a felhasználó által megadott rendszerbiztonsági tag (Felhasználónév, szolgáltatásnév vagy sas-jogkivonat) hozzáfér-e a megadott tárolóhoz.

Az **adattár létrehozása után** ezt az ellenőrzést csak olyan metódusok hajtják végre, amelyek hozzáférést igényelnek a mögöttes tárolóhoz, **nem** minden alkalommal, amikor az adattár-objektumokat lekérik. Például az ellenőrzés akkor történik meg, ha le szeretné tölteni a fájlokat az adattárból. Ha azonban csak módosítani szeretné az alapértelmezett adattárat, akkor az érvényesítés nem történik meg.

A mögöttes tárolási szolgáltatáshoz való hozzáférésének hitelesítéséhez a létrehozni kívánt adattár típusa alapján megadhatja a fiók kulcsát, a közös hozzáférési aláírások (SAS) tokenjét vagy az egyszerű szolgáltatást. A [tárolási típus mátrixa](how-to-access-data.md#matrix) felsorolja az egyes adattár-típusoknak megfelelő támogatott hitelesítési típusokat.

Megtalálhatja a fiók kulcsát, az SAS-tokent és az egyszerű szolgáltatásnév adatait a [Azure Portal](https://portal.azure.com).

* Ha a hitelesítéshez fiókot vagy SAS-tokent szeretne használni, válassza ki a **Storage-fiókok** lehetőséget a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot.
  * Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve.
      1. A fiókok kulcsai lapon lépjen a **hozzáférési kulcsok** elemre a **Beállítások** ablaktáblán.
      1. SAS-tokenek esetén a beállítások ablaktáblán válassza a **közös hozzáférésű aláírások** **lehetőséget** .

* Ha egy [egyszerű szolgáltatásnév](../active-directory/develop/howto-create-service-principal-portal.md) használatát tervezi hitelesítésre, lépjen a **Alkalmazásregisztrációk** , és válassza ki a használni kívánt alkalmazást.
    * A megfelelő **áttekintő** oldal tartalmazni fogja a szükséges információkat, például a bérlő azonosítóját és az ügyfél-azonosítót.

> [!IMPORTANT]
> * Ha módosítania kell egy Azure Storage-fiók (fiók vagy SAS-token) hozzáférési kulcsait, akkor ügyeljen arra, hogy szinkronizálja az új hitelesítő adatokat a munkaterülettel és a hozzá csatlakoztatott adattárakkal. Útmutató [a frissített hitelesítő adatok szinkronizálásához](how-to-change-storage-access-key.md). <br> <br>
> * Ha törli a regisztrációt, és újra regisztrálja az adattárolót ugyanazzal a névvel, és a művelet meghiúsul, előfordulhat, hogy a munkaterület Azure Key Vault nem rendelkezik a helyreállítható törléssel. Alapértelmezés szerint a Soft-delete engedélyezve van a munkaterület által létrehozott Key Vault-példányon, de ez nem engedélyezhető, ha meglévő kulcstartót használt, vagy pedig az október 2020. előtt létrehozott munkaterülettel rendelkezik. További információ a helyreállítható törlés engedélyezéséről: [a meglévő kulcstartó bekapcsolásának bekapcsolása]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

### <a name="permissions"></a>Engedélyek

Az Azure Blob Container és Azure Data Lake 2. generációs tárolók esetében ellenőrizze, hogy a hitelesítési hitelesítő adatok rendelkeznek-e a **Storage blob Adatolvasóval** . További információ a [Storage blob-Adatolvasóról](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). A fiók SAS-tokenje alapértelmezés szerint nem rendelkezik engedélyekkel. 
* Az adatok **olvasási hozzáféréséhez** a hitelesítő adatoknak legalább listáját és olvasási engedélyeket kell tartalmazniuk a tárolók és objektumok számára. 

* Az **adatírások eléréséhez** írási és hozzáadási engedélyekre is szükség van.

## <a name="train-with-datasets"></a>Betanítás adathalmazok használatával

Az adatkészleteket a gépi tanulási kísérletekben használhatja a ML-modellek betanításához. [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Következő lépések

* [Lépésenkénti példa a TabularDatasets és az automatizált gépi tanulással való képzésre](tutorial-first-experiment-automated-ml.md).

* [Modell betanítása](how-to-set-up-training-targets.md).

* További példákat az adatkészlet betanításával kapcsolatban a [minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)című témakörben talál.