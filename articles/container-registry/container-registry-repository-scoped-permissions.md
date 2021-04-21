---
title: Engedélyek az adattárakhoz a Azure Container Registry
description: Hozzon létre egy olyan jogkivonatot, amely egy Prémium beállításjegyzék adott tárházaira vonatkozó engedélyekkel rendelkezik a rendszerképek leküldéséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához
ms.topic: article
ms.date: 02/04/2021
ms.openlocfilehash: 0ac479b696a377509cee6459efd8bbb9de940d2a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781396"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Jogkivonat létrehozása adattárra vonatkozó engedélyekkel

Ez a cikk azt ismerteti, hogyan hozhat létre jogkivonatokat és hatókörtérképeket a tároló-beállításjegyzék adott adattáraihoz való hozzáférés kezeléséhez. Jogkivonatok létrehozásával a beállításjegyzék tulajdonosa korlátozott, korlátozott hozzáféréssel tud hozzáférést biztosítani az adattárakhoz a felhasználók vagy szolgáltatások számára a rendszerképek leküldéséhez vagy leküldéséhez, illetve egyéb műveletek végrehajtásához. A jogkivonatok a beállításjegyzék egyéb [](container-registry-authentication.md)hitelesítési beállításainál pontosabb engedélyeket kínálnak, amelyek a teljes beállításjegyzékre vonatkoznak. 

A jogkivonatok létrehozásának forgatókönyvei a következők:

* Képek adattárból való lekért jogkivonatokkal való lekért ioT-eszközök engedélyezése
* Adjon egy külső szervezetnek engedélyeket egy adott adattárhoz 
* Korlátozza az adattárak hozzáférését a szervezet különböző felhasználói csoportjaira. Biztosítson például írási és olvasási hozzáférést az olyan fejlesztőknek, akik adott adattárakat megcélzott rendszerképeket hoznak létre, valamint olvasási hozzáférést az adott adattárakból üzembe helyező csapatokhoz.

