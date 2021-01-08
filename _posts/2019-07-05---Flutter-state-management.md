---
color: rgb(69,190,247)
title: Zarządzanie stanem aplikacji - StatefulWidget
date: "2019-07-05"
layout: "post"
slug: "flutter-state-management-stateful"
excerpt: "Interakcja użytkownika często wymaga aktualizacji wyświetlanych danych na ekranie - m.in. zmiana języka w sekcji ustawień, czy żonglowanie ulubionym elementem. Zarządzaj stanem aplikacji dzięki wbudowanemu rozwiązaniu StatefulWidget."
thumbnail: "assets/img/blog/state_stateful/thumbnail.png"
---

**Aplikacje mobilne** - a także webowe, czy desktopowe - nie opierają się wyłącznie na statycznych kontrolkach, które widzi nasz użytkownik. Aplikacja to nie tylko zbiór obrazków, przycisków i list, zawsze jest coś więcej. W przeciwnym wypadku zbudowaliśmy wydmuszkę której jedynym zadaniem jest jej podziwianie - dzieło sztuki, nieprawdaż?

Nie ma znaczenia na jaką platformę budujemy nasz program - zawsze potrzebujemy interakcji ze strony użytkownika. Skupmy się jednak na wersji mobilnej. Jakie przykładowe akcje mogą zajść w aplikacji na które musimy odpowiednio zareagować?

- oznaczenie elementu jako ulubiony
- dodanie przedmiotu do koszyka
- stuknięcie w ikonkę koszyka

Każda z tych akcji, wymaga odpowiedniej reakcji po stronie naszego kodu. Musimy przykładowo zaprezentować użytkownikowi co ma w koszyku, ale żeby tego dokonać potrzebujemy ... tak - informacji o stanie koszyka (co w nim właściwie jest). Możemy także żonglować ulubionymi produktami, aby łatwiej móc je odnaleźć w przyszłości. Wszystkie wymienione funkcjonalności łączy część wspólna - **operują na danych (stanie) aplikacji** w trakcie jej działania.

## Stan aplikacji

Stanem aplikacji nazywamy taki zestaw danych, który spełnia następujące kryteria:
- odczyt danych dostępny jest w sposób synchroniczny podczas budowania widgetu (metoda `build`).
- dane mogą (ale nie muszą) zmieniać się w trakcie trwania programu.

Jeśli masz doświadczenie z *backendem (np. API)* w aplikacjach webowych, to tam również operujesz na stanie. Nazywa się co prawda bardziej formalnie - **baza danych** - ale chodzi podobny koncept. Przechowujesz w niej dane, które od czasu do czasu się zmieniają i korzystasz z nich, aby odpowiadać aplikacji mobilnej na przesłane żądania (np. zwracając listę wszystkich zamówień użytkownika).

I tyle? To ten szumny **stan aplikacji**? W skrócie to tak, trudno się bardziej nad nim rozpisywać - formułka oddaje całość tego czym jest. Bardziej złożonym problemem jest jednak to w jaki sposób nim zarządzać, ale o tym dowiesz się już za chwilę.

## Zarządzanie stanem

Skoro stan aplikacji oznacza zbiór danych używanych podczas działania programu, to zarządzanie stanem odnosi się do manipulacji tymi danymi i reagowaniem w interfejsie na te zmiany. Przykładowo poprzez usunięcie danej pozycji z koszyka, gdy użytkownik tapnie w ikonkę ❌ obok jej nazwy. Jako że mam podłoże mocno backendowe, to często utożsamiam akcje na stanie jako typowy **CRUD** (**C**reate **R**ead **U**pdate **D**elete):

- **Odczyt** danych (wyświetlenie wszystkich pozycji w koszyku)
- **Dodawanie** nowych danych (dodanie nowego produktu do koszyka)
- **Modyfikacja** istniejących rekordów (zmiana ilości danego produktu w koszyku) 
- **Usuwanie** zbędnych wpisów (usunięcie produktu z koszyka)

