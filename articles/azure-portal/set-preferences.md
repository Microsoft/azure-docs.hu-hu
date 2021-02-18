---
title: Az Azure Portal beállításainak kezelése
description: Módosíthatja Azure Portal alapértelmezett beállításait, hogy megfeleljen a saját preferenciáinak. A beállítások közé tartozik az inaktív munkamenet időkorlátja, az alapértelmezett nézet, a menü mód, a kontraszt, a téma, az értesítések és a nyelv és a területi
keywords: beállítások, időtúllépés, nyelv, regionális
ms.date: 08/05/2020
ms.topic: how-to
ms.openlocfilehash: 591b1ce6c2c069f9c0266588bf32f7303bf49708
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588792"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Az Azure Portal beállításainak kezelése

Módosíthatja a Azure Portal alapértelmezett beállításait, hogy azok megfeleljenek a saját preferenciáinak. A legtöbb beállítás a globális oldal fejlécének **Beállítások** menüjéből érhető el.

![Képernyőfelvétel a globális oldal fejlécének ikonjairól a Kiemelt beállításokkal](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>Válassza ki az alapértelmezett előfizetést

A Azure Portalba való bejelentkezéskor alapértelmezés szerint megnyithatja az előfizetést. Ez akkor hasznos, ha rendelkezik egy elsődleges előfizetéssel, de alkalmanként másokat használ. 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="Erőforrások listájának szűrése előfizetés alapján.":::

1. Válassza ki a címtár és előfizetés szűrő ikont a globális oldal fejlécében.

1. A portál indításakor válassza ki az alapértelmezett előfizetésként használni kívánt előfizetéseket. 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="A portál indításakor válassza ki az alapértelmezett előfizetésként használni kívánt előfizetéseket."::: 


## <a name="choose-your-default-view"></a>Válassza ki az alapértelmezett nézetet 

A Azure Portalba való bejelentkezéskor alapértelmezés szerint a megnyíló lap módosítható.

![Képernyőfelvétel – az alapértelmezett nézettel Azure Portal beállítások megjelenítése](./media/set-preferences/default-view.png)

- A **Kezdőlap** nem szabható testre.  Megjeleníti a népszerű Azure-szolgáltatásokra mutató parancsikonokat, és felsorolja a legutóbb használt erőforrásokat. Hasznos hivatkozásokat is biztosítunk az olyan erőforrásokhoz, mint a Microsoft Learn és az Azure-útiterv.

- Az irányítópultok testreszabhatók úgy, hogy csak Ön számára kialakított munkaterületet hozzanak létre. Létrehozhat például egy projekt, feladat vagy szerepkör fókuszban lévő irányítópultot. Ha kijelöli az **irányítópultot**, az alapértelmezett nézet a legutóbb használt irányítópultra lép. További információ: [irányítópultok létrehozása és megosztása a Azure Portalban](azure-portal-dashboards.md).

## <a name="choose-a-portal-menu-mode"></a>Portál menü mód kiválasztása

A portál menü alapértelmezett módja határozza meg, hogy a portál menüjében mennyi lemezterületet vesz fel az oldal.

![Képernyőkép, amely bemutatja, hogyan állíthatja be a portál menü alapértelmezett módját.](./media/set-preferences/menu-mode.png)

- Ha a portál menüje a **kilépési módban van** , akkor a rendszer rejtve marad, amíg nincs rá szüksége. Válassza a menü ikont a menü megnyitásához vagy bezárásához.

- Ha a portál menü **dokkolt mód** elemét választja, mindig látható. A menü összecsukásával több munkaterületet is megadhat.

## <a name="choose-a-theme-or-enable-high-contrast"></a>Válasszon egy témát, vagy engedélyezze a kontrasztos megjelenítést

A kiválasztott téma befolyásolja a Azure Portalban megjelenő háttér-és betűszínt. A négy beállított színtéma közül választhat. Válassza ki az egyes miniatűrket, és keresse meg az Önnek legmegfelelőbb témát.

Azt is megteheti, hogy kiválaszthatja a nagy kontrasztú témák egyikét. A kontrasztos megjelenítéssel a Azure Portal könnyebben olvashatók a vizualizációk miatti személyek számára. felülbírálják az összes többi téma kijelölését.

![A Kiemelt témákkal rendelkező Azure Portal beállításokat bemutató képernyőkép](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>Előugró értesítések engedélyezése vagy letiltása

Az értesítések az aktuális munkamenethez kapcsolódó rendszerüzenetek. Olyan információkat biztosítanak, mint a jelenlegi kredit egyenleg, ha az imént létrehozott erőforrások elérhetővé válnak, vagy megerősítik az utolsó műveletet, például:. Amikor bekapcsolják az előugró értesítéseket, a képernyő felső sarkában lévő üzenetek röviden megjelennek. 

Az előugró értesítések engedélyezéséhez vagy letiltásához válassza vagy törölje az **előugró értesítések engedélyezése** lehetőséget.

![Az előugró értesítések kiemelve Azure Portal beállításait bemutató képernyőkép](./media/set-preferences/popup-notifications.png)

Az aktuális munkamenet során fogadott értesítések olvasásához válassza az **értesítések** elemet a globális fejlécből.

![A Azure Portal globális fejlécét bemutató képernyőkép](./media/set-preferences/read-notifications.png)

Ha szeretné beolvasni az előző munkamenetek értesítéseit, keresse meg az eseményeket a tevékenység naplójában. További információ: [a tevékenység naplójának megtekintése](../azure-monitor/essentials/activity-log.md#view-the-activity-log). 

## <a name="change-the-inactivity-timeout-setting"></a>Az inaktivitás időtúllépési beállításának módosítása

Az inaktivitás időtúllépési beállítása segít megvédeni az erőforrásokat a jogosulatlan hozzáféréstől, ha elfelejti a munkaállomás védelmét. Miután elvégezte a tétlenséget, automatikusan kijelentkezik a Azure Portal-munkamenetből. Egyéni beállításként saját kezűleg is módosíthatja az időtúllépési beállítást. Ha Ön rendszergazda, a címtárban megadhatja az összes felhasználójának a címtár szintjén.

### <a name="change-your-individual-timeout-setting-user"></a>Egyéni időtúllépési beállítás módosítása (felhasználó)

Válassza ki a legördülő menüből a kijelentkezés az **inaktív állapotból** lehetőséget. Válassza ki azt az időtartamot, amely után a Azure Portal-munkamenet ki van jelentkezve, ha tétlen.

![Képernyőkép a portál beállításairól az inaktív időtúllépési beállítások kiemelésével](./media/set-preferences/inactive-signout-user.png)

A rendszer automatikusan menti a változást. Ha tétlen, akkor a Azure Portal-munkamenet a beállított időtartam után kijelentkezik.

Ha a rendszergazda engedélyezte a tétlenségi időtúllépési szabályzatot, akkor továbbra is beállíthatja a sajátját, ha az kisebb, mint a címtár-szintű beállítás. Válassza **a könyvtár inaktivitás időtúllépési házirendjének felülbírálása** lehetőséget, majd állítsa be az időintervallumot.

![A portál beállításait megjelenítő képernyőkép, amely felülbírálja a címtár inaktivitási időtúllépési házirend-beállítását.](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>A könyvtár időtúllépési beállításának módosítása (rendszergazda)

A [globális rendszergazdai szerepkör rendszergazdái](../active-directory/roles/permissions-reference.md#global-administrator) a munkamenet kijelentkezése előtt a maximális üresjárati időt is kikényszerítik. Az inaktivitás időtúllépési beállítása a könyvtár szintjén érvényes. A beállítás érvénybe lép az új munkamenetek esetében. Azonnal nem lesz érvényes a már bejelentkezett felhasználókra. További információ a címtárakról: [Active Directory Domain Services Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

Ha Ön globális rendszergazda, és a Azure Portal összes felhasználójának üresjárati időtúllépési beállítását szeretné kényszeríteni, kövesse az alábbi lépéseket:

1. Válassza ki a hivatkozás szövegét a **könyvtár szintjének időtúllépése beállításnál**.

    ![A portál beállításainak megjelenítése a hivatkozás szövegének kiemelésével](./media/set-preferences/settings-admin.png)

1. A **könyvtár szintjének inaktivitásának beállítása** lapon jelölje be **a címtár szintjének üresjárati időkorlátjának engedélyezése a Azure Portal** számára jelölőnégyzetet a beállítás bekapcsolásához.

1. Ezután adja meg az **órákat** és a **perceket** azon maximális időtartamig, ameddig a felhasználó tétlen lehet, mielőtt a munkamenet automatikusan kijelentkezett.

1. Kattintson az **Alkalmaz** gombra.

    ![A címtár-szintű inaktivitás időtúllépésének beállítására szolgáló lap képernyőképe](./media/set-preferences/configure.png)

Annak ellenőrzéséhez, hogy az inaktivitás időtúllépési szabályzata helyesen van-e beállítva, válassza az **értesítések** elemet a globális oldal fejlécében. Ellenőrizze, hogy megjelenik-e a sikeres értesítések listája.

![A címtár szintű inaktivitás időkorlátjának sikeres értesítési üzenetét bemutató képernyőkép](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>Alapértelmezett beállítások visszaállítása

Ha módosította a Azure Portal beállításokat, és el szeretné vetni őket, válassza az **alapértelmezett beállítások visszaállítása** lehetőséget. A portál beállításaiban végrehajtott módosítások elvesznek. Ez a beállítás nem befolyásolja az irányítópultok testreszabását.

![Az alapértelmezett beállítások visszaállítását bemutató képernyőkép](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>Felhasználói beállítások exportálása

Az egyéni beállításokkal kapcsolatos információkat az Azure tárolja. A következő felhasználói adatértékek exportálhatók:

* Privát irányítópultok a Azure Portal
* Felhasználói beállítások, például kedvenc előfizetések vagy címtárak, valamint az utolsó bejelentkezett címtár
* Témák és egyéb egyéni portál beállítások

Érdemes exportálni és áttekinteni a beállításokat, ha azt tervezi, hogy törli őket. Az irányítópultok újraépítése vagy a beállítások elvégzése időigényes lehet.

A portál beállításainak exportálásához válassza az **összes beállítás exportálása** lehetőséget.

![A beállítások exportálását bemutató képernyőkép](./media/set-preferences/useful-links-export-settings.png)

Az exportálási beállítások egy *. JSON* fájlt hoznak létre, amely tartalmazza a felhasználói beállításokat, például a színes témát, a kedvenceket és a privát irányítópultokat. A felhasználói beállítások dinamikus jellege és az adatsérülés kockázata miatt nem importálhatja a beállításokat a *. JSON* fájlból.

## <a name="delete-user-settings-and-dashboards"></a>Felhasználói beállítások és irányítópultok törlése

Az egyéni beállításokkal kapcsolatos információkat az Azure tárolja. A következő felhasználói adatértékeket törölheti:

* Privát irányítópultok a Azure Portal
* Felhasználói beállítások, például kedvenc előfizetések vagy címtárak, valamint az utolsó bejelentkezett címtár
* Témák és egyéb egyéni portál beállítások

A törlés előtt érdemes exportálni és áttekinteni a beállításait. Az irányítópultok újraépítése vagy az egyéni beállítások elvégzése időigényes lehet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

A portál beállításainak törléséhez válassza az **összes beállítás és privát irányítópultok törlése** lehetőséget.

![A beállítások törlését bemutató képernyőkép](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>Nyelvi és területi beállítások módosítása

Két olyan beállítás van, amely azt szabályozza, hogy a Azure Portal szövege hogyan jelenik meg: 
- A **nyelvi** beállítás határozza meg, hogy milyen nyelven jelenik meg a szöveg a Azure Portalban. 

- A **területi formátum** szabályozza a dátumok, az idő, a számok és a pénznem megjelenítésének módját.

A Azure Portal használt nyelv módosításához a legördülő listából válassza ki az elérhető nyelvek listáját.

A területi formátum kiválasztása úgy változik, hogy csak a kiválasztott nyelvhez tartozó területi beállítások jelenjenek meg. Az Automatikus kijelölés módosításához a legördülő menüben válassza ki a kívánt területi formátumot.

Ha például az angol nyelvet választja, majd a területi formátumot Egyesült Államok választja, akkor a pénznem amerikai dollárban jelenik meg. Ha az angol nyelvet választja, és az Európai területi formátumot választja, akkor a pénznem euróban jelenik meg.

Az **alkalmaz** gombra kattintva frissítheti a nyelvi és a területi formátum beállításait.

   ![A nyelvi és a területi formátum beállításait bemutató képernyőkép](./media/set-preferences/language.png)

>[!NOTE]
>Ezek a nyelvi és területi beállítások csak a Azure Portal érintik. Az új lapon vagy ablakban megnyitott dokumentációs hivatkozások a böngésző nyelvi beállításai segítségével határozzák meg a megjelenítendő nyelvet.
>

## <a name="next-steps"></a>Következő lépések

- [Billentyűparancsok a Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Támogatott böngészők és eszközök](azure-portal-supported-browsers-devices.md)
- [Kedvencek hozzáadása, eltávolítása és átrendezése](azure-portal-add-remove-sort-favorites.md)
- [Egyéni irányítópultok létrehozása és megosztása](azure-portal-dashboards.md)
- [Az Azure Portal útmutató videósorozata](azure-portal-video-series.md)
