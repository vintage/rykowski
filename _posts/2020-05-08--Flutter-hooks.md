---
title: HookWidget, czyli Flutterowe hooki
date: "2020-05-08"
layout: "post"
slug: "flutter-hooks"
excerpt: "Nie lubisz StatefulWidgetów za ich rozwarstwienie na dwie klasy, lub z jakiegokolwiek innego powodu, a mimo to musisz wykonać kod w sposób niemożliwy dla StatelessWidgeta? Daj szansę hookom i HookWidgetowi, który robi to samo ale lepiej."
---

Czym właściwie są **hooki**? Jest to termin dobrze znany wszystkim programistom m.in. Reacta, gdzie hooki jakiś czas temu opanowały cały ekosystem i wybuchł na nie ogromny hype. Powstały przede wszystkim po to, aby ułatwić współdzielenie logiki między stanowymi ~~komponentami~~ widgetami (takimi jak `StatefulWidget`) oraz uprościć logikę, która stoi za tzw. **life-cycle methods**, czyli co powinno się wydarzyć przed pierwszym renderowaniem, przy kolejnych, jak reagować na zmiany parametrów renderowania itd. Generalnie wszystko sprowadza się do tego jak zebrać cały ten bałagan i zrobić go czytelniejszym.

Czy się udało? W Reacie jak najbardziej! Większość developerów już przeszła na nowe rozwiązanie, które z własnego doświadczenia również szczerze polecam. A co z Flutterem? No bo przecież dobrze wiemy, że styl pisania aplikacji jest mocno podobny (deklaratywny), więc może hooki są też dla nas? Przyznaj szczerze, że w głębi serca to jednak nie przepadasz za wbudowanym `StatefulWidgetem` i chętnie byś go wymienił na inne rozwiązanie, tylko jakie?

> Flutterowcy żądają hooków!

![logo.png](/assets/img/blog/hooks/logo.png)

# Flutter Hooks

