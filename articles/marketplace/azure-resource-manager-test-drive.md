---
title: A tesztelési meghajtók típusai, a Microsoft kereskedelmi piactér
description: A kereskedelmi piactéren található tesztelési meghajtók típusai
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: 2addf415c39691b4e662f304522a418aa8a778c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730371"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó

Akkor használja ezt a típust, ha az Azure Marketplace-en vagy a AppSource-ban ajánlata van, de csak Azure-erőforrásokkal rendelkező tesztelési meghajtót szeretne létrehozni. Az Azure Resource Manager (ARM-) sablon az Azure-erőforrások olyan kódolt tárolója, amelyet a legjobban a megoldásához terveztek. A test Drive a megadott ARM-sablont veszi fel, és üzembe helyezi az összes szükséges erőforrást egy erőforráscsoporthoz. Ez az egyetlen tesztvezetés lehetőség a virtuális gépekhez vagy az Azure-alkalmazások ajánlatához.

Ha nem ismeri az ARM-sablonokat, olvassa el a [Mi az Azure Resource Manager?](../azure-resource-manager/management/overview.md) című témakört, és [Ismerje meg az ARM-sablonok szerkezetét és szintaxisát](../azure-resource-manager/templates/template-syntax.md) , hogy jobban megértse a saját sablonjainak összeállítását és tesztelését.

