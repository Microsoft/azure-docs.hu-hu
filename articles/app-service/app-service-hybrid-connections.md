---
title: Hibrid kapcsolatok
description: Megtudhatja, hogyan hozhat létre és használhat hibrid kapcsolatokat Azure App Service a különböző hálózatokban lévő erőforrások eléréséhez.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594231"
---
# <a name="azure-app-service-hybrid-connections"></a>Az Azure App Service hibrid kapcsolatai

Hibrid kapcsolatok a szolgáltatás az Azure-ban és egy Azure App Service szolgáltatásban is. Szolgáltatásként a App Service-ban használt alkalmazások és képességek túlmutatnak. Ha többet szeretne megtudni a Hibrid kapcsolatokről és azok használatáról App Servicen kívül, tekintse meg a [Azure Relay hibrid kapcsolatok][HCService]című témakört.

Hibrid kapcsolatok App Serviceon belül bármely olyan hálózatban elérheti az alkalmazás-erőforrásokat, amely kimenő hívásokat hajthat végre az Azure-ban az 443-as porton keresztül. Hibrid kapcsolatok hozzáférést biztosít az alkalmazástól egy TCP-végponthoz, és nem engedélyezi az alkalmazás elérésének új módját. A App Serviceban használt módon minden hibrid csatlakozás egyetlen TCP-gazdagéphez és porthoz kapcsolódik. Ez lehetővé teszi az alkalmazások számára, hogy bármely operációs rendszeren hozzáférjenek az erőforrásokhoz, ha ez egy TCP-végpont. A Hibrid kapcsolatok funkció nem tudja, vagy nem érdekli az alkalmazás protokollja, vagy hogy mi a hozzáférése. Egyszerűen hálózati hozzáférést biztosít.  

## <a name="how-it-works"></a>Működés ##
Hibrid kapcsolatok megköveteli a továbbító ügynök üzembe helyezését, ahol elérheti a kívánt végpontot és az Azure-t is. A Relay-ügynök, hibridkapcsolat-kezelő (HCM) a 443-as porton keresztül hívja meg a Azure Relay. A webalkalmazás-helyről az App Service-infrastruktúra az alkalmazás nevében is kapcsolódik Azure Relayhoz. Az összekapcsolt kapcsolatokon keresztül az alkalmazás elérheti a kívánt végpontot. A kapcsolat TLS 1,2 biztonsági és közös hozzáférési aláírási (SAS-) kulcsokat használ a hitelesítéshez és az engedélyezéshez.    

![A hibrid kapcsolatok magas szintű folyamatának ábrája][1]

Ha az alkalmazás olyan DNS-kérelmet tesz elérhetővé, amely megfelel egy konfigurált hibrid kapcsolódási végpontnak, a kimenő TCP-forgalom a hibrid kapcsolatban lesz átirányítva.  

> [!NOTE]
> Ez azt jelenti, hogy a hibrid kapcsolatok mindig DNS-nevet kell használniuk. Bizonyos ügyfélszoftverek nem végeznek DNS-keresést, ha a végpont IP-címet használ. 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service hibrid kapcsolatok előnyei ###

A Hibrid kapcsolatok képesség számos előnnyel jár, többek között:

- Az alkalmazások biztonságosan érhetik el a helyszíni rendszereket és szolgáltatásokat.
- A szolgáltatáshoz nem szükséges internetről elérhető végpont.
- Gyorsan és egyszerűen beállítható. Nincs szükség átjáróra
- Minden hibrid csatlakozás egyetlen gazdagéphez illeszkedik: a port kombinációja, amely hasznos a biztonság érdekében.
- Általában nincs szükség tűzfal-lyukakra. A kapcsolatok mindegyike kimenő a szabványos webes portoknál.
- Mivel a szolgáltatás hálózati szintű, az alkalmazás által használt nyelv és a végpont által használt technológia egyaránt független.
- A szolgáltatás használatával több hálózatban is biztosítható a hozzáférés egyetlen alkalmazásból. 
- Ez a Windows-alkalmazások és a Linux-alkalmazások esetében is támogatott. A Windows Container apps esetében nem támogatott.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>A Hibrid kapcsolatokkal nem rendelkező dolgok ###

Hibrid kapcsolatok többek között az alábbiakat teheti meg:

- Meghajtó csatlakoztatása.
- UDP használata.
- A dinamikus portokat használó TCP-alapú szolgáltatások, például az FTP-passzív mód vagy a kiterjesztett passzív üzemmód elérése.
- Támogassa az LDAP-t, mert az UDP protokollt igényelhet.
- A Active Directory támogatása, mert nem lehet tartományhoz csatlakozni App Service-feldolgozóhoz. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hibrid kapcsolatok hozzáadása és létrehozása az alkalmazásban ##

