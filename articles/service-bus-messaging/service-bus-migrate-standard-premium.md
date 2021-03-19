---
title: Azure Service Bus névterek migrálása – standard – prémium
description: Útmutató a meglévő Azure Service Bus standard névterek prémium szintű áttelepítésének engedélyezéséhez
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340746"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Meglévő Azure Service Bus standard névterek migrálása a prémium szintre

Korábban csak a standard szinten Azure Service Bus felkínált névtereket. A névterek több-bérlős telepítések, amelyek alacsony átviteli sebességre és fejlesztői környezetekre vannak optimalizálva. A prémium szint az egyes névterekhez tartozó dedikált erőforrásokat kínálja a kiszámítható késéshez és a megnövekedett átviteli sebességhez rögzített áron. A prémium szint nagy teljesítményű és éles környezetekhez van optimalizálva, amelyek további vállalati funkciókat igényelnek.

Ez a cikk bemutatja, hogyan telepítheti át a meglévő standard szintű névtereket a prémium szintre.  

>[!WARNING]
> A Migrálás Service Bus standard névterek prémium szintre való frissítésére szolgál. Az áttelepítési eszköz nem támogatja a visszalépést.

Néhány Megjegyzés:

- Az áttelepítés célja, hogy megtörténjen, ami azt jelenti, hogy a meglévő küldő és fogadó alkalmazások **nem igénylik a kód vagy a konfiguráció módosítását**. A meglévő kapcsolódási sztring automatikusan az új prémium névtérre mutat.
- A **prémium** névtérnek **nem lehetnek entitásai** az áttelepítés sikerességéhez.
- A standard névtérben lévő összes **entitást** az áttelepítési folyamat során a prémium névtérre **másolja** a rendszer.
- A Migrálás a prémium **szinten 1 000 entitást támogat az üzenetkezelési egységenként** . Annak megállapításához, hogy hány üzenetkezelési egységre van szüksége, kezdje az aktuális standard névtérben található entitások számával.
- Az **alapszintű csomagról** a prémium szintre való áttelepítést közvetlenül nem végezheti el, de az alapszintű és a standard közötti áttelepítéssel, majd a következő lépésben a standard és a prémium **szintről** is elvégezhető.

## <a name="migration-steps"></a>A migrálás lépései

Bizonyos feltételek az áttelepítési folyamathoz vannak társítva. Ismerkedjen meg az alábbi lépésekkel a hibák lehetőségének csökkentése érdekében. Ezek a lépések körvonalazzák az áttelepítési folyamatot, és a részletes részletek a következő szakaszokban vannak felsorolva.

1. Hozzon létre egy új, prémium szintű névteret.
1. A standard és a prémium szintű névterek párosítása egymáshoz.
1. A standard és a prémium szintű névtér közötti szinkronizálási (másolási) entitások.
1. Véglegesítse az áttelepítést.
1. A standard névtérben lévő entitások kiürítése a névtér áttelepítésének utáni neve használatával.
1. Törölje a szabványos névteret.

>[!IMPORTANT]
> Az áttelepítés véglegesítése után nyissa meg a régi standard névteret, és ürítse ki a várólistákat és előfizetéseket. Az üzenetek kiürítése után a rendszer a fogadó alkalmazások által feldolgozandó új prémium névtérbe is eljuttathatja őket. A várólisták és az előfizetések kiürítése után javasoljuk, hogy törölje a régi szabványos névteret.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrálás az Azure CLI vagy a PowerShell használatával

Ha az Azure CLI vagy a PowerShell eszköz használatával szeretné áttelepíteni a Service Bus standard szintű névteret prémium szintre, kövesse az alábbi lépéseket.

1. Hozzon létre egy új Service Bus Premium-névteret. Hivatkozhat a Azure Resource Manager- [sablonokra](service-bus-resource-manager-namespace.md) , vagy [használhatja a Azure Portal](service-bus-create-namespace-portal.md). Ügyeljen arra, hogy a **prémium** elemet válassza ki a **serviceBusSku** paraméterhez.