Każda z tych akcji (poza odczytem) zmienia dane, które trzymamy w stanie. Pamiętaj - zmienia dane, ale nie modyfikuje **BEZPOŚREDNIO** elementów wyświetlanych na ekranie. Funkcja uruchamiana w celu usunięcia pozycji z koszyka, aktualizuje tylko stan aplikacji i nic więcej. Nie odpowiada za to, żeby listing koszyka się *magicznie* odświeżył i usunął z ekranu stary element. Programowanie wokół stanu, a nie bezpośrednio na UI nazywamy **programowaniem deklaratywnym**.

## Deklaratywny vs imperatywny

Trudne słowa, które w rzeczywistości łatwo jest zrozumieć. Osobiście sporo czasu pisałem w sposób **deklaratywny**, a jeszcze więcej w **imperatywny**, a dopiero dwa lata temu dowiedziałem się o istnieniu tych dwóch pojęć. Chodzi o sposób w jaki programujemy nasz interfejs użytkownika - o to jak wygląda początkowo (po uruchomieniu aplikacji) i jak zmienia się w trakcie działania.

### Styl imperatywny

Styl imperatywny dotyczy przede wszystkim starszych technologii - Android SDK, czy iOS UIKit. Nie mam z nimi większego komercyjnego doświadczenia, dlatego posłużę się przykładem z aplikacji webowych i technologią jQuery. Niegdyś brylująca technologia do tworzenia aplikacji, dzisiaj leciwy i schorowany dziad(ek). Nie jesteśmy tu jednak żeby wspominać historię, czy wgłębiać się w wady i zalety jQuery, a zrozumieć imperatywny UI. Spójrzmy więc na prosty przykład, który łatwo zrozumieć nawet bez znajomości tej biblioteki:

```javascript
$("input.name").on("change", function() {
  const name = $(this).val();
  $(".greeting").text("Witaj " + name);
});
```

Nasłuchujemy eventu *change* na polu tekstowym *name* i gdy on nastąpi - aktualizujemy komponent *greeting* o nową wartość tekstową np. *"Witaj Kamil"*. Rozsądne i eleganckie rozwiązanie, prawda? ~~**TAK**~~ **NIE**. O ile trudno odmówić prostoty, to mamy tutaj co najmniej dwa problemy:

1. Jeśli jakikolwiek inny komponent w aplikacji chciałby również się aktualizować po zmianie imienia to musimy rozszerzyć naszą funkcję (fuj), lub w innym miejscu w kodzie również wpiąć się w ten sam event (genialne). Co jednak zrobisz, gdy w pewnym momencie okaże się, że imię można zmieniać w inny sposób niż początkowy **input.name**? Tak - zaktualizujesz 100 miejsc w aplikacji (nikt tego nie lubi).
2. Debugowanie jest tak trudne jak granie w Diablo 2 w trybie **hardcore** (no wiesz - permanentna śmierć). Im większa aplikacja, tym więcej się dzieje i w pewnym momencie nie potrafisz odpowiedzieć na pytanie kolegi z działu QA - *"Dlaczego ten przycisk jest zablokowany?"* (swoją drogą - autentyk). Wchodzisz do kodu, odnajdujesz stosowny przycisk, ale nie ma on w swojej deklaracji nic o blokowaniu. To czy jest zablokowany, czy też nie jest wysterowane przez 50 funkcji, które żonglują jego dostępnością.

Podsumowując - styl imperatywny jest **łatwy w zrozumieniu** (*"zmieniam imię to zmienia się nagłówek"*), ale niesie ze sobą **burdel w kodzie**, szczególnie gdy aplikacja się rozrasta i dochodzą nowe funkcjonalności. W pewnym momencie utrzymanie takiego stwora staje się problematyczne i czasochłonne - acz możliwe. Na rynku mamy miliony aplikacji napisanych w taki sposób - nie twierdzę bynajmniej że to coś nierealnego.

