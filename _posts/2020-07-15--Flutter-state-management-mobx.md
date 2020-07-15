---
color: rgb(69,190,247)
title: MobX i Flutter - automatyczny stan
date: "2020-07-15"
layout: "post"
slug: "flutter-mobx"
excerpt: "MobX jest świetnym i sprawdzonym w boju rozwiązaniem do zarządzania stanem aplikacji. Nie wymaga dużej ilości kodu, jest wydajny i sporo rzeczy dzieje się bez udziału programisty."
thumbnail: "assets/img/blog/mobx/thumbnail.png"
---

Flutter posiada dziesiątki rozwiązań przeznaczonych do zarządzania stanem aplikacji. Bardziej, lub mniej popularne, przekombinowane, proste w użyciu. Generalnie jest pełen wachlarz możliwości i każdy bez problemu znajdzie coś dla siebie i swojego projektu.

Co jednak zrobić, gdy nie masz czasu, ani chęci w testowaniu i porównywaniu mnogości opcji? Postaw na sprawdzony wariant w postaci **MobX**, który w ekosystemie JavaScript już dawno udowodnił, że ma solidne fundamenty, a praca z nim to czysta przyjemność. Warto na niego postawić również w przypadku aplikacji Flutterowych!

## MobX

**MobX** to biblioteka do zarządzania stanem, w której wiele rzeczy dzieje się automatycznie. Sprawia to, że jako programista nie musisz się nimi przejmować. Wszystko za sprawą *TFRP* (*Transparent Functional Reactive Programming*) wokół którego wszystko zostało zaprojektowane i zbudowane. Z jednej strony magiczne rozwiązania to zło, bo przecież musisz wiedzieć jak wszystko dokładnie działa, a z drugiej ... Czy nie chcesz przestać się martwić o każdy jeden aspekt oprogramowania? W Darcie nie zajmujesz się alokacją pamięci, ani też jej uwalnianiem, i zakładam, że nie chciałbyś robić tego ręcznie. *Garbage Collector* sam się wszystkim zajmuje. Czasem lepiej zdać się na gotowe i sprawdzone rozwiązanie, a samemu skupić się na rzeczach, które trudniej zautomatyzować narzędziami. Jak chociażby implementacja logiki biznesowej, czy zapierający dech w piersiach UI.

To co finalnie otrzymujesz korzystając z *MobX* to posiadanie zawsze aktualnego stanu, który wyświetlany jest użytkownikowi. A wszystko to przy niskim nakładzie pracy i prostocie kodu, którą potrafiłbym wytłumaczyć nawet mojej mamie. Oczywiście pod warunkiem że by chciała ... a w to szczerze wątpie.

### Fundamenty koncepcyjne

Fundamenty *MobX* składają się z zaledwie trzech podstawowych konceptów. Są to odpowiednio:

- **Observable**, przechowujący stan aplikacji w formie obserwowalnej
- Akcja (**Action**) do manipulowania (zmieniania) stanu
- Reakcja (**Reaction**), jako obserwator stanu uruchamiany po jego każdorazowej zmianie

Przyjrzymy się każdej ze składowych z bliska już za moment, podczas budowania prostej aplikacji, ale poniższy diagram w prosty sposób prezentuje jakie są zależności między tymi elementami. Uruchomienie akcji (*Actions*) prowadzi do zmiany stanu (*Observables*), który powiadamia o tym fakcie powiązane reakcje (*Reactions*).

![MobX triad](/assets/img/blog/mobx/mobx_triad.png)

### Przygotowanie projektu

W celu wykorzystania pełnego potencjału drzemiącego w *MobX* będziemy potrzebowali kilku zależności. Nie jest to co prawda wymóg, ale im większy projekt budujesz tym bardziej docenisz dostarczoną automatyzację.

Podstawowa paczka to **mobx**. Dostarcza pełne wsparcie biblioteczne do zarządzania stanem i nie jest ograniczona do działania wyłącznie we Flutterze. Możesz jej równie dobrze używać w aplikacjach konsolowych pisanych w czystym Darcie. Jej uzupełnieniem jest **flutter_mobx**, dzięki któremu dostajemy wymieniony wyżej element reakcji w postaci widgeta *Observer*.

