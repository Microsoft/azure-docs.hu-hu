---
title: Migrálás Azure Resource authoring-kulcsba
titleSuffix: Azure Cognitive Services
description: Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a Language Understanding (LUIS) szerzői hitelesítését egy e-mail-fiókból egy Azure-erőforrásba.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787637"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrálás Azure Resource authoring-kulcsba

> [!IMPORTANT]
>  December 3. után a meglévő LUIS-felhasználóknak el kell végezniük az áttelepítési folyamatot a LUIS-alkalmazások készítésének folytatásához.

Language Understanding (LUIS) szerzői hitelesítés egy e-mail fiókból egy Azure-erőforrásra módosult. Ebből a cikkből megtudhatja, hogyan telepítheti át a fiókját, ha még nem telepítette át.  


## <a name="what-is-migration"></a>Mi az áttelepítés?

A Migrálás folyamata az e-mail-fiókból egy Azure-erőforrásra történő szerzői hitelesítés módosítására szolgál. A fiók a Migrálás után egy Azure-előfizetéshez és egy Azure authoring-erőforráshoz lesz társítva.

Az áttelepítést a [Luis portálról](https://www.luis.ai)kell elvégezni. Ha például a LUIS CLI használatával hozza létre a szerzői kulcsokat, az áttelepítési folyamatot a LUIS portálon kell végrehajtania. A Migrálás után továbbra is rendelkezhet közös szerzőkkel az alkalmazásaiban, de ezek az alkalmazások szintjének helyett az Azure-erőforrás szintjén lesznek hozzáadva. A fiók áttelepítése nem vonható vissza.

> [!Note]
> * Ha előrejelzés-futtatókörnyezeti erőforrást kell létrehoznia, a létrehozásához [külön folyamat](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) szükséges.
> * Az alkalmazások és a közreműködők érintett alkalmazásával kapcsolatos információkért tekintse meg az alábbi [áttelepítési megjegyzések](#migration-notes) szakaszt. 
> * A LUIS-alkalmazás készítése ingyenes, ahogy az a F0 szinten is szerepel. [További információ a díjszabási szintekről](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Migrálás előfeltételei

* Érvényes Azure-előfizetés. Kérje meg a bérlői rendszergazdát, hogy vegye fel Önt az előfizetésbe, vagy [regisztráljon egy ingyenes](https://azure.microsoft.com/free/cognitive-services)verzióra.
* A LUIS Azure authoring Resource a LUIS portálról vagy a [Azure Portalról](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * A LUIS portál létrehozási erőforrásának létrehozása a következő szakaszban ismertetett áttelepítési folyamat részét képezi.
* Ha az alkalmazásokban munkatársa, az alkalmazások nem lesznek automatikusan áttelepítve. A rendszer kérni fogja, hogy exportálja ezeket az alkalmazásokat az áttelepítési folyamat alatt. Használhatja az [export API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)-t is. A Migrálás után visszaimportálhatja az alkalmazást a LUIS-ba. Az importálási folyamat létrehoz egy új alkalmazást egy új alkalmazás-AZONOSÍTÓval, amelynek Ön a tulajdonosa.        
* Ha Ön az alkalmazás tulajdonosa, nem kell exportálnia az alkalmazásokat, mert azok automatikusan áttelepülnek. Meg kell adni egy e-mail-sablont az egyes alkalmazásokhoz tartozó közreműködők listájával, így az áttelepítési folyamatról értesítést kaphatnak.

## <a name="migration-steps"></a>A migrálás lépései

1. Amikor bejelentkezik a [Luis-portálra](https://www.luis.ai), megnyílik egy Azure áttelepítési ablak az áttelepítés lépéseivel. Ha elveti, nem tudja folytatni a LUIS-alkalmazások készítését, és az egyetlen megjelenő művelettel folytathatja az áttelepítést.

    > [!div class="mx-imgBorder"]
    > ![Áttelepítési ablak bemutatása](./media/migrate-authoring-key/notify-azure-migration.png)

2. Ha bármelyik alkalmazásában közreműködők vannak, akkor megjelenik az Ön tulajdonában lévő alkalmazások neveinek listája, valamint a szerzői régiók és a közreműködő e-mailek az egyes alkalmazásokban. Javasoljuk, hogy küldje el a közreműködőknek egy e-mailt, amely értesíti őket az áttelepítésről. ehhez kattintson a **küldési** szimbólum gombra az alkalmazás nevének bal oldalán.
`*`Az alkalmazás neve mellett egy szimbólum jelenik meg, ha egy közreműködőhöz hozzá van rendelve egy előrejelzési erőforrás az alkalmazáshoz. Az áttelepítés után ezek az alkalmazások továbbra is ezeket az előrejelzési erőforrásokat rendelik hozzájuk, annak ellenére, hogy a közreműködők nem férnek hozzá az alkalmazások létrehozásához. Ez a hozzárendelés azonban megszakad, ha az előrejelzési erőforrás tulajdonosa újra [generálta a kulcsokat](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) a Azure Portalból.  

   > [!div class="mx-imgBorder"]
   > ![Közreműködők értesítése](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Az egyes közreműködők és alkalmazások esetében az alapértelmezett e-mail-alkalmazás egy egyszerűsített formátumú e-mail-címmel nyílik meg. A küldés előtt szerkesztheti az e-maileket. Az e-mail-sablon tartalmazza az alkalmazás AZONOSÍTÓját és az alkalmazás nevét.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Miután áttelepítette a fiókját az Azure-ba, az alkalmazások már nem lesznek elérhetők a közreműködők számára.

3. Ha bármely alkalmazásban közreműködő, az Ön által megosztott alkalmazások neveinek listája a szerzői régió és a tulajdonos e-mail-címével együtt jelenik meg. Javasoljuk, hogy exportálja az alkalmazások egy másolatát az alkalmazás nevének bal oldalán található Exportálás gombra kattintva. Ezeket az alkalmazásokat az áttelepítés után vissza is importálhatja, mivel azok nem lesznek automatikusan áttelepítve.
`*`Az alkalmazás neve mellett egy szimbólum jelenik meg, ha egy alkalmazáshoz hozzárendelt előrejelzési erőforrás van rendelve. Az áttelepítés után az előrejelzési erőforrás továbbra is hozzá lesz rendelve ezekhez az alkalmazásokhoz, még akkor is, ha már nem lesz hozzáférése az alkalmazások létrehozásához. Ha meg szeretné szüntetni az előrejelzési erőforrás és az alkalmazás közötti hozzárendelést, akkor a Azure Portal, majd újra kell [generálnia a kulcsokat](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exportálja alkalmazásait.](./media/migrate-authoring-key/migration-export-apps.png)


4. A régiók áttelepítésének ablakában a rendszer arra kéri, hogy telepítse át az alkalmazásokat egy olyan Azure-erőforrásba, amely ugyanabban a régióban lett létrehozva. LUIS három szerzői régiót [és portált](./luis-reference-regions.md#luis-authoring-regions)tartalmaz. Az ablak megjeleníti azokat a régiókat, amelyeken a tulajdonában lévő alkalmazásokat létrehozták. A megjelenített áttelepítési régiók a használt regionális portáltól és a létrehozott alkalmazástól függően eltérőek lehetnek. 

   > [!div class="mx-imgBorder"]
   > ![Többrégiós áttelepítés.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Az egyes régiók esetében válassza az új LUIS authoring-erőforrás létrehozása lehetőséget, vagy a gombok használatával egy meglévőre migrálja őket.

   > [!div class="mx-imgBorder"]
   > ![Válassza a létrehozás vagy a meglévő szerzői erőforrás létrehozása lehetőséget.](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Adja meg az alábbi információkat:

   * **Bérlő neve**: az a bérlő, amelyhez az Azure-előfizetése társítva van. Alapértelmezés szerint ez az aktuálisan használt bérlőre van állítva. A bérlők átváltásához zárja be ezt az ablakot, és válassza ki az ikont a képernyő jobb felső részén, amely tartalmazza a monogramját. Kattintson az **áttelepítés az Azure** -ra lehetőségre az ablak újbóli megnyitásához.
   * **Azure-előfizetés neve**: az erőforráshoz társítandó előfizetés. Ha egynél több előfizetéssel rendelkezik, amely a bérlőhöz tartozik, válassza ki a kívánt elemet a legördülő listából.
   * **Szerzői erőforrás neve**: A kiválasztott egyéni név. Az URL-cím részeként használatos a szerzői műveletek és előrejelzési végpontok lekérdezéséhez. Ha új authoring-erőforrást hoz létre, vegye figyelembe, hogy az erőforrás neve csak alfanumerikus karaktereket tartalmazhat, `-` és nem kezdődhet vagy végződhet `-` . Ha bármilyen más szimbólum szerepel a névben, az erőforrások létrehozása és áttelepítése sikertelen lesz.
   * **Azure-erőforráscsoport neve**: a legördülő listából kiválasztott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez. Ha jelenleg nincs erőforráscsoport az előfizetésben, nem lehet létrehozni egyet a LUIS portálon. A bejelentkezési folyamat folytatásához lépjen a [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) , és hozzon létre egyet a Luis-ben.

6. Miután sikeresen áttelepítette az összes régióban, kattintson a Befejezés gombra. Ekkor hozzáférhet az alkalmazásaihoz. A portálon található összes régióban folytathatja az alkalmazások létrehozását és karbantartását.

## <a name="migration-notes"></a>Áttelepítési megjegyzések

* A Migrálás előtt a szerzők a LUIS-alkalmazás szintjén _közreműködőként_ ismertek. Az áttelepítés után a _közreműködő_ Azure-szerepköre ugyanarra a funkcióra van felhasználva az Azure-erőforrás szintjén.
* Ha több [Luis regionális portálra](./luis-reference-regions.md#luis-authoring-regions)jelentkezett be, a rendszer egyszerre több régióban is áttelepíti azt.
* Ha Ön az alkalmazás tulajdonosa, a rendszer automatikusan áttelepíti az alkalmazásokat. Az alkalmazások nem lesznek áttelepítve, ha Ön az alkalmazásban közreműködő. A rendszer azonban a közreműködőket is kéri a szükséges alkalmazások exportálására.
* Az alkalmazás tulajdonosai nem választhatják az áttelepíteni kívánt alkalmazások egy részhalmazát, így a tulajdonos nem tudja, hogy a közreműködők migrálva vannak-e.
* Az áttelepítés nem helyezi át automatikusan a közreműködőket az Azure authoring erőforrásba. Az alkalmazás tulajdonosa az, akinek az áttelepítés után el kell végeznie ezt a lépést. Ehhez a lépéshez [engedélyekre van szükség az Azure authoring-erőforráshoz](./luis-how-to-collaborate.md).
* Miután hozzárendelte a közreműködőket az Azure-erőforráshoz, át kell telepítenie őket, mielőtt hozzá tudnak férni az alkalmazásokhoz. Ellenkező esetben nem férhetnek hozzá az alkalmazások létrehozásához.


## <a name="using-apps-after-migration"></a>Alkalmazások használata az áttelepítés után

Az áttelepítési folyamat után minden olyan LUIS-alkalmazást, amelynek Ön a tulajdonosa, most már egyetlen LUIS authoring-erőforráshoz lesz hozzárendelve.
A **saját alkalmazások** lista az új szerzői erőforrásba áttelepített alkalmazásokat jeleníti meg. Az alkalmazások elérése előtt válassza a másik szerzői **erőforrás** kiválasztása lehetőséget az előfizetés és az authoring erőforrás kiválasztásához a megjelenő alkalmazások megtekintéséhez.

> [!div class="mx-imgBorder"]
> ![előfizetés és szerzői erőforrás kiválasztása](./media/migrate-authoring-key/select-sub-and-resource.png)


Ha programozott módon szeretné szerkeszteni az alkalmazásokat, szüksége lesz a szerzői kulcs értékeire. Ezeknek az értékeknek a megjelenítéséhez kattintson a **kezelés** gombra a Luis-portálon a képernyő tetején, majd válassza az **Azure-erőforrások** lehetőséget. A Azure Portal az erőforrás **kulcs és végpontok** lapján is elérhetők. Létrehozhat több szerzői erőforrást is, és hozzárendelheti őket ugyanabból a lapról.

## <a name="adding-contributors-to-authoring-resources"></a>Közreműködők hozzáadása az erőforrások létrehozásához

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Ismerje meg [, hogyan adhat hozzá közreműködőket](luis-how-to-collaborate.md) a szerzői erőforrásokhoz. A közreműködők hozzáférhetnek az adott erőforráshoz tartozó összes alkalmazáshoz.

Az adott erőforrás **Access Control (iam)** lapján hozzáadhat közreműködőket a szerzői erőforráshoz Azure Portal. További információ: [közreműködők hozzáadása az alkalmazáshoz](luis-how-to-collaborate.md).

> [!Note]
> Ha a LUIS-alkalmazás tulajdonosa át lett telepítve, és hozzáadta a közreműködőt az Azure-erőforráshoz, akkor a közreműködő továbbra sem férhet hozzá az alkalmazáshoz, kivéve, ha azok migrálása is megtörténik.

## <a name="troubleshooting-the-migration-process"></a>Az áttelepítési folyamat hibaelhárítása

Ha nem találja az Azure-előfizetését a legördülő listában:
* Győződjön meg arról, hogy rendelkezik olyan érvényes Azure-előfizetéssel, amely jogosult Cognitive Services erőforrások létrehozására. Lépjen a [Azure Portalra](https://ms.portal.azure.com) , és keresse meg az előfizetés állapotát. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/cognitive-services/).
* Győződjön meg arról, hogy az érvényes előfizetéséhez társított megfelelő bérlő van. A bérlőket a képernyő jobb felső részén, a monogramját tartalmazó ikon kiválasztásával állíthatja be.

  > [!div class="mx-imgBorder"]
  > ![A címtárak váltására szolgáló oldal](./media/migrate-authoring-key/switch-directories.png)

Ha meglévő authoring-erőforrással rendelkezik, de nem találja, akkor a **meglévő authoring Resource használata** lehetőség kiválasztásával:
* Az erőforrás valószínűleg egy másik régióban lett létrehozva, mint amit a be szeretne migrálni.
* Ehelyett hozzon létre egy új erőforrást a LUIS-portálon.

Ha bejelöli az **új szerzői erőforrás létrehozása** lehetőséget, és a következő hibaüzenet jelenik meg: "nem sikerült beolvasni a felhasználó Azure-adatait, próbálkozzon újra később":
* Az előfizetés régiónként legalább 10 Author-erőforrással rendelkezhet. Ebben az esetben nem fog tudni új authoring-erőforrást létrehozni.
* Az áttelepítéshez válassza a **meglévő authoring Resource használata** lehetőséget, és válassza ki az előfizetéshez tartozó meglévő erőforrások egyikét.

## <a name="create-new-support-request"></a>Új támogatási kérelem létrehozása

Ha olyan problémák merülnek fel az áttelepítéssel kapcsolatban, amelyek nem szerepelnek a hibaelhárítás szakaszban, [hozzon létre egy támogatási témakört](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) , és adja meg az alábbi adatokat az alábbi mezőkkel:

   * **Probléma típusa**: technikai
   * **Előfizetés**: válasszon egy előfizetést a legördülő listából
   * **Szolgáltatás**: keresés és kijelölés "Cognitive Services"
   * **Erőforrás**: válassza ki a Luis-erőforrást, ha van már egy meglévő. Ha nem, válassza az általános kérdés lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [A szerzői és futtatókörnyezeti kulcsokkal kapcsolatos fogalmak](luis-how-to-azure-subscription.md) áttekintése
* A [kulcsok hozzárendelésének](luis-how-to-azure-subscription.md) és a [közreműködők hozzáadásának](luis-how-to-collaborate.md) áttekintése