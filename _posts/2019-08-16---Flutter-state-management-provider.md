---
color: rgb(69,190,247)
title: Zarządzanie stanem aplikacji - Provider
date: "2019-08-16"
layout: "post"
slug: "flutter-state-management-provider"
excerpt: "Każda średnia i duża aplikacja we Flutterze wymaga narzędzia do zarządzania stanem globalnym. Kiedy go potrzebujesz i dlaczego stan lokalny to za mało? Poznaj Providera - bibliotekę, która ułatwia proces do granic możliwości."
thumbnail: "assets/img/blog/state_provider/thumbnail.png"
---

Jest to druga część serii o zarządzaniu stanem aplikacji we Flutterze. W [poprzednim wpisie](/blog/flutter-state-management-stateful/) rozmawialiśmy o tym czym dokładnie jest stan i w jaki sposób odbywa się jego zarządzanie (**state management**). Stworzyliśmy prostą grę typu clicker, która korzysta ze stanu, aby móc prezentować wciąż aktualne dane graczowi. Wszystko odbywało się w **formie lokalnej**, czyli takiej która jest ograniczona wyłącznie do widgetu w którym stan został zadeklarowany.

W dzisiejszym wpisie poznasz zagadnienie nieco bardziej złożone, lecz niezbędne z punktu widzenia średnich i dużych aplikacji. **Stan globalny**, nazywany również **współdzielonym**. Cała wiedza którą posiadasz na temat stanu lokalnego (czym jest, jak nim zarządzać) jest jak najbardziej nadal aktualna! Co więcej - jest niezbędna w przyswojeniu nowego konceptu. Jeśli jej nie posiadasz - zapraszam Cię do pierwszej części serii. Zmienia się wyłącznie zakres w obrębie którego mamy dostęp do naszego stanu. Przechodzimy z zasięgu **per widget** na **per drzewo**.

## Stan lokalny vs stan globalny

Wyróżniamy dwa rodzaje stanu aplikacji - **lokalny** i **globalny**. Nie ma w tych pojęciach nic podchwytliwego i oznaczają właśnie to co sugeruje ich nazwa. Różnica między nimi jest taka jak między zmienną prywatną, a publiczną w kodzie - chodzi o zasięg określający gdzie dokładnie można skorzystać z danego stanu, czy zmiennej. Jest to co prawda spore uproszczenie, ale warto trzymać je z tyłu głowy aby mieć na start jakikolwiek punkt odniesienia (później możesz go wyrzucić z głowy).

Większość aplikacji korzysta z obu tych wariantów, pomimo tego, że zawsze istnieje sposób aby napisać projekt w kompletnym oparciu się tylko o jeden z nich. Dlaczego więc programiści komplikują sobie życie i nie wybierają jedynego słusznego rozwiązania? Bo takowe nie istnieje i wszystko zależy od rodzaju danych, którymi chcemy zarządzać. **Zawsze dobieraj odpowiednie narzędzie do konkretnej sytuacji.**

> To że bułkę można przekroić nożyczkami, nie znaczy że jest to najlepszy sposób. Nie strzelaj sobie w kolano i dobieraj rodzaj stanu według aktualnych potrzeb. Nie daj się również ocyganić, że stan lokalny to anty-pattern.

### Stan lokalny

Zarządzanie stanem lokalnym we Flutterze odbywa się poprzez dobrze nam już znany `StatefulWidget`. Stan lokalny to zestaw danych wykorzystywany wyłącznie na potrzeby pojedynczego ekranu (precyzyjniej - widgetu), gdzie nie ma potrzeby, aby inne komponenty w aplikacji zaprzątały sobie nim głowę, lub miały do niego dostęp.

Idealnym przykładem może być odliczanie czasu do rozpoczęcia rozgrywki w dowolnej grze mobilnej. Gracz wybiera postać oraz poziom na którym chce zagrać, a następnie pojawia się ekran typu *"3 ... 2 ... 1 ... Start!"* mający dać mu chwilę na przygotowanie. Informacja o tym ile sekund pozostało do rozpoczęcia potyczki jest najprawdopodobniej istotna wyłącznie dla aktualnego ekranu - żaden inny nie będzie tą informacją zainteresowany i udostępnianie jej to strata czasu i często **over-engineering**.

![zgadula_countdown.png](/assets/img/blog/state_provider/local_state.gif)

> Używaj śmiało stanu lokalnego wszędzie tam, gdzie nie chcesz go współdzielić między różnymi widgetami. Wybór leży po Twojej stronie i może się okazać, że nawet powyższy timer ma sens na trafienie do ~~nieba~~ stanu globalnego.

