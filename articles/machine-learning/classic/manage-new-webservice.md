---
title: 'ML Studio (klasszikus): webszolgáltatások kezelése – Azure'
description: Machine Learning új és klasszikus webszolgáltatásokat a Microsoft Azure Machine Learning webszolgáltatások portál használatával kezelheti. Mivel a klasszikus webszolgáltatások és az új webszolgáltatások különböző alapul szolgáló technológiákon alapulnak, némileg eltérő felügyeleti képességei vannak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: d3aea23c816b9d8b32b0563d3116cdc2b92e080f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094299"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Webszolgáltatás kezelése a Azure Machine Learning Studio (klasszikus) webszolgáltatások portál használatával

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A Microsoft Azure Machine Learning webszolgáltatások portál használatával kezelheti Machine Learning új és klasszikus webszolgáltatásait. Mivel a klasszikus webszolgáltatások és az új webszolgáltatások különböző alapul szolgáló technológiákon alapulnak, némileg eltérő felügyeleti képességei vannak.

A Machine Learning Web Services portálon a következőket teheti:

* A webszolgáltatás használatának figyelése.
* Konfigurálja a leírást, frissítse a webszolgáltatás kulcsait (csak új), frissítse a Storage-fiók kulcsát (csak új), engedélyezze a naplózást, és engedélyezze vagy tiltsa le a mintaadatok közül.
* Törölje a webszolgáltatást.
* Számlázási csomagok létrehozása, törlése vagy frissítése (csak új).
* Végpontok hozzáadása és törlése (csak klasszikus)

>[!NOTE]
>A **webszolgáltatások** lapon a klasszikus webszolgáltatásokat [Machine learning Studio (klasszikus)](https://studio.azureml.net) is kezelheti.

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Engedélyek az új erőforrás-kezelő-alapú webszolgáltatások kezeléséhez

Az új webszolgáltatások üzembe helyezése Azure-erőforrásként történik. Ennek megfelelően az új webszolgáltatások üzembe helyezéséhez és kezeléséhez megfelelő engedélyekkel kell rendelkeznie.  Új webszolgáltatások üzembe helyezéséhez vagy kezeléséhez hozzá kell rendelni egy közreműködői vagy rendszergazdai szerepkört azon az előfizetésen, amelyhez a webszolgáltatás telepítve van. Ha egy másik felhasználót is meghív egy Machine learning-munkaterületre, akkor a webszolgáltatások üzembe helyezése vagy kezelése előtt hozzá kell rendelnie őket egy közreműködői vagy rendszergazdai szerepkörhöz az előfizetésben. 

Ha a felhasználó nem rendelkezik a megfelelő engedélyekkel az erőforrások eléréséhez a Azure Machine Learning webszolgáltatások portálon, akkor a következő hibaüzenetet kapja a webszolgáltatás üzembe helyezésekor:

*A webszolgáltatás telepítése nem sikerült. Ez a fiók nem rendelkezik megfelelő hozzáféréssel a munkaterületet tartalmazó Azure-előfizetéshez. Ha webszolgáltatást szeretne üzembe helyezni az Azure-ban, ugyanazt a fiókot kell meghívnia a munkaterületre, és hozzáférést kell adni a munkaterületet tartalmazó Azure-előfizetéshez.*

A munkaterület létrehozásával kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) munkaterület létrehozása és megosztása](create-workspace.md).

