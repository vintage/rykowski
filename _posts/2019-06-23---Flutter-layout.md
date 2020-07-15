---
color: rgb(69,190,247)
title: Pozycjonowanie elementów w gridzie - Row i Column
date: "2019-06-23"
layout: "post"
slug: "flutter-layout-grid"
excerpt: "Jedną z głównych zalet Fluttera jest jego niemal doskonały mechanizm do tworzenia layoutów. Dowiedz się w jaki sposób powiązać ze sobą elementy, aby wyświetlały się dokładnie tam, gdzie im każesz."
---

Ogromną rolę przy tworzeniu aplikacji mobilnych odgrywa część wizualna, czyli User Interface (**UI**). Możemy tutaj wyodrębnić kilka składowych elementów, które w połączeniu wygenerują nam to co użytkownik zobaczy na ekranie po odpaleniu naszej apki:

- wygląd komponentów (*design*)
- animacje
- rozmieszczenie komponentów (*layout*)

Wszystkie z nich są ważne i trudno sobie wyobrazić profesjonalny produkt bez chociażby jednej z nich. Dzisiaj skupię się na ostatniej, czyli w jaki sposób rozmieszczać komponenty na ekranie, aby spełniały nasze oczekiwania (lub oczekiwania *Design Team-u*). Jest to rzecz, która potrafi być frustrująca - zwłaszcza na początku przygody z technologią. O ile łatwo wyszukać w dokumentacji elementy wizualne, które umiemy sobie wyobrazić - przycisk, listę, czy belkę nawigacji - to znalezienie odpowiedzi na pytanie jak sprawić, żeby rzecz *X* była obok rzeczy *Y*, która jest nad rzeczą *Z* bywa zdecydowanie trudniejsze.

## Layout == widget

Tak jak człowiek w większości składa się z wody, tak Flutter opiera się w znacznej części na **widgetach**. Są nimi zarówno przyciski, przełączniki, ikony, a nawet wyświetlany na ekranie tekst, jak również obiekty których nie widzi nasz użytkownik, a mimo to znajdują się na ekranie i pełnią krytyczną rolę. Mowa tutaj o wierszach (`Row`) i kolumnach (`Column`), które w elegancki sposób organizują kolejność wyświetlania widgetów wizualnych.

W dostępnej skrzynce narzędziowej mamy rzecz jasna do dyspozycji więcej narzędzi niż tylko wiersz/kolumna - o tym przeczytasz w kolejnym wpisie. Nie przedłużając wstępu - przejdźmy do praktyki i realnych zastosowań gridu!

## Centralna rozgrzewka

Pamiętasz jak zaczyna się każdy kurs dowolnego języka programowania? Oczywiście że tak, trudno zapomnieć te dwa magiczne słowa: `Hello world`. Tak samo zaczniemy i my - wyświetlimy ten napis na samym środku ekranu. Jedyna różnica będzie polegała na tym, że posłużymy się polską wersją językową tego zwrotu.

```dart
import 'package:flutter/material.dart';

void main() => runApp(App());

class App extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.black,
        body: Center(
          child: Text(
            "Witaj świecie",
            style: TextStyle(
              fontSize: 32,
              color: Colors.white,
            ),
          ),
        ),
      ),
    );
  }
}
```

Na urządzeniu zobaczysz czarne tło na całej powierzchni, a na środku ekranu napis "Witaj świecie" - tak jak na załączonym poniżej zrzucie. Nie jest to co prawda przykład zbyt złożony, ale od czegoś trzeba zacząć - teraz będzie tylko ~~trudniej~~ lepiej.

![layout_center.png](/assets/img/blog/layout_center.png)

*Czy taki layout ma realne zastosowanie w produkcyjnej aplikacji?* Oczywiście! W jednej ze swoich gier wyświetlam ekran informujący gracza o poprawnej odpowiedzi. Jest to prosty ekran z tłem i zadowoloną buźką w środku. Rozwiązanie proste i dające efekt który chciałem osiągnąć:

