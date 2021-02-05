---
title: Biztonsági és hozzáférés-vezérlési problémák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory biztonsági és hozzáférés-vezérlési problémáit.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2021
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 37da2869f0cf315ac0a6851c9a77dcee4debf71a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581686"
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

"Nem sikerült lekérni a Service tokent az ADF szolgáltatásból a (z) * * * * * * * * * * * * * * * * * * * * * * * * * * * 0,1250079 másodperc, a hibakód a következő: InvalidGatewayKey, tevékenységazonosító: XXXXXXX és részletes hibaüzenet az ügyfél IP-címe nem érvényes magánhálózati IP-cím, mert az adatelőállító nem tudta elérni a nyilvános hálózatot, így nem tud hozzáférni a felhőhöz a sikeres kapcsolat érdekében."

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

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Privát hivatkozás a Data Factory](data-factory-private-link.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [A Microsoft Q&egy oldalt](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)