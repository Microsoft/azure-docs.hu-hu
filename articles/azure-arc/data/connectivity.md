---
title: Csatlakozási módok és követelmények
description: Az Azure arc-kompatibilis adatszolgáltatások kapcsolódási lehetőségeinek ismertetése a környezetből az Azure-ba
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493972"
---
# <a name="connectivity-modes-and-requirements"></a>Csatlakozási módok és követelmények

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Csatlakozási módok

Az Azure arc-kompatibilis adatszolgáltatási környezetből az Azure-ba való kapcsolódás foka több lehetőség is rendelkezésre áll. Mivel a követelmények az üzleti házirend, a kormányzati szabályozás vagy az Azure-hoz való hálózati kapcsolat elérhetősége alapján változnak, az alábbi csatlakozási módok közül választhat.

Az Azure arc-kompatibilis adatszolgáltatások lehetőséget biztosít az Azure-hoz való csatlakozásra két különböző *csatlakozási módban*: 

- Közvetlenül csatlakoztatva 
- Közvetve csatlakoztatva

A kapcsolati mód lehetővé teszi a rugalmasságot, hogy kiválassza az Azure-ba irányuló adatmennyiséget, valamint azt, hogy a felhasználók hogyan használják az ív-adatkezelőt. A választott kapcsolati módból függően előfordulhat, hogy az Azure arc-kompatibilis adatszolgáltatások bizonyos funkciói vagy nem érhetők el.

Fontos, hogy ha az Azure arc-kompatibilis adatszolgáltatások közvetlenül csatlakoznak az Azure-hoz, akkor a felhasználók [Azure Resource Manager API-kat](/rest/api/resources/), az Azure CLI-t és a Azure Portal az Azure arc-adatszolgáltatások üzemeltetésére használhatják. A közvetlenül csatlakoztatott mód funkciói ugyanúgy hasonlítanak össze, ahogy bármely más Azure-szolgáltatást a kiépítés/kiépítés, a skálázás, a konfigurálás és így tovább a Azure Portal.  Ha az Azure arc-kompatibilis adatszolgáltatások közvetetten kapcsolódnak az Azure-hoz, akkor a Azure Portal csak olvasható nézet. Megtekintheti a telepített SQL felügyelt példányok és postgres nagy kapacitású-példányok leltárát, valamint a rájuk vonatkozó adatokat, de a Azure Portalon nem hajthat végre műveleteket.  Az indirekt módon csatlakoztatott módban az összes műveletet helyileg kell elvégezni a Azure Data Studio, a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] vagy Kubernetes natív eszközök, például a kubectl használatával.

Emellett a Azure Active Directory és az Azure Role-Based Access Control csak a közvetlenül csatlakoztatott módban használható, mert az Azure-hoz való folyamatos és közvetlen kapcsolat függőséggel rendelkezik.

Néhány Azure-hoz kapcsolódó szolgáltatás csak akkor érhető el, ha közvetlenül elérhetők, például az Azure Defender biztonsági szolgáltatásai, a tároló-felismerések és a blob Storage-Azure Backup.

