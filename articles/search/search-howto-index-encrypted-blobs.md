---
title: Keresés titkosított Azure Blob Storage-tartalommal
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan indexelheti és kinyerheti a titkosított dokumentumokból származó szöveget az Azure Blob Storage az Azure Cognitive Search használatával.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96530935"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Titkosított Blobok indexelése blob-indexelő és szakértelmével használatával az Azure-ban Cognitive Search

Ez a cikk bemutatja, hogyan használható az [azure Cognitive Search](search-what-is-azure-search.md) az [Azure-Blob Storage](../storage/blobs/storage-blobs-introduction.md) korábban titkosított dokumentumok [Azure Key Vault](../key-vault/general/overview.md)használatával történő indexeléséhez. Az indexelő általában nem tudja kibontani a tartalmat a titkosított fájlokból, mert nem fér hozzá a titkosítási kulcshoz. Azonban a [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) egyéni képességeinek a [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)után történő kihasználása révén szabályozott hozzáférést biztosíthat a kulcshoz a fájlok visszafejtéséhez, majd kinyerheti a tartalmat. Ezzel feloldja a dokumentumok indexelésének képességét a tárolt dokumentumok titkosítási állapotának veszélyeztetése nélkül.

A korábban titkosított teljes dokumentumok (strukturálatlan szöveg), például a PDF, a HTML, a DOCX és a PPTX az Azure Blob Storage-ban való elindítása esetén ez az útmutató a Poster és a keresési REST API-k használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Definiáljon egy folyamatot, amely visszafejti a dokumentumokat, és kinyeri belőlük a szöveget.
> * Definiáljon egy indexet a kimenet tárolásához.
> * A folyamat végrehajtásával hozza létre és töltse be az indexet.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Ez a példa feltételezi, hogy már feltöltötte a fájljait az Azure Blob Storageba, és titkosította őket a folyamatban. Ha segítségre van szüksége a fájlok első feltöltéséhez és titkosításához, tekintse meg [ezt az oktatóanyagot](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) .

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ugyanabban az előfizetésben, mint az Azure Cognitive Search. A Key vaultnak engedélyezve kell lennie a helyreállítható **törlési** és **kiürítési védelemmel** .
+ [Azure-Cognitive Search](search-create-service-portal.md) egy [számlázható](search-sku-tier.md#tier-descriptions) szinten (bármely régióban)
+ [Azure-függvény](https://azure.microsoft.com/services/functions/)
+ [Postman asztali alkalmazás](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 – szolgáltatások létrehozása és hitelesítő adatok összegyűjtése

### <a name="set-up-the-custom-skill"></a>Az egyéni képesség beállítása

Ez a példa a minta [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) projektet használja az [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub-tárházból. Ebben a szakaszban egy Azure-függvénybe helyezi a képességet, hogy egy készségkészlet is használható legyen. A beépített telepítési szkript létrehoz egy nevű Azure Function-erőforrást a **psdbf-Function-app-** től kezdődően, és betölti a szaktudást. A rendszer kérni fogja, hogy adjon meg egy előfizetést és egy erőforráscsoportot. Ügyeljen arra, hogy ugyanazt az előfizetést válassza, amelyet az Azure Key Vault-példánya használ.

A DecryptBlobFile skill minden blobhoz bemenetként fogadja az URL-címet és az SAS-tokent, és a letöltött, visszafejtett fájlt az Azure Cognitive Search által várt fájl-hivatkozási szerződéssel adja ki. Ne felejtse el, hogy a DecryptBlobFile szüksége van a titkosítási kulcsra a visszafejtés végrehajtásához. A beállítás részeként létre fog hozni egy hozzáférési szabályzatot is, amely megadja a DecryptBlobFile függvény hozzáférését a titkosítási kulcshoz Azure Key Vaultban.

1. Kattintson az **üzembe helyezés az Azure** -ban gombra a [DecryptBlobFile kezdőlapján](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), amely megnyitja a megadott Resource Manager-sablont a Azure Portalon belül.

1. Válassza ki azt **az előfizetést, amelyben a Azure Key Vault példány létezik** (ez az útmutató nem fog működni, ha másik előfizetést választ), vagy válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat (ha létrehoz egy újat, ki kell választania egy régiót, amelyet telepíteni szeretne).

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, győződjön meg arról, hogy elfogadja a feltételeket, majd válassza a **Létrehozás** lehetőséget az Azure-függvény telepítéséhez.

    ![ARM-sablon a portálon](media/indexing-encrypted-blob-files/arm-template.jpg "ARM-sablon a portálon")

1. Várjon, amíg az üzembe helyezés befejeződik.

1. Navigáljon a Azure Key Vault-példányhoz a portálon. [Hozzon létre egy hozzáférési szabályzatot](../key-vault/general/assign-access-policy-portal.md) a Azure Key Vaultban, amely kulcsfontosságú hozzáférést biztosít az egyéni képességhez.
 
    1. A **Beállítások** területen válassza a **hozzáférési szabályzatok** lehetőséget, majd válassza a **hozzáférési házirend hozzáadása** lehetőséget.
     
       ![Kulcstartó – hozzáférési szabályzat hozzáadása](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Kulcstartó-hozzáférési szabályzatok")

    1. **A konfigurálás sablonból** területen válassza az **Azure Data Lake Storage vagy az Azure Storage** lehetőséget.

    1. A rendszerbiztonsági tag esetében válassza ki az üzembe helyezett Azure-függvény példányát. A 2. lépésben létrehozott erőforrás-előtaggal megkeresheti azt, amely a **psdbf-Function-app** alapértelmezett előtag-értékét használja.

    1. Ne jelölje ki a jogosultságot az alkalmazásra vonatkozó beállításnál.
     
        ![Kulcstartó – hozzáférési szabályzat hozzáadása sablon](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Kulcstartó hozzáférési szabályzatának sablonja")

    1. Ügyeljen arra, hogy a hozzáférési házirendek lapon a **Mentés** gombra kattintva lépjen a hozzáférési házirend tényleges hozzáadására.
     
         ![Kulcstartó – hozzáférési szabályzat mentése](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Kulcstartó-hozzáférési házirend mentése")

1. Navigáljon a **psdbf-Function-app** függvényhez a portálon, és jegyezze fel a következő tulajdonságokat, ahogy az útmutató későbbi részében szüksége lesz rájuk:

    1. A függvény URL-címe, amely a függvény főoldalának **Essentials** területén található.
    
        ![Függvény URL-címe](media/indexing-encrypted-blob-files/function-uri.jpg "Hol található az Azure-függvény URL-címe")

    1. A gazdagép kulcsának kódja, amely az **alkalmazás kulcsainak** megnyitásával, az **alapértelmezett** kulcs megjelenítéséhez és az érték másolásával érhető el.
     
        ![Function Host kulcs kódja](media/indexing-encrypted-blob-files/function-host-key.jpg "Hol található az Azure Function Host kulcs kódja")

### <a name="cognitive-services"></a>Cognitive Services

Az AI-bővítési és-készségkészlet-végrehajtást Cognitive Services támogatja, beleértve a természetes nyelv és a képfeldolgozás Text Analytics és Computer Vision. Ha a cél egy tényleges prototípus vagy projekt teljesítése volt, akkor Cognitive Services (ugyanabban a régióban, mint az Azure Cognitive Search), hogy az indexelési műveletekhez csatolható legyen.

Ennél a gyakorlatnál azonban kihagyhatja az erőforrások kiosztását, mivel az Azure Cognitive Search képes csatlakozni a háttérben a Cognitive Serviceshoz, és az indexelő futtatásakor 20 ingyenes tranzakciót biztosít. 20 dokumentum feldolgozása után az indexelő sikertelen lesz, kivéve, ha Cognitive Services kulcs van csatolva a készségkészlet. Nagyobb projektek esetében tervezze meg Cognitive Services kiépítés az utólagos elszámolású S0 szinten. További információ: [Cognitive Services csatolása](cognitive-search-attach-cognitive-services.md). Vegye figyelembe, hogy Cognitive Services kulcsnak több mint 20 dokumentummal rendelkező készségkészlet futtatására van szükség, még akkor is, ha a kiválasztott kognitív képességek egyike sem csatlakozik Cognitive Serviceshoz (például a megadott készségkészlet, ha nincsenek hozzájuk hozzáadott ismeretek).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Az utolsó összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). Az útmutató elvégzéséhez használhatja az ingyenes szintet. 

Ahogy az Azure-függvényhez is, szánjon egy kis időt a rendszergazdai kulcs begyűjtésére. Tovább, amikor megkezdi a kérelmek strukturálását, meg kell adnia az egyes kérések hitelesítéséhez használt Endpoint és admin API-kulcsot.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL-címe volt `https://mydemo.search.windows.net` , a szolgáltatás neve a következő lesz: `mydemo` .

2. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-javascript/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="2---set-up-postman"></a>2 – Poster beállítása

A Poster telepítése és beállítása.

### <a name="download-and-install-postman"></a>Poster letöltése és telepítése

1. Töltse le a [Poster-gyűjtemény forráskódját](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. A **fájl** importálása lehetőség kiválasztásával  >   importálhatja a forráskódot Poster-ba.
1. Válassza a **gyűjtemények** fület, majd kattintson a **...** (három pont) gombra.
1. Válassza a **Szerkesztés** elemet. 
   
   ![Navigációt bemutató Poster-alkalmazás](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Ugrás a Poster szerkesztési menüjére")
1. A **Szerkesztés** párbeszédpanelen válassza a **változók** lapot. 

A **változók** lapon olyan értékeket adhat hozzá a Poster-feladatokhoz, amelyek minden alkalommal felvesznek egy adott változót a kettős zárójelek között. A Poster például a `{{admin-key}}` megadott aktuális értékkel helyettesíti a szimbólumot `admin-key` . A Poster lehetővé teszi a helyettesítést az URL-címek, a fejlécek, a kérés törzse és így tovább. 

Az érték beszerzéséhez `admin-key` használja a korábban feljegyzett Azure Cognitive Search felügyeleti API-kulcsot. Állítsa be a `search-service-name` használt Azure Cognitive Search szolgáltatás nevét. Állítsa be `storage-connection-string` a Storage-fiók **hozzáférési kulcsainak** lapján található érték használatával, és állítsa `storage-container-name` annak a Storage-fióknak a nevére, amelyen a titkosított fájlok tárolódnak. Állítsa `function-uri` a korábban feljegyzett Azure-függvény URL-címére, és állítsa be `function-code` a korábban feljegyzett Azure Function Host kulcs kódját. A többi értéknél meghagyhatja az alapértelmezett értékeket.

![Poster-alkalmazás változók lapja](media/indexing-encrypted-blob-files/postman-variables-window.jpg "A Poster változói ablaka")

| Változó    | Honnan szerezhető be? |
|-------------|-----------------|
| `admin-key` | Az Azure Cognitive Search szolgáltatás **Keys (kulcsok** ) lapján.  |
| `search-service-name` | Az Azure Cognitive Search szolgáltatás neve. Az URL-cím: `https://{{search-service-name}}.search.windows.net` . |
| `storage-connection-string` | A Storage-fiók **hozzáférési kulcsok** lapján válassza a **key1**  >  **kapcsolati karakterlánc** lehetőséget. |
| `storage-container-name` | Annak a blob-tárolónak a neve, amelybe az indexelni kívánt titkosított fájlok vannak. |
| `function-uri` |  Az Azure-függvény főoldalának **alapok** területén. |
| `function-code` | Az Azure-függvényben navigáljon az **alkalmazás kulcsaihoz**, és kattintson az **alapértelmezett** kulcs megjelenítéséhez és az érték másolásához. |
| `api-version` | **2020-06-30**-ig marad. |
| `datasource-name` | Maradjon **titkosítva – Blobok – DS**. |
| `index-name` | Maradjon **titkosítatlan Blobok-idx-** ként. |
| `skillset-name` | Maradjon **titkosítva – Blobok – SS**. |
| `indexer-name` | Maradjon **titkosítva – Blobok – IXR**. |

### <a name="review-the-request-collection-in-postman"></a>Tekintse át a Poster kérelem-gyűjteményét

Az útmutató futtatásakor négy HTTP-kérelmet kell kiadnia:

- **Az index létrehozásának kérése**: ez az index az Azure Cognitive Search által használt és visszaadott adatok tárolására szolgál.
- **Az adatforrás létrehozásához szükséges post**: ez az adatforrás összekapcsolja az Azure Cognitive Search szolgáltatást a Storage-fiókkal, ezért a titkosított blob-fájlokat. 
- **Kérelem létrehozása a készségkészlet létrehozásához**: a készségkészlet megadja az Azure-függvény egyéni képességének definícióját, amely visszafejti a blob-fájl adatait, valamint egy [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) , amely a visszafejtés után Kinyeri a szöveget az egyes dokumentumokból.
- **Put-kérelem az indexelő létrehozásához**: az indexelő beolvassa az adatokat, alkalmazza a készségkészlet, és az eredményeket tárolja. Ezt a kérést utoljára kell futtatnia.

A [forráskód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) tartalmaz egy Poster-gyűjteményt, amely a négy kérelemmel rendelkezik, valamint néhány hasznos utólagos kérelmet is. A kérések kiadásához a Poster lapon válassza a kérelmek fület, és válassza a **Küldés** lehetőséget.

## <a name="3---monitor-indexing"></a>3 – indexelés figyelése

Az indexelés és a dúsítás a Create Indexer-kérelem elküldésekor azonnal megkezdődik. Attól függően, hogy hány dokumentum található a Storage-fiókban, az indexelés eltarthat egy ideig. Annak megállapításához, hogy az indexelő továbbra is fut-e, használja a Poster-gyűjtemény részeként megadott **Indexelő állapot** kérése lehetőséget, és tekintse át a választ, hogy megtudja, az indexelő fut-e, illetve hogy megtekintse-e a hiba és a figyelmeztetés adatait.  

Ha az ingyenes szintet használja, a következő üzenet várható: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Ez az üzenet akkor jelenik meg, ha az ingyenes szinten lévő blob-indexelés [32K korláttal](search-limits-quotas-capacity.md#indexer-limits)rendelkezik. Ez az üzenet nem jelenik meg ennél a magasabb szintű adatkészletnél. 

## <a name="4---search"></a>4 – keresés

Az indexelő végrehajtásának befejeződése után néhány lekérdezés futtatásával ellenőrizheti, hogy az adatgyűjtés sikeresen visszafejtve és indexelve lett-e. Navigáljon az Azure Cognitive Search szolgáltatásra a portálon, és a [keresési ablak](search-explorer.md) használatával futtasson lekérdezéseket az indexelt adaton.

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen indexelte a titkosított fájlokat, [megismételheti ezt a folyamatot további kognitív képességek hozzáadásával](cognitive-search-defining-skillset.md). Ez lehetővé teszi, hogy bővítse az adatait, és további elemzéseket nyerjen.

Ha kétszeresen titkosított adattal dolgozik, érdemes megvizsgálnia az Azure Cognitive Searchban elérhető index titkosítási funkciókat. Annak ellenére, hogy az indexelő a visszafejtett adattitkosítást igényli az indexeléshez, az index létezése után titkosítható az ügyfél által felügyelt kulcs használatával. Ezzel biztosíthatja, hogy az adatok mindig titkosítva legyenek, ha nyugalmi állapotban van. További információ: az [ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure Cognitive Searchban](search-security-manage-encryption-keys.md).