Hibrid kapcsolatok létrehozásához nyissa meg a [Azure Portal][portal] , és válassza ki az alkalmazást. Válassza   >  **a hálózat beállítása a hibrid kapcsolati végpontok** lehetőséget. Itt láthatja az alkalmazáshoz konfigurált Hibrid kapcsolatok.  

![Képernyőkép a hibrid kapcsolatok listájáról][2]

Új hibrid kapcsolatok hozzáadásához válassza a **[+] hibrid kapcsolódás hozzáadása** elemet.  Ekkor megjelenik a már létrehozott Hibrid kapcsolatok listája. Ha hozzá szeretne adni egy vagy több alkalmazást az alkalmazáshoz, válassza ki a kívánt beállításokat, majd válassza a **kiválasztott hibrid kapcsolatok hozzáadása** lehetőséget.  

![Képernyőkép a hibrid kapcsolatok portálján][3]

Ha új hibrid kapcsolatokat szeretne létrehozni, válassza az **új hibrid kapcsolatok létrehozása** lehetőséget. Határozza meg a következőket: 

- Hibrid kapcsolatok neve.
- Végponti állomásnév.
- Végpont portja.
- Service Bus a használni kívánt névteret.

![Az új hibrid kapcsolatok létrehozása párbeszédpanel képernyőképe][4]

Minden hibrid kapcsolódás egy Service Bus névtérhez van kötve, és minden Service Bus névtér egy Azure-régióban található. Fontos, hogy megpróbáljon egy Service Bus névteret használni az alkalmazással megegyező régióban, hogy elkerülje a hálózat által okozott késést.

Ha el szeretné távolítani a hibrid kapcsolatot az alkalmazásból, kattintson rá a jobb gombbal, és válassza a **Leválasztás** lehetőséget.  

Ha hibrid kapcsolat van hozzáadva az alkalmazáshoz, egyszerűen kiválaszthatja a részleteket. 