||**Közvetve csatlakoztatva**|**Közvetlenül csatlakoztatva**|**Soha nem kapcsolódott**|
|---|---|---|---|
|**Leírás**|A központilag csatlakoztatott mód a legtöbb felügyeleti szolgáltatást helyileg kínálja a környezetben, és nincs közvetlen kapcsolat az Azure-hoz.  Az Azure-ba _csak_ leltározási és számlázási célokat kell elküldeni. Egy fájlba exportálja, és havonta legalább egyszer feltölti az Azure-ba.  Nincs szükség közvetlen vagy folyamatos kapcsolódásra az Azure-hoz.  Az Azure-hoz való csatlakozást igénylő szolgáltatások és szolgáltatások nem lesznek elérhetők.|A közvetlenül csatlakoztatott mód biztosítja az összes elérhető szolgáltatást, ha közvetlen kapcsolat létesíthető az Azure-ban. A kapcsolatokat a rendszer mindig az Azure- _ba kezdeményezi_ , és szabványos portokat és protokollokat használ, mint például a https/443.|Semmilyen módon nem küldhetők adatok az Azure-ba vagy az Azure-ból.|
|**Aktuális rendelkezésre állás**| Előzetes verzióban érhető el.|Előzetes verzióban érhető el.|Jelenleg nem támogatott.|
|**Jellemző használati esetek**|Olyan helyszíni adatközpontok, amelyek nem engedélyezik az adatközpont adatterületének az üzleti vagy jogszabályi megfelelőségi szabályzatok, illetve a külső támadások vagy az adatkiszűrése miatti kapcsolódását.  Tipikus példák: pénzügyi intézmények, egészségügyi ellátás, kormányzat. <br/><br/>Az Edge-helyek olyan helyei, ahol az Edge-hely általában nem kapcsolódik az internethez.  Tipikus példák: olaj/gáz vagy katonai mezők alkalmazásai.  <br/><br/>Az Edge-helyek olyan helyei, amelyek hosszú leállású időszakos kapcsolattal rendelkeznek.  Tipikus példák: stadionok, tengerjáró hajók. | Nyilvános felhőket használó szervezetek.  Tipikus példák: Azure, AWS vagy Google Cloud.<br/><br/>Az oldal azon helyei, ahol az internetkapcsolat általában jelen van és engedélyezett.  Tipikus példák: kiskereskedelmi üzletek, gyártás.<br/><br/>A vállalati adatközpontok az adatközpont és az Internet közötti adatterületük közötti kapcsolathoz biztosítanak több engedékeny házirendet.  Tipikus példák: nem szabályozott vállalkozások, kis-és közepes méretű vállalkozások|Valóban "gapped" környezetekben, amelyekben semmilyen körülmények között semmilyen adat nem érhető el az adatkörnyezetből. Tipikus példák: a szigorúan titkos kormányzati létesítmények.|
|**Az adatküldés az Azure-ba**|Három lehetőség áll rendelkezésre a számlázási és leltározási információk Azure-ba történő elküldésekor:<br><br> 1.) az adatterületről egy automatizált folyamat exportálja az adatterületet, amely kapcsolattal rendelkezik a biztonságos adatterülettel és az Azure-nal.<br><br>2) a rendszer az adatterületről exportálja az adatokból az adatterületet, automatikusan átmásolja egy kevésbé biztonságos régióba, és a kevésbé biztonságos régióba tartozó automatizált folyamat feltölti az Azure-ba.<br><br>3) a biztonságos régióban lévő felhasználó manuálisan exportálja az adatmennyiséget, manuálisan kihozza a biztonságos régiót, és manuálisan feltölti az Azure-ba. <br><br>Az első két lehetőség egy automatizált folyamatos folyamat, amely rendszeres futtatásra ütemezhető, így az adatoknak az Azure-ba való átvitelének minimális késleltetése csak az Azure-hoz elérhető kapcsolat.|Az adatküldés automatikusan és folyamatosan történik az Azure-ban.|Az Azure-ba soha nem kerül be az adatküldés.|

## <a name="feature-availability-by-connectivity-mode"></a>Szolgáltatás rendelkezésre állása csatlakozási mód alapján