1. A következő környezeti változók beállításával egyszerűsítheti az áttelepítési parancsokat.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > A Migrálás utáni alias/név (post_migration_dns_name) a régi standard névtér áttelepítési utáni eléréséhez lesz használva. Ezzel a paranccsal ürítheti a várólistákat és az előfizetéseket, majd törölheti a névteret.

1. Párosítsa a standard és a prémium szintű névtereket, és indítsa el a szinkronizálást a következő parancs használatával:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Az áttelepítés állapotát a következő paranccsal ellenőrizhető:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Az áttelepítés akkor tekinthető befejezettnek, ha a következő értékeket látja:

    * MigrationState = "aktív"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "sikeres"

    Ez a parancs az áttelepítési konfigurációt is megjeleníti. Győződjön meg arról, hogy az értékek helyesen vannak beállítva. Ellenőrizze a prémium szintű névteret is a portálon, és győződjön meg arról, hogy az összes várólista és témakör létrejött, és hogy azok egyeznek a standard névtérben találhatókkal.

1. Véglegesítse az áttelepítést a következő teljes parancs végrehajtásával:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrálás a Azure Portal használatával

A Azure Portal használatával végzett Migrálás ugyanazzal a logikai folyamattal rendelkezik, mint a parancsok használatával történő áttelepítés. Az alábbi lépéseket követve áttelepítheti a Azure Portal használatával.

1. A bal oldali ablaktábla **navigációs** menüjében válassza az **áttelepítés prémiumra** lehetőséget. Kattintson az **első lépések** gombra a következő lapra való továbblépéshez.
    ![Áttelepítési Kezdőlap][]

1. Fejezze be a **telepítést**.
   ![Telepítési névtér][]
   1. Hozza létre és rendelje hozzá a prémium névteret a meglévő standard névtér áttelepítéséhez.
        ![Névtér beállítása – prémium szintű névtér létrehozása][]
   1. Válassza ki az **áttelepítés utáni nevet**. Ezt a nevet fogja használni a standard névtér eléréséhez az áttelepítés befejeződése után.
        ![Névtér beállítása – a kivételezés utáni áttelepítés neve][]
   1. A folytatáshoz válassza a **Next (tovább** ) lehetőséget.
1. Entitások szinkronizálása a standard és a prémium szintű névterek között.
    ![Telepítési névtér – entitások szinkronizálása – indítás][]

   1. Az entitások szinkronizálásának megkezdéséhez kattintson a **szinkronizálás indítása** elemre.
   1. Válassza az **Igen** lehetőséget a párbeszédpanelen a szinkronizálás megerősítéséhez és elindításához.
   1. Várjon, amíg a szinkronizálás be nem fejeződik. Az állapot az állapotsoron érhető el.
        ![Telepítési névtér – entitások szinkronizálása – folyamat][]
        >[!IMPORTANT]
        > Ha bármilyen okból meg kell szakítania az áttelepítést, tekintse át a jelen dokumentum GYIK szakaszának megszakítási folyamatát.
   1. A szinkronizálás befejeződése után kattintson a **Tovább gombra** a lap alján.

1. Tekintse át a módosításokat az összefoglalás lapon. Válassza az **áttelepítés befejezése** lehetőséget a névterek váltásához és az áttelepítés befejezéséhez.
    ![Névtér váltása – váltás menü][]  
    A megerősítő lap az áttelepítés befejezésekor jelenik meg.
    ![Névtér váltása – sikeres][]

## <a name="caveats"></a>Figyelmeztetések

A standard szintű Azure Service Bus által biztosított szolgáltatások némelyikét nem támogatja Azure Service Bus prémium szint. Ezeket úgy tervezték, mivel a prémium szint dedikált erőforrásokat kínál a kiszámítható átviteli sebességhez és a késéshez.

Az alábbi lista a prémium és az azok enyhítését nem támogató szolgáltatásokat tartalmazza.

