---
title: Data Factory adatkezelés átjárója
description: Az adatáthelyezéshez használja a Azure Data Factory adatkezelés átjáróját.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 708d84bdb3ebe8fbba6939aa771a9120868d5d1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375204"
---
# <a name="data-management-gateway"></a>Adatkezelési átjáró
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a saját üzemeltetésű [integrációs modul](../create-self-hosted-integration-runtime.md)című részét.

> [!NOTE]
> Adatkezelés-átjárót már saját üzemeltetésű Integration Runtime-ként lett átnevezve.

Az adatkezelési átjáró egy olyan ügyfél-ügynök, amelyet a helyszíni környezetbe kell telepítenie a Felhőbeli és a helyszíni adattárak közötti adatmásoláshoz. A Data Factory által támogatott helyszíni adattárak listája a [támogatott adatforrások](data-factory-data-movement-activities.md#supported-data-stores-and-formats) szakaszban található.

Ez a cikk az [adatáthelyezés a helyszíni és a Felhőbeli adattárak között című cikk lépéseit](data-factory-move-data-between-onprem-and-cloud.md) egészíti ki. Az útmutatóban egy olyan folyamatot hoz létre, amely az átjárót használja az adatok SQL Server adatbázisból egy Azure-blobba való áthelyezéséhez. Ez a cikk részletes információkat tartalmaz az adatkezelési átjáróról.

Egy adatkezelési átjárót úgy méretezheti fel, hogy több helyszíni gépet társít az átjáróhoz. Az adatáthelyezési feladatok növekvő száma növelhető, amely egyidejűleg futhat egy csomóponton. Ez a funkció egyetlen csomóponttal rendelkező logikai átjáró esetén is elérhető. Részletekért lásd: az [adatkezelési átjáró skálázása Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) cikkben.

> [!NOTE]
> Az átjáró jelenleg csak a másolási tevékenységet és a tárolt eljárási tevékenységet támogatja Data Factoryban. A helyszíni adatforrásokhoz való hozzáféréshez nem lehetséges egyéni tevékenység átjáróját használni.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
### <a name="capabilities-of-data-management-gateway"></a>Az adatkezelési átjáró képességei
Az adatkezelési átjáró a következő képességeket biztosítja:

* A helyszíni adatforrások és a Felhőbeli adatforrások modellezése ugyanazon az adatgyáron belül és az adatáthelyezés.
* Az átjáró állapotának ellenőrzéséhez és kezeléséhez a Data Factory oldaláról egyetlen üvegtábla áll rendelkezésre.
* Biztonságosan kezelheti a helyszíni adatforrásokhoz való hozzáférést.
  * Nincs szükség módosításra a vállalati tűzfalon. Az átjáró csak a kimenő HTTP-alapú kapcsolatokat teszi lehetővé az Internet megnyitásához.
  * A tanúsítványával titkosíthatja a helyszíni adattárak hitelesítő adatait.
* Az adatáthelyezés hatékony – az adatátvitelt párhuzamosan, rugalmasan, az automatikus újrapróbálkozási logikával megszakított hálózati problémák okozhatja.

### <a name="command-flow-and-data-flow"></a>A parancs folyamata és adatfolyama
Ha másolási tevékenységet használ a helyszíni és a felhő közötti adatmásoláshoz, a tevékenység átjáró használatával továbbítja az adatok átvitelét a helyszíni adatforrásból a felhőbe, és fordítva.

Itt látható az adatátjáróval való másolás lépéseinek részletes adatfolyamata és összefoglalása: az ![ átjárót használó adatforgalom](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Az adatfejlesztő létrehoz egy átjárót egy Azure Data Factoryhoz a [Azure Portal](https://portal.azure.com) vagy a [PowerShell-parancsmag](/powershell/module/az.datafactory/)használatával.
2. Az adatfejlesztő létrehoz egy társított szolgáltatást a helyszíni adattárakhoz az átjáró megadásával. A társított szolgáltatás beállításának részeként az adat-fejlesztő a hitelesítő adatok beállítása alkalmazás használatával határozza meg a hitelesítési típusokat és a hitelesítő adatokat. A hitelesítő adatok beállítása párbeszédpanel kommunikál az adattárral a kapcsolatok teszteléséhez és az átjáróhoz a hitelesítő adatok mentéséhez.
3. Az átjáró a hitelesítő adatoknak a felhőben való mentése előtt titkosítja a hitelesítő adatokat az átjáróhoz társított tanúsítvánnyal (az adatfejlesztő által szolgáltatott).
4. Data Factory a szolgáltatás az átjáróval kommunikál a feladatok & kezeléséhez egy olyan vezérlési csatornán keresztül, amely megosztott Azure Service Bus-várólistát használ. Ha egy másolási tevékenységre vonatkozó feladatot ki kell rúgni, Data Factory a kérést a hitelesítő adatokkal együtt. Az átjáró lekéri a feladatot a várólista lekérdezése után.
5. Az átjáró visszafejti a hitelesítő adatokat ugyanazzal a tanúsítvánnyal, majd a megfelelő hitelesítési típussal és hitelesítő adatokkal kapcsolódik a helyszíni adattárolóhoz.
6. Az átjáró átmásolja a helyszíni tárolóból a Felhőbeli tárhelyre, vagy fordítva, attól függően, hogy a másolási tevékenység hogyan lett konfigurálva az adatfolyamatban. Ebben a lépésben az átjáró közvetlenül kommunikál a felhőalapú tárolási szolgáltatásokkal, például az Azure Blob Storage egy biztonságos (HTTPS-) csatornán keresztül.

### <a name="considerations-for-using-gateway"></a>Az átjáró használatának szempontjai
* Az adatkezelési átjáró egy példánya több helyszíni adatforráshoz is használható. **Egy átjáró-példány azonban csak egy Azure-beli adatgyárhoz van kötve** , és nem osztható meg másik adatelőállítóval.
* **Az adatkezelési átjáró csak egyetlen példányát** telepítheti egyetlen gépre. Tegyük fel, hogy két, a helyszíni adatforrásokhoz hozzáférő adatgyárral rendelkezik, két helyszíni számítógépre kell telepítenie az átjárókat. Más szóval az átjáró egy adott adatgyárhoz van kötve
* Az **átjárónak nem kell ugyanazon a gépen lennie, mint az adatforrásnak**. Ugyanakkor az adatforráshoz közelebbi átjáró használata csökkenti az átjárónak az adatforráshoz való kapcsolódáshoz szükséges időt. Azt javasoljuk, hogy az átjárót olyan gépre telepítse, amely eltér a helyszíni adatforrást futtató gépről. Ha az átjáró és az adatforrás különböző gépeken található, az átjáró nem versenyez az adatforrással rendelkező erőforrások esetében.
* **A különböző gépeken több átjáró is lehet ugyanahhoz a helyszíni adatforráshoz csatlakozni**. Előfordulhat például, hogy két, két adatgyárat kiszolgáló átjáróval rendelkezik, de ugyanaz a helyszíni adatforrás is regisztrálva van az adatelőállítókkal.
* Ha a számítógépen már telepítve van egy **Power bi** forgatókönyvet kiszolgáló átjáró, telepítsen egy **külön átjárót a Azure Data Factory** egy másik gépen.
* Az átjárót akkor is használni kell, ha a **ExpressRoute**-t használja.
* Az adatforrást helyszíni adatforrásként (tűzfal mögött) kezelheti, még akkor is, ha a **ExpressRoute**-t használja. Az átjáró használatával kapcsolatot létesíthet a szolgáltatás és az adatforrás között.
* **Az átjárót** akkor is kell használnia, ha az adattár a felhőben van egy **Azure IaaS virtuális gépen**.

## <a name="installation"></a>Telepítés
### <a name="prerequisites"></a>Előfeltételek
* A támogatott **operációsrendszer** -verziók a Windows 7, a Windows 8/8.1, a Windows 10, a windows Server 2008 R2, a windows Server 2012, a windows Server 2012 R2. Az adatkezelési átjáró tartományvezérlőn való telepítése jelenleg nem támogatott.
* A .NET-keretrendszer 4.5.1-es vagy újabb verziójának megadása kötelező. Ha Windows 7 rendszerű gépen telepíti az átjárót, telepítse a .NET-keretrendszer 4,5-es vagy újabb verzióját. A részletekért lásd a [.NET-keretrendszer rendszerkövetelményeit](/dotnet/framework/get-started/system-requirements) ismertető témakört.
* Az átjárót tartalmazó számítógép ajánlott **konfigurációja** legalább 2 GHz, 4 mag, 8 GB RAM és 80 GB-os lemez.
* Ha a gazdaszámítógép hibernált állapotba kerül, az átjáró nem válaszol az adatkérelmekre. Ezért konfigurálja a megfelelő **energiasémát** a számítógépen az átjáró telepítése előtt. Ha a gép hibernált állapotra van állítva, az átjáró telepítése üzenetet kér.
* Az adatkezelési átjáró sikeres telepítéséhez és konfigurálásához rendszergazdának kell lennie a gépen. További felhasználókat adhat hozzá az **adatkezelési átjáró felhasználói** helyi Windows-csoportjához. Ennek a csoportnak a tagjai a **adatkezelés gateway Configuration Manager** eszköz használatával konfigurálhatják az átjárót.

Ha a másolási tevékenység futása egy adott gyakoriságon történik, az erőforrás-használat (CPU, memória) a gépen is ugyanazt a mintát követi, mint a csúcs és az üresjárati idő. Az erőforrás-használat az áthelyezett adatok mennyiségétől függ. Ha több másolási feladat van folyamatban, az erőforrás-használat a csúcs idején jelenik meg.

### <a name="installation-options"></a>Telepítési lehetőségek
Az adatkezelési átjárót a következő módokon lehet telepíteni:

* Egy MSI-telepítőcsomagot a [Microsoft letöltőközpontból töltheti](https://www.microsoft.com/download/details.aspx?id=39717)le. Az MSI használatával a meglévő adatkezelési átjárót a legújabb verzióra is frissítheti, minden beállítás megőrzött.
* Az **adatátjáró letöltéséhez és telepítéséhez** kattintson a manuális telepítés lehetőségre, vagy **telepítse közvetlenül ezen a számítógépen** az expressz beállítás alatt. Az expressz telepítéssel kapcsolatos részletes utasításokért tekintse [meg az adatáthelyezés a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) című cikket. A manuális lépés a letöltőközpontból lép. Az átjáró a letöltőközpontból való letöltésére és telepítésére vonatkozó utasítások a következő szakaszban találhatók.

### <a name="installation-best-practices"></a>Ajánlott eljárások a telepítéshez:
1. Konfigurálja az energiagazdálkodási tervet a gazdagépen az átjáróhoz, hogy a gép ne legyen hibernálva. Ha a gazdaszámítógép hibernált állapotba kerül, az átjáró nem válaszol az adatkérelmekre.
2. Az átjáróhoz társított tanúsítvány biztonsági mentése.

### <a name="install-the-gateway-from-download-center"></a>Az átjáró telepítése a letöltőközpontból
1. Navigáljon a [Microsoft adatkezelés átjáró letöltési oldalára](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kattintson a **Letöltés** lehetőségre, válassza ki a **64 bites** verziót (a 32 bites verzió nem támogatott), és kattintson a **tovább** gombra.
3. Futtassa közvetlenül az **MSI** -t, vagy mentse a merevlemezre, és futtassa a parancsot.
4. Az **üdvözlőlapon** válassza ki a kívánt **nyelvet** , majd kattintson a **tovább** gombra.
5. **Fogadja el** az End-User licencszerződést, és kattintson a **tovább** gombra.
6. Válassza ki az átjáró telepítéséhez szükséges **mappát** , majd kattintson a **tovább** gombra.
7. A **Telepítésre kész** oldalon kattintson a **Telepítés** elemre.
8. A telepítés befejezéséhez kattintson a **Befejezés** gombra.
9. Szerezze be a kulcsot a Azure Portal. A Részletes utasításokért tekintse meg a következő szakaszt.
10. A számítógépen futó **adatkezelés gateway Configuration Manager** átjárójának **regisztrálása** lapon végezze el a következő lépéseket:
    1. Illessze be a kulcsot a szövegbe.
    2. Ha szeretné megtekinteni a kulcs szövegét, kattintson az **átjáró kulcsának megjelenítése** lehetőségre.
    3. Kattintson a **Regisztrálás** parancsra.

### <a name="register-gateway-using-key"></a>Átjáró regisztrálása kulcs használatával
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Ha még nem hozott létre logikai átjárót a portálon
Ha átjárót szeretne létrehozni a portálon, és a configure ( **Konfigurálás** ) lapon le szeretné kérni a kulcsot, kövesse az [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) című témakör lépéseit.

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Ha már létrehozta a logikai átjárót a portálon
1. A Azure Portal területen navigáljon a **Data Factory** lapra, és kattintson a **társított szolgáltatások** csempére.

    ![Data Factory lap](media/data-factory-data-management-gateway/data-factory-blade.png)
2. A **társított szolgáltatások** lapon válassza ki a portálon létrehozott logikai **átjárót** .

    ![logikai átjáró](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Az **adatátjáró** lapon kattintson a **letöltés és az adatátjáró telepítése** elemre.

    ![Letöltési hivatkozás a portálon](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. A **configure (Konfigurálás** ) lapon kattintson a **kulcs újbóli létrehozása** elemre. A figyelmeztetés elolvasása után kattintson az Igen gombra a figyelmeztető üzenetben.

    ![Kulcs újbóli létrehozása gomb](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kattintson a kulcs melletti Másolás gombra. A kulcsot a rendszer a vágólapra másolja.

    ![Kulcs másolása](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Rendszertálca ikonjai/értesítései
Az alábbi képen a megjelenő tálca ikonjai láthatók.

![rendszertálca ikonjai](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Ha áthelyezi a kurzort a rendszertálca ikon/értesítési üzenet fölé, az átjáró/frissítési művelet állapotát egy előugró ablakban tekintheti meg.

### <a name="ports-and-firewall"></a>Portok és tűzfal
Két tűzfalat kell figyelembe vennie: a **vállalati tűzfal** a szervezet központi útválasztóján fut, a **Windows tűzfal** pedig démonként van konfigurálva azon a helyi gépen, amelyen az átjáró telepítve van.

![tűzfalak](./media/data-factory-data-management-gateway/firewalls2.png)

A vállalati tűzfal szintjén a következő tartományokat és kimenő portokat kell konfigurálnia:

| Tartománynevek | Portok | Description |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Adatátviteli szolgáltatás-háttérrel való kommunikációhoz használatos |
| *.core.windows.net |443 |Szakaszos másoláshoz használatos az Azure Blob használatával (ha be van állítva)|
| *.frontend.clouddatahub.net |443 |Adatátviteli szolgáltatás-háttérrel való kommunikációhoz használatos |
| *.servicebus.windows.net |9350-9354, 5671 |Opcionális Service Bus Relay a másolási varázsló által használt TCP protokollon keresztül |

A Windows tűzfal szintjén ezek a kimenő portok általában engedélyezve vannak. Ha nem, a tartományokat és a portokat a megfelelő módon konfigurálhatja az átjárót használó gépen.

> [!NOTE]
> 1. A forrás/elsüllyedés alapján előfordulhat, hogy további tartományokat és kimenő portokat kell engedélyeznie a vállalati és a Windows tűzfalon.
> 2. Egyes felhőalapú adatbázisok esetében (például: [Azure SQL Database](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)stb.), előfordulhat, hogy engedélyeznie kell az átjáró gép IP-címét a tűzfal konfigurációjában.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Adatok másolása a forrás adattárból a fogadó adattárba
Győződjön meg arról, hogy a tűzfalszabályok megfelelően engedélyezve vannak a vállalati tűzfalon, a Windows tűzfalon az átjáró gépen, valamint magát az adattárat. Ezeknek a szabályoknak a engedélyezése lehetővé teszi, hogy az átjáró sikeresen kapcsolódjon a forráshoz és a fogadóhoz. Engedélyezze a szabályokat a másolási műveletben érintett összes adattárhoz.

Ha például egy helyszíni **adattárból egy Azure SQL Database fogadóba vagy egy Azure szinapszis Analytics**-fogadóba szeretne másolni, hajtsa végre a következő lépéseket:

* Engedélyezze a kimenő **TCP** -kommunikációt az **1433** -as porton a Windows tűzfal és a vállalati tűzfal esetében is.
* Konfigurálja a logikai SQL Server tűzfalbeállítások beállítását, és adja hozzá az átjáró-számítógép IP-címét az engedélyezett IP-címek listájához.

> [!NOTE]
> Ha a tűzfal nem engedélyezi a 1433-es kimenő portot, az átjáró nem fér hozzá közvetlenül az Azure SQL-hez. Ebben az esetben a [szakaszos másolással](./data-factory-copy-activity-performance.md#staged-copy) SQL Database/SQL felügyelt példány/SQL Azure DW-t használhat. Ebben az esetben csak HTTPS (443-es port) szükséges az adatáthelyezéshez.
>
>

### <a name="proxy-server-considerations"></a>A proxykiszolgáló szempontjai
Ha a vállalati hálózati környezet proxykiszolgálót használ az Internet eléréséhez, konfigurálja az adatkezelési átjárót a megfelelő proxybeállítások használatára. A proxyt a kezdeti regisztrációs fázisban állíthatja be.

![Proxy beállítása a regisztráció során](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Az átjáró a proxykiszolgálót használja a felhőalapú szolgáltatáshoz való csatlakozáshoz. Kattintson a hivatkozás **módosítása** gombra a kezdeti beállítás során. Megjelenik a **Proxybeállítások** párbeszédpanel.

![Proxy beállítása a Configuration Manager 1 használatával](media/data-factory-data-management-gateway/SetProxySettings.png)

Három konfigurációs lehetőség közül választhat:

* Ne **használja a proxyt**: az átjáró nem használ explicit módon semmilyen proxyt a Cloud Serviceshez való csatlakozáshoz.
* A **System proxy használata**: az átjáró a diahost.exe.config és diawp.exe.config konfigurált proxybeállításokat használja. Ha nincs proxy konfigurálva diahost.exe.config és diawp.exe.config, az átjáró közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez.
* **Egyéni proxy használata**: konfigurálja az átjáróhoz használni kívánt http-proxy beállítást diahost.exe.config és diawp.exe.config konfigurációk használata helyett. A címnek és a portnak kötelező megadni. A proxy hitelesítési beállításától függően a Felhasználónév és a jelszó nem kötelező. Minden beállítás titkosítva van az átjáró hitelesítő adataival, és helyileg tárolódik az átjáró gazdagépén.

Az adatkezelési átjáró gazdagép-szolgáltatása automatikusan újraindul a frissített proxybeállítások mentése után.

Miután sikeresen regisztrálta az átjárót, ha szeretné megtekinteni vagy frissíteni a proxybeállításokat, használja adatkezelés átjáró Configuration Manager.

1. **Adatkezelés átjáró Configuration Manager** elindítása.
2. Váltson a **Beállítások** lapra.
3. A http-proxy **beállítása** párbeszédpanel megnyitásához kattintson a hivatkozás **módosítása** a **http-proxyn** szakaszra.
4. Miután rákattintott a **tovább** gombra, megjelenik egy figyelmeztető párbeszédpanel, amely arra kéri az engedélyt, hogy mentse a proxybeállításokat, és indítsa újra az átjáró szolgáltatást.

Configuration Manager eszköz használatával megtekintheti és frissítheti a HTTP-proxyt.

![Proxy beállítása a Configuration Manager 2 használatával](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Ha NTLM-hitelesítéssel rendelkező proxykiszolgálót állít be, az átjáró szolgáltatás a tartományi fiók alatt fut. Ha később módosítja a tartományi fiók jelszavát, ne felejtse el frissíteni a szolgáltatás konfigurációs beállításait, majd indítsa újra. Ennek a követelménynek köszönhetően javasoljuk, hogy egy dedikált tartományi fiókot használjon a proxykiszolgáló eléréséhez, amely nem igényli a jelszó gyakori frissítését.
>
>

### <a name="configure-proxy-server-settings"></a>Proxykiszolgáló beállításainak konfigurálása
Ha a HTTP-proxyhoz a **rendszerproxy használata** beállítást választja, az átjáró a diahost.exe.config és diawp.exe.config proxy beállítását használja. Ha nincs megadva proxy a diahost.exe.configban, és diawp.exe.config, az átjáró közvetlenül a proxyn keresztül csatlakozik a Cloud Service-hez. Az alábbi eljárás útmutatást nyújt a diahost.exe.config fájl frissítéséhez.

1. A Fájlkezelőben hozzon létre egy biztonságos másolatot a *C: \\ \\ Program Files \\ Microsoft adatkezelés Gateway \\ 2,0 \\ megosztott \\diahost.exe.config* az eredeti fájl biztonsági mentéséhez.
2. Indítsa el Notepad.exe futtató rendszergazdaként, és nyissa meg a *C: \\ \\ Program Files \\ Microsoft adatkezelés Gateway \\ 2,0 \\ megosztott \\diahost.exe.config*. A system.net alapértelmezett címkéjét az alábbi kódban látható módon találja:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Ezután a következő példában látható módon adhatja hozzá a proxykiszolgáló adatait:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A proxy címkén belül további tulajdonságok is megadhatók a szükséges beállítások, például a scriptLocation számára. A szintaxisban tekintse meg a [proxy elemet (hálózati beállítások)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) .

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Mentse a konfigurációs fájlt az eredeti helyre, majd indítsa újra a adatkezelés átjáró-gazdagép szolgáltatást, amely felveszi a módosításokat. A szolgáltatás újraindítása: a Vezérlőpulton vagy a **adatkezelés átjáróból** válassza a szolgáltatások kisalkalmazása elemet Configuration Manager > kattintson a **szolgáltatás leállítása** gombra, majd kattintson a **Start Service**(szolgáltatás indítása) elemre. Ha a szolgáltatás nem indul el, valószínűleg helytelen XML-címke-szintaxis lett hozzáadva a szerkesztett alkalmazás konfigurációs fájljához.

> [!IMPORTANT]
> Ne felejtse el frissíteni a diahost.exe.config és diawp.exe.config **is** .

Ezeken a pontokon kívül meg kell győződnie arról is, Microsoft Azure a vállalat engedélyezési listáján található. Az érvényes Microsoft Azure IP-címek listáját a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=41653)töltheti le.

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>A tűzfal és a proxykiszolgáló szolgáltatással kapcsolatos problémák lehetséges tünetei
Ha a következőhöz hasonló hibák fordulnak elő, valószínűleg a tűzfal vagy a proxykiszolgáló helytelen konfigurációja miatt következik be, ami blokkolja az átjárót, hogy csatlakozzon a Data Factoryhoz a hitelesítéshez. Az előző szakaszban megtekintheti, hogy a tűzfal és a proxykiszolgáló megfelelően van-e konfigurálva.

1. Amikor megpróbálja regisztrálni az átjárót, a következő hibaüzenet jelenik meg: "nem sikerült regisztrálni az átjáró kulcsát. Mielőtt ismét megpróbálja regisztrálni az átjáró kulcsát, győződjön meg róla, hogy az adatkezelési átjáró csatlakoztatott állapotban van, és a adatkezelés átjáró-gazdagép szolgáltatás elindult. "
2. Configuration Manager megnyitásakor a "leválasztott" vagy "kapcsolódás" állapot jelenik meg. A Windows-eseménynaplók megtekintésekor a "Eseménynapló" > "alkalmazás-és szolgáltatások naplói" > "adatkezelés átjáró" alatt a következő hibaüzenet jelenik meg: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Az 8050-es port megnyitása a hitelesítő adatok titkosításához
A **hitelesítő adatok beállítása** alkalmazás a **8050** bejövő portot használja a hitelesítő adatok továbbítására az átjáróhoz, amikor helyi társított szolgáltatást állít be a Azure Portal. Az átjáró telepítése során az átjáró telepítése alapértelmezés szerint megnyitható az átjáró számítógépen.

Ha külső gyártótól származó tűzfalat használ, a 8050-es portot manuálisan is megnyithatja. Ha tűzfalon futtatja a problémát az átjáró telepítésekor, a következő parancs használatával telepítheti az átjárót a tűzfal konfigurálása nélkül.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

Ha úgy dönt, hogy nem nyitja meg az 8050-as portot az átjárót tartalmazó gépen, használja a **hitelesítő adatok beállítása** az adattároló hitelesítő adatainak beállításához használt mechanizmusokat. Használhatja például a [New-AzDataFactoryEncryptValue PowerShell-](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) parancsmagot. Lásd: a hitelesítő adatok és a Biztonság beállítása szakasz, amely az adattár hitelesítő adatainak beállítását ismerteti.

## <a name="update"></a>Frissítés
Alapértelmezés szerint az adatkezelési átjáró automatikusan frissül, amikor az átjáró újabb verziója érhető el. Az átjáró addig nem frissül, amíg az összes ütemezett feladat be nem fejeződik. Az átjáró nem dolgozza fel további feladatokat, amíg a frissítési művelet be nem fejeződik. Ha a frissítés meghiúsul, az átjáró vissza lesz állítva a régi verzióra.

Az ütemezett frissítési idő a következő helyeken jelenik meg:

* A Azure Portal átjáró tulajdonságai lapja.
* Az adatkezelés átjáró kezdőlapja Configuration Manager
* A rendszertálca értesítési üzenete.

A adatkezelés átjáró kezdőlapja Configuration Manager megjeleníti a frissítési ütemtervet, valamint az átjáró legutóbbi telepítésének vagy frissítésének utolsó időpontját.

![Frissítések ütemezése](media/data-factory-data-management-gateway/UpdateSection.png)

A frissítést azonnal telepítheti, vagy megvárhatja, hogy az átjáró automatikusan frissüljön az ütemezett időpontban. Az alábbi képen például az átjáró Configuration Manager látható értesítési üzenet jelenik meg, amelyen a frissítés gombra kattintva azonnal telepítheti.

![Frissítés a DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

A rendszertálcán lévő értesítési üzenet a következő képen látható módon jelenik meg:

![Rendszertálca üzenete](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

A frissítési művelet állapota (manuális vagy automatikus) a tálcán látható. Amikor a következő alkalommal indítja el az átjárót Configuration Manager az értesítési sávon megjelenik egy üzenet, amely az [új témakörre](data-factory-gateway-release-notes.md)mutató hivatkozást tartalmaz.

### <a name="to-disableenable-auto-update-feature"></a>Az automatikus frissítési funkció letiltása/engedélyezése
A következő lépések végrehajtásával letilthatja/engedélyezheti az automatikus frissítés funkciót:

[Egyetlen csomópontos átjáróhoz]
1. Indítsa el a Windows PowerShellt az átjáró-gépen.
2. Váltson a *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\* mappára.
3. A következő parancs futtatásával kapcsolja ki az automatikus frissítés funkciót (Letiltás).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Visszakapcsolás:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [Több csomópontos, magasan elérhető és méretezhető átjáró esetén](data-factory-data-management-gateway-high-availability-scalability.md)
1. Indítsa el a Windows PowerShellt az átjáró-gépen.
2. Váltson a *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\* mappára.
3. A következő parancs futtatásával kapcsolja ki az automatikus frissítés funkciót (Letiltás).

    A magas rendelkezésre állási funkcióval rendelkező átjáró esetén extra AuthKey paraméter szükséges.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Visszakapcsolás:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Configuration Manager
Miután telepítette az átjárót, a következő módszerek egyikével indíthatja el adatkezelés átjáró Configuration Manager:

1. A **Keresés** ablakban írja be **adatkezelés átjárót** a segédprogram eléréséhez.
2. Futtassa a végrehajtható *ConfigManager.exe* a mappában: *C: \\ \\ Program Files \\ Microsoft adatkezelés Gateway \\ 2,0 \\ Shared*.

### <a name="home-page"></a>Kezdőlap
A kezdőlapon a következő műveletek végezhetők el:

* Az átjáró állapotának megtekintése (a felhőalapú szolgáltatáshoz csatlakoztatva stb.).
* **Regisztráljon** egy kulcs használatával a portálon.
* **Állítsa le** és indítsa el a **adatkezelés Gateway Host szolgáltatást** az átjárót futtató számítógépen.
* **Frissítéseket ütemezhet** a napok adott időpontjában.
* Megtekintheti az átjáró **utolsó frissítésének** dátumát.

### <a name="settings-page"></a>Beállítások lap
A beállítások lapon a következő műveleteket hajthatja végre:

* Az átjáró által használt **tanúsítvány** megtekintése, módosítása és exportálása. Ez a tanúsítvány az adatforrás hitelesítő adatainak titkosítására szolgál.
* A végpont **https-portjának** módosítása Az átjáró megnyit egy portot az adatforrás hitelesítő adatainak beállításához.
* A végpont **állapota**
* Az **SSL-tanúsítvány** megtekintése a portál és az ÁTJÁRÓ közötti TLS/SSL-kommunikációhoz használható az adatforrások hitelesítő adatainak beállításához.

### <a name="remote-access-from-intranet"></a>Távoli elérés az intranetről
Ez a funkció a jövőben is engedélyezve lesz. A következő frissítésekben (v 3.4 vagy újabb) engedélyezheti vagy letilthatja a jelenleg a 8050-es portot használó távoli kapcsolatokat a hitelesítő adatok titkosításához a PowerShell vagy a Hitelesítőadat-kezelő alkalmazás használatakor.

### <a name="diagnostics-page"></a>Diagnosztika lap
A diagnosztika lapon a következő műveleteket hajthatja végre:

* Engedélyezze a részletes **naplózást**, tekintse meg a naplókat az eseménynaplóban, és küldjön naplókat a Microsoftnak, ha hiba történt.
* Az adatforráshoz való **Kapcsolódás tesztelése** .

### <a name="help-page"></a>Súgóoldal
A Súgó oldal a következő információkat jeleníti meg:

* Az átjáró rövid leírása
* Verziószám
* Az online súgóra, az adatvédelmi nyilatkozatra és a licencszerződésre mutató hivatkozások.

## <a name="monitor-gateway-in-the-portal"></a>Átjáró figyelése a portálon
A Azure Portal az erőforrás-kihasználtság (CPU, memória, hálózat (in/out), valamint az átjárót tartalmazó gépek közel valós idejű pillanatképét tekintheti meg.

1. A Azure Portalban navigáljon a saját adatelőállítójának kezdőlapjára, és kattintson a **társított szolgáltatások** csempére.

    ![Data factory kezdőlap](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Válassza ki az **átjárót** a **társított szolgáltatások** lapon.

    ![Társított szolgáltatások lap](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Az **átjáró** lapon láthatja az átjáró memória-és CPU-használatát.

    ![Az átjáró processzor-és memóriahasználat](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. A **Speciális beállítások** lehetővé teszik a további részletek, például a hálózati használat megjelenítését.
    
    ![Az átjáró speciális monitorozása](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Az alábbi táblázat az **átjáró-csomópontok** listájában szereplő oszlopok leírását tartalmazza:

Figyelési tulajdonság | Leírás
:------------------ | :----------
Név | Az átjáróhoz társított logikai átjáró és csomópontok neve. A csomópont egy helyszíni Windows-gép, amelyen az átjáró telepítve van. További információ arról, hogyan lehet egynél több csomópontot (legfeljebb négy csomópontot) egyetlen logikai átjáróban megtekinteni: [adatkezelés átjáró – magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md).
Állapot | A logikai átjáró és az átjáró csomópontjainak állapota. Példa: online/offline/korlátozott/stb. További információ ezekről az állapotokról: [átjáró állapota](#gateway-status) szakasz.
Verzió | Megjeleníti a logikai átjáró és az egyes átjáró-csomópontok verzióját. A logikai átjáró verziószáma a csoport csomópontjainak többsége alapján van meghatározva. Ha a logikai átjáró beállításában különböző verziójú csomópontok vannak, akkor csak a logikai átjáróval megegyező verziószámmal rendelkező csomópontok működnek. Mások korlátozott módban vannak, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés meghiúsul).
Igénybe vehető memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték a közel valós idejű pillanatkép.
Processzorhasználat | Egy átjáró-csomópont CPU-kihasználtsága. Ez az érték a közel valós idejű pillanatkép.
Hálózatkezelés (be/ki) | Átjáró-csomópontok hálózati kihasználtsága. Ez az érték a közel valós idejű pillanatkép.
Egyidejű feladatok (futó/korlát) | Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték a közel valós idejű pillanatkép. A korlát az egyes csomópontok maximális egyidejű feladatait jelzi. Ez az érték a gép méretétől függően van meghatározva. Az egyidejű feladatok végrehajtásának korlátját növelheti speciális forgatókönyvekben, ahol a CPU/memória/hálózat nincs használatban, a tevékenységek azonban időtúllépést okozhatnak. Ez a funkció egy egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség és a rendelkezésre állási funkció nincs engedélyezve).
Szerepkör | A több csomópontos átjárók és a feldolgozók két típusú szerepkört használnak. Minden csomópont munkavégző, ami azt jelenti, hogy mind a feladatok végrehajtásához használhatók. Csak egy kiosztó csomópont létezik, amely a feladatok/feladatok a Cloud servicesből való lekérésére, valamint a különböző munkavégző csomópontokra (beleértve a saját magára) történő küldésére szolgál.

Ezen a lapon néhány olyan beállítás látható, amely több értelmet mutat, ha két vagy több csomópont van (kibővíthető forgatókönyv) az átjárón. Lásd: [adatkezelés átjáró – magas rendelkezésre állás és méretezhetőség](data-factory-data-management-gateway-high-availability-scalability.md) a több csomópontos átjáró beállításával kapcsolatos részletekért.

### <a name="gateway-status"></a>Átjáró állapota
Az alábbi táblázat egy **átjáró-csomópont** lehetséges állapotát tartalmazza:

Állapot    | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | Data Factory szolgáltatáshoz csatlakoztatott csomópont.
Offline | A csomópont offline állapotban van.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolódási probléma miatt. A hiba oka lehet a 8050-es HTTP-port, a Service Bus-csatlakozási probléma vagy a hitelesítő adatok szinkronizálása.
Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban található.<br/><br/> Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz.

A következő táblázat a **logikai átjáró** lehetséges állapotait tartalmazza. Az átjáró állapota az átjáró csomópontjainak állapotától függ.

Állapot | Megjegyzések
:----- | :-------
Regisztráció szükséges | Ehhez a logikai átjáróhoz még nincs regisztrálva csomópont
Online | Az átjáró-csomópontok online állapotban vannak
Offline | Nincs online állapotú csomópont.
Korlátozott | Az átjáró nem minden csomópontja kifogástalan állapotban van. Ez az állapot arra figyelmeztet, hogy egyes csomópontok leállnak. <br/><br/>A hitelesítőadat-szinkronizálási probléma oka lehet a diszpécser/feldolgozó csomóponton.

## <a name="scale-up-gateway"></a>Átjáró vertikális felskálázása
Konfigurálhatja a csomóponton futtatható **egyidejű** adatáthelyezési feladatok számát úgy, hogy a helyszíni és a Felhőbeli adattárak közötti adatátviteli képességet bővítse.

Ha a rendelkezésre álló memória és a CPU nem jól működik, de az üresjárati kapacitás 0, akkor a csomóponton futtatható egyidejű feladatok számának növelésével növelje a skálázást. Érdemes lehet vertikális felskálázást végezni, ha a tevékenységek időtúllépés miatt megtörténik, mert az átjáró túlterhelt. Az átjárók csomópontjának speciális beállításaiban növelheti a csomópontok maximális kapacitását.

## <a name="troubleshooting-gateway-issues"></a>Átjáróval kapcsolatos problémák elhárítása
Az adatkezelési átjáró használatával kapcsolatos hibák elhárításával kapcsolatos információkért tekintse meg a [hibaelhárítási átjáróval kapcsolatos problémákat](data-factory-troubleshoot-gateway-issues.md) ismertető cikket.

## <a name="move-gateway-from-one-machine-to-another"></a>Átjáró áthelyezése egyik gépről a másikra
Ez a szakasz az átjáró-ügyfelek egyik gépről egy másik gépre való áthelyezésének lépéseit ismerteti.

1. A portálon navigáljon a **Data Factory kezdőlapjára**, és kattintson a **társított szolgáltatások** csempére.

    ![Adatátjárók hivatkozása](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Válassza ki az átjárót a **társított szolgáltatások** lap **adatátjárók** szakaszában.

    ![Társított szolgáltatások lap kijelölt átjáróval](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Az **adatátjáró** lapon kattintson a **letöltés és az adatátjáró telepítése** elemre.

    ![Átjáró hivatkozásának letöltése](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. A **configure (Konfigurálás** ) lapon kattintson az **adatátjáró letöltése és telepítése** lehetőségre, majd kövesse az utasításokat az adatátjáró számítógépen való telepítéséhez.

    ![Lap konfigurálása](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Tartsa meg a **Microsoft adatkezelés átjáró Configuration Manager** megnyitását.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. A portálon a **configure (Konfigurálás** ) lapon kattintson a parancssorban a **kulcs újbóli létrehozása** elemre, majd kattintson az **Igen** gombra a figyelmeztető üzenethez. Kattintson a **Másolás gombra** a kulcsot tartalmazó szöveg mellett, amely a vágólapra másolja a kulcsot. A régi gépen lévő átjáró működése leáll, amint újra létrehozza a kulcsot.

    ![2. kulcs újbóli létrehozása](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Illessze be a **kulcsot** a **adatkezelés átjáró Configuration Manager** a gépen lévő átjáró **regisztrálása** oldalán található szövegmezőbe. választható Kattintson az **átjáró kulcsának megjelenítése** jelölőnégyzetre a kulcs szövegének megtekintéséhez.

    ![Kulcs másolása és regisztrálása](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kattintson a **regisztráció** elemre az átjáró felhőalapú szolgáltatásban való regisztrálásához.
9. A **Beállítások** lapon kattintson a **módosítás** gombra a régi átjáróval használt tanúsítvány kiválasztásához, írja be a **jelszót**, majd kattintson a **Befejezés** gombra.

   ![Tanúsítvány meghatározása](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   A következő lépések végrehajtásával exportálhatja a tanúsítványt a régi átjáróról: indítsa el adatkezelés átjáró Configuration Manager a régi gépen, váltson a **tanúsítvány** lapra, kattintson az **Exportálás** gombra, és kövesse az utasításokat.
10. Az átjáró sikeres regisztrálását követően a **regisztrációhoz** **regisztrált** és **állapotra** vonatkozó beállítás jelenik meg, amely az átjáró Configuration Manager kezdőlapján **indul** .

## <a name="encrypting-credentials"></a>Hitelesítő adatok titkosítása
A hitelesítő adatok a Data Factory-szerkesztőben való titkosításához hajtsa végre a következő lépéseket:

1. Indítsa el a webböngészőt az **átjárót tartalmazó számítógépen**, és navigáljon [Azure Portal](https://portal.azure.com). Ha szükséges, keresse meg az adatelőállítót, nyissa meg az adatelőállítót az **adatfeldolgozó** lapon, majd kattintson a **Szerző & üzembe helyezés** elemre Data Factory szerkesztő elindításához.
2. Kattintson egy meglévő **társított szolgáltatásra** a fanézetben, és tekintse meg a JSON-definícióját, vagy hozzon létre egy olyan társított szolgáltatást, amelyhez adatkezelési átjáró szükséges (például: SQL Server vagy Oracle).
3. A JSON-szerkesztőben a **átjáró neve** tulajdonságnál adja meg az átjáró nevét.
4. Adja meg a **ConnectionString** nevű **adatforrás** -tulajdonság kiszolgálójának nevét.
5. Adja meg az adatbázis nevét a **ConnectionString** **kezdeti katalógus** tulajdonságához.
6. A parancssorban kattintson a **titkosítás** gombra, amely elindítja a Click-Once **Hitelesítőadat-kezelő** alkalmazást. Ekkor megjelenik a **hitelesítő adatok beállítása** párbeszédpanel.

    ![Hitelesítő adatok beállítása párbeszédpanel](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. A **hitelesítő adatok beállítása** párbeszédpanelen hajtsa végre a következő lépéseket:
   1. Válassza ki azt a **hitelesítést** , amelyet a Data Factory szolgáltatásnak használnia kell az adatbázishoz való kapcsolódáshoz.
   2. Adja meg annak a felhasználónak a nevét, aki hozzáfér az adatbázishoz a **Felhasználónév** beállításnál.
   3. Adja **meg a jelszó beállításhoz** tartozó felhasználó jelszavát.
   4. A hitelesítő adatok titkosításához és a párbeszédpanel bezárásához kattintson **az OK** gombra.
8. Ekkor egy **encryptedCredential** tulajdonságot kell megjelennie a **ConnectionString** -ben.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   Ha az átjárótól eltérő gépről fér hozzá a portálhoz, meg kell győződnie arról, hogy a hitelesítő adatok kezelője alkalmazás tud csatlakozni az átjáró számítógéphez. Ha az alkalmazás nem tudja elérni az átjárót, nem teszi lehetővé az adatforrás hitelesítő adatainak megadását és az adatforrás kapcsolatának tesztelését.

Ha a **hitelesítő adatok beállítása** alkalmazást használja, a portál titkosítja a hitelesítő adatokat az **átjáró** számítógépének Configuration Manager **tanúsítvány** lapján megadott tanúsítvánnyal.

Ha API-alapú megközelítést keres a hitelesítő adatok titkosításához, a [New-AzDataFactoryEncryptValue PowerShell-](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) parancsmagot használhatja a hitelesítő adatok titkosításához. A parancsmag azt a tanúsítványt használja, amelyet az átjáró a hitelesítő adatok titkosítására való használatra konfigurált. A rendszer titkosított hitelesítő adatokat ad hozzá a **EncryptedCredential** - **elemhez a JSON-ban** . A JSON-t a [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) parancsmaggal vagy a Data Factory-szerkesztőben használhatja.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

A hitelesítő adatoknak a Data Factory Editor használatával történő beállításának még egy megközelítése van. Ha SQL Server társított szolgáltatást hoz létre a szerkesztővel, és egyszerű szövegként adja meg a hitelesítő adatokat, a hitelesítő adatok titkosítva lesznek a Data Factory szolgáltatás tulajdonosa által használt tanúsítvánnyal. NEM használja azt a tanúsítványt, amelyet az átjáró a használatára konfigurált. Habár ez a megközelítés némileg gyorsabb lehet bizonyos esetekben, kevésbé biztonságos. Ezért javasoljuk, hogy ezt a megközelítést csak fejlesztési/tesztelési célokra kövesse.

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
Ez a szakasz azt ismerteti, hogyan lehet átjárót létrehozni és regisztrálni Azure PowerShell-parancsmagok használatával.

1. **Azure PowerShell** elindítása rendszergazdai módban.
2. Jelentkezzen be az Azure-fiókjába a következő parancs futtatásával és az Azure-beli hitelesítő adatok megadásával.

    ```powershell
    Connect-AzAccount
    ```
3. A **New-AzDataFactoryGateway** parancsmag használatával hozzon létre egy logikai átjárót a következő módon:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Példa parancs és kimenet**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. A Azure PowerShellban váltson a következő mappára: *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\*. Futtassa *RegisterGateway.ps1* a helyi változóhoz társított **$Key** az alábbi parancsban látható módon. Ez a parancsfájl regisztrálja a gépen a korábban létrehozott logikai átjáróval telepített ügyfél-ügynököt.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Az átjárót egy távoli gépen is regisztrálhatja a IsRegisterOnRemoteMachine paraméter használatával. Példa:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. A **Get-AzDataFactoryGateway** parancsmag használatával lekérheti az átjárók listáját az adatgyárban. Ha az **állapota online állapotú** , az azt jelenti, hogy az átjáró használatra kész. 

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Az átjárót a **Remove-AzDataFactoryGateway** parancsmaggal, a **set-AzDataFactoryGateway** parancsmagok használatával pedig az átjáró frissítésének leírásával távolíthatja el. A parancsmagok szintaxisát és egyéb részleteit lásd: Data Factory parancsmag referenciája.  

### <a name="list-gateways-using-powershell"></a>Átjárók listázása a PowerShell használatával

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Átjáró eltávolítása a PowerShell használatával

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Következő lépések
* Lásd: az [adatáthelyezés a helyszíni és a Felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) . Az útmutatóban egy olyan folyamatot hoz létre, amely az átjárót használja az adatok SQL Server adatbázisból egy Azure-blobba való áthelyezéséhez.