|**Szolgáltatás**|**Közvetve csatlakoztatva**|**Közvetlenül csatlakoztatva**|
|---|---|---|
|**Automatikus magas rendelkezésre állás**|Támogatott|Támogatott|
|**Önkiszolgáló kiépítés**|Támogatott<br/>A létrehozás történhet Azure Data Studioon, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] vagy Kubernetes natív eszközökkel (Helm, kubectl, oC stb.), vagy az Azure arc engedélyezve Kubernetes GitOps kiépítés használatával.|Támogatott<br/>A közvetett módon csatlakoztatott mód létrehozásának lehetőségein kívül a Azure Portalon, Azure Resource Manager API-kon, az Azure CLI-n vagy az ARM-sablonokon is létrehozhat. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**
|**Rugalmas méretezhetőség**|Támogatott|Támogatott<br/>**Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Számlázás**|Támogatott<br/>A számlázási információk rendszeres időközönként exportálhatók, és az Azure-ba kerülnek.|Támogatott<br/>A számlázási információk automatikusan és folyamatosan továbbítódnak az Azure-ba, és közel valós időben tükröződnek. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Leltárkezelés**|Támogatott<br/>A leltári adatkészleteket a rendszer rendszeresen exportálja és elküldi az Azure-nak.<br/><br/>Olyan ügyféleszközök használata, mint például az Azure Data Studio, az Azure-adatcli, illetve `kubectl` a leltár helyi megtekintése és kezelése.|Támogatott<br/>A leltári adatgyűjtés automatikusan és folyamatosan történik az Azure-ba, és közel valós időben jelenik meg. Így közvetlenül a Azure Portal felügyelheti a leltárt. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Automatikus frissítések és javítások**|Támogatott<br/>Az adatkezelőnek vagy közvetlen hozzáféréssel kell rendelkeznie a Microsoft Container Registryhoz (MCR), vagy a tároló lemezképeit le kell húzni a MCR-ből, és egy helyi, privát tároló-beállításjegyzékbe kell küldenie, amelyhez az adatkezelő hozzáfér.|Támogatott<br/>**Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Automatikus biztonsági mentés és visszaállítás**|Támogatott<br/>Automatikus helyi biztonsági mentés és visszaállítás.|Támogatott<br/>A helyi biztonsági mentés és a visszaállítás mellett lehetősége van arra is, hogy a biztonsági mentéseket a hosszú távú, a helyszínen kívüli megőrzéshez is _elküldje_ Azure Backup. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Figyelés**|Támogatott<br/>Helyi monitorozás a Grafana és a Kibana irányítópultok használatával.|Támogatott<br/>A helyi monitorozási irányítópultokon kívül lehetősége van a figyelési adatok és _naplók küldésére_ is, hogy egy helyen egyszerre több hely figyelésére Azure monitor. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Hitelesítés**|Használjon helyi felhasználónevet/jelszót az adatvezérlő és az irányítópult hitelesítéséhez. SQL-és postgres-bejelentkezések vagy Active Directory használata az adatbázis-példányokhoz való kapcsolódáshoz.  K8s-hitelesítési szolgáltatók használata a Kubernetes API-hoz történő hitelesítéshez.|A közvetetten csatlakoztatott mód hitelesítési módszerein kívül vagy _ahelyett is használhatja_ a Azure Active Directory. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Szerepköralapú hozzáférés-vezérlés (RBAC)**|Kubernetes RBAC használata a Kubernetes API-ban. SQL-és postgres-RBAC használata adatbázis-példányokhoz.|Opcionálisan integrálható Azure Active Directory és az Azure RBAC is. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Azure Defender**|Nem támogatott|Jövőre tervezett|

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények

**Egyes funkciókhoz kapcsolódni kell az Azure-hoz.**

**Az Azure-val folytatott kommunikáció minden esetben a környezetből indul el.** Ez akkor is igaz, ha a Azure Portal felhasználója kezdeményezi a műveletet.  Ebben az esetben hatékony feladat van, amely az Azure-ban van várólistán.  A környezet egyik ügynöke kezdeményezi a kommunikációt az Azure-ban, hogy megtekintse, milyen feladatok vannak a várólistában, futtatja a feladatokat, és jelentést készít az állapotról/befejezésről/nem az Azure-hoz.