Dwie kolejne biblioteki są umieszczone w sekcji *dev_dependencies*, bo używamy ich tylko w fazie developmentu. Nie są one częścią zbudowanej już aplikacji, a stanowią "jedynie" wsparcie programisty w postaci narzędzia. W projekcie wykorzystamy je do automatycznego generowania klas, aby zademonstrować maksymalne ograniczenie powtarzalnego kodu (*boilerplate code*).

```yaml
dependencies:
  # ...
  mobx:
  flutter_mobx:

dev_dependencies:
  # ...
  build_runner:
  mobx_codegen:
```

> Pamiętaj o zainstalowaniu nowo dodanych zależności przez `flutter pub get`.

#### UI aplikacji

Aplikacja pokazowa ma dwa zasadnicze ekrany i jej celem jest jedynie pokazanie jak wspołgrać z *MobXem*. Nie doszukuj się w niej przełomowych funkcjonalności, a tym bardziej ładnego UI - to dobry motyw na całkowicie osobny wpis.

1. Pierwszy ekran (startowy) informuje użytkownika że musi się zalogować poprzez stuknięcie w przycisk.
2. Drugi ekran wyświetla aktualnie zalogowanego użytkownika. Nazwa, data urodzenia i lista umiejętności, którą można rozszerzać.

Dodatkowo w górnym pasku zawsze prezentowana jest ilość użytkowników, którzy wylogowali się podczas trwania sesji użytkowej.

{% include aligner.html images="blog/mobx/ui_unauthenticated.png,blog/mobx/ui_authenticated.png" %}

Kod statycznej aplikacji, czysty UI:

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final isAuthenticated = false;

    return MaterialApp(
      theme: ThemeData(
        textTheme: TextTheme(
          bodyText2: TextStyle(
            fontSize: 20,
          ),
        ),
      ),
      home: Scaffold(
        appBar: AppBar(
          title: Text("Previous users: 0"),
        ),
        body: Center(
          child: isAuthenticated ? Authenticated() : Unauthenticated(),
        ),
      ),
    );
  }
}

class Unauthenticated extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text("Detected unauthenticated user!"),
        RaisedButton(
          onPressed: login,
          child: Text("Log in"),
        ),
      ],
    );
  }

  void login() {}
}

class Authenticated extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final name = "Kamil";
    final birthDate = DateTime(1988, 6, 1);
    final skills = ["Flutter", "Dart"];

    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text("Name: $name"),
        Text("Birth date: $birthDate"),
        Text("Skills: $skills"),
        Row(
          mainAxisAlignment: MainAxisAlignment.spaceEvenly,
          children: [
            RaisedButton(
              onPressed: addSkill,
              child: Text("New skill"),
            ),
            RaisedButton(
              onPressed: logout,
              child: Text("Log out"),
            ),
          ],
        )
      ],
    );
  }

  void addSkill() {}

  void logout() {}
}
```

Aplikacja się kompiluje i po uruchomieniu prezentowany jest ekran startowy. Przycisk logowania jest na chwilę obecną wydmuszką, więc jedyną możliwością wyświetlenia ekranu zalogowanego użytkownika jest ręczna zmiana flagi w kodzie:

```dart
final isAuthenticated = true;
```

Przechodzimy zatem do najważniejszej kwestii, czyli do wprowadzenia stanu aplikacji. Umożliwienie logowania, wylogowania, oddelegowania zarządzania danymi poza drzewo widgetów.

#### Model danych

Niezależnie od tego z jaką technologią zarządzania stanem pracujesz, zawsze modeluj swoje dane. **Zawsze**. Nie trzymaj luźno lewitujących zmiennych, które jasno opisują pewien byt w systemie. W naszym przypadku mamy koncept użytkownika, którego opisują takie cechy jak jego nazwa, data urodzenia, czy lista posiadanych umiejętności. Zaprojektujmy więc dla niego czysty model, inaczej mówiąc **worek na dane**.

Tuż obok *main.dart* dodamy nowy plik *data.dart*, którym zdefiniujemy czym jest użytkownik naszego systemu. Klasa ta może być później śmiało wykorzystywana w innym dowolnym miejscu aplikacji. A najpewniej przeżyje nawet takie turbulencje, jak konieczność całkowitego wymienienia innej warstwy aplikacji.

```dart
class User {
  final String name;
  final DateTime birthDate;
  final List<String> skills;