A hozzáférési engedélyek beállításával kapcsolatos további információkért lásd: [Azure-szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Új webszolgáltatások kezelése
Az új webszolgáltatások kezeléséhez:

1. Jelentkezzen be a [Microsoft Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálra a Microsoft Azure-fiók használatával – használja az Azure-előfizetéshez társított fiókot.
2. A menüben kattintson a **Web Services** elemre.

Megjelenik az előfizetéshez tartozó telepített webszolgáltatások listája. 

Webszolgáltatás kezeléséhez kattintson a webszolgáltatások lehetőségre. A webszolgáltatások lapon a következőket teheti:

* Kattintson a webszolgáltatásra a kezeléséhez.
* Kattintson a webszolgáltatáshoz tartozó számlázási tervre a frissítéshez.
* Webszolgáltatás törlése.
* Webszolgáltatások másolása és üzembe helyezése egy másik régióban.

Amikor egy webszolgáltatásra kattint, megnyílik a webszolgáltatás gyors üzembe helyezési lapja. A webszolgáltatás rövid útmutatója két olyan menüpontot tartalmaz, amelyek lehetővé teszik a webszolgáltatások kezelését:

* **Irányítópult** – lehetővé teszi a webszolgáltatás használatának megtekintését.
* **Konfigurálás** – lehetővé teszi a leíró szöveg hozzáadását, a webszolgáltatáshoz társított Storage-fiók kulcsának frissítését, valamint a mintaadatok engedélyezését és letiltását.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson az **irányítópult** fülre.

Az irányítópulton megtekintheti a webszolgáltatás általános használatát egy adott időszakban. A használati diagramok jobb felső sarkában található időszak legördülő menüből kiválaszthatja a megtekinteni kívánt időszakot. Az irányítópult az alábbi információkat jeleníti meg:

* Az **idő múlásával** a kérések száma a kiválasztott időszakra vonatkozó kérelmek számának gráfját jeleníti meg. Segít azonosítani, hogy észlelt-e tüskéket a használat során.
* A **kérelem-válasz kérelmek** megjelenítik a szolgáltatás által a kiválasztott időszakban fogadott Request-Response hívások teljes számát, valamint azt, hogy hány sikertelen volt.
* Az **átlagos Request-Response számítási idő** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* A **Batch-kérelmek** azon kötegelt kérelmek teljes számát jelenítik meg, amelyeket a szolgáltatás a kiválasztott időszakban fogadott el, és hány sikertelen volt.
* A **feladatok átlagos késése** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* A **hibák** a webszolgáltatásra irányuló hívásokban történt hibák összesített számát jelenítik meg.
* A **szolgáltatások költségei** a szolgáltatáshoz társított számlázási csomag díjait jelenítik meg.

### <a name="configuring-the-web-service"></a>Webszolgáltatás konfigurálása
Kattintson a **configure (Konfigurálás** ) menüpontra.

A következő tulajdonságokat frissítheti:

* A **Leírás** lehetővé teszi a webszolgáltatás leírásának megadását.
* A **cím** lehetővé teszi a webszolgáltatás címének megadását
* A **kulcsok** lehetővé teszik az elsődleges és a másodlagos API-kulcsok elforgatását.
* A **Storage-fiók kulcsa** lehetővé teszi a webszolgáltatás változásaihoz társított Storage-fiók kulcsának frissítését. 
* A **mintaadatok engedélyezése** lehetővé teszi a Request-Response szolgáltatás tesztelésére használható mintaadatok megadását. Ha a webszolgáltatást Machine Learning Studioban (klasszikus) hozta létre, a mintaadatok a modell betanításához használt adatokból származnak. Ha programozott módon hozta létre a szolgáltatást, az adatok a JSON-csomag részeként megadott adatokból származnak.

### <a name="managing-billing-plans"></a>Számlázási csomagok kezelése
Kattintson a **csomagok** menüpontra a webszolgáltatások gyors lapja lapon. Az adott webszolgáltatáshoz társított tervet a terv kezelésére is használhatja.

* Az **új** csomag lehetővé teszi, hogy új csomagot hozzon létre.
* A **csomag hozzáadása/eltávolítása** lehetővé teszi egy meglévő csomag kiskálázását a kapacitás hozzáadásához.
* A **verziófrissítés/lefokozás** lehetővé teszi, hogy egy meglévő csomagot "vertikális felskálázással" bővítse a kapacitást.
* A **delete** lehetővé teszi egy csomag törlését.

Kattintson egy tervre az irányítópult megtekintéséhez. Az irányítópult egy adott időszakon belül pillanatfelvételt készít vagy megtervezi a használatot. A megtekinteni kívánt időtartam kiválasztásához kattintson az irányítópult jobb felső sarkában **található legördülő** menüre. 

A terv irányítópultja a következő információkat tartalmazza:

* A **terv leírása** a csomaggal kapcsolatos költségekkel és kapacitással kapcsolatos információkat jeleníti meg.
* A **használati terv** megjeleníti a terv alapján felszámított tranzakciók és számítási órák számát.
* A **webszolgáltatások** a csomagot használó webszolgáltatások számát jelenítik meg.
* A **leggyakoribb webszolgáltatás-hívások** megjelenítik az első négy olyan webszolgáltatást, amelyek a terv alapján felszámított hívásokat tesznek elérhetővé.
* A **Top Web Services by számítási óra** megjeleníti az első négy olyan webszolgáltatást, amely a csomaggal felszámított számítási erőforrásokat használja.

## <a name="manage-classic-web-services"></a>Klasszikus webszolgáltatások kezelése
> [!NOTE]
> Az ebben a szakaszban ismertetett eljárások a klasszikus webszolgáltatások Azure Machine Learning webszolgáltatások portálon történő kezeléséhez szükségesek. A klasszikus webszolgáltatások a Machine Learning Studio (klasszikus) és a Azure Portal használatával történő kezelésével kapcsolatos információkért lásd: [Azure Machine learning Studio (klasszikus) munkaterületek kezelése](manage-workspace.md).
> 
> 

A klasszikus webszolgáltatások kezeléséhez:

1. Jelentkezzen be a [Microsoft Azure Machine learning webszolgáltatások](https://services.azureml.net/quickstart) portálra a Microsoft Azure-fiók használatával – használja az Azure-előfizetéshez társított fiókot.
2. A menüben kattintson a **klasszikus webes szolgáltatások** elemre.

A klasszikus webszolgáltatás kezeléséhez kattintson a **klasszikus webszolgáltatások** lehetőségre. A klasszikus webszolgáltatások oldalon a következőket teheti:

* Kattintson a webszolgáltatásra a társított végpontok megtekintéséhez.
* Webszolgáltatás törlése.

Klasszikus webszolgáltatások kezelésekor a végpontok külön kezelhetők. Ha a webszolgáltatások oldalon rákattint egy webszolgáltatásra, megnyílik a szolgáltatáshoz társított végpontok listája. 

A klasszikus webszolgáltatás-végpont lapon hozzáadhat és törölhet végpontokat a szolgáltatásban. A végpontok hozzáadásával kapcsolatos további információkért lásd: [végpontok létrehozása](create-endpoint.md).

Kattintson az egyik végpontra a webszolgáltatás rövid útmutató lapjának megnyitásához. A rövid útmutató lapon két lehetőség közül választhat, amelyek lehetővé teszik a webszolgáltatás kezelését:

* **Irányítópult** – lehetővé teszi a webszolgáltatás használatának megtekintését.
* **Konfigurálás** – lehetővé teszi a leíró szöveg hozzáadását, a hibák naplózásának bekapcsolását és kikapcsolását, a webszolgáltatáshoz társított Storage-fiók kulcsának frissítését, valamint a mintaadatok engedélyezését és letiltását.

### <a name="monitoring-how-the-web-service-is-being-used"></a>A webszolgáltatás használatának figyelése
Kattintson az **irányítópult** fülre.

Az irányítópulton megtekintheti a webszolgáltatás általános használatát egy adott időszakban. A használati diagramok jobb felső sarkában található időszak legördülő menüből kiválaszthatja a megtekinteni kívánt időszakot. Az irányítópult az alábbi információkat jeleníti meg:

* Az **idő múlásával** a kérések száma a kiválasztott időszakra vonatkozó kérelmek számának gráfját jeleníti meg. Segít azonosítani, hogy észlelt-e tüskéket a használat során.
* A **kérelem-válasz kérelmek** megjelenítik a szolgáltatás által a kiválasztott időszakban fogadott Request-Response hívások teljes számát, valamint azt, hogy hány sikertelen volt.
* Az **átlagos Request-Response számítási idő** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* A **Batch-kérelmek** azon kötegelt kérelmek teljes számát jelenítik meg, amelyeket a szolgáltatás a kiválasztott időszakban fogadott el, és hány sikertelen volt.
* A **feladatok átlagos késése** a fogadott kérelmek végrehajtásához szükséges idő átlagát jeleníti meg.
* A **hibák** a webszolgáltatásra irányuló hívásokban történt hibák összesített számát jelenítik meg.
* A **szolgáltatások költségei** a szolgáltatáshoz társított számlázási csomag díjait jelenítik meg.

### <a name="configuring-the-web-service"></a>Webszolgáltatás konfigurálása
Kattintson a **configure (Konfigurálás** ) menüpontra.

A következő tulajdonságokat frissítheti:

* A **Leírás** lehetővé teszi a webszolgáltatás leírásának megadását. A leírás mező kitöltése kötelező.
* A **naplózással** engedélyezheti vagy letilthatja a hibák naplózását a végponton. További információ a naplózásról: [Machine learning webszolgáltatások naplózásának](web-services-logging.md)engedélyezése.
* A **mintaadatok engedélyezése** lehetővé teszi a Request-Response szolgáltatás tesztelésére használható mintaadatok megadását. Ha a webszolgáltatást Machine Learning Studioban (klasszikus) hozta létre, a mintaadatok a modell betanításához használt adatokból származnak. Ha programozott módon hozta létre a szolgáltatást, az adatok a JSON-csomag részeként megadott adatokból származnak.