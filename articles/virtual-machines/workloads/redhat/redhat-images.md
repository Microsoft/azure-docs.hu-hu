---
title: Red Hat Enterprise Linux rendszerképek áttekintése az Azure-ban
description: Ismerkedjen meg Red Hat Enterprise Linux rendszerképekkel Microsoft Azureban.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 0f3b36729fb07c6f5ae03a46e199e4cadb44a25e
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635591"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux rendszerképek áttekintése

Ez a cikk az Azure Marketplace-en elérhető Red Hat Enterprise Linux-(RHEL-) lemezképeket és az elnevezési és adatmegőrzési szabályzatokat ismerteti.

További információ a RHEL összes verziójának Red Hat-támogatási szabályzatáról: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata). A díjszabással kapcsolatos részletekért lásd: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Az Azure Marketplace-en jelenleg elérhető RHEL-lemezképek a saját előfizetések (BYOS) vagy az utólagos elszámolású licencelési modellek használatát támogatják. A BYOS és az utólagos elszámolású licencelés közötti dinamikus váltás a [Azure Hybrid use Benefiton](../../linux/azure-hybrid-benefit-linux.md)keresztül végezhető el.

>[!NOTE]
> Az Azure Marketplace-en található RHEL-lemezképekkel kapcsolatos bármilyen probléma esetén a Microsoft támogatási jegyet nyújt.

## <a name="view-images-available-in-azure"></a>Az Azure-ban elérhető rendszerképek megtekintése

Ha a "Red Hat" kifejezést keresi az Azure Marketplace-en, vagy ha erőforrást hoz létre a Azure Portal felhasználói felületen, akkor csak az összes elérhető RHEL-lemezkép részhalmaza jelenik meg. Az Azure CLI, a PowerShell és az API használatával bármikor megszerezheti a rendelkezésre álló virtuálisgép-rendszerképek teljes készletét.

Az Azure-ban elérhető Red Hat-lemezképek teljes készletének megtekintéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Elnevezési konvenció

Az Azure-beli virtuálisgép-rendszerképeket a kiadó, az ajánlat, az SKU és a verzió rendezi. A kiadó: ajánlat: SKU: Version a (z) a rendszerkép URN kombinációja, amely egyedileg azonosítja a használni kívánt rendszerképet.

Például `RedHat:RHEL:8-LVM:8.1.20200318` egy RHEL 8,1 LVM-particionálású rendszerképre hivatkozik, amely 2020. március 18-án épült.

Itt látható egy példa arra, hogyan hozhat létre RHEL 8,1 virtuális gépet.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>A "legfrissebb" moniker

Az Azure REST API lehetővé teszi a "legújabb" moniker használatát az adott verzió helyett a verzióhoz. A "legújabb" kifejezéssel az adott közzétevőhöz, ajánlathoz és SKU-hoz készült legújabb elérhető lemezképet helyezheti üzembe.

Például `RedHat:RHEL:8-LVM:latest` a legújabb RHEL 8 családos LVM-particionált lemezképre hivatkozik.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Általánosságban elmondható, hogy a verziók összehasonlítása a legújabbat követi a [compareto metódus végrehajtása metódus](/dotnet/api/system.version.compareto#system_version_compareto_system_version_)szabályainak megfelelően.
A rendszerkép-verzió összehasonlítását úgy végezheti el, hogy az értékeket egy [Version](/dotnet/api/system.version.-ctor) objektumként hasonlítja össze, nem karakterláncként.

## <a name="rhel-6-image-types"></a>RHEL 6 Képtípus

>[!NOTE]
> December 30 2020-én a RHEL 6,10 (EOL) életbe lépett. A folyamatos támogatás érdekében engedélyezze az ELS-t a hosszabb életciklus-támogatási fázis részeként: [Red Hat els dokumentáció](./redhat-extended-lifecycle-support.md).

RHEL 6. x rendszerképek esetén a képtípusok az alábbi táblázatban láthatók.

|Publisher | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Másodlagos verzió (például 6,9) | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.9.2018010506) | A standard RHEL 6. x lemezképek ezt az egyezményt követik.
|RedHat | RHEL – BYOS | RHEL – raw69 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.9.20181023) | Ez a rendszerkép egy RHEL 6,9 BYOS-rendszerkép.
|RedHat | RHEL | RHEL – SAP-APPS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.8.2017053118) | Ez a rendszerkép egy RHEL 6,8 for SAP Applications-rendszerkép. Jogosult az SAP-alkalmazások tárházának és az alap RHEL-Tárházak elérésére.
|RedHat | RHEL | RHEL – SAP-HANA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 6.7.2017053121) | Ez a rendszerkép egy RHEL 6,7 SAP HANA rendszerképhez. A SAP HANA-Tárházak és az alapszintű RHEL-Tárházak elérésére jogosult.