Ez a szolgáltatás a Prémium tároló-beállításjegyzék **szolgáltatási** szinten érhető el. A beállításjegyzék szolgáltatásszintekkel és korlátozásokkal kapcsolatos további információkért lásd: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md)

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és [bizonyos korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

* Jelenleg nem rendelhet tárházra vonatkozó engedélyeket egy Azure Active Directory identitáshoz, például szolgáltatásnévhez vagy felügyelt identitáshoz.
* Nem hozhat létre hatókörtérképet a névtelen lekért hozzáféréshez engedélyezett [beállításjegyzékben.](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)

## <a name="concepts"></a>Alapelvek

Az adattárra vonatkozó engedélyek konfigurálása egy társított hatókörtérképpel rendelkező *jogkivonatot* *hoz létre.* 

* A **jogkivonat** és a generált jelszó lehetővé teszi a felhasználó számára a beállításjegyzékben való hitelesítést. Beállíthatja a jogkivonat jelszavának lejárati dátumát, vagy bármikor letilthatja a jogkivonatot.  

  A jogkivonattal való hitelesítést követően a felhasználó  vagy szolgáltatás végrehajthat egy vagy több, egy vagy több adattárra vonatkozó műveletet.

  |Művelet  |Leírás  | Példa |
  |---------|---------|--------|
  |`content/delete`    | Adatok eltávolítása az adattárból  | Adattár vagy jegyzék törlése |
  |`content/read`     |  Adatok olvasása az adattárból |  Összetevő lekérte |
  |`content/write`     |  Adatok írása az adattárba     | Összetevő `content/read` leküldése a és a használatával |
  |`metadata/read`    | Metaadatok olvasása az adattárból   | Címkék vagy jegyzékek felsorolása |
  |`metadata/write`     |  Metaadatok írása az adattárba  | Olvasási, írási vagy törlési műveletek engedélyezése vagy letiltása |

* A **hatókörleképezés** egy jogkivonatra vonatkozó adattárengedélyeket csoportosít, és más jogkivonatokat is alkalmazhat. Minden jogkivonat egyetlen hatókörtérképhez van társítva. 

   Hatókörtérképpel:

    * Több, azonos engedélyekkel rendelkező jogkivonat konfigurálása adattárak egy készletére
    * Jogkivonat-engedélyek frissítése, amikor adattárműveleteket ad hozzá vagy távolít el a hatókörtérképen, vagy egy másik hatókörleképezetet alkalmaz 

  Azure Container Registry emellett számos rendszer által meghatározott hatókörtérképet is biztosít, amelyek a jogkivonatok létrehozásakor alkalmazhatók. A rendszer által meghatározott hatókörtérképek engedélyei a regisztrációs adatbázis összes adattárában érvényesek.

Az alábbi képen a jogkivonatok és a hatókörtérképek közötti kapcsolat látható. 

![Beállításjegyzék-jogkivonatok és hatókörtérképek](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – A cikkben az Azure CLI parancsparancs-példáihoz az Azure CLI 2.17.0-s vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).
* **Docker** – A regisztrációs adatbázissal való hitelesítéshez a rendszerképek leküldéséhez vagy leküldéséhez helyi Docker-telepítés szükséges. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.
* **Tároló-beállításjegyzék** – Ha még nem rendelkezik ilyen beállításjegyzékvel, hozzon létre egy Prémium szintű tároló-beállításjegyzéket az Azure-előfizetésében, vagy frissítsen egy meglévő regisztrációs adatbázist. Használhatja például a [Azure Portal](container-registry-get-started-portal.md) vagy az Azure [CLI-t.](container-registry-get-started-azure-cli.md) 

## <a name="create-token---cli"></a>Jogkivonat létrehozása – CLI

### <a name="create-token-and-specify-repositories"></a>Jogkivonat létrehozása és adattárak megadása

Hozzon létre egy jogkivonatot [az az acr token create paranccsal.][az-acr-token-create] Jogkivonat létrehozásakor minden adattárhoz megadhat egy vagy több adattárat és a hozzájuk tartozó műveleteket. Az adattáraknak még nem kell a regisztrációs adatbázisban lennie. Ha meglévő hatókörtérkép megadásával jogkivonatot hoz létre, tekintse meg a [következő szakaszt.](#create-token-and-specify-scope-map)

Az alábbi példa egy jogkivonatot hoz létre a *myregistry* beállításjegyzékben az alábbi engedélyekkel a(z) és `samples/hello-world` a (hamis) adatbázison: `content/write` és `content/read` . Alapértelmezés szerint a parancs az alapértelmezett jogkivonat-állapotot a következőre állítja be: , de az állapotot bármikor `enabled` `disabled` frissítheti a következőre: .

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A kimenet a jogkivonat részleteit jeleníti meg. Alapértelmezés szerint két jelszó jön létre, amelyek nem járnak le, de opcionálisan lejárati dátum is beállítható. Javasoljuk, hogy a jelszavakat egy biztonságos helyre mentse, hogy később használva legyen a hitelesítéshez. A jelszavak nem olvashatók be újra, de újak is generálhatóak.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> A jogkivonatjelszavak és a lejárati időszakok újragenerálásával a cikk későbbi, [Jogkivonatjelszavak](#regenerate-token-passwords) újragenerálásával cikket talál.

A kimenet a létrehozott parancs hatókör-leképezésének részleteit tartalmazza. A nevű hatókörtérkép használatával ugyanezeket az adattárműveleteket más jogkivonatra is `MyToken-scope-map` alkalmazhatja. Vagy később frissítheti a hatókörtérképet a társított jogkivonatok engedélyeinek módosításhoz.

### <a name="create-token-and-specify-scope-map"></a>Jogkivonat létrehozása és hatókörleképés megadása

A jogkivonatok létrehozására egy másik módszer egy meglévő hatókörtérkép megadása. Ha még nem rendelkezik hatókörtérképpel, először hozzon létre egyet az adattárak és a kapcsolódó műveletek megadásával. Ezután adja meg a hatókörtérképet a jogkivonat létrehozásakor. 

Hatókörtérkép létrehozásához használja az [az acr scope-map create parancsot.][az-acr-scope-map-create] A következő parancs létrehoz egy hatókörtérképet a korábban használt adattár ugyanezekkel az `samples/hello-world` engedélyekkel. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Az [az acr token create futtatásával][az-acr-token-create] hozzon létre egy jogkivonatot a *MyScopeMap hatókörtérkép* megadásával. Ahogy az előző példában is, a parancs az alapértelmezett jogkivonat-állapotot a következőre állítja: `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A kimenet a jogkivonat részleteit jeleníti meg. Alapértelmezés szerint két jelszó jön létre. Javasoljuk, hogy a jelszavakat egy biztonságos helyre mentse, hogy később használva legyen a hitelesítéshez. A jelszavak nem olvashatók be újra, de újak is generálhatóak.

> [!NOTE]
> A jogkivonatjelszavak és a lejárati időszakok újragenerálásával a cikk későbbi, [Jogkivonatjelszavak](#regenerate-token-passwords) újragenerálását olvashatja.

## <a name="create-token---portal"></a>Jogkivonat létrehozása – portál

A jogkivonatok és hatókörtérképek Azure Portal használhatja az alkalmazásokat. Ahogy a CLI-parancsnál, itt is alkalmazhat egy meglévő hatókörleképezést, vagy létrehozhat hatókörleképezést jogkivonatok létrehozásakor egy vagy több adattár és a kapcsolódó műveletek `az acr token create` megadásával. Az adattáraknak még nem kell a regisztrációs adatbázisban lennie. 

Az alábbi példa létrehoz egy jogkivonatot, és létrehoz egy hatókörtérképet az adattárban a következő `samples/hello-world` engedélyekkel: `content/write` és `content/read` .

1. A portálon keresse meg a tároló-beállításjegyzéket.
1. Az **Adattár engedélyei alatt** válassza a **Jogkivonatok (előzetes verzió) lehetőséget, > +Hozzáadás lehetőséget.**

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Jogkivonat létrehozása a portálon":::
1. Adja meg a jogkivonat nevét.
1. A **Hatókörtérkép alatt** válassza az **Új létrehozása lehetőséget.**
1. Konfigurálja a hatókörtérképet:
    1. Adja meg a hatókörtérkép nevét és leírását. 
    1. Az **Adattárak alatt írja be a**, majd a `samples/hello-world` et az Engedélyek **alatt,** válassza a és  `content/read` a `content/write` lehetőséget. Ezután válassza **a +Hozzáadás lehetőséget.**  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Hatókörtérkép létrehozása a portálon":::

    1. Az adattárak és engedélyek hozzáadása után válassza a **Hozzáadás** lehetőséget a hatókörtérkép hozzáadásához.
1. Fogadja el az alapértelmezett Token **Status** **(Engedélyezve) értéket,** majd válassza a **Create (Létrehozás) lehetőséget.**

A jogkivonat ellenőrzése és létrehozása után a jogkivonat részletei megjelennek a **Jogkivonatok képernyőn.**

### <a name="add-token-password"></a>Jogkivonat jelszavának hozzáadása

A portálon létrehozott jogkivonatok létrehozásához létre kell hoznia egy jelszót. Létrehozhat egy-két jelszót, és mindegyikhez lejárati dátumot állíthat be. 

1. A portálon lépjen a tároló-beállításjegyzékhez.
1. Az **Adattár engedélyei alatt válassza** a **Jogkivonatok (előzetes verzió)** lehetőséget, majd válasszon ki egy jogkivonatot.
1. A jogkivonat részletei között válassza a **password1** vagy **password2** lehetőséget, majd a Generálás ikont.
1. A jelszó képernyőn igény szerint beállíthatja a jelszó lejárati dátumát, majd válassza a **Generate (Generálás) lehetőséget.** Javasoljuk, hogy állítson be lejárati dátumot.
1. A jelszó létrehozása után másolja és mentse egy biztonságos helyre. A képernyő bezárása után nem kérhető le generált jelszó, de létrehozhat egy újat.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Jogkivonat jelszavának létrehozása a portálon":::

## <a name="authenticate-with-token"></a>Hitelesítés jogkivonattal

Amikor egy felhasználó vagy szolgáltatás jogkivonatot használ a cél beállításjegyzékben való hitelesítéshez, a jogkivonat nevét felhasználónévként és a létrehozott jelszavak egyikeként biztosítja. 

A hitelesítési módszer a jogkivonathoz társított konfigurált művelettől vagy műveletektől függ.

|Művelet  |Hitelesítés  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` az Azure CLI-ban<br/><br/>Például: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` az Azure CLI-ban<br/><br/>Például: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` az Azure CLI-ban     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` az Azure CLI-ban   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` az Azure CLI-ban |

## <a name="examples-use-token"></a>Példák: Jogkivonat használata

Az alábbi példák a cikkben korábban létrehozott jogkivonatot használják az adattár gyakori műveleteinek végrehajtásához: leküldéses és leküldéses rendszerképek, rendszerképek törlése és adattárcímkék listása. A jogkivonat először leküldési engedélyekkel (és műveletekkel) lett beállítva `content/write` `content/read` az `samples/hello-world` adattáron.

### <a name="pull-and-tag-test-images"></a>Tesztképek le- és címkézése

A következő példákban a nyilvános és a rendszerképeket a Microsoft Container Registry le, majd címkézd fel őket a regisztrációs adatbázishoz és `hello-world` `nginx` az adattárhoz.

```bash
docker pull mcr.microsoft.com/hello-world
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
docker tag mcr.microsoft.com/hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx:v1
```

### <a name="authenticate-using-token"></a>Hitelesítés jogkivonattal

A `docker login` vagy `az acr login` a futtatásával hitelesítheti magát a beállításjegyzékben a rendszerképek leküldéséhez vagy leküldéséhez. Felhasználónévként adja meg a jogkivonat nevét, és adja meg az egyik jelszavát. A jogkivonatnak állapottal kell `Enabled` lennie.

Az alábbi példa a Bash-rendszerhéjhoz van formázva, és környezeti változók használatával biztosítja az értékeket.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A kimenetben a sikeres hitelesítésnek kell lennie:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

A sikeres bejelentkezés után kísérelje meg leküldéssel a címkézett rendszerképeket a regisztrációs adatbázisba. Mivel a jogkivonat rendelkezik a rendszerképek adattárba való leküldéséhez szükséges engedélyekkel, a `samples/hello-world` következő leküldés sikeres lesz:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A jogkivonat nem rendelkezik engedélyekkel a repo-hoz, ezért a következő leküldéses kísérlet a következő hibához hasonló `samples/nginx` hibával `requested access to the resource is denied` meghiúsul:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

### <a name="update-token-permissions"></a>Jogkivonat engedélyeinek frissítése

Egy jogkivonat engedélyeinek frissítéséhez frissítse a társított hatókörtérképen található engedélyeket. A frissített hatókörtérképet a rendszer azonnal alkalmazza az összes társított jogkivonatra. 

Például frissítsen a és műveletekkel az adattáron, és távolítsa el a műveletet az `MyToken-scope-map` `content/write` `content/read` `samples/ngnx` `content/write` `samples/hello-world` adattáron.  

Az Azure CLI használatával az [az acr scope-map update][az-acr-scope-map-update] futtatásával frissítheti a hatókörtérképet:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/write content/read \
  --remove-repository samples/hello-world content/write 
```

Az Azure Portalon:

1. Lépjen a tároló-beállításjegyzékhez.
1. Az **Adattár engedélyei alatt válassza** a **Hatókörtérképek (előzetes verzió)** lehetőséget, majd válassza ki a frissíteni kívánt hatókörtérképet.
1. Az **Adattárak alatt írja be a**, majd a `samples/nginx` et az Engedélyek **alatt,** válassza a és `content/read` a `content/write` lehetőséget. Ezután válassza **a +Hozzáadás lehetőséget.**
1. Az **Adattárak alatt válassza** a és a lehetőséget az Engedélyek `samples/hello-world` **alatt,** majd törölje a kijelölését. `content/write` Kattintson a **Mentés** gombra.

A hatókörtérkép frissítése után a következő leküldés sikeres lesz:

```bash
docker push myregistry.azurecr.io/samples/nginx:v1
```

Mivel a hatókörtérkép csak az adattárra vonatkozó engedéllyel rendelkezik, az adattárba való leküldéses kísérlet `content/read` `samples/hello-world` most `samples/hello-world` meghiúsul:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A képek mindkét adattárból való lehúzása sikeres, mert a hatókörtérkép mindkét `content/read` adattárhoz biztosít engedélyeket:

```bash
docker pull myregistry.azurecr.io/samples/nginx:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Rendszerképek törlése

Frissítse a hatókörtérképet úgy, hogy hozzáadja a műveletet az `content/delete` `nginx` adattárhoz. Ez a művelet lehetővé teszi a lemezképek törlését az adattárban, vagy a teljes adattár törlését.

A rövidség kedvéért csak az [az acr scope-map update][az-acr-scope-map-update] parancsot mutatjuk be a hatókörtérkép frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/nginx content/delete
``` 

A hatókörtérkép portál használatával való frissítéséhez tekintse meg az előző [szakaszt.](#update-token-permissions)

Az adattár törléséhez használja az [az acr repository delete][az-acr-repository-delete] `samples/nginx` parancsot. Képek vagy adattárak törléséhez adja át a jogkivonat nevét és jelszavát a parancsnak. Az alábbi példa a cikkben korábban létrehozott környezeti változókat használja:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/nginx \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Az objektumcímkék megjelenítése 

Frissítse a hatókörtérképet úgy, hogy hozzáadja a műveletet az `metadata/read` `hello-world` adattárhoz. Ez a művelet lehetővé teszi a jegyzékfájl és a címkeadatok olvasását az adattárban.

A rövidség kedvéért csak az [az acr scope-map update][az-acr-scope-map-update] parancsot mutatjuk be a hatókörtérkép frissítéséhez:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add-repository samples/hello-world metadata/read 
```  

A hatókörtérkép portál használatával való frissítéséhez tekintse meg az előző [szakaszt.](#update-token-permissions)

Az adattár metaadatainak `samples/hello-world` beolvassa az [az acr repository show-manifests][az-acr-repository-show-manifests] vagy [az az acr repository show-tags][az-acr-repository-show-tags] parancsot. 

A metaadatok olvasásához adja át a jogkivonat nevét és jelszavát mindkét parancsnak. Az alábbi példa a cikkben korábban létrehozott környezeti változókat használja:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Példa a kimenetre:

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>Jogkivonatok és hatókörtérképek kezelése

### <a name="list-scope-maps"></a>Listahatókör-leképezések

Használja az [az acr scope-map list][az-acr-scope-map-list] parancsot vagy a portál Hatókörtérképek **(előzetes verzió)** képernyőjét a beállításjegyzékben konfigurált hatókörleképések listához. Például:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A kimenet az Ön által létrehozott három, rendszer által meghatározott hatókörtérképből és egyéb hatókörtérképből áll. A jogkivonatok bármelyik hatókörtérképpel konfigurálhatóak.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Jogkivonat részleteinek megjelenítése

Egy jogkivonat részleteinek ( például az állapot és a jelszó lejárati dátuma) megtekintéséhez futtassa az [az acr token show][az-acr-token-show] parancsot, vagy válassza ki a tokent a portál **Tokens (Előzetes verzió)** képernyőjén. Például:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Használja az [az acr token list parancsot][az-acr-token-list] vagy a portál Tokens (Preview) (Jogkivonatok **(előzetes verzió) képernyőjét a** regisztrációs adatbázisban konfigurált összes jogkivonat listához. Például:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Jogkivonatjelszavak újragenerálása

Ha nem generált jogkivonatjelszót, vagy ha új jelszavakat szeretne létrehozni, futtassa az [az acr token credential generate][az-acr-token-credential-generate] parancsot. 

Az alábbi példa új értéket hoz létre a *myToken* token password1 értékével, 30 napos lejárati időtartammal. A jelszót a környezeti változóban `TOKEN_PWD` tárolja. Ez a példa a Bash-rendszerhéjhoz van formázva.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --expiration-in-days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Ha a Azure Portal jogkivonat jelszavának létrehozására, tekintse meg a cikk korábbi, Jogkivonat létrehozása [– portál](#create-token---portal) lépéseit.

### <a name="update-token-with-new-scope-map"></a>Jogkivonat frissítése új hatókörtérképpel

Ha egy másik hatókörtérképes tokent szeretne frissíteni, futtassa [az az acr token update][az-acr-token-update] futtatását, és adja meg az új hatókörtérképet. Például:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

A portál token (előzetes **verzió) képernyőjén** válassza ki a jogkivonatot, majd a Hatókörtérkép alatt válasszon ki egy másik hatókörtérképet.

> [!TIP]
> Miután frissített egy jogkivonatot egy új hatókörtérképpel, érdemes lehet új jogkivonatjelszavokat létrehozni. Használja az [az acr token credential generate parancsot,][az-acr-token-credential-generate] vagy hozzon létre újra egy tokenjelszót a Azure Portal.

## <a name="disable-or-delete-token"></a>Jogkivonat letiltása vagy törlése

Előfordulhat, hogy ideiglenesen le kell tiltania a jogkivonat hitelesítő adatainak használatát egy felhasználó vagy szolgáltatás számára. 

Az Azure CLI-t használva futtassa [az az acr token update][az-acr-token-update] parancsot a `status` beállításának `disabled` beállítására:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

A portálon válassza ki a jogkivonatot a **Jogkivonatok (előzetes verzió)** képernyőn, majd válassza a **Letiltva** lehetőséget az **Állapot alatt.**

Ha törölni szeretne egy jogkivonatot, hogy a hitelesítő adataival véglegesen érvénytelenítse a hozzáférést, futtassa az [az acr token delete][az-acr-token-delete] parancsot. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

A portálon válassza ki a tokent a **Jogkivonatok (előzetes verzió)** képernyőn, majd válassza a Discard (Elvetés) **lehetőséget.**

## <a name="next-steps"></a>Következő lépések

* A hatókörtérképek és -jogkivonatok kezeléséhez használjon további parancsokat az [az acr scope-map][az-acr-scope-map] és [az az acr token][az-acr-token] parancscsoportokban.
* Az Azure [Container](container-registry-authentication.md) Registryvel való hitelesítés egyéb lehetőségeiről a hitelesítés áttekintésében olvashat, beleértve a Azure Active Directory-identitás, egy szolgáltatásnév vagy egy rendszergazdai fiók használatát.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az_acr_repository_show_manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az_acr_repository_delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az_acr_scope_map_create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az_acr_scope_map_update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az_acr_scope_map_list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az_acr_token_show
[az-acr-token-list]: /cli/azure/acr/token/#az_acr_token_list
[az-acr-token-delete]: /cli/azure/acr/token/#az_acr_token_delete
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-update]: /cli/azure/acr/token/#az_acr_token_update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az_acr_token_credential_generate