### <a name="express-entities"></a>Expressz entitások

   Az olyan expressz entitások, amelyek nem véglegesítenek semmilyen üzenetet a tárolóba, nem támogatottak a prémium szintű támogatásban. A dedikált erőforrások jelentős teljesítménybeli javulást biztosítanak az adatok megőrzésének biztosításához, ahogy azt a vállalati üzenetkezelési rendszerek is elvárják.

   Az áttelepítés során a standard névtérben lévő expressz entitások bármelyike nem expressz entitásként jön létre a prémium szintű névtérben.

   Ha Azure Resource Manager-(ARM-) sablonokat használ, győződjön meg róla, hogy eltávolítja a "enableExpress" jelzőt a telepítési konfigurációból, hogy az automatizált munkafolyamatok hibák nélkül fussanak.

### <a name="partitioned-entities"></a>Particionált entitások

   A standard szintű particionált entitásokat a több-bérlős telepítés jobb rendelkezésre állásának biztosítása érdekében támogatta. Ha a prémium szintű csomagban elérhetővé kívánja tenni a dedikált erőforrásokat, akkor ez már nem szükséges.

   Az áttelepítés során a standard névtérben található particionált entitások nem particionált entitásként jönnek létre a prémium névtérben.

   Ha az ARM-sablon a "enablePartitioning" értéket "true" értékre állítja egy adott üzenetsor vagy témakör esetében, akkor azt a közvetítő figyelmen kívül hagyja.

## <a name="faqs"></a>Gyakori kérdések

### <a name="what-happens-when-the-migration-is-committed"></a>Mi történik az áttelepítés véglegesítése után?

Az áttelepítés véglegesítése után a standard névtérre mutató kapcsolódási sztring a prémium szintű névtérre mutat.

A küldő és fogadó alkalmazások le lesznek választva a standard névtérből, és automatikusan újra csatlakozhatnak a prémium szintű névtérhez.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Mi a teendő, ha a prémium szintű Migrálás befejeződött?

A prémium szintű áttelepítésre vonatkozó standard biztosítja, hogy az entitások metaadatait, például a témaköröket, az előfizetéseket és a szűrőket a standard névtérből a prémium szintű névtérbe másolja. A standard névtérbe véglegesített üzenet nem a standard névtérből a prémium szintű névtérbe lett másolva.

Előfordulhat, hogy a standard névtérben néhány üzenet lett elküldve és véglegesítve, miközben az áttelepítés megkezdődött. Manuálisan Ürítse ki ezeket az üzeneteket a standard névtérből, és manuálisan küldje el azokat a prémium szintű névtérnek. Az üzenetek manuális leürítéséhez használjon egy konzol alkalmazást vagy egy olyan parancsfájlt, amely az áttelepítési parancsokban megadott áttelepítési DNS-név használatával leüríti a szabványos névtér entitásait. Küldje el ezeket az üzeneteket a prémium szintű névtérbe, hogy a fogadók feldolgozhatják őket.

Az üzenetek kiürítését követően törölje a szabványos névteret.

>[!IMPORTANT]
> A standard névtérből származó üzenetek kiürítése után törölje a szabványos névteret. Ez azért fontos, mert a standard névtérhez eredetileg hivatkozott kapcsolódási sztring a prémium névtérre hivatkozik. Többé nem lesz szüksége a standard névtérre. Az áttelepített standard névtér törlése segít csökkenteni a későbbi zavart.

### <a name="how-much-downtime-do-i-expect"></a>Mennyi állásidőt várhatok?

Az áttelepítési folyamat célja, hogy csökkentse az alkalmazások várható leállását. A leállást a küldő és fogadó alkalmazások által az új prémium névtérre mutató kapcsolódási karakterlánc használatával csökkenti.

Az alkalmazás által tapasztalt állásidő arra a időre korlátozódik, ameddig a DNS-bejegyzés frissítéséhez szükséges, hogy a prémium szintű névtérre mutasson. A leállás körülbelül 5 percet vesz igénybe.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Kell-e módosítani a konfigurációt az áttelepítés során?

Nem, az áttelepítés elvégzéséhez nincs szükség kód-vagy konfigurációs módosításra. A küldő és fogadó alkalmazások által a standard névtér eléréséhez használt kapcsolati sztring automatikusan leképezve lesz, hogy aliasként működjön a prémium szintű névtérben.

### <a name="what-happens-when-i-abort-the-migration"></a>Mi történik az áttelepítés megszakításakor?