### Styl deklaratywny

Nowy nurt w tworzeniu interfejsu użytkownika, który zakłada, że my jako programiści nie musimy przejmować się tym, aby konkretny element na ekranie zmienił swoje właściwości (kolor, tekst, cokolwiek) po zadziałaniu się akcji X. Naszym zadaniem jest natomiast stworzenie komponentów (widgetów), które w zależności od otrzymanego stanu mogą zmienić swój "wizerunek". Jest to styl wykorzystywany przez takie technologie jak Swift UI, React, a także rzecz jasna **Flutter**.

A teraz podejdźmy do tego bardziej łopatologicznie na pseudo-przykładzie (pamiętaj - to jest pseudokod, nie używaj go w domu):

```dart
RaisedButton(
  onPressed: () => { clickCount++ },
  child: Text("Kliki: ${clickCount}"),
)
// ...
Text(clickCount < 10 ? "Za klikanie jest nagroda." : "Żartowałem :)")
```

Mamy tutaj przycisk, który po tapnięciu zwiększa wartość zmiennej **clickCount**, a dodatkowo wyświetla jej aktualny stan jako tekst. Dalej w drzewie umieściliśmy dodatkowy tekst, który w zależności od wspomnianego stanu zmienia prezentowaną formę.

Jak widzisz w funkcji **onPressed** nie zmieniamy ani tekstu na przycisku, ani tego na dole drzewa. Aktualizujemy wyłącznie stan aplikacji, a Flutter sam zajmie się resztą. Automatycznie rozwiązują nam się problemy stylu imperatywnego:

1. Stan jest jedynym źródłem prawdy, które każdy komponent może odczytać w dowolnym momencie. Nie ma znaczenia kiedy i jaka akcja zmieni stan - nasz UI odświeży się samoczynnie.
2. Ponownie - tylko stan aplikacji określa obecny UI. Wystarczy znaleźć zbugowany komponent i określić jaki stan musi zajść, aby przycisk się zablokował.

W zależności od Twoich poprzednich doświadczeń - albo już to zaakceptowałeś i czujesz się w domu, albo będziesz potrzebował trochę czasu aby przestawić swój proces myślowy na nowe tory. Z własnego doświadczenia (a naprawdę długo pisałem imperatywnie) powiem, że **WARTO**.

Jeśli chciałbym w jednym zdaniu podsumować styl deklaratywny to byłaba to następująca fraza: 

> UI aplikacji budowany jest w celu zaprezentowania aktualnie posiadanego stanu.

## PokeTap

Dość teorii, daj praktykę! Najłatwiej uczyć się i walidować zdobytą wiedzę na przykładach, tak też zrobimy w tym przypadku. Nie lubię jednak demonstrować czegokolwiek na kolejnej aplikacji typu *lista TODO*. Możliwe że to niezdiagnozowane uczulenie, a może po prostu jest w sieci zbyt wiele rozwiązań tego typu i gdy widzę że będę poznawał nowe zagadnienie przez *"listę rzeczy do zrobienia"* to dopada mnie nagły atak ~~spawacza~~ mini-migreny.

Co więc proponuję? **GRĘ!** Duże (bo drukowane) słowo na mały projekt, ale stworzymy prostego clickera limitowanego czasem. Nie będzie miał zbyt wiele polotu, ale na pewno będziemy się przy nim lepiej bawili niż wiesz przy czym (⬆️), a dodatkowo opanujemy niezbędne **techniki zarządzania stanem**. Poniżej możesz zerknąć na nasz docelowy design, który będzie nam towarzyszył przez resztę wpisu.

![poketap_layout.png](/assets/img/blog/poketap_layout.png)

Gra polega na klikanie w przycisk **Trenuj** tak wiele razy jak to możliwe w ciągu limitowanego czasu 60 sekund. Każdy klik zwiększa nasz poziom oraz atrybuty ataku i obrony. Typowy **grind**, ale bez endgame. Zaczynajmy!

