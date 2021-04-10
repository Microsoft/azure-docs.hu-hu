---
title: Azure arc-kompatibilis adatszolgáltatások – kibocsátási megjegyzések
description: Legújabb kibocsátási megjegyzések
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: conceptual
ms.openlocfilehash: 2f41034331ed21e194fc2b86c2902c5957333313
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010598"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Kibocsátási megjegyzések – az Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)

Ez a cikk a közelmúltban kiadott vagy továbbfejlesztett Azure arc-kompatibilis adatszolgáltatások képességeit, funkcióit és fejlesztéseit emeli ki. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Március 2021

A márciusi 2021 kiadás a 2021. április 6-án lett bevezetve.

Tekintse át a jelen kiadás korlátozásait az [ismert problémákkal – Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](known-issues.md).

Az Azure-beli adatcli ( `azdata` ) verziószáma: 20.3.2. Telepítheti `azdata` Az Azure-beli [adatok parancssori felületét ( `azdata` )](/sql/azdata/install/deploy-install-azdata)is.

### <a name="data-controller"></a>Adatkezelő

- Az Azure arc-kompatibilis adatkezelési adatkezelő üzembe helyezése közvetlen csatlakozási módban a portálról. Indítás az [adatvezérlő üzembe helyezése – közvetlen csatlakozási mód – előfeltételek](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású

A PostgreSQL-hez készült egyéni erőforrás-definíciók (CRD) egyetlen CRD-ben lettek összevonva. Lásd az alábbi táblázatot.

|Kiadás |CRD |
|-----|-----|
|Február 2021 és korábbi verziók| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Március 2021-én kezdődően | postgresqls.arcdata.microsoft.com

Az előző CRDs a korábbi telepítések törlése után törölheti. Lásd: [a korábbi telepítések tisztítása](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-managed-instance"></a>Azure arc-kompatibilis felügyelt példány

- Mostantól 3 replikával állíthatja vissza az adatbázist a felügyelt SQL-példányra, és a rendszer automatikusan hozzáadja a rendelkezésre állási csoporthoz. 

- Most már csatlakozhat egy másodlagos írásvédett végponthoz a 3 replikával üzembe helyezett SQL felügyelt példányokon. `azdata arc sql endpoint list`A paranccsal megtekintheti a másodlagos írásvédett kapcsolódási végpontot.

### <a name="known-issues"></a>Ismert problémák

- Közvetlen kapcsolódási módban a használat, a metrikák és a naplók feltöltése `azdata arc dc upload` jelenleg le van tiltva. A rendszer automatikusan feltölti a használatot. A közvetetten csatlakoztatott módban létrehozott adatkezelőhöz való feltöltésnek továbbra is működnie kell.
- Az adatvezérlő közvetlen módban történő üzembe helyezése csak a Azure Portalból végezhető el, és nem érhető el az ügyféleszközök, például a azdata, a Azure Data Studio vagy a kubectl használatával.
- Az Azure arc-kompatibilis SQL felügyelt példány közvetlen módban történő üzembe helyezése csak a Azure Portalból végezhető el, és nem érhető el olyan eszközökről, mint a azdata, az Azure Data Studio vagy a kubectl.
- Az Azure arc-kompatibilis PostgeSQL-nagy kapacitású közvetlen módban történő üzembe helyezése jelenleg nem érhető el.
- A használati adatoknak a közvetlen kapcsolati módban való automatikus feltöltése nem fog sikerülni, ha a proxyt a segítségével használja `–proxy-cert <path-t-cert-file>` .

## <a name="february-2021"></a>2021. február

### <a name="new-capabilities-and-features"></a>Új képességek és funkciók

Az Azure-beli adatcli ( `azdata` ) verziószáma: 20.3.1. Telepítheti `azdata` Az Azure-beli [adatok parancssori felületét ( `azdata` )](/sql/azdata/install/deploy-install-azdata)is.

A további frissítések a következők:

- Azure Arc-kompatibilis, felügyelt SQL-példány
   - Magas rendelkezésre állás az Always On rendelkezésre állási csoportokkal

- Azure arc engedélyezve PostgreSQL nagy kapacitású Azure Data Studio: 
   - Az áttekintő lap megjeleníti a csomópontok szerint részletezett kiszolgálócsoport állapotát.
   - Most már elérhetővé vált egy új tulajdonságlap a kiszolgálócsoport további részleteinek megjelenítéséhez.
   - Postgres-motor paramétereinek konfigurálása a **csomópont-paraméterek** lapról

A kiadással kapcsolatos problémák esetén lásd: [ismert problémák – Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](known-issues.md)

## <a name="january-2021"></a>2021. január

### <a name="new-capabilities-and-features"></a>Új képességek és funkciók

Az Azure-beli adatcli ( `azdata` ) verziószáma: 20.3.0. Telepítheti `azdata` Az Azure-beli [adatok parancssori felületét ( `azdata` )](/sql/azdata/install/deploy-install-azdata)is.

A további frissítések a következők:
- A honosított portál 17 új nyelven érhető el
- Kube-natív. YAML fájlok kisebb módosításai
- A Grafana és a Kibana új verziói
- Problémák a Python-környezetekben, ha a Azure Data Studio megoldott jegyzetfüzetekben azdata használ
- A pg_audit bővítmény mostantól elérhető a PostgreSQL nagy kapacitású
- A PostgreSQL nagy kapacitású-adatbázis teljes visszaállításához már nincs szükség biztonsági mentési AZONOSÍTÓra
- Az állapotot (állapot) a rendszer minden olyan PostgreSQL-példányra vonatkozóan jelenteni kell, amely egy sever-csoportot alkot

   A korábbi kiadásokban az állapot a kiszolgáló csoport szintjén lett összesítve, és nem részletezve a PostgreSQL csomópont szintjén.

- A PostgreSQL üzembe helyezése most már tiszteletben tartja a Create parancsokban jelzett kötet mérete paramétereit
- A motor verziószáma mostantól a kiszolgálócsoport szerkesztésekor van megbecsülve
- Az Azure arc-alapú, a PostgreSQL nagy kapacitású-hez készült hüvelyek elnevezési konvenciója megváltozott

    Most már a (z) formában van: `ServergroupName{c, w}-n` . Például egy három csomóponttal rendelkező kiszolgálócsoport, egy koordinátori csomópont és két feldolgozói csomópont a következőképpen jelenik meg:
   - `Postgres01c-0` (koordinátori csomópont)
   - `Postgres01w-0` (feldolgozó csomópont)
   - `Postgres01w-1` (feldolgozó csomópont)

## <a name="december-2020"></a>2020. december

### <a name="new-capabilities--features"></a>Új képességek & funkciók

Az Azure-beli adatcli ( `azdata` ) verziószáma: 20.2.5. Telepítheti `azdata` Az Azure-beli [adatok parancssori felületét ( `azdata` )](/sql/azdata/install/deploy-install-azdata)is.

Megtekintheti az SQL felügyelt példányhoz tartozó végpontokat és a PostgreSQL-nagy kapacitású az Azure-beli adatcli ( `azdata` ) `azdata arc sql endpoint list` és `azdata arc postgres endpoint list` parancsokkal.

Az SQL felügyelt példányok erőforrásának (CPU Core és Memory) kérelmeit és korlátait Azure Data Studio használatával szerkessze.

Az Azure arc engedélyezve PostgreSQL nagy kapacitású mostantól támogatja az időponthoz tartozó visszaállítást a PostgreSQL 11. és 12. verziójának teljes biztonsági mentési visszaállítása mellett. Az időponthoz tartozó visszaállítási funkció lehetővé teszi, hogy egy adott dátumot és időpontot jelezzen a visszaállításhoz.

Megváltoztak az Azure arc-alapú, a PostgreSQL nagy kapacitású-hez készült hüvelyek elnevezési konvenciója. Jelenleg a következő formában van: ServergroupName {r, s}-_n_. Például egy három csomóponttal rendelkező kiszolgálócsoport, egy koordinátori csomópont és két feldolgozói csomópont a következőképpen jelenik meg:
- `postgres02r-0` (koordinátori csomópont)
- `postgres02s-0` (feldolgozó csomópont)
- `postgres02s-1` (feldolgozó csomópont)

### <a name="breaking-change"></a>Változás megszakítása

#### <a name="new-resource-provider"></a>Új erőforrás-szolgáltató

Ez a kiadás a nevű frissített [erőforrás-szolgáltatót](../../azure-resource-manager/management/azure-services-resource-providers.md) mutatja be `Microsoft.AzureArcData` . A funkció használatához regisztrálnia kell ezt az erőforrás-szolgáltatót. 

Az erőforrás-szolgáltató regisztrálása: 

1. A Azure Portal válassza az **előfizetések** lehetőséget. 
2. Válassza ki az előfizetését
3. A **Beállítások** területen válassza az **erőforrás-szolgáltatók** elemet. 
4. Keresse meg `Microsoft.AzureArcData` és válassza a **regisztráció** lehetőséget. 

A részletes lépéseket az [Azure Resource Providers és típusainál](../../azure-resource-manager/management/resource-providers-and-types.md)tekintheti át. Ez a módosítás eltávolítja a Azure Portalba feltöltött összes meglévő Azure-erőforrást is. Az erőforrás-szolgáltató használatához frissítenie kell az adatkezelőt, és a legújabb CLI-t kell használnia `azdata` .  

### <a name="platform-release-notes"></a>A platform kibocsátási megjegyzései

#### <a name="direct-connectivity-mode"></a>Közvetlen kapcsolati mód

Ez a kiadás közvetlen csatlakozási módot vezet be. A közvetlen csatlakozási mód lehetővé teszi, hogy az adatkezelő automatikusan feltöltse a használati adatokat az Azure-ba. A használat feltöltésének részeként a rendszer automatikusan létrehozza az ív-adatvezérlő erőforrását a portálon, ha még nem lett létrehozva a `azdata` feltöltésen keresztül.  

Az adatvezérlő létrehozásakor közvetlen kapcsolatot is megadhat. Az alábbi példa egy nevű adatvezérlőt hoz létre a `azdata arc dc create` `arc` Direct connectivity Mode ( `connectivity-mode direct` ) használatával. A példa futtatása előtt cserélje le az `<subscription id>` előfizetése azonosítóját.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Ismert problémák

- Az Azure Kubernetes szolgáltatásban (ak) a Kubernetes 1.19. x verziója nem támogatott.
- A Kubernetes 1,19 `containerd` nem támogatott.
- Az Azure-beli adatkezelő erőforrás jelenleg egy Azure-erőforrás. Az összes frissítés, például a törlés nem kerül vissza a kubernetes-fürtbe.
- A példányok neve nem lehet hosszabb 13 karakternél.
- Nincs helyben történő frissítés az Azure arc-adatkezelő vagy adatbázis-példányok számára.
- Az Arc-kompatibilis adatszolgáltatások tárolórendszerképei nem rendelkeznek aláírással.  Lehet, hogy konfigurálnia kell a Kubernetes-csomópontokat az aláírás nélküli tárolórendszerképek lekérésére.  Ha például a Docker-t tároló futtatókörnyezetként használja, beállíthatja a DOCKER_CONTENT_TRUST = 0 környezeti változót, és újraindulhat.  Más tároló-futtatókörnyezetek hasonló beállításokkal rendelkeznek, ilyen például az [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Nem hozható létre az Azure arc használatára képes SQL felügyelt példányok vagy PostgreSQL nagy kapacitású-kiszolgálócsoportok a Azure Portal.
- Most, ha az NFS-t használja, az `allowRunAsRoot` `true` Azure arc-adatkezelő létrehozása előtt be kell állítania a telepítési profil fájljában.
- Csak az SQL és a PostgreSQL bejelentkezési hitelesítés.  Azure Active Directory vagy Active Directory nem támogatott.
- A OpenShift adatvezérlőjének létrehozásához nyugodt biztonsági korlátozások szükségesek.  További információt a dokumentációban találhat.
- Ha Azure Kubernetes Service (ak) motort használ az Azure arc-adatkezelővel és az adatbázis-példányokkal rendelkező Azure Stack hubhoz, az újabb verzióra való frissítés nem támogatott. A Kubernetes-fürt frissítése előtt távolítsa el az Azure arc-adatkezelőt és az összes adatbázis-példányt.
- A [több rendelkezésre állási zónára](../../aks/availability-zones.md) kiterjedő AK-fürtök jelenleg nem támogatottak az Azure arc-kompatibilis adatszolgáltatások esetében. Ha el szeretné kerülni ezt a problémát, akkor amikor az AK-fürtöt Azure Portalban hozza létre, ha olyan régiót választ ki, ahol elérhetők zónák, törölje az összes zónát a kijelölés vezérlőelemből. Lásd a következő képet:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Törölje az egyes zónák jelölőnégyzeteit a none érték megadásához.":::

## <a name="october-2020"></a>2020. október 

Az Azure-beli adatcli ( `azdata` ) verziószáma: 20.2.3. Telepítheti `azdata` Az Azure-beli [adatok parancssori felületét ( `azdata` )](/sql/azdata/install/deploy-install-azdata)is.

### <a name="breaking-changes"></a>Kompatibilitástörő változások

Ez a kiadás a következő feltörési változásokat mutatja be: 

* A PostgreSQL egyéni erőforrás-definíciójában (CRD) a kifejezést a `shards` rendszer átnevezi `workers` . Ez a kifejezés ( `workers` ) megfelel a parancssori paraméter nevének.

* `azdata arc postgres server delete` a rendszer megerősítést kér a postgres-példány törlése előtt.  `--force`A paranccsal kihagyhatja a kérdést.

### <a name="additional-changes"></a>További módosítások

* Új opcionális paraméter lett hozzáadva a `azdata arc postgres server create` híváshoz `--volume-claim mounts` . Az érték a mennyiségi jogcím-csatlakoztatások vesszővel tagolt listája. A mennyiségi jogcím csatlakoztatása a kötet típusa és a PVC neve pár. Az egyetlen jelenleg támogatott típusú kötet `backup` .  A PostgreSQL-ben, ha a kötet típusa `backup` , a PVC csatlakoztatva van `/mnt/db-backups` .  Ez lehetővé teszi a biztonsági másolatok megosztását a PostgresSQL-példányok között, így egy PostgresSQL-példány biztonsági mentése egy másik példányban is visszaállítható.

* Új rövid nevek a PostgresSQL egyéni erőforrás-definíciók esetében: 
  * `pg11` 
  * `pg12`
* A telemetria feltöltése a következőket teszi lehetővé a felhasználók számára:
   * Az Azure-ba feltöltött pontok száma vagy 
   * Ha az Azure-ba nem töltöttek be adatbevitelt, a rendszer kérni fogja, hogy próbálkozzon újra.
* `azdata arc dc debug copy-logs` most is beolvassa a `/var/opt/controller/log` mappát, és a PostgreSQL-alapú naplókat gyűjti a Linuxon.
*   A biztonsági mentés a PostgreSQL nagy kapacitású való létrehozásakor és visszaállításakor működő kijelző megjelenítése.
* `azdata arc postrgres backup list` a mostantól tartalmazza a biztonsági másolatok méretére vonatkozó információkat.
* Az SQL felügyelt példányának admin Name tulajdonsága a Azure Portal áttekintés paneljének jobb oldali oszlopához lett hozzáadva.
* Azure Data Studio támogatja a PostgreSQL-nagy kapacitású munkavégző csomópontjainak, virtuális mag és memóriahasználat számának konfigurálását. 
* Az előzetes verzió támogatja a postgres biztonsági mentését és visszaállítását a 11-es és a 12-es verziójú verziók esetében.

## <a name="september-2020"></a>2020. szeptember

A nyilvános előzetes verzióban elérhető az Azure arc-kompatibilis adatszolgáltatások. Az arc-kompatibilis adatszolgáltatások lehetővé teszik az adatszolgáltatások bárhol történő kezelését.

- SQL Managed Instance
- PostgreSQL nagy kapacitású

Útmutatás: [Mi az az Azure arc-kompatibilis adatszolgáltatás?](overview.md)

## <a name="next-steps"></a>Következő lépések

> **Csak szeretné kipróbálni a dolgokat?**  
> Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az AK-ra, az AWS rugalmas Kubernetes szolgáltatásra (EKS), a Google Cloud Kubernetes Engine-re (GKE) vagy egy Azure-beli virtuális gépre.

- [Az ügyféleszközök telepítése](install-client-tools.md)
- [Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)
- [Azure SQL felügyelt példány létrehozása az Azure arc](create-sql-managed-instance.md) szolgáltatásban (először létre kell hoznia egy Azure arc-adatkezelőt)
- [Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehozni)
- [Erőforrás-szolgáltatók az Azure-szolgáltatásokhoz](../../azure-resource-manager/management/azure-services-resource-providers.md)
