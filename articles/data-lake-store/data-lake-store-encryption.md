---
title: Titkosítás a Azure Data Lake Storage Gen1ban | Microsoft Docs
description: A Azure Data Lake Storage Gen1-titkosítás segít az adatai védelmében, vállalati biztonsági szabályzatok megvalósításában és a szabályozási megfelelőségi követelmények teljesítésében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 51c9f924c6fe3ac6db86d60e26749d35bc4ab733
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108944"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1ban lévő adattitkosítás

A Azure Data Lake Storage Gen1-titkosítás segít az adatai védelmében, vállalati biztonsági szabályzatok megvalósításában és a szabályozási megfelelőségi követelmények teljesítésében. Ez a cikk áttekintést nyújt a kialakításról, és ismerteti az implementálás egyes technikai aspektusait.

Data Lake Storage Gen1 támogatja az adatok titkosítását mind a nyugalmi, mind a tranzitban. Az inaktív adatok esetében Data Lake Storage Gen1 támogatja a "bekapcsolva, alapértelmezés szerint", transzparens titkosítást. Kicsit részletesebben kifejtve ez az alábbiakat jelenti:

* **Alapértelmezés szerint**: új Data Lake Storage Gen1-fiók létrehozásakor az alapértelmezett beállítás engedélyezi a titkosítást. Ezt követően a Data Lake Storage Gen1 tárolt adatforgalom mindig titkosítva marad az állandó adathordozón való tárolás előtt. Minden adatnál ez lesz a viselkedés, és ez nem módosítható egy fiók létrehozása után.
* **Transzparens**: Data Lake Storage Gen1 automatikusan titkosítja az adatok megtartását a megőrzés előtt, és visszafejti az adatok lekérése előtt. A titkosítást a rendszergazda Data Lake Storage Gen1 fiók szintjén konfigurálja és felügyeli. Az adathozzáférési API-k nem módosulnak. Ezért a titkosítás miatt nem szükséges a Data Lake Storage Gen1t használó alkalmazásokhoz és szolgáltatásokhoz módosítás.

Az átvitt adatforgalom (más néven a mozgásban lévő adatforgalom) is mindig titkosítva van Data Lake Storage Gen1ban. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. A HTTPS az egyetlen olyan protokoll, amely a Data Lake Storage Gen1 REST-felületek esetében támogatott. Az alábbi ábrán látható, hogyan történik az adattitkosítás a Data Lake Storage Gen1ban:

![Adattitkosítás diagramja Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Titkosítás beállítása Data Lake Storage Gen1

A Data Lake Storage Gen1 titkosítása a fiók létrehozása során van beállítva, és alapértelmezés szerint mindig engedélyezve van. Saját maga is kezelheti a kulcsokat, vagy engedélyezheti Data Lake Storage Gen1 számára, hogy kezelje őket (ez az alapértelmezett beállítás).

További részletekért lásd [az első lépéseket](./data-lake-store-get-started-portal.md).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>A titkosítás működése Data Lake Storage Gen1

Az alábbi információk ismertetik a fő titkosítási kulcsok kezelését, valamint a Data Lake Storage Gen1 adattitkosításában használható három különböző típusú kulcsot.

### <a name="master-encryption-keys"></a>Titkosítási főkulcsok

Data Lake Storage Gen1 két módot biztosít a fő titkosítási kulcsok (MEKs) kezelésére. Jelen esetben azt feltételezzük, hogy a titkosítási főkulcs a legfelső szintű kulcs. A Data Lake Storage Gen1ban tárolt adatok visszafejtéséhez a fő titkosítási kulcshoz való hozzáférés szükséges.

A titkosítási főkulcs kezelésének kétféle módja a következő:

*   Szolgáltatás által kezelt kulcsok
*   Felhasználó által kezelt kulcsok

A titkosítási főkulcs mindkét mód esetében az Azure Key Vaultban van biztonságosan tárolva. A Key Vault az Azure teljes körűen felügyelt, magas biztonsági szinten lévő szolgáltatása, amely biztosítja a titkosítási kulcsok védelmét. További információkért lásd a [Key Vaultról](https://azure.microsoft.com/services/key-vault) szóló cikket.

Az alábbiakban röviden összehasonlítjuk a kétféle mód MEK-kezelési képességét.

| Kérdés | Szolgáltatás által kezelt kulcsok | Felhasználó által kezelt kulcsok |
| -------- | -------------------- | --------------------- |
|Hogyan történik az adatok tárolása?|Mindig titkosítva a tárolást megelőzően.|Mindig titkosítva a tárolást megelőzően.|
|Hol történik a titkosítási főkulcs tárolása?|Key Vault|Key Vault|
|Létezik-e titkosítatlanul, a Key Vaulton kívül tárolt titkosítási kulcs? |Nem|Nem|
|Beolvasható a MEK a Key Vaultba?|Nem. A MEK a Key Vaultban történő tárolása után kizárólag titkosításra és visszafejtésre használható.|Nem. A MEK a Key Vaultban történő tárolása után kizárólag titkosításra és visszafejtésre használható.|
|Kié a Key Vault-példány és a titkosítási főkulcs?|A Data Lake Storage Gen1 szolgáltatás|A Key Vault-példány az Öné, és az Ön Azure-előfizetéséhez tartozik. A Key Vaultban lévő MEK szoftver vagy hardver által felügyelt lehet.|
|Visszavonhatja a Data Lake Storage Gen1 szolgáltatás MEK való hozzáférést?|Nem|Igen. Kezelheti Key Vault hozzáférés-vezérlési listáját, és eltávolíthatja a hozzáférés-vezérlési bejegyzéseket a Data Lake Storage Gen1 szolgáltatáshoz tartozó szolgáltatás identitásához.|
|Törölhető véglegesen a titkosítási főkulcs?|Nem|Igen. Ha törli a MEK a Key Vaultból, a Data Lake Storage Gen1-fiókban lévő adatok nem visszafejthetők senkivel, beleértve a Data Lake Storage Gen1 szolgáltatást is. <br><br> Ha készült kifejezett biztonsági mentés a MEK-ről a Key Vaultból történő törlést megelőzően, akkor a kulcs, majd pedig az adatok visszaállíthatók. Ha azonban nem készített biztonsági másolatot a MEK a Key Vault törlése előtt, akkor a Data Lake Storage Gen1-fiókban lévő adatok később soha nem visszafejthetők vissza.|


Ezen, a MEK kezelőjét és a Key Vault-példány elhelyezkedését illető eltérésen kívül a kialakítás a két mód esetében megegyezik.

A titkosítási főkulcsok kezelési módjának megválasztásakor fontos szem előtt tartani a következőket:

*   Kiválaszthatja, hogy az ügyfél által felügyelt kulcsokat vagy a szolgáltatás által felügyelt kulcsokat használja-e Data Lake Storage Gen1 fiók létrehozásakor.
*   Data Lake Storage Gen1 fiók üzembe helyezése után a mód nem módosítható.

### <a name="encryption-and-decryption-of-data"></a>Adattitkosítás és -visszafejtés

Az adattitkosítás során háromféle kulcsot használunk. A következő táblázat az összefoglalást tartalmazza:

| Kulcs                   | Rövidítés | Társítva ezzel: | Tárolási hely                             | Típus       | Jegyzetek                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Titkosítási főkulcs | MEK          | Egy Data Lake Storage Gen1 fiók | Key Vault                              | Aszimmetrikus | Data Lake Storage Gen1 vagy Ön által felügyelhető.                                                              |
| Adattitkosítási kulcs   | DEK          | Egy Data Lake Storage Gen1 fiók | A Data Lake Storage Gen1 szolgáltatás által felügyelt állandó tárterület | Szimmetrikus  | A DEK titkosítását a MEK végzi. A szolgáltatás a titkosított DEK-et tárolja az állandó adathordozón. |
| Blokktitkosítási kulcs  | BEK          | Egy adatblokk | Nincsenek                                         | Szimmetrikus  | A blokktitkosítási kulcsot az adattitkosítási kulcsból és az adatblokkból származtatjuk.                                                      |

Az alapelveket a következő ábra mutatja be:

![Adattitkosítási kulcsok](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>A fájl visszafejtésekor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1-fiók ADATTITKOSÍTÁSI kulcsot gyorsítótárazva van-e és használatra kész-e.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  A fájl minden adatblokkja esetében:
    - Olvassa ki a titkosított adatblokkot az állandó tárolóból.
    - Hozza létre a blokktitkosítási kulcsot az adattitkosítási kulcsból és a titkosított adatblokkból.
    - A blokktitkosítási kulcs használatával fejtse vissza az adatokat.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Az adatblokk titkosításakor használatos pszeudoalgoritmus:
1.  Ellenőrizze, hogy a Data Lake Storage Gen1-fiók ADATTITKOSÍTÁSI kulcsot gyorsítótárazva van-e és használatra kész-e.
    - Ha nem, olvassa ki a titkosított adattitkosítási kulcsot az állandó tárolóból, és visszafejtésre küldje el a Key Vaultba. Gyorsítótárazza a visszafejtett adattitkosítási kulcsot. A fájl ezzel használatra kész.
2.  Hozzon létre egy egyedi blokktitkosítási kulcsot az adatblokk számára az adattitkosítási kulcsból.
3.  AES-256 titkosítással végezze el a blokktitkosítási kulccsal ellátott adatblokk titkosítását.
4.  A titkosított adatblokk az állandó tárolóban lesz tárolva.

> [!NOTE] 
> A MEK mindig titkosítva tárolja az adattitkosítási kulcsot, függetlenül attól, hogy a tárolás állandó adathordozón vagy a memória-gyorsítótárban történik.

## <a name="key-rotation"></a>Kulcsrotálás

Felhasználó által kezelt kulcsok használata esetén a titkosítási főkulcs rotálható. Az ügyfél által felügyelt kulcsokkal rendelkező Data Lake Storage Gen1-fiókok beállításával kapcsolatos további információkért lásd: [első lépések](./data-lake-store-get-started-portal.md).

### <a name="prerequisites"></a>Előfeltételek

A Data Lake Storage Gen1 fiók beállításakor a saját kulcsok használatát választotta. A fiók létrehozása után ez a beállítás már nem módosítható. Az alábbi lépések során azt feltételezzük, hogy felhasználó által kezelt (tehát a Key Vaultból saját maga által kiválasztott) kulcsokat használ.

Vegye figyelembe, hogy ha az alapértelmezett titkosítási beállításokat használja, az adatait a rendszer mindig titkosítja Data Lake Storage Gen1 által felügyelt kulcsok használatával. Ebben a beállításban nem tudja elforgatni a kulcsokat, mivel azokat a Data Lake Storage Gen1 felügyeli.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>A MEK elforgatása Data Lake Storage Gen1

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg a Data Lake Storage Gen1-fiókjához társított kulcsokat tároló Key Vault-példányt. Válassza a **kulcsok** lehetőséget.

    ![Képernyőkép a Key Vaultról](./media/data-lake-store-encryption/keyvault.png)

3. Válassza ki a Data Lake Storage Gen1-fiókjához társított kulcsot, és hozza létre a kulcs új verzióját. Vegye figyelembe, hogy a Data Lake Storage Gen1 jelenleg csak a kulcs új verziójára támogatja a kulcsfontosságú rotációt. A más kulcsra történő kulcsrotálás nem támogatott.

   ![Képernyőkép a Kulcsok ablakról, amelyen az Új verzió elem van kiemelve](./media/data-lake-store-encryption/keynewversion.png)

4. Keresse meg a Data Lake Storage Gen1 fiókot, és válassza a **titkosítás** lehetőséget.

   ![Képernyőkép a Data Lake Storage Gen1 fiók ablakról, a titkosítás kiemelve](./media/data-lake-store-encryption/select-encryption.png)

5. Megjelenik egy tájékoztató üzenet arról, hogy a kulcs egy új verziója érhető el. A kulcs új verzióra történő frissítéséhez kattintson a **Kulcs rotálása** lehetőségre.

   ![Képernyőkép a Data Lake Storage Gen1 ablakról és a Kiemelt kulccsal](./media/data-lake-store-encryption/rotatekey.png)

Ez a művelet kevesebb mint két percet vehet igénybe, és a kulcsrotálás nem jár várt leállással. A művelet befejezését követően a kulcs új verziója lesz használatban.

> [!IMPORTANT]
> A kulcs-elforgatási művelet befejezése után a kulcs régi verziója már nem használható aktívan az új adatmennyiségek titkosításához. Előfordulhatnak olyan esetek, amikor a régebbi adatokhoz való hozzáféréshez szükség lehet a régi kulcsra. Az ilyen régebbi típusú adatolvasások engedélyezéséhez ne törölje a régi kulcsot