Kiedy stan lokalny przestaje się sprawdzać? Przy złożonej aplikacji rośnie nam głębokość drzewa rysowanych widgetów i schodzimy coraz niżej w dół. Pojawia się także sporo własnych klas widgetów, aby zachować jakość i czytelność kodu. W tym wszystkim zachodzi komunikacja między widgetami na różnym poziomie. Wduszam *przycisk A* na dole strony, a na górze pojawiają się nowe wartości. Zmieniam język na ekranie ustawień, a przez to cała reszta aplikacji jest po polsku.

![global_state_diagram.png](/assets/img/blog/state_provider/global_state_diagram.png)

> Diagram przedstawiający główny problem stanu lokalnego. W jaki sposób przekazać wartość **Username** do **WidgetA1** i **WidgetB1** bez ręcznego przekazywania przez wszystkie warstwy w stylu *MyApp -> HomePage -> WidgetA -> WidgetA1*, a dodatkowo umożliwić zmianę tego pola z dowolnego miejsca drzewa?

Co z tym fantem zrobić? Gdzie trzymać ich wspólny stan? Co w przypadku gdy musisz dzielić się danymi pomiędzy różnymi komponentami systemu? Oto zadanie specjalne dla ...

### Stan globalny

... stanu globalnego! Podobnie jak w przypadku stanu lokalnego, Flutter również tutaj dostarcza własny i wbudowany mechanizm w postaci `InheritedWidget`. Przechowuje on stan w taki sposób, że niemal każdy widget jest w stanie go samodzielnie odczytać i przebudować się w razie potrzeby. Czy oznacza to więc, że będziemy go używać w dalszej części wpisu? Otóż nie tym razem. Widget ten jest dość **niskopoziomowy** i **niezbyt przyjemy** w codziennym użytkowaniu. W moim odczuciu wymaga **zbyt wiele kodu**, aby zrealizować nawet najprostszy efekt.

Właśnie dlatego powstała biblioteka **Provider**, której głównym zadaniem jest dostarczenie tzw. lukru składniowego (syntax sugar) na wbudowany i toporny `InheritedWidget`. Cel swój realizuje w 100% - praca z nią jest przyjemna i szybka, a dodatkowo została namaszczona przez Google jako niemal oficjalne rozwiązanie do zarządzania stanem. **Solidna rekomendacja.**

![zgadula_countdown.png](/assets/img/blog/state_provider/zgadula_countdown.gif)

Do **Providera** wrócimy w dalszej części, spójrz jednak na gifa umieszczonego powyżej. Mamy w nim uwzględnione trzy różne ekrany przez które przechodzi gracz.

1. Skrolowalna lista kategorii z której wybierana jest dowolna pozycja. W stanie globalnym przechowujemy pełną listę kategorii (**categories**) oraz informację o aktualnie zaznaczonej (**active_category**), która w obecnej chwili jest pusta (*null*). Po stuknięciu w dowolny element, **active_category** zyska wartość, która może być następnie odczytywana przez inne widgety.

![step_1.png](/assets/img/blog/state_provider/step_1.png)

2. Detal kategorii prezentujący aktualny wybór z grafiką i dodatkowym opisem. W celu prezentacji odpowiednich danych wykorzystywany jest stan **active_category**, aby określić identyfikator aktywnej kategorii (*12*), a następnie lista **categories** jest przeszukiwania pod kątem danego identyfikatora, aby wyszukać pełny obiekt*. Użytkownik ma również możliwość zmiany czasu rundy (30-60-90-120), która także przechowywana jest w globalnym stanie.

![step_2.png](/assets/img/blog/state_provider/step_2.png)

<sup><sub>*Pod kątem wydajnościowym lepiej byłoby przechowywać strukturę w formie mapy, gdzie kluczem jest identyfikator, a wartością obiekt z danymi. Nie ma to jednak znaczenia pod względem merytorycznym.</sub></sup>

3. Ekran końcowy na którym w górnej części widoczna jest nazwa wybranej kategorii to ponownie tandem **active_category**+**categories**. W dolnej widoczny jest pozostały czas rundy, która rozpoczyna odliczanie od wartości **round_time**. Czas pozostały do końca gry nie jest trzymany w stanie globalnym, a jedynie w lokalnym - inne widgety nie będą potrzebowały go konsumować w żadnym celu.

![step_3.png](/assets/img/blog/state_provider/step_3.png)

Ekrany *#2 i #3* potrzebują w jakiś sposób uzyskać informację, jaka kategoria została wybrana na ekranie #1, a ekran #3 potrzebuje dodatkowo wiedzieć jaki jest maksymalny czas rundy z ekranu #2. Jako, że są to widgety całkowicie od siebie niezależne nie możemy wykorzystać stanu lokalnego - w takim przypadku tylko ekran #1 wiedziałby która z kategorii jest aktywna, pozostałe żyłyby w niewiedzy. A wiedza ta jest im niezbędna do zaprezentowania poprawnej grafiki, czy nazwy.