  User(this.name, this.birthDate, this.skills);

  User copyWith({List<String> skills}) {
    return User(
      name,
      birthDate,
      skills,
    );
  }
}
```

#### Observables

*Observable* stanowi reaktywny stan aplikacji. Przez reaktywny mam na myśli taki, który umożliwia reagowanie na to co się z nim dzieje. W szczególności na zachodzące wewnątrz zmiany. Każdy dowolny obiekt możliwy do zdefiniowania w samym Darcie może stać się w łatwy sposób obserwowalny. Nie ważne, czy mamy do czynienia z wartościami prymitywnymi jak *String*, czy *bool*, czy złożonymi obiektami własnych klas. Wszystko w *MobX* może stać się obserwowalne.

Dodajmy plik *store.dart* w którym będziemy przechowywać zarówno stan w postaci *Observable*, jak również akcje do manipulowania danymi. Omówienie kodu tuż poniżej.

```dart
import 'package:flutter_demo_mobx/data.dart';
import 'package:mobx/mobx.dart';

part 'store.g.dart';

class UserStore = UserStoreBase with _$UserStore;

abstract class UserStoreBase with Store {
  @observable
  User me;

  @observable
  ObservableList<User> previousUsers = ObservableList();

  @computed
  bool get isAuthenticated => me != null;
}
```

Trochę się tu dzieje, prawda? Na szczególną uwagę zasługuje część kodu wymagana przez **mobx_codegen** do wygenerowania za nas całego *boilerplate*. Nie jest to coś czym zaprzątasz sobie głowę w codziennej pracy, ale warto wiedzieć jak to ugryźć.

```dart
part 'store.g.dart';

class UserStore = UserStoreBase with _$UserStore;
```

Te dwie linie to jedyne powtarzalne kawałki kodu, które przewijają się gdy definiujemy nową domenę stanu. Nie jest to specjalnie wygórowana cena za późniejszy minimalizm, zwłaszcza, że im bardziej złożona aplikacja tym większe czerpiemy benefity z samego *MobX*. Nazwa *store.g.dart* to nic innego jak nazwa pliku głównego z zamienionym rozszerzeniem z *.dart* na *.g.dart*. Gdy przejdziemy do generowania kodu będziesz mógł do niego nawet zajrzeć.

Dalej mamy zdefiniowane dwie składowe oznaczone adnotacją (**annotation**) `@observable`. W obiekcie `me` chcemy przechowywać aktualnie zalogowanego użytkownika, a w `previousUsers` listę wszystkich tych, którzy się wylogowali. Warto zwrócić uwagę na wykorzystanie **ObservableList** zamiast wbudowanego w Darta *List*. Oba udostępniają taki sam interfejs, a skorzystanie z *ObservableList* zagwarantuje nam to, że jeśli zmienią się elementy wewnątrz listy (np. zostanie dodany nowy wpis) to zostaniemy o tym powiadomieni jak przy każdej innej zmianie.

Ostatni użyty element stanowi `isAuthenticated`. Jest to również *Observable*, ale w postaci wnioskowej na podstawie innych zdefiniowanych obiektów *Observable*. Oznacza to, że nie trzymamy tam zupełnie nowych danych, a jedynie wartość która jest wyliczana na podstawie innych danych w aplikacji. To co daje nam `@computed` to pełna gwarancja tego, że zawsze będzie posiadał aktualną wartość, która zmieni się natychmiast gdy jakikolwiek nasłuchiwany obiekt zostanie zmieniony. *MobX* sam zajmie się ustaleniem jakie *Observable* zostały użyte do wyliczeń i będzie pilnował spójności danych w najwydajniejszy możliwy sposób.

To tyle jeśli chodzi o zdefiniowanie stanu. Zawsze gdy chcesz go rozbudować wystarczy dodać nowy obiekt udekorowany jedną z dostępnych adnotacji: *@observable*, lub *@computed*.

#### Actions

Do zarządzania danymi przechowywanymi w *Observable* wykorzystywane są akcje (**actions**). Jeśli chcesz dokonać jakiejkolwiek zmiany w stanie aplikacji, definiujesz po prostu funkcję, która zrobi to co powinna. Bez zbędnych komplikacji w postaci obsługi i mapowania zdarzeń, czy ręcznego powiadamiania reszty aplikacji, że coś się właśnie wydarzało.

Jak już wspomniałem akcja to najzwyklejsza funkcja i jedyne co ją wyróżnia w kodzie to adnotacja `@action`. Pełni ona dwie role. Pierwsza czysto semantyczna, wskazuje które funkcje manipulują stanem. Druga, czysto techniczna, gwarantuje transakcyjność. Dzięki niej, wszystkie *Observable* zmieniające swoje wartości w trakcie trwania akcji, powiadomią o tym fakcie dopiero po zakończeniu wykonania funkcji. Jeśli zmieniasz w ramach pojedynczej akcji kilka obiektów to reakcja po stronie aplikacji będzie tylko na samym końcu. Dla nas oznacza to w ostatecznym rozrachunku mniejszą ilość rebuildów UI.

```dart
abstract class UserStoreBase with Store {
  // ...

