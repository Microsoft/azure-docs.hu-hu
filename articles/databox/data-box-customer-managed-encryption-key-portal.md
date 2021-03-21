---
title: Az ügyfél által felügyelt kulcsok kezelése a Azure Portal használatával Azure Data Box
description: Megtudhatja, hogyan hozhat létre és kezelhet Azure Portal az ügyfelek által felügyelt kulcsokat az Azure Data Box Azure Key Vault használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791544"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Ügyfél által felügyelt kulcsok használata Azure Key Vaultban Azure Data Box

Azure Data Box védi az eszköz zárolási kulcsát (más néven eszköz jelszavát), amely egy eszköz zárolására szolgál a titkosítási kulcs használatával. Alapértelmezés szerint ez a titkosítási kulcs egy Microsoft által felügyelt kulcs. A további vezérléshez használhatja az ügyfél által felügyelt kulcsot.

Az ügyfél által felügyelt kulcs használata nem befolyásolja az eszközön lévő adattitkosítást. Ez csak azt befolyásolja, hogy az eszköz feloldási kulcsa titkosítva van-e.

Ha meg szeretné tartani ezt a szintű vezérlést a megrendelés folyamata során, használja az ügyfél által felügyelt kulcsot a rendelés létrehozásakor. További információ: [oktatóanyag: Order Azure Data Box](data-box-deploy-ordered.md).