Nie musisz sam rozmyślać o tym jak zaimplementować cały koncept hooków we Flutterze. Ktoś już to zrobił za nas. A w sumie to nie ktoś, a [Remi Rousselet](https://twitter.com/remi_rousselet) tworzący na prawdę mocne paczki w ekosystemie, takie jak chociażby opisywany już [Provider](/blog/flutter-state-management-provider/). Dzięki Remi!

Paczkę znajdziesz na [pub.dev](https://pub.dev/packages/flutter_hooks), a instalacja sprowadza się do dodania pojedynczego wpisu w `pubspec.yaml` i gotowe. Można zacząć korzystać i rzucić `StatefulWidget` w kąt. Tylko co tak na prawdę możemy zrobić z tą paczką? Jakie hooki dostarcza i w jakich sytuacjach ich używać? No i rzecz jasna najważniejsze pytanie ...

> A po co to komu? A na co to komu potrzebne?

## Złota zasada

Zanim przejdziemy do praktycznego zastosowania, omówmy złotą zasadę korzystania z hooków. Brzmi ona następująco: **"Każdy build widgeta musi zawołać te same hooki w tej samej kolejności"**. Nie ma wyjątków od tej reguły, koniec kropka. Oznacza to, że nie wołasz hooków w obrębie `if/else`, ani nie kończysz wywołania builda przed odpalaniem ostatniego ze swoich hooków. Najprościej będzie jeśli zawsze na początku metody `build` uruchomisz wszystkie hooki, a dopiero w dalszej części przejdziesz do wykorzystania ich wartości i rysowania drzewa na ekranie. 

**NIE** dla if-ów:

```dart
Widget build(BuildContext context) {
  if (something) {
    useSomeHook();
  }
}
```

**NIE** dla hooków uruchamianych po warunkowym wyjściu z builda:

```dart
Widget build(BuildContext context) {
  if (something) {
    return Text("Loading");
  }

  final data = useSomeHook();
}
```

**TAK** dla hooków na starcie, a potem rób co chcesz:

```dart
Widget build(BuildContext context) {
  final data = useSomeHook();
  useSomeOtherHook();
  useEvenMoreHooks();

  if (something) {
    return Text("Loading");
  }

  return Text("My super app")
}
```

Co jak jednak nie posłuchasz? To co zwykle dzieje się w takich przypadkach, czekają Cię błędy na poziomie uruchomieniowym (*runtime*). A przecież nikt nie lubi błędów - ani Ty, ani użytkownik Twojej wypieszczonej aplikacji. To co, gotowy?

## useState

Jeden z najczęściej używanych przez mnie hooków, a na dodatek świetnie nadający się do celów demonstracyjnych. `useState` to nic innego jak mechanizm zarządzania lokalnym stanem w obrębie pojedynczego widgeta. Brzmi znajomo? To właśnie główny feature od `StatefulWidgeta` zamknięty w ramach jednej funkcji.

![The future is now, old man](https://i.kym-cdn.com/entries/icons/original/000/029/790/2gi1lylqezh21.jpg)

Spójrzmy jak wygląda domyślna aplikacja wygenerowana po `flutter create`. Pamiętasz ją? Jedno-przyciskowy ekran zliczający kliknięcia.

![default_app.png](/assets/img/blog/hooks/default_app.png)

### StatefulWidget

W wersji standardowej mamy dwie powiązane ze sobą klasy (widget i state), użycie `setState` i to w gruncie rzeczy tyle.

```dart
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key}) : super(key: key);

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

### HookWidget

Osiągnięcie tego samego efektu przy pomocy hooków ogranicza się do użycia jednej klasy:

```dart
class MyHomePage extends HookWidget {
  @override
  Widget build(BuildContext context) {
    final counter = useState(0);

    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '${counter.value}',
              style: Theme.of(context).textTheme.display1,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => counter.value++,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

Różnica polega na tym, że zamiast dwóch klas mamy teraz tylko jedną, dziedziczącą po `HookWidget`. Pamiętaj o tym, bo tylko w takim widgecie możesz używać hooków! Mamy także wywołanie `useState(0)`, który tworzy lokalny stan przechowujący liczbę kliknięć (zaczynając od zera). Zwracany obiekt jest typu `ValueNotifier<int>`, stąd jego odczyt i zapis nie odywa się bezpośrednio przez **counter**, tylko przez **counter.value** (wartość docelowa). Każda zmiana stanu prowadzi do przebudowania widgeta, zupełnie jak to ma miejsce przy domyślnym `setState`.

Na pierwszy rzut oka mogłoby się wydawać, że każde przebudowanie będzie prowadziło do zresetowania licznika. No bo jak to? Przecież ta konkretna linijka jest odpalana za każdym razem gdy rysujemy nasz widget. Masz rację, jest odpalana za każdym razem, ale stan jest automatycznie pamiętany jak to ma miejsce w `StatefulWidget`. Magia hooków, a tak na prawdę to polecam spojrzeć do kodu źródłowego `useState`. Dosłownie kilka linii kodu które wszystko tłumaczą.

Skoro oba rozwiązania działają tak samo to po co przepłacać? Ja tu widzę osobiście benefity na rzecz `HookWidgeta`:

- Jedna klasa zamiast dwóch zaspokaja potrzebę minimalizmu i dobrego smaku
- Mniej kodu to mniej kłopotów
- Dodanie stanowości do bezstanowego widgeta ogranicza się do zmiany `extends StatelessWidget` na `extends HookWidget` (git ma mniejszego diffa)
- Możliwość użycia pozostałych hooków skoro jesteśmy już w `HookWidget`

---

Ciekawą alternatywą dla `useState` jest `useValueNotifier`, który działa na identycznej zasadzie, ale nie przebudowuje widgeta przy zmianie wartości. Nie raz zdarza się, że chcemy trzymać pewną wartość i zmieniać ją w widgecie, ale nie wpływa to nijak na to co prezentujemy użytkownikowi. A skoro nie widać różnicy, to unikajmy zbędnego przerysowywania.

Użyłem go tyle razy, że jestem w stanie zliczyć wystąpienia na palcach jednej ręki, ale zawsze warto pamiętać że mimo wszystko istnieje i czeka na swój czas.

## useMemoized

Mamy ograne zarządzanie stanem, ale to przecież nie wszystko co potrafi `StatefulWidget`! Co więcej, często używamy odmiany stanowej nawet jeśli żadnego stanu nie potrzebujemy. Wszystko za sprawą potrzeby uruchomienia pewnego kodu tylko raz per widget, który przeprowadzi wstępną inicjalizację, a kolejne buildy będą czerpać z uprzedniego wyniku. Mowa o zbawiennym `initState`, który wywołany zostanie tylko raz i umożliwia nam chociażby wysłanie zapytania do API, a następnie korzystanie z zapisanej odpowiedzi przy każdym re-buildzie. Chyba nie chciałbyś bombardować własnego API na każdym renderze? Dobrze, bo ja też nie.

Wersja standardowa w której tylko raz wysyłamy request i wyświetlamy jego odpowiedź w najprostszej możliwej wersji wyglądałaby jak poniżej. Zwróć uwagę że nie parsujemy odpowiedzi do JSONa, ani nie robimy innych zbędnych cyrków. Rysujemy prymitywnego stringa, upraszczamy jak się da.

![album_app.png](/assets/img/blog/hooks/album_app.png)

### StatefulWidget

```dart
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key}) : super(key: key);

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  String data;

  @override
  void initState() {
    initStateAsync();
    super.initState();
  }

  Future<void> initStateAsync() async {
    final response = await http.get('https://jsonplaceholder.typicode.com/albums/1');

    setState(() {
      data = response.body;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text(data),
      ),
    );
  }
}
```

### HookWidget

Wersja zahookowana, w której korzystamy z `useMemoized`. Zostanie on wywołany tylko przy pierwszym buildzie, zupełnie jak wspomniany wyżej `initState`.

```dart
class MyHomePageHook extends HookWidget {
  @override
  Widget build(BuildContext context) {
    final data = useState("");
    useMemoized(() async {
      final response = await http.get('https://jsonplaceholder.typicode.com/albums/1');

      data.value = response.body;
    });

    return Scaffold(
      body: Center(
        child: Text(data.value),
      ),
    );
  }
}
```

Znowu mniej kodu, co? Ja już od dawna jestem sprzedany temu rozwiązaniu, ale pamiętam, że chwilę zajęło mi pogodzenie się z tym nowym formatem. W domyślnym zapisie mam przecież dedykowaną metodę `initState` w której przeprowadzam wszystkie te specyficzne operacje, a tu nagle mam je robić w buildzie? Brzmi nieswojo, ale na prawdę warto! Po pewnym czasie staje się to zupełnie naturalne i oczywiste.

Z tym stwierdzeniem, że `useMemoized` jest uruchamiany tylko raz to tak nie do końca prawda. W sensie w naszym przykładzie jak najbardziej i jeśli będziemy tak go używali to rzeczywiście osiągnięmy wspomnianą *jednorazowość*. Możemy jednak osiągnąć znacznie więcej!

### Klucz do sterowania hookiem

`useMemoized` przyjmuje argument **keys**, który mimo swojej prostoty, otwiera ogrom możliwości (*bo jest kluczem!*). Koncept polega na tym, że jest to opcjonalna lista obiektów, którą przekazujemy do hooka. Stanie się ona jego identyfikacją, tożsamością. Oznacza to, że jakakolwiek zmiana w tej liście doprowadzi do ponownego uruchomienie hooka przy kolejnym buildzie. Taki system cache-owania. Tak długo jak klucze są identyczne bierz wartość z cache, w przeciwnku wypadku przelicz nową wartość i zapisz ją na przyszłość. Najlepiej będzie jak zobaczymy to w akcji rozbudowując poprzedni przykład. Dodajmy przycisk do zmiany albumu na kolejny. Internaktywność, yay!

```dart
class MyHomePageHook extends HookWidget {
  @override
  Widget build(BuildContext context) {
    final albumId = useState(1);
    final data = useState("");
    useMemoized(() async {
      final response = await http
          .get('https://jsonplaceholder.typicode.com/albums/${albumId.value}');

      data.value = response.body;
    });

    return Scaffold(
      body: Center(
        child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
          Text(
            "Album ID: ${albumId.value}",
            style: TextStyle(color: Colors.red),
          ),
          Text(data.value),
          RaisedButton(
            child: Text("Bump"),
            onPressed: () => albumId.value++,
          )
        ]),
      ),
    );
  }
}
```

Jak zadziałała aplikacja? Połowicznie:

![album_app_keys.gif](/assets/img/blog/hooks/album_app_keys.gif)

Co prawda **albumId** się ładnie aktualizuje po każdym kliknięciu, ale wyświetlane dane już nie za bardzo. Co można z tym zrobić? Dodać klucze!

```dart
useMemoized(() async {
  final response = await http
      .get('https://jsonplaceholder.typicode.com/albums/${albumId.value}');

  data.value = response.body;
}, [albumId.value]);
```

Tym właśnie sposobem powiedzieliśmy `useMemoized` że ma wywołać swoją wewnętrzną funkcję za każdym razem gdy zmieni się wartość trzymana w **albumId.value**. Na pierwszy rzut oka cały ten flow jest nieintuicyjny, jednak nic mylnego! To świetnie zaprojektowana machineria do często powtarzających się problemów. Wystarczy delikatnie wysterować swoje rozumowanie na boczny tor i zacząć cieszyć się z prostego i zwięzłego zapisu.

![album_app_keys_fixed.gif](/assets/img/blog/hooks/album_app_keys_fixed.gif)

W liście możesz oczywiście umieścić tyle elementów ile tylko pragniesz. Zmiana któregokolwiek z nich doprowadzi do ponownego odpalenia funkcji w `useMemoized`, ale z praktycznego punktu widzenia będzie to najczęściej jeden obiekt, góra dwa. 

## useEffect

Ostatni z wielkiej trójcy hooków, których używa się najczęściej przy tworzeniu aplikacji. Swoim działaniem jest nieco zbliżony do `useMemoized`, ale nie daj się zwieść pozorom - wykorzystasz go w innych okolicznościach i przypadkach.

Główne podobieństwo do `useMemoized` polega na tym, że tutaj również dostarczamy opcjonalną listę kluczy do identyfikacji, których zmiana powoduje ponowne uruchomienie wewnętrznej funkcji. A różnica? Praktycznie to są dwie:

- `useEffect` nie zwraca żadnej wartości, nie trudź się więc z przypisaniem jego wyniku do zmiennej
- Funkcja uruchamiana przez `useEffect` musi zwrócić funkcję (callback) do ewentualnego posprzątania po sobie

Pierwsza różnica jest prosta. `useMemoized` zwróci nam wynik swojej funkcji, `useEffect` nie. W porządku, a co z tym drugim wymaganiem? Funkcja musi zwrócić funkcję. Dlaczego? Bo taki właśnie jest zamysł stojący za efektami - mogą brudzić, czyli np. otwierać połączenia sieciowe, nasłuchiwać na strumieniach, czy też innych podobnych strukturach i jako dobry developer powinieneś te rzeczy sprzątnąć, zupełnie tak jak robisz to w przypadku metody `dispose` na `StatefulWidget`.

Kiedy zatem używać `useEffect`? Zawsze wtedy gdy musiałbyś użyć `initState` połączonego z `dispose`. Jeśli potrzebujesz takiego `useMemoized`, ale funkcja którą w nim uruchamiasz stackuje pewien problem - np. każde wywołanie dodaje kolejnego listenera - to `useEffect` jest stworzony do rozwiązania tego problemu.

## Hooki

Przedstawione hooki to oczywiście dopiero czubek góry lodowej i nawet samych wbudowanych jest o wiele więcej. Bardzo przydatne okazują się `useAnimationController` do przeprowadzania animacji, `usePrevious` do uzyskania wartości obiektu z poprzedniego builda, czy chociażby `useFuture` zastępujący całkowicie `FutureBuildera`. Jeśli chciałbyś zobaczyć co jeszcze oferują hooki to nie ma lepszego miejsca niż oficjalna dokumentacja.

Na koniec pamiętaj o ważnej rzeczy, która nie została wcześniej poruszona. Jeśli potrzebujesz nietypowego hooka, to bez problemu możesz go sam napisać bazując na innym już istniejącym. W swoich projektach mam kilka dodatkowych hooków, które często za mną podążają jak np. `useTabController`.

Kod pełnej aplikacji znajdziesz na [https://github.com/vintage/flutter_demo_hooks](https://github.com/vintage/flutter_demo_hooks).