## <a name="rhel-7-image-types"></a>RHEL 7 Képtípus

A RHEL 7. x rendszerképek esetében van néhány különböző képtípus. Az alábbi táblázat az általunk kínált rendszerképek különböző készleteit mutatja be. A teljes lista megtekintéséhez használja az Azure CLI-parancsot `az vm image list --publisher redhat --all` .

>[!NOTE]
> Ha másként nincs jelezve, minden lemezkép LVM particionálva van, és csatlakozik a normál RHEL-adattárakhoz. Ez azt is lehetővé tenné, hogy a Tárházak nem bővítik az Update support (EUS) szolgáltatást, és nem frissítik az SAP-t (E4S). A jövőben csak az LVM-particionált rendszerképeket tesszük közzé, de erre a döntésre vonatkozó visszajelzések is nyitva vannak. További információ a bővített frissítés támogatásáról és az SAP-hez készült frissítési szolgáltatásairól: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | Másodlagos verzió (például 7,6) | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019102813) | Az április 2019 előtt közzétett rendszerképek a standard RHEL-adattárakhoz vannak csatolva. Az április 2019 után közzétett rendszerképek a Red Hat EUS-adattárakhoz vannak csatolva, hogy lehetővé tegyék a verzió zárolását egy adott alverzión. Azok az ügyfelek, akik szeretnék, hogy a normál Tárházak az SKU-ban lévő 7-LVM vagy 7-es-nyers képeket használják (részletek követik ezt a részleteket). A RHEL 7,7-es és újabb verziói az LVM particionálva vannak. A kategóriába tartozó összes többi rendszerkép nyers particionálású.
|RedHat | RHEL | 7 – NYERS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019102813) | Ezek a lemezképek nyers particionálva vannak (például nem lettek hozzáadva logikai kötetek).
|RedHat | RHEL | 7-RAW-CI | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019072418) | Ezek a lemezképek nyers particionálva vannak (például nem lettek hozzáadva logikai kötetek), és a Cloud-init használatával kiépíthető.
|RedHat | RHEL | 7 – LVM | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062414) | Ezek a lemezképek az LVM particionálva vannak.
|RedHat | RHEL – BYOS | RHEL – {LVM, RAW} | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.7.20190819) | Ezek a lemezképek a RHEL 7 BYOS rendszerképek. Nincsenek csatolva adattárakhoz, és nem számítanak fel díjat a prémium RHEL. Ha érdekli a RHEL BYOS-lemezképe, [kérjen hozzáférést](https://aka.ms/rhel-byos). Az SKU-értékek a másodlagos verzióval végződik, és azt jelzi, hogy a rendszerkép nyers vagy LVM particionálású-e. Például az RHEL-lvm77 SKU-értéke egy LVM-particionálású RHEL 7,7-rendszerképet jelez.
|RedHat | RHEL | RHEL – SAP | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019071300) | Ezek a rendszerképek RHEL az SAP-lemezképekhez. Jogosultak hozzáférni a SAP HANA és az alkalmazások tárházához, valamint a RHEL E4S-tárházhoz. A számlázás magában foglalja a RHEL Premiumot és az SAP Premiumot az alap számítási díj alapján.
|RedHat | RHEL | RHEL – SAP-HA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062320) | Ezek a rendszerképek a magas rendelkezésre állású és a frissítési szolgáltatások RHEL az SAP számára. Jogosultak hozzáférni a SAP HANA és az alkalmazások tárházához, valamint a magas rendelkezésre állású adattárakhoz, valamint a RHEL E4S-tárházhoz. A számlázás a RHEL Premium, az SAP Premium és a magas rendelkezésre állás díját foglalja magában az alap számítási díj alapján.
|RedHat | RHEL | RHEL – HA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.6.2019062019) | Ezek a lemezképek olyan RHEL lemezképek, amelyek a magas rendelkezésre állású bővítmény elérésére is jogosultak. A magas rendelkezésre állást biztosító Prémium csomag miatt a RHEL és az alap számítási díj után valamivel külön díjat számítunk fel.
|RedHat | RHEL | RHEL – SAP-APPS | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.3.2017053118) | Ezek a rendszerképek elavultak, mert az SAP-alkalmazások és a SAP HANA adattárak az SAP-adattárakba vannak egyesítve. Ezek a rendszerképek RHEL az SAP-alkalmazások rendszerképeihez. Az SAP-alkalmazások tárházait és az alap RHEL-Tárházak elérésére jogosultak.
|RedHat | RHEL | RHEL – SAP-HANA | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 7.3.2018051421) | Ezek a rendszerképek elavultak, mert az SAP-alkalmazások és a SAP HANA adattárak az SAP-adattárakba vannak egyesítve. Ezek a rendszerképek RHEL for SAP HANA lemezképek. Jogosultak a SAP HANA-Tárházak és az alapszintű RHEL-adattárak elérésére.