![zgadula_correct.png](/assets/img/blog/zgadula_correct.png)

## Grid

Pierwsze rozmieszczenie za nami, jednak trudno zbudować aplikację której layout będzie opierał się wyłącznie na pojedynczym elemencie wyświetlanym na środku ekranu. W przykładzie powyżej możesz zaobserwować, że zarówno `Container` jak i `Center` przyjmują parametr `child` (z angielskiego dziecko - liczba pojedyncza), któremu można przekazać tylko jeden widget. Nie mamy możliwości przekazania listy elementów które chcemy narysować. ~~**Żegnaj przygodo.**~~

Jeśli posiadasz doświadczenie jako *Web Developer* i pracowałeś chociaż przez krótką chwilę z *HTML* to coś Ci tu nie pasuje. Przecież do `<div>`, czy dowolnego innego znacznika można przekazać tyle elementów ile sobie zażyczę! Co to za herezja, aby blokować taką możliwość? Odpowiedź jest następująca: Flutter musi wiedzieć jak poukładać listę komponentów - *pionowo? poziomo?* - i dlatego dostarcza nam dedykowane widgety, które wiedzą co i jak.

### Row

Do umieszczania elementów w poziomie (jeden obok drugiego) posługujemy się widgetem `Row`. W odróżnieniu od wielu innych wbudowanych komponentów nie przyjmuje on parametru ~~`child`~~, lecz `children` (z angielskiego dzieci - liczba mnoga) do którego jesteśmy w stanie przekazać listę obiektów do narysowania.

Napiszmy prostą aplikację, której zadaniem będzie wyświetlanie pojedynczych liter ze słowa *Witaj* w kolorowych bloczkach, które narysujemy jeden obok drugiego (od lewej do prawej).

```dart
class App extends StatelessWidget {
  Widget buildItem(String letter, Color color) {
    return Container(
        padding: EdgeInsets.all(16),
        decoration: BoxDecoration(color: Colors.blue),
        child: Text(
          letter,
          style: TextStyle(
            fontSize: 32,
            color: color,
          ),
        )
    );
  }
  
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.black,
        body: Container(
          height: 300,
          decoration: BoxDecoration(color: Colors.green),
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              buildItem("W", Colors.black),
              buildItem("I", Colors.white),
              buildItem("T", Colors.black),
              buildItem("A", Colors.white),
              buildItem("J", Colors.black),
            ],
          ),
        ),
      ),
    );
  }
}
```

Po uruchomieniu aplikacji powinieneś zobaczyć następujący zrzut:

![layout_row.png](/assets/img/blog/layout_row.png)

O to dokładnie nam chodziło! Czarne tło pochodzi z `Scaffold`, który zawsze okupuje pełny rozmiar ekranu. Na górze widzimy zieloną część o wysokości 300 jednostek z kontenera `Container`. Dlaczego ustawiliśmy sztywną wysokość? Widget ten domyślnie zajmie tyle miejsca na ekranie, ile miejsca potrzebuje jego dziecko - w tym wypadku `Row`. W celu demonstracyjnym konfiguracji tego ostatniego - narzuciliśmy mu wymiar umożliwiający swobodną zmianę dostępnych parametrów pozycjonowania.

#### mainAxisAlignment

![flutter_row_axis_main.png](/assets/img/blog/flutter_row_axis_main.png)

Główna oś na której rozmieszczone są elementy. Jeśli pamiętasz ze szkoły oś współrzędnych **X Y** to mamy tutaj naszego **X**. W przykładzie wykorzystaliśmy wartość `spaceAround`, jednak dostępne są również inne  warianty konfiguracyjne.