![Képernyőkép a hibrid kapcsolatok részleteiről][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Hibrid kapcsolatok létrehozása a Azure Relay portálon ###

Az alkalmazáson belüli portálon kívül Hibrid kapcsolatok is létrehozhat a Azure Relay portálon. A App Service által használandó hibrid kapcsolatok esetében a következőket kell tennie:

* Ügyfél-hitelesítés megkövetelése.
* Rendelkeznie kell egy végpont nevű metaadat-elemmel, amely a gazdagép: Port kombinációt tartalmazza értékként.

## <a name="hybrid-connections-and-app-service-plans"></a>Hibrid kapcsolatok és App Service csomagok ##

A App Service Hibrid kapcsolatok csak az alapszintű, a standard, a prémium és az elkülönített díjszabási SKU-ban érhető el. A díjszabási csomaghoz kötött korlátok vannak kötve.  

| Díjszabási csomag | A tervben használható Hibrid kapcsolatok száma |
|----|----|
| Alapszintű | 5/csomag |
| Standard | 25/csomag |
| PremiumV2 | 200/alkalmazás |
| Izolált | 200/alkalmazás |

A App Service terv felhasználói felületén megtekintheti, hogy hány Hibrid kapcsolatok használatban van, és milyen alkalmazásokkal.  

![Képernyőkép a App Service terv tulajdonságairól][6]

A részletek megtekintéséhez válassza a hibrid kapcsolat lehetőséget. Láthatja az összes információt, amelyet az alkalmazás nézetében látott. Azt is megtudhatja, hogy a csomagban hány más alkalmazás használja a hibrid kapcsolatokat.

A App Service-csomagban használható hibrid kapcsolatok végpontok száma korlátozott. Az egyes használt hibrid kapcsolatok azonban tetszőleges számú alkalmazásban használhatók a csomagban. Például egy App Service-csomagban lévő öt különálló alkalmazásban használt egyetlen hibrid kapcsolatok egy hibrid kapcsolatokként számítanak.

### <a name="pricing"></a>Díjszabás ###

Amellett, hogy App Service terv SKU-követelménye van, a Hibrid kapcsolatok használatának további díja is van. A hibrid kapcsolatok által használt minden figyelőért díjat számítunk fel. A figyelő a hibridkapcsolat-kezelő. Ha öt Hibrid kapcsolatok támogatta két hibrid Csatlakozáskezelő, ez 10 figyelő lenne. További információ: [Service Bus díjszabása][sbpricing].

## <a name="hybrid-connection-manager"></a>hibridkapcsolat-kezelő ##

A Hibrid kapcsolatok szolgáltatáshoz a hibrid kapcsolati végpontot üzemeltető továbbító ügynökre van szükség a hálózaton. A továbbító ügynök neve hibridkapcsolat-kezelő (HCM). Az HCM letöltéséhez az alkalmazásból a [Azure Portalban][portal]válassza a **hálózat**  >  **beállítása a hibrid kapcsolati végpontok** lehetőséget.  

Ez az eszköz a Windows Server 2012-es és újabb verzióin fut. A HCM szolgáltatásként fut, és az 443-as porton csatlakozik a kimenő Azure Relayhoz.  

A HCM telepítése után HybridConnectionManagerUi.exe futtathatja az eszköz felhasználói felületének használatát. Ez a fájl a hibridkapcsolat-kezelő telepítési könyvtárában található. A Windows 10-es verzióban egyszerűen kereshet *hibridkapcsolat-kezelő felhasználói felületet* is a keresőmezőbe.  

![Képernyőkép a hibridkapcsolat-kezelőról][7]

Amikor elindítja a HCM felhasználói felületét, az első dolog, amit látni fog egy tábla, amely felsorolja az összes olyan Hibrid kapcsolatok, amely a HCM ezen példányával van konfigurálva. Ha módosításokat szeretne végezni, először hitelesítenie kell magát az Azure-ban. 

Egy vagy több Hibrid kapcsolatok hozzáadása a HCM-hez:

1. Indítsa el a HCM felhasználói felületét.
2. Válassza a **másik hibrid kapcsolatok konfigurálása** lehetőséget.
![Képernyőfelvétel az új Hibrid kapcsolatok konfigurálásáról][8]

1. Jelentkezzen be az Azure-fiókjával, hogy a Hibrid kapcsolatok elérhető legyen az előfizetésében. A HCM nem folytatja az Azure-fiók használatát ezen túlmenően. 
1. Válasszon előfizetést.
1. Válassza ki azt a Hibrid kapcsolatok, amelyre a HCM-t továbbítani kívánja.
![Képernyőkép a Hibrid kapcsolatokról][9]

1. Kattintson a **Mentés** gombra.

Ekkor megjelenik a hozzáadott Hibrid kapcsolatok. A részletek megtekintéséhez a konfigurált hibrid kapcsolat lehetőséget is választhatja.

![Képernyőkép a hibrid kapcsolat részleteiről][10]

A-ben konfigurált Hibrid kapcsolatok támogatásához HCM szükséges:

- TCP-hozzáférés az Azure-hoz a 443-as porton keresztül.
- TCP-hozzáférés a hibrid kapcsolati végponthoz.
- DNS-keresések lehetősége a végponti gazdagépen és a Service Bus névtérben.

> [!NOTE]
> Azure Relay a kapcsolódáshoz használt webes szoftvercsatornán alapul. Ez a funkció csak a Windows Server 2012-es vagy újabb verzióiban érhető el. Emiatt az HCM nem támogatott a Windows Server 2012-nél korábbi verziókon.
>

### <a name="redundancy"></a>Redundancia ###

Mindegyik HCM több Hibrid kapcsolatokt is támogat. Az adott hibrid kapcsolatokat is több HCMs is támogathatja. Az alapértelmezett viselkedés az, hogy átirányítsa a forgalmat az adott végpont konfigurált HCMs. Ha magas rendelkezésre állást szeretne a Hibrid kapcsolatok a hálózatról, több HCMs is futtathat külön gépeken. A továbbítási szolgáltatás által a HCMs való adatforgalom elosztásához használt terheléselosztási algoritmus véletlenszerű hozzárendelés. 

### <a name="manually-add-a-hybrid-connection"></a>Hibrid kapcsolatok manuális hozzáadása ###

Ha engedélyezni szeretné, hogy az előfizetésen kívüli személy egy HCM-példányt működtessen egy adott hibrid kapcsolathoz, ossza meg a hibrid kapcsolathoz tartozó átjáró kapcsolati karakterláncát. Az átjáró-kapcsolatok karakterláncát a [Azure Portal][portal]hibrid kapcsolatok tulajdonságainál tekintheti meg. A karakterlánc használatához válassza a **manuális bevitel** a HCM-ben lehetőséget, majd illessze be az átjáró kapcsolódási karakterláncát.

![Hibrid kapcsolatok manuális hozzáadása][11]

### <a name="upgrade"></a>Frissítés ###

Az hibridkapcsolat-kezelő rendszeres időközönként frissülnek a problémák megoldásához vagy a javításokhoz. Amikor megjelent a frissítések, megjelenik egy előugró ablak a HCM felhasználói felületen. A frissítés alkalmazása alkalmazza a módosításokat, majd újraindítja a HCM-t. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Hibrid kapcsolatok hozzáadása az alkalmazáshoz programozott módon ##

A Hibrid kapcsolatok Azure CLI-támogatással rendelkezik. A megadott parancsok mind az alkalmazás, mind a App Servicei csomag szintjén működnek.  Az alkalmazás szintű parancsok a következők:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

A App Service díjcsomag parancsaival beállíthatja, hogy a rendszer melyik kulcsot fogja használni az adott hibrid kapcsolatok esetében. Minden hibrid kapcsolatban két kulcs van beállítva, amely egy elsődleges és egy másodlagos. Az alábbi parancsokkal használhatja az elsődleges vagy a másodlagos kulcsot. Ez lehetővé teszi a kulcsok váltását, ha rendszeresen szeretné újragenerálni a kulcsokat. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>A Hibrid kapcsolatok biztonságossá tétele ##

Meglévő hibrid kapcsolatok más App Service Web Appshoz is hozzáadhatók olyan felhasználók számára, akik megfelelő engedélyekkel rendelkeznek az alapul szolgáló Azure Service Bus-továbbítóhoz. Ez azt jelenti, hogy ha meg kell akadályoznia, hogy mások újra használják ugyanezt a hibrid kapcsolatot (például ha a célként megadott erőforrás olyan szolgáltatás, amely nem rendelkezik a jogosulatlan hozzáférés megakadályozása érdekében szükséges további biztonsági intézkedésekkel), akkor le kell zárnia az Azure Service Bus-továbbítóhoz való hozzáférést.

Bárki, aki `Reader` hozzáféréssel rendelkezik a továbbítóhoz, _megtekintheti_ a hibrid kapcsolatot, amikor felveszi azt a webalkalmazásba a Azure Portalban, de nem fogja tudni _felvenni_ , mert nincs engedélye a továbbítási kapcsolat létrehozásához használt kapcsolati karakterlánc lekérésére. A hibrid kapcsolatok sikeres hozzáadásához `listKeys` engedélyekkel kell rendelkezniük ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ). A `Contributor` továbbítón a szerepkör vagy bármely más szerepkör, amely tartalmazza ezt az engedélyt, lehetővé teszi a felhasználók számára, hogy a hibrid kapcsolatokat használják, és hozzáadja a saját web Appshoz.