## <a name="rhel-8-image-types"></a>RHEL 8 Képtípus

>[!NOTE]
> A Red Hat azt javasolja, hogy a kivágás használatával konfigurálja a kernel parancssori paramétereit a RHEL 8 +-ban. További részletek [itt](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)érhetők el.

A RHEL 8 képtípusának részletei alább láthatók.

|Publisher | Ajánlat | SKU-érték | Verzió | Részletek
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 8.0.20191023) | Ezek a képek a standard Red Hat-adattárakhoz csatlakoztatott, 8 LVM-particionált rendszerképek RHEL.
|RedHat | RHEL | 8 – Gen2 | A RHEL alverzió és a közzétett dátum összefűzött értékei (például 8.0.20191024) | Ezek a lemezképek a Hyper-V 2. generációs RHEL 8 LVM-particionált rendszerképek a standard Red Hat-tárházhoz csatlakoztatva. További információ az Azure-beli 2. generációs virtuális gépekről: a [2. generációs virtuális gépek támogatása az Azure](../../generation-2.md)-ban.

## <a name="rhel-extended-support-add-ons"></a>RHEL kiterjesztett támogatása – bővítmények

### <a name="extended-life-cycle-support"></a>Meghosszabbított életciklus-támogatás

A kiterjesztett életciklus-támogatás (ELS) bővítmény egy opcionális előfizetés, amely kritikus fontosságú és fontos biztonsági javításokat tesz lehetővé olyan kiadásokban, amelyek elértek az élettartam végét (EOL). A RHEL EUS kapcsolatos további információk a [Red Hat dokumentációjában](https://access.redhat.com/support/policy/updates/errata#Extended_Life_Cycle_Support)olvashatók.

Az ELS jelenleg csak a 6,10-es RHEL érhető el. Az utólagos elszámolású lemezképek esetében az ELS a [Red Hat els dokumentációjának](./redhat-extended-lifecycle-support.md)lépéseit követve engedélyezhető.

Ha régebbi verziójú verziót használ, az ELS engedélyezése előtt frissítenie kell a RHEL 6,10-re.

### <a name="extended-update-support"></a>Bővített frissítési támogatás

Április 2019-én a RHEL-lemezképek elérhetők, amelyek alapértelmezés szerint a EUS-adattárakhoz vannak csatolva. A RHEL EUS kapcsolatos további információk a [Red Hat dokumentációjában](https://access.redhat.com/articles/rhel-eus)olvashatók.

A EUS-tárházra való váltás lehetséges és támogatott. A virtuális gép EUS való átváltására, valamint a EUS támogatásával kapcsolatos további információkért lásd: [RHEL EUs and Version-locking RHEL VMS](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> A EUS nem támogatott a RHEL-extrák esetében. Ez azt jelenti, hogy ha olyan csomagot telepít, amely általában az RHEL extrák csatornán keresztül érhető el, akkor a EUS nem tudja elvégezni. A Red Hat extrák termék életciklusával kapcsolatos további információkért lásd: [Red Hat Enterprise Linux extrák életciklusa](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Különbséget tesz a normál és a EUS képek között

Azok az ügyfelek, akik a EUS-adattárakhoz csatolt képeket szeretnék használni, az SKU-ban található RHEL alverziószámot tartalmazó RHEL-lemezképet kell használniuk.

Előfordulhat például, hogy a következő két RHEL 7,4 lemezkép érhető el.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Ebben az esetben alapértelmezés szerint a `RedHat:RHEL:7.6:7.6.2019102813` EUs-adattárakhoz van csatolva. Az SKU értéke 7,4. És `RedHat:RHEL:7-LVM:7.6.2019062414` alapértelmezés szerint nem EUs-adattárakhoz van csatolva. Az SKU értéke 7 – LVM.

A normál (nem EUS) adattárak használatához olyan rendszerképet használjon, amely nem tartalmaz alverziószámot az SKU-ban.

#### <a name="rhel-images-with-eus"></a>RHEL-lemezképek a EUS

Az alábbi táblázatban szereplő információk a EUS-tárakhoz kapcsolódó RHEL-lemezképekre vonatkoznak.

>[!NOTE]
> Az írás időpontjában csak a 7,4-es és újabb alverziók RHEL EUS-támogatással rendelkeznek. A EUS már nem támogatott a RHEL <= 7,3 esetében.
>
> További információ a RHEL-EUS rendelkezésre állásáról: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata).

Alverzió |EUS-rendszerkép – példa              |EUS állapota                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat: RHEL: 7.4:7.4.2019041718 | Az április 2019-es és újabb verzióiban közzétett lemezképek alapértelmezés szerint EUS.|
RHEL 7.5      |RedHat: RHEL: 7.5:7.5.2019060305 | A 2019-es és újabb verziókon közzétett lemezképek alapértelmezés szerint EUS. |
RHEL 7,6      |RedHat: RHEL: 7.6:7.6.2019052206 | A 2019-es és újabb verzióiban közzétett rendszerképek alapértelmezés szerint EUS. |
RHEL 8,0      |N/A                            | A Red Hat nem érhető el EUS.                               |

### <a name="update-services-for-sap"></a>SAP-szolgáltatások frissítése

Az SAP-lemezképek legújabb RHEL az SAP Solutions-előfizetések (E4S) frissítési szolgáltatásaihoz lesznek csatlakoztatva. A E4S kapcsolatos további információkért tekintse meg a Red Hat [dokumentációját](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions).

#### <a name="rhel-images-with-e4s"></a>RHEL-lemezképek a E4S

A 2019 december után létrehozott alábbi ajánlatokból származó lemezképek a E4S-adattárakhoz csatlakoznak:

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL for SAP magas rendelkezésre állású és frissítési szolgáltatásokkal)

## <a name="other-available-offers-and-skus"></a>Egyéb elérhető ajánlatok és SKU-ket

Az elérhető ajánlatok és SKU-ket teljes listája tartalmazhat további rendszerképeket, amelyek az előző táblázatban láthatók. Például: `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Ezek az ajánlatok az adott Piactéri megoldások támogatásához használhatók. Vagy közzétehető előzetes és tesztelési célokra. Előfordulhat, hogy a figyelmeztetés nélkül bármikor módosíthatók vagy eltávolíthatók. Ne használja őket, hacsak a Microsoft vagy a Red Hat nem nyilvánosan dokumentálja a jelenlétét.

## <a name="publishing-policy"></a>Közzétételi szabályzat

A Microsoft és a Red Hat frissíti a lemezképeket új alverzióként, az egyes gyakori biztonsági rések és kitettségek (CVEs), illetve az esetenkénti konfigurációs változások és frissítések kezelése érdekében. Arra törekszünk, hogy a lehető legrövidebb időn belül elérhetővé váljon a frissített lemezképek a CVE-javítás kiadása vagy rendelkezésre állása után három munkanapon belül.

Egy adott rendszerkép-családban csak az aktuális alverziót frissítjük. Egy újabb alverzió kiadásával a régebbi alverzió frissítése nem áll le. Például a RHEL 7,6 kiadásával a RHEL 7,5 lemezképek már nem frissülnek.

>[!NOTE]
> Az RHEL-as elszámolású képekből kiépített aktív Azure-beli virtuális gépek az Azure-RHUI csatlakoznak, és a Red Hat által kiadott és az Azure-RHUI replikált frissítések és javítások azonnal elérhetők. Az időzítés általában kevesebb, mint 24 óra, amelyet a Red hat hivatalos kiadása követ. Ezeknek a virtuális gépeknek nincs szükségük új közzétett rendszerképre a frissítések beszerzéséhez. Az ügyfelek teljes körű vezérléssel rendelkeznek a frissítés elindításához.

## <a name="image-retention-policy"></a>Képmegőrzési szabályzat

Az aktuális szabályzat az összes korábban közzétett rendszerkép megőrzése. Fenntartjuk a jogot arra, hogy olyan rendszerképeket távolítson el, amelyek bármilyen típusú problémát okozhatnak. Előfordulhat például, hogy a következő platform vagy összetevő frissítései miatt helytelen konfigurációval rendelkező lemezképek el lesznek távolítva. Az esetlegesen eltávolított lemezképek az aktuális Azure Marketplace-szabályzat alapján biztosítanak értesítést akár 30 nappal a lemezkép eltávolítása előtt.

## <a name="next-steps"></a>Következő lépések

* Az Azure-ban elérhető RHEL-lemezképek teljes listájának megtekintéséhez lásd: [Red Hat Enterprise Linux (RHEL) lemezképek az Azure-ban](./redhat-imagelist.md).
* További információ az Azure Red Hat frissítési infrastruktúráról: [Red Hat frissítési infrastruktúra az igény szerinti RHEL virtuális gépekhez az Azure-ban](./redhat-rhui.md).
* Ha többet szeretne megtudni a RHEL BYOS ajánlatáról, tekintse meg az Azure-beli [saját előfizetéssel rendelkező Gold-lemezképek Red Hat Enterprise Linux](./byos.md).
* További információ a RHEL összes verziójának Red Hat-támogatási szabályzatáról: [Red Hat Enterprise Linux életciklusa](https://access.redhat.com/support/policy/updates/errata).
