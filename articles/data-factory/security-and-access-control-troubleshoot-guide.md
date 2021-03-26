---
title: Biztonsági és hozzáférés-vezérlési problémák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory biztonsági és hozzáférés-vezérlési problémáit.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: 5e94ea989002d3d3c6d0e96123d5b8ddb5f078c3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568035"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Biztonsági és hozzáférés-vezérlési problémák elhárítása Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory biztonsági és hozzáférés-vezérlésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Kapcsolódási probléma a Cloud adattár másolási tevékenységében

#### <a name="symptoms"></a>Hibajelenségek

A forrás-vagy a fogadó adattárban csatlakozási problémák merülhetnek fel.

#### <a name="cause"></a>Ok 

A problémát általában az alábbi tényezők okozzák:

* A saját üzemeltetésű integrációs modul (IR) csomópontjának proxy beállítása, ha saját üzemeltetésű IR-t használ.

* A tűzfal beállítása a saját üzemeltetésű IR-csomópontban, ha saját üzemeltetésű integrációs modult használ.

* A tűzfal beállítása a felhő adattárában.

#### <a name="resolution"></a>Feloldás

* A kapcsolódási probléma megoldásához ellenőrizze a következő szempontokat:

   - A hiba a forrás vagy a fogadó összekötőtől származik.
   - A hiba a másolási tevékenység elején történik.
   - A hiba konzisztens a Azure IR vagy a saját üzemeltetésű, egyetlen csomóponttal rendelkező integrációs modul esetében, mert a több csomópontos, saját üzemeltetésű integrációs modul véletlenszerű meghibásodása lehet, ha csak néhány csomópont rendelkezik a problémával.

* Ha **saját** üzemeltetésű integrációs modult használ, ellenőrizze a proxy-, a tűzfal-és a hálózati beállításokat, mert az ugyanahhoz az adattárhoz való csatlakozás sikeres lehet, ha Azure IR használ. A forgatókönyv hibáinak megoldásához tekintse meg a következőt:

   * [Saját üzemeltetésű IR-portok és tűzfalak](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage-összekötő](./connector-azure-data-lake-store.md)
  
