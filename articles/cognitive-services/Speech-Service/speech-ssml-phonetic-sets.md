---
title: Beszédfelismerési fonetikus készletek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a beszédfelismerési szolgáltatás fonetikus ábécéje hogyan képezi le a nemzetközi fonetikus ábécét (IPA), és hogy mikor kell használni a készletet.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 580de0112c99eef4ba22ba946f6a2b81bc326001
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432043"
---
# <a name="speech-service-phonetic-sets"></a>Beszédfelismerési szolgáltatás fonetikus készletei

A beszédfelismerési szolgáltatás hét nyelvből álló fonetikus ábécéket ("telefonos készleteket") határoz meg. ,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` , `zh-CN` és `zh-TW` . A beszédfelismerési szolgáltatás telefonos beállítása általában a <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">nemzetközi fonetikus ábécé (IPA) </a>leképezésére szolgál. A beszédfelismerési szolgáltatás telefonos készletei a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)szolgáltatással együtt használatosak a szöveg-beszéd szolgáltatási ajánlat részeként. Ebből a cikkből megtudhatja, hogyan vannak leképezve ezek a telefonos készletek, és hogy mikor kell használni a beállított telefont.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Angol suprasegmentals

| 1. példa (a mássalhangzó kezdetét, a Word kezdeti a magánhangzó esetében) | 2. példa (a mássalhangzóhoz tartozó, Word mediális nucleus for magánhangzó) | 3. példa (CODA for mássalhangzó, Word Final for magánhangzó) | Megjegyzések |
|--|--|--|--|
| Burger/b **1** r-g AX r/ | Falafel/f AX-l AA **1** -f AX l/ | Guitar/g Administrators IH-t AA **1** r/ | Beszédfelismerési szolgáltatás telefonos beállítása a Kiemelt szótag magánhangzója után |
| alkalmatlan/IH **2** -n AA-p AX r-t ELTE 1 n/ | hasonlóság/d IH-s IH **2**-m AX-l eh 1-r AX-t iy/ | munkaerő/w 1 r-f Ao **2** r/ | Beszédfelismerési szolgáltatás telefonos beállítása a kihangsúlyozatlan szótag magánhangzója után |

### <a name="english-vowels"></a>Angol magánhangzók

| `sapi` | `ipa` | 1\. példa     | 2\. példa | 3\. példa                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy     | `i`   | **EA** t       | f **EE** l  | vall **ey**                  |
| IH     | `ɪ`   | **i** f        | f **i** ll  |                             |
| ey     | `eɪ`  | **a** te       | g **a** te  | d **ay**                     |
| EH     | `ɛ`   | **e** nagyon     | p **e** t   | m **eh** (ritka szó végül) |
| AE     | `æ`   | **egy** ctive    | c **a** t   | n **ah** (ritka szó végül) |
| aa     | `ɑ`   | **o** bstinate | p **o** PPY | r **ah** (ritka szó végül) |
| Ao     | `ɔ`   | **o**-tartomány    | c **au** se | Ut **ah**                    |
| Elfelejtett áttekintéscsempét     | `ʊ`   | b **OO** k      |           |                             |
| ezése     | `oʊ`  | **o** ld       | CL **o** ne | g **o**                      |
| ELTE     | `u`   | **U** BER      | b **OO** St | t **OO**                     |
| Ah     | `ʌ`   | **u** ncle     | c **u** t   |                             |
| Ay     | `aɪ`  | **i** CE       | b **i** te  | FL **y**                     |
| AW     | `aʊ`  | t **szervezeti egység**       | s **ou** th | c **ow**                     |
| Oy     | `ɔɪ`  | **oi** l       | j **oi** n  | t **Oy**                     |
| y ELTE   | `ju`  | **Yu** ma      | h **u** Man | f **EW**                     |
| AX     | `ə`   | **egy** ugrás       | WOM **n** | **a**                    |

### <a name="english-r-colored-vowels"></a>Angol R-színű magánhangzók

| `sapi` | `ipa` | 1\. példa    | 2\. példa      | 3\. példa  |
|--------|-------|--------------|----------------|------------|
| r. IH   | `ɪɹ`  | **fül** s     | t **IR**-amisu   | n **fül**   |
| Mi r   | `ɛɹ`  | **légi** sík | App **AR**-ently | SC **AR** e  |
| r. uh   | `ʊɹ`  |              |                | c **az** e   |
| r   | `aɪɹ` | **Ire**-föld  | f **IR**-EPlace  | CH **OIR**  |
| r   | `aʊɹ` | **óra** s    | p **kisbetűk** ful   | s **a**   |
| Ao r   | `ɔɹ`  | **vagy** Ange   | m **vagy** Al      | s **evezős**   |
| AA r   | `ɑɹ`  | **AR** TIST   | St **AR**      | c **AR**    |
| r   | `ɝ`   | **fül** th    | b **IR** d       | f **a**    |
| AX r   | `ɚ`   |              | összes **er** gy    | Supp **er** |

### <a name="english-semivowels"></a>Angol Semivowels

| `sapi` | `ipa` | 1\. példa           | 2\. példa  | 3\. példa |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w** edik, s **UE** de | Al **w** ays |           |
| é      | `j`   | **y** ARD, f **e** w   | **bekapcsolva**  |           |

### <a name="english-aspirated-oral-stops"></a>Az angol szívós szóbeli leáll

| `sapi` | `ipa` | 1\. példa | 2\. példa   | 3\. példa  |
|--------|-------|-----------|-------------|------------|
| P      | `p`   | **p** ut   | Ha **PP** en  | FLA **p**   |
| b      | `b`   | **b** ig   | **b** számú er  | **b** . CRA   |
| t      | `t`   | **t** alk  | CAPI **t** Al | sough **t** |
| n      | `d`   | **d** ig   | **d** om futott  | ro **d**    |
| k      | `k`   | **c** ut   | SLA –**CK** | IRA **q**   |
| g      | `g`   | **g** o    | egy **g** o     | DRA **g**   |

### <a name="english-nasal-stops"></a>Angol orr leáll

| `sapi` | `ipa` | 1\. példa        | 2\. példa  | 3\. példa   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**, Smash   | CA **m** Era | Roo **m**    |
| p      | `n`   | **n** o, s **n** ow | te **n** t   | naposcsibe **n** |
| ng     | `ŋ`   |                  | Li **n** k   | s **ing**    |

### <a name="english-fricatives"></a>Angol fricatives

| `sapi` | `ipa` | 1\. példa   | 2\. példa        | 3\. példa  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f** ork    | le **f** t         | hal **f**   |
| v      | `v`   | **v** alue   | e **v** ENT        | Lo **v** e   |
| adik     | `θ`   | **th**    | EMPA **th** y      | Mon **th**  |
| DH     | `ð`   | **th** en    | Mo **th**       | smoo **th** |
| s      | `s`   | **s**     | **s** k         | tény **s**  |
| z      | `z`   | **z** AP     | Bu **s** y         | Kid **s**   |
| sh     | `ʃ`   | e. **sh**    | abbrevia | ru **sh**   |
| zh     | `ʒ`   | **J** acques | alapjogalap     | **g** e |
| ó      | `h`   | **h** ELP    | EN **h**-vel      | a-**h** a!  |

### <a name="english-affricates"></a>Angol affricates

| `sapi` | `ipa` | 1\. példa | 2\. példa    | 3\. példa  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **CH** a  | **t** ure   | támadási **CH** |
| JH     | `dʒ`  | **j** Oy   | Ori **g** inal | **g** e |

### <a name="english-approximants"></a>Angol approximants

| `sapi` | `ipa` | 1\. példa          | 2\. példa  | 3\. példa |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l** azonosító, g **l** ad  | PA **l** Ace | Chi **ll** |
| r      | `ɹ`   | **r** ED, b **r** | Bo **RR** ow | ta **r**   |

# <a name="fr-fr"></a>[fr – FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Francia suprasegmentals

A beszédfelismerési szolgáltatás telefonos beállítása a kihangsúlyozott szótag magánhangzója után, azonban a `fr-FR` beszédfelismerési szolgáltatás telefonos készlete nem támogatja a (z) ˌ IPA-alterhelést. Ha az IPA-alterhelésre van szükség, közvetlenül az IPA-t kell használnia.

### <a name="french-vowels"></a>Francia magánhangzók

| `sapi` | `ipa` | 1\. példa     | 2\. példa       | 3\. példa |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **egy** rbre     | p **a** TTE       | IR **a**   |
| aa     | `ɑ`   |               | p **,** te        | p **a** s   |
| AA ~   | `ɑ̃`  | **en** fant    | Emellett ENF igazolást **en** t      | t **em** PS |
| AX     | `ə`   |               | p **e** Tite      | l **e**    |
| EH     | `ɛ`   | **e** LLE      | p **e** RDU       | ét **AI** t |
| EU     | `ø`   | **œu** FS      | CR **EU** ser     | Qu **EU**  |
| ey     | `e`   | ému           | crétin          | ôté       |
| EH ~   | `ɛ̃`  | **im**-hordozhatóság | p **Ein**    | mat **a** |
| iy     | `i`   | **dée**      | PET **i** te      | **i**   |
| OE     | `œ`   | f **œu**       | p **EU** r        |           |
| ó     | `ɔ`   | **o** bstacle  | c **o** RPS       |           |
| Oh ~   | `ɔ̃`  | **on** ze      | r **on** deur     | b **bekapcsolva**   |
| ezése     | `o`   | **au**-diteur  | b **Eau** Coup    | p    |
| OE ~   | `œ̃ ` | **ENSZ**        | l **un** di       | br **un**  |
| ELTE     | `u`   | **szervezeti egység** trage   | intr **ou** vable | **ou**    |
| uy     | `y`   | **u** ne       | p **u** Nir       | él **u**   |

### <a name="french-consonants"></a>Francia mássalhangzók

| `sapi` | `ipa` | 1\. példa   | 2\. példa     | 3\. példa                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b** ête    | Ha **b**– ille   | **b**. e. e                         |
| n      | `d`   | **d** Ire    | **d**. Ron EUR   | **d** e                       |
| f      | `f`   | **f** Emme   | Su **FF** IXE   | Bo **f**                          |
| g      | `g`   | **g** auche  | é **g** ALE     | ba-**Gu** e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)parkoló |
| Hy     | `ɥ`   | h **u** Ile   | n **u** Ire     |                                  |
| k      | `k`   | **c** Arte   | é **c** aille   | legyen **c**                          |
| l      | `l`   | **l** kikerülő    | é **l** Ire     | ba **l**                          |
| m      | `m`   | **m** Ádám  | AI **m** er     | Po **mm** e                        |
| p      | `n`   | **n** szervezeti egység    | te **n** IR     | Bo **NN** e                        |
| egyet     | `ɲ`   |             |               | Pei **GN** e                       |
| P      | `p`   | **p** Atte   | **p** mint     | CA **p**                          |
| r      | `ʁ`   | **r**–     | Cha **r** IOT   | Senti **r**                       |
| s      | `s`   | **s** Ourir  | **SS** ez     | PA **SS** e                        |
| sh     | `ʃ`   | **CH**-ante | ma **CH** ine   | Po **CH** e                        |
| t      | `t`   | **t** ête    | **t** er      | ne **t**                          |
| v      | `v`   | **v** ENT    | a **v**-ban adja meg  | rê **v** e                         |
| w      | `w`   | **szervezeti egység**     | f **ou** ine    |                                  |
| é      | `j`   | **y** od     | p **i** étiner  | Marsa **ille**                    |
| z      | `z`   | * * z * * éro   | Rai **s** onner | ro **s** e                         |
| zh     | `ʒ`   | **j** Madárné Németh Marcsika  | Man **g** er    | Pié **g** e                        |
|        | `n‿`  |             |               | u **n** Arbre                     |
|        | `t‿`  |             |               | **d** . Quan                        |
|        | `z‿`  |             |               | di **x**                          |

<a id="fr-1"></a>
**1** *csak bizonyos idegen szavak esetében.*

> [!TIP]
> A `fr-FR` beszédfelismerési szolgáltatás telefonos készlete nem támogatja a következő francia liasions:,, `n‿` `t‿` és `z‿` . Ha szükségesek, érdemes lehet az IPA használatát közvetlenül használni.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Német suprasegmentals

| 1. példa (a mássalhangzó kezdetét, a Word kezdeti a magánhangzó esetében) | 2. példa (a mássalhangzóhoz tartozó, Word mediális nucleus for magánhangzó) | 3. példa (CODA for mássalhangzó, Word Final for magánhangzó) | Megjegyzések |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m uh l-t iy-p l iy-k a-TS y ow **1** n s-TS ay-c n/ | Biologie/b iy-ow-l ow-g iy **1**/ | Beszédfelismerési szolgáltatás telefonos beállítása a Kiemelt szótag magánhangzója után |
| Allgemeinwissen/a **2** l-g AX-m ay 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z Oh **2** AX r-g uh ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y ELTE 1-t AX r-t ow-m ow-g r a-f iy **2**/ | Beszédfelismerési szolgáltatás telefonos beállítása a kihangsúlyozatlan szótag magánhangzója után |

### <a name="german-vowels"></a>Német magánhangzók

| `sapi` | `ipa`     | 1\. példa                             | 2\. példa     | 3\. példa                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| egy     | `aː`      | **Egy** BER                              | Maßst **b**   | Schem                         |
| a      | `a`       | **Egy** bfall                            | B **egy** CH      | Agath                         |
| ó     | `ɔ`       | **O**                             | PF **Oláh**   |                                    |
| EH    | `ɛː`      | **Ä** hnlichkeit                       | B **ä** r       | [<sup>1</sup>](#de-v-1) Érde **AE** |
| EH     | `ɛ`       | **ä** ndern                            | ProZ **e** NT   | Amygdal **AE**                      |
| AX     | `ə`       | [<sup>2</sup>](#de-v-2)' v **e** rstauen | Aach **e** n    | Frag **e**                          |
| iy     | `iː`      | **Futottam**                              | ABB **IE** gt   | Relativitätstheor **IE**            |
| IH     | `ɪ`       | **Nnung**                            | NGEN    | Fa **y**                          |
| EU     | `øː`      | **Ö** sen                              | abl **√** a  | Malm **ö**                          |
| ezése     | `o`, `oː` | **o** hnE                              | Az **o** n kikapcsolása    | Trept **ow**                        |
| OE     | `œ`       | **Ö** ffnung                           | BEF **√** rdern |                                    |
| ey     | `e`, `eː` | **E** berhard                          | ABF **e** gt    | b                                  |
| ELTE     | `uː`      | **U** do                               | H **u** t       | Akk **u**                           |
| Elfelejtett áttekintéscsempét     | `ʊ`       | **U** nterschiedes                     | b **u** NT      |                                    |
| UE     | `yː`      | **Ü** bermut                           | PFL **ü** gt    | Férfi **ü**                           |
| uy     | `ʏ`       | **ü** ppig                             | S **y** szár    |                                    |

<a id="de-v-1"></a>
**1** *csak idegen eredetű szavakban, például: érde **AE**.*<br>
<a id="de-v-2"></a>
**2** *szó – a intially csak idegen eredetű szavak, például ppointment . Szótag – kezdetben a következőben: "v **e** rstauen.*

### <a name="german-diphthong"></a>Német diphthong

| `sapi` | `ipa`       | 1\. példa    | 2\. példa          | 3\. példa |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei** nsam   | Unabhängigk **ei** t | Abt **ei** |
| AW     | `au`        | **au**-ßen    | ABB **au** St        | St **au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **EU** phorie | TR **äu** MT         | SCH **EU** |

### <a name="german-semivowels"></a>Német semivowels

| `sapi` | `ipa` | 1\. példa | 2\. példa    | 3\. példa  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd **n** | zárolás **er** |

### <a name="german-consonants"></a>Német mássalhangzók

| `sapi` | `ipa` | 1\. példa | 2\. példa | 3\. példa |
|--|--|--|--|--|
| b | `b` | **B** ANK |  | [<sup>1</sup>](#de-c-1) **B** . PU |  |
| c | `ç` | **CH** zoli | mögli **CH** St | [<sup>2</sup>](#de-c-2)i **CH** |
| n | `d` | **d** anken | [<sup>3</sup>](#de-c-3) Hossz **d** l | [<sup>4</sup>](#de-c-4) **D** e |  |
| JH | `ʤ` | **J** EHA | gemana **g** t | [<sup>5</sup>](#de-c-5) Chan **g** e |
| f | `f` | **F** ahrtdauer | dühös **FF** slustig | **f** abbruchrei |  |
| g | `g` | **g** ut |  | [<sup>6</sup>](#de-c-6) GRE **g** |  |
| ó | `h` | **H** ausanbau |  |  |  |
| é | `j` | **J** od | Reakt | hu **i** |  |
| k | `k` | **K** OMA | Aspe **k** t | Flec |  |
| l | `l` | **l** au | ähne **l** n | zuvie **l** |  |
| m | `m` | **M** ut | Egy **m** t | Leh **m** |  |
| p | `n` | **n** un | u **n** d | Huh **n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**ng** Uyen | Schwa **nk** | R **ing** |  |
| P | `p` | **P** Artner | abru **p** t | Ti **p** |  |
| PF | `pf` | **PF**-erd | Dam **PF** t | –**PF** |  |
| r | `ʀ`, `r`, `ʁ` | **R**-eise | knu **RR** t | Haa **r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S** taccato | bi **s** t | a **Mie** |  |
| sh | `ʃ` | **SCH** ijelölt | Mi **SCH** t | Lappi **SCH** |  |
| t | `t` | **T** Raum | S **t** raße | MU **t** |  |
| TS | `ts` | **Z** UG | AR **z** t | Ész.**z** |  |
| ch | `tʃ` | **Tsch** echien | aufgepu **tsch** t | bundesdeu **tsch** |  |
| v | `v` | **w** inken | Q **u** Alle | [<sup>9</sup>](#de-c-9) Gr **OO** ve |  |
| x | [<sup>10</sup>](#de-c-10) `x` ,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Ba **CH** erach | Ma **CH** t mögli **CH** St | Schma **CH** ' i **CH** |
| z | `z` | **s** uper |  |  |  |
| zh | `ʒ` | **G** enre | B **újra** ezinski | Edvi **g** e |

<a id="de-c-1"></a>
**1** *csak idegen eredetű szavakban, például: PU **b**.*<br>
<a id="de-c-2"></a>
**2** *puha "CH" az "e" és az "i" után*<br>
<a id="de-c-3"></a>
**3** *csak idegen eredetű szavakban, például: len **d** l.*<br>
<a id="de-c-4"></a>
**4** *csak idegen eredetű szavakban, például: e.*<br>
<a id="de-c-5"></a>
**5** *csak idegen eredetű szavakban, például: Chan **g** e.*<br>
<a id="de-c-6"></a>
**6** *szó – csak idegen eredetű szavakban, például GRE **g**-ben.*<br>
<a id="de-c-7"></a>
**7** *csak idegen eredetű szavakban, például: **ng** Uyen.*<br>
<a id="de-c-8"></a>
**8** *csak idegen eredetű szavakban, például: **S** taccato.*<br>
<a id="de-c-9"></a>
**9** *csak idegen eredetű szavakban, például: gr **OO** ve.*<br>
<a id="de-c-10"></a>
**10** *az IPA `x` egy nehéz "CH" a nem elülső magánhangzók (a, AA, Oh, ow, uh, ELTE és a diphthong AW) után.*<br>
<a id="de-c-11"></a>
**11** *az IPA `ç` egy puha "CH" az első magánhangzók után (IH, iy, eh, AE, uy, UE, OE, EU is a diphthongs Ay, Oy) és a mássalhangzók* esetében<br>
<a id="de-c-12"></a>
**12** *szó – kezdetben csak idegen eredetű szavakban, például: **J** karbamidban. Szótag – kezdetben a következő szavakkal is hasonló: ba **CH** erach.*<br>

### <a name="german-oral-consonants"></a>Német szóbeli mássalhangzók

| `sapi` | `ipa` | 1\. példa                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX – ^ a 1 x t-l IH c/ |

> [!NOTE]
> Hozzá kell adni egy [GS \] -telefont két különböző magánhangzó között, a két magánhangzó kivételével a valódi diphthong. Ez a szóbeli mássalhangzó egy glottális leállása, további információért lásd: <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank"> glottális leállítása <span class="docon docon-navigate-external x-hidden-focus"> </a> </a> .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spanyol magánhangzók

| `sapi` | `ipa` | 1\. példa    | 2\. példa     | 3\. példa    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **egy** LTO     | c **ntar**    | CAS **a**     |
| i      | `i`   | **bérica**  | AV **i** Spa    | **i** . adó     |
| e      | `e`   | **e** lefante | **e** na    | Elefant **e** |
| o      | `o`   | **o** Caso    | ntrar **-** kódoló | ocasenc **o** |
| u      | `u`   | **u** STED    | p **u** NTA     | Juany **u**   |

### <a name="spanish-consonants"></a>Spanyol mássalhangzók

| `sapi` | `ipa`      | 1\. példa  | 2\. példa      | 3\. példa      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b** aobab |                | **b**        |
|        | `β`        |            | Bao **b** AB     | **b** baoba     |
| ch     | `tʃ`       | **CH** eque | Co **CH** e      | Marraque **CH** |
| n      | `d`        | **d** Edo   |                | portlan **d**   |
|        | `ð`        |            | de **d** o       | verda **d**     |
| f      | `f`        | **f** ácil  | ele-**f** ante   | Pu **f**        |
| g      | `g`        | **g** Anga  |                | dópin     |
|        | `ɣ`        |            | egy **g** ua       | tuare     |
| j      | `j`        | **i** Odo   | Cal **i** Ente   | újra **y**        |
| JJ     | `j.j` `jj` |            | VI **ll** a      |                |
| k      | `k`        | **c** oche  | **c**. a       | titáni **c**    |
| l      | `l`        | **l** ápiz  | a **l** a        | **1** . vezeték     |
| ll     | `ʎ`        | **ll** Ave  | desarro **ll** o |                |
| m      | `m`        | **m** sorrend | **m** AR       | álbu **m**      |
| p      | `n`        | **n** Ada   | CE **n** a       | **n** rató      |
| egyet     | `ɲ`        | **ρ** aña   | Ara **ρ** azo    |                |
| P      | `p`        | **p** Oca   | –**p** o       | **p**       |
| r      | `ɾ`        |            | CA **r** a       | Abri **r**      |
| RR     | `r`        | **r** Adio  | társ **RR** e      | Pu **RR**       |
| s      | `s`        | **s** ACO   | VA **s** o       | pelo **s**      |
| t      | `t`        | **t** oldo  | a **t** AR       | Disque **t**    |
| adik     | `θ`        | **z** ebra  | a **z** ul       | Lápi **z**      |
| w      | `w`        | h **u** ESO  | AG **u** a       | Gua **u**       |
| x      | `x`        | **j** OTA   | a **j** o        | relo **j**      |

> [!TIP]
> A `es-ES` beszédfelismerési szolgáltatás telefonos készlete nem támogatja a következő spanyol IPA-t,, `β` `ð` és `ɣ` . Ha szükségesek, érdemes lehet az IPA használatát közvetlenül használni.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

A beszédfelismerési szolgáltatás telefonos készlete a `zh-CN` natív telefonos <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinjin </a> -készleten alapul.

### <a name="tone"></a>Hangnem

| Pinjin hang | `sapi` | Példa karakterre |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| Mà          | ma 4  | 骂                 |
| ma          | ma 5  | 嘛                 |

#### <a name="example"></a>Példa

| Karakter | Speech szolgáltatás                |
|-----------|-------------------------------|
| 组织关系      | zu 3 – 1 – 4., 1 – XI |
| 累进        | lei 3 – Jin 4                 |
| 西宅巷       | XI 1 – Zhai 2 – Xiang 4      |

# <a name="zh-tw"></a>[zh – TW](#tab/zh-TW)

A beszédfelismerési szolgáltatáshoz beállított telefon a `zh-TW` natív telefonos <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">bopomofo </a> -készleten alapul.

### <a name="tone"></a>Hangnem

| Beszédfelismerési szolgáltatás hangja | Bopomofo hang | Példa (szó) | Beszédfelismerési szolgáltatás telefonok | Bopomofo | Pinjin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | üres         | 偵              | ㄓㄣˉ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ㄔㄚˊ                   | ㄔㄚˊ      | chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚ                   | ㄉㄚ      | dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | wàng        |
| ˙                   | ˙             | 影子             | 一ㄥ ㄗ ̇               | 一ㄥ ㄗ ̇  | yǐng zi    |

#### <a name="example"></a>Példa

| Karakter | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡ      |
| 然后        | ㄖㄢˊㄏㄡˋ   |
| 剪掉        | ㄐㄧㄢ ㄉㄧㄠ ˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

A beszédfelismerő szolgáltatás telefonos készlete `ja-JP` a natív telefonos <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana </a> készleten alapul.

### <a name="stress"></a>Stressz

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ` mainstress |
| `+`    | `ˌ` instress  |

#### <a name="example"></a>Példa

| Karakter | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | Go tɛsɨfɒn wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯ tɛsɨfɒn wɯɕja |
| 最適化       | サィテキカ +  | sajitecika ˌ |

***