  @action
  void login() {
    me = User(
      "Kamil #${previousUsers.length}",
      DateTime(1988, 6, 1),
      ["Dart", "Flutter"],
    );
  }

  @action
  void logout() {
    previousUsers.add(me);
    me = null;
  }

  @action
  void addSkill() {
    me = me.copyWith(skills: [...me.skills, "new skill"]);
  }
}
```

Dodaliśmy trzy akcje: *login*, *logout* i *addSkill*. Każda dokonuje pewnych zmian na *Observable*, a najciekawsza z nich jest ta ostatnia. Chcąc dodać nową umiejętność dla obecnie zalogowanego użytkownika, nie dodajemy jej bezpośrednio do listy posiadanych już umiejętności. Taki zapis nie zadziała.

```dart
@action
void addSkill() {
  me.skills.add("new skill");
}
```

 Lista *skills* w klasie *User* nie jest *Observable*, co skutkuje tym, że *MobX* nie dowie się o tej zmianie i nie wyemituje sygnału do zareagowania na nią. Można ten problem rozwiązać na dwa sposoby:

1. Zaprojektowanie klasy *User* w taki sposób, żeby jej część była obserwowalna. Kłóci się to jednak z koncepcją worka danych, którego jedynym zadaniem jest opisanie użytkownika i wystawienie czystego interfejsu. Możesz się z tym kłócić - śmiało. Architektura aplikacji to kwestia elastyczna, a to tylko mój punkt widzenia.
2. Korzystanie z *Immutable objects*, czyli obiektów których stan nie może zostać zmieniony. Jedyną możliwością na dokonanie zmiany jest utworzenie bliźniaczego obiektu i przypisanie jego instancji w miejsce starej referencji. We Flutterze konwencją jest, że obiekty takie implementują funkcję **copyWith** do której podajemy opcjonalne pola do aktualizacji, a reszta zostanie skopiowana z oryginalnego obiektu. Jeśli nie chcesz implementować podobnej funkcji w każdej jednej klasie która tego wymaga, zainteresuj się proszę biblioteką [freezed](https://pub.dev/packages/freezed). Działa w oparciu o automatyczne generowanie kodu, zupełnie jak *mobx_codegen*.

W przykładzie jak widać wybrałem rozwiązanie #2, *Immutable objects* to coś co ~~szkaluję~~ szanuję.

#### Generowanie kodu

Zanim przejdziemy do ostatniego elementu, czyli reakcji, wygenerujmy wspomniany już wcześniej plik *store.g.dart*. Jest to proces w pełni automatyczny, a sam nie musisz nawet do niego zaglądać. To trochę tak jakbyś chciał podejrzeć zdjęcie w terminalu, albo kod wynikowy programu (*.exe*, *.dmg*). W celu jednorazowego wygenerowania pliku uruchom:

```bash
flutter pub run build_runner build --delete-conflicting-outputs
```

Istnieje również tryb obserwowania, który przegeneruje niezbędne pliki przy każdorazowej ich zmianie. Możesz go przykładowo włączyć, gdy rozpoczynasz pracę i zapomnieć na cały dzień.

```bash
flutter pub run build_runner watch --delete-conflicting-outputs
```

Jeśli postanowisz sprawdzić jak wygląda nowo wygenerowany plik - śmiało. Jest czytelny, a najważniejsza informacja jest zawarta już w pierwszej linii:

```dart
// GENERATED CODE - DO NOT MODIFY BY HAND
```

Nigdy, przenigdy nie wprowadzaj zmian w plikach auto-generowanych. Zdarzyło mi się popełnić kilka razy ten błąd w życiu (nie w Darcie) gdzie zamiast na pliku wejściowym operowałem na tym wygenerowanym. Wystarczy wtedy jedno nieumyślne poproszenie o przegenerowanie plików i cała praca zostaje nadpisana przez automat.

Pytanie co robić z plikami *.g.dart* - trzymać je w repozytorium, czy może dodać do ignorowanych? Osobiście nie wysyłam ich do systemu kontroli wersji, bo nie mają same w sobie żadnej wartości. Wystarczy uruchomić skrypt i zostaną automatycznie wygenerowane na nowo na podstawie plików źródłowych. Wybór jednak zostawiam tobie, żadne podejście nie jest grzechem śmiertelnym i zależy od osobistych preferencji.

#### Reactions

Czas na element, który będzie potrafił zareagować na zmiany w *Observable*. Reakcja (**reaction**) to inaczej *Observer*, funkcja zarejestrowana i uruchamiana zawsze gdy powiązany z nią *Observable* zmieni swój stan. Nie będziemy pisać reakcji niskopoziomowych, skupimy się wyłącznie na tej, która interesuje nas najbardziej z punktu widzenia aplikacji mobilnej. **Zmiana stanu to zmiana UI.**

Tutaj wkracza moja ulubiona część, czyli biblioteka **flutter_mobx**. Jedyne co dostarcza to pojedynczy widget **Observer**, który jest swoistą reakcją. Przebuduje się on automatycznie za każdym razem, gdy będzie taka potrzeba.

Wróćmy zatem do naszego UI (*main.dart*) i tchnijmy w niego nieco życia. Na początku warto by było zainicjalizować stan.

```dart
void main() => runApp(MyApp());