* Ha **Azure IR** használ, próbálja meg letiltani az adattár tűzfalbeállítások beállítását. Ez a módszer a következő két helyzetben képes megoldani a problémákat:
  
   * [Azure IR IP-címek](./azure-integration-runtime-ip-addresses.md) nem szerepelnek az engedélyezési listán.
   * A *megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz* funkció ki van kapcsolva az [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) és [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * Az *Azure-szolgáltatásokhoz való hozzáférés engedélyezése* beállítás nincs engedélyezve a Azure Data Lake Storage Gen1.

Ha az előző módszerek egyike sem működik, forduljon a Microsofthoz segítségért.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Érvénytelen vagy üres hitelesítési kulcs probléma a nyilvános hálózati hozzáférés letiltása után

#### <a name="symptoms"></a>Hibajelenségek

Miután letiltotta Data Factory nyilvános hálózati hozzáférését, a saját üzemeltetésű integrációs modul a következő hibát veti fel: "a hitelesítési kulcs érvénytelen vagy üres."

#### <a name="cause"></a>Ok

A problémát valószínűleg a tartománynévrendszer (DNS) feloldási problémája okozza, mivel a nyilvános kapcsolatok letiltása és a privát végpontok létrehozása megakadályozza az újracsatlakozást.

Annak ellenőrzéséhez, hogy a Data Factory teljes tartományneve (FQDN) fel van-e oldva a nyilvános IP-címhez, tegye a következőket:

1. Győződjön meg arról, hogy az Azure virtuális gépet (VM) ugyanabban a virtuális hálózaton hozta létre, mint a Data Factory magánhálózati végpontot.

2. Futtassa a PsPing és a ping parancsot az Azure-beli virtuális gépről a Data Factory FQDN-re:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > Meg kell adnia egy portot a PsPing parancshoz. Az 443-es port itt látható, de nem kötelező.

3. Ellenőrizze, hogy mindkét parancs egy adott régión alapuló Azure Data Factory nyilvános IP-címhez van-e feloldva. Az IP-címet a következő formátumban kell megadni: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Feloldás

A probléma megoldásához tegye a következőket:

- A lehetőségként azt javasoljuk, hogy manuálisan vegyen fel egy "Virtual Network hivatkozást" a "Private Link DNS Zone" Data Factory alatt. További részletekért tekintse meg az [Azure Private-hivatkozást Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) cikkhez. Az utasítás a magánhálózati DNS-zóna vagy az egyéni DNS-kiszolgáló konfigurálására szolgál a Data Factory FQDN privát IP-címhez való feloldásához. 

- Ha azonban nem szeretné konfigurálni a magánhálózati DNS-zónát vagy az egyéni DNS-kiszolgálót, próbálja meg a következő ideiglenes megoldást:

  1. Módosítsa a Windows rendszerű gazdagépet, és rendelje hozzá a magánhálózati IP-címet (a Azure Data Factory magánhálózati végpontot) a Azure Data Factory FQDN-hez.
  
     Az Azure-beli virtuális gépen lépjen a (z `C:\Windows\System32\drivers\etc` ) elemre, majd nyissa meg a fájlt a Jegyzettömbben.  Adja hozzá azt a sort, amely a magánhálózati IP-címet leképezi a fájl végén lévő teljes tartománynévre, és mentse a változást.
     
     ![Képernyőfelvétel: a magánhálózati IP-cím hozzárendelése a gazdagéphez.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Futtassa újra ugyanazokat a parancsokat, mint az előző ellenőrzési lépésekben a válasz ellenőrzéséhez, amelynek tartalmaznia kell a magánhálózati IP-címet.

  1. Regisztrálja újra a saját üzemeltetésű integrációs modult, és a problémát fel kell oldani.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépeken privát hivatkozás miatt

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépen, mert a magánhálózati kapcsolat engedélyezve van. A következő hibaüzenet jelenik meg:

"Nem sikerült lekérni a Service tokent az ADF szolgáltatásból a (z) * * * * * * * * * * * * * * * * * * * * * * * * * * * 0,1250079 Másodszor, a hibakód a következő: InvalidGatewayKey, tevékenységazonosító: XXXXXXX és részletes hibaüzenet az ügyfél IP-címe nem érvényes magánhálózati IP-cím, mert az adatelőállító nem tudott hozzáférni a nyilvános hálózathoz, így nem tudja elérni a felhőt a sikeres kapcsolat létrehozásához."

#### <a name="cause"></a>Ok

A problémát a virtuális gép okozhatja, amelyben a saját üzemeltetésű integrációs modult próbálja telepíteni. A felhőhöz való kapcsolódáshoz ellenőrizze, hogy engedélyezve van-e a nyilvános hálózati hozzáférés.

#### <a name="resolution"></a>Feloldás

**1\. megoldás**
 
A probléma megoldásához tegye a következőket:

1. Nyissa meg a [gyárak – frissítés](/rest/api/datafactory/Factories/Update) lapot.

1. A jobb felső sarokban kattintson a **TRY IT (kipróbálás** ) gombra.
1. A **Paraméterek** területen végezze el a szükséges információkat. 
1. A **törzs** alatt illessze be a következő tulajdonságot:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Válassza a **Futtatás** lehetőséget a függvény futtatásához. 

1. A **Paraméterek** területen végezze el a szükséges információkat. 

1. A **törzs** alatt illessze be a következő tulajdonságot:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Válassza a **Futtatás** lehetőséget a függvény futtatásához. 
1. Győződjön meg arról, hogy a 200-es **hibakód** jelenik meg. A beillesztett tulajdonságnak a JSON-definícióban is szerepelnie kell.

1. Adja hozzá ismét az IR-hitelesítési kulcsot az Integration Runtime-ban.

**2\. megoldás**

A probléma megoldásához nyissa meg a [Azure Data Factory Azure-beli privát hivatkozását](./data-factory-private-link.md).

Próbálja meg engedélyezni a nyilvános hálózati hozzáférést a felhasználói felületen a következő képernyőképen látható módon:

![Képernyőfelvétel: "engedélyezve" vezérlő a "nyilvános hálózati hozzáférés engedélyezése" beállításnál a hálózatkezelés ablaktáblán.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>A privát DNS-zónák automatikus kiváltása Azure Resource Manager DNS-feloldási hibát okozó "nem található" hiba

#### <a name="cause"></a>Ok
Mind a Azure Resource Manager, mind az ADF ugyanazt a privát zónát használja, amely az ügyfél privát DNS-beli potenciális ütközését hozza létre olyan forgatókönyv esetén, amelyben a Azure Resource Manager rekordok nem találhatók.

#### <a name="solution"></a>Megoldás
1. Saját DNS zónák **privatelink.Azure.com** megkeresése Azure Portalban.
![Képernyőkép a saját DNS zónák megkereséséről.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Ellenőrizze, hogy van-e egy rekord **ADF**.
![Képernyőfelvétel egy rekordról.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Válassza a **virtuális hálózati kapcsolatok**, majd az összes rekord törlése lehetőséget.
![Képernyőfelvétel a virtuális hálózati kapcsolatról.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Azure Portal navigáljon az adatgyárhoz, és hozza létre újra a Azure Data Factory portál privát végpontját.
![Képernyőkép a privát végpont újbóli létrehozásáról.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Térjen vissza saját DNS zónákhoz, és ellenőrizze, hogy van-e új **privatelink.ADF.Azure.com** a saját DNS-zónában.
![Képernyőfelvétel az új DNS-rekordról.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Csatlakoztatási hiba a nyilvános végponton

#### <a name="symptoms"></a>Hibajelenségek

Amikor az Azure Blob Storage fiók nyilvános hozzáférését másolja, a folyamat véletlenszerűen leáll a következő hiba miatt.

Például: az Azure Blob Storage fogadó Azure IR (nyilvános, nem felügyelt VNet) használ, és a Azure SQL Database forrás használta a felügyelt VNet IR-t. Vagy a forrás/fogadó a felügyelt VNet IR-t használja csak a Storage nyilvános eléréssel.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Ok

Az ADF továbbra is használhatja a felügyelt VNet IR-t, de ilyen hiba merülhet fel, mivel az Azure Blob Storage felügyelt VNet való nyilvános végpontja nem megbízható a tesztelési eredmény alapján, és az Azure Blob Storage és Azure Data Lake Gen2 nem támogatott nyilvános Virtual Network végponton keresztül a felügyelt [virtuális hálózatok & felügyelt privát végpontok](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)alapján történő csatlakoztatására.

#### <a name="solution"></a>Megoldás

- Ha a felügyelt VNet IR-t használja, a forráson és a fogadó oldalon is engedélyezve van a magánhálózati végpont.
- Ha továbbra is a nyilvános végpontot szeretné használni, átválthat a nyilvános IR-re a forráshoz és a fogadóhoz tartozó felügyelt VNet használata helyett. Ha a nyilvános IR-re vált, az ADF továbbra is használhatja a felügyelt VNet IR-t, ha a felügyelt VNet IR-je még létezik.

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Privát hivatkozás a Data Factory](data-factory-private-link.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [A Microsoft Q&egy oldalt](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)