- **spaceAround** - układa elementy na całej dostępnej szerokości i z każdej ze stron dodaje tzw. *światło*, czyli pustą przestrzeń. Wolna przestrzeń między dziećmi jest 2x większa, niż ta oddzielająca dziecko od rodzica
- **spaceEvenly** - identyczne zachowanie jak **spaceAround**, z tą różnicą, że przestrzeń między elementami i rodzicem jest jednakowej wielkości.
- **spaceBetween** - podobnie jak **spaceAround** elementy rozłożone są na całej szerokości, jednak bez pustej przestrzeni z przodu i na końcu (przyleganie do krawędzi rodzica).
- **start** - elementy przylegają do siebie i ustawiane są na początku osi (lewa strona).
- **center** - elementy przylegają do siebie i ustawiane są na środku osi.
- **end** - analogicznie jak **start** z tym, że elementy stłoczone są na końcu osi (prawa strona).

![flutter_row_axis_main_table.png](/assets/img/blog/flutter_row_axis_main_table.png)

#### crossAxisAlignment

![flutter_row_axis_cross.png](/assets/img/blog/flutter_row_axis_cross.png)

Dodatkowa oś, która odpowiada za pionowe rozmieszczenie elementów (**Y**). Nasz przykład używa wartości `center`, jednak dostępnych wariantów jest więcej i wszystko zależy od tego jaki efekt chcesz uzyskać.

- **center** - wymusza rozkład w centralnej części wiersza. Elementy znajdują się na środku ekranu, tak jak po użyciu widgetu `Center`.
- **start** - elementy umieszczone są na samej górze kontenera.
- **end** - przeciwieństwo **start**, elementy widoczne są na dole.
- **stretch** - służy do rozciągnięcia elementów w taki sposób, aby zapełniły całą dostępną wysokość.
- **baseline** - opcja przydatna jedynie gdy elementami składowymi wiersza jest bezpośrednio tekst, który chcemy wyrównać względem siebie. Parametr do poprawnego działania wymaga ustawienia dodatkowego parametru `textBaseline` na rodzicu `Row`.

![flutter_row_axis_cross_table.png](/assets/img/blog/flutter_row_axis_cross_table.png)

### Column

Wiemy już jak rozmieszczać elementy w poziomie, czas przyjrzeć się jak zrobić to samo dla pionu (jeden pod drugim). Zadanie to powierzamy widgetowi `Column`, który działa w sposób analogiczny jak `Row`. Najważniejsze różnice to:

1. Elementy rozmieszczone są pionowo, nie poziomo.
2. `Column` okupuje maksymalną dostępną wysokość rodzica, a szerokość tylko taką jakiej potrzebuje do narysowania najszerszego ze swoich dzieci (`Row` zajmuje maksymalną szerokość, a wysokość najwyższego z dzieci).

Nanieśmy odpowiednie modyfikacje do poprzedniego kodu. Jedynymi zmianami, które wprowadzimy jest ustalanie szerokości zamiast wysokości (`height` na `width`) i podmiana widgetu `Row` na `Column`. Cała reszta pozostaje bez zmian.

```dart
class App extends StatelessWidget {
  Widget buildItem(String letter, Color color) {
    return Container(
        padding: EdgeInsets.all(16),
        decoration: BoxDecoration(color: Colors.blue),
        child: Text(
          letter,
          style: TextStyle(
            fontSize: 32,
            color: color,
          ),
        )
    );
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.black,
        body: Container(
          // height replaced with width
          width: 300,
          decoration: BoxDecoration(color: Colors.green),
          // Row changed to Column
          child: Column(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            crossAxisAlignment: CrossAxisAlignment.center,
            children: [
              buildItem("W", Colors.black),
              buildItem("I", Colors.white),
              buildItem("T", Colors.black),
              buildItem("A", Colors.white),
              buildItem("J", Colors.black),
            ],
          ),
        ),
      ),
    );
  }
}
```

Uruchomienie aplikacji spowoduje wyświetlenie poniższego ekranu:

![layout_column.png](/assets/img/blog/layout_column.png)