Az **üzemeltetett** vagy **logikai alkalmazások** tesztelésére szolgáló meghajtóval kapcsolatos információkért lásd: [Mi az a test Drive?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Technikai konfiguráció

A központi telepítési sablon tartalmazza a megoldását alkotó összes Azure-erőforrást. Az ehhez a forgatókönyvhöz illeszkedő termékek csak az Azure-erőforrásokat használják. Állítsa be a következő tulajdonságokat a partner Centerben:

- **Régiók** (kötelező) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztelési meghajtót elérhetővé teheti. Általában elérhetővé szeretné tenni a tesztelési meghajtót azokon a régiókban, ahol a legnagyobb számú ügyfelet tervezi, hogy a lehető legközelebb eső régiót tudják kiválasztani a legjobb teljesítmény érdekében. Győződjön meg arról, hogy az előfizetése jogosult a kiválasztott régiókban minden szükséges erőforrás üzembe helyezésére.

- **Példányok** – válassza ki a típust (gyors vagy hideg) és a rendelkezésre álló példányok számát, amelyet az ajánlat által elérhető régiók számának szorzatával kell megszorozni.

  - Gyakori – ez a típusú példány üzembe van helyezve **, és** a kiválasztott régióhoz való hozzáférésre vár. Az ügyfelek azonnal hozzáférhetnek a tesztvezetés *gyors* példányaihoz, és nem kell megvárniuk az üzembe helyezést. A kompromisszum azt eredményezi, hogy ezek a példányok mindig az Azure-előfizetésen futnak, így a költségek nagyobb üzemidőt jelentenek. Erősen ajánlott, hogy legalább egy *gyors* példányt lehessen használni, mivel a legtöbb ügyfél nem szeretné megvárni a teljes üzembe helyezést, ami a felhasználói használatból való kiesést eredményezi, ha nem érhető el a *forró* példány.

  - **Hideg** – az ilyen típusú példányok az egyes régiókban esetlegesen üzembe helyezhető példányok teljes számát jelölik. A hideg példányok esetében a teljes tesztvezetés Resource Manager-sablon szükséges ahhoz, hogy egy ügyfél a tesztelési meghajtót használja, így a *hideg* példányok sokkal lassabban töltődnek be, mint a *forró* példányok. A kompromisszum az, hogy csak a tesztvezetés időtartamára kell fizetnie, *nem* mindig fut az Azure-előfizetésében, mint a *forró* példányok esetében.

- **Tesztvezetés Azure Resource Manager sablon** – töltse fel a Azure Resource Manager sablont tartalmazó. zip fájlt. További információ a Azure Resource Manager sablon létrehozásáról a rövid útmutató a [Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)című cikkben található.

    > [!note]
    > A sikeres közzétételhez fontos, hogy érvényesítse az ARM-sablon formátumát. Ezt kétféleképpen teheti meg (1) egy [online API-eszköz](/rest/api/resources/deployments/validate) vagy (2) használatával, [tesztelési célú telepítéssel](../azure-resource-manager/templates/deploy-portal.md).

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy hány óra elteltével marad aktív a tesztvezetés. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után. Csak egész számokat használjon (például "2" óra érvényes, "1,5").

## <a name="write-the-test-drive-template"></a>A test Drive-sablon írása

Miután megtervezte a kívánt erőforrás-csomagot, írja és hozza létre a test Drive ARM-sablont. Mivel a test Drive egy teljesen automatizált módban futtatja a központi telepítéseket, a test Drive-sablonokhoz bizonyos korlátozások vonatkoznak:

### <a name="parameters"></a>Paraméterek

A legtöbb sablon olyan paramétereket tartalmaz, amelyek az erőforrásnevek, az erőforrások mérete (például a Storage-fiókok vagy a virtuálisgép-méretek), a felhasználónevek és a jelszavak, a DNS-nevek stb. Ha Azure Portal használatával telepít megoldásokat, manuálisan is feltöltheti ezeket a paramétereket, kiválaszthatja a rendelkezésre álló DNS-neveket vagy a Storage-fiókok nevét, és így tovább.

![A Azure Resource Manager paramétereinek listája](media/test-drive/resource-manager-parameters.png)

A tesztvezetés azonban automatikusan, emberi beavatkozás nélkül működik, így csak korlátozott számú paramétert támogat. Ha a test Drive ARM-sablon egyik paramétere nem tartozik a támogatott kategóriák valamelyikébe, a paramétert változó vagy konstans értékkel kell helyettesítenie.

A paraméterekhez bármilyen érvényes nevet használhat; a test Drive felismeri a paraméter kategóriáját egy metaadat-típusú érték használatával. Adja meg a metaadatok típusát minden sablon paraméterhez, ellenkező esetben a sablon nem fogja átadni az ellenőrzést:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Az összes paraméter nem kötelező, így ha nem kívánja használni, nem kell.

### <a name="accepted-parameter-metadata-types"></a>Elfogadott paraméterek metaadatainak típusai

| Metaadat típusa   | Paraméter típusa  | Description     | Minta értéke    |
|---|---|---|---|
| **BaseUri**     | sztring          | A központi telepítési csomag alap URI-ja| `https://<..>.blob.core.windows.net/<..>` |
| **username**    | sztring          | Új véletlenszerű Felhasználónév.| admin68876      |
| **alaphelyzetbe állítása**    | biztonságos karakterlánc    | Új véletlenszerű jelszó | LP! ACS \^ 2kh     |
| **munkamenet-azonosító**   | sztring          | Egyedi tesztelési meghajtó munkamenet-azonosítója (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>BaseUri

A test Drive inicializálja ezt a paramétert a központi telepítési csomag **Alap URI-ja** alapján, hogy ezzel a paraméterrel létrehozza a csomagban található bármely fájl URI-ját.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Használja ezt a paramétert a sablonon belül, hogy bármely fájl URI-ját létrehozza a test Drive-telepítési csomagból. Az alábbi példa bemutatja, hogyan hozhat létre egy URI-t a csatolt sablonhoz:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>username

A test Drive inicializálja ezt a paramétert egy új véletlenszerű felhasználónévvel:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Minta értéke: `admin68876`

A megoldáshoz véletlenszerű vagy állandó felhasználónevet is használhat.

#### <a name="password"></a>jelszó

A test Drive inicializálja ezt a paramétert egy új véletlenszerű jelszóval:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Minta értéke:  `Lp!ACS^2kh`

A megoldáshoz véletlenszerű vagy állandó jelszavakat is használhat.

#### <a name="session-id"></a>munkamenet-azonosító

A test Drive inicializálja ezt a paramétert egy olyan egyedi GUID azonosítóval, amely a teszt-munkamenet AZONOSÍTÓját jelöli:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Minta értéke: `b8c8693e-5673-449c-badd-257a405a6dee`

Ezzel a paraméterrel egyedileg azonosíthatja a tesztelési meghajtó munkamenetét, ha szükséges.

### <a name="unique-names"></a>Egyedi nevek

Egyes Azure-erőforrások, például a Storage-fiókok vagy a DNS-nevek globálisan egyedi neveket igényelnek. Ez azt jelenti, hogy minden alkalommal, amikor a test Drive üzembe helyezi az ARM-sablont, létrehoz egy új erőforráscsoportot, amely minden erőforrásához egyedi névvel rendelkezik. Ezért a [uniquestring](../azure-resource-manager/templates/template-functions.md) függvényt kell használnia az erőforráscsoport-azonosítók változók neveivel való összefűzéséhez, hogy véletlenszerű egyedi értékeket állítson elő:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Győződjön meg arról, hogy a paraméter/változó sztringeket ( `contosovm` ) egyedi karakterlánc-kimenettel () fűzi össze `resourceGroup().id` , mivel ez garantálja az egyes változók egyediségét és megbízhatóságát.

Például a legtöbb erőforrás neve nem kezdődhet számjegyekkel, de az egyedi karakterlánc függvény egy számjegyből álló karakterláncot ad vissza. Ha tehát nyers egyedi karakterlánc-kimenetet használ, a központi telepítések sikertelenek lesznek.

Az erőforrás-elnevezési szabályokkal és korlátozásokkal kapcsolatos további információkat [ebben a cikkben](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)talál.

### <a name="deployment-location"></a>Központi telepítés helye

A tesztelési meghajtót különböző Azure-régiókban is elérhetővé teheti. Az a gondolat, hogy lehetővé teszi a felhasználó számára a legközelebbi régió kiválasztását a Beast felhasználói élmény biztosításához.

Amikor a test Drive létrehozza a labor egy példányát, az mindig létrehoz egy erőforráscsoportot egy felhasználó által választott régióban, majd végrehajtja a telepítési sablont a csoport környezetében. Ezért a sablonnak ki kell választania a központi telepítési helyet az erőforráscsoporthoz:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Ezután használja ezt a helyet minden erőforráshoz egy adott labor-példány esetében:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Győződjön meg arról, hogy az előfizetése minden kiválasztott régióban üzembe helyezi az összes kívánt erőforrást. Győződjön meg arról is, hogy a virtuálisgép-lemezképek elérhetők minden olyan régióban, amelyet engedélyezni fog, ellenkező esetben a telepítési sablon egyes régiókban nem fog működni.

### <a name="outputs"></a>Kimenetek

A Resource Manager-sablonokkal általában kimenet nélkül is üzembe helyezhető. Ennek az az oka, hogy ismeri az összes olyan értéket, amelyet a sablon paramétereinek feltöltéséhez használ, és bármikor manuálisan megvizsgálhatja bármelyik erőforrás tulajdonságait.

A test Drive Resource Manager-sablonok esetében azonban fontos, hogy térjen vissza a test Drive összes adatához, amely szükséges a laborhoz való hozzáféréshez (webhely URI-k, virtuális gépek állomásneve, felhasználónevek és jelszavak). Győződjön meg arról, hogy az összes kimeneti név olvasható, mert ezek a változók az ügyfél számára jelennek meg.

Nincsenek a sablon kimenetével kapcsolatos korlátozások. A test Drive minden kimeneti értéket sztringre konvertál, így ha egy objektumot küld a kimenetre, a felhasználó a JSON-karakterláncot fogja látni.

Példa:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Előfizetési korlátok

Ne feledkezzen meg az előfizetés és a szolgáltatás korlátairól. Ha például az 10 4-os virtuális gépeket szeretné üzembe helyezni, gondoskodnia kell arról, hogy a laborhoz használt előfizetés lehetővé tegye a 40 magok használatát. Az Azure-előfizetéssel és a szolgáltatás korlátaival kapcsolatos további információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md). Egyszerre több tesztvezetés is elvégezhető. Ellenőrizze, hogy az előfizetés képes-e kezelni a magok számát, szorozva az egyidejű tesztelési meghajtók teljes számával.

### <a name="what-to-upload"></a>Mi a feltöltés?

A test Drive ARM-sablon zip-fájlként lett feltöltve, amely különböző üzembe helyezési összetevőket tartalmazhat, de egy nevű fájllal kell rendelkeznie `main-template.json` . Ez az ARM-telepítési sablon, amelyet a test Drive használ a tesztkörnyezet létrehozásához. Ha a fájlon kívül további erőforrásokkal is rendelkezik, hivatkozhat rájuk külső erőforrásként a sablonon belül, vagy belefoglalhatja őket a zip-fájlba.

A közzétételi minősítés során a test Drive kibontja a telepítési csomagot, és egy belső tesztvezetés blob-tárolóba helyezi a tartalmát. A tároló szerkezete a telepítési csomag szerkezetét tükrözi:

| package.zip                       | Tesztvezetés blob-tárolója         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Meghívjuk a blob Container alap URI azonosítójának URI-ját. Mivel a labor minden változata saját blob-tárolóval rendelkezik, a labor minden változatának saját alap URI-ja van. A test Drive a sablon paramétereinek használatával átadhatja a kibontott telepítési csomag alapszintű URI-ját a sablonba.

### <a name="transform-template-examples-for-test-drive"></a>Példa a tesztelési meghajtóra a sablon átalakításához

Az erőforrások architektúrájának a test Drive Resource Manager-sablonba való bekapcsolásának folyamata ijesztő lehet. További segítségért tekintse meg az alábbi példákat a jelenlegi telepítési sablonok legjobb átalakítására a [Mi az a test Drive?](what-is-test-drive.md#transforming-examples)című témakörben.

## <a name="test-drive-deployment-subscription-details"></a>Tesztelési meghajtó üzembe helyezésének előfizetése – részletek

Az utolsó szakasz a tesztelési meghajtók automatikus üzembe helyezése az Azure-előfizetés és a Azure Active Directory (AD) összekapcsolásával.

![Tesztelési meghajtó üzembe helyezésének előfizetése – részletek](media/test-drive/deployment-subscription-details.png)

1. Szerezze be az **Azure-előfizetés azonosítóját**. Ez hozzáférést biztosít az Azure-szolgáltatásokhoz és a Azure Portalhoz. Az előfizetés az erőforrás-használat jelentését és a szolgáltatások számlázását jelenti. Ha még nem rendelkezik külön Azure-előfizetéssel kizárólag tesztelési meghajtókhoz, végezze el az egyiket. Az Azure-előfizetések azonosítóit (például `1a83645ac-1234-5ab6-6789-1h234g764ghty1` ) a bal oldali navigációs menüben található **előfizetések** lehetőségre Azure Portal való bejelentkezéssel, illetve az előfizetések kiválasztásával érheti el.

   ![Azure-előfizetések](media/test-drive/azure-subscriptions.png)

2. Szerezze be az **Azure ad-bérlő azonosítóját**. Ha már rendelkezik egy bérlői azonosítóval, **Azure Active Directory**  >  **Tulajdonságok**  >  **címtár-azonosítójában** található:

   ![Azure Active Directory tulajdonságai](media/test-drive/azure-active-directory-properties.png)

   Ha nem rendelkezik bérlői AZONOSÍTÓval, hozzon létre egy újat a Azure Active Directoryban. A bérlők beállításával kapcsolatos segítséget a rövid útmutató [: bérlő beállítása](../active-directory/develop/quickstart-create-new-tenant.md)című témakörben talál.

3. **Azure ad alkalmazás ID** – új alkalmazás létrehozása és regisztrálása. Ezt az alkalmazást fogjuk használni a test Drive-példányon végrehajtott műveletek végrehajtásához.

   1. Navigáljon az újonnan létrehozott címtárhoz vagy már meglévő könyvtárhoz, és válassza a Azure Active Directory lehetőséget a szűrő ablaktáblán.
   2. Keressen **Alkalmazásregisztrációk** és válassza a **Hozzáadás** lehetőséget.
   3. Adja meg az alkalmazás nevét.
   4. Válassza ki a **webalkalmazás/API** **típusát** .
   5. Adjon meg bármilyen értéket a bejelentkezési URL-címben, ez a mező nincs használatban.
   6. Válassza a **Létrehozás** lehetőséget.
   7. Az alkalmazás létrehozása után válassza a **Tulajdonságok**  >  **több-bérlős beállítást** , majd a **Mentés** lehetőséget.

4. Kattintson a **Mentés** gombra.

5. Másolja ki az alkalmazás AZONOSÍTÓját a regisztrált alkalmazáshoz, és illessze be a test Drive (teszt) mezőbe.

   ![Azure AD-alkalmazás AZONOSÍTÓjának részletei](media/test-drive/azure-ad-application-id-detail.png)

6. Mivel az alkalmazást az előfizetésre való központi telepítésre használjuk, az alkalmazást közreműködőként kell hozzáadni az előfizetéshez:

   1. Válassza ki a tesztelési meghajtóhoz használt **előfizetés** típusát.
   1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
   1. Válassza ki a **szerepkör-hozzárendelések** lapot, majd **adja hozzá a szerepkör-hozzárendelést**.

      ![Új Access Control rendszerbiztonsági tag hozzáadása](media/test-drive/access-control-principal.jpg)

   1. Adja meg a **szerepkört** , és **rendelje hozzá a hozzáférést a** következőhöz:. A **Select (kiválasztás** ) mezőben adja meg az Azure ad-alkalmazás nevét. Válassza ki azt az alkalmazást, amelyhez hozzá szeretné rendelni a **közreműködő** szerepkört.

      ![Engedélyek hozzáadása](media/test-drive/access-control-permissions.jpg)

   1. Kattintson a **Mentés** gombra.

7. **Azure ad alkalmazás** hitelesítési kulcs létrehozása. A **kulcsok** területen adja meg a **kulcs leírását**, állítsa be az időtartamot, hogy **Soha ne járjon le** (a lejárt kulcs megszakítja a tesztelési meghajtót éles környezetben), majd válassza a **Mentés** lehetőséget. Másolja és illessze be ezt az értéket a szükséges tesztvezetés mezőjébe.

![Megjeleníti az Azure AD-alkalmazás kulcsait](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Ismételt közzététel

Most, hogy az összes Test Drive-mező elkészült, **tegye közzé** újra az ajánlatot. Miután a tesztvezetés átadta a minősítést, tesztelje a felhasználói élményt az ajánlat előnézetében:

1. Indítsa el a tesztelési meghajtót a felhasználói felületen.
1. Nyissa meg az Azure-előfizetését a Azure Portalon belül.
1. Ellenőrizze, hogy a tesztelési meghajtó megfelelően van-e telepítve.

   ![Azure Portal](media/test-drive/azure-portal.png)

Ne törölje az ügyfelek számára kiépített tesztvezetés-példányokat. a test Drive szolgáltatás automatikusan törli ezeket az erőforráscsoportokat, miután az ügyfél befejezte azt.

Ha már elégedett az előzetes verzió ajánlatával, itt az ideje, hogy **éljünk**! Végső felülvizsgálati folyamattal ellenőrizheti a teljes végpontok közötti élményt. Ha elutasítjuk az ajánlatot, elküldjük Önnek az ajánlat mérnöki kapcsolattartóját, amely elmagyarázza, mit kell rögzíteni.

## <a name="next-steps"></a>Következő lépések

- Ha követte az ajánlat a partner Centerben való létrehozásához szükséges utasításokat, a vissza nyílra kattintva térjen vissza ehhez a témakörhöz.
- További információ a tesztelési meghajtók egyéb típusairól: [Mi a test Drive?](what-is-test-drive.md).