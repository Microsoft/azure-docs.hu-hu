---
title: Batch Processing Kit a Speech containers szolgáltatáshoz
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési tárolóra vonatkozó kérelmek méretezése a Batch Processing kit használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631992"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Batch Processing Kit a Speech containers szolgáltatáshoz

A Batch Processing kit használatával kiegészítheti és bővítheti a számítási feladatokat a beszédfelismerési tárolókban. Ez a nyílt forráskódú segédprogram lehetővé teszi a kötegelt átírást a nagy számú hangfájlhoz, a helyszíni és a felhőalapú beszédfelismerési végpontok között. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Egy példa batch-Kit-tároló munkafolyamatot bemutató diagram.":::

A Batch Kit tároló ingyenesen elérhető a [githubon](https://github.com/microsoft/batch-processing-kit) és a   [Docker hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)-on. [Csak a](speech-container-howto.md#billing) használt beszédfelismerési tárolók kell fizetnie.

| Szolgáltatás  | Leírás  |
|---------|---------|
| Köteg hangfájljának eloszlása     | Nagy számú fájl automatikus küldése a helyszíni vagy a felhőalapú beszédfelismerési tároló végpontjai számára. A fájlok bármilyen POSIX-kompatibilis köteten lehetnek, beleértve a hálózati fájlrendszereket is.       |
| A Speech SDK integrációja | A Speech SDK közös jelzőit továbbíthatja, többek között a következőt: n-Best hipotézisek, diarization, nyelv, káromkodás maszkolása.  |
|Futtatási módok     | Futtassa a Batch-ügyfelet egyszer, folyamatosan a háttérben, vagy hozzon létre HTTP-végpontokat a hangfájlok számára.         |
| Hibatűrés | A rendszer automatikusan újrapróbálkozik és folytatja az átírást a folyamat elvesztése nélkül, és különbséget tesz a hibák között, és nem próbálkozhat újra a használatával. |
| Végpont elérhetőségének észlelése | Ha egy végpont elérhetetlenné válik, a Batch-ügyfél folytatja az átírást más tároló-végpontok használatával. Miután ismét elérhetővé vált, az ügyfél automatikusan elkezdi használni a végpontot.   |
| Végpontok gyors cseréje | A beszédfelismerési tároló végpontjának hozzáadása, eltávolítása vagy módosítása a Futtatás közben a Batch előrehaladásának megszakítása nélkül. A frissítések azonnaliek. |
| Valós idejű naplózás | A megkísérelt kérelmek, időbélyegek és meghibásodások valós idejű naplózása az egyes hangfájlok Speech SDK-naplófájljaival. |

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A Batch-készlet legújabb tárolójának letöltéséhez használja a [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) parancsot.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Végpont konfigurációja

A Batch-ügyfél egy YAML-konfigurációs fájlt vesz igénybe, amely megadja a helyszíni tároló-végpontokat. Az alábbi példa a következő példában látható `/mnt/my_nfs/config.yaml` :, amelyet az alábbi példákban használunk. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Ez a YAML-példa három állomáson három beszédfelismerő tárolót határoz meg. Az első gazdagép IPv4-címekkel van megadva, a második a Batch-ügyféllel azonos virtuális gépen fut, és a harmadik tárolót egy másik virtuális gép DNS-állomásneve adja meg. Az `concurrency` érték határozza meg az egyazon tárolón futtatható maximális egyidejű fájlok átírását. A `rtf` (valós idejű tényező) érték nem kötelező, és a teljesítmény finomhangolásához használható.
 
A Batch-ügyfél képes dinamikusan megállapítani, hogy egy végpont elérhetetlenné válik-e (például egy tároló újraindítása vagy hálózati probléma miatt), és ha újra elérhetővé válik. Az átírási kérelmek nem lesznek elküldve a nem elérhető tárolók számára, és az ügyfél továbbra is a többi elérhető tárolót fogja használni. A végpontokat bármikor hozzáadhatja, eltávolíthatja vagy szerkesztheti, a köteg előrehaladásának megszakítása nélkül.


## <a name="run-the-batch-processing-container"></a>A batch feldolgozó tároló futtatása
  
> [!NOTE] 
> * Ez a példa ugyanazt a könyvtárat ( `/my_nfs` ) használja a konfigurációs fájlhoz, valamint a bemenetek, a kimenetek és a naplók könyvtáraihoz. Ezekhez a mappákhoz üzemeltetett vagy NFS-hez csatlakoztatott címtárakat is használhat.
> * Ha a-ügyfelet futtatja, a a `–h` elérhető parancssori paramétereket és azok alapértelmezett értékeit fogja listázni. 
> * A batch-feldolgozó tároló csak Linux rendszeren támogatott.

A `run` tároló elindításához használja a Docker parancsot. Ez egy interaktív felületet indít el a tárolón belül.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

A Batch-ügyfél futtatása:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

A Batch-ügyfél és-tároló futtatása egyetlen paranccsal:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


Az ügyfél elindul. Ha egy hangfájl már egy korábbi futtatás során lett lemásolva, az ügyfél automatikusan kihagyja a fájlt. A rendszer automatikusan újrapróbálkozik a fájlok küldésével, ha átmeneti hibák történnek, és megkülönböztetheti egymástól, hogy az ügyfél milyen hibákat próbálkozzon újra. Átírási hiba esetén az ügyfél továbbra is átírást folytat, és a folyamat elvesztése nélkül újra próbálkozik.  

## <a name="run-modes"></a>Futtatási módok 

A Batch Processing Kit három módot kínál a `--run-mode` paraméter használatával.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` a Mode (egy bemeneti könyvtárból és választható fájlokból) származó hangfájlok egyetlen kötegét írja át egy kimeneti mappába.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Egy diagram, amely a Batch-Kit tároló oneshot módban történő feldolgozását mutatja.":::

1. Adja meg azokat a Speech Container-végpontokat, amelyeket a Batch-ügyfél a fájlban fog használni `config.yaml` . 
2. Helyezzen hangfájlokat az átíráshoz egy bemeneti könyvtárba.  
3. Hívja meg a tárolót a címtárban, amely megkezdi a fájlok feldolgozását. Ha a hangfájl már egy korábbi, ugyanazzal a kimeneti könyvtárral lett lemásolva (ugyanaz a fájlnév és ellenőrzőösszeg), akkor az ügyfél kihagyja a fájlt. 
4. A fájlokat a rendszer az 1. lépésben a tároló végpontjai számára küldi el.
5. A naplókat és a beszédfelismerési tároló kimenetét a rendszer a megadott kimeneti könyvtárba adja vissza. 

#### <a name="daemon"></a>[Démon](#tab/daemon)

> [!TIP]
> Ha egyszerre több fájlt ad hozzá a bemeneti könyvtárhoz, akkor a teljesítményt a rendszeres időközönként való hozzáadásával javíthatja.

`DAEMON` a Mode egy adott mappában lévő meglévő fájlokat írja át, és folyamatosan átmásolja az új hangfájlokat a hozzáadásuk során.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Egy olyan diagram, amely a Batch-Kit tárolók feldolgozási fájljait démon módban dolgozza fel.":::

1. Adja meg azokat a Speech Container-végpontokat, amelyeket a Batch-ügyfél a fájlban fog használni `config.yaml` . 
2. Hívja meg a tárolót egy bemeneti könyvtáron. A Batch-ügyfél megkezdi a bejövő fájlok könyvtárának figyelését. 
3. Folyamatos hangfájl-kézbesítés beállítása a bemeneti könyvtárba. Ha a hangfájl már egy korábbi, ugyanazzal a kimeneti könyvtárral lett lemásolva (ugyanaz a fájlnév és ellenőrzőösszeg), akkor az ügyfél kihagyja a fájlt. 
4. Ha a rendszer írási vagy POSIX-jelet észlel, a tároló a válaszra vált.
5. A fájlokat a rendszer az 1. lépésben a tároló végpontjai számára küldi el.
6. A naplókat és a beszédfelismerési tároló kimenetét a rendszer a megadott kimeneti könyvtárba adja vissza. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` a Mode egy API-kiszolgáló mód, amely alapszintű HTTP-végpontokat biztosít a hangfájlok kötegelt küldéséhez, az állapot-ellenőrzéshez és a hosszú lekérdezésekhez. A lehetővé teszi a programozott felhasználást egy Python-modul bővítmény vagy egy almodulként való importálás használatával.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Egy diagram, amely a Batch-Kit tárolóban lévő fájlokat a REST módban dolgozza fel.":::

1. Adja meg azokat a Speech Container-végpontokat, amelyeket a Batch-ügyfél a fájlban fog használni `config.yaml` . 
2. HTTP-kérelmek küldésére vonatkozó kérelem küldése az API-kiszolgáló egyik végpontjának. 
        
    |Végpont  |Leírás  |
    |---------|---------|
    |`/submit`     | Végpont új batch-kérelmek létrehozásához.        |
    |`/status`     | Egy batch-kérelem állapotának ellenőrzéséhez használt végpont. A hálózat nyitva marad, amíg a köteg be nem fejeződik.       |
    |`/watch`     | Végpont a HTTP-alapú hosszú lekérdezések futtatásához, amíg a köteg be nem fejeződik.        |

3. A rendszer feltölti a hangfájlokat a bemeneti könyvtárból. Ha a hangfájl már egy korábbi, ugyanazzal a kimeneti könyvtárral lett lemásolva (ugyanaz a fájlnév és ellenőrzőösszeg), akkor az ügyfél kihagyja a fájlt. 
4. Ha a rendszer elküldi a kérést a `/submit` végpontnak, a fájlokat a rendszer az 1. lépésben elküldi a tárolói végpontoknak.
5. A naplókat és a beszédfelismerési tároló kimenetét a rendszer a megadott kimeneti könyvtárba adja vissza. 

---

## <a name="logging"></a>Naplózás

> [!NOTE]
> Előfordulhat, hogy a Batch-ügyfél időnként felülírja a *Run. log* fájlt, ha az túl nagy lesz.

Az ügyfél létrehoz egy *Run. log* fájlt a `-log_folder` Docker parancs argumentumában megadott könyvtárban `run` . Alapértelmezés szerint a rendszer a naplókat a HIBAKERESÉSi szinten rögzíti. Ugyanazokat a naplókat a rendszer elküldi a `stdout/stderr` -nek, és a (z `-file_log_level` ) vagy argumentumtól függően szűrve lesz `console_log_level` . Ez a napló csak hibakereséshez szükséges, vagy ha nyomkövetést kell küldenie a támogatáshoz. A naplózási mappa az egyes hangfájlok Speech SDK-naplóit is tartalmazza.

A által megadott kimeneti könyvtár `-output_folder` tartalmaz egy *run_summary.jst a*   fájlon, amely minden esetben 30 másodpercenként újraírható, vagy ha az új átírások befejeződtek. Ezt a fájlt használhatja a folyamat előrehaladásának ellenőrzéséhez. Emellett az összes fájl végleges futtatási statisztikáit és végső állapotát is tartalmazza a köteg befejezésekor. A köteg akkor fejeződik be, amikor a folyamat tiszta kijárattal rendelkezik. 

## <a name="next-steps"></a>Következő lépések

* [Tárolók telepítése és futtatása](speech-container-howto.md)