## Szybki prototyp (StatelessWidget)

**StatelessWidget** (z ang. widget bezstanowy) to prosty element wizualny nie posiadający z definicji stanu oraz nie potrafiący sam się przerysować, gdy użytkownik wykona odpowiednią akcję. Przykładami są wbudowane we Fluttera **Text**, **RaisedButton**, czy **Container**. Przyjmują one parametry podczas tworzenia, ale nie zmieniają swojego wyglądu same z siebie podczas działania aplikacji.

Od designu do kodu. Nie potrafimy jeszcze wykorzystać możliwości stanu, ale nic nie stoi na przeszkodzie, aby skupić się wpierw na części wizualnej. Tajemnicą poliszynela jest fakt, że tak przygotowany layout przekłada się 1:1 na wersję stanową, więc nie tracimy nawet minuty na bezsensowną implementację.

> **StatelessWidget** - używaj go zawsze, gdy nie potrzebujesz przechowywać wewnętrznego stanu, który doprowadziłby do przerysowania komponentu.

```dart
import 'package:flutter/material.dart';

void main() => runApp(App());

class App extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        backgroundColor: Colors.amber,
        body: HomeScreen(),
      ),
    );
  }
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.spaceAround,
      children: [
        Text(
          "60 sekund",
          style: TextStyle(
            color: Colors.black,
            fontSize: 32,
          ),
        ),
        Image.network(
          "http://pluspng.com/img-png/pikachu-face-png-png-svg-512.png",
          width: 160,
          height: 160,
        ),
        RaisedButton(
          onPressed: () {},
          color: Colors.black,
          child: Padding(
            padding: const EdgeInsets.all(16),
            child: Text("Trenuj", style: TextStyle(color: Colors.white)),
          ),
        ),
        Container(
          padding: EdgeInsets.all(16),
          color: Colors.black,
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: [
              StatsBox(label: "Poziom", value: 1),
              StatsBox(label: "Atak", value: 0),
              StatsBox(label: "Obrona", value: 0),
            ],
          ),
        ),
      ],
    );
  }
}

class StatsBox extends StatelessWidget {
  StatsBox({
    Key key,
    this.label,
    this.value,
  }) : super(key: key);

  final String label;
  final int value;

  Widget build(BuildContext context) {
    return Column(
      children: [
        Text(
          label,
          style: TextStyle(
            color: Colors.amber,
            fontSize: 13,
          ),
        ),
        Padding(
          padding: const EdgeInsets.only(top: 8.0),
          child: Text(
            value.toString(),
            style: TextStyle(
              color: Colors.white,
              fontSize: 24,
              fontWeight: FontWeight.bold,
            ),
          ),
        ),
      ],
    );
  }
}
```

Rozbiliśmy nasz layout na 3 widgety:

1. **App** - komponent startowy, okala resztę aplikacji pod kątem wykorzystania *Material UI*
2. **HomeScreen** - główny ekran rysujący naszą aplikację
3. **StatsBox** - pojedynczy rekord, który wyświetla jedną z dostępnych statystyk: poziom, atak, obrona.

Kod wygląda sensownie, więc budujemy naszą aplikację na emulatorze, lub urządzeniu. Po chwili mamy zbudowanego i działającego *PokeTapa*! Kliknięcie w przycisk **Trenuj** nie aktualizuje oczywiście naszych statystyk, a czasomierz na górze nie odlicza nieuniknionego. Zdawaliśmy sobie z tego sprawę (mam nadzieję) i wiemy co stanowi problem - brakuje nam **stanu aplikacji**.

## Praktyczny stan (StatefulWidget)

**StatefulWidget** (z ang. widget stanowy) to element posiadający własny stan, którego zmiana wywołuje przerysowanie elementu wizualnego na ekranie. Dobrymi przykładami są widgety używane w formularzach takie jak **TextField**, czy **Checkbox**, które "pamiętają" wprowadzone do siebie dane i każda taka zmiana (np. wpisanie litery w polu tekstowym) prowadzi do automatycznej aktualizacji kontrolki na ekranie.

