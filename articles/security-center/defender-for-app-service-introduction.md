---
title: Azure Defender for App Service – az előnyök és funkciók
description: Ismerje meg az Azure Defender for App Service funkcióit, valamint azt, hogyan engedélyezheti az előfizetését
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ec4ac5d355266a46b33d89fd25c2665493773f5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100814"
---
# <a name="protect-your-web-apps-and-apis"></a>A webalkalmazások és API-k elleni védelem

## <a name="prerequisites"></a>Előfeltételek

A Security Center natív módon van integrálva a App Serviceval, így nincs szükség az üzembe helyezésre és a bevezetésre – az integráció átlátható.

Ahhoz, hogy a Azure App Service-csomagot a App Service Azure Defender szolgáltatással megvédje, a következőkre lesz szüksége:

- A dedikált gépekhez társított támogatott App Service-csomag. A támogatott csomagok felsorolása a [rendelkezésre állásban](#availability)található.

- Az Azure Defender engedélyezve van az előfizetésében a gyors útmutató [: az Azure Defender engedélyezése](enable-azure-defender.md)című témakörben leírtak szerint.

    > [!TIP]
    > Az Azure Defender (például az Azure Defender for App Service) egyéni csomagjait is engedélyezheti.

## <a name="availability"></a>Rendelkezésre állás

| Szempont                       | Részletek                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadás állapota:               | Általánosan elérhető (GA)                                                                                                                                                                      |
| Árképzési                     | Az [Azure Defender for app Service](azure-defender.md) számlázása [Security Center díjszabás](https://azure.microsoft.com/pricing/details/security-center/) szerint történik<br>A számlázás az összes csomag összes számítási példánya szerint történik       |
| Támogatott App Service csomagok: | Az [összes app Service-csomag](https://azure.microsoft.com/pricing/details/app-service/plans/) támogatott, kivéve [a használati terv Azure Functionsét](../azure-functions/functions-scale.md). |
| Felhők                      | ![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Milyen előnyökkel jár az Azure Defender a App Service?

A Azure App Service egy teljes körűen felügyelt platform a webalkalmazások és API-k létrehozásához és üzemeltetéséhez. Mivel a platform teljes körűen felügyelt, nem kell aggódnia az infrastruktúrával kapcsolatban. Felügyeleti, monitorozási és üzemeltetési megállapításokat biztosít a vállalati szintű teljesítmény-, biztonsági és megfelelőségi követelmények teljesítéséhez. További információ: [Azure app Service](https://azure.microsoft.com/services/app-service/).

Az **Azure Defender for app Service** a felhő méretezését használja a app Serviceon futó alkalmazások megcélzására irányuló támadások azonosítására. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Ha engedélyezi az Azure Defender App Serviceét, azonnal kihasználhatja a következő, az Azure Defender-csomag által kínált szolgáltatásokat:

- A **Secure** -Security Center kiértékeli a app Service terv által érintett erőforrásokat, és a megállapításai alapján biztonsági javaslatokat hoz létre. A javaslatok részletes utasításait felhasználva megerősítheti App Service erőforrásait.

- **Észlelés** – az Azure Defender számos veszélyforrást észlel a app Service-erőforrások figyelésével:
    - a virtuálisgép-példány, amelyben a App Service fut, és a felügyeleti felülete
    - a App Service-alkalmazásokba küldött és onnan érkező kérések és válaszok
    - az alapul szolgáló munkapéldányok és virtuális gépek
    - App Service belső naplók – az Azure felhőalapú szolgáltatóként való láthatóságának köszönhetően elérhető

A Felhőbeli natív megoldásként az Azure Defender több célpontra vonatkozó támadási módszereket is képes azonosítani. Egyetlen gazdagépről például nehéz lenne azonosítani az IP-címek kis részhalmazának elosztott támadását, több gazdagépen a hasonló végpontokra való bejárással.

A naplózási adatok és az infrastruktúra együttesen tájékoztatják a történetet: egy olyan új támadásról, amely a vadonban kering a vásárlói gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Milyen fenyegetéseket tud észlelni az Azure Defender App Service?

### <a name="threats-by-mitre-attck-tactics"></a>Fenyegetések: MITRE ATT&CK-taktika

Az Azure Defender számos fenyegetést figyel a App Service erőforrásaihoz. A riasztások szinte teljes listát mutatnak a MITRE ATT&a CK-taktikáról a parancs-és vezérlési előtámadástól. Az Azure Defender az alábbiakat ismeri fel:

- **Támadás előtti fenyegetések** – a Defender több típusú sebezhetőségi képolvasót is képes észlelni, amelyeket a támadók gyakran használnak az alkalmazások gyengeségeinek észlelésére.

- **Kezdeti hozzáférési fenyegetések**  -  A [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) ezeket a riasztásokat tartalmazza, például riasztást vált ki, ha egy ismert kártékony IP-cím csatlakozik a Azure app Service FTP-interfészhez.

- **Végrehajtási fenyegetések** – a Defender képes észlelni a magas jogosultsági szintű parancsok, a Linux-parancsok futtatását a Windows app Service, a fájl nélküli támadási viselkedést, a digitális pénzbányászati eszközöket, valamint számos más gyanús és kártékony programkód-végrehajtási tevékenységet.

### <a name="dangling-dns-detection"></a>Lelógó DNS-észlelés

Az Azure Defender for App Service a DNS-regisztrálón maradó DNS-bejegyzéseket is azonosítja, ha egy App Service webhely le van szerelve – ezeket a rendszer a DNS-bejegyzések leállítására szolgál. Ha eltávolít egy webhelyet, és nem távolítja el az egyéni tartományt a DNS-regisztrálóból, a DNS-bejegyzés egy nem létező erőforrásra mutat, és az altartománya sebezhető az átvételsel. Az Azure Defender nem ellenőrzi a DNS-regisztrálót a *meglévő* LELÓGÓ DNS-bejegyzésekhez; riasztást küld, ha egy App Service-webhelyet leszerelnek, és az egyéni tartományt (DNS-bejegyzést) nem törli.

Az altartományok beszerzése gyakori, nagy súlyosságú fenyegetést jelent a szervezetek számára. Ha egy veszélyforrás egy lelógó DNS-bejegyzést észlel, a célként megadott címen hozza létre a saját webhelyét. Ezután a szervezet tartományához tartozó forgalmat átirányítják a fenyegetést tartalmazó színész webhelyére, és ezt a forgalmat a kártékony tevékenységek széles körére használhatják.

A DNS-alapú védelem elérhető, függetlenül attól, hogy a tartományokat Azure DNS vagy külső tartományregisztráló felügyeli-e, és a Windows és a Linux rendszeren egyaránt App Service.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Egy példa egy Azure Defender-riasztásra egy észlelt lelógó DNS-bejegyzésről. Engedélyezze az Azure Defender számára a App Service számára, hogy megkapja ezt és az egyéb riasztásokat a környezet számára." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

További információ a DNS-ből és az altartományok átvételének fenyegetéséről: a [DNS-bejegyzések letételének megakadályozása és az altartomány átvételének elkerülése](../security/fundamentals/subdomain-takeover.md).

Az Azure App Service riasztások teljes listájáért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Előfordulhat, hogy a Defender nem aktiválja a DNS-riasztásokat, ha az egyéni tartomány nem közvetlenül egy App Service erőforrásra mutat, vagy ha a Defender nem figyelt a webhelyre irányuló forgalmat, mivel a lelógó DNS-védelem engedélyezve lett (mivel nem lesznek naplók az egyéni tartomány azonosításához).

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan App Service Azure Defendert. 

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [stream-riasztások a Siem, a SOAR vagy az IT Service Management megoldásban](export-to-siem.md)című témakör utasításait.
- Az App Service riasztásokhoz tartozó Azure Defender listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-azureappserv)tartalmazza.
- App Service csomagokkal kapcsolatos további információkért lásd: [app Service csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).
> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](enable-azure-defender.md)