Ez a cikk bemutatja, hogyan engedélyezhető az ügyfél által felügyelt kulcs a meglévő Data Box sorrendjéhez a [Azure Portalban](https://portal.azure.com/). Megtudhatja, hogyan módosíthatja a jelenlegi ügyfél által felügyelt kulcs kulcstartóját, kulcsát, verzióját vagy identitását, vagy visszatérhet a Microsoft által felügyelt kulcs használatára.

Ez a cikk Azure Data Box és Azure Data Box Heavy eszközökre vonatkozik.

## <a name="requirements"></a>Követelmények

Data Box rendelés ügyfél által felügyelt kulcsának a következő követelményeknek kell megfelelnie:

- A kulcsot egy olyan Azure Key Vault kell létrehozni és tárolni, amely helyreállítható **törlést** tartalmaz, és nem engedélyezi a **kiürítést** . További információ: [Mi az Azure Key Vault?](../key-vault/general/overview.md). A rendelés létrehozásakor vagy frissítésekor létrehozhat egy kulcstartót és egy kulcsot is.

- A kulcsnak 2048 méretű vagy nagyobb RSA-kulcsnak kell lennie.

## <a name="enable-key"></a>Kulcs engedélyezése

Az alábbi lépéseket követve engedélyezheti az ügyfél által felügyelt kulcsokat a meglévő Data Box sorrendjéhez a Azure Portalban:

1. Lépjen az **áttekintő** képernyőre a Data Box sorrendjéhez.

    ![Egy Data Box Order-1 áttekintő képernyő](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Lépjen a **beállítások > titkosítás** elemre, és válassza az **ügyfél által felügyelt kulcs** elemet. Ezután válassza **a kulcs és kulcstartó kiválasztása** lehetőséget.

    ![Az ügyfél által felügyelt kulcs titkosítási beállításának kiválasztása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Az előfizetést a **Azure Key Vault** képernyőről automatikusan kitölti a rendszer.

 3. A **Key Vault** esetében kijelölhet egy meglévő kulcstartót a legördülő listából, vagy kiválaszthatja az **új létrehozása** lehetőséget, és létrehozhat egy új kulcstartót.

     ![Key Vault-beállítások ügyfél által felügyelt kulcs kiválasztásakor](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Új kulcstartó létrehozásához adja meg az előfizetést, az erőforráscsoportot, a kulcstároló nevét és az **új Key Vault létrehozása** képernyőn megjelenő további információkat. A **helyreállítási beállítások** területen győződjön meg arról, hogy a **Soft delete** és a **Purge Protection** engedélyezve van. Ezután válassza a **felülvizsgálat + létrehozás** elemet.

      ![Azure Key Vault áttekintése és létrehozása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Tekintse át a kulcstartó adatait, majd válassza a **Létrehozás** lehetőséget. Várjon pár percet, amíg a Key Vault létrehozása befejeződik.

       ![Azure Key Vault létrehozása a beállításokkal](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. A **kulcs kiválasztása Azure Key Vault** képernyőn kiválaszthat egy meglévő kulcsot a kulcstartóból, vagy újat is létrehozhat.

    ![Válassza ki a kulcsot Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Ha új kulcsot szeretne létrehozni, válassza az **új létrehozása** lehetőséget. RSA-kulcsot kell használnia. A méret 2048 vagy nagyobb lehet.

    ![Új kulcs létrehozása Azure Key Vaultban](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Adja meg az új kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás** lehetőséget. Értesítést kap arról, hogy a Key vaultban létrehozta a kulcsot.

    ![Új kulcs neve](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. A **verzióban** a legördülő listából választhat egy meglévő verziót.

    ![Új kulcs verziójának kiválasztása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Ha új kulcs-verziót szeretne létrehozni, válassza az **új létrehozása** lehetőséget.

    ![Nyisson meg egy párbeszédpanelt új kulcs verziójának létrehozásához](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Válassza ki az új kulcs verziójának beállításait, majd válassza a **Létrehozás** lehetőséget.

    ![Új kulcs verziójának létrehozása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Ha a Key Vault, a kulcs és a kulcs verzióját választotta, válassza a **kiválasztás** lehetőséget.

    ![Egy kulcs egy Azure Key Vaultban](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    A **titkosítási típus** beállításai a kiválasztott Key vaultot és kulcsot mutatják.

    ![Ügyfél által felügyelt kulcs és kulcstartó](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Válassza ki az erőforráshoz az ügyfél által felügyelt kulcs kezeléséhez használandó identitás típusát. Használhatja a rendelés létrehozásakor létrehozott **rendszerhez rendelt** identitást, vagy kiválaszthat egy felhasználó által hozzárendelt identitást.

    A felhasználó által hozzárendelt identitás egy független erőforrás, amelyet az erőforrásokhoz való hozzáférés kezelésére használhat. További információ: [felügyelt identitások típusai](../active-directory/managed-identities-azure-resources/overview.md).

    ![Válassza ki az identitás típusát](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Felhasználói identitás hozzárendeléséhez válassza ki a **felhasználó által hozzárendelt** elemet. Ezután válassza ki a **felhasználói identitás kiválasztása** lehetőséget, majd válassza ki a használni kívánt felügyelt identitást.

    ![Válassza ki a használni kívánt identitást](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Itt nem hozhat létre új felhasználói azonosítót. Ha szeretné megtudni, hogyan hozhat létre egyet, tekintse meg a [szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)című témakört.

    A kiválasztott felhasználói identitás megjelenik a **titkosítási típus** beállításai között.

    ![A titkosítási típus beállításaiban megjelenő kiválasztott felhasználói azonosító](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. A frissített **titkosítási típus** beállításainak mentéséhez válassza a **Mentés** lehetőséget.

     ![Az ügyfél által felügyelt kulcs mentése](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    A kulcs URL-címe a **titkosítás típusa** alatt jelenik meg.

    ![Ügyfél által felügyelt kulcs URL-címe](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Kulcs módosítása

A jelenleg használt ügyfél által felügyelt kulcs kulcstartójának, kulcsának és/vagy verziószámának módosításához kövesse az alábbi lépéseket:

1. A Data Box sorrendjének **áttekintése** képernyőn válassza a **Beállítások**  >  **titkosítás** lehetőséget, majd kattintson a **kulcs módosítása** elemre.

    ![Az ügyfél által felügyelt kulccsal rendelkező Data Box rendelés áttekintési képernyője – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Válassza **a másik kulcstartó és kulcs kiválasztása** lehetőséget.

    ![Egy Data Box sorrend áttekintő képernyője, válasszon egy másik kulcs-és kulcstartó-beállítást.](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. A Key **Vault-képernyő Select (kulcs kiválasztása** ) képernyőjén látható az előfizetés, a Key Vault, a Key vagy a Key verzió nem. A következő módosítások bármelyikét elvégezheti:

   - Válasszon másik kulcsot ugyanabból a kulcstartóból. A kulcs és a verzió kiválasztása előtt ki kell választania a kulcstartót.

   - Válasszon egy másik kulcstartót, és rendeljen hozzá egy új kulcsot.

   - Módosítsa az aktuális kulcs verzióját.
   
    A módosítások befejezése után válassza a **kiválasztás** lehetőséget.

    ![Titkosítási lehetőség választása – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Kattintson a **Mentés** gombra.

    ![Frissített titkosítási beállítások mentése – 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Identitás módosítása

Az ügyfél által felügyelt kulcshoz való hozzáférés kezeléséhez használt identitás módosításához kövesse az alábbi lépéseket:

1. A befejezett Data Box rendelés **Áttekintés** képernyőjén válassza a **Beállítások**  >  **titkosítás** lehetőséget.

2. Hajtsa végre a következő módosítások egyikét:

     - Ha másik felhasználói identitásra szeretne váltani, kattintson **a válasszon másik felhasználói identitást** lehetőségre. Ezután válasszon másik identitást a képernyő jobb oldalán lévő panelen, majd válassza a **kiválasztás** lehetőséget.

       ![Az ügyfél által felügyelt kulcshoz tartozó felhasználó által hozzárendelt identitás módosításának lehetősége](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Ha a rendelés létrehozása során létrehozott rendszerhez hozzárendelt identitásra szeretne váltani, válassza a **rendszer által hozzárendelt** identitás **típusának kiválasztása** lehetőséget.

     ![Az ügyfél által felügyelt kulcshoz hozzárendelt rendszerre való váltás lehetősége](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Kattintson a **Mentés** gombra.

    ![Frissített titkosítási beállítások mentése – 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>A Microsoft által felügyelt kulcs használata

Ha az ügyfél által felügyelt kulcs használatával szeretné megváltoztatni a Microsoft által felügyelt kulcsot a rendeléshez, kövesse az alábbi lépéseket:

1. A befejezett Data Box rendelés **Áttekintés** képernyőjén válassza a **Beállítások**  >  **titkosítás** lehetőséget.

2. A **típus kiválasztásával** válassza a **Microsoft által felügyelt kulcs** lehetőséget.

    ![A Data Box Orders-5 áttekintése képernyő](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Kattintson a **Mentés** gombra.

    ![Microsoft által felügyelt kulcs frissített titkosítási beállításainak mentése](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Hibák elhárítása

Ha az ügyfél által felügyelt kulccsal kapcsolatos hibákat kap, a hibaelhárításhoz használja a következő táblázatot.

| Hibakód| A hiba részletei| Helyreállítható?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nem sikerült beolvasni a hitelesítő kulcsot, mert a felhasználó által felügyelt kulcs le van tiltva.| Igen, a kulcs verziójának engedélyezésével.|
| SsemUserErrorEncryptionKeyExpired| A hitelesítő kulcs beolvasása sikertelen volt, mert az ügyfél által felügyelt kulcs lejárt.| Igen, a kulcs verziójának engedélyezésével.|
| SsemUserErrorKeyDetailsNotFound| Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcs.| Ha törölte a kulcstartót, az ügyfél által felügyelt kulcs nem állítható helyre.  Ha áttelepítette a kulcstartót egy másik bérlőre, tekintse meg a [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../key-vault/general/move-subscription.md)című témakört. Ha törölte a Key vaultot:<ol><li>Igen, ha a kiürítés-védelem időtartama alatt van, a [Key Vault helyreállításának](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)lépéseit követve.</li><li>Nem, ha meghaladja a kiürítési-védelmi időtartamot.</li></ol><br>Ha a Key Vault bérlői áttelepítést hajtott végre, igen, az alábbi lépések egyikével állítható helyre: <ol><li>A Key Vault visszaállítása a régi bérlőre.</li><li>Állítsa be `Identity = None` , majd állítsa vissza az értéket `Identity = SystemAssigned` . Ezzel törli és újból létrehozza az identitást az új identitás létrehozása után. A `Get` `Wrap` `Unwrap` Key Vault hozzáférési házirendjében engedélyezze az új identitás engedélyezését és engedélyeit.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Egy ügyfél által felügyelt kulcs alkalmazva, de a kulcshoz való hozzáférés nem lett megadva vagy vissza lett vonva, vagy nem fér hozzá a Key vaulthoz, mert a tűzfal engedélyezve van. | Adja hozzá a Key vaulthoz kiválasztott identitást, hogy engedélyezze a hozzáférést az ügyfél által felügyelt kulcshoz. Ha a Key vaultban engedélyezve van a tűzfal, váltson át egy rendszerhez rendelt identitásra, majd vegyen fel egy ügyfél által felügyelt kulcsot. További információ: [a kulcs engedélyezése](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcshoz tartozó kulcstároló. | Ha törölte a kulcstartót, az ügyfél által felügyelt kulcs nem állítható helyre.  Ha áttelepítette a kulcstartót egy másik bérlőre, tekintse meg a [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../key-vault/general/move-subscription.md)című témakört. Ha törölte a Key vaultot:<ol><li>Igen, ha a kiürítés-védelem időtartama alatt van, a [Key Vault helyreállításának](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)lépéseit követve.</li><li>Nem, ha meghaladja a kiürítési-védelmi időtartamot.</li></ol><br>Ha a Key Vault bérlői áttelepítést hajtott végre, igen, az alábbi lépések egyikével állítható helyre: <ol><li>A Key Vault visszaállítása a régi bérlőre.</li><li>Állítsa be `Identity = None` , majd állítsa vissza az értéket `Identity = SystemAssigned` . Ezzel törli és újból létrehozza az identitást az új identitás létrehozása után. A `Get` `Wrap` `Unwrap` Key Vault hozzáférési házirendjében engedélyezze az új identitás engedélyezését és engedélyeit.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcs.| Igen, ellenőrizze, hogy: <ol><li>A Key Vault továbbra is az MSI szerepel a hozzáférési házirendben.</li><li>Az identitás típusa rendszer van hozzárendelve.</li><li>Engedélyezze a Get, a wrap és a dewrap engedélyeket a Key Vault hozzáférési házirendjében lévő identitáshoz.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Nem sikerült hozzáadni az új felhasználóhoz rendelt identitást, mert elérte a hozzárendelhető felhasználói identitások teljes számának korlátját. | Próbálja megismételni a műveletet kevesebb felhasználói identitással, vagy távolítson el néhány felhasználóhoz rendelt identitást az erőforrásból az újrapróbálkozás előtt. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | A felügyelt identitás-hozzáférési művelet meghiúsult. <br> Megjegyzés: Ez arra az esetre vonatkozik, ha az előfizetést másik bérlőre helyezik át. Az ügyfélnek manuálisan kell áthelyeznie az identitást az új bérlőbe. További részleteket a PFA-mailben talál. | Helyezze át a kiválasztott identitást arra az új bérlőre, amelynek az előfizetése megtalálható. További információ: [a kulcs engedélyezése](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Egy ügyfél által felügyelt kulcs lett alkalmazva, de a kulcshoz hozzáféréssel rendelkező felhasználóhoz rendelt identitás nem található az Active Directoryban. <br> Megjegyzés: Ez arra az esetre, ha a felhasználói identitást törölték az Azure-ból.| A Key vaulthoz való hozzáférés engedélyezéséhez próbáljon meg egy másik felhasználó által hozzárendelt identitást hozzáadni, hogy engedélyezze a hozzáférést az ügyfél által felügyelt kulcshoz. További információ: [a kulcs engedélyezése](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcs. | Nem sikerült hozzáférni az ügyfél által felügyelt kulcshoz. A kulcshoz társított felhasználóhoz rendelt identitás (UAI) törölve lett, vagy módosult a UAI típusa. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | A felügyelt identitás-hozzáférési művelet meghiúsult. <br> Megjegyzés: Ez arra az esetre vonatkozik, ha az előfizetést másik bérlőre helyezik át. Az ügyfélnek manuálisan kell áthelyeznie az identitást az új bérlőbe. További részleteket a PFA-mailben talál. | A Key vaulthoz való hozzáférés engedélyezéséhez próbáljon meg egy másik felhasználó által hozzárendelt identitást hozzáadni, hogy engedélyezze a hozzáférést az ügyfél által felügyelt kulcshoz. További információ: [a kulcs engedélyezése](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Egy ügyfél által felügyelt kulcs alkalmazva, de a kulcshoz való hozzáférés nem lett megadva vagy vissza lett vonva, vagy nem fér hozzá a Key vaulthoz, mert a tűzfal engedélyezve van. | Adja hozzá a Key vaulthoz kiválasztott identitást, hogy engedélyezze a hozzáférést az ügyfél által felügyelt kulcshoz. Ha a Key vaultban engedélyezve van a tűzfal, váltson át egy rendszerhez rendelt identitásra, majd vegyen fel egy ügyfél által felügyelt kulcsot. További információ: [a kulcs engedélyezése](#enable-key). |
| Általános hiba  | A hitelesítő kulcs beolvasása sikertelen.| Ez egy általános hiba. A hiba megoldásához forduljon a Microsoft ügyfélszolgálatahoz, és határozza meg a következő lépéseket.|

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure Key Vault?](../key-vault/general/overview.md)
- [Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból az Azure Portal használatával](../key-vault/secrets/quick-create-portal.md)
