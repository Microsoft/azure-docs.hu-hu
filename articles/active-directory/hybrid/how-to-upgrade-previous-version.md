---
title: 'Azure AD Connect: frissítés korábbi verzióról | Microsoft Docs'
description: Ismerteti a Azure Active Directory Connect legújabb verziójára való frissítés különböző módszereit, beleértve a helyben történő frissítést és a swing áttelepítést.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729990"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: frissítés egy előző verzióról a legújabbra
Ez a témakör azokat a különböző módszereket ismerteti, amelyekkel a Azure Active Directory (Azure AD) a legújabb verzióra történő frissítését végezheti el.  Ha jelentős konfigurációt végez, a [swing Migrálás](#swing-migration) szakasz lépéseit is használhatja.

>[!NOTE]
> Fontos, hogy a kiszolgálók naprakészek legyenek a Azure AD Connect legújabb kiadásaival. Folyamatosan frissítünk a AADConnect, és ezek a frissítések a biztonsági problémákra és a hibákra, valamint a szolgáltatásokra, teljesítményre és méretezhetőségre vonatkozó javításokat is tartalmaznak. Ha szeretné megtekinteni a legújabb verziót, és hogy megtudja, milyen módosításokat hajtottak végre a verziók között, tekintse meg a [kiadási verziók előzményeit](./reference-connect-version-history.md)

>[!NOTE]
> Jelenleg támogatott a Azure AD Connect bármely verziójáról a jelenlegi verzióra való frissítés. A nem támogatott, illetve a ADSync-alapú frissítések nem támogatottak, és szükség van a swing áttelepítésre.  Ha az rSync-ről szeretne frissíteni, tekintse meg az [Azure ad Sync Tool (az rsync)](how-to-dirsync-upgrade-get-started.md) vagy a [swing Migration](#swing-migration) című szakasz frissítését ismertető szakaszt.  </br>A gyakorlatban a rendkívül régi verziókban lévő ügyfelek olyan problémákba ütközhetnek, amelyek közvetlenül nem kapcsolódnak Azure AD Connecthoz. Azok a kiszolgálók, amelyeknek több éve van éles környezetben, általában több javítást is alkalmaztak, és nem mindegyike lehet a következő:.  Általában a 12-18 hónapokban nem frissített ügyfeleknél érdemes megfontolni a swing verziófrissítést, mivel ez a legkonzervatív és legkevesebb kockázatos megoldás.

Ha az rSync-ről szeretne frissíteni, tekintse meg a következőt: [verziófrissítés az Azure ad Sync Tool (az rsync) eszközről](how-to-dirsync-upgrade-get-started.md) .

Néhány különböző stratégia használható a Azure AD Connect frissítéséhez.

| Metódus | Leírás |
| --- | --- |
| [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md) |Ez a legegyszerűbb módszer az expressz telepítéssel rendelkező ügyfelek számára. |
| [Frissítés helyben](#in-place-upgrade) |Ha egyetlen kiszolgálóval rendelkezik, a telepítést helyben is frissítheti ugyanarra a kiszolgálóra. |
| [Párhuzamos migrálás](#swing-migration) |Ha két kiszolgálóval rendelkezik, előkészítheti az egyik kiszolgálót az új kiadással vagy konfigurációval, és megváltoztathatja az aktív kiszolgálót, ha elkészült. |

Az engedélyekkel kapcsolatos információkért tekintse meg a [frissítéshez szükséges engedélyeket](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Miután engedélyezte, hogy az új Azure AD Connect-kiszolgáló megkezdje a módosítások szinkronizálását az Azure AD-ba, nem kell visszaállnia az rSync vagy a Azure AD-szinkronizáló használatára. A Azure AD Connect korábbi ügyfelekre való visszaminősítése, beleértve az rSync és a Azure AD-szinkronizáló használatát, nem támogatott, és olyan problémákhoz vezethet, mint például az adatvesztés az Azure AD-ben.

## <a name="in-place-upgrade"></a>Frissítés helyben
A helyi frissítés Azure AD-szinkronizáló vagy Azure AD Connectból való áthelyezésre is használható. Nem működik a nem az rSync vagy a Forefront Identity Manager (FIM) + Azure AD-összekötővel végzett megoldás esetében.

Ez a módszer akkor javasolt, ha egyetlen kiszolgálóval rendelkezik, és kevesebb, mint körülbelül 100 000 objektum. Ha módosítja a beépített szinkronizálási szabályokat, a frissítés után a teljes Importálás és a teljes szinkronizálás is megtörténik. Ez a módszer biztosítja, hogy az új konfiguráció a rendszer összes meglévő objektumára érvényes legyen. Ez a Futtatás néhány órát is igénybe vehet, attól függően, hogy az objektumok hányan vannak a Szinkronizáló motor hatókörében. A normál különbözeti szinkronizálási ütemező (amely alapértelmezés szerint 30 percenként szinkronizál) fel van függesztve, de a jelszó-szinkronizálás folytatódik. Érdemes megfontolni a helyben történő frissítését egy hétvége során. Ha a beépített konfiguráció nem módosul az új Azure AD Connect kiadással, akkor a normál különbözeti importálás/szinkronizálás elindul.  
![Frissítés helyben](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Ha módosította a beépített szinkronizálási szabályokat, akkor ezek a szabályok a frissítés alapértelmezett konfigurációjához lesznek visszaállítva. Annak ellenőrzéséhez, hogy a konfiguráció a frissítések között marad-e, győződjön meg arról, hogy az [alapértelmezett konfiguráció módosításához az ajánlott eljárások](how-to-connect-sync-best-practices-changing-default-configuration.md)című témakörben leírtak szerint módosításokat végez.

A helyben történő frissítés során előfordulhat, hogy a frissítés befejeződése után meghatározott szinkronizálási tevékenységeket (beleértve a teljes importálási lépést és a teljes szinkronizálási lépést) igénylő módosításokat is be kell állítani. Az ilyen tevékenységek elhalasztásához tekintse meg a [teljes szinkronizálás késleltetése a frissítés után](#how-to-defer-full-synchronization-after-upgrade)című szakaszt.

Ha nem szabványos összekötővel (például általános LDAP-összekötővel és általános SQL-összekötővel) használja a Azure AD Connectt, frissítenie kell a megfelelő összekötő-konfigurációt a [synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) a helyben végzett frissítés után. Az összekötő konfigurációjának frissítésével kapcsolatos részletekért tekintse meg a cikk az [összekötő verziójának kiadási előzményei – hibaelhárítás](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting)című szakaszt. Ha nem frissíti a konfigurációt, az importálási és exportálási futtatási lépések nem fognak megfelelően működni az összekötőn. A *(z) "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" aktuális verziója ("x. x. xxx. x") korábbi, mint az alkalmazás eseménynaplójában a következő üzenet jelenik meg: "a HRE-összekötő konfigurálása (" x. x. xxx. x ").*

## <a name="swing-migration"></a>Párhuzamos migrálás
Ha összetett vagy sok objektummal rendelkezik, előfordulhat, hogy nem célszerű helyben frissíteni az élő rendszeren. Egyes ügyfelek esetében ez a folyamat több napot is igénybe vehet – és ez idő alatt nem történik változás. Ezt a módszert akkor is használhatja, ha jelentős módosításokat tervez a konfigurációban, és a felhőbe való leküldésük előtt szeretné kipróbálni őket.

A forgatókönyvek ajánlott módszere a swing-áttelepítés használata. Szüksége van (legalább) két kiszolgálóra – egy aktív kiszolgálóra és egy átmeneti kiszolgálóra. Az aktív kiszolgáló (amely az alábbi képen látható, folytonos kék vonallal jelenik meg) felelős az aktív éles terhelésért. Az előkészítési kiszolgáló (szaggatott lila vonallal látható) előkészíti az új kiadást vagy konfigurációt. Ha teljesen készen áll, a kiszolgáló aktív állapotba kerül. Az előző aktív kiszolgáló, amelyen már telepítve van a régi verziója vagy konfigurációja, az átmeneti kiszolgálóra kerül, és frissül.

A két kiszolgáló különböző verziókat használhat. A leszerelésre tervezett aktív kiszolgáló például használhatja a Azure AD-szinkronizálót, és az új átmeneti kiszolgáló használhatja a Azure AD Connect. Ha a swing áttelepítéssel új konfigurációt fejleszt ki, érdemes lehet ugyanazokat a verziókat használni a két kiszolgálón.  
![Átmeneti kiszolgáló](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Egyes ügyfelek inkább három vagy négy kiszolgálóval rendelkeznek ehhez a forgatókönyvhöz. Az átmeneti kiszolgáló frissítésekor nincs biztonsági mentési kiszolgáló a vész- [helyreállításhoz](how-to-connect-sync-staging-server.md#disaster-recovery). Három vagy négy kiszolgáló esetén előkészítheti az elsődleges/készenléti kiszolgálók egy készletét az új verzióra, amely biztosítja, hogy mindig legyen egy átmeneti kiszolgáló, amely készen áll az átvételre.

Ezek a lépések a Azure AD-szinkronizáló vagy egy megoldás a FIM + Azure AD Connector használatával való áthelyezésére is használhatók. Ezek a lépések nem működnek az rSync esetében, de ugyanaz a swing áttelepítési módszer (más néven párhuzamos telepítés) az rSync-vel kapcsolatos lépések a [frissítés Azure Active Directory Sync (az rsync)](how-to-dirsync-upgrade-get-started.md)szolgáltatásban.

### <a name="use-a-swing-migration-to-upgrade"></a>A frissítéshez használjon swing áttelepítést
1. Ha Azure AD Connectt használ mindkét kiszolgálón, és úgy tervezi, hogy csak egy konfigurációs változást hajt végre, akkor győződjön meg arról, hogy az aktív kiszolgáló és az átmeneti kiszolgáló egyaránt ugyanazt a verziót használja. Ez megkönnyíti a különbségek összehasonlítását később. Ha Azure AD-szinkronizálóról frissít, akkor ezek a kiszolgálók eltérő verziójúak. Ha a Azure AD Connect egy régebbi verziójáról frissít, érdemes megkezdeni a két kiszolgálót, amelyek ugyanazt a verziót használják, de nem kötelező.
2. Ha egyéni konfigurációt hajtott végre, és az átmeneti kiszolgáló nem rendelkezik vele, kövesse az [Egyéni konfiguráció áthelyezése az aktív kiszolgálóról az átmeneti kiszolgálóra](#move-a-custom-configuration-from-the-active-server-to-the-staging-server)című szakasz lépéseit.
3. Ha Azure AD Connect korábbi kiadásáról frissít, frissítse az átmeneti kiszolgálót a legújabb verzióra. Ha Azure AD-szinkronizáló, akkor telepítse a Azure AD Connectt az átmeneti kiszolgálóra.
4. Lehetővé teszi, hogy a Szinkronizáló motor teljes importálást és teljes szinkronizálást futtasson az átmeneti kiszolgálón.
5. Ellenőrizze, hogy az új konfiguráció nem okozott-e váratlan módosításokat az "ellenőrzés" szakaszban ismertetett lépések használatával a [kiszolgálók konfigurációjának ellenőrzéséhez](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Ha valami nem a várt módon történik, javítsa ki, futtassa az importálást és a szinkronizálást, és ellenőrizze, hogy az adatok nem megfelelőek-e, a lépéseket követve.
6. Állítsa az átmeneti kiszolgálót az aktív kiszolgálóra. Az "aktív kiszolgáló váltása" utolsó lépése a [kiszolgálók konfigurációjának ellenőrzése](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Azure AD Connect frissítésekor frissítse a jelenleg átmeneti módban lévő kiszolgálót a legújabb kiadásra. Az adatgyűjtés és a konfiguráció frissítésének megkezdése előtt kövesse az alábbi lépéseket. Ha Azure AD-szinkronizáló-ból frissítette, most kikapcsolhatja és leszerelheti a régi kiszolgálót.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Egyéni konfiguráció áthelyezése az aktív kiszolgálóról az átmeneti kiszolgálóra
Ha módosította az aktív kiszolgáló konfigurációját, meg kell győződnie arról, hogy ugyanazok a módosítások lesznek alkalmazva az átmeneti kiszolgálóra. Ha segítségre van szüksége az áthelyezéssel kapcsolatban, használhatja a [Azure ad Connect konfigurációs dokumentáló](https://github.com/Microsoft/AADConnectConfigDocumenter).

Áthelyezheti a PowerShell használatával létrehozott egyéni szinkronizálási szabályokat. A módosításokat ugyanúgy kell alkalmaznia, mint mindkét rendszeren, és a módosítások nem telepíthetők át. A [konfigurációs dokumentum](https://github.com/Microsoft/AADConnectConfigDocumenter) segítségével összehasonlíthatja a két rendszert, hogy megbizonyosodjon róla, hogy azonosak-e. Az eszköz segítséget nyújt az ebben a szakaszban található lépések automatizálásában is.

Mindkét kiszolgálón ugyanúgy kell konfigurálnia a következő dolgokat:

* Kapcsolódás ugyanahhoz az erdőhöz
* Bármely tartomány-és OU-szűrés
* Ugyanazok a választható funkciók, mint például a jelszó-szinkronizálás és a jelszó visszaírási

**Egyéni szinkronizálási szabályok áthelyezése**  
Az egyéni szinkronizálási szabályok áthelyezéséhez tegye a következőket:

1. Nyissa meg az aktív kiszolgálón a **szinkronizációs szabályok szerkesztőjét** .
2. Válasszon ki egy egyéni szabályt. Kattintson az **Export** (Exportálás) gombra. Ekkor megjelenik a Jegyzettömb ablaka. Mentse az ideiglenes fájlt egy PS1 bővítménnyel. Ez egy PowerShell-szkriptet tesz lehetővé. Másolja a PS1-fájlt az átmeneti kiszolgálóra.  
   ![Szinkronizálási szabály exportálása](./media/how-to-upgrade-previous-version/exportrule.png)
3. Az összekötő GUID-azonosítója eltér az átmeneti kiszolgálón, és módosítania kell azt. A GUID beszerzéséhez indítsa el a **szinkronizálási szabályok szerkesztőjét**, válassza ki az azonos csatlakoztatott rendszernek megfelelő beépített szabályok egyikét, majd kattintson az **Exportálás** elemre. Cserélje le a GUID azonosítót a PS1-fájlban az átmeneti kiszolgáló GUID azonosítójával.
4. A PowerShell-parancssorban futtassa a PS1-fájlt. Ez létrehozza az egyéni szinkronizálási szabályt az átmeneti kiszolgálón.
5. Ismételje meg az összes egyéni szabályt.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>A teljes szinkronizálás késleltetése a frissítés után
A helyben végzett frissítés során előfordulhat, hogy a végrehajtandó módosítások bizonyos szinkronizálási tevékenységeket igényelnek (beleértve a teljes importálási lépést és a teljes szinkronizálási lépést). Az összekötő sémájának módosításai például a **teljes importálás** lépés és a beépített szinkronizálási szabály módosítására van szükség az érintett összekötők **teljes szinkronizációs** lépésének végrehajtásához. A frissítés során a Azure AD Connect meghatározza, hogy milyen szinkronizálási tevékenységek szükségesek, és *felülbírálásként* rögzíti őket. A következő szinkronizálási ciklusban a szinkronizálási ütemező felveszi ezeket a felülbírálásokat, és végrehajtja azokat. A felülbírálás sikeres végrehajtása után a rendszer eltávolítja azt.

Előfordulhatnak olyan helyzetek, amikor nem szeretné, hogy ezek a felülbírálások azonnal érvénybe lépnek a frissítés után. Például számos szinkronizált objektummal rendelkezik, és szeretné, hogy ezek a szinkronizálási lépések munkaidő után is megtörténjenek. A felülbírálások eltávolítása:

1. A frissítés során **törölje** a **szinkronizálási folyamat elindítása a konfiguráció befejeződése** után lehetőséget. Ez letiltja a szinkronizálási ütemező szolgáltatást, és megakadályozza, hogy a szinkronizálási ciklus automatikusan megtörténjen a felülbírálások eltávolítása előtt.

   ![Képernyőfelvétel: a szinkronizálási folyamat elindítása, amikor a konfiguráció befejeződik, és törölni kell.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. A frissítés befejeződése után futtassa a következő parancsmagot, hogy megtudja, milyen felülbírálások lettek hozzáadva: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > A felülbírálások összekötő-specifikusak. A következő példában a teljes importálási lépést és a teljes szinkronizálás lépést a helyszíni AD-összekötőhöz és az Azure AD-összekötőhöz is hozzáadták.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Jegyezze fel a meglévő felülbírálásokat, amelyek hozzá lettek adva.
   
4. Ha el szeretné távolítani a felülbírálásokat a teljes importáláshoz és a teljes szinkronizáláshoz egy tetszőleges összekötőn, futtassa a következő parancsmagot: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Az összes összekötő felülbírálásának eltávolításához hajtsa végre a következő PowerShell-parancsfájlt:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Az ütemező folytatásához futtassa a következő parancsmagot: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Ne felejtse el végrehajtani a szükséges szinkronizálási lépéseket a lehető leghamarabb. Ezeket a lépéseket manuálisan is végrehajthatja a Synchronization Service Manager használatával, vagy hozzáadhatja a felülbírálásokat a Set-ADSyncSchedulerConnectorOverride parancsmag használatával.

Ha a felülbírálásokat a teljes importáláshoz és a teljes szinkronizáláshoz is hozzá szeretné adni egy tetszőleges összekötőn, futtassa a következő parancsmagot:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Hibaelhárítás
A következő szakasz azokat a hibaelhárítási és információkat tartalmazza, amelyeket akkor használhat, ha a Azure AD Connect frissítése során probléma merül fel.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>A Azure Active Directory-összekötő hiányzó hiba a Azure AD Connect frissítése közben

Ha egy korábbi verzióról Azure AD Connect frissít, a következő hibaüzenet jelenhet meg a frissítés elején. 

![Hiba](./media/how-to-upgrade-previous-version/error1.png)

Ez a hiba azért fordul elő, mert a b891884f-051e-4a83-95af-2544101c9083 azonosítójú Azure Active Directory összekötő nem létezik az aktuális Azure AD Connect-konfigurációban. A helyzet ellenőrzéséhez nyisson meg egy PowerShell-ablakot, és futtassa a parancsmagot. `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

A PowerShell-parancsmag azt a hibát jelzi, **hogy a megadott ma nem található**.

Ennek az az oka, hogy a jelenlegi Azure AD Connect konfiguráció frissítése nem támogatott. 

Ha a Azure AD Connect újabb verzióját szeretné telepíteni: a Azure AD Connect varázsló bezárásához távolítsa el a meglévő Azure AD Connect, és végezze el az újabb Azure AD Connect tiszta telepítését.



## <a name="next-steps"></a>Következő lépések
További információ a helyszíni [identitások és a Azure Active Directory integrálásáról](whatis-hybrid-identity.md).