---
title: Gyakori problémák megoldása
description: Ismerje meg, hogyan háríthatja el az üzembe helyezés, futtatás vagy kezelés során gyakran Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ce7e3018e470df3840eb01127a7bf2ffa01b5cbc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771064"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Az Azure Container Instances gyakori hibáinak elhárítása

Ez a cikk bemutatja, hogyan háríthatja el a tárolók kezelésével vagy üzembe helyezésével kapcsolatos gyakori Azure Container Instances. Lásd [még: Gyakori kérdések.](container-instances-faq.md)

Ha további támogatásra van szüksége, tekintse meg az elérhető **Súgó és** támogatás lehetőségeket a [Azure Portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="issues-during-container-group-deployment"></a>A tárolócsoport üzembe helyezése során problémák
### <a name="naming-conventions"></a>Elnevezési konvenciók

A tároló specifikációi meghatározásakor bizonyos paramétereknek meg kell felelniük az elnevezési korlátozásoknak. Az alábbi táblázat a tárolócsoport tulajdonságaira vonatkozó követelményeket tartalmazza. További információ: [Elnevezési][azure-name-restrictions] konvenciók a Azure Architecture Center és az Azure-erőforrások elnevezési szabályai [és korlátozásai.][naming-rules]

| Hatókör | Hossz | Kis- és nagybetűk | Érvényes karakterek | Javasolt minta | Példa |
| --- | --- | --- | --- | --- | --- |
| Tároló neve<sup>1</sup> | 1–63 |Kisbetűs | Alfanumerikus karakterek és kötőjel az első vagy az utolsó karakter kivételével bárhol |`<name>-<role>-container<number>` |`web-batch-container1` |
| Tárolóportok | 1 és 65535 között |Egész szám |Egész szám 1 és 65535 között |`<port-number>` |`443` |
| DNS-névcímke | 5-63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus karakterek és kötőjel az első vagy az utolsó karakter kivételével bárhol |`<name>` |`frontend-site1` |
| Környezeti változó | 1–63 |Kis- és nagybetűk megkülönböztetése nélkül |Alfanumerikus és aláhúzásjel (_) az első vagy az utolsó karakter kivételével bárhol |`<name>` |`MY_VARIABLE` |
| Kötet neve | 5-63 |Kisbetűs |Alfanumerikus karakterek és kötőjelek bárhol, kivéve az első vagy az utolsó karaktert. Nem tartalmazhat két egymást követő kötőjelet. |`<name>` |`batch-output-volume` |

<sup>1</sup> A tárolócsoport-nevekre vonatkozó korlátozások, ha nincs megadva a tárolópéldánytól függetlenül, például `az container create` parancstelepítések esetén.

### <a name="os-version-of-image-not-supported"></a>A rendszerkép operációsrendszer-verziója nem támogatott

Ha olyan rendszerképet ad meg, Azure Container Instances nem támogatja, a rendszer `OsVersionNotSupported` hibát ad vissza. A hiba az alábbihoz hasonló, ahol a az üzembe helyezni kívánt rendszerkép `{0}` neve:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ez a hiba leggyakrabban a Semi-Annual Channel 1709-es vagy 1803-as kiadásán alapuló, nem támogatott Windows-rendszerképek telepítésekor fordul elő. A támogatott Windows-rendszerképeket Azure Container Instances: [Gyakori kérdések.](container-instances-faq.md#what-windows-base-os-images-are-supported)

### <a name="unable-to-pull-image"></a>Nem sikerült lekért rendszerkép

Ha Azure Container Instances nem tudja lekért rendszerképet, akkor egy ideig újra fog tudni újraküldeni. Ha a rendszerkép lekért művelete továbbra is sikertelen, az ACI végül meghiúsul az üzembe helyezés során, és hibaüzenet jelenhet `Failed to pull image` meg.

A probléma megoldásához törölje a tárolópéldányt, és próbálja meg újra az üzembe helyezést. Ellenőrizze, hogy a rendszerkép létezik-e a beállításjegyzékben, és hogy helyesen gépelte-e be a rendszerkép nevét.

Ha a rendszerkép nem lekért, az alábbihoz hasonló események jelennek meg az [az container show kimenetében:][az-container-show]

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Az erőforrás nem érhető el hiba

Az Azure-ban változó regionális erőforrás-terhelés miatt a következő hibaüzenet jelenhet meg a tárolópéldány üzembe helyezésekor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy az üzembe helyezési régió nagy terhelése miatt a tárolóhoz megadott erőforrások nem lesznek lefoglalva. A probléma megoldásához kövesse az alábbi elhárítás lépéseit.

* Győződjön meg arról, hogy a tároló üzembehelyi beállításai a régiónkénti [rendelkezésre állásban meghatározott paramétereken belül Azure Container Instances](container-instances-region-availability.md)
* Alacsonyabb processzor- és memóriabeállítások megadása a tárolóhoz
* Üzembe helyezés másik Azure-régióban
* Üzembe helyezés később

## <a name="issues-during-container-group-runtime"></a>A tárolócsoport futásidejű problémái
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>A tároló folyamatosan kilép és újraindul (nem hosszan futó folyamat)

A tárolócsoportok alapértelmezés szerint [mindig](container-instances-restart-policy.md) újraindítási szabályzatot hoznak **létre,** így a tárolócsoportban található tárolók a futtatás befejezése után mindig újraindulnak. Ha feladatalapú tárolókat kíván futtatni, ezt **az OnFailure** vagy **Never** (Soha) beállításokra kell módosítania. Ha az **OnFailure** (Hiba esetén) értéket adja meg, és továbbra is folyamatos újraindításokat lát, előfordulhat, hogy probléma van a tárolóban végrehajtott alkalmazással vagy szkriptekkel.

Ha hosszan futó folyamatok nélkül futtat tárolócsoportokat, előfordulhat, hogy ismétlődő kilépéseket és újraindításokat lát olyan rendszerképekkel, mint az Ubuntu vagy az Alpine. Az [EXEC-n](container-instances-exec.md) keresztüli csatlakozás nem fog működni, mivel a tárolónak nincs olyan folyamata, amely életben tarthatja azt. A probléma megoldásához a tárolócsoport üzemelő példányával együtt egy ehhez hasonló indítási parancsot is be kell foglalnia a tároló futtatásához.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

A Container Instances API és Azure Portal tartalmaz egy `restartCount` tulajdonságot. A tároló újraindítási számának ellenőrzéshez használhatja az [az container show][az-container-show] parancsot az Azure CLI-ban. Az alábbi példakimenetben (amelyet rövidség kedvéért csonkoltunk) a kimenet végén láthatja a `restartCount` tulajdonságot.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A Linux-disztribúciókhoz a legtöbb tároló rendszerképe alapértelmezett parancsként beállít egy rendszerhéjat, például a basht. Mivel a rendszerhéj maga nem hosszú ideig futó szolgáltatás, ezek a tárolók azonnal kilépnek, és újraindítási hurokba lépnek, ha az alapértelmezett Always restart **szabályzattal vannak** konfigurálva.

### <a name="container-takes-a-long-time-to-start"></a>A tároló indítása hosszú időt vesz igénybe

A tárolók indítási idejét befolyásoló három elsődleges tényező a Azure Container Instances tényező:

* [Képméret](#image-size)
* [Kép helye](#image-location)
* [Gyorsítótárazott rendszerképek](#cached-images)

A Windows-rendszerképekhez [további szempontokat is figyelembe kell venni.](#cached-images)

#### <a name="image-size"></a>Képméret

Ha a tároló elindulása hosszú időt vesz igénybe, de végül sikerrel jár, kezdje a tároló rendszerképének méretével. Mivel Azure Container Instances a rendszer igény szerint lekérte a tárolólemezképet, a látható indítási idő közvetlenül függ a méretétől.

A tároló rendszerképének méretét a Docker parancssori felületének parancsával `docker images` tudja megtekinteni:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A rendszerképméretek kis méretben való tartásának kulcsa annak biztosítása, hogy a végső lemezkép ne tartalmaz olyan adatokat, amelyek futásidőben nem szükségesek. Ennek egyik módja a többszakaszos [buildek.][docker-multi-stage-builds] A többszakaszos buildek megkönnyítik annak biztosítását, hogy a végső rendszerkép csak az alkalmazáshoz szükséges összetevőkből ássa le a buildhez szükséges tartalmakat, és ne az összeállításkor szükséges további tartalmakat.

#### <a name="image-location"></a>Kép helye

A rendszerkép lekért számának a tároló indítási idejét csökkentő másik módja, ha a tárolólemezképet egy [Azure Container Registry-ban](../container-registry/index.yml) tárolja ugyanabban a régióban, ahol tárolópéldányokat kíván üzembe helyezni. Ez lerövidíti a tároló rendszerképének utazásához szükséges hálózati útvonalat, ami jelentősen lerövidíti a letöltési időt.

#### <a name="cached-images"></a>Gyorsítótárazott képek

Azure Container Instances egy gyorsítótárazó mechanizmust használ a tárolók indítási időének felgyorsítása érdekében a gyakori Windows-alaprendszerképeken (például , és ) készült [](container-instances-faq.md#what-windows-base-os-images-are-supported) `nanoserver:1809` `servercore:ltsc2019` rendszerképeknél. `servercore:1809` A gyakran használt Linux-rendszerképek, például a `ubuntu:1604` és `alpine:3.6` a szintén gyorsítótárazva vannak. Windows- és Linux-rendszerképek esetén ne használja a `latest` címkét. Útmutatásért Container Registry tekintse át a [képcímkékre vonatkozó ajánlott](../container-registry/container-registry-image-tag-version.md) eljárásokat. A gyorsítótárazott rendszerképek és címkék naprakész listájáért használja a Gyorsítótárazott képek listása [API-t.][list-cached-images]

> [!NOTE]
> A Windows Server 2019-alapú rendszerképek használata a Azure Container Instances előzetes verzióban érhető el.

#### <a name="windows-containers-slow-network-readiness"></a>A Windows-tárolók lassú hálózati készenlétben vannak

A kezdeti létrehozáskor előfordulhat, hogy a Windows-tárolók legfeljebb 30 másodpercig (vagy ritka esetekben) nem csatlakoznak bejövő vagy kimenő kapcsolattal. Ha a tárolóalkalmazásnak internetkapcsolatra van szüksége, adjon hozzá késleltetési és újrapróbálkozási logikát, amely 30 másodpercet biztosít az internetkapcsolat létesítése érdekében. A kezdeti beállítás után a tárolóhálózat megfelelő módon folytatódik.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nem lehet csatlakozni a mögöttes Docker API-hoz, vagy nem lehet kiemelt tárolókat futtatni

Azure Container Instances nem teszi elérhetővé közvetlen hozzáférést a tárolócsoportokat tartalmazó mögöttes infrastruktúrához. Ez magában foglalja a tároló gazdagépen futó Docker API-hoz és a kiemelt tárolók futtatásához való hozzáférést. Ha Docker-interakcióra van szüksége, tekintse meg a [REST-referenciadokumentációt](/rest/api/container-instances/) az ACI API által támogatott műveletekért. Ha valami hiányzik, küldjön egy kérést az [ACI visszajelzési fórumain.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Előfordulhat, hogy a tárolócsoport IP-címe nem érhető el az eltérő portok miatt

Azure Container Instances a normál Docker-konfigurációhoz hasonló portleképezést még nem támogatja. Ha úgy találja, hogy a tárolócsoport IP-címe nem érhető el, amikor úgy gondolja, hogy annak kell lennie, győződjön meg arról, hogy a tároló rendszerképét úgy konfigurálta, hogy a tárolócsoportban elérhetővé tegye a portokat a `ports` tulajdonsággal.

Ha meg szeretné erősíteni, hogy a Azure Container Instances tud-e figyelni a tároló rendszerképében konfigurált portot, tesztelje a portot elérhetővé t tároló rendszerkép üzembe `aci-helloworld` helyezését. Futtassa az `aci-helloworld` alkalmazást is, hogy az a porton figyeljen. `aci-helloworld` A nem kötelező környezeti változót fogad el az alapértelmezett `PORT` 80-as port felülbírálása érdekében. A 9000-es port teszteléséhez például állítsa be a környezeti [változót](container-instances-environment-variables.md) a tárolócsoport létrehozásakor:

1. Állítsa be a tárolócsoportot úgy, hogy az elérhetővé tegye a 9000-es portot, és adja meg a portszámot a környezeti változó értékeként. A példa a Bash-rendszerhéjhoz van formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort részesíti előnyben, ennek megfelelően kell módosítania a változó-hozzárendelést.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Keresse meg a tárolócsoport IP-címét a parancs `az container create` kimenetében. Keresse meg az **ip értéket.** 
1. A tároló sikeres kiépítése után keresse meg a tárolóalkalmazás IP-címét és portját a böngészőben, például: `192.0.2.0:9000` . 

    A "Üdvözli a Azure Container Instances!" üzenetet jelenít meg a webalkalmazás.
1. Ha végzett a tárolóval, távolítsa el az `az container delete` paranccsal:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [olvashatja be a tárolók naplóit](container-instances-get-logs.md) és eseményeit a tárolók hibakeresésének segítése során.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