final store = UserStore();
```

Zainicjalizowałem właśnie zmienną globalną. **GLOBALNĄ**. W prawdziwej aplikacji zdecydowanie bym tego unikał, ale proste rzeczy są proste, stąd taki wybór do demonstracji. Jeśli chcesz zbudować większą i przede wszystkim testowalną aplikację, polecam użycie [get_it](https://pub.dev/packages/get_it), lub [provider](https://pub.dev/packages/provider), aby dostarczać stan aplikacji w dół drzewa. We własnych projektach korzystam z *get_it*, bo robi w tym wypadku to samo co *provider*, ale za to mniejszą ilością kodu i bez potrzeby przekazywania kontekstu (*context*).

Co dalej? Wszędzie tam, gdzie chcesz mieć dostęp do zawsze aktualnego stanu użyj widgeta *Observer*. Owijamy, więc *Scaffold* i dostarczamy do niego stan.

Przed:
```dart
Scaffold(
  appBar: AppBar(
    title: Text("Previous users: 0"),
  ),
  body: Center(
    child: isAuthenticated ?
      Authenticated() :
      Unauthenticated(),
  ),
)
```

Po:
```dart
Observer(
  builder: (_) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Previous users: ${store.previousUsers.length}"),
      ),
      body: Center(
        child: store.isAuthenticated ? 
          Authenticated() :
          Unauthenticated(),
      ),
    );
  },
)
```

Użycie *Observera* może wydać ci się delikatnie wybrakowane. Przecież nie powiedzieliśmy mu na jakie zmiany ma nasłuchiwać. Skąd zatem wie, które obiekty ma śledzić?

- Wszystkie zdefiniowane w *store.dart*? **Nie**
- Żadnego? **Nie**
- Tylko te które użyte są wewnątrz *buildera*? **Tak!**

*MobX* sam, bez twojego udziału zadba o to, aby *rebuild* nastąpił tylko wtedy, gdy zmieni się *Observer* użyty wewnątrz wywołania funkcji *builder*. Ty się niczym nie przejmuj, zrób sobie kawę i odpocznij. Co więcej - jeśli nie użyjesz żadnego *Observera* wewnątrz funkcji to konsola wyświetli ostrzeżenie. Skoro nic nie chcesz obserwować to po co używasz Observera?

![Mind blown](https://media.giphy.com/media/xT0xeJpnrWC4XWblEk/giphy.gif)

#### Integracja akcji

Nie ma nic prostszego niż integracja UI z akcjami. Jedyne co należy zrobić to wywołać odpowiednią akcję i ... gotowe. Żadnego dodatkowego wpinania kabelków i dispatcherów.

Przed:
```dart
void login() {}