W przeciwieństwie do widgetu bezstanowego, który swoje działanie opiera na pojedynczej klasie dziedziczącej po *StatelessWidget*, tutaj musimy się napracować ciut mocniej. Potrzebujemy dwóch części - **stałej** (publicznej) i **zmiennej** (prywatnej).

### Definicja publiczna (stała)

```dart
class MyWidget extends StatefulWidget {
    MyWidget({
      Key key,
      this.name,
    }): super(key: key);
    
    final string name;
    
	@override
	_MyWidgetState createState() => new _MyWidgetState();
}
```

Publiczna część komponentu, której używasz w kodzie wyżej zupełnie jak dowolny **StatelessWidget**. W momencie gdy chcesz dodać element stanowy do drzewa - tworzysz instancję klasy poprzez `new MyWidget()` i gotowe. 

Z racji tego, że **StatefulWidget** rozszerza klasę **Widget** - wymusza to na niej bycie niezmienną (**immutable**) - jest to główny powód, dla którego wymagana jest dodatkowa część (z reguły prywatna). Więcej na ten temat znajdziesz na [Stack Overflow](https://stackoverflow.com/a/50613000/1047825).

### Definicja prywatna (zmienna)

```dart
class _MyWidgetState extends State<MyWidget> {
	@override
	Widget build(BuildContext context) {
	    return Text(widget.name);
	}
}
```

Druga wymagana składowa do utworzenia widgetu stanowego. Jest to część zmienna (**mutable**), która może zmieniać swoje dane w trakcie życia - co spowoduje automatyczne przebudowanie elementu wizualnego. Zwróć uwagę na znak **\_** przed nazwą klasy - oznacza on, że klasa jest prywatna i nie można jej przykładowo zaimportować z innego pliku. Jeśli potrzebujesz do niej dostępu z zewnątrz - nie używaj prefixu **_**, nie jest on wymagany, lecz zalecany.

Dostęp do parametrów `MyWidget` z klasy `_MyWidgetState` odbywa się poprzez obiekt **widget**. W zademonstrowanym przykładie metoda build rysuje wartość z pola *widget.name* (*name* zadeklarowane jest w *MyWidget*).

Obiekt stanu przechowuje wszelkie dane, które mogą zmieniać się w trakcie działania (np. poziom naszego stworka), a także metodę *build*, która jest uruchamiana przy każdorazowej zmianie stanu przez wywołanie funkcji **setState(() {})**.

### Zmiana stanu (setState)

W celu zmiany aktualnego stanu obiektu posługujemy się funkcją **setState**, która dostępna jest na StatefulWidget. Powiadomi ona framework o tym, że stan został zmieniony, co spowoduje automatyczne przerysowanie. Mamy dwie możliwości na jego używanie - brzydką, lub oficjalną.

**Brzydka**:
```dart
void fn() {
  clickCount++;
  setState(() {});
}
```

**Oficjalna:**
```dart
void fn() {
  setState(() {
    clickCount++;
  });
}
```

Obie zadziałają identycznie, jednak ze względu na czytelność kodu mocno zalecam wariant **oficjalny**, bo:

1. Oficjalny jest zalecany przez framework
2. Przy code review od razu widać co się dzieje i mamy zgrupowane miejsce w którym dokonujemy zmiany stanu.

Korzystając z podejścia oficjalnego pamiętaj o jeszcze jednej rzeczy - wewnątrz funkcji dostarczanej do *setState* nie wykonuj ciężkich obliczeń, ani innych operacji - dokonaj w niej jedynie przypisania wartości do stanu.

**Źle:**
```dart
void fn() {
  setState(() {
    doSomeHeavyCalc();
    clickCount++;
    someMoreExtraWork();
  });
}
```

**Dobrze:**
```dart
void fn() {
  setState(() {
    clickCount++;
  });
  
  doSomeHeavyCalc();
  someMoreExtraWork();
}
```

> Używaj widgetu stanowego, gdy potrzebujesz wewnętrznego stanu, który będzie automatycznie aktualizował wizualną część kontrolki.

### Zebranie myśli

Wiemy już co należy zrobić - musimy **zamienić StatelessWidget na StatefulWidget** i stworzyć dodatkową klasę na wymagany stan. Tylko który widget ma być stanowy? Wszystkie, czy wystarczy jeden? Żadna interakcja nie zachodzi zarówno w *App* jak i w *StatsBox*, możemy je więc zostawić w spokoju. **HomeScreen** jednak definitywnie potrzebuje pomocy - ma przycisk, który zmienia stan, a dodatkowo powinien się umieć przerysować w odpowiednim momencie. Mamy kandydata do przerobienia, **sukces**!

## Działający prototyp (StatefulWidget)

Wprowadzanie niezbędnych zmian zaczniemy od przemiany `HomeScreen` na wersję stanową. Będziemy potrzebować dwóch klas, pamiętasz? 

**Przed:**
```dart
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // ...
  }
}
```

**Po:**
```dart
class HomeScreen extends StatefulWidget {
  @override
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  int secondsLeft = 60;
  int level = 1;
  int attack = 0;
  int defense = 0;
  
  @override
  Widget build(BuildContext context) {
    // ...
  }
}
```

Widget nabiera kształtu/rzeźby/masy. Sprawiliśmy, że potrafi się automatycznie przebudować poprzez posiadany stan, a dodatkowo zadeklarowaliśmy atrybuty, które przydadzą nam się już za moment do nadania trochę życia tej smutnej grze.

Mam nadzieję, że zadajesz sobie teraz pytanie *"Co dalej?"*, a nie *"Dlaczego to jeszcze nie działa?"*. Kolejną rzeczą której potrzebujemy jest wykorzystanie zadeklarowanych atrybutów w metodzie `build`, zamiast zahardkodowanych wartości. Mamy takie miejsca dwa - licznik na górze ekranu i statystyki na dole. Do pracy!

**Przed:**
```dart
Text(
  "60 sekund",
  // ...
)
// ...
children: [
  StatsBox(label: "Poziom", value: 1),
  StatsBox(label: "Atak", value: 0),
  StatsBox(label: "Obrona", value: 0),
]
```

**Po:**
```dart
Text(
  "$secondsLeft sekund",
  // ...
),
// ...
children: [
  StatsBox(label: "Poziom", value: level),
  StatsBox(label: "Atak", value: attack),
  StatsBox(label: "Obrona", value: defense),
]
```

> Jeśli nie spotkałeś się wcześniej z zapisem w formacie **"$secondsLeft sekund"** to jest to tzw. string interpolation. Służy on do wyświetlania ciągu znaków z dynamicznymi wartościami w środku.

Jesteśmy prawie u celu ukończenia naszej gry. Działa co prawda zupełnie jak wersja bezstanowa (wydmuszka), ale stan jest i czeka tylko aż go uaktywnimy. A kiedy chcemy to zrobić? Co w naszej aplikacji wymusza odświeżenie ekranu jak nie kliknięcie przycisku *Trenuj*! **Eureka** - brakuje obsługi kliknięcia w przycisk! 

**Przed:**
```dart
RaisedButton(
  onPressed: () {},
  // ...
)
```

**Po:**
```dart
RaisedButton(
  onPressed: train,
  // ...
)
```

Dodatkowo w obrębie klasy trzymającej stan zaimplementuj metodę **train**, która przeprowadzi jednorazowy trening stworka. Co powinna zrobić? Przede wszystkim zwiększyć poziom o jedno oczko (szybki grind) i podbić statystyki zgodnie ze wzorem podbijania statystyk:

```dart
void train() {
  setState(() {
    var random = Random();

    level += 1;
    attack += random.nextInt(4) + 1;
    defense += random.nextInt(3) + 1;
  });
}
```

Mój wzór opiera się na wartościach losowych, gdzie atak zwiększam między 1-4, a obronę o 1-3 punkty. Jeśli podoba Ci się koncept losowości (każda rozgrywka będzie inna!) to pamiętaj o zaimportowaniu na górze pliku **modułu obsługującego liczby pseudolosowe**.

```dart
import 'dart:math';
```

Jeśli masz inny pomysł, np. zależny od tego jaki poziom już posiadamy, lub ile czasu pozostało w grze to śmiało zrób to po swojemu - jesteś od teraz Indie Game Developerem.

Gra gotowa - można się zagrywać ~~godzinami~~ sekundami. A jeśli już przy sekundach jesteśmy to co z licznikiem czasu? Dlaczego nie odlicza nieuniknionego i pozwala expić w nieskończoność? Cóż - **secondsLeft** otrzymuje na starcie wartość *60* i nigdy jej nie zmienia, bo i kiedy? Jaką akcję ma wykonać użytkownik, żebyśmy zareagowali odjęciem sekundy z zegarka?

> ...

To było ciut podchwytliwe, bo przecież użytkownik nie musi podejmować żadnej akcji żeby czas upływał (zupełnie jak w realnym życiu). Ot - czas płynie nieubłaganie nawet jeśli nasz gracz patrzy tylko w ekran, nie dotykając i nie stukając w żadne miejsce na ekranie. Czy to oznacza, że **stan można zaktualizować bez zewnętrznej ingerencji**? Nie inaczej. To, że w większości przypadków aktualizujemy go po stosownej akcji, nie oznacza że nie możemy robić tego w dowolnym momencie. No to wio!

## Oszlifowany diament (lifecycle events)

Na zakończenie potrzebujemy aktualizować zmienną **secondsLeft**, co sekundę odejmując *1* od jej aktualnej wartości, aż do momentu gdy osiągniemy zero. Zero arbitralnie - po prostu nie chciałbym pokazywać użytkownikowi ujemnego czasu. Na nasze szczęście Dart dostarcza nam gotowe rozwiązanie w postaci **Timer.periodic**, którego zadanie polega na ciągłym odliczaniu określonego czasu (np. 1 sekundy) i uruchamianiu określonego kodu (np. setState). Brzmi legitnie. Zdefiniujmy funkcję aktualizującą stan zegara (na tym samym poziomie co funkcja **train**):

```dart
void updateClock(Timer timer) {
  if (secondsLeft == 0) {
    // Do nothing
    return;
  }
    
  setState(() {    
    secondsLeft -= 1;
  });
}
```

Funkcja **updateClock** przyjmuje obiekt typu **Timer** - jest to wymaganie pochodzące z faktu, iż **Timer.periodic** jako parametru wymaga właśnie funkcji o takiej sygnaturze. Dodajmy brakujący import na górze pliku:

```dart
import 'dart:async';
```

Ostatni krok stanowi uruchamianie funkcji **updateClock** co sekundę, do czego przyda nam się poniższa wstawka:

```dart
Timer.periodic(Duration(seconds: 1), updateClock);
```

Umieść ją **na samym początku metody build** - chcemy aby licznik zaczął się odświeżać niezwłocznie przed pierwszym narysowaniem. Uruchamiamy aplikację i voila - dostępny czas ucieka jak przez palce. Zaraz, zaraz. Chyba jednak trochę za szybko!

![poketap_broken_counter.gif](/assets/img/blog/poketap_broken_counter.gif)

Uruchamiamy nowy timer za każdym przebudowaniem widgetu, które następuje po każdorazowej aktualizacji stanu, które z kolei ... dzieje się raz na sekundę za sprawą licznika. Zobrazujmy sobie początek tego szaleństwa:

1. Pierwsze narysowanie widgetu tworzy timer, który co sekundę zmienia pozostały czas (stan)
2. Po sekundzie timer zmienia *secondsLeft* z **60 na 59** poprzez wywołanie **setState**
3. Następuje przerysowanie komponentu (uruchamiana jest metoda *build*) i wystartowanie dodatkowego timera (taki sam jak w punkcie 1)
4. Po kolejnej sekundzie timer1 aktualizuje stan z **59 na 58**
5. Mikrosekundę po nim timer2 aktualizuje stan z **58 na 57**
6. Każda aktualizacja stanu rozpędza zegar coraz mocniej
7. No profit

Idealnym rozwiązaniem problemu wydaje się uruchomienie tylko jednego timera i to w momencie pierwszego rysowania. Można by dodać flagę typu **isFirstDraw**, ale z pomocą przychodzą nam specjalne metody wbudowane, które automatycznie wołane są przez Fluttera w odpowiednim czasie życia widgetu tzw. **lifecycle**.

### Dodanie do drzewa (initState)

Funkcja **initState** uruchamiana jest jednorazowo na czas życia widgetu. Nazwa sugeruje, że możemy tutaj zainicjalizować wstępny stan, ale możemy również bez obaw wystartować nasz unikalny timer i mieć pewność, że **initState** nie zostanie zawołany więcej niż raz. Wyrzuć timer z builda i dodaj następującą funkcję:

```dart
Timer timer;

@override
initState() {
  super.initState();
  timer = Timer.periodic(Duration(seconds: 1), updateClock);
}
```

Od razu lepiej - licznik nie gna na złamanie karku i odlicza sekunda-po-sekundzie. Właśnie tego oczekiwaliśmy po naszej spokojnej grze.

![poketap_fixed_counter.gif](/assets/img/blog/poketap_fixed_counter.gif)

### Zdjęcie z drzewa (dispose)

*PokeTap* jest grą bardzo prostą, która posiada wyłącznie jeden ekran. Wyobraź sobie jednak sytuację w której się rozrasta i możliwa jest nawigacja między różnymi ekranami (np. listing stworków). W takim przypadku warto rozważyć czy kod, który uruchamiamy w **initState** wymaga "posprzątania" przed opuszczeniem ekranu. 

**initState** startuje nowy timer, który co sekundę wywoła funkcję *uploadClock*. Jeśli wyjdziemy z tego ekranu (w przyszłości, gdy będzie to możliwe) to timer dalej będzie robił swoje. Flutter nie posprząta za nas wszystkiego - wyczyści widget oraz jego stan, ale nie zdaje sobie sprawy z naszych programistycznych poczynań. Musimy znaleźć więc sposób aby wykonać dodatkowy kod, który zatrzyma timer w momencie usuwania widgetu z ekranu. **Dispose** *to the rescue!*

```dart
@override
void dispose() {
  timer.cancel();
  super.dispose();
}
```

Pamiętaj - kod nie ma żadnego praktycznego zastosowania w aktualnej wersji aplikacji, nie musisz go więc nanosić na swój projekt. Wspominam o metodzie **dispose** wyłącznie dla dopełnienia przeciwieństwa **initState**. Sam temat metod **lifecycle** jest na tyle ważny i warty poznania, że na pewno pojawi się krótki wpis na blogu dedykowany właśnie nim.

## Dalsze plany

Mamy to - **w pełni działająca gra** typu clicker, bez żadnych udziwnień. Możesz je rzecz jasna dodać i rozszerzyć koncept, a ostatecznie nawet wydać na Android i iOSa. Wierzę, że wszystko okazało się jasne i potrafisz zarządzać stanem swojej Flutterowej aplikacji. Skomentuj wpis jeśli chciałbyś się czymś podzielić, lub masz dowolne pytanie. Wyszeruj dalej, aby więcej Flutter devów dowiedziało się co i jak. Do następnego wpisu!

Pełny kod aplikacji PokeTap znajdziesz na [GitHubie](https://github.com/vintage/rykowski.dev/blob/master/samples/poketap.dart).