|**Adattípus**|**Irány**|**Kötelező vagy nem kötelező**|**További költségek**|**A mód megadása kötelező**|**Megjegyzések**|
|---|---|---|---|---|---|
|**Tárolólemezképek**|Microsoft Container Registry – > ügyfél|Kötelező|Nem|Közvetett vagy közvetlen|A tároló lemezképei a szoftver terjesztésének módszerei.  Olyan környezetben, amely az interneten keresztül tud csatlakozni a Microsoft Container Registryhoz (MCR), a tároló lemezképei közvetlenül a MCR tölthetők le.  Abban az esetben, ha a telepítési környezet nem rendelkezik közvetlen kapcsolattal, lekérheti a lemezképeket a MCR, és leküldheti azokat egy privát tároló-beállításjegyzékbe a telepítési környezetben.  A létrehozáskor beállíthatja, hogy a létrehozási folyamat a MCR helyett a privát tároló beállításjegyzékből legyen lehívható. Ez az automatikus frissítésekre is vonatkozik.|
|**Erőforrás-leltár**|Felhasználói környezet – > Azure|Kötelező|Nem|Közvetett vagy közvetlen|Az adatkezelők, az adatbázis-példányok (PostgreSQL és SQL) leltározása az Azure-ban számlázás céljából történik, valamint az adatkezelők és az adatbázis-példányok leltározásának egy helyen történő létrehozása, amely különösen akkor hasznos, ha egynél több környezettel rendelkezik az Azure arc-adatszolgáltatásokkal.  A példányok kiépítésének, megszüntetésének, méretezésének/beépítésének, a készletnek a felskálázása/leállítása érdekében az Azure-ban frissülnek.|
|**Számlázási telemetria-információk**|Felhasználói környezet – > Azure|Kötelező|Nem|Közvetett vagy közvetlen|Az adatbázis-példányok kihasználtságát számlázási célokra kell elküldeni az Azure-nak.  Az Azure arc-kompatibilis adatszolgáltatások díját az előzetes verzió ideje alatt nem lehet fizetni.|
|**Az adatkezelés és a naplók figyelése**|Felhasználói környezet – > Azure|Választható|Az adatmennyiségtől függően (lásd: [Azure monitor díjszabás](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Közvetett vagy közvetlen|Előfordulhat, hogy el szeretné küldeni a helyileg összegyűjtött figyelési adatokat és naplókat, hogy Azure Monitor az adatok több környezetből való összesítését egy helyre, valamint Azure Monitor szolgáltatások, például a riasztások használatát, a Azure Machine Learningban található adatok használatával stb.|
|**Azure szerepköralapú Access Control (Azure RBAC)**|Felhasználói környezet – > Azure-> ügyfél-környezet|Választható|Nem|Csak közvetlen|Ha az Azure RBAC-t szeretné használni, akkor a kapcsolódást az Azure-ban mindig meg kell teremteni.  Ha nem szeretné használni az Azure RBAC-t, akkor a helyi Kubernetes RBAC is használható.  **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Azure Active Directory (AD)**|Felhasználói környezet – > Azure-> ügyfél-környezet|Választható|Lehet, hogy már fizet az Azure AD-nek|Csak közvetlen|Ha az Azure AD-t a hitelesítéshez szeretné használni, akkor a kapcsolatot mindig az Azure-ban kell létrehozni. Ha nem szeretné a hitelesítéshez használni az Azure AD-t, Active Directory összevonási szolgáltatások (AD FS) (ADFS) Active Directoryon keresztül. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Biztonsági mentés és visszaállítás**|Felhasználói környezet – > ügyfél-környezet|Kötelező|Nem|Közvetlen vagy közvetett|A biztonsági mentési és visszaállítási szolgáltatás úgy konfigurálható, hogy a helyi tárolási osztályokra mutasson. |
|**Azure Backup – hosszú távú adatmegőrzés**| Felhasználói környezet – > Azure | Választható| Igen az Azure Storage-hoz | Csak közvetlen |Előfordulhat, hogy olyan biztonsági másolatokat szeretne küldeni, amelyeket helyileg készítettek Azure Backup a biztonsági másolatok hosszú távú megőrzéséhez, és visszaállítják őket a helyi környezetbe a visszaállításhoz. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Azure Defender biztonsági szolgáltatások**|Felhasználói környezet – > Azure-> ügyfél-környezet|Választható|Igen|Csak közvetlen|**Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|
|**Kiépítés és konfigurációs változások Azure Portal**|Felhasználói környezet – > Azure-> ügyfél-környezet|Választható|Nem|Csak közvetlen|A kiépítés és a konfiguráció módosítása helyileg, Azure Data Studio vagy a használatával végezhető el [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  Közvetlenül csatlakoztatott módban a Azure Portal is kiépítheti és módosíthatja a konfigurációs módosításokat. **Közvetlenül csatlakoztatott üzemmód függőben lévő rendelkezésre állása**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Információk az internetes címekről, a portokról, a titkosításról és a proxykiszolgáló támogatásáról

Jelenleg az előzetes verzió fázisában csak a közvetetten csatlakoztatott mód támogatott. Ebben a módban csak három kapcsolat szükséges az interneten elérhető szolgáltatásokhoz. Ilyen a kapcsolatok például a következők:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure Resource Manager API-k](#azure-resource-manager-apis)
- [Azure monitor API-k](#azure-monitor-apis)

Az Azure-hoz és a Microsoft Container Registryhoz tartozó összes HTTPS-kapcsolat titkosítása SSL/TLS használatával történik, amely hivatalosan aláírt és ellenőrizhető tanúsítványokat használ.

A következő szakasz a kapcsolatok részleteit tartalmazza. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

A Microsoft Container Registry üzemelteti az Azure arc-kompatibilis adatszolgáltatások tárolójának lemezképeit.  Ezeket a lemezképeket lehívhatja a MCR, és leküldheti azokat egy privát tároló-beállításjegyzékbe, és konfigurálhatja az adatkezelő telepítési folyamatát, hogy lekérje a tároló lemezképeit a saját tároló beállításjegyzékből.

#### <a name="connection-source"></a>Kapcsolat forrása

Az egyes Kubernetes-csomópontokon lévő Kubernetes-kubelet meghúzza a tároló lemezképeit.

#### <a name="connection-target"></a>Kapcsolat célja

`mcr.microsoft.com`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Használhatja a proxyt

Igen

#### <a name="authentication"></a>Hitelesítés

Nincs

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager API-k
A Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] és az Azure CLI a Azure Resource Manager API-khoz csatlakozik az Azure-ba irányuló és onnan érkező adatok bizonyos funkciókhoz való küldéséhez és lekéréséhez.

#### <a name="connection-source"></a>Kapcsolat forrása

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] vagy Azure CLI-t futtató számítógép, amely az Azure-hoz csatlakozik.

#### <a name="connection-target"></a>Kapcsolat célja

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Használhatja a proxyt

Igen

#### <a name="authentication"></a>Hitelesítés 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure monitor API-k

A Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] és az Azure CLI a Azure Resource Manager API-khoz csatlakozik az Azure-ba irányuló és onnan érkező adatok bizonyos funkciókhoz való küldéséhez és lekéréséhez.

#### <a name="connection-source"></a>Kapcsolat forrása

Egy vagy az Azure CLI-t futtató számítógép, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] amely a figyelési metrikák vagy naplók feltöltését Azure monitorba.

#### <a name="connection-target"></a>Kapcsolat célja

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Használhatja a proxyt

Igen

#### <a name="authentication"></a>Hitelesítés 

Azure Active Directory

> [!NOTE]
> Egyelőre a Grafana-és Kibana-irányítópultok és az adatkezelő API közötti összes böngésző HTTPS/443 kapcsolata [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] önaláírt tanúsítványokkal van titkosítva.  A jövőben a szolgáltatás elérhető lesz, amely lehetővé teszi saját tanúsítványok megadását az SSL-kapcsolatok titkosításához.

A Azure Data Studio és [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] a KUBERNETES API-kiszolgáló közötti kapcsolat a létrehozott Kubernetes-hitelesítést és-titkosítást használja.  Minden Azure Data Studiot és a-t használó felhasználónak [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] hitelesített kapcsolattal kell rendelkeznie a KUBERNETES API-val, hogy az Azure arc-kompatibilis adatszolgáltatásokkal kapcsolatos számos műveletet végrehajtsa.