## <a name="troubleshooting"></a>Hibaelhárítás ##

A "Connected" állapot azt jelenti, hogy legalább egy HCM a hibrid kapcsolattal van konfigurálva, és képes elérni az Azure-t. Ha a hibrid kapcsolat állapota nem **kapcsolódik a hálózathoz**, a hibrid kapcsolat nincs KONFIGURÁLVA olyan HCM-re, amely hozzáfér az Azure-hoz. Ha a HCM **nem csatlakozik** , néhány dolgot ellenőriznie kell:

* Rendelkezik a gazdagép kimenő hozzáféréssel az Azure-hoz az 443-as porton? Tesztelheti a HCM-gazdagépről a PowerShell-parancs *tesztelése – NetConnection cél – P port* használatával 
* A HCM valószínűleg rossz állapotban van? Próbálja meg újraindítani az "Azure hibridkapcsolat-kezelő szolgáltatás" helyi szolgáltatást.

Ha az állapota **kapcsolódik** , de az alkalmazás nem tudja elérni a végpontot, akkor:

* Győződjön meg arról, hogy DNS-nevet használ a hibrid kapcsolatban. Ha IP-címet használ, akkor előfordulhat, hogy a szükséges ügyfél DNS-keresése nem fog történni. Ha a webalkalmazásban futó ügyfél nem végez DNS-keresést, akkor a hibrid kapcsolat nem fog működni.
* Győződjön meg arról, hogy a hibrid kapcsolatban használt DNS-név fel tud oldani a HCM-gazdagépről. A felbontást az *nslookup EndpointDNSname* használatával tekintheti meg, ahol a EndpointDNSname pontosan egyeznek a hibrid kapcsolat definíciójában használtkkal.
* tesztelje a hozzáférést a HCM-gazdagépről a végpontra a PowerShell-parancs *test-NetConnection EndpointDNSname-P portjának*  használatával, ha nem tudja elérni a végpontot a HCM-gazdagépről, majd ellenőrizze a két gazdagép közötti tűzfalat, beleértve a gazdagépen alapuló tűzfalakat is a cél gazdagépen.

App Service a **tcpping** parancssori eszköz a speciális eszközök (kudu) konzolról hívható meg. Ez az eszköz tudja megállapítani, hogy van-e hozzáférése egy TCP-végponthoz, de nem mondja el, hogy van-e hozzáférése hibrid kapcsolati végponthoz. Ha az eszközt a-konzolon egy hibrid kapcsolódási végponton használja, csak egy gazdagép: Port kombinációt használ.  

Ha rendelkezik parancssori ügyféllel a végponthoz, tesztelheti a kapcsolatot az App Console használatával. A webkiszolgáló-végpontokhoz való hozzáférést például a curl használatával ellenőrizheti.


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
