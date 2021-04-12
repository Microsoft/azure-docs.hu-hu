---
title: A szögletes alkalmazás felhasználói felületének létrehozása Azure Cosmos DB API-MongoDB (part3)
description: A MongoDB-alkalmazások Azure Cosmos DB-n Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 3. része.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097821"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Szögletes alkalmazás létrehozása Azure Cosmos DB API-MongoDB – a felhasználói felület felépítése szögletes
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy olyan új alkalmazást, amelyet expressz és szögletes Node.js írt be, majd a [Cosmos db API-MongoDB konfigurált Cosmos-fiókhoz](mongodb-introduction.md)csatlakozik.

Az oktatóanyag 3. része a [2. részre](tutorial-develop-mongodb-nodejs-part2.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Az Angular felhasználói felület létrehozása
> * A megjelenés beállítása a CSS segítségével
> * Az alkalmazás helyi tesztelése

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [2. részében](tutorial-develop-mongodb-nodejs-part2.md) ismertetett feladatokat.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="build-the-ui"></a>A felhasználói felület létrehozása

1. A Visual Studio Code-ban kattintson a Leállítás gombra a :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: Node alkalmazás leállításához. 

2. Írja be a következő parancsot a Windows-parancssorba vagy a Mac-terminálablakba egy főképösszetevő létrehozásához. A kódban a g a létrehozás, a c az összetevő, a heroes az összetevő neve, és egyszintű fájlstruktúrát (--flat) használ, azaz a rendszer nem hoz hozzá létre almappát.

    ```
    ng g c heroes --flat 
    ```

    Az új összetevők létrehozásának megerősítése a terminálablakban látható.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="A hero összetevő telepítése":::

    Vessünk egy pillantást a létrehozott és frissített fájlokra. 

3. A Visual Studio Code-ban az **Explorer** panelen lépjen az új **src\app** mappába, és nyissa meg az abban létrejött **heroes.component.ts** fájlt. Ezt a TypeScript-összetevőfájlt az előző paranccsal hoztuk létre.

    > [!TIP]
    > Ha az alkalmazás mappája nem jelenik meg a Visual Studio Code-ban, nyomja le Mac gépen a CMD + SHIFT + P vagy Windows gépen a Ctrl + Shift + P billentyűkombinációt a parancskatalógus megnyitásához, majd írja be a *Reload Window* parancsot a rendszer módosításának életbe léptetéséhez.

4. Ugyanebben a mappában nyissa meg az **app.module.ts** fájlt, és láthatja, hogy a rendszer felvette a `HeroesComponent` összetevőt a deklarációk közé az 5. sorban, valamint a 10. sorba is importálta.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Az app-module.ts fájl megnyitása":::

5. Lépjen vissza a **heroes.component.html** fájlra, és másolja be ezt a kódot. A `<div>` tároló tárolja a teljes oldalt. A tárolóban a fő képek egy listája található, amelyeket létre kell hoznia, hogy amikor majd azokra kattint, akkor a felhasználói felületen kijelölhesse és szerkeszthesse, illetve törölhesse azokat. Ezután a HTML-ben némi stíluskezelést hajtunk végre, amely azt mutatja majd, hogy melyik elem lett kijelölve. Emellett van egy szerkesztési terület, ahol felvehetők az új fő képek vagy szerkeszthetők a meglévők. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Most, hogy elkészültünk a HTML-lel, hozzá kell adnunk a **heroes.component.ts** fájlhoz, hogy használhassuk a sablont. Az alábbi kód adja hozzá a sablont az összetevőfájlhoz. Ez hozzáad egy konstruktort, amely lekér néhány fő képet, majd inicializálja a főszolgáltatás-összetevőt az összes adat lekéréséhez. Ez a kódrészlet emellett hozzáadja az események kezeléséhez szükséges összes metódust a felhasználói felületen. Ön pedig bemásolhatja az alábbi kódot a meglévő kód helyére a **heroes.component.ts** fájlban. A kiemelt Hero és HeroService területeken várhatóan hibaüzenetek jelennek meg, mivel a vonatkozó összetevők még nincsenek importálva. Ezeket a hibákat a következő szakaszban orvosoljuk. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. Az **Explorerben** nyissa meg az **app/app.module.ts** fájlt, és frissítse az importálási szakaszt egy `FormsModule` importálásának a hozzáadásával. Az importálási szakasznak most így kell kinéznie:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. Az **app/app.module.ts** fájlban adja hozzá az új FormsModule modul importálását a 3. sorban. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>A megjelenés beállítása a CSS segítségével

1. Az Explorer panelen nyissa meg az **src/styles.scss** fájlt.

2. Másolja be az alábbi kódot a **styles.scss** fájlba a fájl meglévő tartalmának a helyére.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Mentse a fájlt. 

## <a name="display-the-component"></a>Az összetevő megjelenítése

Most, hogy megvagyunk az összetevővel, hogy jelenítjük meg azt a képernyőn? Módosítsuk az alapértelmezett összetevőket az **app.component.ts** fájlban.

1. Az Explorer panelen nyissa meg az **/app/app.component.ts** fájlt, módosítsa a címet a Heroes címre, majd illessze be a **heroes.components.ts** fájlban létrehozott összetevő nevét (app-heroes), hogy az az új összetevőre hivatkozzon. A fájl tartalmának most így kell kinéznie: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Vannak további összetevők is a **heroes.components.ts** fájlban, amelyekre hivatkozunk, például a Hero összetevő, így azt is létre kell hoznunk. Az Angular CLI parancssorában az alábbi paranccsal létrehozunk egy főképmodellt és egy **hero.ts** nevű fájlt. A g a létrehozás, a cl az osztály és a hero az osztály neve.

    ```bash
    ng g cl hero
    ```

3. Az Explorer panelen nyissa meg az **src\app\hero.ts** fájlt. A **hero.ts** fájlban cserélje le a fájl tartalmát az alábbi kódra, amely egy Hero osztályt ad hozzá egy azonosítóval, egy névvel és egy üzenettel.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Lépjen vissza a **heroes.components.ts** fájlra, és láthatja, hogy a `selectedHero: Hero;` sorban (10. sor), a `Hero` egy piros vonallal alá van húzva. 

5. Kattintson a `Hero` kifejezésre, és a Visual Studio egy villanykörte ikont jelenít meg a kódblokk bal oldalán. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Villanykörte a Visual Studio Code-ban":::

6. Kattintson a villanykörtére, majd a **Fő kép importálása az „/app/hero” fájlból** vagy a **Fő kép importálása a „./hero” fájlból** lehetőségre. (Az üzenet a konfigurációtól függően változik.)

    Egy új kódsor jelenik meg a 2. sorban. Ha a 2. sor az /app/hero mappára hivatkozik, módosítsa, hogy a helyi mappában lévő főképfájlra hivatkozzon (./hero). A 2. sornak így kell kinéznie:

   ```
   import { Hero } from "./hero";
   ``` 

    Ezzel megvagyunk a modellel, a szolgáltatást azonban még létre kell hoznunk.

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Az Angular CLI parancssorában az alábbi paranccsal létrehozunk egy főképszolgáltatást az **app.module.ts** fájlban. A g a létrehozás, az s a szolgáltatás, a hero a szolgáltatás neve és a -m az app.module-ba való helyezés.

    ```bash
    ng g s hero -m app.module
    ```

2. A Visual Studio Code-ban váltson vissza a **heroes.components.ts** fájlra. Láthatja, hogy a `constructor(private heroService: HeroService) {}` sorban (13. sor), a `HeroService` egy piros vonallal alá van húzva. Kattintson a `HeroService` elemre, és a kódblokk bal oldalán megjelenik a villanykörte ikon. Kattintson a villanykörtére, majd a **HeroService importálása a „./hero.service” fájlból** vagy a **HeroService importálása az „app/hero.service” fájlból** lehetőségre.

    A villanykörtére kattintva a rendszer beilleszt egy új kódsort a 2. sorba. Ha a 2. sor a/app/Hero.Service mappára hivatkozik, módosítsa úgy, hogy az a helyi mappából (./Hero.Service) hivatkozik a Hero-fájlra. A 2. sornak így kell kinéznie:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. A Visual Studio Code-ban nyissa meg a **hero.service.ts** fájlt, és másolja be a következő kódot a fájl tartalma helyére.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    A kód a HttpClient az Angular által kínált legújabb verzióját használja, amely egy olyan modul, amelyet meg kell adni, ezért most ezt is fogjuk tenni.

4. A Visual Studio Code-ban nyissa meg az **app.module.ts** fájlt, és az importálás szakaszba vegye fel a HttpClientModule modult annak importálásához.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. Az **app.module.ts** fájlban vegye fel a HttpClientModule importálási utasítást az importálandó elemek listájára.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Mentse az összes módosítást a Visual Studio Code-ban.

## <a name="build-the-app"></a>Az alkalmazás létrehozása

1. A parancssorban adja meg a következő parancsot az Angular-alkalmazás létrehozásához. 

    ```bash
    ng b
    ``` 

    Ha problémák merülnének fel, a terminálablakban megjelennek a javítandó fájlokkal kapcsolatos információk. Amikor az alkalmazás létrehozása befejeződik, az új fájlok a **dist** mappába kerülnek. Az új fájlokat a **dist** mappában tekintheti meg, amennyiben szeretné.

    Most pedig futtassuk az alkalmazást.

2. A Visual Studio Code-ban kattintson a **hibakeresés** gombra a :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: bal oldalon, majd kattintson a **hibakeresés indítása** gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png"::: .

3. Nyisson meg egy webböngészőt, és lépjen a **localhost:3000** helyre, ahol megtekintheti a helyileg futó alkalmazást.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Helyileg futó főképalkalmazás":::

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozta az Angular felhasználói felületet
> * Helyileg tesztelte az alkalmazást

Továbbléphet az oktatóanyag következő részére, amelyben egy Azure Cosmos DB-fiókot hoz létre.

> [!div class="nextstepaction"]
> [Azure Cosmos DB-fiók létrehozása az Azure CLI használatával](tutorial-develop-mongodb-nodejs-part4.md)