void addSkill() {}

void logout() {}
```

Po:
```dart
void login() => store.login();

void addSkill() => store.addSkill();

void logout() => store.logout();
```

Uruchomienie aplikacji w obecnym stanie spowoduje, że można się do niej zalogować i wylogować. Licznik poprzednich użytkowników również poprawnie się odświeża po wylogowaniu. Działa wszystko z wyjątkiem dodawania nowej umiejętności, bo tam ciągle mamy zahardkodowane wartości, nie korzystające ze stanu.

W tym fragmencie nie ma już nic odkrywczego. Ponownie korzystamy z *Observera* i czytamy aktualne dane o użytkowniku. Nie ma tutaj niczego nowego nie dlatego, że jestem leniwy i mi się nie chciało. To już generalnie wszystko co musisz wiedzieć, żeby zacząć budować aplikację w oparciu o *MobX*.

```dart
class Authenticated extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Observer(
      builder: (_) {
        final user = store.me;

        return Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text("Name: ${user.name}"),
            Text("Birth date: ${user.birthDate}"),
            Text("Skills: ${user.skills}"),
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                RaisedButton(
                  onPressed: addSkill,
                  child: Text("New skill"),
                ),
                RaisedButton(
                  onPressed: logout,
                  child: Text("Log out"),
                ),
              ],
            )
          ],
        );
      }
    );
  }

  void addSkill() => store.addSkill();

  void logout() => store.logout();
}
```

### Podsumowanie

*MobX* jest świetnym i sprawdzonym w boju rozwiązaniem do zarządzania stanem aplikacji. Nie wymaga dużej ilości kodu, jest wydajny i sporo rzeczy dzieje się bez udziału programisty. Generowanie kodu niemal do zera redukuje ilość *boilerplate*, a widget *Observer* ze swoim auto przebudowywaniem oszczędza czasu na zastanawianiu się pod które części stanu trzeba się zarejestrować.

Samo korzystanie z biblioteki ogranicza się do:
- definiowania klas z wyeksponowanymi obiektami `@observable` i `@computed`
- implementacji akcji mutujących stan `@action`
- korzystanie z widgeta `Observer` na warstwie UI
- pamiętaniu o przegenerowaniu kodu po zmianach (lub uruchomienia *watcha*)
- polubienia *immutable objects*

Osobiście *MobX* to dla mnie numer #1 jeśli chodzi o dostępne biblioteki do zarządzania stanem. Jest prosty, szybki i przyjemny, a są to cechy, które mocno sobie cenię zwłaszcza w pobocznych projektach robionych po godzinach. Używam go m.in. w developmencie pierwszego RPG online osadzonego w świecie post-apo we Flutterze - [Vaultomb](https://twitter.com/vaultomb). Premiera w dalszej przyszłości :)