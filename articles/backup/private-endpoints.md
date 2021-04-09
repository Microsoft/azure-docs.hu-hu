---
title: Privát végpontok
description: Megtudhatja, hogyan hozhat létre privát végpontokat a Azure Backuphoz, és hogy a saját végpontok használata hogyan segít megőrizni az erőforrások biztonságát.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 1775ec2c337dba0a618f9e7d186af9ed11a0e303
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559383"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup magánhálózati végpontok

Azure Backup lehetővé teszi az adatok biztonságos biztonsági mentését és visszaállítását a Recovery Services-tárolóból [privát végpontok](../private-link/private-endpoint-overview.md)használatával. A privát végpontok egy vagy több magánhálózati IP-címet használnak a VNet, és így hatékonyan helyezik üzembe a szolgáltatást a VNet.

Ez a cikk segít megérteni a Azure Backup magánhálózati végpontok létrehozásának folyamatát, valamint azokat a forgatókönyveket, amelyekben a privát végpontok használata hozzájárul az erőforrások biztonságának fenntartásához.

## <a name="before-you-start"></a>Előkészületek

- A magánhálózati végpontok csak az új Recovery Services-tárolók számára hozhatók létre (amelyek nem rendelkeznek a tárolóhoz regisztrált elemekkel). Ezért létre kell hozni a privát végpontokat, mielőtt a tár bármely elemét védetté tenné.
- Egy virtuális hálózat több Recovery Services-tárolóhoz is tartalmazhat privát végpontokat. Emellett egy Recovery Services-tár több virtuális hálózatban is rendelkezhet saját végpontokkal. Azonban a tárolóhoz létrehozható privát végpontok maximális száma 12.
- Miután létrehozta a tárolóhoz egy privát végpontot, a rendszer zárolja a tárolót. Nem lesz elérhető (biztonsági mentéshez és visszaállításhoz) a hálózatokon kívülről is, amelyek a tárolóhoz privát végpontot tartalmaznak. Ha a tár összes privát végpontja törlődik, a tároló minden hálózatról elérhető lesz.
- A biztonsági mentéshez használt privát végponti kapcsolatok összesen 11 magánhálózati IP-címet használnak az alhálózaton, beleértve a Azure Backup által a Storage szolgáltatáshoz használt. Az egyes Azure-régiók esetében ez a szám magasabb (akár 25) is lehet. Ezért javasoljuk, hogy elegendő privát IP-cím álljon rendelkezésre, ha privát végpontokat próbál létrehozni a biztonsági mentéshez.
- A Recovery Services-tárolót (mindkettő) Azure Backup és Azure Site Recovery is használja, ez a cikk a privát végpontok használatát ismerteti csak Azure Backup esetén.
- Azure Active Directory jelenleg nem támogatja a privát végpontokat. A Azure Active Directory működéséhez szükséges IP-címeket és teljes tartományneveket engedélyezni kell a biztonságos hálózatról, amikor az Azure-beli virtuális gépeken található adatbázisok biztonsági mentését hajtja végre, és a MARS-ügynök használatával készít biztonsági mentést. NSG-címkéket és Azure Firewall címkéket is használhat az Azure AD-hez való hozzáférés engedélyezéséhez.
- A hálózati házirendekkel rendelkező virtuális hálózatok magánhálózati végpontok esetén nem támogatottak. A folytatás előtt [le kell tiltania a hálózati házirendeket](../private-link/disable-private-endpoint-network-policy.md) .
- A Recovery Services erőforrás-szolgáltatót újra regisztrálnia kell az előfizetéssel, ha azt a 1 2020. május előtt regisztrálta. A szolgáltató újbóli regisztrálásához nyissa meg az előfizetését a Azure Portalban, navigáljon az **erőforrás-szolgáltatóhoz** a bal oldali navigációs sávon, majd válassza a **Microsoft. recoveryservices szolgáltatónál** elemet, és válassza az **ismételt regisztráció** lehetőséget.
- Az SQL-és SAP HANA-adatbázisok [régiók közötti visszaállítása](backup-create-rs-vault.md#set-cross-region-restore) nem támogatott, ha a tárolón engedélyezve vannak a privát végpontok.
- Ha egy Recovery Services-tárolót egy új bérlőhöz már használ privát végpontokkal, akkor frissítenie kell a Recovery Services-tárolót a tár felügyelt identitásának újbóli létrehozásához és újrakonfigurálásához, és szükség szerint létre kell hoznia egy új privát végpontot (amelynek az új Bérlőnek kell lennie). Ha ez nem történik meg, a biztonsági mentési és visszaállítási műveletek sikertelenek lesznek. Az előfizetésen belül beállított szerepköralapú hozzáférés-vezérlési (RBAC) engedélyeket is újra kell konfigurálni.

## <a name="recommended-and-supported-scenarios"></a>Ajánlott és támogatott forgatókönyvek

Habár a privát végpontok engedélyezve vannak a tárolóhoz, az SQL-és SAP HANA-munkaterhelések biztonsági mentésére és helyreállítására szolgálnak az Azure-beli virtuális gépeken és a MARS-ügynök biztonsági mentésében. A tárolót más számítási feladatok biztonsági mentéséhez is használhatja (a saját végpontokat azonban nem igénylik). Az SQL és a SAP HANA számítási feladatainak és a MARS-ügynök használatával történő biztonsági mentésének kiegészítéseként az Azure-beli virtuális gépek biztonsági mentésére szolgáló fájlok helyreállítására is használhatók a privát végpontok. További információkért tekintse meg a következő táblázatot:

| Munkaterhelések biztonsági mentése az Azure-beli virtuális gépen (SQL, SAP HANA), biztonsági mentés a MARS-ügynök használatával | A privát végpontok használata ajánlott a biztonsági mentéshez és a visszaállításhoz anélkül, hogy hozzá kellene adni egy engedélyezési listához a virtuális hálózatok Azure Backup vagy az Azure Storage összes IP-címe/teljes tartománynevét. Ebben az esetben győződjön meg arról, hogy az SQL Database-t futtató virtuális gépek hozzáférhetnek az Azure AD IP-címeihez vagy teljes tartománynevéhez. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure-beli virtuális gép biztonsági mentése**                                         | A virtuális gép biztonsági mentése nem igényli, hogy bármely IP-cím vagy teljes tartománynév számára engedélyezze a hozzáférést. Így nincs szükség privát végpontokra a lemezek biztonsági mentéséhez és visszaállításához.  <br><br>   A privát végpontokat tartalmazó tárolóból származó fájlok helyreállítása azonban olyan virtuális hálózatokra korlátozódik, amelyek a tárolóhoz privát végpontot tartalmaznak. <br><br>    A ACL'ed nem felügyelt lemezek használata esetén győződjön meg arról, hogy a lemezeket tartalmazó Storage-fiók lehetővé teszi a **megbízható Microsoft-szolgáltatások** elérését, ha az ACL'ed. |
| **Azure Files biztonsági mentés**                                      | Azure Files biztonsági mentéseket a helyi Storage-fiók tárolja. Így nincs szükség privát végpontokra a biztonsági mentéshez és a visszaállításhoz. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Ismerkedés a privát végpontok létrehozásával a biztonsági mentéshez

A következő részekben ismertetjük a virtuális hálózatokon belüli Azure Backup magánhálózati végpontok létrehozásának és használatának lépéseit.

>[!IMPORTANT]
> Javasoljuk, hogy kövesse az ebben a dokumentumban említett lépéseket. Ennek elmulasztása miatt előfordulhat, hogy a tár nem kompatibilis a privát végpontok használatára, és egy új tárolóval újra kell indítania a folyamatot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A biztonsági mentéshez használható privát végpontok csak olyan Recovery Services-tárolók számára hozhatók létre, amelyeknek nincs védett eleme (vagy nem volt olyan elem, amely korábban nem volt védett vagy regisztrált). Ezért javasoljuk, hogy hozzon létre egy új tárolót a kezdéshez. További információ az új tárolók létrehozásáról:  [Recovery Services-tároló létrehozása és konfigurálása](backup-create-rs-vault.md).

[Ebből a szakaszból](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) megtudhatja, hogyan hozhat létre tárolót az Azure Resource Manager-ügyfél használatával. Ezzel létrehoz egy tárolót, amelynek felügyelt identitása már engedélyezve van.

## <a name="enable-managed-identity-for-your-vault"></a>Felügyelt identitás engedélyezése a tárolóhoz

A felügyelt identitások lehetővé teszik, hogy a tároló privát végpontokat hozzon létre és használjon. Ez a szakasz a tár felügyelt identitásának engedélyezését tárgyalja.

1. Nyissa meg a Recovery Services > **identitását**.

    ![Identitás állapotának módosítása a következőre](./media/private-endpoints/identity-status-on.png)

1. Módosítsa az **állapotot** **be értékre, majd** válassza a **Mentés** lehetőséget.

1. A rendszer létrehoz egy **objektumazonosító-azonosítót** , amely a tár felügyelt identitása.

    >[!NOTE]
    >Ha engedélyezve van, a felügyelt identitás **nem** tiltható le (akár átmenetileg is). A felügyelt identitás letiltása inkonzisztens viselkedést eredményezhet.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Engedélyek megadása a tárolónak a szükséges privát végpontok létrehozásához

A Azure Backuphoz szükséges privát végpontok létrehozásához a tárolónak (a tár felügyelt identitásának) a következő erőforráscsoportok engedélyekkel kell rendelkeznie:

- A célként megadott VNet tartalmazó erőforráscsoport
- Az erőforráscsoport, amelyben létre kell hozni a privát végpontokat
- Az saját DNS zónákat tartalmazó erőforráscsoport, ahogy az [itt](#create-private-endpoints-for-azure-backup) részletesen szerepel

Javasoljuk, hogy adja meg a **közreműködői** szerepkört a három erőforráscsoport számára a tárolóhoz (felügyelt identitás). A következő lépések azt írják le, hogyan kell ezt megtenni egy adott erőforráscsoport esetében (ezt a három erőforráscsoporthoz kell elvégezni):

1. Nyissa meg az erőforráscsoportot, és navigáljon a **Access Control (iam)** elemre a bal oldali sávon.
1. Ha **Access Control**, lépjen a **szerepkör-hozzárendelés hozzáadása** elemre.

    ![Szerepkör-hozzárendelés hozzáadása](./media/private-endpoints/add-role-assignment.png)

1. A **szerepkör-hozzárendelés hozzáadása** panelen válassza a **közreműködő** **szerepkört**, és használja a tároló **nevét** a **rendszerbiztonsági tag** néven. Válassza ki a tárolót, és kattintson a **Mentés** gombra, ha elkészült.

    ![Szerepkör és rendszerbiztonsági tag kiválasztása](./media/private-endpoints/choose-role-and-principal.png)

Az engedélyek részletesebb kezeléséhez tekintse meg a [szerepkörök és engedélyek manuális létrehozása](#create-roles-and-permissions-manually)című témakört.

## <a name="create-private-endpoints-for-azure-backup"></a>Privát végpontok létrehozása a Azure Backuphoz

Ez a szakasz azt ismerteti, hogyan hozhat létre privát végpontot a tárolóhoz.

1. Navigáljon a fent létrehozott tárolóhoz, és nyissa meg a bal oldali navigációs sávon a **privát végponti kapcsolatok** csomópontot. A felső részen válassza a **+ privát végpont** lehetőséget, hogy megkezdje a tár új privát végpontjának létrehozását.

    ![Új privát végpont létrehozása](./media/private-endpoints/new-private-endpoint.png)

1. Ha a **privát végpont létrehozása** folyamatban van, meg kell adnia a privát végponti kapcsolat létrehozásához szükséges adatokat.
  
    1. **Alapismeretek**: adja meg a privát végpontok alapszintű részleteit. A régiónak meg kell egyeznie a tárolóval és a biztonsági mentés alatt álló erőforrással.

        ![Adja meg az alapszintű részleteket](./media/private-endpoints/basics-tab.png)

    1. **Erőforrás**: ezen a lapon ki kell választania azt a Pásti-erőforrást, amelyhez létre kívánja hozni a kapcsolatát. Válassza ki a **Microsoft. recoveryservices szolgáltatónál/Vaults** elemet a kívánt előfizetés erőforrástípus alapján. Ha elkészült, válassza ki a Recovery Services-tároló nevét az **erőforrás** -és **AzureBackup** célként megadott **alerőforrásként**.

        ![Válassza ki a kapcsolatok erőforrását](./media/private-endpoints/resource-tab.png)

    1. **Konfiguráció**: a konfiguráció területen válassza ki azt a virtuális hálózatot és alhálózatot, ahol létre szeretné hozni a privát végpontot. Ez lesz a vnet, ahol a virtuális gép megtalálható.

        A magánjellegű kapcsolódáshoz szükséges DNS-rekordokra van szükség. A hálózat beállítása alapján a következők közül választhat:

          - Privát végpont integrálása privát DNS-zónával: válassza az **Igen** lehetőséget, ha integrálni szeretné.
          - Egyéni DNS-kiszolgáló használata: válassza a **nem** lehetőséget, ha saját DNS-kiszolgálót szeretne használni.

        Ezeket a DNS-rekordokat a következő [módon](#manage-dns-records)kell kezelni.

          ![A virtuális hálózat és az alhálózat meghatározása](./media/private-endpoints/configuration-tab.png)

    1. Igény szerint hozzáadhat **címkéket** a privát végponthoz.
    1. Folytassa a **felülvizsgálat + létrehozás** után a részletek beírásával. Ha az ellenőrzés befejeződött, válassza a **Létrehozás** lehetőséget a privát végpont létrehozásához.

## <a name="approve-private-endpoints"></a>Privát végpontok jóváhagyása

Ha a privát végpontot létrehozó felhasználó egyben a Recovery Services-tároló tulajdonosa is, a fent létrehozott privát végpont automatikusan jóvá lesz hagyva. Ellenkező esetben a tároló tulajdonosának jóvá kell hagynia a privát végpontot, mielőtt használni tudná. Ez a szakasz a privát végpontok manuális jóváhagyását ismerteti a Azure Portalon keresztül.

Lásd: [privát végpontok manuális jóváhagyása a Azure Resource Manager ügyfél használatával](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) a Azure Resource Manager ügyfél használatára a privát végpontok jóváhagyásához.

1. A Recovery Services-tárolóban navigáljon a bal oldali sávban található **privát végponti kapcsolatokhoz** .
1. Válassza ki a jóváhagyni kívánt privát végpont-kapcsolatokat.
1. Válassza a **jóváhagyás** lehetőséget a felső sávon. Az **elutasítás** vagy az **Eltávolítás** lehetőséget is választhatja, ha el kívánja utasítani vagy törölni szeretné a végponti kapcsolatokat.

    ![Privát végpontok jóváhagyása](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>DNS-rekordok kezelése

A korábban leírtaknak megfelelően a saját DNS-zónákban vagy-kiszolgálókon a kötelező DNS-rekordokra van szükség a magánhálózati kapcsolódáshoz. A magánhálózati végpontokat közvetlenül az Azure magánhálózati DNS-zónákkal integrálhatja, vagy az egyéni DNS-kiszolgálókat használhatja a hálózati beállítások alapján. Ezt mindhárom szolgáltatás esetében el kell végezni: a biztonsági mentést, a blobokat és a várólistákat.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Privát végpontok Azure-beli magánhálózati DNS-zónákkal való integrálásakor

Ha úgy dönt, hogy a magánhálózati végpontot privát DNS-zónákkal integrálja, a biztonsági mentés hozzáadja a szükséges DNS-rekordokat. Megtekintheti a privát végpont **DNS-konfigurációja** alatt használt magánhálózati DNS-zónákat. Ha ezek a DNS-zónák nem találhatók, automatikusan létrejönnek a magánhálózati végpont létrehozásakor. Azt azonban ellenőriznie kell, hogy a virtuális hálózat (amely tartalmazza a biztonsági mentéshez szükséges erőforrásokat) megfelelően van-e csatolva mindhárom saját DNS-zónához az alább leírtak szerint.

![DNS-konfiguráció az Azure Private DNS-zónában](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Virtuális hálózati kapcsolatok ellenőrzése a magánhálózati DNS-zónákban

A fent felsorolt **privát DNS-** zónák (biztonsági mentés, blobok és várólisták) esetében tegye a következőket:

1. Navigáljon a megfelelő **virtuális hálózati kapcsolatok** lehetőségre a bal oldali navigációs sávon.
1. Látnia kell egy bejegyzést ahhoz a virtuális hálózathoz, amelyhez létrehozta a privát végpontot, például az alább látható módon:

    ![Virtuális hálózat magánhálózati végponthoz](./media/private-endpoints/virtual-network-links.png)

1. Ha nem lát bejegyzést, adjon hozzá egy virtuális hálózati hivatkozást az összes olyan DNS-zónához, amely nem rendelkezik velük.

    ![Virtuális hálózati kapcsolat hozzáadása](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>Egyéni DNS-kiszolgáló vagy-gazdagépek használatakor

Ha egyéni DNS-kiszolgálókat használ, létre kell hoznia a szükséges DNS-zónákat, és hozzá kell adnia a magánhálózati végpontok által a DNS-kiszolgálókhoz szükséges DNS-rekordokat. A blobok és a várólisták esetében feltételes továbbítókat is használhat.

#### <a name="for-the-backup-service"></a>A Backup szolgáltatáshoz

1. A DNS-kiszolgálón hozzon létre egy DNS-zónát a biztonsági mentéshez a következő elnevezési konvenciónak megfelelően:

    |Zóna |Szolgáltatás |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > A fenti szövegben `<geo>` a régiókódra (például *EUs* és *ne* az USA keleti régiója, illetve Észak-Európára) hivatkozik. Tekintse át a következő felsorolásokat a régiók kódjaihoz:
    >
    > - [Minden nyilvános felhő](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Kína](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Németország](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. Ezután hozzá kell adnia a szükséges DNS-rekordokat. A biztonsági mentési DNS-zónához hozzáadandó rekordok megtekintéséhez navigáljon a fent létrehozott privát végponthoz, és lépjen a bal oldali navigációs sávon a **DNS-konfiguráció** lehetőségre.

    ![DNS-konfiguráció egyéni DNS-kiszolgálóhoz](./media/private-endpoints/custom-dns-configuration.png)

1. A biztonsági mentéshez adjon hozzá egy bejegyzést a DNS-zónában Type Records formában megjelenő teljes tartománynévhez és IP-címhez. Ha a névfeloldáshoz egy gazda fájlt használ, a megfelelő bejegyzéseket minden IP-cím és FQDN számára a következő formátum szerint végezze el:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Ahogy az a fenti képernyőképen is látható, a teljes tartománynevek láthatók `xxxxxxxx.<geo>.backup.windowsazure.com` és nem `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . Ilyen esetekben ügyeljen `.privatelink.` arra, hogy a megadott formátumnak megfelelően belefoglalja (és ha szükséges, adja hozzá) a következőt:.

#### <a name="for-blob-and-queue-services"></a>BLOB-és üzenetsor-szolgáltatásokhoz

A blobok és a várólisták esetében használhatja a feltételes továbbítókat, vagy létrehozhat DNS-zónákat a DNS-kiszolgálón.

##### <a name="if-using-conditional-forwarders"></a>Feltételes továbbítók használata esetén

Ha feltételes továbbítókat használ, a következők szerint adja hozzá a továbbítókat a blob és a várólista teljes tartománynevéhez:

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Saját DNS-zónák használata esetén

Ha a blobokhoz és a várólistákhoz DNS-zónákat használ, először létre kell hoznia ezeket a DNS-zónákat, és később hozzá kell adnia a szükséges rekordokat.

|Zóna |Szolgáltatás  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | Üzenetsor        |

Ebben a pillanatban csak a blobok és a várólisták zónáit hozunk létre egyéni DNS-kiszolgálók használata esetén. A DNS-rekordok hozzáadására később kerül sor a következő két lépésben:

1. Amikor regisztrálja az első biztonsági mentési példányt, azaz amikor először konfigurálja a biztonsági mentést
1. Az első biztonsági mentés futtatásakor

Ezeket a lépéseket a következő fejezetekben hajtjuk végre.

## <a name="use-private-endpoints-for-backup"></a>Privát végpontok használata biztonsági mentéshez

Miután jóváhagyta a VNet-tárolóhoz létrehozott privát végpontokat, megkezdheti a biztonsági mentések és a visszaállítások elvégzését.

>[!IMPORTANT]
>A folytatás előtt győződjön meg arról, hogy sikeresen elvégezte az összes fent említett lépést a dokumentumban. A betöltéshez el kell végeznie az alábbi ellenőrzőlista lépéseit:
>
>1. Létrehozott egy (új) Recovery Services-tárolót
>2. A tár engedélyezése a rendszerhez rendelt felügyelt identitás használatára
>3. A tár felügyelt identitásához hozzárendelt megfelelő engedélyek
>4. Létrehozott egy privát végpontot a tárolóhoz
>5. Jóváhagyta a privát végpontot (ha nem engedélyezett automatikusan)
>6. Gondoskodjon arról, hogy minden DNS-rekord megfelelően legyen hozzáadva (kivéve az egyéni kiszolgálók blob-és üzenetsor-rekordjait, amelyek a következő szakaszokban lesznek tárgyalva)

### <a name="check-vm-connectivity"></a>Virtuális gép kapcsolatának ellenőrzése

A zárolt hálózatban lévő virtuális gépen ügyeljen a következőkre:

1. A virtuális gépnek hozzáféréssel kell rendelkeznie a HRE.
2. A kapcsolat biztosítása érdekében hajtsa végre az **nslookup** alkalmazást a virtuális gépről a Backup URL-címére ( `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ). Ennek a virtuális hálózatban hozzárendelt magánhálózati IP-címet kell visszaadnia.

### <a name="configure-backup"></a>Biztonsági mentés konfigurálása

Ha a fenti ellenőrzőlista és a hozzáférés sikeres befejezését biztosítja, továbbra is konfigurálhatja a munkaterhelések biztonsági mentését a tárba. Ha egyéni DNS-kiszolgálót használ, hozzá kell adnia a blobok és a várólisták számára az első biztonsági mentés konfigurálása után elérhető DNS-bejegyzéseket.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>A blobok és a várólisták DNS-rekordjai (csak az egyéni DNS-kiszolgálók vagy-gazdagépek fájljai esetében) az első regisztráció után

Miután konfigurálta a biztonsági mentést legalább egy erőforráshoz egy privát végponton engedélyezett tárolón, adja hozzá a szükséges DNS-rekordokat a blobokhoz és a várólistákhoz az alább leírtak szerint.

1. Keresse meg az erőforráscsoportot, és keresse meg a létrehozott privát végpontot.
1. Az Ön által megadott privát végpont nevétől eltekintve két több privát végpont jön létre. Ezek a és a és a `<the name of the private endpoint>_ecs` utótaggal kezdődnek `_blob` `_queue` .

    ![Privát végpont erőforrásai](./media/private-endpoints/private-endpoint-resources.png)

1. Navigáljon az egyes privát végpontokhoz. A két privát végpont DNS-konfigurációs beállításában egy, a és egy FQDN és egy IP-címmel rendelkező rekord jelenik meg. Adja hozzá mindkettőt az egyéni DNS-kiszolgálóhoz a korábban leírt módon felül.
Ha egy gazdagépet használ, a következő formátum szerint végezze el a megfelelő bejegyzéseket a gazdagép fájljában minden IP-cím/FQDN számára:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![BLOB DNS-konfigurációja](./media/private-endpoints/blob-dns-configuration.png)

A fentiek mellett további bejegyzésre is szükség van az első biztonsági mentés után, amelyet [később](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)ismertetünk.

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Munkaterhelések biztonsági mentése és visszaállítása az Azure-beli virtuális gépen (SQL és SAP HANA)

Miután létrehozta és jóváhagyta a privát végpontot, a privát végpont használatához nincs szükség további módosításokra (kivéve, ha SQL rendelkezésre állási csoportokat használ, és ezt a szakasz későbbi részében tárgyaljuk). A biztonságos hálózatról a tárolóra irányuló összes kommunikációt és adatátvitelt a privát végponton keresztül hajtja végre a rendszer. Ha azonban egy kiszolgáló (SQL vagy SAP HANA) regisztrálása után eltávolít egy privát végpontot a tárolóhoz, akkor újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>A Blobok DNS-rekordjai (csak az egyéni DNS-kiszolgálók vagy-gazdagépek fájljai esetében) az első biztonsági mentés után

Miután futtatta az első biztonsági mentést, és egyéni DNS-kiszolgálót használ (feltételes továbbítás nélkül), valószínű, hogy a biztonsági mentés sikertelen lesz. Ha ez történik:

1. Keresse meg az erőforráscsoportot, és keresse meg a létrehozott privát végpontot.
1. A korábban tárgyalt három privát végponton kívül egy negyedik privát végpontot fog látni, amely a nevével kezdődik, `<the name of the private endpoint>_prot` és a utótagja a következő: `_blob` .

    ![Privát endpoing "Prot" utótaggal](./media/private-endpoints/private-endpoint-prot.png)

1. Navigáljon ehhez az új privát végponthoz. A DNS-konfiguráció lehetőségnél egy teljes tartománynevet és egy IP-címet tartalmazó rekordot fog látni. Adja hozzá ezeket a saját DNS-kiszolgálójához a korábban ismertetett módon felül.

    Ha egy gazdagépet használ, a következő formátum szerint végezze el a megfelelő bejegyzéseket az egyes IP-címekhez és FQDN-fájlokhoz:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>Ezen a ponton képesnek kell lennie az **nslookup** futtatására a virtuális gépről, és a privát IP-címekre kell feloldania, amikor a tár biztonsági mentési és tárolási URL-címein végzett.

### <a name="when-using-sql-availability-groups"></a>SQL rendelkezésre állási csoportok használatakor

Az SQL rendelkezésre állási csoportok (AG) használatakor a feltételes továbbítást az alábbi módon kell kiépíteni az egyéni AG DNS-ben:

1. Jelentkezzen be a tartományvezérlőre.
1. A DNS-alkalmazásban adja hozzá a feltételes továbbítókat mindhárom DNS-zónához (biztonsági mentéshez, Blobokhoz és várólistákhoz) a gazdagép IP-168.63.129.16 vagy az egyéni DNS-kiszolgáló IP-címéhez, szükség szerint. Az alábbi képernyőképek az Azure Host IP-címére való továbbításkor jelennek meg. Ha saját DNS-kiszolgálót használ, cserélje le a címet a DNS-kiszolgálójának IP-címére.

    ![Feltételes továbbítók a DNS-kezelőben](./media/private-endpoints/dns-manager.png)

    ![Új feltételes továbbító](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Biztonsági mentés és visszaállítás a MARS-ügynökön keresztül

Ha a MARS-ügynököt használja a helyszíni erőforrások biztonsági mentésére, győződjön meg arról, hogy a helyszíni hálózat (a biztonsági mentéshez szükséges erőforrásokat tartalmazza) az Azure-VNet van társítva, amely a tárolóhoz saját végpontot tartalmaz, így használhatja azt. Ezután továbbra is telepítheti a MARS-ügynököt, és konfigurálhatja a biztonsági mentést az itt leírtak szerint. Azonban gondoskodnia kell arról, hogy a biztonsági mentés minden kommunikációja csak a csak a hálózaton keresztül történjen.

Ha azonban a MARS-ügynök regisztrálása után eltávolítja a tárolóhoz tartozó magánhálózati végpontokat, újra regisztrálnia kell a tárolót a tárolóban. Nem kell leállítania a védelmet.

## <a name="deleting-private-endpoints"></a>Privát végpontok törlése

[Ebből a szakaszból](/rest/api/virtualnetwork/privateendpoints/delete) megtudhatja, hogyan törölhet privát végpontokat.

## <a name="additional-topics"></a>További témakörök

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Recovery Services-tároló létrehozása az Azure Resource Manager ügyfél használatával

Létrehozhatja a Recovery Services-tárolót, és engedélyezheti a felügyelt identitását (a felügyelt identitás engedélyezése szükséges, ahogy azt később látni fogjuk) a Azure Resource Manager-ügyfél használatával. Ehhez az alábbi módon kell megosztva egy mintát:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

A fenti JSON-fájlnak a következő tartalommal kell rendelkeznie:

Kérelem JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Válasz JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>A példában a Azure Resource Manager ügyfélen létrehozott tár már létre van hozva egy rendszer által hozzárendelt felügyelt identitással.

### <a name="managing-permissions-on-resource-groups"></a>Erőforráscsoportok engedélyeinek kezelése

A tár felügyelt identitásának a következő engedélyekkel kell rendelkeznie az erőforráscsoport és a virtuális hálózat között, ahol a magánhálózati végpontok létre lesznek hozva:

- `Microsoft.Network/privateEndpoints/*` Ez szükséges ahhoz, hogy a szifilisz az erőforráscsoport privát végpontján legyen végrehajtva. Hozzá kell rendelni az erőforráscsoporthoz.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Erre azért van szükség, mert a virtuális hálózaton a magánhálózati IP-cím csatlakozik a privát végponthoz.
- `Microsoft.Network/networkInterfaces/read` Erre azért van szükség az erőforráscsoporthoz, hogy megkapja a magánhálózati végponthoz létrehozott hálózati adaptert.
- Saját DNS zóna közreműködői szerepköre ez a szerepkör már létezik, és használható a biztosításához és az engedélyek megadásához `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

A szükséges engedélyekkel rendelkező szerepkörök létrehozásához az alábbi módszerek egyikét használhatja:

#### <a name="create-roles-and-permissions-manually"></a>Szerepkörök és engedélyek manuális létrehozása

Hozza létre a következő JSON-fájlokat, és használja a szakasz végén található PowerShell-parancsot a szerepkörök létrehozásához:

PrivateEndpointContributorRoleDef.jsbekapcsolva

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsbekapcsolva

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsbekapcsolva

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Parancsfájl használata

1. Indítsa el a **Cloud Shell** a Azure Portalban, és válassza a **fájl feltöltése** lehetőséget a PowerShell ablakban.

    ![Válassza a fájl feltöltése a PowerShell-ablakban lehetőséget.](./media/private-endpoints/upload-file-in-powershell.png)

1. Töltse fel a következő szkriptet: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Nyissa meg a saját mappáját (például: `cd /home/user` )

1. Futtassa a következő parancsfájlt:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Ezek a paraméterek:

    - **előfizetés**: * * olyan SubscriptionId, amelyhez a tárolóhoz tartozó magánhálózati végpontot kell létrehozni, valamint azt az alhálózatot, ahol a tár magánhálózati végpontja csatolva lesz

    - **vaultPEResourceGroup**: az erőforráscsoport, amelyben létrejön a tár magánhálózati végpontja

    - **vaultPESubnetResourceGroup**: annak az alhálózatnak az erőforráscsoport, amelyhez a magánhálózati végpont csatlakozni fog

    - **vaultMsiName**: a tár MSI-fájljának neve, amely ugyanaz, mint a **VaultName**

1. Végezze el a hitelesítést, és a szkript a fent megadott előfizetés kontextusát fogja elvégezni. Létrehozza a megfelelő szerepköröket, ha hiányoznak a bérlőből, és hozzárendeli a szerepköröket a tároló MSI-hez.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Privát végpontok létrehozása Azure PowerShell használatával

#### <a name="auto-approved-private-endpoints"></a>Automatikusan jóváhagyott privát végpontok

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Privát végpontok manuális jóváhagyása az Azure Resource Manager ügyfél használatával

1. A **GetVault** használatával szerezze be a privát végponthoz tartozó MAGÁNHÁLÓZATI kapcsolatok azonosítóját.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Ez visszaküldi a magánhálózati végponti kapcsolatok AZONOSÍTÓját. A kapcsolat neve a következő módon kérhető le a kapcsolati azonosító első részének használatával:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Kérje le a **privát végponti kapcsolat azonosítóját** (és a **magánhálózati végpont nevét**, ahol szükséges) a válaszból, és cserélje le a következő JSON-és Azure Resource Manager URI-ra, majd próbálja meg módosítani az állapotot "jóváhagyott/visszautasított/leválasztott" értékre, ahogyan az alábbi példában látható:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. Létrehozhatok privát végpontot egy meglévő Backup-tárolóhoz?<br>
A. Nem, a saját végpontok csak az új biztonsági mentési tárolók számára hozhatók létre. Így a tár nem rendelkezhetett olyan elemmel, amely védett volt. Valójában a privát végpontok létrehozása előtt nem lehet a tárolóhoz tartozó elemeket védelemmel ellátni.

K. Megpróbáltam védelemmel ellátni egy elemet a tárolóban, de nem sikerült, és a tár továbbra sem tartalmaz védett elemeket. Létrehozhatok privát végpontokat ehhez a tárolóhoz?<br>
A. Nem, a tár nem rendelkezhet semmilyen, a múltbeli elemek elleni védelemmel kapcsolatos kísérlettel.

K. Olyan tárolóval rendelkezem, amely privát végpontokat használ a biztonsági mentéshez és a visszaállításhoz. Később is Hozzáadhatok vagy eltávolíthatok privát végpontokat ehhez a tárolóhoz, még akkor is, ha a biztonsági másolati elemek védettek?<br>
A. Igen. Ha már létrehozott privát végpontokat egy tárolóhoz, és védett biztonsági másolati elemeket hoz létre, akkor később szükség szerint hozzáadhat vagy eltávolíthat privát végpontokat.

K. Használható-e a Azure Backup privát végpontja a Azure Site Recoveryhoz is?<br>
A. Nem, a biztonsági mentéshez használt magánhálózati végpont csak Azure Backup használható. Ha a szolgáltatás támogatja, létre kell hoznia egy új privát végpontot a Azure Site Recoveryhoz.

K. Kihagytam a cikkben szereplő lépések egyikét, és folytattam az adatforrások védelme érdekében. Továbbra is használhatok privát végpontokat?<br>
A. Nem követi a cikkben szereplő lépéseket, és az elemek védelemmel való ellátása azt eredményezheti, hogy a tár nem tudja használni a privát végpontokat. Ezért javasoljuk, hogy tekintse meg ezt az ellenőrzőlistát, mielőtt továbblép az elemek védelemmel.

K. Használhatom a saját DNS-kiszolgálót az Azure saját DNS-zóna vagy egy integrált magánhálózati DNS-zóna használata helyett?<br>
A. Igen, használhatja a saját DNS-kiszolgálóit. Azonban győződjön meg arról, hogy az összes szükséges DNS-rekord hozzá van adva az ebben a részben javasolt módon.

K. Kell-e további lépéseket végrehajtani a kiszolgálón, miután követtem a jelen cikkben leírt eljárást?<br>
A. A cikkben részletezett folyamat után nem kell további munkát végeznie a privát végpontok használatához a biztonsági mentéshez és a visszaállításhoz.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [Azure Backup összes biztonsági szolgáltatását](security-overview.md).