Ponownie uzyskujemy spodziewany wynik! Elementy narysowane są jeden pod drugim, a czarny pas po prawej stronie to nic innego jak `Scaffold` umieszczony pod spodem naszego layoutu. Na koniec przyjrzyjmy się opcjom konfiguracyjnym - jest to czysta formalność, gdyż ustawienia przekładają się 1:1 z tym co widzieliśmy w `Row`.

#### mainAxisAlignment

![flutter_column_axis_main.png](/assets/img/blog/flutter_column_axis_main.png)

Jako, że widget służy do układania komponentów w pionie to jego główną osią nie jest **X**, lecz **Y**. Trzeba o tym pamiętać - mi do tej pory zdarza się sporadycznie ustawić parametry nie tak jak powinienem. Konfiguracja jest identyczna jak w `Row`, pozwól, więc że nie będę jej tutaj ponownie wklejał. 

![flutter_column_axis_main_table.png](/assets/img/blog/flutter_column_axis_main_table.png)

#### crossAxisAlignment

![flutter_column_axis_cross.png](/assets/img/blog/flutter_column_axis_cross.png)

Druga z osi, która rozmieszcza elementy w poziomie **X**. Ponownie opcje konfiguracyjne możesz podejrzeć w sekcji w której omawialiśmy widget `Row`.

![flutter_column_axis_cross_table.png](/assets/img/blog/flutter_column_axis_cross_table.png)

## Synteza

Przeszliśmy przez pozycjonowanie poziome i pionowe - jest to mocny fundament przy projektowaniu layoutu naszej aplikacji. Możesz (a nawet powinieneś) osadzać kolumny w wierszach, a wiersze w kolumnach. W końcu `Column` i `Row` to widgety, nic więc nie stoi na przeszkodzie aby w drzewie komponentów zawierały się wzajemnie.

Na zakończenie spójrz jeszcze na bardziej złożony (ale nieskomplikowany!) ekran, który posiada wiele różnych elementów i demonstruje w jaki sposób uzyskać pożądany efekt:

![flutter_layout_complex.png](/assets/img/blog/flutter_layout_complex.png)

```dart
import 'package:flutter/material.dart';

void main() => runApp(App());

class App extends StatelessWidget {
  Widget buildInfo(Icon icon, String text) {
    return Column(
      children: [
        icon,
        Padding(
          padding: const EdgeInsets.only(top: 8.0),
          child: Text(text, style: TextStyle(color: Colors.white)),
        ),
      ],
    );
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.amber,
        body: Column(
          mainAxisAlignment: MainAxisAlignment.spaceAround,
          children: [
            Column(
              children: <Widget>[
                Text(
                  "Pikachu",
                  style: TextStyle(
                    color: Colors.black,
                    fontSize: 48,
                    fontWeight: FontWeight.bold,
                  ),
                ),
                Image.network(
                  "http://pluspng.com/img-png/pikachu-face-png-png-svg-512.png",
                  width: 160,
                  height: 160,
                ),
              ],
            ),
            Text(
              "Pikachu jest jednym z najbardziej rozpoznawalnych Pokémonów, "
              "głównie ze względu na fakt, że jest on centralną postacią w serii anime. "
              "Pikachu jest powszechnie uważany za najbardziej popularnego Pokémona.",
              textAlign: TextAlign.center,
              style: TextStyle(height: 1.4, fontSize: 16),
            ),
            Container(
              padding: EdgeInsets.all(16),
              color: Colors.black,
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                children: [
                  buildInfo(
                    Icon(Icons.flash_on, color: Colors.yellowAccent),
                    "Type",
                  ),
                  buildInfo(
                    Icon(Icons.insert_emoticon, color: Colors.greenAccent),
                    "Mood",
                  ),
                  buildInfo(
                    Icon(Icons.do_not_disturb_alt, color: Colors.redAccent),
                    "Full HP",
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```