Az áttelepítés a `Abort` paranccsal vagy a Azure Portal használatával szakítható meg.

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Folyamat megszakítása – a szinkronizálás megszakítása folyamat megszakítása ][]
 ![ – Befejezés megszakítása][]

Az áttelepítési folyamat megszakadása esetén a megszakítja az entitások (témakörök, előfizetések és szűrők) másolásának folyamatát a standard és a prémium névtér között, és megszakítja a párosítást.

A kapcsolódási karakterlánc nem frissül, hogy a prémium szintű névtérre mutasson. A meglévő alkalmazásai továbbra is ugyanúgy működnek, mint az áttelepítés megkezdése előtt.

Azonban nem törli az entitásokat a prémium szintű névtérből, vagy törli a prémium szintű névteret. Manuálisan törölje az entitásokat, ha úgy döntött, hogy nem halad előre az áttelepítéssel.

>[!IMPORTANT]
> Ha úgy dönt, hogy megszakítja az áttelepítést, törölje az áttelepítés során kiépített prémium névteret, hogy ne kelljen fizetnie az erőforrásért.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nem szeretném lecsapolni az üzeneteket. Mit tegyek?

Előfordulhat, hogy a küldő alkalmazások elküldik az üzeneteket, és az áttelepítés véglegesítése előtt a standard névtérben lévő tárterületre kötelezik őket.

Az áttelepítés során a tényleges üzenet-és adattartalom nem másolódik a standard és a prémium névtér között. Az üzeneteket manuálisan kell lecsapolni, majd elküldeni a prémium szintű névtérbe.

Ha azonban egy tervezett karbantartási/kitakarítási időszakban is át tud térni, és nem szeretné manuálisan lecsapolni és elküldeni az üzeneteket, kövesse az alábbi lépéseket:

1. Állítsa le a küldő alkalmazásokat. A fogadó alkalmazások feldolgozzák a jelenleg a standard névtérben lévő üzeneteket, és leürítik a várólistát.
1. Miután a standard névtérben lévő várólisták és előfizetések üresek, kövesse a korábban ismertetett eljárást a standard és a prémium szintű névtér közötti áttelepítés végrehajtásához.
1. Az áttelepítés befejezése után újraindíthatja a küldő alkalmazásokat.
1. A küldők és a fogadók mostantól automatikusan csatlakoznak a prémium szintű névtérhez.

    >[!NOTE]
    > Nem kell leállítania a fogadó alkalmazásait az áttelepítéshez.
    >
    > Az áttelepítés befejezése után a fogadó alkalmazások le lesznek választva a standard névtérből, és automatikusan csatlakoznak a prémium szintű névtérhez.

## <a name="next-steps"></a>Következő lépések

* További információk a [standard és a prémium szintű üzenetkezelés közötti különbségekről](./service-bus-premium-messaging.md).
* Ismerje meg a [Service Bus Premium magas rendelkezésre állási és Geo-Disaster helyreállítási szempontjait](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Áttelepítési Kezdőlap]: ./media/service-bus-standard-premium-migration/1.png
[Telepítési névtér]: ./media/service-bus-standard-premium-migration/2.png
[Névtér beállítása – prémium szintű névtér létrehozása]: ./media/service-bus-standard-premium-migration/3.png
[Névtér beállítása – a kivételezés utáni áttelepítés neve]: ./media/service-bus-standard-premium-migration/4.png
[Telepítési névtér – entitások szinkronizálása – indítás]: ./media/service-bus-standard-premium-migration/5.png
[Telepítési névtér – entitások szinkronizálása – folyamat]: ./media/service-bus-standard-premium-migration/8.png
[Névtér váltása – váltás menü]: ./media/service-bus-standard-premium-migration/9.png
[Névtér váltása – sikeres]: ./media/service-bus-standard-premium-migration/12.png

[Folyamat megszakítása – szinkronizálás megszakítása]: ./media/service-bus-standard-premium-migration/abort1.png
[Folyamat megszakítása – a megszakítás befejeződött]: ./media/service-bus-standard-premium-migration/abort3.png