Tym właśnie zajmuje się stan globalny. Dba o to, aby wskazane dane były ogólnodostępne dla wszystkich zainteresowanych. Zajmuje się także (jak to stan) informowaniem o wszelkich zmianach, aby możliwe było automatyczne przebudowanie widgetów. Zupełnie jak wywołanie `setState`.

> Nie przejmuj się jeśli w głowie masz pytania o to który stan powinien być globalny, a który lokalny. Początkowo wszystko może być lokalne, a następnie zrefaktorowane gdy najdzie taka potrzeba. Z czasem nabierzesz fachowego przeczucia i określanie zasięgu będzie przychodziło naturalnie. Nie lubię frazesów, ale **Praktyka czyni mistrza**.

## Provider

Jednym z najpopularniejszych i przystępnych narzędzi we Flutterze do zarządzania stanem globalnym jest wspomniana już paczka [Provider](https://pub.dev/packages/provider). Przychylność developerów zyskała  dzięki niskiemu progowi wejścia oraz minimalną ilością kodu, którą trzeba wklepywać raz-po-raz, a nie jest związana bezpośrednio z logiką naszej aplikacji. Chodzi fachowo o **boilerplate** - kod którego każdy chce unikać jak ognia. Nie ma go tutaj praktycznie wcale, co skutkuje tym, że mamy mniej kodu do utrzymania. **WIN-WIN**. Ciekawa jest również sama historia która stoi za biblioteką, którą możesz prześledzić na [GitHubie](https://github.com/google/flutter-provide/issues/3).

**TL;DR** *Google w podobnym czasie co Remi (authora Providera) pracował nad bliźniaczym rozwiązaniem. Wycofał się jednak z niego i pobłogosławił Providera jako "oficjalne" rozwiązanie.*.

Starczy jednak tego wywodu teoretyczno-historycznego. Zajmijmy się tym co naprawdę istotne. Co robi ten cały Provider i jak nam pomoże w pisaniu aplikacji mobilnej? **Pora na warsztaty!**

## Przygotowanie aplikacji

Usiądźmy do praktycznego przykładu, aby lepiej poznać niezbędne tajniki stanu globalnego. Nie weźmiemy na warsztat co prawda aplikacji z powyższego gifa, bo jest zbyt skomplikowana jako punkt referencyjny. Zbyt wiele się w niej dzieje. Wolę abyśmy skupili się jedynie na tym co jest naprawdę istotne, czyli kwestii zarządzania stanem. Co więc proponuję? **Colorek** - mini program do mieszania kolorów.

![colorek.gif](/assets/img/blog/state_provider/colorek.gif)
> Colorek w wersji 1.0.0

Aplikacja polega na dobieraniu proporcji trzech bazowych kolorów przy pomocy suwaków. W trakcie doboru są one mieszane i prezentowane jako pojedynczy kolor **RGB** (**R**ed **G**reen **B**lue) na dole ekranu. Jeśli nie słyszałeś nigdy o RGB - jest to model kolorystyczny w którym dobieramy wartości trzech tytułowych kolorów z przedziału od 0 do 255, a z mieszanki tej powstaje kolor wynikowy. Skrajnymi kolorami są biały (wszystkie wartości na 0) oraz czarny (255).

Ale, ale! Gdzie tu jest niby stan globalny? Wszystko jak na dłoni widać na pojedynczym ekranie, można to więc ograć stanem lokalnym i zakończyć materiał szkoleniowy. No niby tak, ale nie do końca. Można by się pokusić o użycie stanu lokalnego - jak zawsze. Jednak z punktu widzenia dobrych praktyk i tego że najłatwiej się uczyć na prostych przykładach zostaniemy przy nim. Zaufaj mi, że to pierwszy krok ku lepszemu światu i nieograniczonym możliwościom.

### Punkt wejściowy (main.dart)

Plik **main.dart** wykorzystamy jedynie jako punkt wejściowy do aplikacji. W trakcie dalszej implementacji będzie rozszerzany o dodatkowe konfiguracje, ale bez żadnej logiki, czy stylowania. Na start jego głównym zadaniem będzie narysowanie widgetu `HomePage`. Nie jest to widget wbudowany we Fluttera, musimy go za chwilę sami zakodować.

```dart
import 'package:flutter/material.dart';

import 'pages/home_page.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Provider Demo',
      home: HomePage(),
    );
  }
}
```

> Nie ma magii, nie ma czarów. Prosty bezstanowy widget, który niemal nic nie robi. Zwróć jedynie uwagę, że `HomePage` pochodzi z innego pliku (import).

### Ekran główny (pages/home_page.dart)

Stwórzmy katalog **pages**, a w nim plik **home_page.dart**. Będzie to nasz widget reprezentujący ekran główny aplikacji - rysowany zaraz po jej uruchomieniu. Zwróć uwagę, że pomimo faktu bycia widgetem wyświetlającym pełny ekran (w praktyce - złożonym) **nie jest on stanowy**. Taki też pozostanie.

```dart
import 'package:flutter/material.dart';

import '../components/rgb_slider.dart';
import '../components/rgb_preview.dart';

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          RGBSlider(),
          Expanded(
            child: RGBPreview(),
          )
        ],
      ),
    );
  }
}
```

W celu rozproszenia odpowiedzialności rozbiliśmy nasz ekran na dwa główne komponenty, które same sobie rzepkę skrobią:

1. `RGBSlider` - część służąca do zmiany wartości kolorów przy pomocy interaktywnych suwaków.
2. `RGBPreview` - podgląd koloru wynikowego, który aktualizuje się automatycznie na zmianę wartości w stanie aplikacji.

Możliwe że zapaliła Ci się mała lampka kontrolna patrząc na powyższy przykład. Wygląda na tyle prosto, że z tego miejsca mógłbyś zakopać pomysł o globalnym stanie, a samo rozwiązanie oprzeć o stan lokalny. Ot poprosisz `HomePage` żeby przytrzymał Ci ~~piwo~~ wartości kolorów, które przekażesz manualnie w dół do `RGBSlider` i `RGBPreview` a slider dostanie jeszcze funkcje do ich zmiany. Właśnie tak:

```dart
import 'package:flutter/material.dart';

import '../components/rgb_slider.dart';
import '../components/rgb_preview.dart';

class HomePage extends StatefulWidget {
  HomePage({Key key}) : super(key: key);

  @override
  HomePageState createState() => HomePageState();
}

class HomePageState extends State<HomePage> {
  int red = 0;
  int green = 0;
  int blue = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Column(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: [
          RGBSlider(
            red: red,
            onRedChange: (value) => setState(() { red = value; }),
            green: green,
            onGreenChange: (value) => setState(() { green = value; }),
            blue: blue,
            onBlueChange: (value) => setState(() { blue = value; }),
          ),
          Expanded(
            child: RGBPreview(
              red: red,
              green: green,
              blue: blue,
            ),
          )
        ],
      ),
    );
  }
}
```

Zadziała? Pewnie że tak! Czy jest to dobre rozwiązanie? Być może. Czy stan globalny będzie lepszym wyborem? To zależy. Powyższa struktura wygląda mocno pokracznie - co zrobisz gdy będzie trzeba przekazać kolory jeszcze jeden poziom w dół? **Kod spaghetti**. Właśnie dlatego z zaciekawieniem patrzymy na stan globalny.

Do pełni możliwości skompilowania projektu - a to pierwszy krok ku temu aby działał poprawnie - brakuje nam już tylko dwóch rzeczy. A raczej widgetów. Wspomniamy `RGBSlider` i `RGBPreview`, które umieścimy w katalogu **components** stworzonym na tym samym poziomie co **pages**.

![project_structure.png](/assets/img/blog/state_provider/project_structure.png)
> Struktura projektu. Pojawiają się w niej **details_page.dart** oraz **login_page.dart**, które możesz narazie zignorować. Są to zadania bonusowe do zrobienia w formie pracy domowej.

### Modyfikator kolorów (components/rgb_slider.dart)

Trzy suwaki gdzie każdy służy do określenia wartości innego koloru. Jako, że są to widgety bliźniacze i różnią je jedynie drobne szczegóły (np. wyświetlany tekst, czy kolor suwaka) - skorzystamy z funkcji pomocniczej `buildSlider`. Narysuje ona pojedynczy suwak na ekranie z odpowiednią konfiguracją.

```dart
import 'package:flutter/material.dart';

class RGBSlider extends StatelessWidget {
  Widget buildSlider(
      {String label, Color color, double value, Function onChanged}) {
    return Padding(
      padding: const EdgeInsets.only(bottom: 16),
      child: Column(
        children: [
          Text(label),
          Slider(
            value: value,
            min: 0,
            max: 255,
            onChanged: onChanged,
            activeColor: color,
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        buildSlider(
          label: "Red",
          color: Colors.red,
          value: 0,
          onChanged: (value) => print("Red $value"),
        ),
        buildSlider(
          label: "Green",
          color: Colors.green,
          value: 0,
          onChanged: (value) => print("Green $value"),
        ),
        buildSlider(
          label: "Blue",
          color: Colors.blue,
          value: 0,
          onChanged: (value) => print("Blue $value"),
        ),
      ],
    );
  }
}
```

Do funkcji `buildSlider` przekazujemy dwa kluczowe parametry odpowiadające za jego działanie: wartość (**value**) i funkcję do uruchomienia gdy wartość powinna się zaktualizować (**onChanged**). Nasza wartość jest zawsze zerem, a gdy dostajemy informację że powinniśmy ją zaktualizować - robimy **printa**. Wszystko prawie dobrze, ale można by to zrobić delikatnie lepiej (bardziej działająco).

### Podglądacz koloru (components/rgb_preview.dart)

Naprostszy możliwy widget we Flutterze w postaci kolorowego kwadratu. A my go jeszcze honorujemy własnym plikiem i dumną nazwą `RGBPreview`. Nie da się jednak ukryć że robi to co sugeruje, czyli wyświetla podgląd kolorystyczny.

```dart
import 'package:flutter/material.dart';

class RGBPreview extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        width: 200,
        height: 200,
        color: Color.fromRGBO(11, 7, 218, 1),
      ),
    );
  }
}
```

`Color.fromRGBO(11, 7, 218, 1)` wygeneruje nam kolor, który składa się z 11 jednostek czerwonego, 7 zielonego, 218 niebieskiego. Jedynka na końcu określa przezroczystość, a raczej jej brak.

![colorek_draft.png](/assets/img/blog/state_provider/colorek_draft.png)
> Colorek w wersji 0.0.1-alpha

Właśnie w tym miejsciu kończymy nieciekawy **boilerplate** w postaci części wizualnej i rozpoczynamy oprogramowanie stanu. Aplikacja już w tym momencie powinna się poprawnie budować, zachęcam Cię do sprawdzenia, abyśmy na pewno byli w tym samym punkcie. Nie działa jednak jeszcze prawidłowo, czas to zmienić!

## Dostarczanie stanu

Mamy już przygotowany pełen layout aplikacji, a nie dotknęliśmy jeszcze nawet w najmniejszym stopniu kwestii stanu. O to się nie martw. Właśnie teraz, w tym momencie rozpoczynamy proces nadrabiania strat i wyrównywania szans. Pora na turbo dawkę wiedzy praktycznej dotyczącej stanu globalnego, a w szczególności Providera. Ożywmy nasz projekt! ⚡

Zarządzanie stanem w Providerze wymaga trzech bazowych elementów:

1. Dostawca (**Provider**) - widget umieszczony na górze drzewa i propagujący swoje dane (stan) nieprzerwanie w dół do zainteresowanych odbiorców.
2. Odbiorca (**Consumer**) - element ulokowany na dole drzewa i nasłuchujący wskazanych dostawców (jednego lub wielu). Poza odczytem, może także zmieniać dostarczony stan oraz automatycznie się przebudować na dowolną jego zmianę.
3. Stan (**State**) - paczka z danymi która wędruje od dostawcy do odbiorcy.

Obrazową analogią wymienionych elementów jest klasyczna gra **Donkey Kong** z NES-a (lokalnie zwanego pegazusem). Tytułowy goryl (dostawca) miota w dół planszy (drzewa) beczkami (stanem), które Mario (odbiorca) łapie aby dowiedzieć się co jest w środku. Gdy tylko Mario złapie beczkę zmienia swój stan - ginie, umiera, znika na zawsze. Główna różnica polega na tym, że Mario nie chce zmieniać swojego stanu (nie dziwi mnie to). My chcemy! Będziemy więc łapać interesujące nas "beczki" i aktualizować w locie warstwę prezentacyjną.

![donkey_kong.png](/assets/img/blog/state_provider/donkey_kong.png)

### Instalacja zależności

W pliku *pubspec.yaml* dodaj nową zależność i zainstaluj ją:
```yaml
dependencies:
  provider: ^3.0.0+1
```

Nie wiesz jak zainstalować dodaną zależność? W konsoli przejdź do katalogu projektu i wykonaj następującą komendę:

```bash
flutter pub get
```

### Stan (state)

Pierwszym elementem łańcucha który weźmiemy na warsztat jest stan. Definiujemy go poprzez utworzenie własnej klasy rozszerzającej Flutterowy `ChangeNotifier`. Jest to bazowa klasa, która udostępnia ogólne API do powiadamiania o zmianach - idealnie więc wpasowuje się w tematykę zmiany stanu. 

Tuż obok **main.dart** stwórzmy plik **models.dart** w którym przechowamy definicję stanu dla kolorów.

```dart
import 'package:flutter/material.dart';

class ColorModel extends ChangeNotifier {
  double _red = 0;
  double _green = 0;
  double _blue = 0;

  double get red => _red;
  set red(double value) {
    _red = value;
    notifyListeners();
  }

  double get green => _green;
  set green(double value) {
    _green = value;
    notifyListeners();
  }

  double get blue => _blue;
  set blue(double value) {
    _blue = value;
    notifyListeners();
  }
}
```

Powyższa klasa przechowuje informacje o wartościach trzech kolorów: czerwony, zielony, niebieski. Pola zdefiniowane są jako prywatne (poprzedzone znakiem **_**), aby nikt poza klasą nie mógł ich zmienić w sposób niekontrolowany. Jest to ostatnia rzecz jakiej potrzebujesz w swojej aplikacji. Kontrolowany stan to dobry stan.

Publiczny odczyt (**get**) odbywa się po identycznej nazwie jak nazwa pola prywatnego z tym że bez podkreślnika - standardowy zabieg. W celu odczytu wartości pola `double _red`, poprosisz po prostu o `model.red`, gdzie `model` to instancja klasy. Publiczna zmiana (**set**) dokonuje stosownej zmiany pola (norma), a dodatkowo wywołuje `notifyListeners()`, aby poinformować o zmianie wszystkich zainteresowanych odbiorców. W ten sposób kontrolujemy wartość stanu, a dodatkowo to jakie informacje mają w danym czasie wszyscy odbiorcy.

Moglibyśmy rzecz jasna zaimplementować wszystko bez gettera i settera. Dzięki takiemu zabiegowi kod będzie znacząco krótszy ...

```dart
import 'package:flutter/material.dart';

class ColorModel extends ChangeNotifier {
  double red = 0;
  double green = 0;
  double blue = 0;
}
```

... ale jednocześnie niedziałający w sposób jakiego oczekujemy. Można co prawda zmieniać dowolnie wartości kolorów, ale żaden odbiorca nie zostanie o tym fakcie poinformowany, bo brakuje w kluczowym momencie wywołania `notifyListeners()`. Po co zmieniać stan, skoro aplikacja nie może na niego zareagować? Fajna ta implementacja, taka nie za mądra.

> Reasumując - lepsza dłuższa implementacja która działa, niż krótsza która nie robi nic.

Pracując z klasą stanu pamiętaj o kilku prostych ~~trikach~~ zasadach:

1. Przerysowanie drzewa odbędzie się tylko gdy spełnione są następujące kryteria:
    - Aktualnie wyświetlany widget zarejestrował się jako odbiorca (o tym w kolejnej sekcji)
    - Klasa stanu uruchomi funkcję `notifyListeners()`, która jest kluczowa z punktu widzenia zarządzania stanem. Można ją przyrównać do `setState`, który również przebudowuje fragment drzewa.
2. Każdorazowe zawołanie `notifyListeners()` prowadzi do przebudowania zależnych widgetów. Nie wykonuj go więc częściej niż naprawdę potrzebujesz. Unikaj sytuacji w której pojedyncza funkcja zmieniająca stan wywołuje ją więcej niż jeden-dwa razy.
3. Do wykonania akcji na stanie (np. zmiana koloru) wymagane jest zarejestrowanie się jako odbiorca. Nie możesz zmienić globalnego stanu, nie będąc jego odbiorcą. Kropka.

### Dostawca (provider)

Dostawca jest widegetem nadrzędnym, który umieszczony w dowolnym miejscu drzewa, propaguje/informuje wszystkie swoje dzieci (również te zagnieżdzone X poziomów w dół) o aktualnym stanie aplikacji.

Pomimo tego, że dostawca może być umieszczony w dowolnym miejscu drzewa, w praktyce często definiuje się go bezpośrednio w pliku konfiguracyjnym `main.dart`. Chodzi o to, aby umieścić go w szczytowym miejscu drzewa (korona drzewa?) skąd jest w stanie obsłużyć całą aplikację bez wyjątku.

Przejdźmy do pliku **main.dart**, aby skonfigurować dostawcę stanu dla aplikacji.

**Przed:**
```dart
void main() => runApp(MyApp());
```

**Po:**
```dart
import 'package:provider/provider.dart';

import 'models.dart';

void main() => runApp(
  MultiProvider(
    providers: [
      ChangeNotifierProvider(builder: (context) => ColorModel()),
    ],
    child: MyApp(),
  ),
);
```

Do osadzenia potrzebujemy trzech rzeczy:
1. `MultiProvider` widget rejestrujący wielu dostawców na raz. Klasyczny syntax sugar, który jedynie upraszcza sposób rejestracji - można to robić również jeden po drugim (ale wymaga więcej kodu).
2. `ChangeNotifierProvider` pojedynczy dostawca stanu, budowany na podstawie klasy pochodnej od `ChangeNotifier`.
3. `ColorModel`  klasa przechowująca stan, którą dopiero co utworzyliśmy.

Alternatywnym (i krótszym) rozwiązaniem jest pominięcie `MultiProvider`. Ma on realne zastosowanie tylko gdy mamy więcej niż jedną klasę stanu, a bazowa wersja Colorka nie jest na tyle złożona aby jej wymagała.

```dart
void main() => runApp(
  ChangeNotifierProvider.value(
    value: ColorModel(),
    child: MyApp(),
  )
);
```

### Odbiorca (consumer)

Jesteśmy na etapie w którym mamy zbudowany layout oraz dwie z trzech częsci stanu. Aplikacja poprawnie się buduje i uruchamia na emulatorze, bądź urządzeniu. Nie jest jednak interaktywna. Brakuje ostatniego klocka w wieży lego, ostatniej nutki w symfonii. Jest nim odbiorca stanu. Coś co konsumuje stan i wyświetla go na ekranie, a rownocześnie moze go aktualizować jeśli uzna że istnieje taka potrzeba.

Przed rozpoczęciem konsumowania stanu wymagany jest ... import biblioteki. Wiadomo - przy dostawcy zrobiliśmy to samo, ale chcę mieć pewność że o tym pamiętasz. W każdym pliku który korzystać będzie z Providera potrzebujemy poniższego importu.

```dart
import 'package:provider/provider.dart';
```

Pozostało nam jedynie wybranie sposobu w jaki skonsumujemy dostępny stan. Wybranie? Tak. Provider dostarcza dwa niezależne sposoby na to, aby móc korzystać i operować stanem. Są one bardzo zbliżone, niemal identyczne - różnią je jak zawsze detale implementacyjne.

> Żaden ze sposobów nie jest lepszy, lub gorszy. Mają delikatnie różne zastosowanie, jednak osobiście częściej korzystam z Consumera.

#### Provider.of

Pierwszym sposobem na korzystanie ze stanu jest statyczna funkcja `Provider.of`. Wymaga ona podania klasy stanu którą chcemy odnaleźć, oraz aktualnego kontekstu. Dodatkowo przyjmuje specjalną flagę **listen** (domyślnie *true*), która określa czy widget który wywołał funkcję będzie automatycznie przebudowywany po jakiejkolwiek zmianie stanu w obrębie odnalezionej klasy.


```dart
Provider.of<ColorModel>(
  context, listen: true,
)
```

Zwraca instancję stanu z której możemy wyciągnąć interesujące dane, a dodatkowo wywoływać dowolne metody na nim występujące. Widget na poziomie którego wykonamy to polecenie będzie automatycznie przebudowywany za każdym razem gdy w `ColorModel` wywołana zostanie funkcja `notifyListeners()`.

```dart
Provider.of<ColorModel>(
  context, listen: false,
)
```

Analogicznie jak powyżej, z tą różnicą że widget nigdy automatycznie się nie przebuduje. Używaj z rozwagą - wyłącznie w przypadkach gdy chcesz zmieniać stan, ale bezpośrednio od niego nie zależysz (nie wyświetlasz go).

**Prosty przykład:**

```dart
var state = Provider.of<ColorModel>(context, listen: true);

return RaisedButton(
  child: Text(state.red),
  onPressed: () => state.red++;
);
```

**Zalety:**
+ Mało kodu
+ Możliwość operowania stanem bez konieczności przebudowania

**Wady:**
- Zmiana stanu powoduje przebudowanie całego widgetu odbiorcy

W celu zademonstrowania działania na żyjącym organizmie, użyjmy tego sposobu zapisu do obsługi suwaków. Przejdźmy do pliku **components/rgb_slider.dart** i wymieńmy całkowicie metodę build.

```dart
import '../models.dart';

// ...

@override
Widget build(BuildContext context) {
  var color = Provider.of<ColorModel>(context, listen: true);

  return Column(
    children: [
      buildSlider(
        label: "Red",
        color: Colors.red,
        value: color.red,
        onChanged: (value) => color.red = value,
      ),
      buildSlider(
        label: "Green",
        color: Colors.green,
        value: color.green,
        onChanged: (value) => color.green = value,
      ),
      buildSlider(
        label: "Blue",
        color: Colors.blue,
        value: color.blue,
        onChanged: (value) => color.blue = value,
      ),
    ],
  );
}
```

Co zmieniło się w porównaniu z poprzednią wersją? Niewiele. Do funkcji `buildSlider` przekazujemy prawdziwą wartość koloru pobraną ze stanu, a także funkcję która będzie aktualizować na bieżąco dany kolor. Przyznaj sam, że jest to naprawdę mała ilość kodu i trudno sobie wyobrazić go mniej.

Od teraz po uruchomieniu aplikacji możesz targać suwakami na lewo/prawo i zmienią one swoje położenie. Wartość każdego koloru jest ogólnodostępna, a nie tylko zamknięta do aktualnego widgeta. W praktyce oznacza to, że jesteśmy o krok od skonsumowania stanu w **components/rgb_preview.dart**. Spójrzmy jednak na alternatywny sposób konsumcji.

#### Consumer

Jak to we Flutterze - wszystko jest widgetem, Provider też może zostać tak wykorzystany. Otrzymujemy z pudełka widget `Consumer`, który działa na zasadzie **buildera** - zamiast parametru *child* występuje *builder*, będący funkcją uruchamianą w trakcie budowania drzewa (w uproszczeniu).

Builder przyjmuje łącznie trzy parametry, z czego istotne dla nas będą tylko dwa pierwsze:

- context - dobrze znany *BuildContext* w ramach którego budowane jest drzewo.
- value - instanacja stanu o który pytaliśmy. Identyczny obiekt jak ten uzyskany przez `Provider.of`.

Ostatni argument nazwaliśmy lakonicznie **_** co jest konwencją języka Dart na parametr który wiemy że istnieje, ale stanowczo nie będziemy go do niczego wykorzystywali.

**Prosty przykład:**

```dart
return Consumer<ColorModel>(
  builder: (context, value, _) {
    return RaisedButton(
      child: Text(value.red),
      onPressed: () => value.red++;
    );
  }
);
```

**Zalety:**
+ Jest widgetem
+ Umożliwia przebudowywanie tylko fragmentu drzewa

**Wady:**
- Zawsze nasłuchuje na zmiany stanu (konieczne rebuildy)

Czas na deser i finalizację aplikacji. Wykorzystamy jeden prosty trik, aby narysować podgląd koloru wybranego przez użytkownika. Przejdźmy tym razem do pliku **components/rgb_preview.dart** i skorzystajmy z Consumera.

```dart
import '../models.dart';

// ...

@override
Widget build(BuildContext context) {
  return Consumer<ColorModel>(
    builder: (context, color, _) {
      return Center(
        child: Container(
          width: 200,
          height: 200,
          color: Color.fromRGBO(
            color.red.toInt(),
            color.green.toInt(),
            color.blue.toInt(),
            1,
          ),
        ),
      );
    },
  );
}
```

Otoczyliśmy nasz poprzedni kod builderem `Consumer` i zamiast przekazywać losowe wartości poszczególnych kolorów - używamy tych które zapisane są w stanie. Mogliśmy użyć co prawda `Provider.of` - chcę jednak abyś wiedział że możesz ich używać zamiennie w oparciu o ich wady/zalety.

Aplikacja działa już teraz w 100%. Osiągnęliśmy zamierzony cel i mimo tego, że nie jest to cud techniki to masz teraz solidną wiedzę odnośnie pełnego zarządzania stanem - możesz zbudować jakąkolwiek apkę sobie wymarzysz (tylko nie przesadzaj).

### Podsumowanie

Oto i cały Provider w pigułce. Jest ktoś na górze drzewa kto dostarcza aktualny stan (dostawca), jest także ten który czeka na dole drzewa i nasłuchuja na zmiany (odbiorca). Jest też ostatecznie sam stan wysyłany od dostawcy do odbiorcy.

Przypomnienie jak korzystać z providera:

1. W pliku *main.dart* (lub innym wybranym) dodaj nowy wpis do `MultiProvider`.
2. Zdefiniuj klasę stanu dziedziczącą po `ChangeNotifier`.
3. W dowolnym widgecie w drzewie poniżej użyj `Provider.of` lub `Consumer` aby móc korzystać ze stanu.
4. Pamiętaj o wywołaniu `notifyListeners()`, aby wymusić przebudowanie drzewa.

Pełny kod aplikacji znajdziesz jak zawsze na [GitHubie](https://github.com/vintage/flutter_demo_provider). A co dalej z projektem? Jak go rozbudować, aby upewnić się, że opanowałeś w pełni zarządzanie stanem? Oto kilka pomysłów które po części uwzględniłem w referencyjnej aplikacji:

- Dodanie ekranu logowania w którym użytkownik podaje swoją nazwę przed rozpoczęciem wyboru kolorów.
- Wyświetlanie na wszystkich kolejnych ekranach nazwy użytkownika w belce aplikacji (**AppBar**).
- Po stuknięciu w podgląd koloru przeniesienie użytkownika na kolejny ekran prezentujący te same dane ale w inny sposób (bez suwaków? podgląd w formie koła zamiast kwadratu?).
- Dodanie nowego suwaka do ustawiania przezroczystości i uwzględnienie tej wartości w podglądzie (ostatni parametr funkcji **Color.fromRGBO**).
- ... i wiele więcej - ogranicza Cię tylko wyobraźnia (i ograniczenia sprzętowe).

![colorek_final.gif](/assets/img/blog/state_provider/colorek_final.gif)
