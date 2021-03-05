---
title: A Virtual Network elkülönítése és biztonsági áttekintése
titleSuffix: Azure Machine Learning
description: A munkaterület-erőforrások és a számítási környezetek védelméhez használjon egy elkülönített Azure-Virtual Network Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions, contperf-fy21q1
ms.openlocfilehash: 1309ad1b3e3f6bd6f9b543959220bf71c569f083
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175005"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Virtual Network elkülönítés és Adatvédelem – áttekintés

Ebből a cikkből megtudhatja, hogyan használhatja a virtuális hálózatokat (virtuális hálózatok) a Azure Machine Learning hálózati kommunikációjának biztonságossá tételéhez. Ez a cikk egy példát mutat be, amely bemutatja, hogyan konfigurálhat egy teljes virtuális hálózatot.

Ez a cikk egy öt részes sorozat első része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el ezt az áttekintő cikket a fogalmak megismeréséhez. 

A sorozat további cikkei:

**1. VNet – áttekintés**  >  [2. A](how-to-secure-workspace-vnet.md)  >  [3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)  >  [. A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy a következő témakörök ismerete szükséges:
+ [Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)
+ [IP-hálózatkezelés](../virtual-network/public-ip-addresses.md)
+ [Azure Private Link](how-to-configure-private-link.md)
+ [Hálózati biztonsági csoportok (NSG)](../virtual-network/network-security-groups-overview.md)
+ [Hálózati tűzfalak](../firewall/overview.md)

## <a name="example-scenario"></a>Példaforgatókönyv

Ebben a szakaszban megtudhatja, hogyan történik a közös hálózati forgatókönyv beállítása a magánhálózati IP-címekkel való Azure Machine Learning kommunikáció biztonságossá tételére.

Az alábbi táblázat összehasonlítja, hogyan fér hozzá a szolgáltatások a Azure Machine Learning hálózat különböző részeihez egy VNet és VNet nélkül.

| Eset | Munkaterület | Társított erőforrások | Számítási környezet betanítása | Viszonyítási számítási környezet |
|-|-|-|-|-|-|
|**Nincs virtuális hálózat**| Nyilvános IP-cím | Nyilvános IP-cím | Nyilvános IP-cím | Nyilvános IP-cím |
|**Virtuális hálózatok erőforrásainak védelme**| Magánhálózati IP-cím (privát végpont) | Nyilvános IP-cím (szolgáltatási végpont) <br> **vagy** <br> Magánhálózati IP-cím (privát végpont) | Magánhálózati IP-cím | Magánhálózati IP-cím  | 

* **Munkaterület** – hozzon létre egy privát végpontot a VNet a munkaterületen lévő privát hivatkozáshoz való csatlakozáshoz. A magánhálózati végpont több magánhálózati IP-cím használatával csatlakoztatja a munkaterületet a vnet.
* **Társított erőforrás** -szolgáltatási végpontok vagy magánhálózati végpontok olyan munkaterület-erőforrásokhoz való kapcsolódáshoz, mint például az Azure Storage, a Azure Key Vault és az Azure Container Services.
    * A **szolgáltatási végpontok** biztosítják a virtuális hálózat identitását az Azure-szolgáltatásnak. Miután engedélyezte a szolgáltatási végpontokat a virtuális hálózaton, hozzáadhat egy virtuális hálózati szabályt az Azure-szolgáltatási erőforrások biztonságossá tételéhez a virtuális hálózaton. A szolgáltatási végpontok nyilvános IP-címeket használnak.
    * A **privát végpontok** olyan hálózati adapterek, amelyek biztonságosan csatlakoznak az Azure privát kapcsolaton keresztül működő szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet.
* A **számítási hozzáférés betanítása** – hozzáférés betanítása a számítási célokhoz, például Azure Machine learning számítási példányhoz, valamint a saját IP-címekkel biztonságosan Azure Machine learning számítási fürtökhöz. 
* A **számítási hozzáférés következtetése** – az Azure Kubernetes Services (ak) számítási fürtök magánhálózati IP-címmel való elérése.


A következő öt szakasz bemutatja, hogyan védheti meg a fent ismertetett hálózati forgatókönyvet. A hálózat védelme érdekében a következőket kell tennie:

1. A [**munkaterület és a kapcsolódó erőforrások**](#secure-the-workspace-and-associated-resources)védelme.
1. Gondoskodjon a [**képzési környezet**](#secure-the-training-environment)védelméről.
1. Gondoskodjon a [**következtetési környezet**](#secure-the-inferencing-environment)védelméről.
1. Opcionálisan: a [**Studio funkciójának engedélyezése**](#optional-enable-studio-functionality).
1. [ **Tűzfalbeállítások** konfigurálása](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>A munkaterület és a kapcsolódó erőforrások védelme

A munkaterület és a kapcsolódó erőforrások védelméhez kövesse az alábbi lépéseket. Ezek a lépések lehetővé teszik, hogy a szolgáltatások kommunikálhassanak a virtuális hálózaton.

1. Hozzon létre egy [magánhálózati kapcsolattal rendelkező munkaterületet](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) a VNet és a munkaterület közötti kommunikáció engedélyezéséhez.
1. Azure Key Vault hozzáadása a virtuális hálózathoz [szolgáltatási végponttal](../key-vault/general/overview-vnet-service-endpoints.md) vagy [privát végponttal](../key-vault/general/private-link-service.md). A Key Vault beállítása a [következőre: "megbízható Microsoft-szolgáltatások engedélyezése a tűzfal megkerüléséhez"](how-to-secure-workspace-vnet.md#secure-azure-key-vault).
1. Adja hozzá az Azure Storage-fiókot a virtuális hálózathoz egy [szolgáltatási végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) vagy egy [privát végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints).
1. [Azure Container Registry konfigurálása privát végpont használatára](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

![Architektúra-diagram, amely bemutatja, hogy a munkaterület és a kapcsolódó erőforrások hogyan kommunikálnak egymással a szolgáltatási végpontokon vagy a VNet belüli privát végpontokon](./media/how-to-network-security-overview/secure-workspace-resources.png)

A lépések végrehajtásával kapcsolatos részletes útmutatásért lásd: [Azure Machine learning munkaterület biztonságossá tétele](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Korlátozások

A munkaterület és a kapcsolódó erőforrások virtuális hálózaton belüli biztonságossá tétele a következő korlátozásokkal jár:
- A privát hivatkozást tartalmazó Azure Machine Learning munkaterület nem érhető el a Azure Government vagy az Azure China 21Vianet régióiban.
- Minden erőforrásnak azonos VNet kell lennie. Az azonos VNet lévő alhálózatok azonban engedélyezettek.

## <a name="secure-the-training-environment"></a>A képzési környezet biztonságossá tétele

Ebből a szakaszból megtudhatja, hogyan védheti meg Azure Machine Learning képzési környezetét. Azt is megtudhatja, hogyan végezheti el a Azure Machine Learning betanítási feladatok elvégzését a hálózati konfigurációk együttműködésének megismeréséhez.

A képzési környezet biztonságossá tételéhez kövesse az alábbi lépéseket:

1. Hozzon létre egy Azure Machine Learning [számítási példányt és egy számítógép-fürtöt a virtuális hálózaton](how-to-secure-training-vnet.md#compute-instance) a betanítási feladatok futtatásához.
1. [Bejövő kommunikáció engedélyezése Azure batch szolgáltatásból](how-to-secure-training-vnet.md#mlcports) , így a Batch szolgáltatás feladatokat küldhet a számítási erőforrásoknak. 

![Architektúra-diagram, amely bemutatja a felügyelt számítási fürtök és példányok biztonságossá tételét](./media/how-to-network-security-overview/secure-training-environment.png)

A lépések végrehajtásával kapcsolatos részletes útmutatásért lásd a [képzési környezet biztonságossá](how-to-secure-training-vnet.md)tételét ismertető témakört. 

### <a name="example-training-job-submission"></a>Példa a betanítási feladatok beküldésére 

Ebből a szakaszból megtudhatja, hogyan Azure Machine Learning biztonságosan kommunikálni a szolgáltatások között a betanítási feladatok elküldéséhez. Ez azt mutatja be, hogy az összes konfiguráció hogyan működik együtt a kommunikáció biztonságossá tételéhez.

1. Az ügyfél feltölti a betanítási parancsfájlokat és betanítási adatokkal a szolgáltatással vagy privát végponttal védett Storage-fiókokba.

1. Az ügyfél a privát végponton keresztül elküld egy betanítási feladatot a Azure Machine Learning munkaterületre.

1. Azure Batch szolgáltatások fogadják a feladatot a munkaterületről, és elküldik a betanítási feladatot a számítási környezetnek a számítási erőforrással kiépített nyilvános terheléselosztó használatával. 

1. A számítási erőforrás megkapja a feladatot, és megkezdi a betanítást. A számítási erőforrások biztonságos Storage-fiókokhoz férnek hozzá a betanítási fájlok letöltéséhez és a kimenet feltöltéséhez.

### <a name="limitations"></a>Korlátozások

- Az Azure számítási példányának és az Azure számítási fürtöknek ugyanabban a VNet, régióban és előfizetésben kell lenniük, mint a munkaterületnek és a hozzá tartozó erőforrásoknak. 

## <a name="secure-the-inferencing-environment"></a>A következtetési környezet biztonságossá tétele

Ebben a szakaszban megismerheti a következtetési környezet biztonságossá tételéhez rendelkezésre álló lehetőségeket. Javasoljuk, hogy az Azure Kubernetes Services (ak) fürtöket nagy léptékű, éles környezetben üzemelő példányokhoz használja.

Egy virtuális hálózatban két lehetőség van az AK-fürtökhöz:

- Helyezzen üzembe vagy csatoljon egy alapértelmezett AK-fürtöt a VNet.
- Csatoljon egy privát AK-fürtöt a VNet.

Az **alapértelmezett AK-fürtök** egy nyilvános IP-címmel rendelkező vezérlési síkon rendelkeznek. Az üzembe helyezés során hozzáadhat egy alapértelmezett AK-fürtöt a VNet, vagy csatlakoztathat egy fürtöt a létrehozása után.

A **privát AK-fürtöknek** van egy vezérlő síkja, amely csak privát IP-címeken keresztül érhető el. A fürt létrehozása után csatolni kell a privát AK-fürtöket.

Az alapértelmezett és a privát fürtök hozzáadásával kapcsolatos részletes utasításokért lásd: [következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md). 

A következő hálózati diagram egy biztonságos Azure Machine Learning munkaterületet mutat be a virtuális hálózathoz csatlakoztatott privát AK-fürttel.

![Architektúra-diagram, amely bemutatja, hogyan csatolhat egy privát AK-fürtöt a virtuális hálózathoz. Az AK vezérlő síkja az ügyfél VNet kívülre kerül](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Korlátozások
- Az AK-fürtöknek ugyanahhoz a VNet kell tartozniuk, mint a munkaterületnek és a hozzá tartozó erőforrásoknak. 

## <a name="optional-enable-public-access"></a>Opcionális: nyilvános hozzáférés engedélyezése

A munkaterületet egy privát végponton keresztül biztonságossá teheti egy VNet mögött, és továbbra is engedélyezheti a hozzáférést a nyilvános interneten keresztül. A kezdeti konfiguráció ugyanaz, mint a [munkaterület és a kapcsolódó erőforrások védelme](#secure-the-workspace-and-associated-resources). 

Miután a munkaterületet privát kapcsolattal [biztosította, engedélyezheti a nyilvános hozzáférést](how-to-configure-private-link.md#enable-public-access). Ezt követően a munkaterületet a nyilvános internetről és a VNet is elérheti.

### <a name="limitations"></a>Korlátozások

- Ha a Azure Machine Learning studiót a nyilvános interneten keresztül használja, bizonyos funkciók, például a tervező nem tud hozzáférni az adataihoz. Ez a probléma akkor fordul elő, ha az adattárolást egy olyan szolgáltatás tárolja, amely a VNet mögött található. Például egy Azure Storage-fiók.
## <a name="optional-enable-studio-functionality"></a>Opcionális: a Studio funkciójának engedélyezése

[A munkaterület védelme](#secure-the-workspace-and-associated-resources)  >  [A képzési környezet](#secure-the-training-environment)  >  biztonságossá tétele [A következtetési környezet](#secure-the-inferencing-environment)  >  biztonságossá tétele A **Studio funkcióinak engedélyezése**  >  [Tűzfalbeállítások konfigurálása](#configure-firewall-settings)

Ha a tároló egy VNet van, először további konfigurációs lépéseket kell végrehajtania a [Studio](overview-what-is-machine-learning-studio.md)teljes funkcionalitásának engedélyezéséhez. Alapértelmezés szerint a következő szolgáltatás le van tiltva:

* A Studióban tárolt előzetes verzió.
* Jelenítse meg a tervezőben tárolt adatmegjelenítést.
* Modell üzembe helyezése a tervezőben.
* AutoML-kísérlet küldése.
* Címkéző projekt elindítása.

Ha egy VNet belül szeretné engedélyezni a teljes Studio-funkciókat, tekintse meg a [Azure Machine learning Studio használata virtuális hálózatban](how-to-enable-studio-virtual-network.md#configure-data-access-in-the-studio)című témakört. A Studio a szolgáltatási végpontok vagy a magánhálózati végpontok használatával támogatja a Storage-fiókokat.

### <a name="limitations"></a>Korlátozások

A [ml által támogatott adatfeliratok](how-to-create-labeling-projects.md#use-ml-assisted-data-labeling) nem támogatják a virtuális hálózat mögötti biztonságos alapértelmezett Storage-fiókokat. A ML által támogatott adatfeliratokhoz nem alapértelmezett Storage-fiókot kell használnia. Vegye figyelembe, hogy a nem alapértelmezett Storage-fiók biztonságossá tétele a virtuális hálózat mögött végezhető el. 

## <a name="configure-firewall-settings"></a>Tűzfalbeállítások konfigurálása

Konfigurálja a tűzfalat a Azure Machine Learning munkaterület erőforrásaihoz és a nyilvános internethez való hozzáférés szabályozásához. A Azure Firewall javasoljuk, hogy a hálózat védelme érdekében más tűzfalakat is használni tudjon. Ha kérdése van a tűzfalon keresztüli kommunikáció engedélyezésével kapcsolatban, tekintse meg az Ön által használt tűzfal dokumentációját.

További információ a tűzfal beállításairól: [munkaterület használata tűzfal mögött](how-to-access-azureml-behind-firewall.md).

## <a name="custom-dns"></a>Egyéni DNS

Ha egyéni DNS-megoldást kell használnia a virtuális hálózatához, hozzá kell adnia a munkaterülethez tartozó gazdagép-rekordokat.

További információ a szükséges tartománynévekről és IP-címekről: [munkaterület használata egyéni DNS-kiszolgálóval](how-to-custom-dns.md).

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy öt részből álló virtuális hálózati sorozat első része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [2. rész: a Virtual Network áttekintése](how-to-secure-workspace-vnet.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)
* [5. rész: a Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)
